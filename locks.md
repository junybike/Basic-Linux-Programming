## Pthread mutex lock

```C
pthread_mutex_t mutexlock = PTHREAD_MUTEX_INITIALIZER;
pthread_mutex_lock(&mutexlock);
pthread_mutex_unlock(&mutexlock);
```

Example:
```C
#include <pthread.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

int cnt = 0;
pthread_mutex_t mutexlock = PTHREAD_MUTEX_INITIALIZER;

void *function(void *arg)
{
  for (int i = 0; i < 100; i++)
  {
    pthread_mutex_lock(&mutexlock);
    cnt++;
    pthread_mutex_lock(&mutexlock);
  }
  pthread_exit(0);
}

int main()
{
  pthread_t t1;
  pthread_t t2;
  if (pthread_create(&t1, NULL, function, NULL) != 0)
    perror("pthread_create");
  if (pthread_create(&t2, NULL, function, NULL) != 0)
    perror("pthread_create");

  if (pthread_join(t1, NULL) != 0)
    perror("pthread_join");
  if (pthread_join(t2, NULL) != 0)
    perror("pthread_join");

  printf("cnt: %d", cnt);
  // With mutex lock: expected output = 200
  // Without mutex lock: expected output = mostly 100, less than 200
}

```
## Pthread conditional lock

```C
pthread_cond_t cond = PTHREAD_COND_INITIALIZER;
pthread_cond_signal(pthread_cond_t *cond);
pthread_cond_wait(pthread_cond_t *cond, pthread_mutex_t *mutex);
pthread_cond_broadcast(pthread_cond_t *cond);
```

Example:

```C
#include <pthread.h>
#include <stdio.h>
#include <stdlib.h>

pthread_mutex_t mutex = PTHREAD_MUTEX_INITIALIZER;
pthread_cond_t cond = PTHREAD_COND_INITIALIZER;
int candy = 0;

void *morecandy(void *arg)
{
  for (;;)
  {
    pthread_mutex_lock(&mutex);
    candy++;
    pthread_mutex_unlock(&mutex);
    pthread_cond_signal(&cond);
  }
  pthread_exit(0);
}

int main()
{
  pthread_t t1;
  pthread_create(&t1, NULL, morecandy, NULL);

  for (;;)
  {
    pthread_mutex_lock(&mutex);
    while (cnt == 0)
      pthread_cond_wait(&cond, &mutex);

    while (candy > 0)
      candy--;

    pthread_mutex_unlock(&mutex);
  }
  pthread_join(t1, NULL);
}
```

## Semaphore

```C

```
