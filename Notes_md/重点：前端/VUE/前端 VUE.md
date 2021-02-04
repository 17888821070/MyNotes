# 前端 VUE.js

## 安装 

* 方式1:链接下载本地版本 https://vuejs.org/js/vue.min.js

* 方式二：添加script 引用

  ```html
  <script src="https://vuejs.org/js/vue.min.js"></script>
  
  //使用Staticfile CDN（国内）
  <script src="https://cdn.staticfile.org/vue/2.2.2/vue.min.js"></script>
  
  //使用unpkg（推荐）
  <script src="https://unpkg.com/vue/dist/vue.js"></script>
  
  //cdnjs
  <script src="https://cdnjs.cloudflare.com/ajax/libs/vue/2.1.8/vue.min.js"></script>
  ```

* 方式三：NPM

  * 安装node.js 

    ```
    brew install node 或者使用安装包
    ```

  * 切换node.js镜像

    ```
    npm config set registry http://registry.npm.taobao.org
    ```

  * 使用npm安装vue

    * npm install vue

## 创建新项目

https://segmentfault.com/a/1190000007441374

## 入门

* <!--vue是js的封装，所以vue代码要写在script内-->

``` html
<!--vue是js的封装，所以vue代码要写在script内-->
<script type="text/javascript">
		var vm = new Vue({
			//选项
		})
	</script>
```

* vue使用时要通过实例化vue

```html
<script type="text/javascript">
		var vm = new Vue({
			//选项
		})
</script>
```

* 初始化vue时的元素

  ```html
  <div id="vue_det">
      <h1>site : {{site}}</h1>
      <h1>url : {{url}}</h1>
      <h1>{{details()}}</h1>
  </div>
  <script type="text/javascript">
      var vm = new Vue({
          el: '#vue_det',
          data: {
              site: "菜鸟教程",
              url: "www.runoob.com",
              alexa: "10000"
          },
          methods: {
              details: function() {
                  return  this.site + " - 学的不仅是技术，更是梦想！";
              }
          }
      })
  </script>
  ```

  * el参数绑定元素

    * el:'#xxid'

    * 是html元素的id

  * data参数定义属性

    * key-value形式
    * data: { 属性名:"属性值", 属性名:属性值}   ——data: { name:"lizixing", age:18}
    * 可以在html元素中使用{{属性名}}获取值

  * methods参数定义函数

    * methods: { 函数名: function(){ xxx; return xxx;}}

  * {{}}用于输出vue对象属性或函数返回值

    * ```html
      <h1>url : {{url}}</h1>
      <h1>{{details()}}</h1>
      ```

## 模版语法 new vue()

* v-html

  * 相当于{{}}
  * v-html=“属性名”

  ```html
  <div v-html="name"/>
  ```

* v-bind:属性名==>缩写:

  * 绑定属性

  ```html
  <div v-bind:id="aaa"/>  ===  <div :id="aaa"/>  
  <div v-bind:color="red">  ===  <div :color="red"/>  
  <div v-bind:href="www.baidu.com">  ===  <div :href="www.baidu.com"/>  
  ```

* v-if

  * true显示，false隐藏

  ```html
  <div v-if="true">显示</div>
  <div v-if="false">隐藏</div>
  ```

* v-on:响应事件==>缩写@

  * 绑定事件函数

  ```html
  <a v-on:click="dosth">  ===  <a @click="dosth">  
  ```

* v-model

  * 用于指定input select textarea等元素与数据的双向绑定

  ```html
  <input v-model="password">
  ```

## 条件语句v-if

* v-if ，v-else ，v-else-if

  * 可在元素和<template>中使用v-if

  ```html
  <p v-if="true">显示</p>
  <div v-if="type === 'A'">
    A
  </div>
  <div v-else-if="type === 'B'">
    B
  </div>
  <div v-else-if="type === 'C'">
    C
  </div>
  <div v-else>
    Not A/B/C
  </div>
  ```

* v-show

  ```html
  <h1 v-show="true">Hello!</h1>
  ```

## 循环v-for

* v-for

  * 循环元素

  ```html
  <ul>
    <template v-for="site in sites">
      <li>{{ site.name }}</li>
      <li>--------------</li>
    </template>
  </ul>
  ```

  * 迭代对象

  ```html
  <ul>
    <li v-for="value in object">
      {{ value }}
    </li>
    <li v-for="(value, key) in object">
      {{ key }} : {{ value }}
    </li>
  </ul>
  ```

  * 迭代整数

  ```html
  <ul>
    <li v-for="n in 10">
      {{ n }}
    </li>
  </ul>
  ```

  

## 计算属性computed

* computed: 	

  ```html
  <script type="text/javascript">
      var vm = new Vue({
          el: '#vue_det',
          data: {
              site: "菜鸟教程",
              url: "www.runoob.com",
              alexa: "10000"
          },
          methods: {
              details: function() {
                  return  this.site + " - 学的不仅是技术，更是梦想！";
              }
          }
        	computed: {
              // 计算属性的 getter
              reversedMessage: function () {
                  // `this` 指向 vm 实例
                  return this.message.split('').reverse().join('')
              }
          }
      })
  </script>
  ```

  * computed vs methods

  我们可以使用 methods 来替代 computed，效果上两个都是一样的，但是 computed 是基于它的依赖缓存，只有相关依赖发生改变时才会重新取值。而使用 methods ，在重新渲染的时候，函数总会重新调用执行。

## 监听属性watch

* watch:
  * 通过 watch 来响应数据的变化。

```html
<!DOCTYPE html>
<html>
<head>
	<meta charset="utf-8">
	<title>Vue 测试实例 - 菜鸟教程(runoob.com)</title>
	<script src="https://cdn.staticfile.org/vue/2.4.2/vue.min.js"></script>
</head>
   <body>
      <div id = "computed_props">
         千米 : <input type = "text" v-model = "kilometers">
         米 : <input type = "text" v-model = "meters">
      </div>
	   <p id="info"></p>
      <script type = "text/javascript">
         var vm = new Vue({
            el: '#computed_props',
            data: {
               kilometers : 0,
               meters:0
            },
            methods: {
            },
            computed :{
            },
            watch : {
               kilometers:function(val) {
                  this.kilometers = val;
                  this.meters = this.kilometers * 1000
               },
               meters : function (val) {
                  this.kilometers = val/ 1000;
                  this.meters = val;
               }
            }
         });
         // $watch 是一个实例方法
		vm.$watch('kilometers', function (newValue, oldValue) {
			// 这个回调将在 vm.kilometers 改变后调用
		    document.getElementById ("info").innerHTML = "修改前值为: " + oldValue + "，修改后值为: " + newValue;
		})
      </script>
   </body>
</html>
```

## 样式绑定

https://www.runoob.com/vue2/vue-class-style.html

## 事件处理器

https://www.runoob.com/vue2/vue-events.html

## 表单

https://www.runoob.com/vue2/vue-forms.html

* radio 示例
* checkbox 示例
* select option 示例
* 约束输入类型
* 去掉前后空格等

## 组件

components 引入其他自组件

pops：定义组件使用时可传入的参数

```html
// 注册名为child的组件，指定可接受参数massage
Vue.component('child', {
  // 声明 props
  props: ['message'],
  // 同样也可以在 vm 实例中像 “this.message” 这样使用
  template: '<span>{{ message }}</span>'
})


//使用child组件，并传入参数
<div id="app">
	<child message="hello!"></child>
</div>
```

* 使用组件时可以用 :参数名=“值”，为组件动态传入数据

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="utf-8">
<title>Vue 测试实例 - 菜鸟教程(runoob.com)</title>
<script src="https://cdn.staticfile.org/vue/2.2.2/vue.min.js"></script>
</head>
<body>
<div id="app">
	<div>
	  <input v-model="parentMsg">
	  <br>
	  <child :message="parentMsg"></child>
	</div>
</div>

<script>
// 注册
Vue.component('child', {
  // 声明 props
  props: ['message'],
  // 同样也可以在 vm 实例中像 “this.message” 这样使用
  template: '<span>{{ message }}</span>'
})
// 创建根实例
new Vue({
  el: '#app',
  data: {
	parentMsg: '父组件内容'
  }
})
</script>
</body>
</html>
```

## ajax 

https://www.runoob.com/vue2/vuejs-ajax-axios.html

### GET 方法

* 普通的读json response

```html
mounted () {
    axios
      .get('https://www.runoob.com/try/ajax/json_demo.json')
      .then(response => (this.info = response))
      .catch(function (error) { // 请求失败处理
        console.log(error);
      });
  }
```

* 通过response.data读取返回数据 response.data.xxx

```html
  mounted () {
    axios
      .get('https://www.runoob.com/try/ajax/json_demo.json')
      .then(response => (this.info = response.data.sites))
      .catch(function (error) { // 请求失败处理
        console.log(error);
      });
  }
```

* 参数传递

```html
// 直接在 URL 上添加参数 ID=12345
axios.get('/user?ID=12345')
  .then(function (response) {
    console.log(response);
  })
  .catch(function (error) {
    console.log(error);
  });
 
// 也可以通过 params 设置参数：
axios.get('/user', {
    params: {
      ID: 12345
    }
  })
  .then(function (response) {
    console.log(response);
  })
  .catch(function (error) {
    console.log(error);
  });
```

### POST方法

* 直接读response

```html
  mounted () {
    axios
      .post('https://www.runoob.com/try/ajax/demo_axios_post.php')
      .then(response => (this.info = response))
      .catch(function (error) { // 请求失败处理
        console.log(error);
      });
  }
```