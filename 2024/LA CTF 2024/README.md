## LA CTF 2024 WriteUp
# **web/terms-and-conditions**
```javascript
const accept = document.getElementById("accept");
            document.body.addEventListener("touchstart", (e) => {
                document.body.innerHTML = "<div><h1>NO TOUCHING ALLOWED</h1></div>";
            });
```
If i move cursor to button, the button moves another way.
`I Accpet` button's element ID is `accept`.
```javascript
document.getElementById('accept').click();
```
   
`lactf{that_button_was_definitely_not_one_of_the_terms}`   


# **web/flaglang**
```yaml
Flagistan:
  iso: FL
  msg: "<REDACTED>"
  password: "<REDACTED>"
  deny: 
    ["AF","AX","AL", ~~~ ]
```
flag country has redacted password.
```javascript
app.get('/view', (req, res) => {
  if (!req.query.country) {
    res.status(400).json({ err: 'please give a country' });
    return;
  }
  if (!countries.has(req.query.country)) {
    res.status(400).json({ err: 'please give a valid country' });
    return;
  }
  const country = countryData[req.query.country];
  const userISO = req.signedCookies.iso;
  if (country.deny.includes(userISO)) {
    res.status(400).json({ err: `${req.query.country} has an embargo on your country` });
    return;
  }
  res.status(200).json({ msg: country.msg, iso: country.iso });
});
```
I send request `/view?country=Flagistan`.   
It returned err...   

Edit `iso` cookie value to anything except `FL`, you can get flag.   
   
`lactf{n0rw3g7an_y4m7_f4ns_7n_sh4mbl3s}`   


# **rev/shattered-memories**
```c
v4 = strncmp(v10, "t_what_f", 8uLL);
v14 += v3 == 0;
v5 = strncmp(v13, "t_means}", 8uLL);
v14 += v5 == 0;
v6 = strncmp(v12, "nd_forge", 8uLL);
v14 += v6 == 0;
v7 = strncmp(s, "lactf{no", 8uLL);
v14 += v7 == 0;
v8 = strncmp(v11, "orgive_a", 8uLL);
v14 += v8 == 0;
```
Disassembled in IDA, you can get flag.   
   
`lactf{not_what_forgive_and_forget_means}`


# **rev/aplet321**
```c
do
  {
    v6 += strncmp(v3, "pretty", 6uLL) == 0;
    v5 += strncmp(v3++, "please", 6uLL) == 0;
  }
  while ( v3 != v7 );
  if ( v5 )
  {
    if ( strstr(s, "flag") )
    {
      if ( v6 + v5 == 54 && v6 - v5 == -24 )
      {
        puts("ok here's your flag");
        system("cat flag.txt");
      }
```
Disassembled in IDA.   
`v6` + `v5` = 54 , `v6` - `v5` = -24    
`v5` = 39, `v6` = 15   
   
```
pretty pretty pretty pretty pretty pretty pretty pretty pretty pretty pretty pretty pretty pretty pretty please please please please please please please please please please please please please please please please please please please please please please please please please please please please please please please please please please please please please please please 
```
send payload including `pretty` 15 times, `please` 39 times.   

`lactf{next_year_i'll_make_aplet456_hp3c1a7bip5bmnc}`


# **crypto/valentines-day**
`ropgf{qvjal_dfuxaxzbk_gbq_jeci_hdt_nr_hdr_eexij}`
Encrypted flag in `ct.txt`.   
Use vigenere cipher decoder, you can get flag.   
   
key:
```
nevergonnagiveyouupnevergonnaletyoudownnevergonnarunaroundanddesertyounevergonnamakeyoucrynevergonnasaygoodbyenevergonnatellalieandhurtyouihopeyouenjoyedthissong
```

`lactf{known_plaintext_and_were_off_to_the_races}`

# **crypto/very-hot**
```python
from Crypto.Util.number import getPrime, isPrime, bytes_to_long
from flag import FLAG

FLAG = bytes_to_long(FLAG.encode())

p = getPrime(384) # 116자리수
print(p)
while(not isPrime(p + 6) or not isPrime(p + 12)):
    p = getPrime(384) # 2657
q = p + 6
r = p + 12

n = p * q * r
e = 2**16 + 1 # 65537
ct = pow(FLAG, 65537, n)

print(f'n: {n}')
print(f'e: {e}')
print(f'ct: {ct}')
```
`p`, `p+6`, `p+12`, `n` are prime numbers.   
I found `n` from FactorDB.    

```python
from Crypto.Util.number import inverse
from Crypto.Util.number import getPrime, isPrime, bytes_to_long, long_to_bytes

n = 1056511174...
ct = 9953835612...
e = 65537

p=2194276565

phi_n = (p - 1) * (p+6 - 1) * (p+12 - 1)
d = inverse(e, phi_n)
pt = pow(ct, d, n)

print("FLAG:", long_to_bytes(pt))
```
  
`lactf{c4n_y0u_plz_unm1x_my_c1gn4lz}`

# **crypto/selamat pagi**
```
Efe kqkbkx czwkf akfs kdkf qzfskf wzdcjtfk
Ieqku kqk akfs ikxj kck akfs wkak ukikukf :Q
Lzfqztk ukdj kqk qe wefe: bkvim{wzbkdki_ckse_kckukx_ukdj_wjuk_kfkbewew_mtzujzfwe}
```
Encrypted Indonesian...    

I just solved it letter by letter with google and naver dictionary...   

`lactf{selamat_pagi_apakah_kamu_suka_analisis_frekuensi}`

# **misc/infinite loop**
You have to find flag in Google Form Loop.   
Just Press F12 and search `lactf` keyword, you can find flag easily.   
   
`lactf{134k1ng_4h3_f04mz_s3cr3tz}`   

# **misc/mixed signals**
Just listen wav file.   
If you gather all the first letters of the words you hear, you can get flag.   

`lactf{c4n_y0u_plz_unm1x_my_s1gn4lz}`

# **misc/one by one**
Press F12 and find `jsmodel` in `role="listitem"` in `role="list"` at each questions in Google Forms.   
Only one letter has another number.   
Combine all letters from 26 questions.   

`lactf{1_by_0n3_by3_un0_*,\"g1'}`