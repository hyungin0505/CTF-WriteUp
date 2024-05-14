## DiceCTF 2024 Quals WriteUp
# **Web/dicedicegoose**
You can view script code at developer tools.

    
```javascript
let player = [0, 1];
let goose = [9, 9];
```
Player and Goose Coordinations are in arrays.   
   
```javascript
let history = [];
    history.push([player, goose]);
```
```javascript
function win(history) {
    const code = encode(history) + ";" + prompt("Name?");

    const saveURL = location.origin + "?code=" + code;
    displaywrapper.classList.remove("hidden");

    const score = history.length;

    display.children[1].innerHTML = "Your score was: <b>" + score + "</b>";
    display.children[2].href =
        "https://twitter.com/intent/tweet?text=" +
        encodeURIComponent(
          "Can you beat my score of " + score + " in Dice Dice Goose?",
        ) +
        "&url=" +
        encodeURIComponent(saveURL);

    if (score === 9) log("flag: dice{pr0_duck_gam3r_" + encode(history) + "}");
  }
```
If `score` is 9, you can get flag.
```javascript
history.push([[1,1],[9,8]])
history.push([[2,1],[9,7]])
history.push([[3,1],[9,6]])
history.push([[4,1],[9,5]])
history.push([[5,1],[9,4]])
history.push([[6,1],[9,3]])
history.push([[7,1],[9,2]])
history.push([[8,1],[9,1]])
win(history)
```
You can get flag on console.   
`dice{pr0_duck_gam3r_AAEJCQEBCQgCAQkHAwEJBgQBCQUFAQkEBgEJAwcBCQIIAQkB}`