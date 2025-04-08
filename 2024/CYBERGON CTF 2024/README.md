# OSINT
## Favorite Journal
Given jounal is 'Shwe Thway Vol. 45 and No. 15'.

You can see the published date on the cover of Vol. 1 and No. 1.  
Also, there are printing house in last page.  

`CYBERGON_CTF2024{4-1-69_0032}`

## The Statue
The place in image is 'Maha Bodhi Ta Htaung Standing Buddha'.  

`CYBERGON_CTF2024{22.0801_95.2885}`

## Vacation(1)
A png file is given but cannot be opened.  
If you extract hex with 010 editor, word `ftyp` is found.  

`ftyp` means this file is `HEIC` file  (general `HEIF` image), so just convert `HEIF` to `PNG`.  

You can see the view of Ha Long in Vietnam in the picture.  
The place was taken in `Muong Thanh Luxury Ha Long Centre Hotel`.  

`CYBERGON_CTF2024{Muong Thanh Luxury Ha Long Centre Hotel, Ha Long, Vietnam}`

# STEGANO
## Truesight
PNG file given cannot be opend.  
We can guess this file is png file format with `IHDR` and `RGB` hex but without file signature of png file.  

Just add file signature of png file  
`89 50 4E 47 0D 0A 1A 0A`

You can find flag in the normal png file. 
`CYBERGON_CTF2024{y0u_g07_7h3_r!gh7_s1gn5}`

# Crypto
## RSA1
This is simple RSA chall.  
```python
from Crypto.Util.number import inverse, long_to_bytes

n = 157508528276758767638734754424621334466394815259243977959210580239577661657714722726225362774231543920376913579658052494826650164280151836289734452590647102313381584133512835595817708427222746495824286741840967127393187086028742577763080469063534742728547285121808241078515099307495843605080694383425986909029
cip1 = 69950256754119187070741220414057295159525964023691737870808579797990094306696842507546591858691032981385348052406246203530192324935867616305070637936848926878022662082401090988631324024964630729510728043900454511012552105883413265919300434674823577232105833994040714469215427142851489025266027204415434792116
cip2 = 26975575766224799967239054937673125413993489249748738598424368718984020839138611191333159231531582854571888911372230794559127658721738810364069579050102089465873134218196672846627352697187584137181503188003597560229078494880917705349140663228281705408967589237626894208542139123054938434957445017636202240137
e1 = 65539
e2 = 65537

def extended_gcd(a, b):
    if b == 0:
        return a, 1, 0
    g, x1, y1 = extended_gcd(b, a % b)
    x = y1
    y = x1 - (a // b) * y1
    return g, x, y

_, x, y = extended_gcd(e1, e2)

m1 = pow(cip1, x, n)
m2 = pow(cip2, y, n)
m = (m1 * m2) % n

flag = long_to_bytes(m)
print("FLAG:", flag.decode())
```
`CYBERGON_CTF2024{54m3_m0Du1u5!!!!!}`

## Chill Bro
Some human postures are in the picture.

It is `dancing men cipher`, you can decode in `dCode` website.

`CYBERGON_CTF2024{TAKEABREAKBROLETSDANCE}`


# Reconnaissance
## Secure Life
`certificate.der` file is given.  
Just open it and see expiration.  

`CYBERGON_CTF2024{2039:11:24:20:38:00}`