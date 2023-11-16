- Word Count Clone (``wc`` utility in Linux)
```C
#include <stdio.h>  
#include <ctype.h>  
  
// wget http://staff.cs.upt.ro/~valy/pt/random_text.txt  
  
/*  
    student@b418b-9:~$ cat random_text.txt | wc       
	    1225   64600  464135
    student@b418b-9:~$ ./a.out < random_text.txt  
	    1225   64600  464135  
*/  
  
void word_count(int *lines, int *words, int *characters){  
  
    int w = 0, c = 0, l = 0;  
    char letter, lastLetter=0;  
  
    while((letter = getchar()) != EOF){  
       c++;  
       if(isalpha(lastLetter) && isspace(letter)){  
          w++;  
       }       
       if(letter == '\n'){  
          l++;  
       }  
       lastLetter = letter;  
    }  
    *lines = l;  
    *words = w;  
    *characters = c;  
}  
int main(void){  
  
    int lines, words, characters;  
    word_count(&lines, &words, &characters);  
  
    printf("%7d %7d %7d\n",lines, words, characters);  
  
    return 0;  
}
```


- Histogram of the letter usage:
```C
#include <stdio.h>  
#include <ctype.h>  
#include <stdint.h>  
#include <stdlib.h>  
#include <string.h>  
// wget http://staff.cs.upt.ro/~valy/pt/random_text.txt  
  
  
void word_count(uint32_t *freq, int *lines, int *words, int *characters, int *alpha_characters){  
  
    int w = 0, c = 0, l = 0, a_c = 0;  
    char letter, lastLetter=0;  
  
    while((letter = getchar()) != EOF){  
       c++;  
       if(isalpha(letter)){  
          a_c++;  
          freq[(int)letter]++;  
       }  
       if(isalpha(lastLetter) && isspace(letter)){  
          w++;  
       }       
       if(letter == '\n'){  
          l++;  
       }  
       lastLetter = letter;  
    }  
    *lines = l;  
    *words = w;  
    *characters = c;  
    *alpha_characters = a_c;   
}  
int main(void){  
  
    int lines, words, characters, alpha_characters;  
    int first_ch = (int)'A', last_ch = (int)'z';  
    // malloc also need to set first last_ch + 1 characters  
    // this error was caught by valgrind    
    uint32_t *freq = (uint32_t*)malloc( (last_ch+1) * sizeof(uint32_t) );  
    memset(freq, 0, (last_ch+1) * sizeof(uint32_t));  
    word_count(freq, &lines, &words, &characters, &alpha_characters);  
    for (int i=first_ch; i<=last_ch; i++){  
       if(isalpha(i)){  
          printf("%5c %.3f%%\n", (char)i, ((double)freq[i]/alpha_characters)*100);  
       }  
    }  
    free(freq);  
  
    //printf("%7d %7d %7d\n",lines, words, characters);  
  
    return 0;  
}
```