$ npm init --yes
$ npm install honkit --save-dev
$ npx honkit init
$ npx honkit serve
The Zlib data compression library is built into Java, and allows you to compress and decompress data. So, uh… ’nuff said?

1. If you have not already done so, download and install the Java Development Kit. Details are given in a previous tutorial. Make a note of the directory to which the files “javac.exe” and “java.exe” are installed.

2. In any convenient location, create a new directory named “CompressionTest”.

3. In the newly created CompressionTest directory, create a new text file named “CompressionTest.java”, containing the following text.

import java.io.*;
import java.util.*;
import java.util.zip.*;

public class CompressionTest
{
	public static void main(String[] args)
	{
		Compressor compressor = new Compressor();

		String stringToCompress = "This is a test!";
		//String stringToCompress = "When in the course of human events, it becomes necessary for one people to dissolve the bands that bind them...";

		System.out.println("stringToCompress is: '" + stringToCompress + "'");

		byte[] bytesCompressed = compressor.compress(stringToCompress);

		System.out.println("bytesCompressed is: ");
		Console.writeBytesAsHexadecimal(bytesCompressed);

		String stringDecompressed = compressor.decompressToString(bytesCompressed);

		System.out.println("stringDecompressed is: '" + stringDecompressed + "'");
	}
}

class Compressor
{
	public Compressor()
	{}

	public byte[] compress(byte[] bytesToCompress)
	{		
		Deflater deflater = new Deflater();
		deflater.setInput(bytesToCompress);
		deflater.finish();

		byte[] bytesCompressed = new byte[Short.MAX_VALUE];

		int numberOfBytesAfterCompression = deflater.deflate(bytesCompressed);

		byte[] returnValues = new byte[numberOfBytesAfterCompression];

		System.arraycopy
		(
			bytesCompressed,
			0,
			returnValues,
			0,
			numberOfBytesAfterCompression
		);

		return returnValues;
	}

	public byte[] compress(String stringToCompress)
	{		
		byte[] returnValues = null;

		try
		{

			returnValues = this.compress
			(
				stringToCompress.getBytes("UTF-8")
			);
		}
		catch (UnsupportedEncodingException uee)
		{
			uee.printStackTrace();
		}

		return returnValues;
	}

	public byte[] decompress(byte[] bytesToDecompress)
	{
		byte[] returnValues = null;

		Inflater inflater = new Inflater();

		int numberOfBytesToDecompress = bytesToDecompress.length;

		inflater.setInput
		(
			bytesToDecompress,
			0,
			numberOfBytesToDecompress
		);

		int bufferSizeInBytes = numberOfBytesToDecompress;

		int numberOfBytesDecompressedSoFar = 0;
		List<Byte> bytesDecompressedSoFar = new ArrayList<Byte>();

		try
		{
			while (inflater.needsInput() == false)
			{
				byte[] bytesDecompressedBuffer = new byte[bufferSizeInBytes];

				int numberOfBytesDecompressedThisTime = inflater.inflate
				(
					bytesDecompressedBuffer
				);

				numberOfBytesDecompressedSoFar += numberOfBytesDecompressedThisTime;

				for (int b = 0; b < numberOfBytesDecompressedThisTime; b++)
				{
					bytesDecompressedSoFar.add(bytesDecompressedBuffer[b]);
				}
			}

			returnValues = new byte[bytesDecompressedSoFar.size()];
			for (int b = 0; b < returnValues.length; b++) 
			{
				returnValues[b] = (byte)(bytesDecompressedSoFar.get(b));
			}

		}
		catch (DataFormatException dfe)
		{
			dfe.printStackTrace();
		}

		inflater.end();

		return returnValues;
	}

	public String decompressToString(byte[] bytesToDecompress)
	{	
		byte[] bytesDecompressed = this.decompress
		(
			bytesToDecompress
		);

		String returnValue = null;

		try
		{
			returnValue = new String
			(
				bytesDecompressed,
				0,
				bytesDecompressed.length,
				"UTF-8"
			);	
		}
		catch (UnsupportedEncodingException uee)
		{
			uee.printStackTrace();
		}

		return returnValue;
	}
}

class Console
{
	public static void writeBytesAsHexadecimal(byte[] bytesToWrite)
	{
		int numberOfBytes = bytesToWrite.length;
		int bytesPerLine = 16;
		int bytesPerColumn = 8;	
		int numberOfColumns = bytesPerLine / bytesPerColumn;
		int linesPerBlock = 16;

		int numberOfLines = (int)Math.ceil
		(
			(double)numberOfBytes / (double)bytesPerLine
		);

		int widthOfDataOnDisplayInChars =
			numberOfColumns
			* (bytesPerColumn * 3 + 1)
			- 1;

		String horizontalRule = "";

		for (int i = 0; i < widthOfDataOnDisplayInChars; i++)
		{
			horizontalRule += "=";
		}

		System.out.println(horizontalRule);

		for (int y = 0; y < numberOfLines; y++)
		{
			if (y > 0 && y % linesPerBlock == 0)
			{
				System.out.println("");
			}

			int bytesOnThisLine;

			if (y < numberOfLines - 1)
			{
				bytesOnThisLine = bytesPerLine;
			}
			else
			{
				bytesOnThisLine = numberOfBytes % bytesPerLine;
			}			

			for (int x = 0; x < bytesOnThisLine; x++)
			{
				if (x > 0 && x % bytesPerColumn == 0)
				{
					System.out.print("  ");
				}

				int byteIndex = y * bytesPerLine + x;
				byte byteCurrent = bytesToWrite[byteIndex];

				String byteAsString = Integer.toHexString(byteCurrent & 0xFF);
				if (byteAsString.length() < 2)
				{
					byteAsString = "0" + byteAsString;
				}

				byteAsString += " ";

				System.out.print(byteAsString);
			}

			System.out.println();
		}

		System.out.println(horizontalRule);
	}
}
Advertisements

REPORT THIS AD

4. Still in the CompressionTest directory, create a new text file named “ProgramBuildAndRun.bat”, containing the following text. Substitute the path of the directory containing javac.exe in the indicated place.

@echo off

set javaPath="[path of the directory containing javac.exe]"

%javaPath%\javac.exe *.java
%javaPath%\java.exe CompressionTest

pause
5. Execute the newly created ProgramBuildAndRun.bat. The program will be compiled and executed, and, if all goes well, the text shown below will be displayed in a console window.

stringToCompress is: 'This is a test!'
bytesCompressed is:
=================================================
78 9c 0b c9 c8 2c 56 00   a2 44 85 92 d4 e2 12 45
00 29 8a 05 17
=================================================
stringDecompressed is: 'This is a test!'
Notes

This method of compression uses the “deflate” algorithm, which is 100% patent-free. This choice is a response to the fact that the more popular LZW (Lempel-Ziv-Welch) algorithm was at the heart of a big ruckus a few years back. Back at the turn of the millenium, Unisys, which apparently held the patent on LZW, tried to start collecting royalties on any program that implemented it. I’d almost say that that was so long ago that the patent has expired by now, but patents don’t actually seem to expire anymore. Despite the fact that that’s pretty much the whole point of patents to begin with. I blame Mickey Mouse.
The decompress() method formerly contained a  “compressionFactorMaxLikely” variable, which was causing errors if the compression factor was higher than whatever I guessed it might be (specifically, 3).  I have changed the method to avoid this problem.
Speaking of compression ratios, I just noticed that the “compressed” version of the data actually takes up more bytes than the uncompressed version. Presumably this is because the data being compressed is so small. No doubt efficiency would improve with a larger sample. I mean, I have no doubt. You still might, I guess.
Advertisements

REPORT THIS AD
Share this:
Reddit

Related
Playing an MP3 from Java Using JLayer
2011/06/14
In "Java"

Sending an Email from Java Using JavaMail and Apache James
2011/09/04
In "email"

Reading and Writing a BMP File Using Java
2011/08/16
In "bmp"

2011/08/266 Replies
« Previous
Next »
Leave a Reply
Your email address will not be published. Required fields are marked *

Comment * 

Name *

Email *

Website

 Notify me of new comments via email.

 Notify me of new posts via email.

Muhammad Umair on 2012/12/18 at 12:54
Reblogged this on Tech Muffins & Musings Smoothie and commented:
Compressing and Uncompressing Data in Android/Java Using Zlib

Reply
About Java on 2012/12/29 at 13:12
good article.. thanks for sharing.. 🙂

Reply
davidc87 on 2013/02/05 at 14:15
Good article over the internet for the data compression..:)

Reply
doeke on 2013/06/11 at 19:13
Hi, if i test your code with:
String stringToCompress = “This is aaaaaaaaaaaaaaaaaa aaaaaaaaaaaaaaaa aaaaaaaaaaaaaaaaa aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa aaaaaaaaaaaaaaaaaa aaaaaaaaaaaaaaaaaaaaaaaaaaa aaaaaaaaa test!”;

Then this part is missing with decrompressing:
“aaaaaaaaaa aaaaaaaaaaaaaaaaaaaaaaaaaaa aaaaaaaaa test!”

so seems like there is a nasty bug.

Reply
thiscouldbebetter on 2013/06/11 at 19:48
Agreed. The problem was that your test string compresses much too well, since I was assuming that the maximum compression ratio was going to be about three to one. I have changed the decompress() method to get around that problem, though I’m pretty sure I did it in the most inefficient way possible (by using a List<Byte>). But it seems to work now. I’ll leave the efficiency tweaks as an exercise for the reader.

Reply
Advertisements

REPORT THIS AD

Nerpesto Cortez on 2022/11/17 at 02:02
In the decompress method you can use ByteArrayOutputStream instead of List like this:

public static byte[] decompress(byte[] compressedData)
{
Inflater inflater = new Inflater();
inflater.setInput(compressedData);
byte[] data = null;
try (ByteArrayOutputStream baos = new ByteArrayOutputStream())
{
while (!inflater.needsInput())
{
byte[] buffer = new byte[compressedData.length];
int count = inflater.inflate(buffer);
baos.write(buffer, 0, count);
}
data = baos.toByteArray();
}
catch (DataFormatException dfe)
{
dfe.printStackTrace();
}
return data;
}

When you compress you are using a buffer with to keep the compressed data with size Short.MAX_VALUE. What happens if the compressed data exceeds the buffer size?

Reply
