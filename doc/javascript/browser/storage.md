# 存储

## Cookie

Cookie 是服务器保存在浏览器的一小段文本信息，一般大小不能超过4KB。浏览器每次向服务器发出请求，就会自动附上这段信息。

HTTP 协议不带有状态，有些请求需要区分状态，就通过 Cookie 附带字符串，让服务器返回不一样的回应。

Cookie 的目的就是区分用户，以及放置状态信息，它的使用场景主要如下。

- 对话（session）管理：保存登录状态、购物车等需要记录的信息。
- 个性化信息：保存用户的偏好，比如网页的字体大小、背景色等等。
- 追踪用户：记录和分析用户行为。

每个 Cookie 都有以下几方面的元数据。

- Cookie 的名字
- Cookie 的值（真正的数据写在这里面）
- 到期时间（超过这个时间会失效）
- 所属域名（默认为当前域名）
- 生效的路径（默认为当前网址）

Cookie 是按照域名区分的，不考虑协议和端口。一级域名不能读取二级域名留下的 Cookie。但是有一个例外，设置 Cookie 的时候（不管是一级域名设置的，还是二级域名设置的），明确将`domain`属性设为一级域名，则这个域名下面的各级域名可以共享这个 Cookie。

### 与HTTP协议

#### 响应：Cookie生成

服务器如果希望在浏览器保存 Cookie，就要在 HTTP 回应的头信息里面，放置一个`Set-Cookie`字段。除了 Cookie 的值，`Set-Cookie`字段还可以附加 Cookie 的属性。

如果服务器想改变一个早先设置的 Cookie，必须同时满足四个条件：Cookie 的`key`、`domain`、`path`和`secure`都匹配。

#### 请求：Cookie发送

浏览器向服务器发送 HTTP 请求时，每个请求`Cookie`字段都会带上相应的 Cookie。`Cookie`字段只携带`key=value`，不携带Cookie的其他属性。

### Cookie属性

#### Expires, Max-Age

`Expires`属性指定一个具体的到期时间，本地到了指定时间以后，浏览器就不再保留这个 Cookie。它的值是 UTC 格式。

如果不设置该属性，或者设为`null`，Cookie 只在当前会话（session）有效，浏览器窗口一旦关闭，当前 Session 结束，该 Cookie 就会被删除。

由于本地时间是不精确的，Expires没有办法保证 Cookie 一定会在服务器指定的时间过期。

`Max-Age`属性优先级更高，指定从现在开始 Cookie 存在的秒数。过了这个时间以后，浏览器就不再保留这个 Cookie。

#### Domain, Path

`Domain`属性指定 Cookie 属于哪个域名，以后浏览器向服务器发送 HTTP 请求时，通过这个属性判断是否要附带某个 Cookie。

服务器设定 Cookie 时，如果没有指定 Domain 属性，浏览器会默认将其设为浏览器的当前域名。如果当前域名是一个 IP 地址，则不得设置 Domain 属性。

如果指定 Domain 属性，需要遵守下面规则：Domain 属性只能是当前域名或者当前域名的上级域名，但设为上级域名时，不能设为顶级域名或公共域名。（顶级域名指的是 .com、.net 这样的域名，公共域名指的是开放给外部用户设置子域名的域名，比如 github.io。）

`Path`属性指定浏览器发出 HTTP 请求时，哪些路径要附带这个 Cookie。

#### Secure, HttpOnly

`Secure`属性指定浏览器只有在加密协议 HTTPS 下，才能将这个 Cookie 发送到服务器。另一方面，如果当前协议是 HTTP，浏览器会自动忽略服务器发来的`Secure`属性。该属性只是一个开关，不需要指定值。如果通信是 HTTPS 协议，该开关自动打开。

`HttpOnly`属性指定该 Cookie 无法通过 JavaScript 脚本拿到，主要是`document.cookie`属性、`XMLHttpRequest`对象和 Request API 都拿不到该属性。这样就防止了该 Cookie 被脚本读到，只有浏览器发出 HTTP 请求时，才会带上该 Cookie。

#### SameSite

Chrome 51 开始，浏览器的 Cookie 新增加了一个`SameSite`属性，用来防止 CSRF 攻击和用户追踪。

`SameSite`属性用来限制第三方 Cookie，从而减少安全风险。它可以设置三个值。

##### Strict

`Strict`最为严格，完全禁止第三方 Cookie，跨站点时，任何情况下都不会发送 Cookie。

##### Lax

| 请求类型  |                 示例                 |    正常情况 | Lax         |
| --------- | :----------------------------------: | ----------: | ----------- |
| 链接      |         `<a href="..."></a>`         | 发送 Cookie | 发送 Cookie |
| 预加载    | `<link rel="prerender" href="..."/>` | 发送 Cookie | 发送 Cookie |
| GET 表单  |  `<form method="GET" action="...">`  | 发送 Cookie | 发送 Cookie |
| POST 表单 | `<form method="POST" action="...">`  | 发送 Cookie | 不发送      |
| iframe    |    `<iframe src="..."></iframe>`     | 发送 Cookie | 不发送      |
| AJAX      |            `$.get("...")`            | 发送 Cookie | 不发送      |
| Image     |          `<img src="...">`           | 发送 Cookie | 不发送      |

##### None

Chrome 计划将`Lax`变为默认设置。这时，网站可以选择显式关闭`SameSite`属性，将其设为`None`。不过，前提是必须同时设置`Secure`属性（Cookie 只能通过 HTTPS 协议发送），否则无效。

### document.cookie

`document.cookie`属性用于读写当前网页的 Cookie。

读取的时候，它会返回当前网页的所有 Cookie，前提是该 Cookie 不能有`HTTPOnly`属性。

`document.cookie`属性是可写的，Cookie 的值必须写成`key=value`的形式。写入 Cookie 的时候，必须对分号、逗号和空格进行转义（它们都不允许作为 Cookie 的值），这可以用`encodeURIComponent`方法达到。

`document.cookie`一次只能写入一个 Cookie，而且写入并不是覆盖，而是添加。

- `path`属性必须为绝对路径，默认为当前路径。
- `domain`属性值必须是当前发送 Cookie 的域名的一部分。
- `max-age`属性的值为秒数。
- `expires`属性的值为 UTC 格式

Cookie 的属性一旦设置完成，就没有办法读取这些属性的值。

删除一个现存 Cookie 的唯一方法，是设置它的`expires`属性为一个过去的日期。

## Storage接口

Storage 接口用于脚本在浏览器保存数据。两个对象部署了这个接口：`window.sessionStorage`和`window.localStorage`。

`sessionStorage`保存的数据用于浏览器的一次会话（session），当会话结束（通常是窗口关闭），数据被清空；`localStorage`保存的数据长期存在，下一次访问该网站的时候，网页可以直接读取以前保存的数据。

保存的数据都以“键值对”的形式存在。也就是说，每一项数据都有一个键名和对应的值。所有的数据都是以文本格式保存。Storage能够使用比Cookie大的存储空间。

### 方法

Storage只有一个length属性，返回保存的数据项个数。

#### setItem()

`Storage.setItem()`方法用于存入数据。它接受两个参数，第一个是键名，第二个是保存的数据。如果键名已经存在，该方法会更新已有的键值。该方法没有返回值。

`Storage.setItem()`两个参数都是字符串。如果不是字符串，会自动转成字符串，再存入浏览器。

#### getItem()

`Storage.getItem()`方法用于读取数据。它只有一个参数，就是键名。如果键名不存在，该方法返回`null`。

#### removeItem()

`Storage.removeItem()`方法用于清除某个键名对应的键值。它接受键名作为参数，如果键名不存在，该方法不会做任何事情。

#### clear()

`Storage.clear()`方法用于清除所有保存的数据。该方法的返回值是`undefined`。

#### key()

`Storage.key()`方法接受一个整数作为参数（从零开始），返回该位置对应的键名。

结合使用`Storage.length`属性和`Storage.key()`方法，可以遍历所有的键。

### storage事件

Storage 接口储存的数据发生变化时，会触发 storage 事件，可以指定这个事件的监听函数。

监听函数接受一个`event`实例对象作为参数。这个实例对象继承了 StorageEvent 接口，有几个特有的属性，都是只读属性。

- `StorageEvent.key`：字符串，表示发生变动的键名。如果 storage 事件是由`clear()`方法引起，该属性返回`null`。
- `StorageEvent.newValue`：字符串，表示新的键值。如果 storage 事件是由`clear()`方法或删除该键值对引发的，该属性返回`null`。
- `StorageEvent.oldValue`：字符串，表示旧的键值。如果该键值对是新增的，该属性返回`null`。
- `StorageEvent.storageArea`：对象，返回键值对所在的整个对象。也说是说，可以从这个属性上面拿到当前域名储存的所有键值对。
- `StorageEvent.url`：字符串，表示原始触发 storage 事件的那个网页的网址。

## IndexedDB

通俗地说，IndexedDB 就是浏览器提供的本地数据库，它可以被网页脚本创建和操作。IndexedDB 允许储存大量数据，提供查找接口，还能建立索引。这些都是 LocalStorage 所不具备的。就数据库类型而言，IndexedDB 不属于关系型数据库（不支持 SQL 查询语句），更接近 NoSQL 数据库。

**特点**

1. 键值对存储，采用对象仓库存放数据，数据以键值对形式存储，键值是唯一的，不能重复
2. 异步，数据库操作是异步的
3. 支持事务
4. 同源限制
5. 存储空间大
6. 支持二进制存储

### 操作流程

#### 打开数据库

打开数据库，使用`indexedDB.open()`方法。

这个方法接受两个参数，第一个参数是字符串，表示数据库的名字。如果指定的数据库不存在，就会新建数据库。第二个参数是整数，表示数据库的版本。如果省略，打开已有数据库时，默认为当前版本；新建数据库时，默认为`1`。

`indexedDB.open()`方法返回一个 IDBRequest 对象。这个对象通过三种事件`error`、`success`、`upgradeneeded`，处理打开数据库的操作结果。

error事件表示打开数据库失败，success事件表示成功打开数据库，upgradeneeded事件表示指定版本号大于实际版本号。

#### 新建数据库

使用`open()`打开数据库时，如果数据库不存在，会触发`upgradeneeded`事件，需要在该事件的监听函数中完成数据库的新建。

```javascript
request.onupgradeneeded = function (event) {
  db = event.target.result;
  var objectStore;
  if (!db.objectStoreNames.contains('person')) {
    objectStore = db.createObjectStore('person', { keyPath: 'id' });
    objectSrote.crateIndex('name', 'name', { unique: false});
  }
}
```

`IDBObject.createIndex()`的三个参数分别为索引名称、索引所在的属性、配置对象（说明该属性是否包含重复的值）。

#### 新增数据

向对象仓库写入数据记录需要通过事务完成。

```javascript
function add() {
  var request = db.transaction(['person'], 'readwrite')
    .objectStore('person')
    .add({ id: 1, name: '张三', age: 24, email: 'zhangsan@example.com' });

  request.onsuccess = function (event) {
    console.log('数据写入成功');
  };

  request.onerror = function (event) {
    console.log('数据写入失败');
  }
}

add();
```

#### 读取数据

`objectStore.get()`方法用于读取数据。

#### 遍历数据

遍历数据仓库的所有记录需要使用指针对象IDBCursor。

```javascript
function readAll() {
  var objectStore = db.transaction('person').objectStore('person');

   objectStore.openCursor().onsuccess = function (event) {
     var cursor = event.target.result;

     if (cursor) {
       console.log('Id: ' + cursor.key);
       console.log('Name: ' + cursor.value.name);
       console.log('Age: ' + cursor.value.age);
       console.log('Email: ' + cursor.value.email);
       cursor.continue();
    } else {
      console.log('没有更多数据了！');
    }
  };
}
```

#### 更新数据

更新数据要使用`IDBObject.put()`方法。

#### 删除数据

`IDBObjectStore.delete()`方法用于删除记录。

#### 使用索引

对某个字段建立索引后，可以通过搜索该字段的值获取数据记录。

使用`IDBObjectStore.index()`获取索引对象，调用索引对象的`get()`方法获取记录。

### indexedDB对象

#### open()

`indexedDB.open()`方法用于打开数据库。这是一个异步操作，会立刻返回一个 IDBOpenDBRequest 对象。

`open()`方法的第一个参数是数据库名称，格式为字符串，不可省略；第二个参数是数据库版本，是一个大于`0`的正整数（`0`将报错），如果该参数大于当前版本，会触发数据库升级。第二个参数可省略，如果数据库已存在，将打开当前版本的数据库；如果数据库不存在，将创建该版本的数据库，默认版本为`1`。

打开数据库是异步操作，通过各种事件通知客户端。下面是有可能触发的4种事件。

- **success**：打开成功。
- **error**：打开失败。
- **upgradeneeded**：第一次打开该数据库，或者数据库版本发生变化。
- **blocked**：上一次的数据库连接还未关闭。

第一次打开数据库时，会先触发`upgradeneeded`事件，然后触发`success`事件。`success`事件发生后，从`IDBOpenDBRequest.result`属性可以拿到已经打开的`IndexedDB`数据库对象。

#### deleteDatabase()

`indexedDB.deleteDatabase()`方法用于删除一个数据库，参数为数据库的名字。它会立刻返回一个`IDBOpenDBRequest`对象，然后对数据库执行异步删除。删除操作的结果会通过事件通知，`IDBOpenDBRequest`对象可以监听以下事件。

- `success`：删除成功
- `error`：删除报错

调用`deleteDatabase()`方法以后，当前数据库的其他已经打开的连接都会接收到`versionchange`事件。

#### cmp()

`indexedDB.cmp()`方法比较两个值是否为 indexedDB 的相同的主键。它返回一个整数，表示比较的结果：`0`表示相同，`1`表示第一个主键大于第二个主键，`-1`表示第一个主键小于第二个主键。

### IDBRequest对象

IDBRequest 对象表示打开的数据库连接，`indexedDB.open()`方法和`indexedDB.deleteDatabase()`方法会返回这个对象。数据库的操作都是通过这个对象完成的。

#### 属性

- `IDBRequest.readyState`：等于`pending`表示操作正在进行，等于`done`表示操作正在完成。
- `IDBRequest.result`：返回请求的结果。如果请求失败、结果不可用，读取该属性会报错。
- `IDBRequest.error`：请求失败时，返回错误对象。
- `IDBRequest.source`：返回请求的来源（比如索引对象或 ObjectStore）。
- `IDBRequest.transaction`：返回当前请求正在进行的事务，如果不包含事务，返回`null`。
- `IDBRequest.onsuccess`：指定`success`事件的监听函数。
- `IDBRequest.onerror`：指定`error`事件的监听函数。

IDBOpenDBRequest 对象继承了 IDBRequest 对象，提供了两个额外的事件监听属性。

- `IDBOpenDBRequest.onblocked`：指定`blocked`事件（`upgradeneeded`事件触发时，数据库仍然在使用）的监听函数。
- `IDBOpenDBRequest.onupgradeneeded`：`upgradeneeded`事件的监听函数。

### IDBDatabase对象

打开数据成功以后，可以从`IDBOpenDBRequest`对象的`result`属性上面，拿到一个`IDBDatabase`对象，它表示连接的数据库。后面对数据库的操作，都通过这个对象完成。

#### 属性

- `IDBDatabase.name`：字符串，数据库名称。
- `IDBDatabase.version`：整数，数据库版本。数据库第一次创建时，该属性为空字符串。
- `IDBDatabase.objectStoreNames`：DOMStringList 对象（字符串的集合），包含当前数据的所有 object store 的名字。
- `IDBDatabase.onabort`：指定 abort 事件（事务中止）的监听函数。
- `IDBDatabase.onclose`：指定 close 事件（数据库意外关闭）的监听函数。
- `IDBDatabase.onerror`：指定 error 事件（访问数据库失败）的监听函数。
- `IDBDatabase.onversionchange`：数据库版本变化时触发（发生`upgradeneeded`事件，或调用`indexedDB.deleteDatabase()`）。

可以使用 DOMStringList 对象的`contains`方法，检查数据库是否包含某个对象仓库。

#### 方法

- `IDBDatabase.close()`：关闭数据库连接，实际会等所有事务完成后再关闭。
- `IDBDatabase.deleteObjectStore()`：删除指定的对象仓库。该方法只能在`versionchange`事件监听函数中调用。

##### createObjectStore()

`IDBDatabase.createObjectStore()`,创建存放数据的对象仓库，返回一个 IDBObjectStore 对象。该方法只能在`versionchange`事件监听函数中调用。

`createObjectStore()`第一个参数是数据库名，如果已有则报错，第二个可选参数是配置对象，用来设置对象仓库的属性。

配置对象默认为null可以使用`keyPath`属性指定作为主键的属性名，也可以使用`auoIncrement: true`设置自动增长的主键。

##### transaction()

`IDBDatabase.transaction()`返回一个 IDBTransaction 事务对象。

`transaction()`方法接受两个参数：第一个参数是一个数组，里面是所涉及的对象仓库，通常是只有一个；第二个可选参数是一个表示操作类型的字符串。目前，操作类型只有两种：`readonly`（只读）和`readwrite`（读写），默认为`readonly`模式。

### IDBTransaction对象

IDBTransaction 对象用来异步操作数据库事务，所有的读写操作都要通过这个对象进行。

`IDBDatabase.transaction()`方法返回的就是一个 IDBTransaction 对象。

事务的执行顺序是按照创建的顺序，而不是发出请求的顺序。

#### 属性

- `IDBTransaction.db`：返回当前事务所在的数据库对象 IDBDatabase。
- `IDBTransaction.error`：返回当前事务的错误。如果事务没有结束，或者事务成功结束，或者被手动终止，该方法返回`null`。
- `IDBTransaction.mode`：返回当前事务的模式，默认是`readonly`（只读），另一个值是`readwrite`。
- `IDBTransaction.objectStoreNames`：返回一个类似数组的对象 DOMStringList，成员是当前事务涉及的对象仓库的名字。
- `IDBTransaction.onabort`：指定`abort`事件（事务中断）的监听函数。
- `IDBTransaction.oncomplete`：指定`complete`事件（事务成功）的监听函数。
- `IDBTransaction.onerror`：指定`error`事件（事务失败）的监听函数。

#### 方法

- `IDBTransaction.abort()`：终止当前事务，回滚所有已经进行的变更。
- `IDBTransaction.objectStore(name)`：返回指定名称的对象仓库 IDBObjectStore。

### IDBObjectStore对象

IDBObjectStore 对象对应一个对象仓库（object store）。

IDBDatabase 对象的`transaction()`返回一个事务对象，该对象的`objectStore()`方法返回 IDBObjectStore 对象。

#### 属性

- `IDBObjectStore.indexNames`：返回一个类似数组的对象（DOMStringList），包含了当前对象仓库的所有索引。
- `IDBObjectStore.keyPath`：返回当前对象仓库的主键。
- `IDBObjectStore.name`：返回当前对象仓库的名称。
- `IDBObjectStore.transaction`：返回当前对象仓库所属的事务对象。
- `IDBObjectStore.autoIncrement`：布尔值，表示主键是否会自动递增。

#### 方法

##### add()

`IDBObjectStore.add()`用于向对象仓库添加数据，返回一个 IDBRequest 对象。该方法只用于添加数据，如果主键相同会报错，因此更新数据必须使用`put()`方法。

该方法接受两个参数，第一个参数是键值，第二个参数是主键，该参数可选，如果省略默认为`null`。

##### put()

`IDBObjectStore.put()`方法用于更新某个主键对应的数据记录，如果对应的键值不存在，则插入一条新的记录。该方法返回一个 IDBRequest 对象。

该方法接受两个参数，第一个参数为新数据，第二个参数为主键，该参数可选，且只在自动递增时才有必要提供，因为那时主键不包含在数据值里面。

##### clear()

`IDBObjectStore.clear()`删除当前对象仓库的所有记录。该方法返回一个 IDBRequest 对象。

##### delete()

`IDBObjectStore.delete()`方法用于删除指定主键的记录。该方法接收一个主键值，返回一个 IDBRequest 对象。

##### count()

`IDBObjectStore.count()`方法用于计算记录的数量。该方法返回一个 IDBRequest 对象。

不带参数时，该方法返回当前对象仓库的所有记录数量。如果主键或 IDBKeyRange 对象作为参数，则返回对应的记录数量。

##### getKey()

`IDBObjectStore.getKey()`用于获取主键。该方法返回一个 IDBRequest 对象。

该方法的参数可以是主键值或 IDBKeyRange 对象。

##### get()

`IDBObjectStore.get()`用于获取主键对应的数据记录。该方法返回一个 IDBRequest 对象。

##### getAll()

`DBObjectStore.getAll()`用于获取对象仓库的记录。该方法返回一个 IDBRequest 对象。

```javascript
// 获取所有记录
objectStore.getAll()

// 获取所有符合指定主键或 IDBKeyRange 的记录
objectStore.getAll(query)

// 指定获取记录的数量
objectStore.getAll(query, count)
```

##### getAllKeys()

使用同getAll()方法，返回一个IDBRequest对象。

##### index()

`IDBObjectStore.index()`方法返回指定名称的索引对象 IDBIndex。

##### createIndex()

`IDBObjectStore.createIndex()`方法用于新建当前数据库的一个索引。该方法只能在`VersionChange`监听函数里面调用。

该方法可以接受三个参数。

- indexName：索引名
- keyPath：主键
- objectParameters：配置对象（可选）

第三个参数可以配置以下属性。

- unique：如果设为`true`，将不允许重复的值
- multiEntry：如果设为`true`，对于有多个值的主键数组，每个值将在索引里面新建一个条目，否则主键数组对应一个条目。

##### deleteIndex()

`IDBObjectStore.deleteIndex()`方法用于删除指定的索引。该方法只能在`VersionChange`监听函数里面调用。

##### openCursor()

`IDBObjectStore.openCursor()`用于获取一个指针对象。

`openCursor()`方法的第一个参数是主键值，或者一个 IDBKeyRange 对象。如果指定该参数，将只处理包含指定主键的记录；如果省略，将处理所有的记录。该方法还可以接受第二个参数，表示遍历方向，默认值为`next`，其他可能的值为`prev`、`nextunique`和`prevunique`。后两个值表示如果遇到重复值，会自动跳过。

### IDBIndex对象

IDBIndex 对象代表数据库的索引，通过这个对象可以获取数据库里面的记录。数据记录的主键默认就是带有索引，IDBIndex 对象主要用于通过除主键以外的其他键，建立索引获取对象。

#### 属性

- `IDBIndex.name`：字符串，索引的名称。
- `IDBIndex.objectStore`：索引所在的对象仓库。
- `IDBIndex.keyPath`：索引的主键。
- `IDBIndex.multiEntry`：布尔值，针对`keyPath`为数组的情况，如果设为`true`，创建数组时，每个数组成员都会有一个条目，否则每个数组都只有一个条目。
- `IDBIndex.unique`：布尔值，表示创建索引时是否允许相同的主键。

#### 方法

方法使用同IDBObjectStore对象。

- `IDBIndex.count()`
- `IDBIndex.get(key)`
- `IDBIndex.getKey(key)`
- `IDBIndex.getAll()`
- `IDBIndex.getAllKeys()`
- `IDBIndex.openCursor()`
- `IDBIndex.openKeyCursor()`

### IDBCursor对象

IDBCursor 对象代表指针对象，用来遍历数据仓库（IDBObjectStore）或索引（IDBIndex）的记录。

IDBCursor 对象一般通过`IDBObjectStore.openCursor()`方法获得。

#### 属性

- `IDBCursor.source`：返回正在遍历的对象仓库或索引。
- `IDBCursor.direction`：字符串，表示指针遍历的方向。共有四个可能的值：next（从头开始向后遍历）、nextunique（从头开始向后遍历，重复的值只遍历一次）、prev（从尾部开始向前遍历）、prevunique（从尾部开始向前遍历，重复的值只遍历一次）。该属性通过`IDBObjectStore.openCursor()`方法的第二个参数指定，一旦指定就不能改变了。
- `IDBCursor.key`：返回当前记录的主键。
- `IDBCursor.value`：返回当前记录的数据值。
- `IDBCursor.primaryKey`：返回当前记录的主键。对于数据仓库（objectStore）来说，这个属性等同于 IDBCursor.key；对于索引，IDBCursor.key 返回索引的位置值，该属性返回数据记录的主键。

#### 方法

- `IDBCursor.advance(n)`：指针向前移动 n 个位置。
- `IDBCursor.continue()`：指针向前移动一个位置。它可以接受一个主键作为参数，这时会跳转到这个主键。
- `IDBCursor.continuePrimaryKey()`：该方法需要两个参数，第一个是`key`，第二个是`primaryKey`，将指针移到符合这两个参数的位置。
- `IDBCursor.delete()`：用来删除当前位置的记录，返回一个 IDBRequest 对象。该方法不会改变指针的位置。
- `IDBCursor.update()`：用来更新当前位置的记录，返回一个 IDBRequest 对象。它的参数是要写入数据库的新的值。

### IDBKeyRange对象

IDBKeyRange 对象代表数据仓库（object store）里面的一组主键。根据这组主键，可以获取数据仓库或索引里面的一组记录。

#### 静态方法

有四个静态方法，用来指定主键的范围。

- `IDBKeyRange.lowerBound()`：指定下限。
- `IDBKeyRange.upperBound()`：指定上限。
- `IDBKeyRange.bound()`：同时指定上下限。
- `IDBKeyRange.only()`：指定只包含一个值。

`IDBKeyRange.lowerBound()`、`IDBKeyRange.upperBound()`、`IDBKeyRange.bound()`这三个方法默认包括端点值，可以传入一个布尔值，修改这个属性。

#### 属性

IDBKeyRange 对象有四个只读属性。

- `IDBKeyRange.lower`：返回下限
- `IDBKeyRange.lowerOpen`：布尔值，表示下限是否为开区间（即下限是否排除在范围之外）
- `IDBKeyRange.upper`：返回上限
- `IDBKeyRange.upperOpen`：布尔值，表示上限是否为开区间（即上限是否排除在范围之外）