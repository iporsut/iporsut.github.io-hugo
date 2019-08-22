---
title: "ติดตั้งของเพื่อสร้าง GRPC service ด้วย Go"
date: 2019-05-29T23:50:10+07:00
draft: false
summary: "GRPC เป็น RPC (Remote Procedure Call) Framework ที่ช่วยให้เราสร้าง service เพื่อให้คนอื่นเรียกใช้งานได้เนี่ยแหละ ทีนี้ตัว runtime มันก็มีออกมาให้ใช้งานร่วมกับหลายๆภาษา แต่วันนี้จะลองลงเครื่องมือที่จำเป็นเบื้องต้นเพื่อให้ลองใช้ได้ สร้าง service ง่ายๆเช่นพ่น Hello World กลับออกไป"
---

GRPC เป็น RPC (Remote Procedure Call) Framework ที่ช่วยให้เราสร้าง service เพื่อให้คนอื่นเรียกใช้งานได้เนี่ยแหละ ทีนี้ตัว runtime มันก็มีออกมาให้ใช้งานร่วมกับหลายๆภาษา แต่วันนี้จะลองลงเครื่องมือที่จำเป็นเบื้องต้นเพื่อให้ลองใช้ได้ สร้าง service ง่ายๆเช่นพ่น Hello World กลับออกไป

## เครื่องมือที่ต้องติดตั้ง

1. Go - แน่นอนอันนี้ต้องลงไว้สิ วิธีลงหาต่อเองนะใน https://golang.org/dl/
2. protoc - Protobuf (Protocal Buffer) compiler คือ GRPC เนี่ยใช้ Protocal Buffer เพื่อกำหนด spec servic, emethod ที่มีของ service และ โครงสร้างของ message ต่างๆที่จะถูกส่งมาเป็น argument ให้กับ method ซึ่งเราจะใช้ protoc เพื่อช่วย generate จาก protobuf ให้เป็นโค้ดในภาษาที่เราต้องการเอาไปสร้าง service ซึ่ง protoc จะสร้างโค้ดที่เอาไว้สำหรับเป็น client ให้ด้วย
3. protoc-gen-go - ตัวนี้เป็น protoc plugin คือเพื่อช่วยให้ protoc มัน generate code Go ได้นั่นเอง
4. Go package google.golang.org/grpc - ส่วนอันนี้เป็น package Go ที่เราจะใช้สำหรับ implement GRPC service ด้วย Go

## เริ่มทำ Hello World service

### ติดตั้ง Go

ผมลง Go ไว้แล้ว เป็น version 1.12.5 จริงๆถ้าไม่มีผมก็ลงผ่าน brew ได้อยู่ดีแบบนี้

```
brew install go
```

ซึ่ง version นี้รองรับ go module ด้วยทำให้เริ่มโปรเจคง่ายขึ้นเลย

### ติดตั้ง protoc

ผมใช้ macOS และใช้ brew เป็นตัวช่วยในการจัดการติดตั้งโปรแกรมต่างๆ ดังนั้นผมเลยเริ่มจากการติดตั้ง protoc ผ่าน brew ดังนี้

```
brew install protobuf
```

### ติดตั้ง protoc-gen-go

ส่วนตัว protoc-gen-go มันเขียนด้วย Go จะเลือกใช้ go get ก็ได้ แต่ใน brew มันก็มี package นี้ให้ลงเหมือนกัน ผมเลยใช้วิธีลงผ่าน brew ดังนี้

```
brew install protoc-gen-go
```

(ป.ล. วันที่เขียน blog ตัวติดตั้งมันยังมี bug มีคนเปิด PR แล้วคงแก้ในอีกไม่กี่วัน) หรือใช้วิธีติดตั้งผ่าน go get ดังนี้

```
go get github.com/golang/protobuf/protoc-gen-go
```

ถ้าใคร enable GO111MODULE=on ก็สามารถเลือก version ได้ด้วยแบบนี้

```
go get github.com/golang/protobuf/protoc-gen-go@v1.3.1
```

### สร้าง go module ใหม่

ต่อไปก็เริ่มเขียนโค้ดได้ละ ผมจะยกตัวอย่างโดยสร้างโค้ด 2 โปรแกรม ตัวนึงแทน server ตัวนึงแทน client โดยทั้งสองอยู่ภายใน module ชื่อ hello-grpc สร้าง directory ให้ก่อนเลยแบบนี้ แล้วก็ cd เข้าไป

```
mkdir hello-grpc
cd hello-grpc
```

หลังจากนั้นใช้คำสั่ง แบบนี้เพื่อเริ่มสร้าง go module ที่ชื่อ hello-grpc

```
go mod init hello-grpc
```

### สร้าง proto file เพื่อกำหนด spec service

เริ่มจากสร้าง directory ชื่อ hello แบบนี้

```
mkdir hello
```

หลังจากนั้นสร้างไฟล์ hello.proto เพื่อกำหนด spec ของ service อยู่ภายใน directory proto ดังนี้

```
syntax = "proto3";

package hello;

service Hello {
  rpc Say(SayRequest) returns (SayResponse) {}
}

message SayRequest {
  string name = 1;
}

message SayResponse {
  string message = 1;
}
```

ส่วน syntax ที่เห็นคือเป็นของ Protocal Buffer หลักๆ ก็กำหนด service ชื่อ Hello มี rpc method หนึ่งอันชื่อ Say ที่รับ SayRequest message แล้วตอบกลับด้วย SayResponse message หลังจากนั้นก็กำหนด SayRequest ว่ามี field หนึ่งชื่อ name ส่วน SayResponse ตอบกับด้วย 1 field ชื่อ message

### Generate code ด้วย protoc และ protoc-gen-go

จากนั้นเราจะต้องใช้ protoc ร่วมกับ protoc-gen-go เพื่อ generate โค้ด go ขึ้นมา โดยให้ cd เข้าไปใน directory hello ที่สร้างเมื่อกี้ก่อน แล้วสั่ง

```
protoc --go_out=plugins=grpc:. hello.proto
```

ซึ่งเราจะได้โค้ดที่ถูก generate ออกมาชื่อไฟล์ hello.pb.go

### สร้างโค้ดฝั่ง Server

ใช้ cd ถอยกลับไปที่ directory hello-grpc แล้วสร้าง directory ใหม่ชื่อ server แบบนี้

```
mkdir server
```

จากนั้น cd เข้าไปที่ server แล้วสร้างไฟล์ main.go โดยมีโค้ดดังนี้

```go
package main

import (
	"context"
	"fmt"
	"hello-grpc/hello"
	"log"
	"net"

	"google.golang.org/grpc"
)

type helloServer struct{}

func (hs *helloServer) Say(ctx context.Context, in *hello.SayRequest) (*hello.SayResponse, error) {
	return &hello.SayResponse{Message: fmt.Sprintf("Hello %s", in.Name)}, nil
}

func main() {
	l, err := net.Listen("tcp", ":50051")
	if err != nil {
		log.Fatalf("failed to listen: %v", err)
	}
	s := grpc.NewServer()
	hello.RegisterHelloServer(s, &helloServer{})
	if err := s.Serve(l); err != nil {
		log.Fatalf("failed to serve: %v", err)
	}
}
```

ผมไม่ลงรายละเอียดมากนะว่าโค้ดแต่ละบรรทัดทำอะไรบ้าง เอาเป็นว่า protoc จะ generate โค้ดที่มี interface ตามชื่อ service ใน proto เช่น service Hello ก็จะได้ชื่อ interface HelloServer จะเห็นว่ามี Server เป็น suffix

โค้ดเราต้องสร้าง type ใหม่ขึ้นมาแล้ว implement interface นั้นๆ เช่น ผมสร้าง type helloServer ขึ้นมาแล้ว implement method Say

แล้วใน main ก็ทำการสร้าง grpc.Server ด้วย grpc.NewServer จากนั้นเราจะต้องเรียก RegisterHelloServer จะเห็นว่า method ที่มี Register ด้านหน้าก็เกิดจากโค้ดที่ protoc generate ออกมาเช่นกันเพื่อ register โค้ดที่เรา implement ผูกกับ grpc.Server ที่เพิ่งสร้างเมื่อกี้

ส่วนวิธีรัน grpc.Server เราต้องสร้าง TCP Listener ขึ้นมาก่อนด้วย net.Listen("tcp", ":50051") เพราะ grpc.Server รันบน http2 บน tcp protocol อีกที

หลังจากได้ listener แล้วก็เรียก s.Serve(l) คือส่ง listener l ให้เพื่อบอกว่าให้ grpc.Server รอรับ request ที่ tcp port 50051 นะ ประมาณนี้ครับ

### สร้างโค้ดฝั่ง Client

ใช้ cd ถอยกลับไปที่ directory hello-grpc แล้วสร้าง directory ใหม่ชื่อ client แบบนี้

```
mkdir client
```

จากนั้น cd เข้าไปที่ client แล้วสร้างไฟล์ main.go โดยมีโค้ดดังนี้

```go
package main

import (
	"context"
	"fmt"
	"hello-grpc/hello"
	"log"

	"google.golang.org/grpc"
)

func main() {
	clientConn, err := grpc.DialContext(context.Background(), ":50051", grpc.WithInsecure())
	if err != nil {
		log.Fatalf("failed to dail: %v", err)
	}

	client := hello.NewHelloClient(clientConn)
	sayResp, err := client.Say(context.Background(), &hello.SayRequest{Name: "Por"})
	if err != nil {
		log.Fatalf("failed to say: %v", err)
	}
	fmt.Println(sayResp.Message)
}
```

ฝั่ง client ขั้นตอนการเรียกใช้ method Say ของ Hello ที่ server ให้เราสร้าง grpc.ClientConn ขึ้นมาก่อนด้วย grpc.DialContext พร้อมระบุ context, address (hostname:port) และใส่ option grpc.WithInsecure() เพิ่มไปหน่อยเพราะตอนนี้เรายังไม่ได้ทำเรื่อง tls เรียกวิธีนี้ไปก่อนสำหรับทดสอบตอนนี้

จากนั้นสร้าง HelloClient โดยสั่ง hello.NewHelloClient ซึ่งก็เป็นโค้ดที่ generate จาก protoc เช่นกันเพื่อสร้าง stub object ที่จะเรียก method Say ไปยังฝั่ง server ให้เรา

จากนั้นเราก็เอา client เนี่ยแหละไปเรียก method ได้เลย

## สรุป

สรุป GRPC เป็น framework ที่ช่วยให้เราสร้าง service ในลักษณะ RPC โดยมีเครื่องมือให้เรากำหนด spec service ผ่าน protobuf โดยใช้ protoc ช่วย generate โค้ดในแต่ละภาษาที่รองรับให้เราเอาไป implement ในฝั่ง server และ client ได้ง่ายยิ่งขึ้น และ runtime ของ GRPC เองก็ช่วยเราได้หลายเรื่องเลยเอาไว้โอกาสต่อๆไปค่อยมาเขียนให้อ่านกันอีกที
