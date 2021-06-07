# Module模块

模块是一个个局部作用域中的代码块。模块系统能够管理多个模块使之能够协同工作。

模块系统解决的主要问题：

* 模块化问题
* 消除全局变量
* 管理加载顺序

## ES6模块系统

es6新增模块系统用于管理多个模块，如果html中需要加载模块，那么必须在script标签上添加type="module"属性。

一个模块如果没有导出，可以通过`import "./demo.js"`语法将其导入，被导入的代码会执行且仅执行一遍。

导出的内容可以是常量、函数、文件、模块等，只要是js中能够作为参数传递的数据类型都可以。

### default方式

default方式是指通过`export default`导出数据类型，一个模块只能有一个默认导出。

导出文件<airplane.js>

```javascript
function meetsSpeedRangeRequirements(maxSpeed, minSpeed,requiredSpeedRange){
  const range = maxSpeed - minSpeed;
  if (range>requiredSpeedRange){
    return true;
  }
  else return false;
}
export default meetsSpeedRangeRequirements;
```

导入时，标识符命名不要求与导出模块中相同。

```javascript
import meetsSpeedRangeRequirements from './airplane.js';
```

### Named方式

#### 基本用法

* 一次性导出或导入

  导出文件<airplane.js>

  ```javascript
  let availableAirplanes=[
    {name:'AeroJet',
    fuelCapacity:800,
    maxSpeed:1200,
    minSpeed:300,
    availableStaff:['pilots', 'flightAttendants',    'engineers', 'medicalAssistance','sensorOperators']
    },
    {name:'SkyJet',
    fuelCapacity:500,
    maxSpeed:800,
    minSpeed:200,
    availableStaff:[
    'pilots','flightAttendants'
  ]}];
  let flightRequirements = {
  };
  function meetsSpeedRangeRequirements(maxSpeed, minSpeed,requiredSpeedRange){
  }
  function meetsStaffRequirements(availableStaff,requiredStaff){
  }
  export {availableAirplanes, flightRequirements,meetsStaffRequirements};
  ```

  导入文件

  ```javascript
  import {availableAirplanes, flightRequirements,meetsStaffRequirements} from './airplane';
  ```

* 逐个导出或导入

  导出文件<airplane.js>

  ```javascript
  export let availableAirplanes=[
    {name:'AeroJet',
    fuelCapacity:800,
    maxSpeed:1200,
    minSpeed:300,
    availableStaff:['pilots', 'flightAttendants',    'engineers', 'medicalAssistance','sensorOperators']
    },
    {name:'SkyJet',
    fuelCapacity:500,
    maxSpeed:800,
    minSpeed:200,
    availableStaff:[
    'pilots','flightAttendants'
  ]}];
  ```

  导入方式同前一个。

#### 起别名

此类导入方式基于named一次性导出或导入方式，通过as关键字给变量起别名。

导出文件<airplane.js>

```javascript
export {availableAirplanes as aircrafts, flightRequirements as flightReqs,meetsStaffRequirements as meetsStaffReqs,meetsSpeedRangeRequirements as meetsSpeedRangeReqs};
```

导入方式

1. 基本导入方式同前一个

2. 起别名导入，参考导出方式

3. 对整个导入模块起别名

   ```javascript
   import * as Carte from './menu';
   Carte.chefsSpecial;
   Carte.isVeg();
   Carte.isLowSodium(); 
   ```

## Node模块系统

此类导入方式借助`module`对象通过`module.exports`赋予js文件中某数据类型能够被导入的权限，通过`require(<filename>)`导入数据类型，这种导入方式主要在Node.js中使用。每个运行在Node上的js文件都存在一个有导出属性的本地`module`对象。`require(<filename>)`赋予的变量名不必须与导出文件中的变量相同，原则上保持一致。

### 基本方式

导出文件 <1-airplane.js>

```javascript
let Airplane = {};
Airplane.myAirplane = "StarJet";
module.exports = Airplane;
```

导入文件与模块文件位于同一目录

```javascript
const Airplane = require('./1-airplane.js');
const displayAirplane=()=>{
  console.log(Airplane);
  console.log(Airplane.myAirplane);
}
displayAirplane();
```

### 匿名方式

此类导入方式依赖了`JavaScript`语言变量是引用的特性，直接在`module.exports`后定义数据类型。

导出文件 <2-airplane.js>

```javascript
module.exports = {
  myAirplane:"CloudJet",
  displayAirplane:function(){
    return this.myAirplane;
  }
};
```

导入方式同上。