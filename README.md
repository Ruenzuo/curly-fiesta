## Trying to understand how linking works

libfoo_static is a static library that depends on CoreFoundation.
libaba_static is a static library that depends on CoreFoundation and libfoo_static.
main should be an executable that statically links both libfoo_static and libfoo_static.

### Building libfoo_static

```bash
clang -c bar.c -o bar.o # create object file
libtool -static bar.o -o libfoo_static.a # create static library
```

### Building libaba_static

```bash
clang -c aba.c -o aba.o -I ../libfoo_static/ # create object file
libtool -static aba.o -o libaba_static.a # create static library
```

### Building main

```bash
clang -c main.c -o main.o -I ../libfoo_static/ -I ../libaba_static/ # create object file
ld main.o -framework CoreFoundation -lSystem -L ../libaba_static/  -laba_static -L ../libfoo_static -lfoo_static -o main -macosx_version_min 10.11 # create binary
```

### Result

```bash
./main 
buzz
buzz
bab
```
