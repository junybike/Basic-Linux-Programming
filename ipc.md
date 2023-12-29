# Inter Process Communication

## Pipes

Example:

```C
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <sys/types.h>
#include <sys/wait.h>
#include <unistd.h>

char *msg = "Hello World";

int main()
{
  int pipefd[2];
  pid_t cpid;
  cpid = fork();

  if (cpid == 0) // child process (cpid)
  {
    close(pipefd[1]);
    while (read(pipefd[0], &buf, 1) > 0) // receive msg from parent proces
      write(STDOUT_FILENO, &buf, 1);
    close(pipefd[0]);
    exit(EXIT_SUCCESS);
  }
  else // parent process
  {
    close(pipefd[0]);
    write(pipefd[1], msg, strlen(msg)); // send msg to child process
    close(pipefd[1]);
    exit(EXIT_SUCCESS);
  }
}
```

## Memory mapping

`void *mmap(void *addr, size_t length, int prot, int flags, int fd, offset, off_t offset)`
- prot: executable, readable, writable, or not accessible
- flags: MAP_SHARED, MAP_PRIVATE, MAP_ANONYMOUS

Example: 
```C
#include <fcntl.h>
#include <stdio.h>
#include <stdlib.h>
#include <sys/mman.h>
#include <sys/stat.h>
#include <unistd.h>

int main(int argc, char *argv[])
{
  char *addr;
  int fd;
  struct stat sb;
  size_t length;
  ssize_t s;

  fd = open(argv[1], O_RDONLY);
  fstat(fd, &sb);
  length = sb.st_size;

  addr = mmap(NULL, length, PROT_READ, MAP_PRIVATE, fd, 0);

  s = write(STDOUT_FILENO, addr, length);
  if (s != length)
  {
    if (s == -1)
    {
      // write error
    }
    // partial write error
  }
  munmap(addr, length);
  close(fd);
  exit(EXIT_SUCCESS);
}
```

## Shared memory

`int shm_open(const char *name, int oflag, mode_t mode)`
- shared memory initializer
- provide file descripter for shared memory.
- creates a file in the directory for all shared memory object files

`int ftruncate(int fd, off_t length)`
- set the size of shared memory

`shm_unlink(const char *name)`
- remove shared memory object

Example (writer):

```C
#define _XOPEN_SOURCE 500

#include <fcntl.h>
#include <stdint.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <sys/mman.h>
#include <sys/stat.h>
#include <sys/types.h>
#include <unistd.h>

int main(int argc, char *argv[])
{
  char *shm = argv[1];
  int fd = shm_open(shm, O_CREAT | O_RDWR, S_IRUSR | S_IWUSR);
  ftruncate(fd, sizeof(uint8_t));

  uint8_t *ptr = mmap(NULL, sizeof(*ptr), PROT_READ | PROT_WRITE, MAP_SHARED, fd, 0);
  *ptr = 100;
}
```

Example (reader):

```C
#define _XOPEN_SOURCE 500

#include <fcntl.h>
#include <stdint.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <sys/mman.h>
#include <sys/stat.h>
#include <sys/types.h>
#include <unistd.h>

int main(int argc, char *argv[])
{
  char shm = argv[1];
  int fd = shm_open(shmpath, O_RDWR, S_IRUSR | S_IWUSR);
  ftruncate(fd, sizeof(uint8_t));

  uint8_t *ptr = mmap(NULL, sizeof(*ptr), PROT_READ | PROT_WRITE, MAP_SHARED, fd, 0);
  printf("%d\n", *ptr);
}
```
