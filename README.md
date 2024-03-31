#include<stdio.h>
#include <string.h>
#include <stdlib.h>
/*
Zina Ghottis, Younes Tita, Dina Daoudi,
*/
typedef struct crs {
  char crs_name[12], crs_no[7];
  int n_crdh;
  char crs_grade;
} course;

typedef struct st {
  int paid;
  char ID[7];
  char fname[30], lname[30];
  int numCourses, numcredits;
  course crs[10];
  double gpa;

  struct st *next;
  struct st *prev;
} student;


student *head = NULL;
int numstudent, creditPrice;

void sort(course* crs, int numCourses);
void swap(course* a, course* b);

int calculateNCredits(student* st);
void menu();
student* search(char* ID);
void update();
void deletestudent(student* stoDel);
void search_student();
void save_exit();
int TuitionCheck(char t);
void read_file();
course set_crs_info(course *c);
void print_crs_info(course *c);
void show_crs_crdh(course *c);
void show_crsnumber(course*c);
void Printlist(student * st);
int countcoursesh();
double GPA_calculator(student* st);
student* addstudent();




int main() {

  student *New_st;
  int choice;
  char ID[7];
  read_file();

  do {
    menu();
    scanf("%d", &choice);
    getchar();
    switch (choice) {
    case 1:
        addstudent();
        save_exit();
        break;
    case 2:
        update();
        save_exit();
    break;
      //

    case 3:
        printf("please enter the id of the student to be deleted : ");
        scanf("%s",ID);
        deletestudent(search(ID));
        save_exit();
      break;
      //

    case 4:
        search_student();
      break;
      //

    case 5:
        printf("Thank you for using our program.\n");
        save_exit();

      break;

    default:
      printf("\n\t\tWrong input ! Please try again !\n\n");
    }

  } while (choice != 5);
}


void sort(course* crs, int numCourses) {

    for(int i = 0; i < numCourses; i++) {
        for (int j = 0; j < numCourses - 1; j++) {
            if (strcmp(crs[j].crs_no, crs[j+1].crs_no) > 0)
                swap(&crs[j], &crs[j+1]);
        }
    }
}

void swap(course* a, course* b) {
    course tmp = *a;
    *a = *b;
    *b = tmp;
}


student* search(char* ID) {
    student* w = head;

    while(w  && strcmp(w->ID, ID) ) {
        w = w->next;
    }
    return w;
}
void menu() {
    printf("\n\t\t------------- Menu --------------\n");
    printf("\n\t\t 1- Add a student\n");
    printf("\n\t\t 2- Update a student's course grade\n");
    printf("\n\t\t 3- Delete a student\n");
    printf("\n\t\t 4- Search for a student\n");
    printf("\n\t\t 5- Exit\n");
    printf("\n\t\t--------------------------------------\n");
    printf("\n\t\t Please enter your choice: \n");
  }
void deletestudent(student* stoDel) {
    char ID[7];
    /*
    printf("please enter the id of the student to be deleted : ");
    getchar();
    scanf("%s",ID);
    */

    if (head == NULL || stoDel == NULL)
    {
        printf(" the student you're looking for does not exist:");
    }
        
 
    if (head == stoDel){
        head = stoDel->next;
        head->prev = NULL;
    }
        
    else if (stoDel->next != NULL){
        stoDel->next->prev = stoDel->prev;
        stoDel->prev->next = stoDel->next;
    }
    
   
    else if (stoDel->prev != NULL){
        stoDel->prev->next = NULL;
    }
        
    numstudent--;
 
    free(stoDel);
    return;
}
void update(){
    char choice;
        do {

            char student_ID[7];
            printf("\n enter the student ID :");
            scanf("%s",&student_ID);

            student* st = search(student_ID);
            char Crs_no[7], Crs_grade;

            if( st==NULL)
            {
                printf("the id entered does not exist.\n");
            }
            else{                    
                printf("enter the course number:");
                scanf("%s", Crs_no);
                printf("enter the new grade:");
                scanf(" %c", &Crs_grade);
                for (int i = 0; i < st->numCourses; i++) {
                    if (!strcmp(Crs_no, st->crs[i].crs_no)){
                        st->crs[i].crs_grade = Crs_grade;
                        break;
                    }
                }
            }
            printf("Do you want to update another student's information? (Y/N)\n");
            scanf(" %c", &choice);

        }while(choice != 'n' && choice != 'N');
    }
void search_student(){
    char Id[7];
    printf("please enter the Id of student you're looking for: ");
    scanf("%s",Id);

    student* found = search(Id);   

    if (found == NULL )
    {
        printf("the student you're looking for does not exist:\n");
    } 
    else 
    {
        if(found->paid == 0 )
        {
            printf("the grade report cannot be displayed until the tuition is paid:\n");
        } 
        else
        {            
            printf("The Student Name is %s %s\n ",found->fname, found->lname);
            printf("The Student ID is %s\n ",found->ID);
            printf("The Number of courses enrolled is %d \n", found->numCourses);
            printf("Course No \t Course Name \t Credits \t Grade \n");
            for(int i=0; i< found->numCourses; i++ )
            {
            printf("%-8s \t %-8s \t %-8d \t %-8c\n",  found->crs[i].crs_no, found->crs[i].crs_name, found->crs[i].n_crdh, found->crs[i].crs_grade) ;
            }
            printf("Total number of credits : %d\n", found->numcredits);
            printf("Mid-Semester GPA : %.2f\n", found->gpa);

        }

    }

}

student* addstudent() {  
  student *st;
  int i = 0;
  char c;
  st = (student *)malloc(sizeof(student));

  printf("\nEnter the first and last name of the student: ");
  scanf("%s %s", st->fname, st->lname);

  printf("\nEnter the ID of the student: ");
  scanf("%s", st->ID);

  printf("Has the student paid their tuition? (Y/N)\n");
  scanf(" %c", &c);
  st->paid = TuitionCheck(c);

  printf("\nEnter the number of courses the student is enrolled in: ");
  scanf("%d", &st->numCourses);

  
  while (i < st->numCourses) {
    
    printf("\nPlease enter the course number: ");
    scanf("%s",st->crs[i].crs_no); 
    printf("\nPlease enter the course name: ");
    scanf("%s",st->crs[i].crs_name);
    printf("\nPlease input the number of credit hours for this course: ");
    scanf("%d", &st->crs[i].n_crdh);
    printf("\nPlease input the course grade: ");
    scanf(" %c",&st->crs[i].crs_grade);    
    ++i;
  }

    sort(st->crs, st->numCourses);


  st->numcredits = calculateNCredits(st);
  st->gpa = GPA_calculator(st);

    if (!head)
        head = st;
    else {
        st->next = head;
        head->prev = st;
        head = st;
    }

    numstudent++;

  return st;
}

void save_exit()
{
    FILE *ptr;
    student *st = head;
    char c;
    ptr = fopen("stData.txt","w");
        fprintf( ptr, "%d %d\n", numstudent, creditPrice);
        while( st != NULL)
        {
            fprintf(ptr,"%s %s %s %c %d\n", st->fname, st->lname, st->ID, st->paid == 1 ? 'Y': 'N' ,st->numCourses );
            for(int i=0; i< st->numCourses; i++ ){
                fprintf(ptr, "%s %s %d %c\n", st->crs[i].crs_name, st->crs[i].crs_no, st->crs[i].n_crdh, st->crs[i].crs_grade );
            }
            st = st->next;
            if (st != NULL)
                fprintf(ptr, "\n");
            //if (st->next) fprintf(ptr, "\n");
        }
        fclose(ptr);
}
int TuitionCheck(char t) {

  if (t == 'Y')
    return 1;
  else
    return 0;
}
void read_file() {
  FILE *ptr;
  char c;
  ptr = fopen("stData.txt", "r");
  if (ptr == NULL) {
    printf("Error! File cannot be opened");
  }
  fscanf(ptr, "%d", &numstudent);
  fscanf(ptr, "%d", &creditPrice);
  for (int i = 0; i < numstudent; i++) {

    student *newnode = (student *)malloc(sizeof(student));
    fscanf(ptr, "%s", newnode->fname);
    fscanf(ptr, "%s", newnode->lname);
    fscanf(ptr, "%s %c %d", newnode->ID, &c, &newnode->numCourses);
    newnode->paid = TuitionCheck(c);
    for (int j = 0; j < newnode->numCourses; j++) {
      fscanf(ptr, "%s %s %d %c", newnode->crs[j].crs_name,
             newnode->crs[j].crs_no, &newnode->crs[j].n_crdh,
             &newnode->crs[j].crs_grade);
    }
    sort(newnode->crs, newnode->numCourses);
    newnode->next = newnode->prev = NULL;
    newnode->numcredits = calculateNCredits(newnode);
    newnode->gpa = GPA_calculator(newnode);
    fgetc(ptr);
    if (!head)
      head = newnode;
    else {
      newnode->next = head;
      head->prev = newnode;
      head = newnode;
    }
  }
}
course set_crs_info(course *c) {

  printf("please enter the course name: ");
  gets(c->crs_name);
  printf("please input the course number: ");
  gets(c->crs_no);
  printf("please input the course grade: ");
  //getc(c->crs_grade);
  printf("please input the number of credit hours for this course: ");
  scanf("%d", &c->n_crdh);
  return *c;
}
void print_crs_info(course *c){
   
    printf("%s\t\t%s\t\t%d\t\t%c",c->crs_name,c->crs_no,c->n_crdh,c->crs_grade);
}
void show_crs_crdh(course *c){
    printf("the number of credit hours is: %d",c->n_crdh);

}
void show_crsnumber(course*c){
    printf("the course number is: %s", c->crs_no);
}
void Printlist(student * st) {

    FILE *ptr;
    ptr = fopen("sData.txt", "a");
    if (ptr == NULL) {
      printf("Error! File cannot be opened");
    }

    while (st != NULL) {
      //Printst(st);
      st = st->next;
      //fprintf(ptr, "%s", st->Name);
    }
  }
int countcoursesh() {
  int n, c, sum, i, a[1];
  int x=1;
  sum=0; 
  
  printf("\nEnter the number of courses the student is enrolled in: ");
  scanf("%d", &n);

  while (x <= n) {

    printf("\nPlease input the number of credit hours for these courses: ");
    scanf("%d", &c);
    
    for (i = 0; i < n; i++)
       sum = sum + c;

    ++x;
  }
  printf("\nThe number of credit hours for the student is %d", c);

  return 0;
}
double GPA_calculator(student* st) {

  int i;
  double sum = 0;
  for (i = 0; i < st->numCourses; i++) {
      switch(st->crs[i].crs_grade) {
        case 'A':
        sum += 4.0*st->crs[i].n_crdh;
        break;
        case 'B':
        sum += 3.0*st->crs[i].n_crdh;
        break;
        case 'C':
        sum += 2.0*st->crs[i].n_crdh;
        break;
        case 'D':
        sum += 1.0*st->crs[i].n_crdh;
        break;
        case 'F':
        sum += 0.0*st->crs[i].n_crdh;
        break;
      }
  }
  sum = sum / st->numcredits;

  return sum;
}

int calculateNCredits(student* st) {
    int ncredits = 0;
    for (int i = 0; i < st->numCourses; i++) {
        ncredits += st->crs[i].n_crdh;
    }
    return ncredits;
}
