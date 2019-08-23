---
title: "Ruby: ความแตกต่างระหว่างการ ส่ง message และ เรียก method"
date: 2019-06-03T21:13:40+07:00
draft: false
---

## เรียก method

ในภาษาที่รองรับการเขียนโปรแกรมแบบ Object Oriented ส่วนใหญ่แล้วก็ให้เราสร้าง Class เพื่อเป็นพิมพ์เขียวให้การสร้าง Object ซึ่งรายละเอียดของที่อยู่ใน Class นั่นก็คือ method ที่จะเป็นพฤติกรรมที่ Object นั้นสามารถทำได้ ดังนั้นส่วนใหญ่แล้วเวลาเราเห็นโค้ดแบบนี้ เช่น

```
num = str.to_i
```

จะหมายถึงว่าเราเรียก method to_i ของ object str

## ส่ง message

แต่ในอีกมุมนึงความสำคัญของ Object Oriented นั้นไม่ได้อยู่ที่ตัว Object เอง แต่อยู่ที่การสื่อสารกันระหว่างแต่ละ Object ผ่านการส่ง message หากัน

[Alan Kay](https://en.wikipedia.org/wiki/Alan_Kay) ที่เรายกให้เขาเป็นผู้ให้กำเนิดแนวคิด OOP (จริงๆแล้วก็ช่วยกันหลายคน) ได้เคยตอบเมลลิ้งลิสต์เรื่อง [prototypes vs classes was: Re: Sun's HotSpot](http://lists.squeakfoundation.org/pipermail/squeak-dev/1998-October/017019.html) ซึ่งพูดถึงเรื่อง Object เอาไว้ว่า

> I'm sorry that I long ago coined the term "objects" for this topic because it gets many people to focus on the lesser idea. The big idea is "messaging"

และสำหรับ Ruby ถ้าเราเลือกที่จะพูดว่า str.to_i คือการส่ง message to_i ไปให้ object str แล้ว object str ค่อยเป็นคนตัดสินใจเองว่าได้รับ message to_i แล้วจะเรียก method ไหนให้ทำงานจะดูชัดเจนกว่า เพราะในโลกของ Ruby นั้น เราสามารถทำให้ object ตัดสินใจภายหลังได้ว่าจะทำอะไร โดยที่เราไม่ต้องสร้าง method ชื่อเดียวกันกับ message ที่ได้รับเสมอไป โดยการ implement method ชื่อ method_missing นั่นเอง เช่น

```
class C
  def method_missing(m, *args, &block)
    puts "Receiving message: %s" % m
  end
end

c = C.new
c.hello_world
```

จากโค้ด class C ไม่มี method hello_world มีแค่ method_missing แต่เราสามารถส่ง message hello_world ไปให้ object ของ class C ทำงานได้ ซึ่งผลลัพธ์ที่ได้จากโค้ดข้างบนคือข้อความ Receiving message: hello_world
