## Basic thread

```C

void *function(void *arg)
{
  printf("In thread %s\n", (char *)arg);
  pthread_exit(0);
}

int main()
{
  pthread_t pid;
  pthread_create(&pid, NULL, function, "pid");
  pthread_join(pid, NULL); // wait for pid to terminate 
}

```
