Books
-----

* CheatSheet - http://claymore.engineer.gvsu.edu/~steriana/226/C.CheatSheet.pdf
* Learn C the Hard Way - http://c.learncodethehardway.org/book/
* GNU C Library - http://www.gnu.org/software/libc/manual/html_mono/libc.html


Hello World
-----------

hello.c

    #include <stdio.h>
    int main(){ printf("Hello, world.\n"); }

erf.c

    #include <math.h>  //erf, sqrt
    #include <stdio.h> //printf

    int main(){
      printf("The integral of a Normal(0, 1) distribution "
             "between -1.96 and 1.96 is: %g\n", erf(1.96*sqrt(1/2.)));
    }

Compilation
-----------

Compile with:

* -g - add symbols for debuggin
* -std=gnu11 - (GNU) use standards for POSIX and c11

    gcc erf.c -o erf -lm -g -Wall -O3 -std=gnu11

or compiler agnostic:

    c99 erf.c -o erf -lm -g -Wall -O3

Linking, Libraries and pkg-config
---------------------------------

* -i - include search path
* -L - library search path

    gcc -I /usr/local/include use_useful.c -o use_useful -L /usr/local/lib -luseful

Order matters. If you have a file named specific.o that depends on the Libbroad library, and Libbroad depends on Libgeneral, then you will need:

    gcc specific.o -lbroad -lgeneral
    
pkg-config maintains a repository of the libraries which can be searched for with:

    pkg-config --libs libxml-2.0
    
* Static linking - copying the contents of the library into the final executable
* Dynamic linking - resolving the library at runtime from the path

    export LD_LIBRARY_PATH=libpath:$LD_LIBRARY_PATH
    
or if compiling add the following to the Makefile:

    LDADD=-Llibpath -Wl,-Rlibpath
    
Headers
-------

As well as including headers are the top of your C program, with GCC you can include the headers:

    gcc -include stdio.h
    
Headers can be used to share functions.  You can also share variables across files using:

    extern int passcode;

Here documents 
--------------

Here docs are a standard POSIX feature.  They allow you to define a program using stdin on the command line like so:

    python - <<"XXXX"
    lines=2
    print "\nThis script is %i lines long.\n" %(lines,)
    XXXX

Datatypes
---------

Basic array:

    char *states[] = {
        "California", "Oregon",
        "Washington", "Texas"
    };
    
Pointers
--------

See - http://c.learncodethehardway.org/book/ex15.html

Generally define one with:

    int x = 5;
    char *pnt_content = x;

Print the address with:

    printf("Address is %p", *pnt_content);
    
Change the contents with:

    *pnt_content = 10;

Strings
-------

Concatenating a string can be down in a number of ways:

    snprintf();
    
or via strcpy() and strcat() although you'll need to malloc the space for the new string based on the lengths.


Macros
------

Macros provide a mechanism for pre-processing via the compiler.

    #define WHEREARG  __FILE__, __LINE__

Structs
-------

Structs are data structures for simple types:

    struct fish {
        const char *name;
        int teeth;
        struct preferences care;
    };

Construct this with:

    struct fish snappy = {"Snappy", 10};
    
You can also created an aliased name for a struct with a typedef

    typedef struct fish {
    } fishy;
    
Pass this around as follows, note though that this is copied *by value* so the changes made by the method won't be reflected.  This is in direct contrast to Java where objects are passed by reference.

    void label(struct fish f) {}
    
So, if you need to pass a struct to a function for update purposes you need to pass a pointer to it:

    void happybirthday(turtle *t) {}
    myrtle = {"myrtle", 20}
    happybirthday(&myrtle);

When updating the value in the struct you need to be careful:

    (*t).age

Parenthesis really matter here.

You can also use the following notation:

    t->age

For large complex structures you can use struct pointers.

Unions
------

Structs are OK for simple datatypes, but each element in a struct allocates new space for it.

A Union is more efficient, it allows you to reuse memory space and defines enough space for only the largest item in the union.

    union quantity {
        float count;
        float weight;
    }
    quantity q = {.count=1,weight=20};

One downside here of using a union is that you have to remember the order and type.  Enums can help you decide what to use where to some extent.

In terms of efficiency you can also store bitfields instead of long lists of shorts.  Use this to define an int that occupies one bit of space:

    unsigned int reverb:1;

Enums
-----

Useful for storing symbols:

    enum colors {RED< GREEN< BLUE}
    
Dynamic Storage and Data Structures
-----------------------------------

Instead of fixed length arrays you can use other data structures that expand dynamically, e.g. a linked list:

    struct island *next;

Create the structs and then link them usingby assigning next to the &address.

This means creating your own implementation or using the glib GList:

https://developer.gnome.org/glib/2.37/glib-Singly-Linked-Lists.html

We need to allocate memory on the heap for dynamic storage as opposed to the stack (local variables for functions).

Debug, Test
-----------

Benchmarking
------------

clock - will measure the CPU time consumed

Useful Libraries
----------------

* glib (don't confused with glibc) - cross platform utility library - http://en.wikipedia.org/wiki/GLib
* libcURL - file transfer
* libGSL - Scientific library routines
* libSQLite3 - SQLite library
* libXML2 - XML processing
* Apophenia - https://github.com/b-k/Apophenia
* GTK - http://www.gtk.org/

Windows - use  MinGW (./configure --host=ming32)
Linux - 

Autotools:
* configure
* make
* make install

Makefiles
---------

Shell grammar is based on the Bash/C shell syntax

    P=program_name
    OBJECTS=
    CFLAGS = -g -Wall -O3
    LDLIBS=
    CC=c99
    $(P): $(OBJECTS)
    
Define a variable (can also override on the command line):

    make CFLAGS="-g -Wall"
    
Built in variables:
* $@ - target filename
* $* - target file without extension

Example:

    all: html doc publish

    doc:
        pdflatex $(f).tex
    
    html:
        latex -interaction batchmode $(f)
        latex2html $(f).tex
    
    publish:
        scp $(f).pdf $(Blogserver)
        
Compiling an object from a c source file:

    $(CC) $(CFLAGS) $(LDFLAGS) -o $@ $*c.
    
Default Make rules:

    make -p > default_rules
    
Setting CFLAGS:

    CFLAGS=`pkg-config --cflags apophenia glib-2.0` -g -Wall -std=gnu11 -O3
    LDLIBS=`pkg-config --libs apophenia glib-2.0`
