---
title: vue使用梳理
date: 2020-10-16 10:29:44
categories: web
tags: 
    - vue
description: 介绍了vue的安装、通过npm创建项目和vue的基础语法。
---

### 安装
1. 开发环境版本
```
<!-- 开发环境版本，包含了有帮助的命令行警告 -->
<script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
```
2. 生产环境版本
```
<!-- 生产环境版本，优化了尺寸和速度 -->
<script src="https://cdn.jsdelivr.net/npm/vue"></script>
```

### npm创建项目
1. 安装vue的命令行工具vue-cli（通过此工具管理vue项目）
```
npm install --global vue-cli
```

2. 创建vue项目
```
vue init webpack my-project
```

3. 给my-project配置vue库
```
cd my-project
npm install
```

4. 运行项目
成功执行以上命令后访问 http://localhost:8080/访问
```
npm run dev
```

### vue基础语法
#### 模板语法
1. mustache语法(双大括号)
```
<h1>hello {{name}} </h1>
<h1>{{name1}} {{name2}} </h1>
<h1>{{name1 + ' ' + name2}} </h1>
<h1>{{name1 * 2}} </h1>
```

2. v-once（静态数据）
```
//当data中数据改变时，元素中的数据不改变
<h1 v-once>hello {{name}} </h1>
```

3. v-html（添加标签）
```
//url为含标签的字符串
<h1 v-html="url">hello</h1>
```

4. v-text（添加文本）
```
//msg为字符串,缺点是会覆盖元素中原有文本 建议用{{}}
<h1 v-text="msg">hello</h1>
```

5. v-pre（禁止解析）
```
//{{name}}将不会被vue解析
<h1 v-pre>hello {{name}} </h1>
```

6. v-cloak（遮挡）
```
//vue还没解析这段代码时，使这段代码不显示，解析完这段代码后会去掉v-cloak，使代码显示
//解决因为卡顿，{{name}}没有及时解析而引起的用户体验问题
<h1 v-cloak>hello {{name}} </h1>
```

7. v-bind（:）（动态绑定属性）
   7.1 简单绑定
```
//v-bind表明src属性是动态绑定的，于是就会在data中寻找对应的值
<img v-bind:src="url">
```
   7.2 对象绑定
```
<div v-bind:class="{active:isActive,text-danger:hasError}"></div>
<button v-on:click="btnClick">按钮</button>
//添加一个对象，当对象中的类名的值为true时，该类绑定到元素上
<script>
  const app=new Vue({
    el:'#app',
    data:{
      isActive:true,
      hasError:false
    },
    methods:{
    	btnClick:function(){
    		this.isActive = !this.isActive
    	}
    }
  })
</script>
```
   7.3 数组绑定
```
<div v-bind:class="[class1,class2]"></div>
//<div v-bind:class="['class1','class2']"></div>
//有''表示为字符串 无''表示变量
<script>
  const app=new Vue({
    el:'#app',
    data:{
    	class1:'aa',
    	class2:'bb'
    }
  })
</script>
```
   7.4 style对象绑定
```
<div v-bind:style="{fontSize:finalSize + 'px', color:finalColor}"></div>
//有''表示为字符串，无''表示变量
<script>
  const app=new Vue({
    el:'#app',
    data:{
 		finalSize：100,
 		finalColor:'red'
 		//data中字符串得加''
    }
  })
</script>
```
   7.5 style数组绑定
```
<div v-bind:style="[baseStyle,base]"></div>
<script>
  const app=new Vue({
    el:'#app',
    data:{
		baseStyle:{fontSize:'20px'},
		base:{backgroundColor:'red'}
    }
  })
</script>
```

8. v-on（@）（监听事件）
   8.1 参数传递
```
//方法没有形参时 加不加()都一样
<button @click="btn1()">按钮</button>
<button @click="btn1">按钮</button>
//方法有形参时 加了(),没有参数 undefined
<button @click="btn2()">按钮</button>
//方法有形参时 不加() 传递浏览器默认事件event
<button @click="btn2">按钮</button>
//方法有两个形参时 不加() 第一个形参为浏览器默认event 第二个为undefined
//方法有两个形参时 加() 不传参 两个都是undefined
<button @click="btn3">按钮</button>
//方法有两个形参时 通过$event获取
<button @click="btn1(12,$event)">按钮</button>

<script src="vue.js"></script>
<script>
  const app=new Vue({
    el:'#app',
    methods:{
        btn1(){
            console.log('btn1');
        },
        btn2(e){
            console.log(e);
        },
        btn3(e,v){
            console.log(e);
            console.log('v:',v);
        }
    }
  })
</script>
```
   8.2 修饰符使用
```
//阻止冒泡''和""都可以
<button @click.stop='btnClick'>按钮</button>
//阻止默认事件
<input tupe="submit" value="提交" @click.prevent="subFunc">
//按键监听enter
<input type="text" @keyup.enter="keyFunc">
//一次回调
<button @click.once="btnFunc">一次使用</button>
```

9. 条件语句（v-if v-else-if v-else）
```
<div id="app">
//v-if 如果为true则显示 为false则不显示
<h1 v-if="isShow"></h1>
//v-if-else 显示true的那个
<h1 v-if="isShow">if显示</h1>
<h1 v-else>else显示</h1>
//v-else-if 多条件不建议使用 太复杂 
<h1 v-if="score>=90">优秀</h1>
<h1 v-else-if="score>=60">良好</h1>
<h1 v-else>差</h1>
</div>
<script src="vue.js"></script>
<script>
  const app=new Vue({
    el:'#app',
    data:{
        isShow:true,
        score:50
    }
  })
</script>
```

10. v-show
```
//当v-if为false时，元素根本不在dom中
//当v-show为false时，给元素添加一个行内样式dipslay:none
//当需要频繁在显示和隐藏之间切换时用v-show
//当只有一次切换时，多用v-if
<h1 v-if="isShow">if显示</h1>
<h1 v-show="isShow">show显示</h1>
```

11. v-for（遍历）
```
<div id="app">
    <ul>
    //只获得value
        <li v-for="item in names">{{item}}</li>
    </ul>
    <ul>
    //获得index和value index在后
        <li v-for="(v,i) in names">{{i+1}} . {{v}}</li>
    </ul>
</div>

<script src="vue.js"></script>
<script>
  const app=new Vue({
    el:'#app',
    data:{
        names:['jack','lucy','candy']
    }
  })
</script>
```

12. v-model（表单绑定-双向）
```
//v-model可作用于 text 和 textarea
//原理： v-bind绑定一个value属性 v-on监听input事件
<input type="text" v-model="message">
//复杂写法
<input type="text" :value="message" @input="change">
//或把event直接写在input里
<input type="text" :value="message" @input="message=$event.target.value">

<script src="vue.js"></script>
<script>
    const app=new Vue({
        el:'#app',
        data:{
            message:"你好"
        },
        methods:{
            change(e){
                this.message = e.target.value;
            }  
        }
    })
</script>
```

13. v-model修饰符
```
//lazy 输入的内容按 回车后才会传回 data
<input type="text" v-model.lazy="message">
//number 没有添加number修饰符 默认是string类型
<input type="number" v-model.number="age">
//trim 去字符串中空格
<input type="text" v-model.trim="name">
```

#### computed与methods
1. 语法
```
略
```
2. computed与methods的区别
```
1、computed中的方法有缓存机制，当多次调用时，不会进行多次计算，而是把第一次的结果进行缓存，当再次调用时，直接调用缓存的结果。
2、methods中的方法会进行多次调用，浪费资源。
```

### es6方法
[es6常用方法](/web/es6常用方法)

PS: 参考
[Vue语法|菜鸟教学](https://www.runoob.com/vue2/vue-template-syntax.html)



