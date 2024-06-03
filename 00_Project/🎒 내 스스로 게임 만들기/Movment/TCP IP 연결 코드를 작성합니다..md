server.go
```go
package main

import (
    "bufio"
    "fmt"
    "net"
)

func main() {
    // TCP 리스너를 설정합니다.
    listener, err := net.Listen("tcp", ":8080")
    if err != nil {
        fmt.Println("Error creating listener:", err)
        return
    }
    defer listener.Close()
    fmt.Println("Server is listening on port 8080")

    for {
        // 클라이언트의 연결을 기다립니다.
        conn, err := listener.Accept()
        if err != nil {
            fmt.Println("Error accepting connection:", err)
            continue
        }

        // 고루틴으로 클라이언트의 요청을 처리합니다.
        go handleConnection(conn)
    }
}

func handleConnection(conn net.Conn) {
    defer conn.Close()
    fmt.Println("Client connected")

    // 클라이언트로부터 메시지를 읽습니다.
    reader := bufio.NewReader(conn)
    message, err := reader.ReadString('\n')
    if err != nil {
        fmt.Println("Error reading message:", err)
        return
    }
    fmt.Printf("Received message: %s", message)

    // 메시지를 클라이언트에게 다시 보냅니다.
    _, err = conn.Write([]byte("Message received: " + message))
    if err != nil {
        fmt.Println("Error writing message:", err)
        return
    }
    fmt.Println("Message sent back to client")
}

```

client.go
```go
package main

import (
    "bufio"
    "fmt"
    "net"
    "os"
)

func main() {
    // 서버에 연결합니다.
    conn, err := net.Dial("tcp", "localhost:8080")
    if err != nil {
        fmt.Println("Error connecting to server:", err)
        return
    }
    defer conn.Close()

    // 사용자로부터 메시지를 입력받습니다.
    reader := bufio.NewReader(os.Stdin)
    fmt.Print("Enter message: ")
    message, _ := reader.ReadString('\n')

    // 서버로 메시지를 보냅니다.
    _, err = conn.Write([]byte(message))
    if err != nil {
        fmt.Println("Error sending message:", err)
        return
    }

    // 서버로부터 응답을 읽습니다.
    response, err := bufio.NewReader(conn).ReadString('\n')
    if err != nil {
        fmt.Println("Error reading response:", err)
        return
    }
    fmt.Printf("Received from server: %s", response)
}

```