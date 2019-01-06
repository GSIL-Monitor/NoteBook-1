## JSON

以下内容随便写的：

```json
myObj = {
    "name":"网站",
    "num":3,
    "sites": [
        { "name":"Google", "info":[ "Android", "Google 搜索", "Google 翻译" ] },
        { "name":"Runoob", "info":[ "菜鸟教程", "菜鸟工具", "菜鸟微信" ] },
        { "name":"Taobao", "info":[ "淘宝", "网购" ] }
    ]
}
```

```json
myObj.sites[1] = { "name":"Google", "info":[ "Android", "Google 搜索", "Google 翻译" ] 
```

### 通过json数据创建JavaScript数据：

```json
var obj = JSON.parse('{ "name":"runoob", "alexa":10000, "site":"www.runoob.com" }');
```

# 