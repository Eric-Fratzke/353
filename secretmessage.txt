# Author: Eric Fratzke
# Course: CPSC 353
# Due date: 5/10/2018 7:00pm
# This program hides and reveals a hidden message in a picture.
# Hide your secret message in secretmessage.txt and it will be hidden into a picture.
# Hide your message in a yourfilename.jpg you provide and save its to yourfilename_Encoded.png
# py -3 textInImage.py -e testImageHideHere.jpg
# py -3 textInImage.py -d testImageHideHere_Encoded.png
from PIL import Image
import sys, os, argparse, math

# Function used to decode message
def decode(fileName):
    
	# Open the file
    picture = Image.open(fileName)
    
    # Rotate the image so that the bottom right is now on the top left
    picture = picture.rotate(180)
	
	#Returns the contents of this image as a sequence object containing pixel values
	#To convert it to an ordinary sequence use list(x.getdata()). Pillow Documentation
    imagePixels = list(picture.getdata())
    
    # Gets the length of the message
    messageLength = []	
    for counter in range (0, 11):
        r,g,b = imagePixels[counter]
        messageLength.append(r & 1)
        messageLength.append(g & 1)
        messageLength.append(b & 1)
   
    # Convert the 32 bit binary string to an integer
    textLength = int("".join(map(str, messageLength[0:32])), 2)
    print("Length of message is:", textLength, "bits")
 
    decodedMessage = []	
    #Range is narrowed down to only grab the pixels we are changing
    for counter in range (11, int(math.ceil((float(textLength)/3)))+12):
        r,g,b = imagePixels[counter]
        decodedMessage.append(r & 1)
        decodedMessage.append(g & 1)
        decodedMessage.append(b & 1)
    
    byte = [0,0,0,0,0,0,0,0]
    message = []
    bitCounter = 0
    
    for i in range(0,len(decodedMessage)):
        # Every 8 bits convert binary to a character
        if (bitCounter == 8):
            value = int("".join(map(str, byte)), 2)
            message.append(chr(value))
            bitCounter = 0
            
        # Store the bit into byte until all 8 bits found
        byte[bitCounter] = decodedMessage[i]
        bitCounter += 1

    msg = "".join(map(str,message))
    print('The Secret Message is: ' + msg)
    
    return

def encode(fileName):

    picture = Image.open(fileName)
    picture = picture.rotate(180)
    width = picture.width
    height = picture.height
    # Read in text file containing message to hide
    text = open('secretmessage.txt', 'r')
    message = text.read()

    # check if we can hide the whole message
    if (len(message)*8 + 33 > 3 * picture.width * picture.height):
        print("Larger Picture is needed to hide message.") 
        sys.exit(0)
        
    # Get bit lengh of message and turn into binary
    textLength = format(len(message*8),'032b')
    charsOfMsg = list(message)

    # Convert each character to an integer and then represent it in binary
    binaryMsg = []
    for i in range(len(charsOfMsg)):
        binaryMsg.append(format(ord(charsOfMsg[i]),'08b'))

    # add the textlength
    encodeBin = list(textLength)
    encodeBin.append('0')

    # Add the message
    for j in range(len(binaryMsg)):
        encodeBin = encodeBin + list(binaryMsg[j])

	# Grab Pixels
    pngImagePixels = list(picture.getdata())
    newPixels = []
   
    counter = 0
    index = 0
    rangeX = int(math.ceil((float(int(textLength,2)/3)))+11)
    for index in range(rangeX):
		#grabs rgb
        r,g,b = pngImagePixels[index]

        if (counter < len(encodeBin)-2):
            r =  format(r,'08b')
            g =  format(g,'08b')
            b =  format(b,'08b')
 
            r = r[:7] + encodeBin[counter]
            r = int(r, 2)
           
            g = g[:7] + encodeBin[(counter+1)]
            g = int(g, 2)
           
            b = b[:7] + encodeBin[(counter+2)]
            b = int(b,2)
           
            newPixels.append(tuple([r,g,b]))
			#Noticed it needs this if only 2 letters in secret message
        elif (counter == len(encodeBin)-1):
            r =  format(r,'08b')
            r = r[:7] + encodeBin[counter]
            r = int(r, 2)  
            newPixels.append(tuple([r,g,b]))				
        
		#index moves to next pixel and counter updates for 'rgb'		
        counter += 3
        index += 1
	
	#Add your hidden message to picture
    picture.putdata(newPixels)	
    
	# Rotate image back to original orientation 
    picture = picture.rotate(180)
    
    name, extension = os.path.splitext(fileName)
    # add _Encoded and makes it a PNG
    picture.save('{}_Encoded.png'.format(name), "PNG")
    text.close()
	
    return

# Placeholder for console-entered file name
fileName = "testImage.png"

# Parse arguments to choose encode or decode and file used
parser = argparse.ArgumentParser(description = "image")
parser.add_argument('-d', metavar = 'Decode', type = str, help = 'Need image to decode')
parser.add_argument('-e', metavar = 'Encode', type = str, help = 'Need image to encode')
args = parser.parse_args()

if (args.__dict__['d'] != None):
    fileName = args.__dict__['d']
    decode(fileName)
elif (args.__dict__['e'] != None):
    fileName = args.__dict__['e']
    encode(fileName)