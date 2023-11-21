#include <pthread.h>
#include <semaphore.h>
#include <stdio.h>
#include <unistd.h>
#include <stdlib.h>
 
#define MAXTHREAD 4  /* define # readers */
 
 
int access_database(int);     /* prototypes */
void non_access_database(); 
void write_database();
 
void* reader(void*);
void* writer(void*);
 
sem_t mutex;    /* controls access to rc */
sem_t db;       /* controls access to db */
sem_t q;        /* establish queue */
int rc = 0;     /* number of processes reading or wanting to */

int dbase[MAXTHREAD]; 

int main()
{
  pthread_t readers[MAXTHREAD],writerTh;
  int index;
  int ids[MAXTHREAD]; /*initialize readers and initialize mutex, 
			q and db-set them to 1 */

  //srand(time(NULL));

  sem_init(&mutex,0,1);
  sem_init(&db,0,1);
  sem_init (&q,0,1);
  for(index = 0; index < MAXTHREAD; index ++)  
    {
      ids[index]=index+1;  /* create readers and error check */
      if(pthread_create(&readers[index],0,reader,&ids[index])!=0){
	perror("Cannot create reader!");
	exit(1);                            
      }
    }
  if(pthread_create(&writerTh,0,writer,0)!=0){
    perror("Cannot create writer");     /* create writers and error check */
    exit(1);
  }
     
  pthread_join(writerTh,0);   
  sem_destroy(&mutex);
  sem_destroy(&db);
  sem_destroy (&q);
  return 0;
}
 
void* reader(void*arg)   /* readers function to read */
{
  int index = *(int*)arg;
  int value;
  while(1){
    sem_wait(&q);
    sem_wait(&mutex);
    rc++;
    if(rc == 1) sem_wait(&db);
    sem_post(&mutex);
     
    value = access_database(index-1);
    sem_post (&q);
    printf("Thread %d read %d\n",index, value);
    sleep(index);
    sem_wait(&mutex);
    rc--;
    if(rc == 0) sem_post(&db);
    sem_post(&mutex);   
    //non_access_database();
  }
  return 0;
}
;
void* writer(void*arg)      /* writer's function to write */
{
  while(1){
    //non_access_database();
    sem_wait (&q);
    sem_wait(&db);
     
    write_database();
    sem_post (&q);
    printf("Writer is now writing...Number of readers: %d\n",rc);
    sleep(1);
    sem_post(&db);
  }
  return 0;
}
 
int access_database(int index)
{
  return dbase[index];
}
 
 
void non_access_database()
{
  return; 
}

void write_database() {
  int i;
  for(i=0; i<MAXTHREAD; i++) {
    dbase[i] = rand()%1000;
    //printf("...writing: dbase[%d] = %d\n", i, dbase[i]);
  }
  return;
}
