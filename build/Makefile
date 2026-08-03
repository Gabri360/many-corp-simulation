CFLAGS=-O3 -ffast-math -funroll-loops -flto -pedantic -std=c99 -Wall -Wextra -Werror -Wconversion
CFLAGS += -I../vendor/glfw/include

CC = $(CROSS)gcc

ifeq ($(OS),Windows_NT)
	TARGET = sim.exe
	LDFLAGS = -L../vendor/glfw/build/src -lglfw3 -lopengl32 -lgdi32 -luser32 -lshell32 -lm
else
	TARGET = sim
	LDFLAGS = -L../vendor/glfw/build/src -lglfw3 -lGL -lm
endif

CC = $(CROSS)gcc

all: $(TARGET)

$(TARGET): sim.o simlib.o simlib_base.o simlib_metodi.o
	$(CC) $(CFLAGS) sim.o simlib.o simlib_base.o simlib_metodi.o -o $(TARGET) $(LDFLAGS)

sim.o: ../prg/sim.c ../head/simlib.h ../head/simlib_struct.h
	$(CC) $(CFLAGS) -c ../prg/sim.c

simlib.o: ../lib/simlib.c ../head/simlib.h ../head/simlib_base.h ../head/simlib_struct.h
	$(CC) $(CFLAGS) -c ../lib/simlib.c

simlib_base.o: ../lib/simlib_base.c ../head/simlib_base.h ../head/simlib_struct.h
	$(CC) $(CFLAGS) -c ../lib/simlib_base.c

simlib_metodi.o: ../lib/simlib_metodi.c ../head/simlib_metodi.h ../head/simlib.h ../head/simlib_struct.h
	$(CC) $(CFLAGS) -c ../lib/simlib_metodi.c

c:
	rm -f *.o $(TARGET)
