## Enum
- Declaration:
```C
enum my_enum{
	constanta_1,
	constanta_2,
	constanta_3
};
```
- TODO: What happens behind the scenes when declaring an anonymous enum?
- Usage:
```C
int main(){
	enum my_enum var1; // la baza var1 este un int, unde indexarea incepe de la 0
	// constanta_1 = 0, constanta_2 = 1, constanta_3 = 2
	var1 = constanta_2;
	if(var1 == constanta_3){
		// 
	}
	var1 = var1 + 1; // => var1 = constanta_3
	var1 = 17; // this works but has no constant correspondence
}
```

```C
#include <stdio.h>
enum saptamana{
	luni = 4,
	marti = 78,
	miercuri = 15,
	joi = 0,
	vineri = 34,
	sambata,
	duminica
};
// by default, if the value is not specified, it will just be incremented by one
// that means here sambata will be 35
int main(void){
	enum saptamana zi;
	zi = marti;
	printf("%d\n", zi);
	if(zi == marti){
		printf("marti\n");
	}
	// it is impossible to convert an enum to string(its correspondence) since unde the hood they're just ints
	switch(zi){
		case luni:
		case marti:
			printf("luni sau marti\n");
			break;
		default:
			printf("restul\n");
			break;
	}

	// we can also use enums as pointers
	enum saptamana *zi2;
	zi2 = &zi;
	zi2 = malloc(sizeof(enum saptamana)); // TODO: Check this
	
	free(zi);
	
	return 0;
}
```
- ``sizeof(enum)`` will be 4, since it's a signed integer.

## Struct
- Memory for each field is allocated independently.
- Declaration:
```C
struct my_struct{
	int a;
	char text[100];
	float f;
	char *p;
}; // this occupies a total of 116 bytes
```
- Usage:
```C
int main(void){
	struct my_struct s;
	
	s.a=15;
	strcpy(s.text, "hello");
	if(s.f>3.15){
		//...
	}
	s.p = malloc(sizeof(char));
}
```

- However,
```C
#include <stdio.h>
#include <stdint.h>
struct my_struct{
	uint32_t a;
	char ch;
}
int main(void){
	struct my_struct str;
	printf("%ld\n", sizeof(str));
}
```
- !!!! This program will display 8 even though ``sizeof(uint_32) + sizeof(char) = 5``. This happens because of the padding left in memory. After the ``char`` memory is allocated, a padding of ``3 bytes is left`` to acomodate further allocation of variables. 

- In fact, the size will be the same for this struct as well:
```C
#include <stdio.h>
#include <stdint.h>
struct my_struct{
	uint32_t a;
	char ch1;
	char ch2;
	char ch3;
	char ch4;
}
int main(void){
	struct my_struct str;
	printf("%ld\n", sizeof(str));
}
```

- Another alignment problem:
```C
#include <stdio.h>
#include <stdint.h>

struct my_struc2{
	uint16_t b;
	uint32_t a;
	char ch;
}

struct my_struc{
	uint32_t a;
	uint16_t b;
	char ch;
}

int main(void){
	struct my_struct str;
	struct my_struct2 str2;
	
	printf("%ld\n", sizeof(str));
	printf("%ld\n", sizeof(str2));
	return 0;
}
```
Output:
```Bash
8
12
```
- Struct2 will be allocated like this:
	- (uint16_t)2bytes
		- -- because uint32_t needs to be stored at a address that a multiple of 4, a padding of two bytes will be needed
		- (padding)2 bytes
			- (uint32_t)4 bytes
				- (char)1 byte
					- (padding)3 bytes
	- This results in 12 bytes
	
- Struct1 will be allocated like this:
	- (uint32_t)4bytes
		- (uint16_t)2 bytes
			- (char)1 byte
				- (padding)1 byte

- By default, compilers leave struct allocation and alignment alone, but by specifying a flag it can realign them. TODO: Find flag

- This can be very inefficient when declaring an array of structs. The solution is to assure that the fields are in order of their multiples, so the least amount of padding is allocated.

#### Finding "deplasamentul" dintre structuri
- With pointer difference
```C
struct my_struc{
	uint32_t a;
	uint16_t b;
	char ch;
}
int main(void){
	char *p = (char*)&p;
	char *q = (char*)&s.ch;
}

```
- With the ``offsetof`` macro:

#### Operations
- Assignment between structs executes ``memcpy`` behind the scenes (slide 277).
- We cannot use ``+, -, /, *, >, <, etc.`` operators between structs.

- The ``->`` operator was invented to replace ``*().`` when using pointers to structures.
```C
#include <stdlib.h> 
#include <string.h> 
struct MyStruct { 
	int n; 
	char text[10]; 
}; 
int main(void) { 
	struct MyStruct *str1; 
	str1 = malloc(sizeof(struct MyStruct)); 
	
	str1->n = 123; // same as (*str1).n = 123
	strcpy(str1->text, "salut"); 
	
	struct MyStruct str2; 
	str2 = *str1; 
	
	return 0; 
}
```

TODO: Slide 281 (clase de precedenta si exemple pt operatorul unar ``->``)


## Union
- The difference between union and structs is that the memory is shared between all fields. The size will be the size of the biggest element inside the union.

- Declaration:
```C
union my_union{
	uint32_t n;
	char text[4];
}
```
- This means, when we modify ``n``, ``text`` will also be modified since they share the same memory zone.
- Usage when reading big CSV files and creating complex data structures from that data.

- Usage:
```C
union my_union{
	uint32_t n;
	char text[4];
}
int main(void){
	strcpy(u.text,"abc");
	// that means u.n  = 0x00636261, because 00 is the NULL character at the end
}
```

## User-defined types
``typedef definitie_tip identificator_nume_tip ;``