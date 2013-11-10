# Wedit Makefile for project sender
SRCDIR=c:\msc_code\sender
CFLAGS=-Ic:\lcc\include  -g2 
CC=$(LCCROOT)\bin\lcc.exe
LINKER=$(LCCROOT)\bin\lcclnk.exe
OBJS=\
	sender.obj

LIBS=
EXE=sender.exe

$(EXE):	$(OBJS) Makefile
	$(LINKER)  -subsystem console -o $(SRCDIR)\lcc\sender.exe $(OBJS) $(LIBS)

# Build sender.c
SENDER_C=\
	c:\lcc\include\stdio.h\
	c:\lcc\include\_syslist.h\
	c:\lcc\include\stdlib.h\
	c:\lcc\include\stddef.h\
	c:\lcc\include\_syslist.h\
	c:\lcc\include\time.h\

sender.obj: $(SENDER_C) $(SRCDIR)\sender.c
	$(CC) -c $(CFLAGS) $(SRCDIR)\sender.c

link:
	$(LINKER)  -subsystem console -o $(SRCDIR)\lcc\sender.exe $(OBJS) $(LIBS)

clean:
	del $(OBJS) C:\msc_code\sender\lcc\sender.exe
