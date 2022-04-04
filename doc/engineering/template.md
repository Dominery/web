## 模板引擎

art-template是一种模板引擎，用于在js中生成html代码。

### 使用

以下将简要说明art-template的使用。

1. 引入

   可以通过script标签在线引入

   ```html
   <script src="https://unpkg.com/art-template@4.13.2/lib/template-web.js"></script>
   ```

2. 规定模板

   在type为`"text/html"`的script标签中书写模板

   ```html
   <script type="text/html" id="tpl-student">
   	{{each students}}
   	<li>{{$value.name}} {{$value.age}}</li>
   	{{/each}}
   </script>
   ```

3. 获取模板

   art-template提供了一个template函数，返回根据模板生成的字符串，第一个参数是模板的id，第二个参数是传递的数据的对象。

   ```javascript
   const students = [
       {name:Bob,age:10},
       {name:Alice,age:18}
   ];
   template('tpl-students',{
       'students':students
   })
   ```

### 语法

[官方文档](http://aui.github.io/art-template/zh-cn/docs/)中记录了art-template的用法。模板中{{}}中内容称为输出，符合js代码规范。

#### 输出数据

* 数据获取

  1. $data可以获取到template函数的第二个参数，通过属性访问获取到传入的数据
  2. 直接书写数据的属性名获取数据。

* 转义

  如果数据是字符串，那么template函数会根据html规则进行转义。

  如果不希望数据被转义，那么在数据前加@。

#### 条件

```html
{{if age>=18}}
<p>成年</p>
{{else}}
<p>未成年</p>
{{/if}}
```

#### 循环

循环中可以通过\$value获取每个循环时的数据，$index获取循环的索引。

```html
<script type="text/html" id="tpl-student">
	{{each students}}
	<li>{{$value.name}} {{$value.age}}</li>
	{{/each}}
</script>
```

#### 子模板

art-template提供在模板中引入其他模板的功能，称引入的模板为子模板。

```html
<script type="text/html" id="tpl-1">
	{{include 'tp1-2'}}
</script>
<script type="text/html" id="tpl-2">
	<p>子模板</p>
</script>
```

如果一个应用场景是多个地方使用同一个有子模板的模板，但是要求子模板有一些差异，可以使用原始语法实现向子模板传参的功能。

```html
<script type="text/html" id="tpl-3">
	<% include('tpl-4',{page:首页}) %>
</script>
<script type="text/html" id="tpl-4">
	{{page}}
</script>
```

### webpack中使用art-template

1. 初始化项目目录，`npm init`

2. 安装webpack需要的包

   ```
   npm install --save-dev webpack-cli@3.3.12 webpack@4.44.1 art-template-loader@1.4.3  html-webpack-plugin@4.3.0 
   ```

3. 安装art-template包

   ```
   npm install art-template@4.13.2
   ```

4. 修改package.json中scripts内容为`"start":"webpack"`

5. 配置webpack.config.js

6. 依据art-template语法书写各组件

7. 打包`npm start`