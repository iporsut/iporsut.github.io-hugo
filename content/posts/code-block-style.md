---
title: "เพิ่ม CSS เพื่อให้แสดง block ของ source code อย่างง่ายๆ"
date: 2019-05-30T12:33:30+07:00
draft: false
---

## เพิ่ม CSS เพื่อให้แสดง block ของ source code อย่างง่ายๆ

เนื่องจากตอนนี้เวลาแสดง code ใน page ผมใช้ของง่ายๆคือ tag pre และ code เช่น

```
<pre><code>puts "Hello, World"</code></pre>
```

ซึ่งมันจะ render ออกมาดิบๆ แบบนี้

<pre><code>puts &quot;Hello, World&quot;</code></pre>

ผมยังเลยลองปรับ style ให้กับแต่ละ pre &gt; code อีกนิดนึงโดยใช้ CSS แบบนี้

```
body {
    line-height: 140%;
    margin: 50px;
}

pre > code {
    font-size: 120%;
    background-color: #eee;
    border: 1px solid #999;
    display: block;
    padding: 20px;
    word-wrap: break-word;
}
```

พอได้ CSS แบบนี้ก็ทำให้ตรงส่วนที่เป็นโค้ดอ่านง่ายขึ้นมาหน่อย ซึ่งก็ปรับง่ายๆแค่เปลี่ยนสี background-color ปรับ display ให้มันเป็น block แล้วปรับ padding ระยะระหว่าง text ข้างในกับขอบของ block แล้วก็ตีเส้นกรอบนิดหน่อย
