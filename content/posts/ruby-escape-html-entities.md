---
title: "Ruby Escape HTML Entities"
date: 2019-05-31T04:48:35+07:00
draft: false
---
เวลาที่เราต้องเอาโค้ดมาแสดงที่หน้า HTML เนี่ย บางครั้งสัญลักษณ์ที่ใช้ในโค้ดนั้นก็ดันเป็นสัญลักษณ์ที่ถูกใช้ใน syntax ของ HTML เอง ซึ่งอักษรเหล่านี้เรียกว่า [HTML Entities](https://www.w3schools.com/html/html_entities.asp) ดังนั้นเวลาจะเขียนสัญลักษณ์พวกนี้ต้องถูกเขียนอยู่ในรูปแบบอื่นที่เข้ารหัสไว้ เช่นจะเขียนเครื่องหมายน้อยกว่า ก็ต้องเขียนเป็น &lt; จะเขียนเครื่องหมายมากกว่าก็ต้องเขียนเป็น &gt;

สำหรับภาษา [Ruby](https://www.ruby-lang.org) นั่นมี class CGI ที่มี method escapeHTML ให้เราแปลงจากสัญลักษณ์ที่เขียนปกติไปเป็นรูปแบบ HTML Entities และมี unescapeHTML เพื่อแปลงกลับ

ตัวอย่าง escapeHTML คือใช้แบบนี้

```
require "cgi";
puts CGI.escapeHTML('<div>Hello</div>');
```

ก็จะถูกแปลงเป็น

```
&lt;div&gt;Hello&lt;/div&gt;
```

ตัวอย่าง unescapeHTML คือใช้แบบนี้

```
require "cgi";
puts CGI.unescapeHTML('&lt;div&gt;Hello&lt;/div&gt;');
```

ก็จะถูกแปลงกลับเป็น

```
<div>Hello</div>
```
