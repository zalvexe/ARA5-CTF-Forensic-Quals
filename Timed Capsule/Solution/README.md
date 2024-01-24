# Time Capsule
Author: zalv

Category: Forensic

**Flag: ARA5{zipp1_zipp1_z4pp4_z4pp4}**

## Description
I discovered my old time capsule, and it has my favorite song in it. The problem is, I forgot the password, and due to its age, the file inside might be corrupted. I also found the note that I wrote a long time ago about the password. My cat said that you might be able to help me. Can you?

## Difficulty
Easy?

## Solution
Based on ```notes.png``` we can make a wordlist to brute force the password of ```TimeCapsule.zip```

```python
with open("wordlist.txt", "w") as outputFile:
	month = [1,2,3,4,5,6,7,8,9,10,11,12]
	character = ["kAor1","s3nKu","sTev3","Lev1","L1Ly"]
	char = ['*','#','!','%','&','+']
	num = [0,1,2,3,4,5,6,7,8,9]

	for i in month:
		for j in character:
			for k in char:
				for l in num:
					for letter in range(ord('A'), ord('Z')+1):
						for m in char:
							outputFile.write(f"{i}{j}{k}{l}{chr(letter)}{m}\n")
							                           
```
After we save the output, use John the Ripper to brute force all the possible passwords



Then we can unlock the zip with that password : ```5s3nKu%3T+```

After unzipping the ```TimeCapsule.zip``` you’ll find another zip named ```MyCapsule.zip```. when you try to unzip it, it will give an error



By that, we know that it doesn’t have the End of central directory signature, which means we need to add that signature. 
Based on this [website](https://users.cs.jmu.edu/buchhofp/forensics/formats/pkzip.html), We can determine at which byte the End-of-central-directory signature started



As you can see, we need to count the byte position from the end, starting with the byte of Comment until byte of Disk Number. (But our zip file doesn't have a comment, so we can start at Comment len byte)


After matching every structure of our zip, now we know where we suppose to change the End of central directory signature, it starts 22 bytes from the very end. The correct signature should be '\x50\x4b\x05\x06'. So we need to change ```5E 2C 2A 26``` to ```50 4B 05 06```and try to unzip it again.



Now it appears to be another error about the header which can be easily solved by changing the header signature



We need to change ```41 52 41 2E``` with the correct signature ```50 4B 03 04```, and try to unzip it again



Now we got the wrong CRC, but the file inside can still be extracted. To fix this, we can find the wrong CRC, which based on that information is ```CB F4 39 26```



Change that with the calculated CRC ```A4 3A C2 1C``` and try to unzip it again



From the ```song.bz2``` we can unzip that with Bunzip2 to get the wav file named ```song```  

When we put that audio into a spectrogram analyzer e.g. Sonic Visualiser, we will get the flag 
```ARA5{zipp1_zipp1_z4pp4_z4pp4}```
