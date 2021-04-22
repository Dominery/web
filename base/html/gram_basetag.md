# 语法与基础标签

## HTML骨架

```markup
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>

</body>
</html>
```

| 元素 | 说明 |
| :--- | :--- |
| `<!DOCTYPE html>` | DTD文档类型声明 |
| lang属性 | 设置网页的语言 |
| `<head></head>` | 配置网页信息 |
| `<title></title>` | 设置网页标题 |
| `<meta>` | 设置网页基础配置 |
| `<meta charset="UTF-8">` | 设置页面字符集，html文件必须保存为与字符集相同的编码的格式，否则会出现乱码。 |
| `<meta name="Keywords" content="">` | 设置网页关键字，有助于提升页面自然排名，是seo的重要手段 |
| `<meta name="Description" content="">` | 设置网页描述信息，有助于提升页面自然排名，是seo的重要手段 |
| `<body></body>` | 标签内部书写网页的内容 |

## 基本标签

1. 标题和段落标签
2. div标签

## html特性

html存在空白折叠现象，为了使文字之间有多个空格，需要使用转义字符`&nbsp;`。

> 空白折叠现象：文字与文字之间存在的多个空格、换行会被折叠成一个空格，标签内壁和文字之间的空格会被忽略。

| 转义字符 | 说明 |
| :--- | :--- |
| `&nbsp;` | 空格 |
| `&lt;` | 小于 |
| `&gt;` | 大于 |
| `&copy;` | 版权符号 |

HTML注释：通过`<!-- -->`形式将内容注释，使之不会被浏览器解析。

