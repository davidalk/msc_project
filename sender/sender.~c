/*
Section 1:
Load libraries and declare variables and functions
*/
#include <stdio.h>
#include <stdlib.h>
#include <time.h>

//Define the data types for a bit and a byte
typedef int bit;
typedef bit byte[8];

char *beacon_file;  //Name of the beacon file
char *beacon_holder;  //Data stream which holds the contents of the beacon file
int bfile_size;

//Declare functions
int* char_to_bin(char);
int* int_to_bin(int);
void increase_beacon(void);
void decrease_beacon(void);

int main(int argc, char *argv[])
{
	FILE *fptr;  //Source fle pointer
	FILE *bfptr;  //Beacon file pointer
	int i, j;  //Counters for looping
	char *data;  //Storage for source file which is to be sent
	int file_size;  //Source file size
	char *file_name;  //Source file name
	int *binary;  //Holder for binary conversion operations
	int bin_file_size[16];  //File size in binary
	bit *bit_stream;  //Data to be sent in binary format
	int period;  //Time between each bit
	long start_time;  //Time variable for timming operations

	/*
	Section 2:
	Check that the correct number of arguments
	have been sent on execution, if not
	then send usage instructions to the user.  If
	the number of arguments is correct then initialise the
	relevant variables.
	*/
	if ((argc < 3) || (argc > 4))
	{
        fprintf(stderr, "Usage: %s <source> <beacon> [<period>]\n", argv[0]);
        exit(1);
    }
	beacon_file = argv[2];
	file_name = argv[1];

	//Set up time period to that entered by user otherwise
	if (argc==4)
		period = atoi(argv[3]);
	else
		period = 3000;

	/*
	Section 3:
	Open file to be sent, allocate space for storage and
	read the file into the data variable
	*/
	fptr = fopen(file_name,"rb");
	fseek(fptr, 0, SEEK_END);
	file_size = ftell(fptr);
	data = calloc(file_size, sizeof(char));
	fseek(fptr, 0, SEEK_SET);
	fread(data, file_size, 1, fptr);
	fclose(fptr);

	/*
	Section 4:
	Create the bit stream which will hold the
	whole of the beacon in binary format
	*/
	bit_stream = calloc((file_size * 8), sizeof(int));

	/*
	Section 5:
	The first two bytes of the bit srteam tell the reciever the
	size of the file it is going to recieve.  The size is
	calculated as follows:
	File size = size_a + (size_b * 255)
	Where size_a is the first 8 bits of the
	stream and size_b is the next 8.
	*/
	int size_a, size_b;
	size_b = file_size/255;
	size_a = file_size%255;

	/*
	Section 6:
	The size_a and size_b variables are converted into binary
	and placed inside the bin_file_size variable
	*/
	binary = int_to_bin(size_a);
	for (i=7; i>=0; --i)
		bin_file_size[7-i]=binary[i];

	binary = int_to_bin(size_b);
	for (i=7; i>=0; --i)
		bin_file_size[(7-i)+8]=binary[i];

	/*
	Section 7:
	The whole of the source file is now converted
	into binary and placed inside the bit_stream
	*/
	for (i=0; i<file_size; ++i) {
		binary = char_to_bin(data[i]);
		int start_pos = (8 * i);
		for (j=7; j>=0; --j)
			bit_stream[start_pos+(7-j)] = binary[j];
	}

	/*
	Section 8:
	Load unedited beacon file into beacon_holder
	*/
	bfptr = fopen(beacon_file,"rb");
	fseek(bfptr, 0, SEEK_END);
	bfile_size = ftell(bfptr);
	beacon_holder = calloc(bfile_size, sizeof(char));
	fseek(bfptr, 0, SEEK_SET);
	fread(beacon_holder, bfile_size, 1, bfptr);
	fclose(bfptr);

	/*
	Section 9:
	This section gives the starting signal
	to the recieving program.  The convention
	for the signal is that if the beacon file has
	increased in size for longer than 9 seconds,
	then the transmision will begin in 1 second
	from that time.
	*/
	start_time=clock();  //Record time at the start of the operation
	increase_beacon();
	sleep(9500);
	decrease_beacon();
	while(((int)difftime(clock(),start_time))<10000) {}  //Enter a pausing while loop

	/*
	Section 10:
	This operation sends the file size in the form of
	two bytes stored in bin_file_size (see sections 4 & 5 above).
	The code modulates the size of the beacon file in order to
	transmit the contents of bin_file_size.  An increase in the
	size of the beacon file apertains to a 1 and a decrease
	a 0.  Note that the conditional if statements will only change
	the size of the beacon file if the current bit in
	bin_file_size is different from the previous one.
	*/
	for (i=0; i<16; ++i) {
		start_time=clock();
		printf("%d",bin_file_size[i]);
		if (i==0) {
			if (bin_file_size[0] == 1)
				increase_beacon();
		}
		else {
			if (bin_file_size[i]==1 && bin_file_size[i-1]==0)
				increase_beacon();
			if (bin_file_size[i]==0 && bin_file_size[i-1]==1)
				decrease_beacon();
		}
		while(((int)difftime(clock(),start_time))<period) {}
	}

	/*
	Section 11:
	A 2 second pause is now required between the
	sending of the source file size and the source
	file data.
	*/
	start_time=clock();
	printf("\n");
	while(((int)difftime(clock(),start_time))<2000) {}

	/*
	Section 12:
	This section sends the data in bit_stream, which
	is the actual contents of the source file.  It
	works in the same way as in section 9 above.
	*/
	for (i=0; i<(file_size * 8); ++i) {
		start_time=clock();
		printf("%d",bit_stream[i]);
		if (i==0) {
			if (bit_stream[0] == 1)
				increase_beacon();
		}
		else {
			if (bit_stream[i]==1 && bit_stream[i-1]==0)
				increase_beacon();
			if (bit_stream[i]==0 && bit_stream[i-1]==1)
				decrease_beacon();
		}
		while(((int)difftime(clock(),start_time))<period) {}

	}

	//Return beacon to normal
	decrease_beacon();

	return 0;
}

/*
Function 1:
This function converts a char data type
into a byte.  It handles the byte which
is to be returned as a pointer to the data type
int.
*/
int* char_to_bin(char in)
{
	int char_val;

	char_val = (int) in;

	return int_to_bin(char_val);
}

/*
Function 2:
This function converts an int data type
into a byte.  It handles the byte which
is to be returned as a pointer to the data type
int.
*/
int* int_to_bin(int in)
{
	int i;
	static byte temp;

	for (i=0; i<8; i++)
		temp[i] = 0;

	i=0;
	while(in>0) {
		if (in%2 == 1)
			temp[i] = 1;
		in /= 2;
		++i;
	}
	return temp;
}

/*
Function 3:
This function increases the size
of the beacon file.  This is done by
utilising the append facillity.  A set of
1's is then appended to the end of the file.
*/
void increase_beacon(void)
{
	FILE *bfptr;

	bfptr = fopen(beacon_file,"ab");
	fprintf(bfptr,"1111111111");
	fclose(bfptr);
}

/*
Function 4:
This function decreases the size of the
beacon file.  It does this by overwriting the
current beacon file with the original that
is stored inside beacon_holder.
*/
void decrease_beacon(void)
{
	FILE *bfptr;

	bfptr = fopen(beacon_file,"wb");
	fseek(bfptr, 0, SEEK_SET);
	fwrite(beacon_holder, bfile_size, 1, bfptr);
	fclose(bfptr);
}
