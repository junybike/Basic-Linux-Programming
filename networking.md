## Unix TCP

Server example:

```C
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <sys/socket.h>
#include <sys/un.h>
#include <unistd.h>

#define BUF_SIZE 64
#define MY_SOCK_PATH "tmp"
#define LISTEN_BACKLOG 32

int main()
{
  struct sockaddr_un addr;
  int sfd, cfd;
  ssize_t num_read;
  char msg[BUF_SIZE];

  sfd = socket(AF_UNIX, SOCK_STREAM, 0);
  memset(&addr, 0, sizeof(struct sockaddr_un));
  addr.sun_family = AF_UNIX;
  strncpy(addr.sun_path, MY_SOCK_PATH, sizeof(addr.sun_path) - 1);

  bind(sfd, (struct sockaddr *)&addr, sizeof(struct sockaddr_un));
  listen(sfd, LISTEN_BACKLOG);

  for (;;)
  {
    cfd = accept(sfd, NULL, NULL);
    while ((num_read = read(cfd, msg, BUF_SIZE)) > 0)
      write(STDOUT_FILENO, msg, num_read);
  }

  close(cfd);
}
```

Client example:

```C
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <sys/socket.h>
#include <sys/un.h>
#include <unistd.h>

#define BUF_SIZE 64
#define MY_SOCK_PATH "tmp"

int main()
{
  struct sockaddr_un addr;
  int sfd, cfd;
  ssize_t num_read;
  char msg[BUF_SIZE];

  sfd = socket(AF_UNIX, SOCK_STREAM, 0);
  memset(&addr, 0, sizeof(struct sockaddr_un));
  addr.sun_family = AF_UNIX;
  strncpy(addr.sun_path, MY_SOCK_PATH, sizeof(addr.sun_path) - 1);

  if (connect(sfd, (struct sockaddr *)&addr, sizeof(struct sockaddr_un);
  while ((num_read = read(STDIN_FILENO, msg, BUF_SIZE)) > 0)
    write(sfd, msg, num_read);
  
  exit(EXIT_SUCCESS);
```

## Unix UDP

Server example:

```C
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <sys/socket.h>
#include <sys/un.h>
#include <unistd.h>

#define BUF_SIZE 64
#define MY_SOCK_PATH "tmp"
#define LISTEN_BACKLOG 32

int main() {
  struct sockaddr_un addr;
  int sfd;
  ssize_t num_read;
  char msg[BUF_SIZE];
  socklen_t len = sizeof(struct sockaddr_un);

  sfd = socket(AF_UNIX, SOCK_DGRAM, 0);
  memset(&addr, 0, sizeof(struct sockaddr_un));
  addr.sun_family = AF_UNIX;
  strncpy(addr.sun_path, MY_SOCK_PATH, sizeof(addr.sun_path) - 1);

  bind(sfd, (struct sockaddr *)&addr, sizeof(struct sockaddr_un));
  for (;;)
  {
    num_read = recvfrom(sfd, msg, BUF_SIZE, 0, NULL &len);
  }
}
```

Client example:

```C
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <sys/socket.h>
#include <sys/un.h>
#include <unistd.h>

#define MY_SOCK_PATH "tmp"
#define BUF_SIZE 64

int main() {
  struct sockaddr_un addr;
  int sfd;
  ssize_t num_read;
  char msg[BUF_SIZE];

  sfd = socket(AF_UNIX, SOCK_DGRAM, 0);
  memset(&addr, 0, sizeof(struct sockaddr_un));
  addr.sun_family = AF_UNIX;
  strncpy(addr.sun_path, MY_SOCK_PATH, sizeof(addr.sun_path) - 1);

  while ((num_read = read(STDIN_FILENO, buf, BUF_SIZE)) > 0)
    sendto(sfd, buf, num_read, 0, (struct sockaddr *)&addr);

  exit(EXIT_SUCCESS);
} 
```

## INET TCP

```C
#include <arpa/inet.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <sys/socket.h>
#include <unistd.h>

#define BUF_SIZE 64
#define PORT 8000
#define LISTEN_BACKLOG 32

int main()
{
  struct sockaddr_in addr;
  int sfd, cfd;
  ssize_t num_read;
  char msg[BUF_SIZE]

  sfd = socket(AF_INET, SOCK_STREAM, 0);
  memset(&addr, 0, sizeof(struct sockaddr_in));
  addr.sin_family = AF_INET;
  addr.sin_port = htons(PORT);
  addr.sin_addr.s_addr = htonl(INADDR_ANY);

  bind(sfd, (struct sockaddr *)&addr, sizeof(struct sockaddr_in));
  listen(sfd, LISTEN_BACKLOG);

  for (;;)
  {
    cfd = accept(sfd, NULL, NULL);
    while ((num_read = read(cfd, msg, BUF_SIZE)) > 0)
      write(STDOUT_FILENO, msg, num_read);
  }
close(cfd);
}
```

Client example:

```C
#include <arpa/inet.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <sys/socket.h>
#include <unistd.h>

#define PORT 8000
#define BUF_SIZE 64
#define ADDR "192.168.0.4"

int main()
{
  struct sockaddr_in addr;
  int sfd;
  ssize_t num_read;
  char msg[BUF_SIZE];

  sfd = socket(AF_INET, SOCK_STREAM, 0);
  memset(&addr, 0, sizeof(struct sockaddr_in));
  addr.sin_family = AF_INET;
  addr.sin_port = htons(PORT);

  inet_pton(AF_INET, ADDR, &addr.sin_addr);
  connect(sfd, (struct sockaddr *)&addr, sizeof(struct sockaddr_in));

  while ((num_read = read(STDIN_FILENO, buf, BUF_SIZE)) > 0)
    write(sfd, buf, num_read);

  exit(EXIT_SUCCESS);  
}
```

## Epoll

```C

```
