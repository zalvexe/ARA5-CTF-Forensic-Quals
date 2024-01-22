# The QRazy Chest
Author: zalv

Category: Forensic

**Flag: ARA5{t3chn0bl4d3_nev4h_d13s}**

## Description
Technoblade, the greatest Minecraft player, loves keeping his secrets inside his chests. One day, you, as his ally, need to know his secret that has a flag in it. But unfortunately, he forgot in which chest he kept it so you need to find it yourself. While looking for it, he told you, you might want to grab the ```flag here ```

## Difficulty
Easy

## Solution
From that image, we can use binwalk to check and extract the hidden file

![image](https://github.com/ZalfaNafila/ARA5Forens/assets/92864261/a62bf744-63b9-4719-bf13-66d64ea76980)

We found a GIF file. From the GIF file, you’ll see a lot of QR codes. We can extract all those GIF frames to make it easier to scan the QR. We can use a tool like GraphicsMagick  

```gm convert 6C67C -coalesce +adjoin GIF_Frame%3d.png```  

You'll find 1000 QR frames extracted and when you try to scan the QR, it will give some random string (unless you got lucky and found the flag).   

To automatically scan all of that QR, we can make a script then save the output to a txt file
```python
import cv2
import glob
import pathlib
import pandas as pd
from cv2 import *

folder = glob.glob('foldername/*.png')
df = pd.DataFrame(columns=['filename', 'qr'])

with open("resultQR.txt", "w") as outputFile:
    for file in folder:
        img = cv2.imread(file)
        detect =cv2.QRCodeDetector()
        val, pts, st_code = detect.detectAndDecode(img)
        row = {'filename': file, 'qr': val}
        outputFile.write(str(row)+"\n")
        df = pd.concat([df, pd.DataFrame([row])], ignore_index=True)

    df.head()
```

From the output file, we can see a lot of string. When you try to ```grep flag``` it will give you lot of list

![image](https://github.com/ZalfaNafila/ARA5Forens/assets/92864261/1f4315e4-2ef0-4c5a-aa02-9ef21b0b7a78)

Based on Challenge’s description we can get the hint ```grab flag here``` so we can try to use ```grep "flag here"```

![image](https://github.com/ZalfaNafila/ARA5Forens/assets/92864261/a0cc6847-c019-4c0a-8537-62cbd47420e4)

After we download that file, we’ll see a mostly black image 

![image](https://github.com/ZalfaNafila/ARA5Forens/assets/92864261/ff7e7a5d-a749-45e4-ad6b-15961d84ec2c)

If we apply a filter from Stegsolve, we’ll see the flag **ARA5{t3chn0bl4d3_nev4h_d13s}**

![image](https://github.com/ZalfaNafila/ARA5Forens/assets/92864261/443baf65-11bd-4089-99b4-59b4b1b8d817)

## SOLVER
```bash
#!/bin/bash

binwalk --dd=".*" -q myChest.jpg
cd _myChest.jpg.extracted
gm convert 6C67C -coalesce +adjoin GIF_Frame%3d.png
cd ..
python3 qrcode.py
cat resultQR.txt | grep "flag here"
megadl https://mega.nz/file/TUVxRQpZ#AMmOgOmA86aVmk0wHrWKmMIlgvWKsfvuAleE7BilZBU
java -jar $home/bin/stegsolve.jar
#use Red Plane 1 Filter to find the flag
```
