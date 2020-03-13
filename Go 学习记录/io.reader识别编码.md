

# io.Reader下的识别编码和转码



## 利用`x/text`手动进行字符转码

* 安装/ 导入`x/text`

* 通过原来的 Reader 生成新的 Reader

* ```go
  // 把 GBK 转为 UTF8
  utf8Reader := transform.NewReader(resp.Body, simpllifiedchinese.GBK.NewDeCoder())
  // 读取内容
  all, err := ioutil.ReadAll(utf8Reader)
  ```

* 



## 利用x/net/html自动识别编码进行转码

* 方法名: charset.DetermineEncoding(content []type, contentType string) (e encoding.Encoding, name string, 	certain bool)

* ```go
  // 自动识别原编码 转为 UTF8
  utf8Reader := transform.NewReader(resp.Body, determineEncoding(resp.Body).NewDeCoder())
  // 读取内容
  all, err := ioutil.ReadAll(utf8Reader)
  
  // 自动识别原编码, 并返回 encoding.Encoding
  func determineEncoding(r io.Reader) encoding.Encoding {
    // 由于如果直接读取 io.Reader 中的数据, 会造成读出来(重点), 所以需要缓存
    bytes, err := bufio.NewReader(r).Peek(1024)
    if err != nil {
      panic(err)
    }
    e, _, _ = charset.DetermineEncoding(bytes, "")
    return e
  }
  ```

* 