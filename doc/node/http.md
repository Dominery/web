# Node.js处理HTTP

```javascript
const http = require('http')
const querystring = require('querystring')
const server = http.createServer((req,res) => {
    const method = req.method
    const url = req.url
    const [path,queryString] = url.split('?')
    const query = querystring(queryString)
    if(method==='POST'){
        const contentType = req.headers['content-type']
        let data = ''
        req.on('data',chunk=>{
            data += chunk
        })
        req.on('end',()=>{
            console.log(data)
            res.end()
        })
        return
    }
    if(path==='/user'){
        res.writeHead(200,{
            'Content-type':'application/json'
        })
        res.end(JSON.stringify(query))
    }
});
server.listen(8080);
```

