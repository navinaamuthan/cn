# cn
https://vcansimplify.wordpress.com/2013/03/14/c-socket-tutorial-echo-server/ 

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
