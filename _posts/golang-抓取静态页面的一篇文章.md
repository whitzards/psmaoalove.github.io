> 首先我先声明并无恶意，仅因为学习使用
```
package main

import (
	"fmt"
	"log"
	"github.com/PuerkitoBio/goquery"
)

func GetJokes() {
	doc, err := goquery.NewDocument("https://www.saikr.com/a/2669")
	if err != nil {
		log.Fatal(err)
	}

	dtitle := doc.Find("title").Text()
	fmt.Println("标题:" + dtitle)
	keywords, _ := doc.Find("meta[name=keywords]").Attr("content")
	fmt.Println("关键字:" + keywords)
	description, _ := doc.Find("meta[name=description]").Attr("content")
	fmt.Println("文章描述内容:" + description)

	doc.Find(".sk-circle4-1-para-box").Find("div.para").Each(func(index int, sel *goquery.Selection) {
		fmt.Println(sel.Text())
	})

}
func main() {
	GetJokes()
}

```
