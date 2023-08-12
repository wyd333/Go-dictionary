# Go-dictionary
Go语言实现命令行在线词典



# 在线词典

**命令行词典**。效果是输入一个单词，命令行中会显示这个单词的发音和注释。

![img](https://pqmo9n8d6gt.feishu.cn/space/api/box/stream/download/asynccode/?code=ZjcxZDY3ODY5M2RiY2MzNDI2YTg0MjVmY2I0NGEzZjNfd3AwbWJjTnl2SXQ0Y2pHbTdweTJvMEs2MWNBZGNVY09fVG9rZW46VXpKS2JSY3Byb0xOZ0d4TWR3Y2MzRXRsbjJnXzE2OTE4Mzk3NDM6MTY5MTg0MzM0M19WNA)

原理是调用第三方的API去查询结果，并且其打印出来。

实现这个程序的关键在于，如何用Go语言发送HTTP请求和解析JSON。

## 1、抓包

以彩云小译为例：https://fanyi.caiyunapp.com/#/

![img](https://pqmo9n8d6gt.feishu.cn/space/api/box/stream/download/asynccode/?code=N2Q3YTQ4OGM1M2IwYTUxZjlmMzA1NTBjYmFkMTdlNzZfdFZyWndJemZIeUVyRUo4UE84alNjMThFaUd5MktBQmpfVG9rZW46VnR5bGJERktZb2l4NEx4SFU4UmNzc0hBbm5lXzE2OTE4Mzk3NDM6MTY5MTg0MzM0M19WNA)

分析这个请求：

![img](https://pqmo9n8d6gt.feishu.cn/space/api/box/stream/download/asynccode/?code=NzRmNDMyNTRhYzUyMGQ2MjE0OGJmNmZiZjZiMGE2N2ZfYU5BY2FQRUd3ZU83TkdGcjFEbkdKNDdsbnIwMVR0UVdfVG9rZW46R0lyVGJwRTNwb0pxREF4S25EVWM3RlRIbmRkXzE2OTE4Mzk3NDM6MTY5MTg0MzM0M19WNA)

## 2、代码自动生成

我们要实现在Golang里发送这个请求。

但这个请求很复杂，直接用代码来构造很麻烦，所以我们可以用另一种简单的方式来生成请求。

右键浏览器中的请求：

![img](https://pqmo9n8d6gt.feishu.cn/space/api/box/stream/download/asynccode/?code=MzRjNzdiZGQ0MzBlODRmZDNmZWM3ZTkzYjJiZjIxMzFfc2E2bnQ5ank0cnYyNWtaM0ZmVFdVTGJBS1BDdzhlZ1NfVG9rZW46R2JGYmJ3R3VHb0xOaFh4WU1PYWNNeUZqbk5kXzE2OTE4Mzk3NDM6MTY5MTg0MzM0M19WNA)

点击 `Copy as cURL(bash)` 后，打开这个网址：https://curlconverter.com/go/

把刚才复制的东西粘贴进去，自动会生成响应的请求代码：

![img](https://pqmo9n8d6gt.feishu.cn/space/api/box/stream/download/asynccode/?code=ZmVjNmE2ZGRkOGJlMDBiZjk4MDAyMDIyZjQwNDYyMDZfc1FsTU5kbWh5SU4xRm9CQkFsRWZsVjRnSHQ5OE5INWNfVG9rZW46SHV2U2Jhdmk2b3pJQ0Z4V1pqOGNERnB3bkpnXzE2OTE4Mzk3NDM6MTY5MTg0MzM0M19WNA)

将这些代码复制到Goland：

```Go
package main

import (
    "fmt"
    "io"
    "log"
    "net/http"
    "strings"
)

func main() {
    client := &http.Client{}
    var data = strings.NewReader(`{"trans_type":"en2zh","source":"hello"}`)
    req, err := http.NewRequest("POST", "https://api.interpreter.caiyunai.com/v1/dict", data)
    if err != nil {
       log.Fatal(err)
    }
    req.Header.Set("authority", "api.interpreter.caiyunai.com")
    req.Header.Set("accept", "application/json, text/plain, */*")
    req.Header.Set("accept-language", "zh-CN,zh;q=0.9")
    req.Header.Set("app-name", "xy")
    req.Header.Set("cache-control", "no-cache")
    req.Header.Set("content-type", "application/json;charset=UTF-8")
    req.Header.Set("device-id", "b72e9f6cbb97432941b8adf317a17dee")
    req.Header.Set("origin", "https://fanyi.caiyunapp.com")
    req.Header.Set("os-type", "web")
    req.Header.Set("os-version", "")
    req.Header.Set("pragma", "no-cache")
    req.Header.Set("referer", "https://fanyi.caiyunapp.com/")
    req.Header.Set("sec-ch-ua", `"Not/A)Brand";v="99", "Google Chrome";v="115", "Chromium";v="115"`)
    req.Header.Set("sec-ch-ua-mobile", "?0")
    req.Header.Set("sec-ch-ua-platform", `"Windows"`)
    req.Header.Set("sec-fetch-dest", "empty")
    req.Header.Set("sec-fetch-mode", "cors")
    req.Header.Set("sec-fetch-site", "cross-site")
    req.Header.Set("user-agent", "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/115.0.0.0 Safari/537.36")
    req.Header.Set("x-authorization", "token:qgemv4jr1y38jyq6vhvi")
    resp, err := client.Do(req)
    if err != nil {
       log.Fatal(err)
    }
    defer resp.Body.Close()
    bodyText, err := io.ReadAll(resp.Body)
    if err != nil {
       log.Fatal(err)
    }
    fmt.Printf("%s\n", bodyText)
}
```

代码解读：

![img](https://pqmo9n8d6gt.feishu.cn/space/api/box/stream/download/asynccode/?code=NDRjYzYyNDQ5MGY5YzM5YzAxMzg1NjMxZjk2N2RjZTFfNFlGU3lSTW03cjNyVmg4alNxQlZDVDMxMnY1YldSejJfVG9rZW46UFpOU2I5djlLbzRYcTN4YnFwbGM4MkVzbnNjXzE2OTE4Mzk3NDM6MTY5MTg0MzM0M19WNA)

> 这里特别解释一下上图中第40行的 defer 关键字。
>
> 在 Go 语言中，`defer` 是一个用于延迟函数调用执行的关键字。通过使用 `defer`，可以在函数返回之前（无论这个函数是正常结束还是发生了异常），推迟某个函数的执行。这在编写代码时可以很有用，特别是在需要确保资源释放或清理操作的情况下。
>
> `defer` 语句的语法是：
>
> ```Go
> defer functionCall(arguments)
> ```
>
> 当在一个函数内部使用 `defer` 时，被推迟执行的函数调用会被添加到一个栈中，而不会立即执行。在函数执行完毕并即将返回之前，栈中的函数调用会按照逆序执行。因此，如果有多个 `defer` 语句，它们会按照后进先出（LIFO）的顺序执行，即最后一个推迟的函数调用会最先执行，而最早的推迟的函数调用会最后执行。
>
> 以下示例演示了有三个 `defer` 的情况：
>
> ```Go
> package main
> 
> import "fmt"
> 
> func main() {
>     defer fmt.Println("第一个defer")
>     defer fmt.Println("第二个defer")
>     defer fmt.Println("第三个defer")
>     fmt.Println("正常的函数调用")
> }
> ```
>
> 在这个示例中，尽管 `fmt.Println("正常的函数调用")` 在代码中位于三个 `defer` 语句之前，但它会最先执行。然后，`defer` 语句会按照后进先出的顺序执行，所以先执行第三个 `defer`，然后是第二个 `defer`，最后是第一个 `defer`。这就是 `defer` 的执行顺序。
>
> `defer` 的设计是为了在函数返回前执行一些清理工作，或者确保一些资源被正确释放。通过使用 `defer`，我们可以更方便地管理代码，并确保清理操作不会被遗漏。

运行自动生成得到的代码块，成功输出一大串JSON：

![img](https://pqmo9n8d6gt.feishu.cn/space/api/box/stream/download/asynccode/?code=ODMxZGNhNzhkOTU0MWQ5YTY5YzJhOTA5MWRmNjkzMTlfY2xjOHc3YjZ1ZWNsNE90cmREckxqMFlkc2VGR0gyck1fVG9rZW46R0prbmJrSVNxb1lPUDZ4YjMxY2N5bXdHbmtlXzE2OTE4Mzk3NDM6MTY5MTg0MzM0M19WNA)

说明请求成功。

但是，这片请求代码是固定的，传入的data也是固定的 `data = strings.NewReader(`{"trans_type":"en2zh","source":"hello"}`)`

我们肯定希望用户能通过一个变量进行输入，想翻译什么单词，就翻译什么单词，而不是固定的只能输入JSON字符串。因此，我们要用到JSON序列化。

## 3、生成request body

如何进行JSON序列化？在上一篇文章中提到过：https://juejin.cn/post/7265577455208955938#heading-62

只需要构造一个结构体，让这个结构体的字段名称和JSON的结构一一对应，然后直接调用 json.Marshal() 即可。

因此这里，我们也需要构造出一个结构体，然后将结构体序列化，通过这样的方式，让data中的值是可变的：

```Go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "io"
    "log"
    "net/http"
)

//构建结构体
type DictRequest struct {
    TransType string `json:"trans_type"`
    Source    string `json:"source"`
    UserID    string `json:"user_id"`
}

func main() {
    client := &http.Client{}
    //将翻译的类型和要翻译的单词传入结构体
    request := DictRequest{TransType: "en2zh", Source: "hello"}
    buf, err := json.Marshal(request)
    if err != nil {
       log.Fatal(err)
    }
    var data = bytes.NewReader(buf)
    
    req, err := http.NewRequest("POST", "https://api.interpreter.caiyunai.com/v1/dict", data)
    if err != nil {
       log.Fatal(err)
    }
    req.Header.Set("authority", "api.interpreter.caiyunai.com")
    req.Header.Set("accept", "application/json, text/plain, */*")
    req.Header.Set("accept-language", "zh-CN,zh;q=0.9")
    req.Header.Set("app-name", "xy")
    req.Header.Set("cache-control", "no-cache")
    req.Header.Set("content-type", "application/json;charset=UTF-8")
    req.Header.Set("device-id", "b72e9f6cbb97432941b8adf317a17dee")
    req.Header.Set("origin", "https://fanyi.caiyunapp.com")
    req.Header.Set("os-type", "web")
    req.Header.Set("os-version", "")
    req.Header.Set("pragma", "no-cache")
    req.Header.Set("referer", "https://fanyi.caiyunapp.com/")
    req.Header.Set("sec-ch-ua", `"Not/A)Brand";v="99", "Google Chrome";v="115", "Chromium";v="115"`)
    req.Header.Set("sec-ch-ua-mobile", "?0")
    req.Header.Set("sec-ch-ua-platform", `"Windows"`)
    req.Header.Set("sec-fetch-dest", "empty")
    req.Header.Set("sec-fetch-mode", "cors")
    req.Header.Set("sec-fetch-site", "cross-site")
    req.Header.Set("user-agent", "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/115.0.0.0 Safari/537.36")
    req.Header.Set("x-authorization", "token:qgemv4jr1y38jyq6vhvi")
    resp, err := client.Do(req)
    if err != nil {
       log.Fatal(err)
    }
    defer resp.Body.Close()
    bodyText, err := io.ReadAll(resp.Body)
    if err != nil {
       log.Fatal(err)
    }
    fmt.Printf("%s\n", bodyText)
}
```

上述代码中，手动将要翻译的类型（"en2zh"）和单词（"hello"）传入了结构体。

此时代码的运行结果应与一开始的代码运行结果完全相同。对请求的序列化的逻辑就完成了。

## 4、解析 response body

完成了请求的序列化后，我们还要进行响应的反序列化，这样才能得到结果。这我们要解析出这一堆响应，并且获取到其中的一些关键信息如"explanations"等。

![img](https://pqmo9n8d6gt.feishu.cn/space/api/box/stream/download/asynccode/?code=M2JlNjJjMmM5NGViODgzMDVkODNkMGNiNDE1ZDgxYWZfYk1rY2xJdkV2TFFmNkxQYnNScGs0NkVKVUYzcHpyVmdfVG9rZW46RTByZ2JrWm1Kb1g3Yk14d3RremNkV0xkbnVzXzE2OTE4Mzk3NDM6MTY5MTg0MzM0M19WNA)

如果是python或者js，返回的会是一个字典的结构，可以直接通过 [] 或  .  去取值。但是Go是一种强类型的语言，这种方式并不是最佳实践（虽然也可以做到）。

**更常见的方式是和处理request body一样，写一个结构体，这个结构体的字段和返回的response是一一对应的。再把返回的****json****反****序列化****（json.Unmarshal()）到结构体里面。**

但是，我们看到，浏览器里返回的这个response结构非常复杂，手动一一创建结构体去对应显然不是好的做法。所以我们还是用代码生成的方式来实现。

打开这个工具网站：https://oktools.net/json2go

然后，把彩云小译界面的 response 的json粘贴进去：

![img](https://pqmo9n8d6gt.feishu.cn/space/api/box/stream/download/asynccode/?code=YzU5Mjc5NjIxMzQzZTJhN2RhZDFiYWRkYjA0M2U2OTBfZGVtWFAwdDYwa1gwZkJob3Rwc1Z4S29YdlRMZXdiRXRfVG9rZW46WmFZZ2JHcGxQb0N3TUh4Q29IRWN3aEJ2bmhmXzE2OTE4Mzk3NDM6MTY5MTg0MzM0M19WNA)

![img](https://pqmo9n8d6gt.feishu.cn/space/api/box/stream/download/asynccode/?code=YzIwYzBiNDUzZmM1Y2MwNzVhMzcxM2ExYzdlYWIwNzdfNmNKWkwxUVRYeDJjWDRPMk0yM0JlSU9FNzR6V2l6Yk5fVG9rZW46SEFSZWJjVWhEb1kxUk14MHdpeWNkSng5blFlXzE2OTE4Mzk3NDM6MTY5MTg0MzM0M19WNA)

点击“转换-嵌套”，就会生成一个结构体。（如果点击“转换-展开”，会生成独立的多个结构体。）

将生成的结构体代码粘贴到Goland中，将结构体改名为 DictResponse，然后修改最后处理响应的部分代码：

```Go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "io"
    "log"
    "net/http"
)

type DictRequest struct {
    TransType string `json:"trans_type"`
    Source    string `json:"source"`
    UserID    string `json:"user_id"`
}

//粘贴过来的响应结构体
type DictResponse struct {
    Rc   int `json:"rc"`
    Wiki struct {
    } `json:"wiki"`
    Dictionary struct {
       Prons struct {
          EnUs string `json:"en-us"`
          En   string `json:"en"`
       } `json:"prons"`
       Explanations []string      `json:"explanations"`
       Synonym      []string      `json:"synonym"`
       Antonym      []interface{} `json:"antonym"`
       WqxExample   [][]string    `json:"wqx_example"`
       Entry        string        `json:"entry"`
       Type         string        `json:"type"`
       Related      []interface{} `json:"related"`
       Source       string        `json:"source"`
    } `json:"dictionary"`
}

func main() {
    client := &http.Client{}
    request := DictRequest{TransType: "en2zh", Source: "hello"}
    buf, err := json.Marshal(request)
    if err != nil {
       log.Fatal(err)
    }
    var data = bytes.NewReader(buf)

    req, err := http.NewRequest("POST", "https://api.interpreter.caiyunai.com/v1/dict", data)
    if err != nil {
       log.Fatal(err)
    }
    req.Header.Set("authority", "api.interpreter.caiyunai.com")
    req.Header.Set("accept", "application/json, text/plain, */*")
    req.Header.Set("accept-language", "zh-CN,zh;q=0.9")
    req.Header.Set("app-name", "xy")
    req.Header.Set("cache-control", "no-cache")
    req.Header.Set("content-type", "application/json;charset=UTF-8")
    req.Header.Set("device-id", "b72e9f6cbb97432941b8adf317a17dee")
    req.Header.Set("origin", "https://fanyi.caiyunapp.com")
    req.Header.Set("os-type", "web")
    req.Header.Set("os-version", "")
    req.Header.Set("pragma", "no-cache")
    req.Header.Set("referer", "https://fanyi.caiyunapp.com/")
    req.Header.Set("sec-ch-ua", `"Not/A)Brand";v="99", "Google Chrome";v="115", "Chromium";v="115"`)
    req.Header.Set("sec-ch-ua-mobile", "?0")
    req.Header.Set("sec-ch-ua-platform", `"Windows"`)
    req.Header.Set("sec-fetch-dest", "empty")
    req.Header.Set("sec-fetch-mode", "cors")
    req.Header.Set("sec-fetch-site", "cross-site")
    req.Header.Set("user-agent", "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/115.0.0.0 Safari/537.36")
    req.Header.Set("x-authorization", "token:qgemv4jr1y38jyq6vhvi")
    resp, err := client.Do(req)
    if err != nil {
       log.Fatal(err)
    }
    defer resp.Body.Close()
    bodyText, err := io.ReadAll(resp.Body)
    if err != nil {
       log.Fatal(err)
    }
    
    //处理响应
    var dictResponse DictResponse
    err = json.Unmarshal(bodyText, &dictResponse)
    if err != nil {
       log.Fatal(err)
    }
    //用 %#v 会以最详细的方式来打印结构体，包括结构体的名字和字段的名字
    fmt.Println("%#v\n", dictResponse)
}
```

我们需要从response中筛选出我们需要的信息：如“音标”，“解释”：

```Go
//fmt.Println("%#v\n", dictResponse)
fmt.Println(word, "UK:", dictResponse.Dictionary.Prons.En,
    "US:", dictResponse.Dictionary.Prons.EnUs)
for _, item := range dictResponse.Dictionary.Explanations {
    fmt.Println(item)
}
```

最终经过调整，完整的程序代码如下：

```Go
package main

import (
    "bytes"
    "encoding/json"
    "fmt"
    "io"
    "log"
    "net/http"
    "os"
)

// 请求
type DictRequest struct {
    TransType string `json:"trans_type"`
    Source    string `json:"source"`
    UserID    string `json:"user_id"`
}

// 响应
type DictResponse struct {
    Rc   int `json:"rc"`
    Wiki struct {
    } `json:"wiki"`
    Dictionary struct {
       Prons struct {
          EnUs string `json:"en-us"`
          En   string `json:"en"`
       } `json:"prons"`
       Explanations []string      `json:"explanations"`
       Synonym      []string      `json:"synonym"`
       Antonym      []string      `json:"antonym"`
       WqxExample   [][]string    `json:"wqx_example"`
       Entry        string        `json:"entry"`
       Type         string        `json:"type"`
       Related      []interface{} `json:"related"`
       Source       string        `json:"source"`
    } `json:"dictionary"`
}

func main() {
    //检查命令行传入的参数个数（第一个参数是程序名称本身，第二个参数是传入的单词，因此必须是两个参数）
    if len(os.Args) != 2 {
       fmt.Fprintf(os.Stderr, `usage: simpleDict WORD example: simpleDict hello`)
       os.Exit(1)
    }
    //将第二个命令行参数也就是单词传给变量 word
    word := os.Args[1]
    query(word)
}

// 将程序的主要代码封装为query()函数，传入要翻译的单词
func query(word string) {
    client := &http.Client{}
    request := DictRequest{TransType: "en2zh", Source: word}
    buf, err := json.Marshal(request)
    if err != nil {
       log.Fatal(err)
    }
    var data = bytes.NewReader(buf)

    req, err := http.NewRequest("POST", "https://api.interpreter.caiyunai.com/v1/dict", data)
    if err != nil {
       log.Fatal(err)
    }
    req.Header.Set("authority", "api.interpreter.caiyunai.com")
    req.Header.Set("accept", "application/json, text/plain, */*")
    req.Header.Set("accept-language", "zh-CN,zh;q=0.9")
    req.Header.Set("app-name", "xy")
    req.Header.Set("cache-control", "no-cache")
    req.Header.Set("content-type", "application/json;charset=UTF-8")
    req.Header.Set("device-id", "b72e9f6cbb97432941b8adf317a17dee")
    req.Header.Set("origin", "https://fanyi.caiyunapp.com")
    req.Header.Set("os-type", "web")
    req.Header.Set("os-version", "")
    req.Header.Set("pragma", "no-cache")
    req.Header.Set("referer", "https://fanyi.caiyunapp.com/")
    req.Header.Set("sec-ch-ua", `"Not/A)Brand";v="99", "Google Chrome";v="115", "Chromium";v="115"`)
    req.Header.Set("sec-ch-ua-mobile", "?0")
    req.Header.Set("sec-ch-ua-platform", `"Windows"`)
    req.Header.Set("sec-fetch-dest", "empty")
    req.Header.Set("sec-fetch-mode", "cors")
    req.Header.Set("sec-fetch-site", "cross-site")
    req.Header.Set("user-agent", "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/115.0.0.0 Safari/537.36")
    req.Header.Set("x-authorization", "token:qgemv4jr1y38jyq6vhvi")
    resp, err := client.Do(req)
    if err != nil {
       log.Fatal(err)
    }
    defer resp.Body.Close()
    bodyText, err := io.ReadAll(resp.Body)
    if err != nil {
       log.Fatal(err)
    }

    //防御性编程
    if resp.StatusCode != 200 {
       log.Fatal("bad StatusCode:", resp.StatusCode, "body:", string(bodyText))
    }
    var dictResponse DictResponse
    err = json.Unmarshal(bodyText, &dictResponse)
    if err != nil {
       log.Fatal(err)
    }

    //fmt.Println("%#v\n", dictResponse)
    fmt.Println(word, "UK:", dictResponse.Dictionary.Prons.En,
       "US:", dictResponse.Dictionary.Prons.EnUs)
    for _, item := range dictResponse.Dictionary.Explanations {
       fmt.Println(item)
    }
}
```

在命令行运行：输入要翻译的单词，可以调用接口实现在线翻译

![img](https://pqmo9n8d6gt.feishu.cn/space/api/box/stream/download/asynccode/?code=Mzk0NjE1NGU0NjIyZGE1YjQ2OGNjMzBlZjUyMjQ0ZmNfYUplOVpvUk1PZW5HcUhqVTZ0Wll0TjlHR3lQdzh4UDdfVG9rZW46TnNOS2JoNlp3b0t3S1p4VTkxZGNESk40bnhCXzE2OTE4Mzk3NDM6MTY5MTg0MzM0M19WNA)