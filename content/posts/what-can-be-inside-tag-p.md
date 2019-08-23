---
title: "HTML: มีอะไรอยู่ได้บ้างภายใน tag <p>"
date: 2019-05-31T22:35:00+07:00
draft: false
---

![Tag p error](/tag-p-error.png)

จากรูปจะเห็นว่าผมมี tag `<p>` เปิดในบรรทัด 18 และ tag `</p>` ในบรรทัดที่ 33 แต่ว่า browser กลับบอกว่า tag ปิดนั้น error เพราะไม่ tag เปิดซะอย่างนั้น

ผมจึงไปค้นมาว่าทำไม browser ถึงบอกแบบนี้ ไปเจอมาจากเว็บของ MDN (Mozilla Developer Network) https://developer.mozilla.org/en-US/docs/Web/HTML/Element/p

> Paragraphs are block-level elements, and notably will automatically close if another block-level element is parsed before the closing `</p>` tag.

นั่นคือ tag `<p>` เป็น block-level elements ซึ่ง HTML elements นั้นแบ่งได้หลักๆสองกลุ่มคือ [block-level element](https://developer.mozilla.org/en-US/docs/Web/HTML/Block-level_elements) กับ [inline-level element](https://developer.mozilla.org/en-US/docs/Web/HTML/Inline_elements) และตาม document ของ tag p นั้น จะถือว่า tag โดนปิดอัตโนมัติ ถ้าเจอ block-level elements ก่อนที่จะเจอ tag ปิดนั่นเอง

ซึ่งถ้าดูในรูปจะพบว่าผมมี tag `<pre>` แทรกมา ซึ่งมันเป็น block-level element เหมือนกัน ทำให้ browser มองว่า tag p นั้นปิดไปก่อนแล้วพอเจอ tag `</p>` ก็เลยมองว่าปิดโดยไม่มีการเปิดมาก่อนนั่นเอง

## สรุป

จะพบว่ารายละเอียดเล็กๆน้อยๆของ HTML แต่ละ tag นั้นมีอยู่ใน document ให้เราอ่านหมดแล้ว ซึ่งช่วยให้เราใช้แต่ละ tag ได้สื่อความหมายอย่างที่มันถูกออกแบบมาจริงๆขึ้นมาก โดยที่ไม่ใช่แค่จับ tag ซ้อนๆกันไปมา ซึ่งจริงๆก็มีอะไรให้อ่านอีกเยอะเลยเกี่ยวกับ HTML ลองอ่านดูได้ในเว็บ MDN นะครับ
