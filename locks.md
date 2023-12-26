## Pthread mutex lock

```
pthread_mutex_t mutexlock
pthread_mutex_lock(pthread_mutex_t *mutexlock)
pthread_mutex_unlock(pthread_mutex_t *mutexlock)
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

```
pthread_cond_t cond
pthread_cond_signal(pthread_cond_t *cond) // Signals to only one of waiting processes
pthread_cond_wait(pthread_cond_t *cond, pthread_mutex_t *mutex)
pthread_cond_broadcast(pthread_cond_t *cond) // Signals to wall waiting processes
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

```
sem_init(sem_t *sem, int pshared, unsigned int value)
sem_wait(sem_t *sem)
sem_post(sem_t *sem)
```

Example (Circular buffer):

```C
#include <pthread.h>
#include <semaphore.h>
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

#define SIZE 10

char bag[SIZE] = {0};
int empty = 0, candy = 0;
sem_t empty_cnt;
sem_t candy_cnt;
pthread_mutex_t mutex = PTHREAD_MUTEX_INITIALIZER;

void *takecandy(void *arg)
{
  for (;;)
  {
    sem_wait(&candy_cnt);
    pthread_mutex_lock(&mutex);

    empty = (empty + 1) % SIZE;

    pthread_mutex_unlock(&mutex);
    sem_post(&empty_cnt);
  }
}

int main()
{
  pthread t1;
  sem_init(&empty_cnt, 0, SIZE);
  sem_init(&candy_cnt, 0, 0);
  pthread_create(&t1, NULL, takecandy, NULL);

  for (int i = 0; ; i++)
  {
    sem_wait(&candy_cnt);
    pthread_mutex_lock(&mutex);

    bag[candy] = i;
    candy = (candy + 1) % SIZE;

    pthread_mutex_unlock(&mutex);
    sem_post(&candy_cnt);
  }
}
```
