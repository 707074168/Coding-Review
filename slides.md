---
# try also 'default' to start simple
theme: seriph
# random image from a curated Unsplash collection by Anthony
# like them? see https://unsplash.com/collections/94734566/slidev
background: https://source.unsplash.com/collection/94734566/1920x1080
# apply any windi css classes to the current slide
class: 'text-center'
# https://sli.dev/custom/highlighters.html
highlighter: shiki
# show line numbers in code blocks
lineNumbers: false
# some information about the slides, markdown enabled
info: |
  ## Slidev Starter Template
  Presentation slides for developers.

  Learn more at [Sli.dev](https://sli.dev)
# persist drawings in exports and build
drawings:
  persist: false
# use UnoCSS
css: unocss
---

# 代码Coding


<div class="pt-12">
  <span @click="$slidev.nav.next" class="px-2 py-1 rounded cursor-pointer" hover="bg-white bg-opacity-10"> <carbon:arrow-right class="inline"/>
  </span>
</div>


<!--
The last comment block of each slide will be treated as slide notes. It will be visible and editable in Presenter Mode along with the slide. [Read more in the docs](https://sli.dev/guide/syntax.html#notes)
-->

---

## 命名规范

<br>

> 尽量减少缩写的命名或者拼音的命名 做到见名知意

<br>

* 变量命名
```javascript
1. 布尔值
hasXxx 判断是否含有某个值
isXxx 判断是否为某个值

2. 私有变量属性
前缀可以为_

3. 常量
使用全大写字母

// bad 拼音
let zongshu = 100
// bad 意思不清晰的缩写
let tc = 100
// good
let totalCount = 100
```
<br>
<br>

* 函数命名
```javascript
// 明确函数意图 
onXxx 事件监听回调 
handleXxx 处理事件 
getXxx 获取某个值 
setXxx 设置某个值
shouldXxx/isXxx/canXxx/hasXxx 返回boolean类型

// bad 从名称无法知道函数返回值类型
function showNamesList() {}

// good
function getNamesList() {}
```

<style>
.slidev-layout {
  overflow: scroll
}
</style>

---

## 变量兜底
```javascript
// bad
const { data } = getApiRequest()
data.map((s) => s.id)

// good
const { data = [] } = getApiRequest()
data.map((s) => s?.id)

// good
const { data } = getApiRequest()
(data || []).map((s) => s?.id)
```

---

## 辅助函数必须是纯函数
```javascript
// bad
function plus(a, b, c) {
  let c = fetch('xxx.xxx')
  return a + b + c 
} 

// good
function plus(a, b, c) {
  return a + b + c
}
```


---

## 优先使用函数式编程
```javascript
// bad
let arr = []
for(let i = 0; i < a.length; i++) {
  if(a[i] & 1) {
    arr.push(a[i])
  }
}

// good
let arr = a.filter(num => num & 1)
```

---

## 组件/函数导入导出
```javascript
// 在文件头部导入，顺序依次为: 第三方库 > 公共组件/方法 > 非公共部分组件/方法
import React from 'react'
import _ from 'loadsh'
import Header from '@components/header'
import Content from './Content'

// 在底部导出
export { Content, Header }
export default Component
```
---

## 连环if优化
* 未优化
```javascript
function getColor(code){
  if(code === '200'){
    return 'blue'
  }else if(code === '201'){
    return 'green'
  }else if(code === '202'){
    return 'red'
  }else{
    return 'other'
  }
}
```
<br>

* switch优化 （只优化了代码结构 提高了代码可读性 但不是最优解）
```javascript
function getColor(code){
  switch(code){
    case '200':
      return 'blue'
      break
    case '201':
      return 'green'
      break
    case '202':
      return 'red'
      break
    default:
      return 'other'
      break
  }
}
```
<br>

* 使用映射优化（代码量降低，可读性高）
```javascript
// 使用映射优化 
const colorMap = {
    '200': 'blue',
    '201': 'green',
    '202': 'red',
}
function getColor(code){
  return colorMap[code] || 'other'
}
```

<style>
.slidev-layout {
  overflow: scroll
}
</style>

---

## 合并对象
* 逐一赋值
```javascript
let info = {
  name: 'jack',
  tel: '183xxxxx222'
}
let otherInfo = {
  name: 'jack',
  age: '18',
  gender: 'male'
}
info.age = otherInfo.age
info.gender = otherInfo.gender
```

<br>

* 使用扩展运算符
```javascript
let info = {
  name: 'jack',
  tel: '183xxxxx222'
}
let otherInfo = {
  age: '18',
  gender: 'male'
}
info = {...info, ...otherInfo}
```

<style>
.slidev-layout {
  overflow: scroll
}
</style>

---

## 一些项目中能用到的设计模式
* 策略模式
```javascript
function calculator(type, a, b) {
  const strategy = {
    add: function(a, b) {
      return a + b;
    },
    minus: function(a, b) {
      return a - b;
    },
    division: function(a, b) {
      return a / b;
    },
    times: function(a, b) {
      return a * b;
    }
  }
  
  return strategy[type](a, b);
}

// 使用时
calculator('add', 1, 1);
```
<br>

* 状态模式
```javascript
class Light {
    // 灯的各种状态的属性聚合
    offLight = new OffLightState(this);
    weakLight = new WeakLightState(this);
    strongLight = new StrongLightState(this);
    sleepLight = new SleepLightState(this);

    constructor() {
      // 设置灯打开时的默认,光线状态
      this.currentState = this.weakLight;
    }
    // 修改灯的光线状态
    setState(state) {
        this.currentState = state;
    }
}

class WeakLightState {
    light = null;
    stateName = '柔光状态';

    constructor(light) {
        this.light = light;
    }

    // 柔光灯光按下之后,将状态切换为强光
    pressed() {
        const strongLight = this.light.strongLight;
        // 设置下一次灯要显示的光线
        this.light.setState(strongLight);
    }
}
class OffLightState {}
class StrongLightState {}
class SleepLightState {}

```
<br>

* 享元模式
```javascript
class Gender {
  idx = ''
  sex = ''

  constructor(sex, idx = null) {
    this.idx = idx;
    this.sex = sex
  }

  takePhoto() {
    console.log(this.idx, this.sex)
  }
}
  // 未使用享元模式 创建多个对象 造成性能损耗 浪费内存
  for(let i = 0; i < 20 ; i++){
    new Gender('男', i).takePhoto();
  }
  for(let i = 0; i < 20 ; i++){
    new Gender('女', i).takePhoto();
  }

  // 使用享元 只创建了两个对象 提高了性能
  let ModelF = new Gender('女');
  let ModelM = new Gender('男');

  for(let i = 0; i < 20 ; i++) {
    ModelF.idx = i;
    ModelF.takePhoto();
  }
  for(let i = 0; i < 20 ; i++) {
    ModelM.idx = i;
    ModelM.takePhoto();
  }
```

<style>
.slidev-layout {
  overflow: scroll
}
</style>
