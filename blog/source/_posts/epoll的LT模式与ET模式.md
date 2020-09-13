---
title: epoll的LT模式与ET模式
date: 2020-09-13 22:17:17
tags: ['网络编程']
categories: 网络编程
---

对`epoll`的两种模式做一个小结，其中也给出一些实例。

<!--more-->

### LT模式

`LT`(Level Trigger, 水平触发模式):`LT`是`epoll`默认的模式，这种模式下`epoll`相当于一个效率较高的`poll`。

`socket`接收缓冲区不为空，有数据可读，一直触发读事件。

`socket`发送缓冲区不为满，可以继续写入数据，一直触发写事件。

### ET模式

`ET`(Edge Trigger, 边沿触发模式):`ET`是一种高效工作模式。

`socket`接收缓冲区状态发生变化触发读事件，即空的接收缓冲区刚接收到数据是触发读事件。

`socket`发送缓冲区的状态发生变化触发写事件， 即满的缓冲区刚腾出空间时触发写事件。

```C++ 
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <errno.h>
#include <assert.h>
#include <fcntl.h>
#include <sys/types.h>
#include <sys/socket.h>
#include <sys/epoll.h>
#include <arpa/inet.h>

#define MAX_EVENT_NUM   1024
#define BUFFER_SIZE 10

// 设置句柄为fd的文件为非阻塞, 并且返回原有的设置选项
int setnonblocking(int fd){
    int old_option = fcntl(fd, F_GETFL);
    int new_option = old_option | O_NONBLOCK;
    fcntl(fd, F_SETFL, new_option);
    return old_option;
}

void addfd(int epfd, int fd, bool enable_et){
    epoll_event ev;
    ev.data.fd = fd;
    ev.events  = EPOLLIN;
    if (enable_et)
        ev.events |= EPOLLET;
    // 将fd添加到epoll树上, 并且设置为非阻塞
    // 在ET模式的时候必须使用非阻塞套接字, 以避免由于一个文件句柄的阻塞读/阻塞写操作把处理多个文件描述符的任务饿死。
    epoll_ctl(epfd, EPOLL_CTL_ADD, fd, &ev);    //注册epoll事件
    setnonblocking(fd);
    return ;
}

void lt(epoll_event* ev, int number, int epfd, int listenfd){
    char buf[BUFFER_SIZE];
    for (int i = 0; i < number; ++i){
        int fd = ev[i].data.fd;
        // 新到来的链接
        if (fd == listenfd){
            struct sockaddr_in cli;
            socklen_t cli_len = sizeof(cli);
            int connfd = accept(fd, (struct sockaddr*)&cli, &cli_len);
            // 不开启ET模式
            addfd(epfd, connfd, false);
        }
        else if (ev[i].events & EPOLLIN){
            // 如果收到读事件发生
            // LT模式下如果buf中还有数据就会一直触发
            printf("LT: event trigger once \n");
            bzero(buf, sizeof(buf));
            int ret = recv(fd, buf, BUFFER_SIZE - 1, 0);
            if (ret <= 0){
                close(fd);
                continue;
            }
            printf("get %d bytes of content: %s\n", ret, buf);
        }
        else
            printf("something else happened\n");
    }
}

void et(epoll_event* ev, int number, int epfd, int listenfd){
    char buf[BUFFER_SIZE];
    for (int i = 0; i < number; ++i){
        int fd = ev[i].data.fd;
        if (fd == listenfd){
            struct sockaddr_in cli;
            socklen_t cli_len = sizeof(cli);
            int connfd = accept(fd, (struct sockaddr*)&cli, &cli_len);
            assert(connfd != -1);
            // 开启ET模式
            addfd(epfd, connfd, true);
        }
        else if (ev[i].events & EPOLLIN){
            // 当有读事件被触发
            // else if 中的代码不会被重复触发
            // ET模式因为只通知一次,故需要一次性将所有的数据都读完
            printf("ET: event trigger once \n");
            while(1){
                bzero(buf, sizeof(buf));
                int ret = recv(fd, buf, sizeof(buf), 0);
                if (ret < 0){
                    // 对于非阻塞的IO,下面的条件成立表示数据已经全部读取完毕.
                    // 此后epoll就能再次触发sockfd上的epoll事件,以驱动下一次读事件
                    if (errno == EAGAIN){
                        printf("read last\n");
                        break;
                    }
                    close(fd);
                    break;
                }
                else if (ret == 0)
                    close(fd);
                else
                    printf("get %d bytes of content: %s\n", ret, buf);
            }
        }
        else
            printf("something else happened \n");
    }
}


int main(int argc, char* argv[]){
    if (argc < 2){
        printf("please input port!\n");
        return -1;
    }
    int port = atoi(argv[1]);
    // 服务器的socket属性
    struct sockaddr_in serv;
    serv.sin_family = AF_INET;
    serv.sin_port = htons(port);
    serv.sin_addr.s_addr = htonl(INADDR_ANY);
    int listfd = socket(AF_INET, SOCK_STREAM, 0);
    assert(listfd >= 0);

    int ret = bind(listfd, (struct sockaddr*)&serv, sizeof(serv));
    assert(ret != -1);
    
    ret = listen(listfd, 5);
    assert(ret != -1);

    int epfd = epoll_create(5);
    assert(epfd != -1);
    epoll_event all[MAX_EVENT_NUM];
    addfd(epfd, listfd, true);

    while(1){
        int ret = epoll_wait(epfd, all, MAX_EVENT_NUM, -1);
        if (ret < 0){
            printf("epoll failure\n");
            break;
        }
        lt(all, ret, epfd, listfd);
        //et(all, ret, epfd, listfd);
    }
    close(listfd);
    return 0;
}
```

![LT](https://wooyooyoo-photo.oss-cn-hangzhou.aliyuncs.com/blog/2020/09/Snipaste_2020-09-13_22-09-46.png)

- 上图`LT`模式触发了两次.因为设置`buf`设置的是10个字节(减去一个'\0')，故触发两次。

![ET](https://wooyooyoo-photo.oss-cn-hangzhou.aliyuncs.com/blog/2020/09/Snipaste_2020-09-13_22-09-00.png)

- 上图`ET`模式触发了一次.

**PS：ET模式的时候必须使用非阻塞的套接字，以避免由于一个文件句柄的阻塞读/阻塞写操作把处理多个文件描述符的任务饿死。**

