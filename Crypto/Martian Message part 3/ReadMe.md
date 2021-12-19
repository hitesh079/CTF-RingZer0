# Challenge - Finding out the flag from given sentence - RU9CRC43aWdxNDsxaWtiNTFpYk9PMDs6NDFS
## My Approach:

When I started the challenge, I noticed that the format of the text looked a little familiar. I tried to decode it in python using a base64 decoder and it decoded to the message below:
#### EOBD.7igq4;1ikb51ibOO0;:41R

Having solved some of the challenges before I know that RingZer0 challenge flags usually follow the format of FLAG-#######. Seeing this I thought of trying to XORing the first 4 characters of the decoded text with FLAG.

x = [70,76,65,71]  # FLAG
y = [69,79,66,68]  # EOBD

for i in range(len(x)):
    print (Xor(x[i],y[i]))
    
This output to 3 3 3 3

Which meant that the decoded stream was encrypted by XORing it with 3. I ran another loop to Xor every element with 3 and got the flag.

### Flag - 4jdr782jha62jaLL38972Q
