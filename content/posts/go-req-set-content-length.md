---
title: "Go Request: อย่าลืม ContentLength ถ้าไม่ได้กำหนด Body ด้วย NewRequest"
date: 2019-06-13T18:38:36+07:00
draft: false
---

ปกติเวลาเราต้องการยิง HTTP Request ด้วย Go เราสามารถใช้ package net/http ช่วยได้โดยใช้การสร้าง request ใหม่แล้วเรียกเมธอด Do แบบนี้

```
package main

import (
	"io"
	"log"
	"net/http"
	"os"
	"strings"
)

func main() {
	r, err := http.NewRequest(
			"POST",
			"https://httpbin.org/delay/1",
			strings.NewReader(`{"say": "Hello, World"}`))
	if err != nil {
		log.Fatal(err)
	}
	resp, err := http.DefaultClient.Do(r)
	if err != nil {
		log.Fatal(err)
	}
	io.Copy(os.Stdout, resp.Body)
}
```

สิ่งที่ http.NewRequest ทำให้นอกจากจะกำหนดค่าให้ r.Body แล้วยังกำหนดค่า r.ContentLength ให้อีกด้วย โดยหาขนาดจาก body ที่เราส่งเข้าไป แต่ถ้าเกิดเราเรียก http.NewRequest โดยกำหนด body เป็น nil ไปแล้วล่ะก็ r.ContentLength ก็ยังคงเป็น 0

ทีนี้เราสามารถกำหนดค่า r.Body เพิ่มเติมเข้าไปได้ แต่ว่าตอนนี้ต้อง wrap ด้วย ioutil.NopCloser เพราะ type ของ Body จริงๆแล้วคือ io.ReadCloser แต่ strings.Reader เป็นแค่ io.Reader เท่านั้น เช่น

```
r.Body = ioutil.NopCloser(strings.NewReader(`{"say": "Hello, World"}`))
```

แต่เมื่อไหร่ก็ตามที่ request เรามี Body แต่ ContentLength เป็น 0 เวลาเรียก `http.DefaultClient.Do(r)` ไปจริงๆนั้นมันจะกำหนด header "Content-Length: -1" ออกไป

การส่ง Content-Length -1 ออกไปจะมีปัญหากับบาง server ที่มองว่ามี Body ไม่ตรงกับ Content-Length แล้วจะตอบกลับมาด้วย 400 Bad Request

วิธีแก้คือ ถ้ามีความจำเป็นต้องกำหนดค่า Body เองโดยไม่ผ่าน NewRequest อย่าลืมกำหนด ContentLength ไปด้วยเช่นกรณีนี้ก็ทำได้ง่ายๆคือ

```
package main

import (
	"io"
	"io/ioutil"
	"log"
	"net/http"
	"os"
	"strings"
)

func main() {
	r, err := http.NewRequest("POST", "https://httpbin.org/delay/1", nil)
	if err != nil {
		log.Fatal(err)
	}
	buf := strings.NewReader(`{"say": "Hello, World"}`)
	r.Body = ioutil.NopCloser(buf)
	r.ContentLength = int64(buf.Len())
	resp, err := http.DefaultClient.Do(r)
	if err != nil {
		log.Fatal(err)
	}
	io.Copy(os.Stdout, resp.Body)
}
```
