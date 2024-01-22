# Time Capsule
Author: zalv

Category: Forensic

**Flag: ARA5{zipp1_zipp1_z4pp4_z4pp4}**

## Description
I discovered my old time capsule, and it has my favorite song in it. The problem is, I forgot the password, and due to its age, the file inside might be corrupted. I also found some notes I wrote a long time ago about the password. My cat said that you might be able to help me. Can you?

## Difficulty
Easy?

## Solution
Based on notes.png we can make a wordlist to brute force the password of TimeCapsule.zip

```python
with open("wordlist.txt", "w") as outputFile:
    mo = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12]
    charac = ["kAor1", "s3nKu", "sTev3", "Lev1", "L1Ly"]
    rand = ['*', '#', '!', '%', '&', '+']
    num = [0,1,2,3,4,5,6,7,8,9]

    for i in range(12): #month
        for j in range(5): #character name
            for k in range(6): #character
                for l in range(10): #num
                    for letter in range(ord('A'), ord('Z')+1):
                        for m in range(6): #character
                            outputFile.write(
                                f"{mo[i]}{charac[j]}{rand[k]}{num[l]}{chr(let							ter)}{rand[m]}\n")
                            
```
After we save the output, use John the Ripper to brute force all the possible passwords

![image](https://github.com/ZalfaNafila/ARA5Forens/assets/92864261/f5d17cc6-13ec-4b93-b838-164b6aaae352)

Then we can unlock the zip with that password : ```5s3nKu%3T+```

After unzipping the **“TimeCapsule.zip”** you’ll find another zip named **“MyCapsule.zip”**. when you try to unzip it, it will give an error

![image](https://github.com/ZalfaNafila/ARA5Forens/assets/92864261/6722ec04-2480-49c0-a5d2-e74da611e7b4)

By that, we know that it doesn’t have the End of central directory signature, which means we need to add that signature. 
Based on this website https://users.cs.jmu.edu/buchhofp/forensics/formats/pkzip.html, We can count at which byte the End-of-central-directory signature started

![image](https://github.com/ZalfaNafila/ARA5Forens/assets/92864261/c7bfc6a7-ecb0-4ae4-9646-02da9e4b9e60)

As you can see, we need to count the byte from the end, starting with the byte of Comment until byte of Disk Number. (But our zip file doesn't have a comment, so we can start at Comment len byte)

![image](https://github.com/ZalfaNafila/ARA5Forens/assets/92864261/44c70822-6ef1-493a-8d78-c3462df4aea0)

After matching every structure of our zip, now we know where we suppose to change the End of central directory signature, it starts 22 bytes from the very end. The correct signature should be '\x50\x4b\x05\x06'. So we need to change ```5E 2C 2A 26``` to ```50 4B 05 06```and try to unzip it again.

![image](https://github.com/ZalfaNafila/ARA5Forens/assets/92864261/60cdf72d-eabe-42c8-b12f-9dd64c2d06a8)

Now it appears to be another error about the header which can be easily solved by changing the header signature

![image](https://github.com/ZalfaNafila/ARA5Forens/assets/92864261/13c73ac6-cf15-483c-8238-ee47c73e2919)

We need to change ```41 52 41 2E``` with the correct signature ```50 4B 03 04```, and try to unzip it again

![image](https://github.com/ZalfaNafila/ARA5Forens/assets/92864261/34790301-f914-4f5e-95b5-ee40f0d1f812)

Now we got the wrong CRC. To fix this, we can find the wrong CRC, which based on that information is ```CB F4 39 26```

![image](https://github.com/ZalfaNafila/ARA5Forens/assets/92864261/d2950290-8dd7-4f8c-8957-ffe29ae3c1b9)

Change that with the calculated CRC ```A4 3A C2 1C``` and try to unzip it again

![image](https://github.com/ZalfaNafila/ARA5Forens/assets/92864261/389b7484-5ff7-4689-a38b-c878f2df2c74)

From the **song.bz2** we can unzip that with Bunzip2 to get the wav file named **“song”**
When we put that audio into a spectrogram analyzer e.g. Sonic Visualiser, we will get the flag 
```ARA5{zipp1_zipp1_z4pp4_z4pp4}```

![image](https://github.com/ZalfaNafila/ARA5Forens/assets/92864261/01d310a7-f3ca-44f5-9a75-b65f5c4b5e01)
