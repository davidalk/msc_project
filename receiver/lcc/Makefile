# Wedit Makefile for project reciever
SRCDIR=c:\msc_code\receiver
CFLAGS=-Ic:\lcc\include -IC:\FCE4C\apps  -g2 
CC=$(LCCROOT)\bin\lcc.exe
LINKER=$(LCCROOT)\bin\lcclnk.exe
OBJS=\
	receiver.obj \

LIBS=c:\fce4c\apps\fce32lcc.lib
EXE=receiver.exe

$(EXE):	$(OBJS) Makefile
	$(LINKER)  -subsystem console -o $(SRCDIR)\lcc\receiver.exe $(OBJS) $(LIBS)

# Build receiver.c
RECEIVER_C=\
	c:\lcc\include\windows.h\
	c:\lcc\include\stdlib.h\
	c:\lcc\include\stddef.h\
	c:\lcc\include\_syslist.h\
	c:\lcc\include\string.h\
	c:\lcc\include\win.h\
	c:\lcc\include\limits.h\
	c:\lcc\include\stdarg.h\
	c:\lcc\include\ctype.h\
	c:\lcc\include\basetsd.h\
	c:\lcc\include\stdio.h\
	c:\lcc\include\_syslist.h\
	c:\lcc\include\stdlib.h\
	c:\lcc\include\math.h\
	c:\lcc\include\time.h\
	C:\FCE4C\apps\fce.h\

receiver.obj: $(RECEIVER_C) $(SRCDIR)\receiver.c
	$(CC) -c $(CFLAGS) $(SRCDIR)\receiver.c

link:
	$(LINKER)  -subsystem console -o $(SRCDIR)\lcc\receiver.exe $(OBJS) $(LIBS)

clean:
	del $(OBJS) C:\msc_code\receiver\lcc\receiver.exe
