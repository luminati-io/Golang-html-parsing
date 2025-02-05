# How to Parse HTML With Golang?

[![Promo](https://github.com/luminati-io/LinkedIn-Scraper/raw/main/Proxies%20and%20scrapers%20GitHub%20bonus%20banner.png)](https://brightdata.com/) 

Master HTML parsing in Go using [Node Parser](https://www.npmjs.com/package/node-html-parser), [Tokenizer](https://github.com/greim/html-tokenizer), and third-party tools like Goquery, Colly, and [Bright Data's Web Scrapers](https://brightdata.com/products/web-scraper) for efficient [web scraping](https://github.com/luminati-io/Awesome-Web-Scraping).

## Prerequisites

Before you begin, it's helpful to have a basic understanding of Go (Golang) and web scraping concepts. Make sure Go is installed on your machine. Then, create a new project folder and initialize it with the following commands:

```bash
mkdir goparser
cd goparser
go mod init goparser
```

Test your setup with:

```go
package main

import "fmt"

func main() {
    fmt.Println("Hello, World!")
}
```

Run the file:

```bash
go run main.go
```

Install the dependency:

```bash
go get golang.org/x/net/html
```

## Extracting Data With Node Parser

The Node Parser allows you to traverse the DOM (Document Object Model) of an HTML page. This is useful for extracting specific elements, such as quotes and authors. Here's an example of how to use the Node Parser to achieve this:

```go
package main

import (
    "fmt"
    "net/http"
    "golang.org/x/net/html"
)

func main() {
    resp, _ := http.Get("http://quotes.toscrape.com")
    defer resp.Body.Close()
    doc, _ := html.Parse(resp.Body)

    var processNode func(*html.Node)
    processNode = func(n *html.Node) {
        if n.Type == html.ElementNode && n.Data == "span" {
            for _, a := range n.Attr {
                if a.Key == "class" && a.Val == "text" {
                    fmt.Println("Quote:", n.FirstChild.Data)
                }
            }
        }
        if n.Type == html.ElementNode && n.Data == "small" {
            for _, a := range n.Attr {
                if a.Key == "class" && a.Val == "author" {
                    fmt.Println("Author:", n.FirstChild.Data)
                }
            }
        }
        for c := n.FirstChild; c != nil; c = c.NextSibling {
            processNode(c)
        }
    }
    processNode(doc)
}
```

## Extracting Data With Tokenizer

The Tokenizer processes HTML pages as a stream of tokens, which represent individual components of the HTML (e.g., tags, attributes, and text). This approach is more memory-efficient for large pages but requires more manual handling. Here's an example of how to use the Tokenizer to extract quotes and authors:

```go
package main

import (
    "fmt"
    "net/http"
    "strings"
    "golang.org/x/net/html"
)

func main() {
    resp, _ := http.Get("http://quotes.toscrape.com")
    defer resp.Body.Close()
    tokenizer := html.NewTokenizer(resp.Body)

    inQuote := false
    inAuthor := false

    for {
        tt := tokenizer.Next()
        switch tt {
        case html.ErrorToken:
            return
        case html.StartTagToken:
            t := tokenizer.Token()
            if t.Data == "span" {
                for _, a := range t.Attr {
                    if a.Key == "class" && a.Val == "text" {
                        inQuote = true
                    }
                }
            }
            if t.Data == "small" {
                for _, a := range t.Attr {
                    if a.Key == "class" && a.Val == "author" {
                        inAuthor = true
                    }
                }
            }
        case html.TextToken:
            if inQuote {
                fmt.Println("Quote:", strings.TrimSpace(tokenizer.Token().Data))
                inQuote = false
            }
            if inAuthor {
                fmt.Println("Author:", strings.TrimSpace(tokenizer.Token().Data))
                inAuthor = false
            }
        }
    }
}
```

## Third Party Alternatives

- **Goquery**: A Go alternative to jQuery, supports DOM traversal and CSS selectors.
- **htmlquery**: Similar to Goquery but uses XPath selectors.
- **Colly**: A full-fledged web scraping framework for Go.
- **Bright Data Web Scraper**: An API service for scraping pages and returning data in JSON format.

## Conclusion

Now you know how to parse HTML using Go. Use Node Parser for full-page traversal and Tokenizer for parsing relevant data. Explore third-party tools for more features. Go over some of our other scraping guides:

- [**Amazon**](https://github.com/luminati-io/LinkedIn-Scraper)
- [**LinkedIn**](https://github.com/luminati-io/LinkedIn-Scraper)
- [**Google Maps**](https://github.com/luminati-io/Google-Maps-Scraper)
- [**Google News**](https://github.com/luminati-io/Google-News-Scraper)
