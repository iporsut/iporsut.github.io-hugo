---
title: "สร้าง Enumerator ด้วย ::new method"
date: 2019-09-19T13:46:09+07:00
summary: "สร้าง Enumerator ด้วย ::new method โดยไม่ต้องสร้าง class ใหม่เพื่อ implement each method เอง"
draft: false
---

สำหรับ Ruby แล้ว enumerator ก็คือ Object ที่สามารถมีพฤติกรรมที่สามารถ generate ค่าบางอย่างออกมาได้ เราสามารถสร้าง enumerator ได้หลายวิธี แต่วิธีที่มาบันทึกไว้หน่อยคือใช้ `Enumerator.new` เพราะอยากได้ Enumerator โดยไม่ต้องสร้าง method ใหม่เอง แค่ใช้การโยน block ให้กับ method `new` เพื่อรับ yielder object มาให้เราส่งค่าเข้าไปเท่านั้น

ตัวอย่างโค้ดเช่น อยากได้ Enumerator อ่านข้อมูลในไฟล์ทีละ 4 MB สามารถสร้างได้แบบนี้

```ruby

enum = Enumerator.new do |yielder|
  loop do
    break if file.eof

    bytes = file.read(4*1024*1024)
    yielder << bytes
  end
end
```

ซึ่งใน block ที่ส่งให้ new ก็เขียนโค้ดวนซ้ำอ่านค่าในไฟล์จนกว่าจะ EOF แล้วก็ใช้ method `<<` ส่งค่า bytes ให้ yielder object นั่นเอง
