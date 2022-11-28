# cn
https://vcansimplify.wordpress.com/2013/03/14/c-socket-tutorial-echo-server/  

**tcpsever** 

#include <sys/types.h>
#include <sys/socket.h>
#include <netdb.h>
#include <stdio.h>
#include<string.h>
int main()
{
char str[100];
int listen_fd, comm_fd;
struct sockaddr_in servaddr;
listen_fd = socket(AF_INET, SOCK_STREAM, 0);
bzero( &servaddr, sizeof(servaddr));
servaddr.sin_family = AF_INET;
servaddr.sin_addr.s_addr = htons(INADDR_ANY);
servaddr.sin_port = htons(22000);
bind(listen_fd, (struct sockaddr *) &servaddr, sizeof(servaddr));
listen(listen_fd, 10);
comm_fd = accept(listen_fd, (struct sockaddr*) NULL, NULL);
while(1)
{
bzero( str, 100);
read(comm_fd,str,100);
printf("Echoing back - %s",str);
write(comm_fd, str, strlen(str)+1);
}
4
} 

**tcpclient** 

#include <sys/types.h>
#include <sys/socket.h>
#include <netdb.h>
#include <stdio.h>
#include<string.h>
int main(int argc,char **argv)
{
int sockfd,n;
char sendline[100];
char recvline[100];
struct sockaddr_in servaddr;
sockfd=socket(AF_INET,SOCK_STREAM,0);
bzero(&servaddr,sizeof servaddr);
servaddr.sin_family=AF_INET;
servaddr.sin_port=htons(22000);
inet_pton(AF_INET,"127.0.0.1",&(servaddr.sin_addr));
connect(sockfd,(struct sockaddr *)&servaddr,sizeof(servaddr));
while(1)
5
{
bzero( sendline, 100);
bzero( recvline, 100);
fgets(sendline,100,stdin); /*stdin = 0 , for standard input */
write(sockfd,sendline,strlen(sendline)+1);
read(sockfd,recvline,100);
printf("%s",recvline);
}
} 

**udpserver**

#include<sys/types.h>
#include<sys/socket.h>
#include<stdio.h>
#include<netinet/in.h>
#define MAX 100
#define SERPORT 1090
14
#define SA struct sockaddr
void str_echo(FILE*,int,SA*,socklen_t);
int main(int argc,char** argv)
{
int sockfd;
struct sockaddr_in servaddr,cliaddr;
sockfd=socket(AF_INET,SOCK_DGRAM,0);
bzero(&servaddr,sizeof(servaddr));
servaddr.sin_family=AF_INET;
servaddr.sin_addr.s_addr=htonl(0);
servaddr.sin_port=htons(SERPORT);
bind(sockfd,(SA*)&servaddr,sizeof(servaddr));
str_echo(stdin,sockfd,(SA*)&cliaddr,sizeof(cliaddr));
exit(0);
}
void str_echo(FILE* fp,int sockfd,SA* cliaddr,socklen_t clilen)
{
char msg[MAX],send[MAX];
int n;
while(1)
{
if((n=recvfrom(sockfd,msg,MAX,0,cliaddr,&clilen))>0)
{
msg[n]='\0';
printf("Client msg : ");
15
fputs(msg,stdout);
printf("SERVER msg : ");
fgets(msg,MAX,fp);
sendto(sockfd,msg,strlen(msg),0,cliaddr,clilen);
}
}
}

**udpclient** 

#include<sys/types.h>
#include<sys/socket.h>
#include<stdio.h>
#include<netinet/in.h>
#define MAX 100
#define SERPORT 1090
#define SA struct sockaddr
void str_cli(FILE*,int,SA*,socklen_t);
int main(int argc,char** argv)
{
int sockfd;
struct sockaddr_in servaddr;
sockfd=socket(AF_INET,SOCK_DGRAM,0);
bzero(&servaddr,sizeof(servaddr));
servaddr.sin_family=AF_INET;
16
servaddr.sin_addr.s_addr=inet_addr("127.0.0.1");
servaddr.sin_port=htons(SERPORT);
printf("Client msg : ");
str_cli(stdin,sockfd,(SA*)&servaddr,sizeof(servaddr));
exit(0);
}
void str_cli(FILE* fp,int sockfd,SA* seraddr,socklen_t len)
{
char msg[MAX],rcv[MAX];
int n;
while(fgets(msg,MAX,fp)!=NULL)
{
sendto(sockfd,msg,MAX,0,seraddr,len);
if((n=recvfrom(sockfd,rcv,MAX,0,seraddr,&len))>0)
{
rcv[n]='\0';
fputs(rcv,stdout);
}
printf("Client msg : ");
}
}

ping -ping google.com  
tcpdump - sudo tcpdump -D 

traceroute - sudo traceroute/ traceroute google.com 

netstat - sudo netstat -tnlp/netstat -r 

ipconfig - ip addr/ ip link show 


