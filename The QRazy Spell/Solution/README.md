# The QRazy Chest
Author: zalv

Category: Forensic

**Flag: ARA5{t3chn0bl4d3_nev4h_d13s}**

## Description 
Technoblade's immortal spell is now lost in pieces. Help him find it!

## Difficulty 
Easy

## Solution 
From the challenge's image, we can use binwalk to check and extract the hidden file 

<img src="https://github.com/zalvexe/ARA5Forens/blob/main/The%20QRazy%20Spell/Solution/binwalk.png" width="625">

From there, we know there’s a GIF file of QR codes. 

<img src="https://github.com/zalvexe/ARA5Forens/blob/main/The%20QRazy%20Spell/Solution/Pieces.gif" width="225">

If we try to scan one of them, it will give you a random string. Based on Challenge description, *"the spell is lost in pieces"* So we need to scan all of that QR to know the full string.  
To extract all GIF frames, we can use ```Graphicsmagick```

```gm convert 362E -coalesce +adjoin GIF_Frame%3d.png```

After extracted, we can automatically scan all of them with python 

```python
import cv2
import glob
import pathlib
from cv2 import *

folder = glob.glob('_TheBookOfMagick.jpg.extracted/*.png')

with open("resultQR.txt", "w") as outputFile:
    for file in sorted(folder):
        img = cv2.imread(file)
        detect =cv2.QRCodeDetector()
        data, pts, st_code = detect.detectAndDecode(img)
        outputFile.write(data)

```
From the output file, we can find a link to Mega
[https://mega.nz/file/TUVxRQpZ#AMmOgOmA86aVmk0wHrWKmMIlgvWKsfvuAleE7BilZBU]

<img src="https://github.com/zalvexe/ARA5Forens/blob/main/The%20QRazy%20Spell/Solution/catResult.png" width="625">

After downloading that file, we’ll see a mostly black image

<img src="https://github.com/zalvexe/ARA5Forens/blob/main/The%20QRazy%20Spell/Solution/Picture1.png" width="625">

If we apply a ```Red Plane``` filter from Stegsolve, we’ll see the hidden text which is the flag

<img src="https://github.com/zalvexe/ARA5Forens/blob/main/The%20QRazy%20Spell/Solution/flag.png" width="625">

```ARA5{t3chn0bl4d3_nev4h_d13s}```
