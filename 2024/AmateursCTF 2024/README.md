## AmateursCTF 2024
# **web/denied**
```javascript
const express = require('express')
const app = express()
const port = 3000

app.get('/', (req, res) => {
  if (req.method == "GET") return res.send("Bad!");
  res.cookie('flag', process.env.FLAG ?? "flag{fake_flag}")
  res.send('Winner!')
})

app.listen(port, () => {
  console.log(`Example app listening on port ${port}`)
})
```
If request with methods except GET, you can find flag in cookie.   

```
curl -X HEAD -i denied amt.rs/
```

`amateursCTF{s0_m@ny_0ptions...}`

# **web/agile-rut**
Could download font file at `/agile-rut.otf`.   
Put font file on Glyphr Studio online web.   

`amateursCTF{0k_but_1_dont_like_the_jbmon0_===}`

# **osint/cherry-blossoms**
You can expect the place is Washington DC from question explanation.   
At Google Map, you can find there.   
Coordination is `38.890, -77.0343`.   

Put token on netcat solution and send coordinates.   

`amateursCTF{l00k1ng_l0v3ly_1n_4k}`