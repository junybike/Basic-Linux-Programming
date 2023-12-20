## Basic Fork 

```C
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

int main()
{
  pid_t id = fork();
  if (id == 0) // Child process 
  {
    int parent_id = getppid();
    int condition = 1;
    if (!condition)
    {
      exit(EXIT_FAILURE); // child will not successfully terminate
    }
    exit(EXIT_SUCCESSFUL); // child terminate successfully
  }
  else // Parent process
  {
    int child_id = id;
    waitpid(id, &status, 0); // waits for child process (id) to terminate
  }
  if (WIFEXITED(wstatus))
  {
    // child terminated successfully
  }
  else
  {
    // child is not successfully terminated
  }
}
```

## 
