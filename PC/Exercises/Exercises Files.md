```C
#include <stdio.h>  
#include <stdlib.h>  
#include <errno.h>  
#include <string.h>  
  
// A very efficient way would be to read up to the maximum read buffer size (in linux is 4096)  
// Reading character by character is very inneficient  
// A more efficient approach would be to read line by line  
#define SIZE 2000  
  
int histoLine(char *line, int *tab){  
    int count = 0;  
  
    for(int i=0;i<strlen(line);i++){  
        tab[(int)line[i]]++;  
        count++;  
    }  
  
    return count;  
}  
  
void printHistogram(int *tab, int count){  
    int i = 0;  
    // fun fact: In ANSI C, you can't do for(int i=0)  
    for(i=32;i<127;i++){  
        printf("%c - %4d - %.3f\n", i, tab[i], ((float)tab[i]*100)/count);  
    }  
}  
  
void printHistogramInFile(int *tab, int count){  
    int i = 0;  
    FILE *out = NULL;  
    if( (out = fopen("histogram.txt","w")) == NULL){  
        perror(NULL);  
        exit(-5);  
    }  
  
    for(i=32;i<127;i++){  
        fprintf(out, "%c - %4d - %.3f\n", i, tab[i], ((float)tab[i]*100)/count);  
    }  
    if(fclose(out) != 0){  
        perror(NULL);  
        exit(-7);  
    }  
}  
  
  
int main(void){  
    FILE *f = NULL;  
    int tab[255]; // for letter histogram  
    memset(tab, 0, 255*sizeof(int));  
    int count = 0;  
  
    f = fopen("scrisoare.txt", "r");  
    if(f == NULL){  
        perror("Eroare la deschidere fisier");  
        exit(-1);  
    }  
    printf("Am deschis fisierul\n");  
  
    char line[SIZE];  
    while( (fgets(line, SIZE, f)) != NULL){  
        printf("%s", line);  
        count += histoLine(line, tab);  
    }  
  
    if(fclose(f) != 0){  
        perror("Eroare la inchidere fisier:");  
        exit(-2);  
    }  
    printf("Am inchis fisierul\n");  
  
    printHistogram(tab, count);  
  
    printHistogramInFile(tab, count);  
  
    return 0;  
}
```