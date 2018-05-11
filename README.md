# 353 Final Project
Name: Eric Fratzke

This program was developed with Python3 and uses Pillow.
The program hides your secret message into yourFileName.jpg file that you provide into a yourFileName_Encoded.png file . 
It can also decode the yourFileName.png in order to reveal the message hidden in the picture. 

Files Included:
	secretmessage.txt	=> Write your hidden message in this file
	README.txt		    => Description and program usage
	testImage.png	    => test picture to test decode
	testImageHideHere.jpg	=> Image that I used to encode my message (source code)
	testImageHideHere_Encoded.png	=> Image that hides the encoded message
  textInImage.py => The program source code

Usage:
	(For reference):
		-d 		decode
		-e		encode

	Open terminal navigate to this folder '353' or wherever you may place it.
	Use the command python textInImage.py followed by -e or -d followed by imageFile you are using.
 
 # Example commands:
	 python textInImage.py -e testImageHideHere.jpg
	 python textInImage.py -d testImageHideHere_Encoded.png
   
	decode:
		Enter -d followed by the name of the png file.
		The message will be decoded from the picture and output will be in terminal.
	encode:
		You secretmessage must be saved in file secretmessage.txt .
		The message will be encoded into the picture and the picture will now be saved with original file name + _Encoded and saved as a .png.


