---
title: "สร้าง elisp ฟังก์ชันเพื่อ Escape HTML Entities โดยเรียกใช้ Ruby"
date: 2019-06-01T14:31:45+07:00
draft: false
---

![example use function](/el-ruby-escape-unescape-html.gif)

ผมใช้ [Emacs](https://www.gnu.org/software/emacs/) เป็นเครื่องมือหลักในการเขียนโค้ด รวมทั้งที่ใช้เขียน blog post นี้อยู่ด้วย ซึ่ง เราสามารถเพิ่มความสามารถต่างๆให้ Emacs ได้โดยการเขียน [elisp](https://en.wikipedia.org/wiki/Emacs_Lisp) ซึ่งเป็นภาษาตระกูล [LISP](https://en.wikipedia.org/wiki/Lisp_(programming_language)) ภาษาหนึ่งซึ่งติดมากับตัว Emacs เลย

ผมอยากได้ฟีเจอร์นึงคือแปลงโค้ด HTML โดย escape HTML Entities ใน text ที่ผมเลือกเอาไว้อยู่ ซึ่งผมสามารถเขียน elisp ฟังก์ชันเพื่อช่วยแปลงได้ เพราะว่าผมพยายามหาแล้วมีคนเขียนฟังก์ชันไว้แล้วหลายคน แต่ไม่ถูกใจผม ผมอยากได้แบบเดียวกันกับที่ [Ruby CGI.escape]({{< ref "ruby-escape-html-entities" >}}) ทำซึ่งผมเขียนไว้ในโพสต์ก่อนหน้านี้

ผมเลยได้ท่านี้มาคือ เขียน elisp ฟังก์ชันให้ส่งโค้ด Ruby ไปทำงานแล้วเอาผลลัพธ์กลับมา หน้าตา source code ของ elisp ฟังก์ชันเลยออกมาเป็นแบบนี้

```
(defun escape-html (start end)
  (interactive "r")
  (if (use-region-p)
      (let ((regionp (buffer-substring start end)))
	(delete-region start end)
	(call-process-region regionp nil "ruby" nil t nil "-e" "# encoding: utf-8
require 'cgi'
print CGI.escapeHTML(ARGF.read)"))))
```

คราวๆคือฟังก์ชันนี้จะรับค่าตำแหน่งเริ่มต้น และ สิ้นสุดที่ของ region ที่ถูกเลือกไว้อยู่ แล้วผมจะใช้ function buffer-substring เพื่อดึง text ตรงนั้นมาเก็บไว้ในตัวแปร regionp

หลังจากนั้นจะลบ text ตรง region นั้นทิ้งออกไปจาก buffer ก่อน

แล้วใช้ call-process-region เพื่อส่ง regionp ไปทาง standard input ให้กับ Ruby โดยผมเขียนโค้ด Ruby ลงไปตรงๆผ่าน option -e ของ Ruby โค้ดตรงนั้นคือ

```
# encoding: utf-8
require 'cgi'
print CGI.escapeHTML(ARGF.read)
```

ซึ่งผมใช้ ARGF.read เพื่อรับค่าจาก standard input นั่นเอง

จากโค้ด escape-html ข้างบน เราก็เอามาเขียน unescape-html ได้เหมือนกันเปลี่ยนแค่ตอนเรียน method ของ CGI แค่นั้นเองแบบนี้

```
(defun unescape-html (start end)
  (interactive "r")
  (if (use-region-p)
      (let ((regionp (buffer-substring start end)))
	(delete-region start end)
	(call-process-region regionp nil "ruby" nil t nil "-e" "# encoding: utf-8
require 'cgi'
print CGI.unescapeHTML(ARGF.read)"))))
```
