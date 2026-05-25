# Week 3:

## Struct in C/C++

```C++
\#include <iostream>
using namespace std;

// creating a struct:
typedef struct 
{
	string name;
	string number;
} person();
/* 
now we have created a stuct named person holds a name and a number
in two strings, a simple data structure 
*/

int main()
{
	person people[3]; // to hold the data for 3 persons
	people[0].name = "David";
	people[0].number = "+1-617-495-1000";
	// now we have the data of David saved in the array of type struckt
}

// NOTE: in C we have to write typedef struct while in C++ we can directly write Struct
```

  

## Sorting methods:

### ==Selecting sort:==

==Start at the beginning of the list and just keep looping through it to find the smallest number and replacing it with the first number in the list, then look for the smallest again and replace it with the second in the list, and so on….==

  

Example : ==7 2 5 4 1 6 0 3==

E1 : 0 2 5 4 1 6 7 3

E2 : 0 1 5 4 2 6 7 3

E3 : 0 1 2 4 5 6 7 3

E4 : 0 1 2 3 5 6 7 4

E5 : 0 1 2 3 4 6 7 5

E6 : 0 1 2 3 4 5 7 6

E7 : 0 1 2 3 4 5 6 7

  

Now, we can clearly notice that to sort 8 unsorted numbers we needed 7 iterations, which means that if we have 1000 000 000 unsorted numbers we may need 1000 000 000 - 1 iterations in the worst case, which is non-efficient at all.

Calculating the number of steps:

(n - 1) + (n - 2) + (n - 3) + (n - 4) + (n - 5) + (n - 6) + … + … + 1.

therefore we could write this into a mathematical formula: n(n - 1) / 2 = (n^2 ) x n / 2 = (n^2 / 2) - n/2

therefore this is an ==O(n^2).==

  

### ==Bubble Sort:==

Start at the beginning and ==SWAP== ==every two adjacent numbers if the second is greater than the first==

till reaching the end, and if the array is still not sorted, loop again !

  

Example : ==7 2 5 4 1 6 0 3==

E1 : 2 7 5 4 1 6 0 3

2 5 7 4 1 6 0 3

2 5 4 7 1 6 0 3

2 5 4 1 7 6 0 3

2 5 4 1 6 7 0 3

2 5 4 1 6 0 7 3

2 5 4 1 6 0 3 7

  

E2 : ==2 5 4 1 6 0 3 7==

2 4 5 1 6 0 3 7

2 4 1 5 6 0 3 7

2 4 1 5 0 6 3 7

2 4 1 5 0 3 6 7

  

E2 : ==2 4 1 5 0 3 6 7==

2 1 4 5 0 3 6 7

2 1 4 0 5 3 6 7

2 1 4 0 3 5 6 7

  

E4 : ==1 2 4 0 3 5 6 7==

1 2 0 4 3 5 6 7

1 2 0 3 4 5 6 7

  

E5 : ==1 0 2 3 4 5 6 7==

1 0 2 3 4 5 6 7

  

E6 : ==1 0 2 3 4 5 6 7==

0 1 2 3 4 5 6 7

  

And the Complexity is still ==O(n^2).==

  

### ==Recursion:==

==Recursion== is a general programming technique where a function calls itself to solve a smaller version of the problem until a base condition is met.  
  
  

Example with code:

==iteration:==

```C
\#include <cs50.h>
\#include <stdio.h>
void draw(int n);

int main()
{
    int height = get_int("Height: ");
    draw(height);

    return 0;
}

void draw(int n)
{
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < i + 1; j++) {
            printf("#");
        }
        printf("\n");
    }
}
```

==same but with recursion:==

```C
\#include <cs50.h>
\#include <stdio.h>
void draw(int n);

int main()
{
    int height = get_int("Height: ");
    draw(height);

    return 0;
}

void draw(int n)
{
    // make sure that the recursion will not be repeated blindly
    if (n <= 0)
        return;
    // print a pyramid of hight n - 1
    draw(n - 1);

    // print one more row
    for (int i = 0; i < n; i++) 
        printf("#");
    printf("\n");
}
```

  

==Conclusion:== Recursion is **the process of defining a problem (or the solution to a problem) in terms of (a simpler version of) itself**. For example, we can define the operation "find your way home" as: If you are at home, stop moving. Take one step toward home. "find your way home".  
  
  

### ==Merge Sort:==

  

Pseudo code:

_`If only one number`_

_`Quit`_

_`Else`_

_`Sort left half of numbers`_

_`Sort right half of numbers`_

_`Merge sorted halves`_

  

How does it works:

. Divide the array into two halves

. Sort the right half array

. Sort the left half array

. Create a new empty array

. Now compare between the first number in the right array and the first number in the left array.

. Add the smaller one to the empty array.

. Compare again between the two halves.

  

==Conclusion: it is simply an algorithm to repeat which is:==

==.== ==Divide into two==

==.== ==Sort the left half==

==.== ==Sort the right half==

==.== ==Merge==

==. Repeat==

  

  

Example :

  

==7 2 5 4 1 6 0 3==

0 2 5 4 1 6 7 3

==0 2 5 4 1 6 7 3==

==0 2 4 5 1 6 3 7==

0 2 4 5 1 3 6 7

==0 1 2 3 4 5 6 7==

  

  

The complexity of Merge Sort is  **log₂(n) which means** ==**log(n)**==

  

Visualization here : ==[https://www.cs.usfca.edu/~galles/visualization/ComparisonSort.html](https://www.cs.usfca.edu/~galles/visualization/ComparisonSort.html)==

  

  

  

  

# Week 4:

  

  

. Colors and graphics are represented in hexadecimal “Base 16” : 0 1 2 3 4 5 6 7 8 9 A B C D E F

. RGB colors are represented in red green and blue, represented in a code of 6 digits, each color represented in one pair of 2 digits

==Example:== ==# FFFFFF : Black color==

==#== ==00FF00 : Green color==

==#== ==0000FF : Blue color==

  

. FF represents 255 in decimal where FF = 16^0 * (15) + 16^1 * (15) = 255.

. A hexadecimal digit represents 4 bits of memory in binary so FF is a hexadecimal number which could be represented in 8 bits which is one byte.

  

  

. in code, computer use the hexadecimal numbering system to represent the place of a bit.

. Note: computer represents the 0 hexadecimal in the form of 0 X 0 and 1 in 1 X 1 and 1D in 0 X 1D

and so on

. How it looks like :

  

![[Screenshot_2024-12-17_165925.png]]

  

## Pointers

A **pointer** is a variable that **stores** the **memory address** of another variable as its value.  
  

```C
\#include <stdio.h>

int main()
{
    int n = 50;
    int *p = &n; // *p is a pointer to the address of the integer n

    printf("Address: %p\n", p);

    // we could also use pointers to make as follows:
    // tell the pointer to go to it's address and tell me what is finds there:

    printf("Value found in the address pointer value of n: %i\n", *p);
    // meaning : "*p" = go to that address, "%i" = find the integer inside that address

    return 0;
}
```

==Note:== ==the data type of a pointer is int* but the conventional way is to write the star beside the variable’s name as follows : int *pointer1 = &num.==

.Size of a Pointer is 8 bytes = 64 bits.

  

  

## Stings:

The pointer of a string is actually the same pointer of the string’s first character

therefore we can conclude that any pointer for a string will just simply hold the address of it’s first character.

  

Now we could say that a string datatype is actually char *name;

```C
\#include <cs50.h>
\#include <stdio.h>

int main(void)
{
    string s = "HI!";       // this will print "HI!"
    
    printf("%p\n", s);      // this will print the address of the first character
    printf("%p\n", &s[0]);  // this will also print the address of the first character
		printf("%p\n", &s[1])   // this will print the address of the second character
    char *s1 = "hi!";       // this is equivelent to string s1 = "hi!";
    
    printf("%s\n", s1);     // this will print "hi!"
}
```

  

## Pointers arithmetic:

```C
\#include <cs50.h>
\#include <stdio.h>

int main(void)
{
    char *s = "HI!";

    printf("%c", *s);        // prints the character in the first address the string s
    printf("%c", *(s + 1));  // prints the character in the second address the string s
    printf("%c\n", *(s + 2));  // prints the character in the third address the string
}
```

  

## Malloc/Free:

if i make a string then i made another string and set it to be equal to the first one, this is not duplicating the string, it is just setting the pointer of the second variable to be equal to the first one and this will cause them to be both the same string in the same place in the memory of the computer.

if i need to really duplicate a string i should use the function malloc() which stands for “memory allocate”, and to use it i have to include other header files:

```C
\#include <stlib.h>
\#include <ctype.h>
```

  

malloc will just free up some bytes in the memory for me to use, i should fill them up with my string to be duplicated.

```C
\#include <cs50.h>
\#include <ctype.h>
\#include <string.h>
\#include <stdlib.h>
\#include <stdio.h>

int main(void)
{
    char *s = get_string("s: ");
    char *t = malloc(strlen(s) + 1); // we add one for the no character "\0"

    for (int i = 0, n = strlen(s) + 1; i < n; i++)
    {
        t[i] = s[i];
    }

    printf("s: %p\n", s);
    printf("t: %p\n", t);
    printf("s: %s\n", s);
    printf("t: %s\n", t);
}
```

we can also use the function strcopy() instead of the loop.

we have to free the memory of malloc before exiting the program : free(t).

we have to make sure that malloc doesn’t return the address NULL which means that there is no enough memory to use.

  

```C
// gcc -o string_allocate string_allocate.c -lcs50
\#include <cs50.h>
\#include <ctype.h>
\#include <string.h>
\#include <stdlib.h>
\#include <stdio.h>

int main(void)
{
    char *s = get_string("s: ");
    char *t = malloc(strlen(s) + 1);

    if (t == NULL)
    {
        return 1;
    }

    strcopy(t, s);

    printf("s: %p\n", s);
    printf("t: %p\n", t);
    printf("s: %s\n", s);
    printf("t: %s\n", t);

    if (strlen(t) > 0)
    {
        t[0] = toupper(t[0]);
        printf("%s\n", t); // this will only change t and will not change s because t is allocated
        printf("%s\n", s); // s will keep the same and will not be capitalized 
    }

    free(t); 
}
```

  

## Deeply understanding data types:

all c datatypes are actually pointers for the starting of the free bytes

Example:

```C
// this is the code we usually write:
int x;
int y;

x = 43;
y = 13;
```

```C
// this is how a compiler understand it:
int *x;
int *y;

*x = malloc(sizeof(int));
*y = malloc(sizeof(int));

*x = 42;
*y = 13; 
```

## Passing by reference:

  

In an example when we want to make a function that swap two variables by having their values, it doesn’t work out doing what we actually need, because we will be just swapping other variables with the same name but in another scope.

So in this example we will pass by reference, which is just passing the address of the variables to the swapping function, not their names:

```C
\#include <cs50.h>
\#include <stdio.h>

void swap (int *a, int *b);

int main(void)
{
    int a = 50;
    int b = 40;

    printf("a value is: %i and b value is %i\n", a, b);

    printf("Swaping...\n");
    swap(&a, &b);
    
    printf("a value is: %i and b value is %i\n", a, b);
}

void swap (int *a, int *b)
{
    int tmp = *a;
    *a = *b;
    *b = tmp;
}
```

  

## **[File I/O](https://cs50.harvard.edu/x/2024/notes/4/#file-io)**

- You can read from and manipulate files. While this topic will be discussed further in a future week, consider the following code for `phonebook.c`:
    
    ```C
    \#include <cs50.h>
    \#include <stdio.h>
    \#include <string.h>int main(void)
    {
        // Open CSV fileFILE *file = fopen("phonebook.csv", "a");
    
        // Get name and numberchar *name = get_string("Name: ");
        char *number = get_string("Number: ");
    
        // Print to filefprintf(file, "%s,%s\n", name, number);
    
        // Close filefclose(file);
    }
    ```
    
    Notice that this code uses pointers to access the file.
    
- You can create a file called `phonebook.csv` in advance of running the above code. After running the above program and inputting a name and phone number, you will notice that this data persists in your CSV file.
- If we want to ensure that `phonebook.csv` exists prior to running the program, we can modify our code as follows:
    
    ```C
    \#include <cs50.h>
    \#include <stdio.h>
    \#include <string.h>int main(void)
    {
        // Open CSV fileFILE *file = fopen("phonebook.csv", "a");
        if (!file)
        {
            return 1;
        }
    
        // Get name and numberchar *name = get_string("Name: ");
        char *number = get_string("Number: ");
    
        // Print to filefprintf(file, "%s,%s\n", name, number);
    
        // Close filefclose(file);
    }
    ```
    
    Notice that this program protects against a `NULL` pointer by invoking `return 1`.
    
- We can implement our own copy program by typing `code cp.c` and writing code as follows:
    
    ```C
    \#include <stdio.h>
    \#include <stdint.h>typedef uint8_t BYTE;
    
    int main(int argc, char *argv[])
    {
        FILE *src = fopen(argv[1], "rb");
        FILE *dst = fopen(argv[2], "wb");
    
        BYTE b;
    
        while (fread(&b, sizeof(b), 1, src) !=0)
        {
            fwrite(&b, sizeof(b), 1, dst);
        }
    
        fclose(dst);
        fclose(src);
    }
    ```
    
    Notice that this file creates our own data type called a `BYTE` that is the size of a `uint8_t`. Then, the file reads a `BYTE` and writes it to a file.
    
- BMPs are also assortments of data that we can examine and manipulate. This week, you will be doing just that in your problem sets!

  

Simple to do list:

```C
\#include <iostream>
\#include <stdio.h>
\#include <string>

using namespace std;

int main()
{
    FILE *list  = fopen("list.txt", "a+");
    
    if (list == NULL)
    {
        return 1;
    }

    string task;

    cout << "Enter a new task: \n";
    getline(cin, task);

    fprintf(list, "%s\n", task.c_str());

    rewind(list);
    char line[100];
    while (fgets(line, sizeof(line), list) != NULL)
    {
        cout << line;
    }

    fclose(list); 

    return 0;
}
```

### **Comparison with** `**fseek()**`:

|   |   |
|---|---|
|Function|Purpose|
|`rewind()`|Resets file pointer to the beginning of the file.|
|`fseek()`|Moves the file pointer to a specific location in the file.|

  

# Week 5, Data structures:

## Abstract datatypes:

. FIFO : First Input First Output.

. Stacks : LIFO; Last Input First Output

  

### Reallocating memory size for new inputs of an array:

```C
\#include <stdio.h>
\#include <stdlib.h>

int main(void)
{
    int* list = malloc(3 * sizeof(int));

    if (list == NULL)
    {
        return 1;
    }

    list[0] = 1;
    list[1] = 2;
    list[2] = 3;

    int* tmp = malloc(4 * sizeof(int));

    if (tmp == NULL)
    {
        free(list);
        return 1;
    }

    for (int i = 0; i < 3; i++)
    {
        tmp[i] = list[i];
    }
    tmp[3] = 4;
    free(list);
    list = tmp;

    for (int i = 0; i < 4; i++)
    {
        printf("%i\n", list[i]);
    }
    free(list);
}
```

  

## Linked List:

```C
\#include <stdio.h>
\#include <stdlib.h>

typedef struct node // give me a deffinention for a strucure called node
{
    int num;
    struct node *next;
} node;

int main(int argc, char *argv[])
{
    node *list = NULL;

    for (int i = 1; i < argc; i++)
    {
        int num = atoi(argv[i]);
        
        node *n = malloc(sizeof(node));

        if (n == NULL)
        {
            // free memory
            return 1;
        }
        n->num = num;    // (*n).num = num;
        n->next = list;
        list = n;
    }
    // print whole list:
    node *ptr = list;
    while (ptr != NULL)
    {
        printf("%i\n", ptr->num);
        ptr = ptr->next;
    }
}
```

### The idea of the implementation:

1. node *list = NULL; ==// this will create a new node (structure named node) and set a variable called list to be a pointer of the node and initialize the pointer to be NULL.==
2. node *n = malloc(sizeof(node)); ==// this will make a new temp variable, the variable n is a pointer which points to a memory allocated node.==
3. n → num = num; ==// the user enters a number and it is saved on the variable called num, so the first part of this line n → : means make the pointer n points to the variable num inside the structure node (remember that *n was actually pointing to a node, so it can easily access the variable num which is inside the same node), the second part of the line = num : this means set the num variable inside the node structure to equal the value that saves the user input and named also num.==
4. n → next = list; ==// this will save the previous pointer saved in list to the next variable inside the node structure.==
5. list = n; ==// this will save the current pointer saved in n in the list variable, and the list variable will be treated to be the previous pointer in the following etiration so that we could save it to the following next variable, and so on, and now all saved variables are connected together.==
