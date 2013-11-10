/*
Section 1:  Load libraries and initialise variables
*/
#include <windows.h>
#include <stdio.h>
#include <stdlib.h>
#include <math.h>
#include <time.h>
#include "fce.h"

//Define the data types for a bit and a byte
typedef int bit;
typedef bit byte[8];

//Function declerations
void DieWithError(int);
int getBeaconSize(char*);
int bin_to_int(int*);
char bin_to_char(int*);

int main(int argc,char *argv[])
{
	int code;  //Return code for FCE functions
	int beacon_size;  //Size of the beacon file

	/* Strings containing the beacon file name, the
    output file name and the ftp site address. */
	char *beacon_file, *output_file, *ftp_site;

	FILE *fptr;  //File pointer for the output file
	int period;  //Time period per bit
	int file_size;  //Number of characters in the file which is to be received
	bit *bit_stream;  //Binary data which is received

	//Time containers used for timing operations
	long start_time, start_time_2, current_time;
	double elapsed_time;

	int ftp_lag;  //Lag between client and server
	int i,j;  //Loop conters

	/*
	Section 2:
	Check that the correct number of arguments have
	been passed to the application, if not then issue
	usage instructions to the user.  If the number of
	arguments is correct then initialise the relevant
    variables.
	*/
	if((argc<4)||(argc>5))
	{
		printf("Usage: %s <ftp> <beacon> <output> [<period>]",argv[0]);
		exit(1);
	}
	else
	{
		ftp_site=argv[1];
		beacon_file=argv[2];
		output_file=argv[3];
		if(argc==5)
			period=atoi(argv[4]);
		else
			period=3000;
	}

	/*
	Section 3:
	Start up FCE and loads the relevant dll's
	*/
	code = fceAttach(1,0);
	if(code<0)
		DieWithError(code);

	/*
	Section 4:
	Connect to ftp server using the 'anonymous' user name
	and the 'guest' password.
	*/
	code = fceConnect(0,(LPSTR)ftp_site,(LPSTR)"anonymous",(LPSTR)"guest");
	if(code<0)
		DieWithError(code);

	/*
	Section 5:
	Get the size of beacon file and calculate average FTP lag.
	*/
	beacon_size = getBeaconSize(beacon_file);
	printf("\nTesting ftp lag");
	ftp_lag=0;
	for (i=0; i<20; ++i)
	{
		start_time=clock();
		getBeaconSize(beacon_file);
		current_time=clock();
		ftp_lag+=difftime(current_time,start_time);
		printf(".");

	}
	ftp_lag/=20;
	printf("\nAverage FTP lag: %d ms\n",ftp_lag);

	/*
	Section 6:
	Wait for starting signal from beacon.  The starting
	signal is an increase in the beacon size for longer than
	9 seconds.  After 9 seconds of the beacon size increase
	the transmission begins after a further 1 second pause.
	*/
	start_time=0;
	current_time=0;
	elapsed_time=0;
	printf("\nReady to recieve\n");

	while (elapsed_time<9.0)
	{
		if(getBeaconSize(beacon_file)>beacon_size)
		{
			if(start_time==0) {
				start_time_2=clock();
				start_time=time(NULL);
			}
			else
			{
				current_time=time(NULL);
				elapsed_time=difftime(current_time,start_time);
			}
		}
		else
		{
			start_time=0;
			elapsed_time=0;
		}
	}
	while(((int)difftime(clock(),start_time_2))<10000) {}

	printf("\nStarting...\n");


	/*
	Section 7:
	Start receiving the transmission size data,
	by checking for modultions in the beacon file's size.
	If the current size of the beacon, found using the
	getBeaconSize() function, is found to be greater than the
	original size, then this represents a 1.  If the alternate
	is the case and the beacon file is found not to have
	increased in size then this represents a 0.  The resultant
	data which corresponds to the size of the file being trnasmited
	is stored in the size_a and size_b variables.
	*/
	byte size_a, size_b;
	for(i=15; i>=0; --i)
	{
		start_time=clock();
		if(getBeaconSize(beacon_file)>beacon_size)
		{
			printf("1");
			if(i>7)
				size_a[i-8]=1;
			else
				size_b[i]=1;
		}
		else
		{
			printf("0");
			if(i>7)
				size_a[i-8]=0;
			else
				size_b[i]=0;
		}
		while(((int)difftime(clock(),start_time))<period) {}
	}

	/*
	Section 8:
	The program pauses for 2 seconds and also allocates
	storage space in the bit_stream array for the file
	which is to be transmitted.
	*/
	start_time=clock();
	file_size = bin_to_int(size_a) + (bin_to_int(size_b)*255);
	printf("\nFile size: %d bytes\n\n",file_size);
	bit_stream=calloc(file_size*8,sizeof(bit));
	printf("\nRecieving data...\n");
	while(((int)difftime(clock(),start_time))<2000) {}

	/*
	Section 9:
	The source file is received by reading changes in the beacon
	in the same manner as in section 6.  However on this occasion
	the data is stored in the bit_stream array.
	*/
	for(i=0; i<(file_size*8); ++i)
	{
		start_time=clock();
		if(getBeaconSize(beacon_file)>beacon_size)
		{
			printf("1");
			bit_stream[i]=1;
		}
		else
		{
			printf("0");
			bit_stream[i]=0;
		}
		while(((int)difftime(clock(),start_time))<period) {}
	}

	/*
	Section 10:
	This section converts each byte in the bit stream
	to a integer using the bin_to_int() function.
	It then converts the integer to a character and
	stores that character in the output file, the
	name of which was designated by the user.
	*/
	fptr=fopen(output_file,"wb");
	byte temp_byte;
	for(i=0; i<file_size; ++i)
	{
		for(j=7; j>=0; --j)
		{
			temp_byte[j]=bit_stream[(i*8)+(7-j)];
		}
		fprintf(fptr,"%c",bin_to_int(temp_byte));
	}
	fclose(fptr);

	fceClose(0);
	return 0;
}

/*
Function 1:
This function queries the size of the beacon file on the ftp
server.  It then returns this value as an integer to the calling
procedure.
*/
int getBeaconSize(char *beacon)
{
	char *buffer;
	char *size_string;
	int code;
	int i;

	buffer = malloc(200);
	size_string = calloc(2, sizeof(char));

	memset(buffer, 0, 200);
	strcpy(buffer, beacon);
	code = fceGetList(0, FCE_FULL_LIST_FILE, (LPSTR)buffer,200);
	if(code<0)
		DieWithError(code);

	buffer+=36;
	strncat(size_string, buffer, 2);

	return atoi(size_string);
}

/*
Function 2:
This function converts a binary byte into
an integer.  It then returns that integer to the
calling function.
*/
int bin_to_int(byte bin)
{
	int i;
	int total=0;
	for (i=7; i>=0; --i)
	{
		total+=(pow(2,i)*bin[i]);
	}
	return total;
}

/*
Function 3:
This function handles errors thrown be the
different functions of the FCE package.
*/
void DieWithError(int code)
{
	char *buff;
	buff = malloc(300);
	fceErrorText(0,code,(LPSTR)buff,300);
	printf(buff);
	exit(1);
}


