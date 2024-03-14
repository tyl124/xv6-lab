# lab1

## 0.实验环境配置

## 1.sleep(easy)

## 2.pingpong(easy)

## 3.primes(moderate/hard)

```c
#include <stdio.h>
#include <sys/wait.h>
#include <stdlib.h>
#include <unistd.h>

void primes(int pipefd[]) {
    int Prime, n;
    if (read(pipefd[0], &Prime, sizeof Prime) != 4) {
        printf("Read number failed!\n");
        exit(1);
    }
    printf("prime: %d\n", Prime);
    
    int next_p[2];
    pipe(next_p);
    int pid = fork();
    if (pid == -1) {
        printf("Fork failed!\n");
        exit(1);
    }
    else if (pid == 0) {
        primes(next_p);   
        exit(0);          
    }
    else {
        close(next_p[0]); 
        while (read(pipefd[0], &n, sizeof n) > 0) {
            if (n % Prime != 0) write(next_p[1], &n, sizeof n);
        }
        close(pipefd[0]); 
        close(next_p[1]); 
        wait(NULL);       
    }
    exit(0);
}

int main() {
    int pipefd[2];
    pipe(pipefd);

    int pid = fork();
    if (pid == -1) {
        printf("Fork failed!\n");
        exit(1);
    }
    else if (pid == 0) {
        primes(pipefd);   // Call primes function
        exit(0);          
    }
    else {
        close(pipefd[0]); 
        for (int i = 2; i <= 35; i++) {
            if (write(pipefd[1], &i, sizeof i) != sizeof i) {
                printf("Write numbers in pipe failed!\n");
                exit(1);
            }
        }
        close(pipefd[1]); 
        wait(NULL);       
    }
    exit(0);
}

```

按照要求容易写出以上代码，但是当运行时出现bug，即程序最终不会自动结束，而是会进入死循环:

![noend-cycle](./pic/noend-cycle.png)

然后就是快(痛)乐(苦)的debug时间了:

首先分析，程序没有终止，说明存在进程没有运行到`exit()`语句，经过简单思考后，可以知道一定是存在子进程没有正确终止。因为如果子进程都正常结束，那么父进程在`wait(0)`后一定会进入`exit(0)`，从而完成整个程序的退出。所以，重点就是对于`primes`函数的分析。

手动模拟一下程序的执行过程(数据量很小，如果多的话还是建议用GDB)，从31开始





## 4.find(moderate)

## 5.xargs(moderate)

## Optional challenge exercises
 等待施工中...
