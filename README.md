<div align="center">

## Webserver Logfile Analyzer


</div>

### Description

I wrote this quick bit of code after looking at the cost of a product from the leader in the analysis of logfiles. - I'm a website operator and I have my sites hosted, and as such, the little things, like bandwidth usage, become rather important. Free Online Service type methods for tracking website usage cant provide bandwidth reports because a website is more then just html files. This program will go through a logfile of a specified format and add up the total bandwidth from server to client.
 
### More Info
 
When run, the program looks at the first command line parameter. If a value is present, then it will try to open that value as the filename for the logfile to analyze. If no value is supplied, it assumes that the file to look for is 'wwwtraffic.log'

It assumes that the field in question, generally named "sc-bytes" is the 11th field in a hit line. If your logfile format differs, then you'll need to adjust the number of "strtok" calls.

The number of total actual hits (not pageviews) are provided, as well as total bandwidth, in either Bytes, Kilobytes, or Megabytes.

Values of gigabytes and terrabytes will be reported in megabytes. For example, 3 gigabytes, would be reported as 3000MB.


<span>             |<span>
---                |---
**Submitted On**   |
**By**             |[Lucas Moten](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByAuthor/lucas-moten.md)
**Level**          |Beginner
**User Rating**    |4.7 (14 globes from 3 users)
**Compatibility**  |C, C\+\+ \(general\), Microsoft Visual C\+\+, UNIX C\+\+
**Category**       |[Internet/ Browsers/ HTML](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByCategory/internet-browsers-html__3-9.md)
**World**          |[C / C\+\+](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByWorld/c-c.md)
**Archive File**   |[](https://github.com/Planet-Source-Code/lucas-moten-webserver-logfile-analyzer__3-1702/archive/master.zip)





### Source Code

```
#include <stdio.h>		// io access (filestream, screen)
#include <stdlib.h>		// atoi
#include <string.h>		// strtok
int main(int argc, char* argv[])
{
	printf("\n");
	printf("-------------------------------------------------------------------------------\n");
	printf("Bandwidth Calculator for WWW Logfiles v. 0.03a\n");
	printf("Written by Lucas Moten(krozy@theonlinegamer.com) 2001-04-30\n");
	printf("-------------------------------------------------------------------------------\n");
	//FILE FORMAT: (code utilizes the 11th field)
	//
	//#Fields: date time c-ip cs-username s-ip s-port cs-method cs-uri-stem cs-uri-query sc-status sc-bytes cs-bytes time-taken cs-version cs(User-Agent) cs(Cookie) cs(Referer)
	//2000-12-01 00:02:09 64.20.169.144 - 64.27.79.90 80 GET /favicon.ico - 200 3228 239 828 HTTP/1.1 Mozilla/4.0+(compatible;+MSIE+5.5;+Windows+98) ASPSESSIONIDGQGQQJCQ=OACMLIMAMCGBPMFAKFGMHAOO -
	int bytes = 0;
	int kbytes = 0;
	int mbytes = 0;
	int linebytes = 0;
	char line[4096];
	int toknum = 0;
	char * token;
	int hits = 0;
	// Open the file supplied in the first command line argument
	// If no filename is supplied, try a default of 'wwwtraffic.log'
	FILE* logfile;
	if(argv[1] != NULL) {
		logfile = fopen(argv[1], "r");
	} else {
		printf("Name of logfile was not supplied, trying 'wwwtraffic.log'\n");
		logfile = fopen("wwwtraffic.log", "r");
	}
	if(logfile == NULL) {
		printf("The file wasn't found.\n");
		return 0;
	}
	// If we have an error opening the file, then bail out.
	if(!(ferror(logfile))) {
		// Continue reading until we reach the end of the file
		while( fgets(line, 4096, logfile) != NULL )
		{
			// Ignore Comments
			if(line[0] != '#')
			{
				// Not a comment, increase our hit count. Report the number
				// of hits as "eye-candy", for every 50,000 hits so that the
				// user doesnt think the program isn't working
				hits ++;
				if ((hits%50000)==0) printf("Total Hits so far: %i\n", hits); //status
				// Reset information for this line
				toknum = 0;
				linebytes = 0;
				// Prep the String Tokenizer. Note that this would return
				// the first 'token'. A token is deliminated by the 2nd parameter
				strtok(line, " ");
				// bleed off 9 more 'tokens'.
				for(toknum = 0; toknum < 9; toknum ++) strtok(NULL, " ");
				// We've reached the 11th token, this is the one we want, so capture it
				token = strtok(NULL, " ");
				// Make sure the token is valid, and if so, set the amount of bytes
				if (token != NULL) linebytes = atoi(token);
				// Add the bytes and perform conversions
				bytes = bytes + linebytes;
				if(bytes > 1024) {
					kbytes ++;
					bytes -= 1024;
					if(kbytes > 1024) {
						mbytes ++;
						kbytes -= 1024;
					}
				}
			}
		}
		// Done with the file, close it out
		fclose(logfile);
	} else {
		// Report the fact that there was an error
		printf("error opening file!\n");
	}
	// Show bandwidth + total hit results. Not that in terms of bandwidth,
	// we're showing whatever the largest type was. - easily convertible to
	// include gigabytes, terrabytes, etc. as this becomes necessary in the
	// future.
	if(mbytes)
	{
		printf("Bandwidth in Megabytes: %i\n", mbytes);
	} else {
		if(kbytes)
		{
			printf("Bandwidth in Kilobytes: %i\n", kbytes);
		} else {
			printf("Bandwidth in Bytes: %i\n", bytes);
		}
	}
	printf("Total Hits: %i\n", hits);
	return 0;
}
```

