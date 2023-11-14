#### Reading an array of an unknown size:
- Using ``realloc`` would be a bad approach, so we allocate memory in chunks.
- Use ``-g`` in gcc for debug, so tools like ``valgrind --leak-check=full`` to also get the line where the memory leak happened. 
	- ``Valgrind`` can be used to both detect memory leaks and know where segmentation faults are happening. 
```C
#include <stdio.h>  
#include <stdint.h>  
#include <stdlib.h>  
  
#define CHUNK 2000  
  
uint16_t *read_array_dynamic(int *size){  
    uint16_t temp;  
    uint16_t *array = NULL;  
    int current_size = 0, i = 0;  
  
    while(scanf("%hu", &temp) == 1){  
        if(i == *size){  
            // Nu mai am loc  
            current_size += CHUNK;  
            if ( (array = realloc(array, current_size * sizeof(uint16_t))) == NULL){  
                return NULL;  
            }  
        }  
        array[i] = temp;  
        i++;  
    }  
    // we can do a realloc with i size to remove the last trailing memory  
    if ( (array = realloc(array, i*sizeof (uint16_t))) == NULL ){  
        *size = 0;  
        return NULL;  
    }  
    *size = i;  
    return array;  
}  
  
void print_array(uint16_t* arr, int size){  
    for(int i=0;i<size;i++){  
        printf("%d ", arr[i]);  
    }  
    printf("\n");  
}  
  
int main(void){  
    uint16_t *array = NULL;  
    int size = 0;  
    if( (array = read_array_dynamic(&size)) == NULL ){  
        printf("Eroare\n");  
    }  
    print_array(array, size);  
  
    free(array)  
  
    return 0;  
}

```


```C
// Reading a string of an unknown size  
// Fgetsc doesn't solve the problem since it wants the size  
#include <stdio.h>  
#include <stdlib.h>  
#define CHUNK 32  
char *read_line(void){  
    char *line = NULL;  
    int ch, current_size = 0, i = 0;  
    while( (ch= getchar()) != EOF){  
        if(ch=='\n'){  
            break;  
        }  
        if(i==current_size) {  
            current_size += CHUNK;  
            line = realloc(line, current_size * sizeof(char));  
        }  
        line[i++] = ch;  
    }  
    if(line == NULL){  
        return NULL;  
    }  
    if(i==current_size) {  
        current_size ++;  
        line = realloc(line, current_size * sizeof(char));  
    }  
    line[i] = '\0';  
    return line;  
}  
  
int main(void){  
    char *string = NULL;  
    string = read_line();  
    printf("%s\n", string);  
    return 0;  
}
```