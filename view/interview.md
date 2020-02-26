~~~~javascript
1.路由的懒加载
在访问路由的时候,才去加载对应的组件
https://cn.vuejs.org/v2/guide/components-dynamic-async.html#%E5%BC%82%E6%AD%A5%E7%BB%84%E4%BB%B6
1.1什么是异步组件
将应用分割成小一些的代码块，并且只在需要的时候才从服务器加载一个模块
第一,可以将异步组件定义为返回一个 Promise 的工厂函数
const Foo = () => Promise.resolve({ /* 组件定义对象 */ })
第二，在 Webpack 2 中，我们可以使用动态 import语法来定义代码分块点 (split point)
import('./Foo.vue')
结合这两者，这就是如何定义一个能够被 Webpack 自动代码分割的异步组件。
const Foo = () => import('./Foo.vue')
常用的懒加载方式有两种：即使用vue异步组件 和 ES6中的import

怎么回答:
在你访问那个路由的时候,再去加载对应的模块,也就是说按需加载,有两种方式,第一种是用es6种的import 第二种就是用异步组件加载
~~~~
~~~~
把不同路由对应的组件分割成不同的代码块，然后当路由被访问的时候才加载对应组件，这样就更加高效了。
懒加载 是利用import方法，当你切换到这个路由的时候才对这个文件开始加载执行,类似require，只有调用它才会去加载文件并执行
const Foo = () => import(/* webpackChunkName: "group-foo" */ './Foo.vue')
const Bar = () => import(/* webpackChunkName: "group-foo" */ './Bar.vue')
const Baz = () => import(/* webpackChunkName: "group-foo" */ './Baz.vue')
~~~~
~~~~
2.
作用:iterator接口目的是为所有的数据结构提供一种统一的访问机制,
特点 + 举例子:数据结构只要具有Symbol.iterator属性,那就可以认为他是可遍历的，可以用for of来进行循环 
这些事包含iterator接口的方法array.from map set 
不同点：
for of循环有很多优点，比如不像for...in一样只遍历键名（甚至包括原型链上的键），而且不像foreach不能跳出循环。并且for...of为各种数据结构提供了一个统一的遍历方法

~~~~

~~~~

3.keep-alive的写法
     <keep-alive >
      <router-view v-if="$route.meta.keepAlive"></router-view>
    </keep-alive>
    <router-view v-if="!$route.meta.keepAlive"></router-view>
v-if写的地方还要多注意,如果写在keep-alive上相当于直接销毁
从A页面到b页面 再回到A页面的时候,做一个数据的缓存,这时候不会触发created了,会触发生命周期钩子activated, deactivated 被 keep-alive 缓存的组件停用时调用
~~~~

~~~~
4 for in 是可以循环数组和对象,主要是对key的循环,for of读取键值
~~~~

~~~
5 new Set()和new Map

并不是所有的类似数组的对象都有Iterator接口,一个最简单的方法就是用array,.from将其转成数组
1.对象想要获取到键值,直接用object.keys可以生成一个数组，再用for of进行遍历,或者用generator函数将对象重新包装一下
2.for of对比for in 和foreach,foreach不能return break continue, 而for of可以 for in 只能拿到key值

ES6 提供了 Map 数据结构。它类似于对象，也是键值对的集合，但是“键”的范围不限于字符串，各种类型的值（包括对象）都可以当作键。也就是说，Object 结构提供了“字符串—值”的对应，Map 结构提供了“值—值”的对应

怎么回答:
new Set主要是为了去重,set结构遍历的时候返回的是一个值,可以通过add进行添加数据,for of可以循环
Set的遍历顺序就是插入顺序。这个特性有时非常有用
new map让key不再简单只是字符串,可能是值-值的对应
~~~

~~~~
6 generator是es6提供的一种异步编程解决方案，执行generator函数会返回一个遍历器对象
generator函数调用后并不会执行,返回的也不是一个函数运行结果,而是一个指向内部状态的指针对象
----调用generator函数返回一个遍历起对象,代表generator函数的内部的指针,以后每次调用遍历器对象的next方法,就会返回有着value和done两个属性的对象,value表示的是当前内部状态的值,done属性是一个boolean值,表示是否遍历结束
1.yield只能出现在generator函数里面

怎么回答:
调用generator函数并不会马上执行,而且通过调用next来进行下一步,遇到yield就会暂停,yield只出现在generator函数里面

~~~~

~~~javascript
7 for...of循环可以自动遍历Generator函数生成的Iterator对象,且此时不再调用next方法
        function* fibonacci() {
            let [prev, curr] = [0, 1]
            for (;;) {
                [prev, curr] = [curr, prev + curr]
                yield curr
            }
        }
        for (let n of fibonacci()) {
            if (n > 1000) break;
            console.log(n, 'n');
        }

7.1.原生的js对象没有遍历接口,无法使用for-of循环,通过generator函数为他击伤这个接口就可以了
        function* objEntries(obj) {
            console.log(555, obj);
            let propkeys = Reflect.ownKeys(obj)
            console.log(444, propkeys);
            for (let propkey of propkeys) {
                yield [propkey, obj[propkey]]
            }
        }
        let jane = {first: 'jane', last: 'doe'}
        
        for(let [key, value] of objEntries(jane)) {
            console.log(`${key}:${value}`); 
        }
7.2 
 // 还有一种,将generator函数加到对象的Symbol.iterator属性上
        function* objectEntries() {
            let propKeys = Object.keys(this)
            for (let propKey of propKeys) {
                yield [propKey, this[propKey]]
            }
        } 

        let jane = {
            first: 'jane',
            last: 'doe'
        }
        jane[Symbol.iterator] = objectEntries

        for(let [key, value] of jane) {
            console.log(`${key}: ${value}`);
        }
7.3
我这个函数yield后面的程序是在操作数据，而我们要先请求数据之后才能操作，因为请求数据，有的快有的慢。那为了保证一定能够获取到数据之后再执行，就可以使用我们这个generator函数了
~~~

~~~~
8 除了for of循环,扩展运算符...，解构赋值和array.from 方法内部调用的都是遍历器接口,这意味着,他们都可以将generator函数返回的iterator对方作为参数
~~~~

~~~
9 array.from必须要有length.不然是空数组,对象里面的key必须是数字
~~~

~~~~javascript
10 如果generator函数内部没有部署try...catch代码块,那么throw方法会从被外部的catch抛出
  var g = function* () {
            while (true) {
                yield;
                console.log('内部捕获', e)
            }
        }

        var i = g()
        i.next()

        try {
            i.throw('a')
            i.throw('b')
        } catch (e) {
            console.log('外部捕获', e);
        }
throw方法被捕获以后会附带执行下一条yield表达式,即附带执行一次next方法
~~~~

~~~~javascript
11 generator函数体外抛出的错误可以在函数体内捕获,反过来,generator函数体内抛出的错误也可以被函数体外的catch捕获
        function* foo() {
            var x = yield 3
            var y = x.toUpperCase()
            yield y
        }
        var it = foo()
        it.next()

        try {
            it.next(42)
        } catch(err) {
            console.log(err);
        }
11.1 一旦generator执行过程中抛出错误,就不会再执行下去了,如果此后还调用next方法,会犯坏u 一个value 为undefined和done为true的对象
~~~~

~~~javascript
12 generator 函数里面有一些reture方法,可以返回给定的值,并且终结generator函数的遍历
        function* gen() {
            yield 1;
            yield 2;
            yield 3;
        }
        var g = gen()
        console.log(g.next()); // 1 false
        console.log(g.return(555)); // 555 true
12.1 调用return 方法就开始执行finanlly代码块,然后等到finanlly代码块执行完再执行return方法
12.2   用yield*语句,再generator函数里面执行另一个generator函数
12.3 yield*后面的generator函数(没有return语句时)等同于在generator函数内部部署一个for of循环
~~~

~~~~javascript
13 //如果被代理的generator函数有return语句,那么便可以向代理他的generator函数返回数据
        function *foo() {
            yield 2;
            yield 3;
            return "周欢"
        }

        function *bar() {
            yield 1;
            var v = yield *foo()
            console.log('v: ' + v);
            yield 4
        }
        var it = bar()
        console.log(it.next()); // 1
        console.log(it.next()); // 2
        console.log(it.next()); // 3
        console.log(it.next()); //v 周欢 4
        console.log(it.next()); // undefined
~~~~

~~~~javascript
14怎么用yield取出嵌套数组的所有成员
       var arr = ['a', ['b', 'c'], ['d', 'e']]
        function* iterTree(tree) {
            if (Array.isArray(tree)) {
                for(let i=0; i < tree.length; i ++) {
                    yield* iterTree(tree[i]) //generator函数里面输出函数 必须用yield*
                }
            } else {
                yield tree
            }
        }

        for(let x of iterTree(arr)) {
            console.log(x);
        }
~~~~

~~~javascript
15 // genrrator函数g返回的遍历器obj是g的实例,而且继承了g.prototype,
       
        function* g() {

        }
        g.prototype.hello = function () {
            return 'hi'
        }
        let obj = g()
        console.log(obj instanceof g); // true
	//但是如果g当作是普通的构造函数,则不会生效,因为g总是返回遍历器对象,而不是this
        function* g() {
            this.a = 11
        }
        let text = g()
        console.log(text.a, 666); // undefined
15.1 generator函数不能和new一起使用,否则会报错
        
~~~

~~~javascript
16 让generator函数返回一个正常的对象实例,既可以用next方法,又可以获取正常的this
 // ajax获取数据
        function* main() {
            var result = yield request('https://www.baidu.com')
            var resp = JSON.parse(result)
            console.log(resp.value);  
        }
        function request(url) {
           makeAjaxCall(url, function(response){
               it.next(response)
           })
        }
        var it = main()
        it.next()

~~~

~~~~
17generator读取文件
~~~~

~~~javascript
18 vue-router
18.1
当你要把 Vue Router 添加进来，我们需要做的是，将组件 (components) 映射到路由 (routes)，然后告诉 Vue Router 在哪里渲染它们
18.2这种跳转是组件间的跳转,必须要写到路由里面,路由的跳转来切换组件
    <div>
        <router-link to="/router2">我是测试1</router-link>
    </div>
18.3$route对象 
表示当前激活的路由的状态信息，包含了当前 URL 解析得到的信息，还有 URL 匹配到的 route records（路由记录）。
  1.$route.path**
      字符串，对应当前路由的路径，总是解析为绝对路径，如 "/foo/bar"。
  2.$route.params**
      一个 key/value 对象，包含了 动态片段 和 全匹配片段，如果没有路由参数，就是一个空对象。
  3.$route.query**
      一个 key/value 对象，表示 URL 查询参数。例如，对于路径 /foo?user=1，则有 $route.query.user == 1，如果没有查询参数，则是个空对象。
  4.route.hash
      当前路由的 hash 值 (不带 #) ，如果没有 hash 值，则为空字符串。锚点
 5$route.fullPath
      完成解析后的 URL，包含查询参数和 hash 的完整路径。
6.$route.matched
      数组，包含当前匹配的路径中所包含的所有片段所对应的配置参数对象。
 7$route.name    当前路径名字
 8$route.meta  路由元信息
 ---------------------------------------
 导航触发后的流程
      “导航”表示路由正在发生改变。
			导航被触发。
			在失活的组件里调用离开守卫。
			调用全局的 beforeEach 守卫。
			在重用的组件里调用 beforeRouteUpdate 守卫 (2.2+)。
			在路由配置里调用 beforeEnter。
			解析异步路由组件。
			在被激活的组件里调用 beforeRouteEnter。
			调用全局的 beforeResolve 守卫 (2.5+)。
			导航被确认。
			调用全局的 afterEach 钩子。
			触发 DOM 更新。
			用创建好的实例调用 beforeRouteEnter 守卫中传给 next 的回调函数。
怎么回答:
组件:vue-router有三个组件router-link进行跳转, keep-alive进行缓存,route-view做组件的嵌套
嵌套路由:在一个路由里面是配置子路由,在必要的时候需要显示不用的子路由,保持父级页面不变,并且页面是不切换的
路由组件传参数---直接设置props为true就是
导航守卫: 调用全局的 beforeEach 守卫---判断登陆什么的,设置白名单, 在单个路由配置调用使用beforeEnter,在激活的组件里面使用在被激活的组件里调用 beforeRouteEnter
beforeRouteLeave (to, from, next) {
  const answer = window.confirm('Do you really want to leave? you have unsaved changes!')
  if (answer) {
    next()
  } else {
    next(false)
  }
}

一、hash

1.  原理：监听onhashchange事件
2. 当url发生变化时，浏览器会记录下来，因此前进后退按钮都可以使用。需要注意的是hash模式下修改的是#后面的内容，使用url的hash来模拟一个完整的url，所以当url改变时，页面不会重新被加载
3. 特点： 能用来构建SPA（单页面引用），改变视图的同事不会向后端发送请求
4. 缺点：url比较丑，如果传复杂的数据，会有体积的限制
5. 能兼容到IE8

二、history
1. 原理：监听onpopstate事件，pushState添加一条新的历史记录，replaceState则会替换当前的历史记录
2. 使用history模式，需要后台的配置支持，因为应用是个单页客户端应用，如果后台没有正确的配置，当用户在浏览器直接访问http://oursite.com/user/id 就会返回404
3. 缺点： history模式中可以实现前进、后退以及跳转，但是刷新的时候是去请求服务器的，如果服务器中没有相应的响应或者资源，就会出现404
4. 只能兼容到IE10
~~~

~~~
18  app.vue 和index.html是靠什么联系起来的
打包后的js 会引入到html里面执行挂载更新替换,等等---最基本的前端三件套啊html，css，js  打包后的js就被引入到index.html里面去啊--所有的操作都在js里面完成了
~~~

~~~javascript
19
 {
        path: "",
        component: Layout,
        redirect: "dashboard", //如果是空的话重定向就是某一个
        children: [{
            path: "dashboard",
            component: _import("dashboard/index"),
            name: "Dashboard",
            meta: {
                title: "首页",
                icon: "icon-shouye",
                noCache: true
            }
        }]
    },
    
~~~

~~~javascript
20
const router = new VueRouter({
  routes: [
    { path: '/user/:id', component: User,
      children: [
        {
          // 当 /user/:id/profile 匹配成功，
          // UserProfile 会被渲染在 User 的 <router-view> 中
          path: 'profile',
          component: UserProfile
        },
        {
          // 当 /user/:id/posts 匹配成功
          // UserPosts 会被渲染在 User 的 <router-view> 中
          path: 'posts',
          component: UserPosts
        }
      ]
    }
  ]
})
// 当你访问 /user/foo 时，User 的出口是不会渲染任何东西，这是因为没有匹配到合适的子路由
 children: [
        // 当 /user/:id 匹配成功，
        // UserHome 会被渲染在 User 的 <router-view> 中
        { path: '', component: UserHome },

        // ...其他子路由
      ]
实际例子
 {
        path: "",
        component: Layout,
        redirect: "dashboard",
        children: [{
            path: "dashboard",
            component: _import("dashboard/index"),
            name: "Dashboard",
            meta: {
                title: "首页",
                icon: "icon-shouye",
                noCache: true
            }
        }]
    },
~~~

~~~~javascript
21 路由守卫

to：即将要进入的目标路由对象
from：当前导航正要离开的路由
next：执行下一步

beforeEach是路由跳转前执行的，afterEach是路由跳转后执行的
const whiteList = ['/login', '/register', '/changePassword'] // 不重定向白名单
router.beforeEach((to, from, next) => {
    NProgress.start()
    if (store.getters.account.token) {
        if (to.path === '/login') {
            next({ path: '' })
        } else {
            let resdata = localStorage.get('userResData');
            if (store.getters.addRouters.length === 0 && resdata.length) { // 判断是否已生成可访问的路由表
                const account = session('account')
                account && getUserMenus(account.adminId).then(() => {
                    next({...to, replace: true }) // hack方法 确保addRoutes已完成 ,replace: true so the navigation will not leave a history record
                })

            } else {
                next()
            }
            next()
        }
    } else {
        if (whiteList.indexOf(to.path) !== -1) { //如果是这几个路由的话,就直接进入下一步
            next()
        } else {
            next('/login')
            NProgress.done()
        }
    }
})
router.afterEach(() => {
    NProgress.done()
})

路由守卫只要是做跳转拦截,实际例子中,如果token存在,就是表示已经登陆,如果是login,直接跳去首页,如果不是判断有没有生成路由表,有的话就进入下一步，如果没有登陆,一律跳到login,除了设置的白名单
21.1
路由独享的守卫beforeEnter
不同的是：全局守卫可以作用于全局，路由独享守卫只作用于被设置守卫的路由
21.1
beforeRouterEnter来判断是一定条件下才执行相应的页面跳转
  const pages = {
    'formula': true,
    'formula-add': true
  }
  
// 销毁用到的recipeName
    beforeRouteEnter (to, from, next) {
      next(vm => {
        // 通过 `vm` 访问组件实例
        if(pages[from.name]) {
          vm.buildModel()
        } else {
          vm.$store.commit('clearSelectRecipe')
        }
      })
    }
 buildModel() {
        this.opModel.recipeName = this.$store.state.order.selectRecipe.recipeName
      }
21.2如何写一个路由守卫
 1.首先写一个permission的js,然后引入到import './permission.js' // 操作权限指令
 2.这里必须要next才能执行,而且每次触发路由都要去做next的调用
import router from './router'
router.beforeEach((to, from, next)=> {
    // console.log(to, 'to');
    if (to.path === '/hello') {
        console.log('1111')
        next({ path: '/alive' })
    } else {
        next()
    }
    console.log(from, 'from');
    console.log(next, 'next');
})
~~~~

~~~javascript
22 router-link相当于是a标签,通过切换路由来切换组件
组件可以引进来,也可以直接写const Bar = { template: '<div>bar</div>' }
一个是经过vue-loader处理过的，一个直接是个对象
~~~

~~~~
23 $router和$route的区别
我们可以在任何组件内通过 this.$router 访问路由器，也可以通过 this.$route 访问当前路由
route相当于当前正在跳转的路由对象。。可以从里面获取name,path,params,query等

router为VueRouter的实例，相当于一个全局的路由器对象，里面含有很多属性和子对象，例如history对象。。。经常用的跳转链接就可以用this.$router.push，和router-link跳转一样。。。

this.$router.push会往history栈中添加一个新的记录

我们使用 this.$router 的原因是我们并不想在每个独立需要封装路由的组件中都导入路由

length 属性声明了浏览器历史列表中的元素数量。

注意：Internet Explorer和Opera从0开始，而Firefox、Chrome和Safari从1开始。
怎么回答:
我们可以在任何组件内通过 this.$router 访问路由器，也可以通过 this.$route 访问当前路由，this.$router.push会往history栈中添加一个新的记录,router.push(location)
想要导航到不同的 URL，则使用 router.push 方法。这个方法会向 history 栈添加一个新的记录，所以，当用户点击浏览器后退按钮时，则回到之前的 URL

~~~~

~~~~javascript
24 query和params跳转的区别
路由传参的方式
//可以手写完整的path
1.直接在路由上面配id的是用params来接收
   {
      path: '/router1/:id',
      name: 'router1',
      component: router1,
      meta: {
        keepAlive: false
      }
    },
    
     toRouter() {
            this.$router.push(
                {
                    path: `/router1/1`
                }
            )
        }
    用console.log(this.$route.params, 'toRouter'); 获取
2. path对应query传参数,用query接收
       this.$router.push(
                {
                    path: `/router1`,
                    query: {
                        age: 12
                    }

                }
            )
3.name 对应params的传输参数
 this.$router.push(
                {
                    name: `router1`,
                    params: {
                        age: 12
                    }

                }
            )
4.直接写path或者name都可以
  toRouter() {
            this.$router.push(
               '/router1'
            )
        }
跳转到这个url显示什么组件，得配置路由。router跳转和<router-link>标签跳转，规则差不多
params与name 不会显示在地址栏,隐藏性比较好
query更加类似于我们ajax中get传参，params则类似于post，说的再简单一点，前者在浏览器地址栏中显示参数，后者则不显示

因为它是路由参数，而query是url的查询参数也就是？号后面的字符串

important
你可以在一个路由中设置多段“路径参数”，对应的值都会设置到 $route.params 中
怎么回答:
query用对应path路径,params 对应name,query会暴露的url上,params相对安全,路由传参数也是直接用params获取,query对应get 请求, params 对应post
~~~~

~~~~javascript
25 响应路由参数的变化
复用就是多个不同的路由里面有相同的组件,这个必须是组件的复用才会出现
 watch: {
    '$route' (to, from) {
      // 对路由变化作出响应...
    }
  }
{ path: '*', redirect: '/404', hidden: true }
域名+非固定的路由,通配到undefined
~~~~

~~~javascript
26 嵌套路由相当于是子集组件
要在嵌套的出口中渲染组件，需要在 VueRouter 的参数中使用 children 配置
const router = new VueRouter({
  routes: [
    { path: '/user/:id', component: User,
      children: [
        {
          // 当 /user/:id/profile 匹配成功，
          // UserProfile 会被渲染在 User 的 <router-view> 中
          path: 'profile',
          component: UserProfile
        },
        {
          // 当 /user/:id/posts 匹配成功
          // UserPosts 会被渲染在 User 的 <router-view> 中
          path: 'posts',
          component: UserPosts
        }
      ]
    }
  ]
})
在父组件里面要用router-view
~~~

~~~~
27 
router.replace与跟 router.push 很像，唯一的不同就是，它不会向 history 添加新记录，而是跟它的方法名一样 —— 替换掉当前的 history 记录
~~~~

~~~~
28
注意导航守卫并没有应用在跳转路由上，而仅仅应用在其目标上。在下面这个例子中，为 /a 路由添加一个 beforeEach 或 beforeLeave 守卫并不会有任何效果。
~~~~

~~~javascript
29
 { path: '/user/:id', component: User, props: true }, 
   props: true设置为true就可以接收传过来的值
   如果是params传参数也是可以直接用props: true
如果是query传参数props: (route) => ({ query: route.query.q })直接接收query
如果是对象的话,直接注入到props里面 
   
~~~

~~~
30执行顺序
已经了解了setTimeout是宏任务会在最后执行的前提
async 函数返回一个promise对象,可以添加then方法添加回调函数,当函数执行时,一旦遇到await会先返回,等异步完成再执行函数体后面的数
~~~

~~~~javascript
31 关于执行顺序
        async function async1(){
            console.log('1')
            await async2() //因为await 后面的是promise,调用之后就是被await成promise了,所以后面的2还需要等待执行，它只是相当于promise.then
            console.log('2')
        }
        async function async2(){
            console.log('3')
        }
        console.log('4')
        setTimeout(function(){
            console.log('5') 
        },0)  
        async1();
        new Promise(function(resolve){
            console.log('6')
            resolve();
        }).then(function(){
            console.log('7')
        })
        console.log('8')

       // 4 1 3 6 8 2 7 5
单线程从上往下执行，碰到异步操作直接丢给其他线程，js线程就不管了,只有等js线程执行完了，才回过头来去看任务队列有没有还未执行的任务，异步操作在其他线程执行完了之后就推到任务队列中,等待js主线程执行完再去执行，那任务队列现在分为微任务和宏任务,微任务要比宏任务的优先级高，js主线程执行完紧接着执行微任务队列，然后再去看宏任务有没有任务执行，主线程是js线程,其他线程是环境提供的，例如浏览器环境提供的定时器，定时器分到定时器的线程去执行的,不在同一条线程的都叫异步，因为没法按顺序执行

怎么回答:
js是单线程的,就是代码从上到下执行,在一段时间内只能做一任务,那么就出现了同步任务和异步任务,那么同步任务就会在主线程,形成一个执行栈,遇到异步任务就会挂起,到其他线程里面去(例如:settimeout由定时器线程去计算时间，时间到则放到下一次事件循环的队列中排队, 浏览器或者宿主环境的io线程去执行http请求),然后就放到任务队列里面去排队,等同步任务执行完就去任务队列里面去看有没有任务需要执行.
任务又分为宏任务和微任务:要分清楚哪些是宏任务,哪些是微任务
宏任务包括整体代码script，setTimeout，setInterval
微任务：Promise，process.nextTick
先是执行整段的script代码,这是第一轮循环,然后执行第一轮所有的微任务,第二轮又从宏任务开始,去执行里面的微任务。


~~~~

~~~javascript
32 
32.1全局前置守卫
正如其名，vue-router 提供的导航守卫主要用来通过跳转或取消的方式守卫导航。有多种机会植入路由导航过程中：全局的, 单个路由独享的, 或者组件级的。next(false)会回到from里面去
32.2路由独享的守卫
const router = new VueRouter({
  routes: [
    {
      path: '/foo',
      component: Foo,
      beforeEnter: (to, from, next) => {
        // ...
      }
    }
  ]
})
32.3 组件内的守卫
beforeRouteEnter 就是进入这个组件也要做操作
不能用this
因为当守卫执行前，组件实例还没被创建
beforeRouteEnter 里面可以先在前一个页面去加载数据



~~~

~~~javascript
33export 和export default的区别与应用
export的用法:
1要么直接输出语句
export let obj = {
    name: 'jack',
    age: 18
}
2要么直接用对象抛出去
let obj = {
    name: 'jack',
    age: 18
}
let str = 'jack'
export {
    obj,
    str
}
export命令除了可以输出变量,还可以输出函数或者类
export function test() {
    return 5
}
2.用import 去拿export出来的数据 
import { obj, str} from '../testExport'
console.log('----', obj, str);
import命令具有提升作用,会提升到整个头部再执行.,
import 是静态执行,所以不能使用表达式和变量

import *  as circle from '../testExport'
// console.log('----', obj, str);
console.log(circle, 'circle');

3.模块整体加载所在的对象应该是可以静态分析的,所以不允许运行时改变
4. export default命令
使用import命令用户需要知道所要加载的变量名和函数名,否则无法加载
加载模块的时候,import可以为匿名函数起任意的名字
export default function () {
    return 5
}
import test from '../testExport'
console.log(test, 'test');
export default命令也可以出现在非匿名函数前面
5 规律：
export default 不用大括号引入,因为是引入指定的模块，一个模块只有一个默认的输出
直接export 要用大括号,因为是导入不同的变量名

怎么回答:
export default 不用大括号引入,因为是引入指定的模块，一个模块只有一个默认的输出
直接export 要用大括号,因为是导入不同的变量名

~~~

~~~javascript
34.scrollBehavior滚动行为
使用keep-alive标签后部分安卓机返回缓存页位置不精确问题
export default new Router({
    // mode: 'history', //后端支持可开，/切换路径模式，变成history模式,不然路径为/#/home
    scrollBehavior: () => ({ y: 0 }), // 滚动条滚动的行为，不加这个默认就会记忆原来滚动条的位置
    routes: constantRouterMap
})
下面：滚动到锚点
scrollBehavior (to, from, savedPosition) {
  if (to.hash) {
    return {
      selector: to.hash
    }
  }
}
// 记忆上次滚动行为,不然就是出现在顶部
  scrollBehavior (to, from, savedPosition) {
    if (savedPosition) {
      return savedPosition
    } else {
      return { x: 0, y: 0 }
    }
  },
~~~

~~~~
35 vue的异步组件
-----当一个函数返回一个对象时，我们称之他为 工厂函数
在大型应用中，我们可能需要将应用分割成小一些的代码块,只有需要使用某个模块,才会从服务器加载某个模块, 

~~~~

~~~javascript
36组件的注册
1.全局的组件---tempalte里面要写div,其他有些标签也可以,全局组件可以注入到不同的vue实例子里面去,只要创建就注入了，在template里面去写
<div id="app">
 <input-word></input-word>
</div>
<div id="app2">
	<input-word></input-word>
</div>
<script src="https://cdn.jsdelivr.net/npm/vue"></script>
<script>
   //全局组件
  Vue.component('input-word', {
  template: '<div>666</div>'
})
new Vue({
  el: '#app'
})
new Vue({
  el: '#app2'
})
</script>


2.局部组件
 <div id="app">    
     <my-text></my-text>
 </div>
 var child = {
   template: '<div>局部注册组件的内容</div>'
 }
 new Vue({
   el: '#app',
   components: {
     'my-text' : child
   }
 })
vue的实例化会把全局注册的组件注册到当前实例中
实例化完成你再全局注册组件是对这个实例不起作用的
Vue.component是全局注入的表现,项目中局部注入比较多
组件的复用：注意当点击按钮时，每个组件都会各自独立维护它的 count。因为你每用一次组件，就会有一个它的新实例被创建。

3.组件的返回必须是一个函数,主要是因为组件可以复用,所以每一个组件需要有自己独立的一份数据--如果复用是一个对象的话,就是共享同样的data对象,当你修改一个属性的时候，data也会发生改变,如果是函数的话,就相当于创建新的obj,所以不一样----｛｝相当于new Object
1.如果是对象
        var obj = {
            name: 'jack'
        }
        var text1 = obj
        var text2 = obj
        text1.name = 'rose'
        console.log(text2.name); //rose

2.工厂函数
 var fun = function() {
            return {
                name: 'jack'
            }
        }
        var text1 = fun()
        var text2 = fun()
        text1.name = 'rose'
        console.log(text2.name); //jack



~~~

~~~javascript
37组件之间的传值
*父传子--父组件将数组传到引入的子组件,子组件通过prop来接收参数.
     <div id="app">
        <new-input :item="item" v-for="(item,index) in list"></new-input>
    </div>
       Vue.component('new-input', {
            props: ['item'],
            template: '<div>{{item.name}}---{{item.age}}</div>'
        })
        new Vue({
            el: '#app',
            data: {
                list: [
                    { 
                        name: 'jack',
                        age: 18
                    },
                    { 
                        name: 'rose',
                        age: 20
                    }
                ]
            }
        })
*子传父---子组件事件触发
<button v-on:click="$emit('enlarge-text')">
  Enlarge text
</button>
怎么回答:
1、父组件可以使用 props 把数据传给子组件。
2、子组件可以使用 $emit 触发父组件的自定义事件。

$emit 触发当前实例上的事件。附加参数都会传给监听器回调
*组件之间的v-model的绑定
组件之间
*兄弟组件之间的传值
1.先是子传父,再是父传子
2.借助中央事件总线
3.通过路由传参数
4.vuex
https://blog.csdn.net/amanda_wmy/article/details/80134823

~~~

~~~~~
38 抽取公共组件
调用open的方法～～～多练练吧～～～
1.先将组件抽出去,子组件需要的值,父组件传过来,父组件需要的值或者事件,子组件emit传过去
2.子组件里面调完接口再触发emit去保存等操作
封装过什么组件?
流程组件-step组件
时间选择器--datapicker组件
信息确认弹框等--confirmDialog
城市选择组件
判断为空的组件--empty
~~~~~

~~~javascript
40 axios
axios({
    method: 'post',
    url: '/user/12345',
    data: {
        firstName: 'Fred',
        lastName: 'Flintstone'
    }
})
.then(function (response) {
    console.log(response);
})
.catch(function (error) {
    console.log(error);
});
1.从浏览器中创建 XMLHttpRequest
2.支持 Promise API
3.客户端支持防止CSRF
4.提供了一些并发请求的接口（重要，方便了很多的操作）
5.从 node.js 创建 http 请求
6.拦截请求和响应
7.转换请求和响应数据
8.取消请求
9.自动转换JSON数据
跨站点请求伪造(Cross—Site Request Forgery)
PS:防止CSRF:就是让你的每个请求都带一个从cookie中拿到的key, 根据浏览器同源策略，假冒的网站是拿不到你cookie中得key的，这样，后台就可以轻松辨别出这个请求是否是用户在假冒网站上的误导输入，从而采取正确的策略。
怎么回答:
本质上也是对原生XHR的封装，只不过它是Promise的实现版本，符合最新的ES规范
支持promise Api 能够防止csrf,自动转成json数据 axios.all的并发请求接口
~~~

~~~
41 兄弟组件的传值--bus事件总线传值
https://blog.csdn.net/zhonghuachun/article/details/80594439
vue中$emit与$on
　var Event = new Vue();　　　　　　相当于又new了一个vue实例，Event中含有vue的全部方法；
Event.$emit('msg',this.msg);　　　   发送数据，第一个参数是发送数据的名称，接收时还用这个名字接收，第二个参数是这个数据现在的位置；

　　Event.$on('msg',function(msg){　　接收数据，第一个参数是数据的名字，与发送时的名字对应，第二个参数是一个方法，要对数据的操作

　　　　/这里是对数据的操作

　　})
怎么回答:
现在用bus（一个空的vue对象）作为介质传值更加方便,主要利用emit来传输数据 和 on 来接收数据，在App.vue里使用这两个组件,直接将Bus注入到Vue根对象中
~~~

~~~javascript
42 vuex
应用于：
多个视图依赖于同一状态。
来自不同视图的行为需要变更同一状态

每一个 Vuex 应用的核心就是 store（仓库）。“store”基本上就是一个容器，它包含着你的应用中大部分的状态 (state)。Vuex 和单纯的全局对象有以下两点不同：
1.Vuex 的状态存储是响应式
2.你不能直接改变 store 中的状态。改变 store 中的状态的唯一途径就是显式地提交 (commit) mutation

1.Vuex 通过 store 选项，提供了一种机制将状态从根组件“注入”到每一个子组件中
const app = new Vue({
  el: '#app',
  // 把 store 对象提供给 “store” 选项，这可以把 store 的实例注入所有的子组件
  store,
  components: { Counter },
  template: `
    <div class="app">
      <counter></counter>
    </div>
  `
})

const Counter = {
  template: `<div>{{ count }}</div>`,
  computed: {
    count () {
      return this.$store.state.count
    }
  }
}
用mapState
   computed: mapState({
        count: 'count',
        init: 'init'
    })
当映射的计算属性的名称与 state 的子节点名称相同时，我们也可以给 mapState 传一个字符串数组
  computed: mapState([
        'count',
        'init']
    )
2mapState 函数返回的是一个对象。我们如何将它与局部计算属性混合使用呢？通常，我们需要使用一个工具函数将多个对象合并为一个，以使我们可以将最终对象传给 computed 属性。但是自从有了对象展开运算符

    // 混合使用
    computed: {
        test(){
            return true
        },
        ...mapState({
            count: state => state.count,
            init: state => state.init,
            other(state) {
                return state.count + state.init
            }
        })
    }
3.Vuex 允许我们在 store 中定义“getter”（可以认为是 store 的计算属性）。就像计算属性一样，getter 的返回值会根据它的依赖被缓存起来，且只有当它的依赖值发生了改变才会被重新计算
Getter 会暴露为 store.getters 对象，你可以以属性的形式访问这些值
你也可以通过让 getter 返回一个函数，来实现给 getter 传参。在你对 store 里的数组进行查询时非常有用
  getters: {
        //计算属性
        doneTodos: state => {
            return state.todos.filter(todo => todo.done)
        },
        // 可以接收两个参数,第一个是state,第二个是getter
        otherComputed:(state, getters) => {
            getters.doneTodos.push({
                id: 4, text: '...', done: true
            })
            return  getters.doneTodos
            // return  getters.doneTodos
        },
        //变成一个函数来传参数
        getTodoById: (state) => (id) => {
          return state.todos.find(todo => todo.id === id)
        }
    }
4 更改 Vuex 的 store 中的状态的唯一方法是提交 mutation
4.1怎么传入额外的参数--Mutation 必须是同步函数
       increase() {
            store.commit('addDouble', 2)  
        }
     mutations: {
        increment (state) {
            state.count ++
        },
        addDouble(state, payload) {
            state.init += payload
        },
    },
4.2辅助函数提交
import { mapMutations } from 'vuex'

export default {
  // ...
  methods: {
    ...mapMutations([
      'increment', // 将 `this.increment()` 映射为 `this.$store.commit('increment')`

      // `mapMutations` 也支持载荷：
      'incrementBy' // 将 `this.incrementBy(amount)` 映射为 `this.$store.commit('incrementBy', amount)`
    ]),
    ...mapMutations({
      add: 'increment' // 将 `this.add()` 映射为 `this.$store.commit('increment')`
    })
  }
}
5.
			 makeSync(state) {
            // state.count++
            console.log(state.count, 'state.count1');
            setTimeout(()=> {
                state.count++
            console.log(state.count, 'state.count2');
            }, 3000) 
            console.log(state.count, 'state.count3');   
        }
调试debug困难 无法预知状态什么时候改变
6.Action 类似于 mutation，不同在于：

Action 提交的是 mutation，而不是直接变更状态。
Action 可以包含任意异步操作。
 actions: {
    actionIncrement (context) {
       context.commit('increment')
    }
 }
 testAction() {
     store.dispatch('actionIncrement')
   actions: {
  actionA ({ commit }) {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        commit('someMutation')
        resolve()
      }, 1000)
    })
  }
}
store.dispatch('actionA').then(() => {
  // ...
})
怎么回答:
   vuex主要是为了解决多层嵌套的组件之间的传参数，主要是把组件的共享状态抽离出来。
   特点:1.从store里面拿参数是响应式的 2.更改状态的唯一方式是提交mutation
   vuex分为4个部分,第一个state来存储状态数据,mutations相当于事件,要去改变state的时候用提交用commit去提交mutation,getter用来过滤数据,会暴露store.getters来提供,并且通过属性的方式来调计算属性,Action 函数接受一个与 store 实例具有相同方法和属性的 context 对象，因此你可以调用 context.commit 提交一个 mutation，或者通过 context.state 和 context.getters 来获取 state 和 getters, store.dispatch('increment')
   Action 通常是异步的, mutation必须是同步的
~~~

~~~~
43 webpack--搭建项目https://github.com/PanJiaChen/vue-admin-template
load主要是遇到什么样的文件,该怎么打包,查看webpack可以知道对应的load 进行处理
tree shaking只支持静态资源的打包
optimization sideEffets
43.1可能在本地文件上可以,但是在打包上不行,主要是因为样式没有用scoped或者没有用div包起来
怎么回答:
webpack是一个模块打包工具,分为5个部分:
第一个是入口entry: 哪个模块，来作为构建其内部依赖图的开始。进入入口起点后，webpack 会找出有哪些模块和库是入口起点（直接和间接）依赖的
第二个是出口: 这里可以这是文件名以及文件打包出来的路径,这里要设置模块标识符[hash]，这样打包出来呢,就会根据版本不同而自动更新
第三个是module模块部分:根据不同的文件去下载不同的load,正则匹配是vue文件,就用vue-load,css用css-load
第四个是plugin 每一次运行npm run build都会生成一个js文件,所以不需要那么多,这时可以去下载CleanWebpackPlugin,html-webpack-plugin 的作用是：当使用 webpack打包时，创建一个 html 文件，并把 webpack 打包后的静态文件自动插入到这个 html 文件当中
https://www.jianshu.com/p/42e11515c10f
~~~~

~~~~javascript
44 import 与require的区别
require：运行时加载
import：编译时加载（效率更高）【由于是编译时加载，所以import命令会提升到整个模块的头部】
44.1 require是AMD规范的写法，import是ES6模块的写法
44.2

let { a, b, c } = require('module');
// import { a, b, c } from 'module';

看似只引入了a，b，c，实际上引入了整个模块，然后通过解构赋值给a，b，c赋值对应的模块内容
44.3
require是执行时引入，import是编译时引入，因此，require可以用在代码的任何地方，import即使不写在文件的开头也会自动提升到开头，比如
foo();
 
import { foo } from 'my_module';
44.4
运行时加载就是说 执行require函数后再加载/ 编译加载就是把你import的模块在编译的时候就加载过来了，一进来就执行/把模块代码编译到你的文件中去，一进入这个文件你那些引入的模块js也执行了/require就是你必须require才去寻找模块并且执行

怎么回答:
requie是引入整个模块,然后把里面的东西拿出来用---require运行时加载,用于读取并执行js文件
import是直接引入模块里面的部分东西---import是编译时加载,由于是编译时加载，所以import命令会提升到整个模块的头部,import用于引入外部模块， 其他脚本等的函数， 对象或者基本类型，等到真正使用到该模块的时候才会去加载模块中的值。
https://juejin.im/post/5df0765be51d455825128f6d

~~~~

~~~
45 https://juejin.im/post/5cc580215188257feb01cad8
webpack打包
有四个核心模块
1.入口起点entry--来指定一个入口起点,webpack 会找出有哪些模块和库是入口起点（直接和间接）依赖的

2.出口output 属性告诉 webpack 在哪里输出它所创建的 bundles，以及如何命名这些文件，默认值为 ./dist
----来告诉 webpack bundle 的名称，以及我们想要 bundle 生成(emit)到哪里

生成的bundle文件添加hash为什么添加hash？

是为了防止浏览器缓存机制阻止文件的更新，为打包文件添加hash后缀之后，每次构建打包生成的文件名的hash都会发生改变，强制浏览器进行刷新，获取当前最新的文件就可以防止使用缓存文件。

3.loader 让 webpack 能够去处理那些非 JavaScript 文件
	test 属性，用于标识出应该被对应的 loader 进行转换的某个或某些文件。
  use 属性，表示进行转换时，应该使用哪个 loader
  vue-loader---css-loader---babel-loader
  
4.plugin
loader 被用于转换某些类型的模块，而插件则可以用于执行范围更广的任务。插件的范围包括，从打包优化和压缩，一直到重新定义环境中的变量。插件接口功能极其强大，可以用来处理各种各样的任务

在npm run build之后，dist文件夹中并没有index.html文件，要想引用打包的文件，需要手动引用，并且由于上一步为bundle添加了hash，所以每次构建都需要手动修改script标签的src路径。
使用HtmlWebpackPlugin可以自动生成html文件并注入打包的文件。

CleanWebpackPlugin每次打包都会去掉之前的
npm i -D webpack-dev-server
webpack-dev-server生成的包并没有放在你的真实目录中,而是放在了内存中

比如在本地运行没事,但是在线上有问题,可能是样式的问题


~~~

~~~
46 html5新标签
46.1 
<audio>--定义音频内容
<video>--定义视频内容
<header>--定义文档的头部区域
<footer>--定义 section 或 document 的页脚

~~~

~~~css
47 css新特性
flex-box:可以用来布局
border-radius :给边框做圆角
box-sizing:content-box; box-sizing:border-box
media媒体查询
@media screen and (max-width: 699px) and (min-width: 520px) {
    ul li a {
        padding-left: 30px;
        background: url(email-icon.png) left center no-repeat;
    }
}

~~~

~~~css
48jquery的一些常见的用法
------------------------------
48.1获取元素对象
  $('#id')
  $('.class')
  $('div')
------------------------------
48.2ajax
$.ajax(
    {
    url:"demo_test.txt",
    data:{}，
    success:function(result){
        $("#div1").html(result);
    }
});
------------------------------
48.3属性
$("img").attr("src")
$("img").removeAttr("src")
$("p").addClass("selected")
$("p").removeClass("selected")
$("p").html("Hello <b>world</b>!")--设置html内容
48.4 css方面
$("p").css({ "color": "#ff0011", "background": "blue" });
------------------------------
48.4 事件
直接用on写事件--点击
$("p").on("click", function(){
alert( $(this).text() );
});
在DOM加载完成时运行的代码
$(document).ready(function(){
  // 在这里写你的代码...
});
------------------------------
48.5事件对象
eve.preventDefault()--阻止默认事件
eve.currentTarget--在事件冒泡阶段中的当前DOM元素
event.stopPropagation()--灭掉冒泡
除了阻止元素上其它的事件处理函数的执行，这个方法还会通过在内部调用 event.stopPropagation() 来停止事件冒泡。如果仅仅想要停止事件冒泡到前辈元素上，而让这个元素上的其它事件处理函数继续执行，我们可以使用event.stopPropagation() 来代替
~~~

~~~javascript
49 事件轮询(Event Loop)
------------------------------
49.1js是单线程的,什么是单线程？就是在同一时间只能做一个任务,代码也是从上到下执行的,如果有多个任务，就必须排队，前面一个任务完成，再执行后面一个任务，以此类推，如果一个任务耗时很长,就会一直等待,直接等到上一个处理好
------------------------------
49.2 为了解决这个问题，分为两类任务：同步和异步
什么是同步？什么是异步呢
同步模式是--后一个任务等待前一个任务结束，然后再执行,程序的执行顺序与任务的排列顺序是一致的、同步的
异步模式是--每一个任务有一个或多个回调函数（callback），前一个任务结束后，不是执行后一个任务，而是执行回调函数，后一个任务则是不等前一个任务结束就执行，所以程序的执行顺序与任务的排列顺序是不一致的、异步的
------------------------------
49.3事件轮询
具体来说，异步运行机制如下：
（1）所有同步任务都在主线程上执行，形成一个执行栈（execution context stack）。
（2）主线程之外，还存在一个"任务队列"（task queue）。只要异步任务有了运行结果，就在"任务队列"之中放置一个事件。
（3）一旦"执行栈"中的所有同步任务执行完毕，系统就会读取"任务队列"，看看里面有哪些事件。那些对应的异步任务，于是结束等待状态，进入执行栈，开始执行。
（4）述过程会不断重复，也就是常说的Event Loop(事件循环)。

js执行机制
（所有同步任务都在主线程上执行，形成一个"执行栈"）
首先判断JS是同步还是异步,同步就进入主线程,异步就进入event table
异步任务在event table中注册函数,当满足触发条件后,被推入任务队列
同步任务进入主线程后一直执行,直到主线程空闲时,才会去任务队列中查看是否有可执行的异步任务,如果有就推入主线程中
------------------------------
"异步模式"编程的4种方法
1.回调函数
2.事件监听,任务的执行不取决于代码的顺序，而取决于某个事件是否发生
3.发布订阅模式，也叫观察者模式
4.Promises对象

最基础的异步是setTimeout和setInterval函数，很常见，但是很少人有人知道其实这就是异步，因为它们可以控制js的执行顺序,关于setTimeout要补充的是，即便主线程为空，0毫秒实际上也是达不到的。根据HTML的标准，最低是4毫秒
------------------------------
49.4 任务又会分为宏任务和微任务
macro-task(宏任务)：包括整体代码script，setTimeout，setInterval
micro-task(微任务)：Promise，process.nextTick
事件循环的顺序，决定js代码的执行顺序。进入整体代码(宏任务)后，开始第一次循环。接着执行所有的微任务。然后再次从宏任务开始，找到其中一个任务队列执行完毕，再执行所有的微任务。
//微任务和宏任务执行顺序
console.log('1');
setTimeout(function() {
    console.log('2');
    process.nextTick(function() {
        console.log('3');
    })
    new Promise(function(resolve) {
        console.log('4');
        resolve();
    }).then(function() {
        console.log('5')
    })
})
process.nextTick(function() {
    console.log('6');
})
new Promise(function(resolve) {
    console.log('7');
    resolve();
}).then(function() {
    console.log('8')
})

setTimeout(function() {
    console.log('9');
    process.nextTick(function() {
        console.log('10');
    })
    new Promise(function(resolve) {
        console.log('11');
        resolve();
    }).then(function() {
        console.log('12')
    })
})
// 1，7，6，8，2，4，3，5，9，11，10，12

 //  async 函数中可能会有 await 表达式，这会使 async 函数暂停执行，等待表达式中的
 // Promise 解析完成后继续执行 async 函数并返回解决结果。
				async function async1(){
            console.log('1')
            await async2()
            console.log('2')
        }
        async function async2(){
            console.log('3')
        }
        console.log('4')
        setTimeout(function(){
            console.log('5') 
        },0)  
        async1();
        new Promise(function(resolve){
            console.log('6')
            resolve();
        }).then(function(){
            console.log('7')
        })
        console.log('8')

        // 4136 8275
~~~

~~~javascript
50 跨域请求的问题

同源策略: 域名，协议，端口相同
同源策略是浏览器的行为，是为了保护本地数据不被JavaScript代码获取回来的数据污染，因此拦截的是客户端发出的请求回来的数据接收，即请求发送了，服务器响应了，但是无法被浏览器接收

50.1axios没有提供jsonp等方法进行跨域访问数据
1.1要么就是后台处理
1.2代理可以解决的原因：因为客户端请求服务端的数据是存在跨域问题的，而服务器和服务器之间可以相互请求数据，是没有跨域的概念（如果服务器没有设置禁止跨域的权限问题），也就是说，我们可以配置一个代理的服务器可以请求另一个服务器中的数据，然后把请求出来的数据返回到我们的代理服务器中，代理服务器再返回数据给我们的客户端，这样我们就可以实现跨域访问数据
---跨域配置baseUrl,然后在--去做拦截
proxyTable: {
      '/api': {
        target:'http://api.douban.com/v2', // 你请求的第三方接口
        changeOrigin:true, // 在本地会创建一个虚拟服务端，然后发送请求的数据，并同时接收请求的数据，这样服务端和服务端进行数据的交互就不会有跨域问题
        pathRewrite:{  // 路径重写，
          '^/api': ''  // 替换target中的请求地址，也就是说以后你在请求http://api.douban.com/v2/XXXXX这个地址的时候直接写成/api即可。
        }
      }
50.2通过jsonp
首先是利用script标签的src属性来实现跨域。因为src属性无跨域限制的特点
通过将前端方法作为参数传递到服务器端，然后由服务器端注入参数之后再返回，实现服务器端向客户端通信。
由于使用script标签的src属性，因此只支持get方法限制
alert("You’re at IP address " + response.ip + ", which is in " + 
 response.city + ", " + response.region_name); 
} 
var script = document.createElement("script"); 
script.src = "http://freegeoip.net/json/?callback=handleResponse"; 
document.body.insertBefore(script, document.body.firstChild);
JSONP 由两部分组成：回调函数和数据。回调函数是当响应到来时应该在页面中调用的函数。回调
函数的名字一般是在请求中指定的。而数据就是传入回调函数中的JSON数据。下面是一个典型的JSONP
请求。

只能发送get请求。因为script只能发送get请求
需要后台配合。此种请求方式应该前后端配合，将返回结果包装成callback(result)的形式
JSONP 之所以在开发人员中极为流行，主要原因是它非常简单易用。与图像 Ping 相比，它的优点
在于能够直接访问响应文本，支持在浏览器与服务器之间双向通信。不过，JSONP 也有两点不足。

首先，JSONP 是从其他域中加载代码执行。如果其他域不安全，很可能会在响应中夹带一些恶意代
码，而此时除了完全放弃 JSONP 调用之外，没有办法追究。因此在使用不是你自己运维的 Web 服务时，
一定得保证它安全可靠。
其次，要确定 JSONP 请求是否失败并不容易。虽然 HTML5 给<script>元素新增了一个 onerror
事件处理程序，但目前还没有得到任何浏览器支持。为此，开发人员不得不使用计时器检测指定时间内
图灵社区会员 StinkBC(StinkBC@gmail.com) 专享 尊重版权
588 第 21 章 Ajax 与 Comet 
是否接收到了响应。但就算这样也不能尽如人意，毕竟不是每个用户上网的速度和带宽都一样

50.3图像ping
第一种跨域请求技术是使用<img>标签。我们知道，一个网页可以从任何网页中加载图像，不
用担心跨域不跨域
var img = new Image(); 
img.onload = img.onerror = function(){ 
 alert("Done!"); 
}; 
img.src = "http://www.example.com/test?name=Nicholas";
通过图像 Ping，浏览器得不到任何具体的数据，但通过侦听 load 和 error 事件，它能知道响应是什么时
候接收到的。  

50.4CORS（Cross-Origin Resource Sharing，跨源资源共享）
必须访问跨源资源时，浏览器与服务器应该如何沟通。CORS 背后的基本思想，就是使用自定义的 HTTP 头部
让浏览器与服务器进行沟通，从而决定请求或响应是应该成功，还是应该失败

现代浏览器都支持CORS，所以前端不需要做任何改变，即使普通的ajax，只需要服务器实现CORS接口；http如何请求服务器，我们不会有如何感觉。

在发送该请求时，需要给它附加一个额外的 Origin 头部，其中包含请求页面的源信息（协议、域名和端口），以便服务器根据这个头部信息来决定是否给予响应。下面是 Origin 头部的一个示例：Origin: http://www.nczonline.net 
如果服务器认为这个请求可以接受，就在 Access-Control-Allow-Origin 头部中回发相同的源信息（如果是公共资源，可以回发"*"）。例如：Access-Control-Allow-Origin: http://www.nczonline.net 
如果没有这个头部，或者有这个头部但源信息不匹配，浏览器就会驳回请求。正常情况下，浏览器会处理请求。

怎么回答:
同源策略就是 端口 协议名 域名相同
可以通过图像ping跨域,jsonp跨域,cors跨域
必须访问跨源资源时，浏览器与服务器应该如何沟通。CORS 背后的基本思想，就是使用自定义的 HTTP 头部
让浏览器与服务器进行沟通，从而决定请求或响应是应该成功，还是应该失败
jsonp就是利用script标签里面的src无跨域限制的特点。src会发起一个get的请求,这时可以将参数带过去,也设置一个callback把服务端的response里面把资源带回来

jsonp也有他的缺点:
首先，JSONP 是从其他域中加载代码执行。如果其他域不安全，很可能会在响应中夹带一些恶意代
码，而此时除了完全放弃 JSONP 调用之外，没有办法追究。因此在使用不是你自己运维的 Web 服务时，
一定得保证它安全可靠。
~~~

~~~
51 BootStrap,Element ui,Mint-ui,了解Cube ui,Vant ui
删格系统
~~~

~~~javascript
52常见的es6语法
------------------------------
let 和const
	let声明的变量只在它所在的代码块有效
	不存在变量名提升
	在代码块内，使用let命令声明变量之前，该变量都是不可用的。这在语法上，称为“暂时性死区”
	const声明的变量不得改变值，这意味着，const一旦声明变量，就必须立即初始化，不能留到以后赋值
------------------------------
解构赋值
------------------------------
ES6 允许为函数的参数设置默认值，即直接写在参数定义的后面
function log(x, y = 'World') {
  console.log(x, y);
}

log('Hello') // Hello World
log('Hello', 'China') // Hello China
log('Hello', '') // Hello
-------------------------------
promise
红灯3秒亮一次，绿灯1秒亮一次，黄灯2秒亮一次；如何让三个灯不断交替重复亮灯？（用Promise实现）
        function text(timer, color) {
            return new Promise((resolve, reject)=> {
                setTimeout(()=> {
                    resolve(color)
                }, timer)
            })
        }
        function cb() {
            text(3000, 'red').then((res)=> {
                console.log(res);      
                return text(1000, 'green')
            }).then((res)=> {
                console.log(res);
                return text(2000, 'yellow')
            }).then((res)=> {
                console.log(res);
                return cb()
            })
        }
        cb()
Promse.all在处理多个异步处理时非常有用，比如说一个页面上需要等两个或多个ajax的数据回来以后才正常显示，在此之前只显示loading图标

顾名思义，Promse.race就是赛跑的意思，意思就是说，Promise.race([p1, p2, p3])里面哪个结果获得的快，就返回那个结果，不管结果本身是成功状态还是失败状态
-------------------------------
 promise 有 3 种状态：pending、fulfilled 或 rejected。状态改变只能是 pending->fulfilled 或者 pending->rejected，状态一旦改变则不能再
~~~

~~~
ES5 只有两种声明变量的方法：var命令和function命令。ES6 除了添加let和const命令，后面章节还会提到，另外两种声明变量的方法：import命令和class命令。所以，ES6 一共有 6 种声明变量的方法

~~~

~~~~javascript
53性能优化.
53.1资源压缩合并，减少http请求
53.2非核心代码异步加载---异步加载的方式---异步记载的区别
defer和sync
defer
53.3 利用浏览器缓存---缓存的分类---缓存的原理
53.4 使用CDN
53.5 预解析
<link rel="dns-prefetch" href="//www.baidu.com">
<meta http-equiv="x-dns-prefetch-control">

  

~~~~

~~~css
54 预处理器
54.1直接去下载loader就行了
Less的书写风格更接近于css,Sass、Stylus 相比之下激进一些，利用缩进、空格和换行来减少需要输入的字符,也兼容css的写法
51.2
混入（mixin）应该说是预处理器最精髓的功能之一了
它提供了 CSS 缺失的最关键的东西：样式层面的抽象
https://www.jianshu.com/p/9d84bb5043bb
Less 的混入有两种方式：
1.直接在目标位置混入另一个类样式（输出已经确定，无法使用参数）；
2.定义一个不输出的样式片段（可以输入参数），在目标位置输出。
.alert {
  font-weight: 700;
}

.highlight(@color: red) {
  font-size: 1.2em;
  color: @color;
}

.heads-up {
  .alert;
  .highlight(red);
}
--------------------
.alert {
  font-weight: 700;
}
.heads-up {
  font-weight: 700;
  font-size: 1.2em;
  color: red;
}
怎么回答:主要的作用是可以进行css样式的嵌套,层级分明,还有就是混入,进行样式层面的抽离

~~~

~~~~
55git版本控制工具
创建分支 git branch dev
创建并切换分支 git branch -b dev
https://www.cnblogs.com/antyhouse/p/8967635.html
~~~~

~~~javascript
56 gulp
gulp的简单应用
gulp和webpack的区别
它们都属于前端构建工具，但gulp是任务管理工具（task runner），webpack是模块化打包工具

使用gulp可以配置各种插件来做css或js文件的合并压缩、编译sass或less、添加兼容性前缀（-webkit-, -moz- …）、自动刷新浏览器等等，它能替代手工实现自动化工作、优化前端工作流程。

一。gulp：强调的是前端开发的流程，通过配置一系列的task，定义task处理的事物（例如文件压缩合并、雪碧图、启动server、 版本控制等），然后定义执行顺序，来让gulp执行task，从而构建前端项目的流程。

webpack：是一个前端模块化方案，侧重模块打包，把开发中的所有资源（图片、js文件、css文件等）都看成模块，通过loader（加载器）和plugins（插件）对资源进行处理，打包成符合生产环境部署的前端资源。

相同：可以文件合并与压缩（css）

不同点：

虽然都是前端自动化构建工具，但看他们的定位就知道不是对等的。

gulp严格上讲，模块化不是他强调的东西，他旨在规范前端开发流程。

webpack更是明显强调模块化开发，而那些文件压缩合并、预处理等功能，不过是他附带的功能。
https://segmentfault.com/a/1190000018336570
-----------------------------------------------------------------------
26  Gulp
1、安装nodejs
2、全局安装gulp
3、新建package.json文件  //直接用cnpm.init
4、本地安装gulp插件
5、新建gulpfile.js文件
内容:
//导入工具包 require('node_modules里对应模块')
var gulp = require('gulp'), //本地安装gulp所用到的地方
    less = require('gulp-less');
 
//定义一个testLess任务（自定义任务名称）
gulp.task('testLess', function () {
    gulp.src('src/less/index.less') //该任务针对的文件
        .pipe(less()) //该任务调用的模块
        .pipe(gulp.dest('src/css')); //将会在src/css下生成index.css
});
 
gulp.task('default',['testLess', 'elseTask']); //定义默认任务 elseTask为其他任务，该示例没有定义elseTask任务
 
//gulp.task(name[, deps], fn) 定义任务  name：任务名称 deps：依赖任务名称 fn：回调函数
//gulp.src(globs[, options]) 执行任务处理的文件  globs：处理的文件路径(字符串或者字符串数组) 
//gulp.dest(path[, options]) 处理完后文件生成路径
6、运行gulp
7、使用webstorm运行gulp任务

~~~

~~~~html
57语意化的优点
增强可读性,增强seo搜索
setion:某部分
你好<em>周杰伦</em>---斜体
你好<strong>周杰伦</strong>强调
实用场景
自然语言表达能力的补充；
文章标题摘要；
适合机器阅读的整体结构。c
~~~~



~~~
58 7种类型

Undefined；
Null；
Boolean；
String；
Number；
Symbol；
Object。

~~~

~~~
59 undefined 与 null的区别
Undefined跟 null 有一定的表意差别，null表示的是：“定义了但是为空”。所以，在实际编程时，我们一般不会把变量赋值为 undefined，这样可以保证所有值为 undefined 的变量，都是从未赋值的自然状态。

Null 类型也只有一个值，就是 null，它的语义表示空值，与 undefined 不同，null 是 JavaScript 关键字，所以在任何代码中，你都可以放心用 null 关键字来获取 null 值

59.1任何涉及 NaN 的操作（例如 NaN/10）都会返回 NaN,NaN与任何数都不想等，包括他本身
 
~~~

~~~
60JavaScript对象的两类属性
----------------------------
先来说第一类属性，数据属性。它比较接近于其它语言的属性概念。数据属性具有四个特征。

value：就是属性的值。
writable：决定属性能否被赋值。
enumerable：决定for in能否枚举该属性。
configurable：决定该属性能否被删除或者改变特征值
------------------------------
第二类属性是访问器（getter/setter）属性，它也有四个特征。

getter：函数或undefined，在取属性值时被调用。
setter：函数或undefined，在设置属性值时被调用。
enumerable：决定for in能否枚举该属性。
configurable：决定该属性能否被删除或者改变特征值


~~~

~~~
61 浏览器如何工作
实际上，对浏览器的实现者来说，他们做的事情，就是把一个URL变成一个屏幕上显示的网页。

这个过程是这样的：

浏览器首先使用HTTP协议或者HTTPS协议，向服务端请求页面；
把请求回来的HTML代码经过解析，构建成DOM树；
计算DOM树上的CSS属性；
最后根据CSS属性对元素逐个进行渲染，得到内存中的位图；
一个可选的步骤是对位图进行合成，这会极大地增加后续绘制的速度；
合成之后，再绘制到界面上。

~~~

~~~
62 tpc传输的三次握手四次挥手策略
62.1 客户端首先发送一个带syn标志的数据包给对方
62.2 服务端接收后回转一个带有syn/ack标志的数据包已示传达确认信息
62.3 客户端再回传一个带ack标志的数据包,代表握手结束

~~~

~~~
63
async函数必定返回Promise，我们把所有返回Promise的函数都可以认为是异步函数。
async函数是一种特殊语法，特征是在function关键字之前加上async关键字，这样，就定义了一个async函数，我们可以在其中使用await来等待一个Promise
~~~

~~~javascript
64 箭头函数的作用
javascript是一个单线程语言，这意味着在浏览器中同时只能做一件事情。当javascript解释器初始执行代码，它首先默认进入全局上下文。每次调用一个函数将会创建一个新的执行上下文
-----------------------------------
        function test() {
            console.log(this, 'this'); 
        }
        test() ----//window
-----------------------------------
         function test() {
            console.log(this, 'this'); 
        }
         var o = {
            showThis: test
        }
        o.showThis() //指向this
 ------------------------------------
     var x = 11;
        var obb = {
            x: 222,
            y: {
                x:333,
                obc: function f() {
                    console.log(this)
                    var x = 111;
                    var obj = {
                        x: 22,
                        say: () => {
                            console.log(this.x);
                        }
                    }
                    obj.say()
                }
            }
        }
        obb.y.obc() // 333
原因箭头函数没有this，剪头函数的this是继承父执行上下文里面的this ，这里箭头函数的执行上下文是函数f()，所以它就继承了f()的this
 ------------------------------------
   var x = 11;
    var obb = {
        x: 222,
        y: {
            x: 333,
            obc: f = () =>{
                console.log(this)
                var x = 111;
                var obj = {
                    x: 22,
                    say: () => {
                        console.log(this.x);
                    }
                }
                obj.say();
            }
        }
    }
    obb.y.obc()// 11
如果f也是一个剪头函数，它就只能继续向上找也就是window了
-----------------------------------
  var x = 11;
        var obb = {
            x: 222,
            y: {
                x:333,
                obc: function() {
                    var x = 111;
                    var obj = {
                        x: 22,
                        say: function() {
                            console.log(this.x);
                        }
                    }
                     obj.say()
                }
            }
        }
        obb.y.obc()
// 输出结果是22，普通函数的this指向是调用它的那个对象，这里调用say进行打印的是obj对象
~~~

~~~~javascript
65 移动端的适配
65.1 rem进行适配
rem的大小取决于html节点的font-size大小，用的时候先按照设计稿的大小计算出一套rem值，然后在css里加入不同的媒体适配断点和对应的font-size，实现伸缩；

px2rem.js 设置一下设计稿尺寸
flex
 <style>
            .huan {
                width: 2rem;
                font-size: 1rem;
            }
    </style>
</head>
<body>
    <script>
        window.onload = function () {
            var stylediv = document.createElement('style')
            var w = document.documentElement.clientWidth/16 + 'px'
            stylediv.innerHTML = 'html{font-size:"+w+"!important}'
            document.head.append(stylediv)
        }
    </script> 




~~~~

~~~
66
凡是替换型元素，都是使用src属性来引用文件的，而我们之前的课程中已经讲过，链接型元素是使用href标签的
~~~

~~~~
67 flex作用
http://www.ruanyifeng.com/blog/2015/07/flex-grammar.html
justify-content的作用
项目在主轴方向的对齐方式 
aligin-items
项目在交叉轴方向的对齐方式
aligin-content
align-content属性定义了多根轴线的对齐方式。如果项目只有一根轴线，该属性不起作用。
~~~~

~~~
68今天我从宏观的角度介绍了前端架构相关的知识，我重点介绍了“组件化”“适配性”“单页应用”三个前端架构需要解决的核心问题，组件化在社区有很多现成的方案，我们需要做的主要工作是框架选型。适配性需要用到CSS的几种特性：vw单位、viewport规则和media规则，单页应用重点是逻辑页面解耦、独立开发和发布和保持前进后退历史
~~~

~~~
69
69.1.我一直看见闭包这个词，但是一直也没有弄清楚它是什么东西，老师可以简单概括一下什么是闭包吗？

答：你可以这样理解，闭包其实就是函数，还是能访问外面变量的函数。
闭包其实就是函数包含函数 能够解决全局变量的污染,能够读到外层变量,闭包将自己私有的方法提供到外部使用,但是内存也会释放不出去,

外部有引用就不释放咯
你外部用完断掉引用就行
设置为null
~~~

~~~
70 https://www.cnblogs.com/hentai-miao/p/10271652.html
vue.extend（）
使用基础 Vue 构造器，创建一个“子类”。参数是一个包含组件选项的对象。

data 选项是特例，需要注意 - 在 Vue.extend() 中它必须是函数
相当于是不用new vue直接创建一个组件去挂载
~~~

~~~javascript
71 Object.defineProperty() 方法会直接在一个对象上定义一个新属性，或者修改一个对象的现有属性， 并返回这个对象
//obj.name的值现在总是与text相同，除非重新定义obj.name
      var text = 'boy'
       var obj = {}
       Object.defineProperty(obj, 'name', {
           get () {
               return text
           },
           set (val) {
               text = val
           }
       })
       console.log(obj, 'obj');

~~~

~~~javascript
72 mvvm双向数据绑定
UE实现双向数据绑定的原理就是利用了 Object.defineProperty() 这个方法重新定义了对象获取属性值(get)和设置属性值(set)的操作来实现的
get
一个给属性提供 getter 的方法，如果没有 getter 则为 undefined。当访问该属性时，该方法会被执行，方法执行时没有参数传入，但是会传入this对象（由于继承关系，这里的this并不一定是定义该属性的对象）。
默认为 undefined。
set
一个给属性提供 setter 的方法，如果没有 setter 则为 undefined。当属性值修改时，触发执行该方法。该方法将接受唯一参数，即该属性新的参数值。默认为 undefined。

 首先vue的双向绑定就是数据层变化然后视图层跟着变
  这个主要是通过Object.defineProperty()来劫持各个属性的setter，getter，在数据变动时发布消息给订阅者，触发相应的监听回调
例如 先创建一个对象,如果在input里面输入东西,会触发addEventListener并且将值给这个obj的某个key.这时候会发生数据劫持,在get时是直接返回,在重新赋值的时候会将数据给需要展示的标签,其实就是拦截对象去做一些操作,输入想要的效果
   <div id="box">
        <div id="show"></div>
        <input type="text" id="input">
    </div>
</head>
<body>
    <script>
        var show =  document.getElementById('show')
        var ipt = document.getElementById('input')
        var obj = {}
        Object.defineProperty(obj, 'name', {
            get() {
                return ipt.value
            },
            set(value) {
                ipt.value = value
                show.innerHTML = value
            }
        })
        ipt.addEventListener('input', function(e){
            obj.name = e.target.value
        })
    </script>
https://www.jianshu.com/p/23180880d3aa
~~~

~~~javascript
73
  //第一个 根据index来获取字符串的某个值
        var str = 'hello'
        console.log(str.charAt(1)); // h

        // 第二个 拼接字符串或者数组,直接在后面去添加
        var str = 'hello'
        console.log(str.concat('world')); // helloworld
        
        var arr = [ 1, 2, 3]
        var arr2 = [3, 4, 4]
        var newarr = arr.concat(arr2)
        console.log(newarr); // [1, 2, 3, 3, 4, 4]
        
        // 3个基于字符串创建新的字符串的方式 slice  substr substring
        // 第一个参数指定子字符串的开始位置，第二个参数（在指定的情况下）表示子字符串到哪里结束,
        // slice()和substring()的第二个参数指定的是子字符串最后一个字符后面的位置。
        // 而substr最后的一个参数定的则是返回的字符个数,如果没有给这些方法传递第二个参数，则将字符串的长度作为结束位置
        var stringValue = "hello world"; 
        console.log(stringValue.slice(3)); // lo world
        console.log(stringValue.slice(3, 4)); // l
        
        console.log(stringValue.substring(3)); // lo world
        console.log(stringValue.substring(3, 4)); // l

        console.log(stringValue.substr(3)) // lo world
        console.log(stringValue.substr(3, 4)) //lo w

        //以从字符串中查找子字符串的方法
        var str = 'hello world'
        console.log(str.indexOf('e')); // 1  indexof 的结果等于-1表示不存在 lastindexof表示从后面搜索

        // trim 去掉空格
        var str = 'hello world '
        console.log(str.trim()); // 去掉前后空格

        //最后一个与模式匹配有关的方法是 split()，这个方法可以基于指定的分隔符将一个字符串分割成
        //多个子字符串，并将结果放在一个数组中
        // split()方法可以接受可选的第二个参数，用于指定数组的大小，以便确保返回的数组不会超过既定大小
          var colorText = "red,blue,green,yellow";
          console.log(colorText.split(',')); // ["red", "blue", "green", "yellow"]
          console.log(colorText.split(',', 1)); // ["red"]
~~~

~~~
74 jsbrige 的使用
https://github.com/lzyzsd/JsBridge
https://juejin.im/post/5c4fc0726fb9a049dd80b046
分为原生开发、混合开发两种 美团的、大众点评的APp、微信APP都是app内嵌h5
如今最火的APP开发模式是Hybrid APP开发（即混合模式，半原生半H5页面）。 
原生是Native APP 
H5就是Web App
Js和Android方法的第一个参数需要两端保持一致，并且Js代码必须使用window.WebViewJavascriptBridge.callHandler(x,y,function(data))这种类型的方法

https://juejin.im/post/5c4fc0726fb9a049dd80b046
1.客户端注入jsbrige,ios和android注入的jsbrige可能会有些差异,安卓和iOS会在WebView中的window下注入一个WebViewJavascriptBridge的对象，把这个函数挂在在window下,为了使用方便，在页面onload后，我们将这个函数挂在window下，我们这边的函数就是JSBridge.如果要使用一个复制的功能,window.jsbrige.copyClip(this.text),
上面相当于向app发起了一个playMusic请求，app收到请求后，执行相关的动作，然后可以callback，网页可以拿到回调并继续做相关动作（需要的话）， 
webView.callHandler去拿着一个方法

h5 与原生 app 交互的原理
现在移动端 web 应用，很多时候都需要与原生 app 进行交互、沟通（运行在 webview中），比如微信的 jssdk，通过 window.wx 对象调用一些原生 app 的功能

h5 与原生 app 的交互，本质上说，就是两种调用, app 调用 h5 的代码，h5 调用 app 的代码,因为 app 是宿主，可以直接访问 h5，所以这种调用比较简单，就是在 h5 中曝露一些全局对象（包括方法），然后在原生 app 中调用这些对象,由 app 向 h5 注入一个全局 js 对象，那么就用jsbrige作为一个桥梁,在客户端暴露一个方法给h5.

由于H5页面是内嵌到原生应用的WebView组件（一个浏览器内核）中

为什么要使用h5和app结合
们为什么要采用hybrid模式开发？简而言之就是同时利用H5的跨平台、快速迭代能力以及Native的流畅性、系统API调用能力

https://www.jianshu.com/p/910e058a1d63/


~~~

~~~javascript
75 正则表达式
var expression = / pattern / flags
模式与信号
其中的模式（pattern）部分可以是任何简单或复杂的正则表达式，可以包含字符类、限定符、分组、
向前查找以及反向引用。每个正则表达式都可带有一或多个标志（flags），用以标明正则表达式的行为。
正则表达式的匹配模式支持下列 3 个标志
g：表示全局（global）模式，即模式将被应用于所有字符串，而非在发现第一个匹配项时立即
停止
i：表示不区分大小写（case-insensitive）模式，即在确定匹配项时忽略模式与字符串的大小写
m：表示多行（multiline）模式，即在到达一行文本末尾时还会继续查找下一行中是否存在与模
式匹配的项。
-------------------------------------------------
match与test的区别
test是RegExp的方法，参数是字符串，返回值是boolean类型。
match是String的方法，参数是正则表达式，返回值是数组。
-------------------------------------------------
  下面是全局的模式
       var reg=/ab/;
        var str="abefgabcdab";
        console.log(str.match(reg)) // ['ab]
       // var reg=/ab/g;
        // var str="abefgabcdab";
        // console.log(str.match(reg)) // ["ab", "ab", "ab"]
正则表达式:
 var str = 'hello goodboy'
 console.log(/he/g.test(str));  // true
---------------------------------------------
  i 大小写的匹配
        var str = 'hello goodboy'
        var pattern = /[hm]e/g
        console.log(pattern.test(str), 111); // true
        
        var str = 'hello goodboy'
        var pattern = /[Hm]e/g
        console.log(pattern.test(str)); // false

        var str = 'hello goodboy'
        var pattern = /[Hm]e/i
        console.log(pattern.test(str)); // true
 var str = 'hel[lo]goodboy'
        var pattern = /\[lo]\g/g
        console.log(pattern.test(str)); // true
-------------------------------------------
  直接匹配对应的东西需要转译符号
       var str = 'hel[lo]goodboy'
        var pattern = /\[lo]\g/g
        console.log(pattern.test(str)); // true
一个.表示以什么结尾的匹配
 var str = 'hello goodboy'
  console.log(/.boy/g.test(str));  // true
匹配.good需要转译
        var str = 'hello.goodboy'
        console.log(/\.good/g.test(str));  // true

  -------------------------------------
     匹配除换行符以外的任意字符

\w 匹配字母或数字或下划线或汉字 等价于 '[^A-Za-z0-9_]'。

\s 匹配任意的空白符

\d 匹配数字

\b 匹配单词的开始或结束

^ 匹配字符串的开始

$ 匹配字符串的结束
---------------------------------------
   // {n}---	n 是一个非负整数。匹配确定的 n 次。
        // 例如，'o{2}' 不能匹配 "Bob" 中的 'o'，但是能匹配 "food" 中的两个 o。
        var str = '123456789111'
        var pattern = /\d{11}/
        console.log(pattern.test(str));// true

        var str = 'food'
        var pattern = /o{2}/g
        console.log(pattern.test(str));
----------------------------------------
 var str = '18819164274'
        var pattern = /^1[345678]\d{9}/
        console.log(pattern.test(str));
---------------------------------------
replace()方法。这个方法接受两个参数：第
一个参数可以是一个 RegExp 对象或者一个字符串（这个字符串不会被转换成正则表达式），第二个参
数可以是一个字符串或者一个函数,如果第一个参数是字符串，那么只会替换第一个子字符串。要想替
换所有子字符串，唯一的办法就是提供一个正则表达式，而且要指定全局（g）标志
----------------------------------------
        // 第一个为匹配规则,第二个为匹配结果
        var text = 'cat,fat'
        var res =  text.replace('at', 'it')
        console.log(res); // cit,fat

        var text = 'cat,fat'
        var res =  text.replace(/at/g, 'it')
        console.log(res); // cit,fit
~~~

~~~javascript
76 h5-searchCar 的判断
1怎么去判断是在app里面打开还是在企业微信里面打开


2如何判断是iphoneX
isiPhoneX: /iphone/gi.test(navigator.userAgent) && (screen.height === 812 && screen.width === 375),
    if (this.GLOBAL.isiPhoneX) {
      document.getElementsByTagName('html')[0].style.height = '692px'
    }
-----------------------------------------------------------------------------------------
<div class="demandStatus" :style="fixiPhoneX" v-show="editMore" style="left: 6.7rem" v-click-outside="closeMoreSelect"> </div>
给出一个全局的是不是iphoneX 在个别的样式中去判断是不是iphonex,去给不同的样式
        fixiPhoneX: {
          bottom: this.GLOBAL.isiPhoneX ? '2.2rem' : '1.4rem'
        }

-------------------------------------------------------------------------------------------

 fixiPhoneX: {
          paddingBottom: this.GLOBAL.isiPhoneX ? '1.5rem' : ''
        },
3.遇到兼容性问题
// 判断键盘是否调起
document.addEventListener('focus', function (e) {
  const type = e.target.type
  const height = document.body.scrollHeight || document.documentElement.scrollHeight
  const clientHeight = document.body.clientHeight || document.documentElement.clientHeight
  Vue.prototype.GLOBAL.maxScrollTop = height - clientHeight
  // 输入框
  if (type === 'text' || type === 'textarea' || type === 'number' || type === 'tel' || type === 'email' || type === 'search' || type === 'url') {
    setTimeout(function () {
      Vue.prototype.GLOBAL.isKeyboardShow = true
    })
  }
}, true)
document.addEventListener('blur', function () {
  if (Vue.prototype.GLOBAL.appInfo.type) {
    setTimeout(function () {
      // 325 -- iOS12以上系统 键盘bug的填充物高度
      const scrollTop = document.body.scrollTop + document.documentElement.scrollTop
      if (Vue.prototype.GLOBAL.appInfo && Vue.prototype.GLOBAL.appInfo.type === 'iOS' && scrollTop >= Vue.prototype.GLOBAL.maxScrollTop) {
        document.body.scrollTop = Vue.prototype.GLOBAL.maxScrollTop
      }
      Vue.prototype.GLOBAL.isKeyboardShow = false
    })
  }
}, true)
先设置全局变量
Vue.prototype.GLOBAL = {
  isKeyboardShow: false
}
 fixKeyBoard () {
      // 修复 iOS系统版本为12以上时，键盘弹起视窗高度未缩小的Bug
      const iOSVersion = this.GLOBAL.appInfo.iOSVersion.split('.')[0]
      return parseInt(iOSVersion) >= 12
    }
-----------------------------------
   <div v-if="fixKeyBoard && this.GLOBAL.isKeyboardShow" class="fix__iOS-12"></div>
.fix__iOS-12
  height 325px
  width 100%
~~~

~~~javascript
includes
        var arr = ['zhou', 'huan']
        console.log(arr.includes('zhou')); // true

        var str = 'hello,world'
        console.log(str.includes('hell')); // true
~~~

~~~javascript
里面有有jsbridge
window.JSBridge.openMiniProgra()
window.JSBridge.copyToClipboard(this.text)
window.JSBridge.navigationTo('location', JSON.stringify(data))
window.JSBridge.shareLinkToWx(t）
window.JSBridge.goBack()
 this.isWeChat = /MicroMessenger/i.test(navigator.userAgent)

    // this.isIos = /(iPhone|iPad|iPod|iOS)/i.test(navigator.userAgent)
    this.isAndroid = /(Android)/i.test(navigator.userAgent)
~~~

~~~javascript
77 小程序的代码亮点总结
77.1 怎么判断是iphoneX
    let systemInfo = wx.getSystemInfoSync()
    if (systemInfo.model.search('iPhone X') != -1) {
      that.globalData.isIPX = true
    }
在app.js里面去定义一个全局变量globalData
class="{{isIPX ? 'padding-bottom198' : 'padding-bottom140'}}"增加一个padding-bottom


~~~

~~~
78
解偶
简单理解就是 函数拆分，每个函数都独立处理和别的函数无关而不是所有的if else 逻辑全都是一个大函数里面
组件化和模块化
--------------------------------------------------------------------------------------
组件化主要是为了减少重复的代码,还有都对ui组件的封装,弹框的抽离---组件之间联系不大
模块化主要是以功能为划分---模块是相互联系的
-------------------------------------------------------------------------------------
状态码
400 语义错误,当前请求无法被服务器理解 
403 拒绝访问 forbidden
404  找不到页面
500 服务器内部错误，ajax模板错误也是回报这个错误
200 ok
------------------------------------------------------------------------------------
兼容性问题
1.企业微信浏览器
微信浏览器和ios 10.0.2以下版本对es6的不兼容
2.h5跳小程序，在苹果手机能够跳转,但是用安卓不能跳到小程序,主要是因为引入的WeixinJS接口的版本比较老,然后1.3.2版本才解决了这个问题
3.iphoneX的适配
4.在iOS12以上系统 键盘bug的填充物高度
----------------------------------------------------
37三次握手四次挥手
三次握手
（1）第一次握手：建立连接时，客户端A发送SYN包（SYN=j）到服务器B，并进入SYN_SEND状态，等待服务器B确认。

（2）第二次握手：服务器B收到SYN包，必须确认客户A的SYN（ACK=j+1），同时自己也发送一个SYN包（SYN=k），即SYN+ACK包，此时服务器B进入SYN_RECV状态。

（3）第三次握手：客户端A收到服务器B的SYN＋ACK包，向服务器B发送确认包ACK（ACK=k+1），此包发送完毕，客户端A和服务器B进入ESTABLISHED状态，完成三次握手。

完成三次握手，客户端与服务器开始传送数据

四次挥手---关闭连接
1.服务器读通道关闭
2.客户机写通道关闭
3.客户机读通道关闭
4.服务器写通道关闭
~~~

~~~~
79谈谈es6
1.新增了模版字符串,就是更加清晰
2.新增了promise,主要是解决回调地狱的问题
3.Let const定义了定义域,就是不会让全局变量的污染
4.还有解构赋值,能够在很多对象里面找出最重要的
5.箭头函数,代码简洁，结构清晰,this主要是指向外部作用域的上下文
6.set map结构,能够创建key不仅仅是字符串，可以是任意的类型
7通常async函数里面的await的是一个异步函数调用，await就是等待这个异步结束才执行后面的代码
避免了异步编程中的过多回调函数的嵌套
~~~~

~~~javascript
80 vue的生命周期函数

beforeCreate 初始化的作用
created  可以调用data里面的数据
Beforemount dom 没有和数据挂载起来
mounted就是指把data里面的数据挂载到bom节点里面去
beforeUpdate
Update 数据更新完 dom也更新完
beforeDestory 销毁前执行 用来清除定时器等
Destoryed dom存在但是不受vue的控制 卸载watch等

~~~

~~~javascript
81 object.assign和...
Object.assign会触发object.defineProperty.会触发getter和setter
         var obj1 = {
            name: 'jack',
            age: 18
        }
        var obj2 = {
            name: 'rose'
        }
        var newObj = Object.assign(obj1, obj2)
        console.log(newObj, 'newObj');  // {name: rose, age: 18}
--------------------------------------------------------------------
 var obj1 = {
            name: 'jack',
            age: 18
        }
        var obj2 = {
            name: 'rose'
        }
        var newObj  = {...obj1, ...obj2}
        console.log(newObj, 'newObj');// {name: rose, age: 18}
~~~

~~~javascript
82 小程序
模式弹框居中:
    <div class="father">
        <div class="box"></div>
    </div>
    <style>
        .box {
            position:fixed;
            top:0;
            right:0;
            left:0;
            bottom:0;
            margin:auto;
            width: 100px;
            height: 100px;
            background: red;
        }
       
    </style>
第一种就是子元素设置position:fixed， margin: 0 auto;设置固定的宽高left: 0,right:0,top: 0,bottom: 0
 <div class="father">
        <div class="box"></div>
    </div>
    <style>
        .box {
            position:fixed; 
            top:50%; 
            left:50%; 
            transform:translateX(-50%) translateY(-50%);
            background: red;
            width: 100px;
            height: 100px;
        }
    </style>
第二种就是子元素设置position:fixed,left:50%,right: 50%,transform:translatex和translatey为-50%;

直接用display:none, display:show小程序的生命周期
https://www.jianshu.com/p/366e374e108d
头图切换https://blog.csdn.net/HaiJun_Aion/article/details/82082668
https://www.jianshu.com/p/7d3d768eecac
-----------------------------------------------------------------------
  移动端弹出遮罩层禁止页面滚动，遮罩里面的框允许滚动如何实现
 activityAgreement:function(){
     vm.agreementsDailog=true;
     vm.hideTheAgreements=true
       var scrollTop=document.documentElement.scrollTop
       $('body').css({
            'overflow':'hidden',
            'position': 'fixed',
            'top': scrollTop*(-1)
        });
    },
  关闭时
  closeAgreements:function(){
      vm.agreementsDailog=false
      vm.hideTheAgreements=false
       $('body').css({
            'overflow':'auto',
            'position': 'static',
            'top': 'auto'
        });
       
    }
------------------------------------------------------------------
移动端,如何禁止弹窗下面内容滚动/页面滚动
弹起时
          $("body,html").on('touchmove',function(e){
                   e.preventDefault();  //阻止默认行为
          })
关闭时
$("body,html").unbind("touchmove");
---------------------------------------------------------------
53性能优化.
53.1资源压缩合并，减少http请求
53.2非核心代码异步加载---异步加载的方式---异步记载的区别
defer和sync
defer
53.3 利用浏览器缓存---缓存的分类---缓存的原理
53.4 使用CDN
53.5 预解析
<link rel="dns-prefetch" href="//www.baidu.com">
<meta http-equiv="x-dns-prefetch-control">


~~~

~~~
83小程序的结构
wxml,json,wxss,js
Json 主要是小程序的设置,如页面注册,页面标题以及tabBar颜色设置等等
~~~

~~~javascript
84.怎么传值?
a 在html设置data-属性来传递我们需要的值,event.currentTarget.dataset来获取这个数
B 就是在wx.navigationrto ,从onload里面的options获取
C.设置在全局变量里面,用
getApp().globalData.vendor
来获取
~~~

~~~
85小程序的生命周期
1、用户首次打开小程序，触发 onLaunch（全局只触发一次）。
那么什么时候 onLaunch 会再次触发呢
第一次打开微信 第一次打开小程序（可以）
按小程序右上方退出 再次打开小程序 （不可以）
关闭微信进程 再次打开微信 打开小程序 （可以）
切换微信账号 手机第一次授权 第一次关注小程序 （可以）
切换微信账号 打开已有小程序 （可以）
小程序重新打包 （可以）
2、小程序初始化完成后，触发onShow方法，监听小程序显示。
3、小程序从前台进入后台，触发 onHide方法。
4、小程序从后台进入前台显示，触发 onShow方法
~~~

~~~
小程序page页面的一个生命周期
1.onload --能够接受options参数,页面加载
2.onshow--页面显示
3.onready--页面初次渲染完成
4 onhide--页面隐藏
5 onunload--页面卸载
~~~

~~~~
86load与onshow的区别
onLoad页面加载时调用，可以获取参数，通过options。
onShow页面显示时调用。
主要区别：
从二级页面返回该页面时，onLoad不会再次加载，而onshow会重新加载。
这点很重要：
1.如果加载列表页，二级页面对一级的列表页面内容有修改，则以及列表函数应该在onShow中加载，否则可以选择onLoad
2.如果从一个页面携带参数跳转到另外一个页面，在另一个页面获取参数的方式：onLoad(options){  console.log(options.xxx)  },这些参数都挂在在options

小程序的性能优化
1.尽量少使用setDate---仅仅在视图需要更新的时候去使用
	setData会引发视图层页面内容的更新，这一耗时操作一定时间内会阻塞用户交互
  setDate也是比较影响性能的
  ～～通信的开销 + webview更新的开销。
 
2.上传时自动压缩混淆
3.首屏的建议,为了不白屏,可以先显示骨架,不等页面展现完成就开始请求异步
  3.1图片放在cdn
  3.2 压缩代码，清理无用的代码
4.请求的时候
	onLoad 阶段就可以发起请求，不用等ready
  请求结果放在缓存中, 下次接着用
  请求中可以先展示骨架图
  先反馈，再请求。比如说，点赞的按钮，可以先改变按钮的样式，再 发起异步请求
对于一些耗时的操作，在用户等待的过程中，即使给予交互操作的反馈，避免用户以为小程序无响应

3.页面打开一个新页面时微信会深拷贝一个page对象，因此，应该尽量减少默认data的大小
深拷贝一个page对象,深拷贝就是递归去拷贝数据，你数据越大嵌套越深  花的时间就越多

当小程序的代码包下载完毕后，业务代码分别注入逻辑层和渲染层。提高性能优化就只能是减少包的大小
一些小小的建议:
返回首页,应该等那个接口请求回来再显示.margin 可以给多一点,去使用那个ui有点尴尬
设置那里总觉得不能做任何操作 
~~~~

~~~
87原生与wepy的区别
1.文件目录上面 2.支持组件化开发 3.更接近于vue的写法 4.可以使用promise.await等 5.原生只能请求5个,解决了并发请求的问题
已经解除了 超过10个会排队
~~~

~~~
88关于5层嵌套的问题
wx.navigateTo 用于保留当前页面、跳转到应用内的某个页面
wx.redirectTo()用于关闭当前页面，跳转到应用内的某个页面
wx.reLaunch wx.reLaunch()与 wx.redirectTo()的用途基本相同， 只是 wx.reLaunch()先关闭了内存中所有保留的页面，再跳转到目标页面
wx.switchTab 对于跳转到 tab bar 的页面，最好选择 wx.switchTab()，它会先关闭所有非 tab bar 的页面
~~~

~~~

89 bindtap和catchtap的区别
bindtap  ：子元素使用bindtap绑定事件后，执行的时候，会冒泡到父元素（触发父元素上绑定的bingtap事件）
catchtap  ：不会冒泡到父元素上，阻止事件冒泡
bindBlur 失去焦点

~~~

~~~
 90为什么var that = this
this指向的是page对象,在wx.request里面没有办法获取到这个this.所以用that=this来代替
~~~

~~~
91 小程序遇到的坑
1tab的跳转 要用wx.switchtab,也可以用relaunchTo去跳
2.宽度的设置 mode withfix 
3.小程序 image跟view标签上下会有间隙---verticle baselineß

~~~

~~~
async 比较好用就是让异步代码更加像同步代码
https://juejin.im/entry/58df68498d6d810061488922
~~~

~~~
setStorageSync 与setStorage存储的区别
~~~

