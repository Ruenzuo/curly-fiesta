## Trying to understand how linking works

* libfoo_static is a static library that depends on CoreFoundation.
* libaba_static is a static library that depends on CoreFoundation and libfoo_static.
* main should be an executable that statically links both libfoo_static and libfoo_static.

### Building libfoo_static

```bash
clang -c bar.c -o bar.o # create object file
libtool -static bar.o -o libfoo_static.a # create static Library

nm libfoo_static.a

libfoo_static.a(bar.o):
                 U _CFShow
                 U ___CFConstantStringClassReference
0000000000000000 T _fizz
```

### Building libaba_static

```bash
clang -c aba.c -o aba.o -I ../libfoo_static/ # create object file
libtool -static aba.o -o libaba_static.a # create static library

nm libaba_static.a

libaba_static.a(aba.o):
                 U _CFShow
                 U ___CFConstantStringClassReference
0000000000000000 T _aba
                 U _fizz
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

otool -L ./main/main
./main/main:
  /System/Library/Frameworks/CoreFoundation.framework/Versions/A/CoreFoundation (compatibility version 150.0.0, current version 1258.1.0)
  /usr/lib/libSystem.B.dylib (compatibility version 1.0.0, current version 1226.10.1)

nm ./main/main
                 U _CFShow
                 U ___CFConstantStringClassReference
0000000000001000 A __mh_execute_header
0000000000001f40 T _aba
0000000000001f70 T _fizz
0000000000001f10 T _main
                 U dyld_stub_binder
```
