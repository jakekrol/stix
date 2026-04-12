BIN=../bin
OBJ=../obj
GIGGLE_ROOT=../../giggle
HTS_ROOT=$(GIGGLE_ROOT)/lib/htslib
HTS_INC=$(HTS_ROOT)
HTS_LIB=$(HTS_ROOT)
HTS_LINK=$(HTS_LIB)/libhts.a
HTS_RPATH=

ifeq ($(wildcard $(HTS_ROOT)/libhts.a),)
ifdef CONDA_PREFIX
HTS_INC=$(CONDA_PREFIX)/include
HTS_LIB=$(CONDA_PREFIX)/lib
HTS_LINK=-L$(HTS_LIB) -lhts
HTS_RPATH=-Wl,-rpath,$(HTS_LIB)
endif
endif
CFLAGS=-O2 -D_FILE_OFFSET_BITS=64 -Werror -Wuninitialized -Wno-error=unused-result
#CFLAGS=-g -D_FILE_OFFSET_BITS=64 -Werror -Wuninitialized
INCLUDES=-I$(HTS_INC) -I$(GIGGLE_ROOT) -I$(SQLITE_ROOT)
LIBS=-ldl -lz -lm -pthread -lcurl
LIBS+=-lcrypto
SQLITE_ROOT=../../sqlite-amalgamation-3170000
CC=gcc -fcommon

LIB=ped.o \
    search.o\
	sharding_utils.o

.SUFFIXES:

OBJS=$(addprefix $(OBJ)/, $(LIB))

.SECONDARY: $(OBJS)


PROG=stix

LIST=$(SQLITE_ROOT)/sqlite3.o $(addprefix $(BIN)/, $(PROG))

all: check-env $(LIST)

$(SQLITE_ROOT)/sqlite3.o: $(SQLITE_ROOT)/sqlite3.c
	$(CC) -c $(SQLITE_ROOT)/sqlite3.c -o $(SQLITE_ROOT)/sqlite3.o

$(OBJ)/%.o: %.c 
	$(CC) -c $(CFLAGS) -o $@ $< \
		$(INCLUDES) 

$(BIN)/%: %.c $(OBJS)
	$(CC) $(CFLAGS) -o $@ $^ \
		$(INCLUDES) \
		-DSAMTOOLS=1 \
		$(GIGGLE_ROOT)/lib/libgiggle.a \
		$(HTS_LINK) \
		$(SQLITE_ROOT)/sqlite3.o \
		$(LIBS) \
		$(HTS_RPATH)

check-env:
ifdef TRAVIS_BUILD_DIR
HTS_ROOT=$(TRAVIS_BUILD_DIR)/htslib
GIGGLE_ROOT=$(TRAVIS_BUILD_DIR)/giggle
endif
