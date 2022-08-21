# 目前所用的单个文件编译Makefile
```bash
.PHONY: main, run, clean

CC = g++ -std=c++11 

CFLAGS += -ggdb -Wall -Wextra -Werror
INCLUDE_PATH = include
LIBRARY_PATH = library

SRC = src
targets = $(SRC)/main.cc 
output = output

main: $(targets)
	$(CC) $(targets) $(CFLAGS) -I $(INCLUDE_PATH) -o $(SRC)/$(output)

run:
	$(SRC)/$(output)

clean:
	@ rm -rf $(SRC)/$(output)
```