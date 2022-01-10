# test


14123
ES6


1.NULL判断符？？
由 陈亮全01创建于2021-11-05Go to start of metadata
读取对象属性的时候，如果某个属性的值是null或undefined，有时候需要为它们指定默认值。常见做法是通过||运算符指定默认值。
const headerText = response.settings.headerText || 'Hello, world!';
const animationDuration = response.settings.animationDuration || 300;
const showSplashScreen = response.settings.showSplashScreen || true;
上面的三行代码都通过||运算符指定默认值，但是这样写是错的。开发者的原意是，只要属性的值为null或undefined，默认值就会生效，但是属性的值如果为空字符串或false或0，默认值也会生效。
为了避免这种情况，ES2020 引入了一个新的 Null 判断运算符??。它的行为类似||，但是只有运算符左侧的值为null或undefined时，才会返回右侧的值。
const headerText = response.settings.headerText ?? 'Hello, world!';
const animationDuration = response.settings.animationDuration ?? 300;
const showSplashScreen = response.settings.showSplashScreen ?? true;
上面代码中，默认值只有在左侧属性值为null或undefined时，才会生效。
这个运算符的一个目的，就是跟链判断运算符?.配合使用，为null或undefined的值设置默认值。
const animationDuration = response.settings?.animationDuration ?? 300;
上面代码中，如果response.settings是null或undefined，或者response.settings.animationDuration是null或undefined，就会返回默认值300。也就是说，这一行代码包括了两级属性的判断。
这个运算符很适合判断函数参数是否赋值。
function Component(props) {
const enable = props.enabled ?? true; // … }
上面代码判断props参数的enabled属性是否赋值，基本等同于下面的写法。
function Component(props) {
const { enabled: enable = true, } = props; // … }
??本质上是逻辑运算，它与其他两个逻辑运算符&&和||有一个优先级问题，它们之间的优先级到底孰高孰低。优先级的不同，往往会导致逻辑运算的结果不同。
现在的规则是，如果多个逻辑运算符一起使用，必须用括号表明优先级，否则会报错。
// 报错 lhs && middle ?? rhs lhs ?? middle && rhs lhs || middle ?? rhs lhs ?? middle || rhs
上面四个表达式都会报错，必须加入表明优先级的括号。

2.object.assign(source,target)
注意点
（1）浅拷贝
Object.assign()方法实行的是浅拷贝，而不是深拷贝。也就是说，如果源对象某个属性的值是对象，那么目标对象拷贝得到的是这个对象的引用。
const obj1 = {a: {b: 1}}; const obj2 = Object.assign({}, obj1); obj1.a.b = 2; obj2.a.b // 2
上面代码中，源对象obj1的a属性的值是一个对象，Object.assign()拷贝得到的是这个对象的引用。这个对象的任何变化，都会反映到目标对象上面。
（2）同名属性的替换
对于这种嵌套的对象，一旦遇到同名属性，Object.assign()的处理方法是替换，而不是添加。
const target = { a: { b: 'c', d: 'e' } } const source = { a: { b: 'hello' } } Object.assign(target, source) // { a: { b: 'hello' } }
上面代码中，target对象的a属性被source对象的a属性整个替换掉了，而不会得到{ a: { b: 'hello', d: 'e' } }的结果。这通常不是开发者想要的，需要特别小心。
一些函数库提供Object.assign()的定制版本（比如 Lodash 的_.defaultsDeep()方法），可以得到深拷贝的合并。 //合并URL请求的params
（3）数组的处理
Object.assign()可以用来处理数组，但是会把数组视为对象。
Object.assign([1, 2, 3], [4, 5]) // [4, 5, 3]
上面代码中，Object.assign()把数组视为属性名为 0、1、2 的对象，因此源数组的 0 号属性4覆盖了目标数组的 0 号属性1。
（4）取值函数的处理
Object.assign()只能进行值的复制，如果要复制的值是一个取值函数，那么将求值后再复制。
const source = { get foo() { return 1 } }; const target = {}; Object.assign(target, source) // { foo: 1 }
上面代码中，source对象的foo属性是一个取值函数，Object.assign()不会复制这个取值函数，只会拿到值以后，将这个值复制过去。




注意，由于存在浅拷贝的问题，DEFAULTS对象和options对象的所有属性的值，最好都是简单类型，不要指向另一个对象。否则，DEFAULTS对象的该属性很可能不起作用。
const default = {
 params:{
 host : "liangquan.chen@email"
port:7070
    }
}
function test(params){
params = object.assign({},defaullt,params)
}
test({url:{
post:8000
}})
得到的结果： {url:{post:l8000}}
上面代码的原意是将url.port改成 8000，url.host不变。实际结果却是options.url覆盖掉DEFAULTS.url，所以url.host就不存在了。
解决方法：
一些函数库提供Object.assign()的定制版本（比如 Lodash 的_.defaultsDeep()方法），可以得到深拷贝的合并。 //合并URL请求的params

3. Promise
Promise 是异步编程的一种解决方案，比传统的解决方案——回调函数和事件——更合理和更强大。它由社区最早提出和实现，ES6 将其写进了语言标准，统一了用法，原生提供了Promise对象。

所谓Promise，简单说就是一个容器，里面保存着某个未来才会结束的事件（通常是一个异步操作）的结果。从语法上说，Promise 是一个对象，从它可以获取异步操作的消息。Promise 提供统一的 API，各种异步操作都可以用同样的方法进行处理。

Promise对象有以下两个特点。

（1）对象的状态不受外界影响。Promise对象代表一个异步操作，有三种状态：pending（进行中）、fulfilled（已成功）和rejected（已失败）。只有异步操作的结果，可以决定当前是哪一种状态，任何其他操作都无法改变这个状态。这也是Promise这个名字的由来，它的英语意思就是“承诺”，表示其他手段无法改变。

（2）一旦状态改变，就不会再变，任何时候都可以得到这个结果。Promise对象的状态改变，只有两种可能：从pending变为fulfilled和从pending变为rejected。只要这两种情况发生，状态就凝固了，不会再变了，会一直保持这个结果，这时就称为 resolved（已定型）。如果改变已经发生了，你再对Promise对象添加回调函数，也会立即得到这个结果。这与事件（Event）完全不同，事件的特点是，如果你错过了它，再去监听，是得不到结果的。

注意，为了行文方便，本章后面的resolved统一只指fulfilled状态，不包含rejected状态。

有了Promise对象，就可以将异步操作以同步操作的流程表达出来，避免了层层嵌套的回调函数。此外，Promise对象提供统一的接口，使得控制异步操作更加容易。

Promise也有一些缺点。首先，无法取消Promise，一旦新建它就会立即执行，无法中途取消。其次，如果不设置回调函数，Promise内部抛出的错误，不会反应到外部。第三，当处于pending状态时，无法得知目前进展到哪一个阶段（刚刚开始还是即将完成）。

如果某些事件不断地反复发生，一般来说，使用 Stream 模式是比部署Promise更好的选择。

4. Provy
proxy 用于修改某些操作的默认行为，等同于在语言层面做出修改，所以属于一种“元编程”（meta programming），即对编程语言进行编程。
Proxy 可以理解成，在目标对象之前架设一层“拦截”，外界对该对象的访问，都必须先通过这层拦截，因此提供了一种机制，可以对外界的访问进行过滤和改写。Proxy 这个词的原意是代理，用在这里表示由它来“代理”某些操作，可以译为“代理器”。

5. Reflect 
Reflect对象一共有 13 个静态方法。
•	Reflect.apply(target, thisArg, args)
•	Reflect.construct(target, args)
•	Reflect.get(target, name, receiver)
•	Reflect.set(target, name, value, receiver)
•	Reflect.defineProperty(target, name, desc)
•	Reflect.deleteProperty(target, name)
•	Reflect.has(target, name)
•	Reflect.ownKeys(target)
•	Reflect.isExtensible(target)
•	Reflect.preventExtensions(target)
•	Reflect.getOwnPropertyDescriptor(target, name)
•	Reflect.getPrototypeOf(target)
•	Reflect.setPrototypeOf(target, prototype)
6. 逻辑赋值运算符 ||= &&= ??=
// 或赋值运算符
x ||= y // 等同于 x || (x = y)
// 与赋值运算符
x &&= y // 等同于 x && (x = y)
// Null 赋值运算符
x ??= y // 等同于 x ?? (x = y
ps：
opts.foo ??= 'bar';
opts.baz ??= 'qux'

GIT使用
1.	修改commit
2.	git status 查看状态
3.	git add  添加缓存
4.	git commit -m ''message'' 
5.	    git commit -m '
6.	    message1
7.	    message2
8.	    '         //提交多条信息
9.	git commit --amend   1.未增加新的缓存，可进入vi编辑器，i开始编写，q退出编写，：wq保存退出
10.	                                    2.有新的缓存未提交，可以将新的内容的提交到最近一次提交中
11.	git reset --soft HEAD~1 //将当前的提交后退到第一次提交,提交的内容回到缓存里
12.	git reset HEAD 文件来源（file） //删除缓存中的指定文件，此时commit不会将这个文件提交
13.	git revert    撤销本次提交的内容，不过本次提交依然在分支中，git revert只是将本次提交的内容全部回滚
14.	git reset --hard HEAD~1 //会直接将本次提交内容全部扔掉，用户如果考虑以后可能还需要提交的内容建议使用git revert，如果确认完全无用可以使用git reset --hard命令，硬重置后提交的内容都无法从分支中找到
2. 回退
git处理冲突
当拉取下来的文件与本地修改的文件有冲突，先提交你的改变，或者先将你的改变暂时存储起来
1、将本地修改存储起来
git stash
2、pull内容
git pull
3、还原暂存的内容
git stash pop stash@{0}
也可以简写

1.	git stash pop
2.	

git放弃修改文件
本地修改了文件但还未add

1.	git checkout --filename //单个文件
2.	
3.	git checkout . //全部文件

本地新增了文件还未add

1.	rm filename / rm dir -rf //单个文件 //直接删除文件
2.	
3.	
4.	git clean -xdf //全部文件
5.	
6.	// 删除新增文件，如果文件已经git add到缓存区，并不会删除

已经git add提交到了暂存区

1.	git reset HEAD filename //单个文件
2.	git reset HEAD . //全部文件

git add以及git commit之后

1.	git reset commit_id //commit_id是你回到的那个节点，可通过git log查看，可以只选前几位
2.	
3.	//撤销之后，已经commit的修改还在工作区
4.	
5.	git reset --hard commit_id
6.	
7.	//撤销之后，已经commit的修改将会删除，仍在工作区/暂存区的代码不会清除
8.	

回滚远程分支
原理：先将本地分支退回某个commit，删除远程分支，再重新push本地分支

1.	git log //查看要回退的commit_id
2.	git branch the_branch_backup //备份当前这个分支的情况
3.	git reset --hard commit_id //本地分支回滚到对应版本
4.	git push origin :the_branch //删除远端分支
5.	git push origin the_branch //用回滚后的本地分支建立远端分支

git另一个进程还在运行
问题描述
出现这种情况可能是git在执行的过程中，你中止之后异常，进程一直停留

1.	Another git process seems to be running in this repository, e.g.
2.	an editor opened by 'git commit'. Please make sure all processes
3.	are terminated then try again. If it still fails, a git process
4.	may have crashed in this repository earlier:
5.	remove the file manually to continue.

问题原因
因为进程的互斥，所以资源被上锁，但是由于进程突然崩溃，所以未来得及解锁，导致其他进程访问不了。
问题解决
打开隐藏文件夹选项，进入工作区文件目录的隐藏文件.git，把其中的index.lock问价删除掉
git本地版本回退
•	通过tortoiseGit查看日志show log
•	选中需要回退的代码版本
•	右键选择Reset "master to this...
•	在弹出的窗口Reset Type选择Hard:Reset working tree and index(discard all local changes)
3.	基本提交流程
git add .             //添加
git commit -m "提交信息"  //提交到本地仓库
git pull origin 当前分支    //和远程仓库分支合并
git push origin 当前分支   //提交到远程仓库
4. 基础命令
查看分支：git branch

创建分支：git branch <name>

切换分支：git checkout <name>

创建+切换分支：git checkout -b <name>

合并某分支到当前分支：git merge <name>

删除分支：git branch -d <name>

查看状态：git status
5更新仓库
git fetch origin dev  (分支)

git checkout dev

git merge FETCH_HEAD

git checkout liangquan.chen

git status //查看状态

git merge dev

6. 覆盖
开发时，对于本地的项目中修改不做保存操作（或代码改崩），可以用到Git pull的强制覆盖，具体代码如下：

git fetch --all
git reset --hard origin/分支名称（比如：git reset --hard origin/master）
git pull //可以省略

git fetch 指令是下载远程仓库最新内容，不做合并
git reset 指令把HEAD指向master最新版本

JavaScript
1.JS 中如何判断 undefined
JavaScript 中有两个特殊数据类型：undefined 和 null，下节介绍了 null 的判断，下面谈谈 undefined 的判断。

以下是不正确的用法：
var exp = undefined;
if (exp == undefined)
{
    alert("undefined");
}
exp 为 null 时，也会得到与 undefined 相同的结果，虽然 null 和 undefined 不一样。注意：要同时判断 undefined 和 null 时可使用本法。

var exp = undefined;
if (typeof(exp) == undefined)
{
    alert("undefined");
}
以下是正确的用法：



var exp = undefined;
if (typeof(exp) == "undefined")
{
    alert("undefined");
}
2.JS 中如何判断 null
以下是不正确的用法：
var exp = null; 
if (exp == null) 
{ 
alert(“is null”); 
}

exp 为 undefined 时，也会得到与 null 相同的结果，虽然 null 和 undefined 不一样。注意：要同时判断 null 和 undefined 时可使用本法。

var exp = null; 
if (!exp) 
{ 
alert(“is null”); 
}

如果 exp 为 undefined 或者数字零，也会得到与 null 相同的结果，虽然 null 和二者不一样。注意：要同时判断 null、undefined 和数字零时可使用本法。

var exp = null; 
if (typeof(exp) == “null”) 
{ 
alert(“is null”); 
}

为了向下兼容，exp 为 null 时，typeof 总返回 object。

var exp = null; 
if (isNull(exp)) 
{ 
alert(“is null”); 
}

JavaScript 中没有 isNull 这个函数。


以下是正确的用法：
var exp = null; 
if (!exp && typeof(exp)!=”undefined” && exp!=0) 
{ 
alert(“is null”); 
}　
2.数组方法
整理了以下数组方法

join()
push()和pop()
shift() 和 unshift()
sort()
reverse()
concat()
slice()
splice()
indexOf()和 lastIndexOf() （ES5新增）
forEach() （ES5新增）
map() （ES5新增）
filter() （ES5新增）
every() （ES5新增）
some() （ES5新增）
1|0join()
join，就是把数组转换成字符串，然后给他规定个连接字符，默认的是逗号(  ，)

　　书写格式：join(" ")，括号里面写字符串  ("要加引号"),

var arr = [1,2,3]; console.log(arr.join()); 　　　　// 1,2,3

console.log(arr.join("-")); 　　// 1-2-3

console.log(arr); 　　　　　　　　// [1, 2, 3]（原数组不变）



2|0push()和pop()
push():  把里面的内容添加到数组末尾，并返回修改后的长度。

pop()：移除数组最后一项，返回移除的那个值，减少数组的length。

　　　　书写格式：arr.push(" ")，括号里面写内容  ("字符串要加引号"),

　　　   书写格式：arr.pop( )

var arr = ["Lily","lucy","Tom"]; var count = arr.push("Jack","Sean"); console.log(count); 　　　　　　　　　　// 5

console.log(arr); 　　　　　　　　　　　// ["Lily", "lucy", "Tom", "Jack", "Sean"]

var item = arr.pop(); console.log(item); 　　　　　　　　　　 // Sean

console.log(arr); 　　　　　　　　　　 // ["Lily", "lucy", "Tom", "Jack"]

3|0shift() 和 unshift()   (和上面的push，pop相反，针对第一项内容)
shift()：删除原数组第一项，并返回删除元素的值；如果数组为空则返回undefined 。 



unshift:将参数添加到原数组开头，并返回数组的长度 。

　　　　书写格式：arr.shift(" ")，括号里面写内容  ("字符串要加引号"),

var arr = ["Lily","lucy","Tom"];

var count = arr.unshift("Jack","Sean");

console.log(count); 　　　　　　　　　　　　　　// 5

console.log(arr); 　　　　　　　　　　　　　　　//["Jack", "Sean", "Lily", "lucy", "Tom"]

var item = arr.shift(); console.log(item); 　　　　　　　　　　　　　　// Jack

console.log(arr); 　　　　　　　　　　　　　　 // ["Sean", "Lily", "lucy", "Tom"]

4|0sort()
sort()：将数组里的项从小到大排序

　　　   书写格式：arr.sort( )



var arr1 = ["a", "d", "c", "b"]; console.log(arr1.sort()); 　　　　　　　　　　// ["a", "b", "c", "d"]

sort()方法比较的是字符串，没有按照数值的大小对数字进行排序，要实现这一点，就必须使用一个排序函数

function sortNumber(a,b) { 　　return a - b }

arr = [13, 24, 51, 3]; console.log(arr.sort()); 　　　　　　　　　　// [13, 24, 3, 51]
console.log(arr.sort(sortNumber)); 　　　　// [3, 13, 24, 51](数组被改变)

5|0reverse()
reverse()：反转数组项的顺序。

　　　   书写格式：arr.reverse( )

var arr = [13, 24, 51, 3]; console.log(arr.reverse()); 　　　　　　　　//[3, 51, 24, 13]

console.log(arr); 　　　　　　　　　　　　　　//[3, 51, 24, 13](原数组改变)

6|0concat()
concat() ：将参数添加到原数组中。这个方法会先创建当前数组一个副本，然后将接收到的参数添加到这个副本的末尾，最后返回新构建的数组。在没有给 concat()方法传递参数的情况下，它只是复制当前数组并返回副本。

　　　　书写格式：arr.concat()，括号里面写内容  ("字符串要加引号"),

var arr = [1,3,5,7];

var arrCopy = arr.concat(9,[11,13]);

console.log(arrCopy); 　　　　　　　　　　　　//[1, 3, 5, 7, 9, 11, 13]

console.log(arr); 　　　　　　　　　　　　　　// [1, 3, 5, 7](原数组未被修改)

7|0slice()
slice()：返回从原数组中指定开始下标到结束下标之间的项组成的新数组。slice()方法可以接受一或两个参数，即要返回项的起始和结束位置。在只有一个参数的情况下， slice()方法返回从该参数指定位置开始到当前数组末尾的所有项。如果有两个参数，该方法返回起始和结束位置之间的项——但不包括结束位置的项。

　　　　书写格式：arr.slice( 1 , 3  )

var arr = [1,3,5,7,9,11];

var arrCopy = arr.slice(1);

var arrCopy2 = arr.slice(1,4);

var arrCopy3 = arr.slice(1,-2);

var arrCopy4 = arr.slice(-4,-1);

console.log(arr); 　　　　　　　　　　　　　　//[1, 3, 5, 7, 9, 11](原数组没变)

console.log(arrCopy); 　　　　　　　　　　　 //[3, 5, 7, 9, 11]

console.log(arrCopy2); 　　　　　　　　　　　//[3, 5, 7]

console.log(arrCopy3); 　　　　　　　　　　　//[3, 5, 7]

console.log(arrCopy4); 　　　　　　　　　　　//[5, 7, 9]

　　arrCopy只设置了一个参数，也就是起始下标为1，所以返回的数组为下标1（包括下标1）开始到数组最后。 

　　arrCopy2设置了两个参数，返回起始下标（包括1）开始到终止下标（不包括4）的子数组。 

　　arrCopy3设置了两个参数，终止下标为负数，当出现负数时，将负数加上数组长度的值（6）来替换该位置的数，因此就是从1开始到4（不包括）的子数组。 

　　arrCopy4中两个参数都是负数，所以都加上数组长度6转换成正数，因此相当于slice(2,5)。

8|0splice()
splice()：删除、插入和替换。

删除：指定 2 个参数：要删除的第一项的位置和要删除的项数。

　　　　书写格式：arr.splice( 1 , 3  )



插入：可以向指定位置插入任意数量的项，只需提供 3 个参数：起始位置、 0（要删除的项数）和要插入的项。

　　　　书写格式：arr.splice(  2,0,4,6  )
替换：可以向指定位置插入任意数量的项，且同时删除任意数量的项，只需指定 3 个参数：起始位置、要删除的项数和要插入的任意数量的项。插入的项数不必与删除的项数相等。

　　　　书写格式：arr.splice(  2,0,4,6  )

var arr = [1,3,5,7,9,11];

var arrRemoved = arr.splice(0,2);

console.log(arr); 　　　　　　　　　　　　　　　//[5, 7, 9, 11]

console.log(arrRemoved); 　　　　　　　　　　　//[1, 3]

var arrRemoved2 = arr.splice(2,0,4,6); console.log(arr); 　　　　　　　　　　　　　　　// [5, 7, 4, 6, 9, 11]

console.log(arrRemoved2); 　　　　　　　　　　// []

var arrRemoved3 = arr.splice(1,1,2,4); console.log(arr); 　　　　　　　　　　　　　　　// [5, 2, 4, 4, 6, 9, 11]

console.log(arrRemoved3); 　　　　　　　　　　//[7]

9|0indexOf()和 lastIndexOf()
indexOf()：接收两个参数：要查找的项和（可选的）表示查找起点位置的索引。其中， 从数组的开头（位置 0）开始向后查找。

　　　　书写格式：arr.indexof( 5 )

lastIndexOf：接收两个参数：要查找的项和（可选的）表示查找起点位置的索引。其中， 从数组的末尾开始向前查找。 

　　　　书写格式：arr.lastIndexOf(  5,4  )

var arr = [1,3,5,7,7,5,3,1]; console.log(arr.indexOf(5)); 　　　　　　//2

console.log(arr.lastIndexOf(5)); 　　　 //5

console.log(arr.indexOf(5,2)); 　　　　 //2

console.log(arr.lastIndexOf(5,4)); 　　//2

console.log(arr.indexOf("5")); 　　　　 //-1

10|0forEach()
forEach()：对数组进行遍历循环，对数组中的每一项运行给定函数。这个方法没有返回值。参数都是function类型，默认有传参，参数分别为：遍历的数组内容；第对应的数组索引，数组本身。

　　　　书写格式：arr.forEach()

var arr = [1, 2, 3, 4, 5];

arr.forEach(function(x, index, a){ console.log(x + '|' + index + '|' + (a === arr)); });

// 输出为：

// 1|0|true

// 2|1|true

// 3|2|true

// 4|3|true

// 5|4|true

11|0map()
map()：指“映射”，对数组中的每一项运行给定函数，返回每次函数调用的结果组成的数组。

　　　　书写格式：arr.map()

var arr = [1, 2, 3, 4, 5];

var arr2 = arr.map(function(item){ return item*item; });

console.log(arr2); 　　　　　　　　//[1, 4, 9, 16, 25]

12|0filter()
filter()：“过滤”功能，数组中的每一项运行给定函数，返回满足过滤条件组成的数组。

　　　　书写格式：arr.filter()

var arr = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];

var arr2 = arr.filter(function(x, index) { return index % 3 === 0 || x >= 8; });

console.log(arr2); 　　　　　　　　//[1, 4, 7, 8, 9, 10]

13|0every()
every()：判断数组中每一项都是否满足条件，只有所有项都满足条件，才会返回true。

　　　　书写格式：arr.every()

var arr = [1, 2, 3, 4, 5];

var arr2 = arr.every(function(x) { return x < 10; });

console.log(arr2); 　　　　　　　　//true

var arr3 = arr.every(function(x) { return x < 3; });

console.log(arr3); 　　　　　　　　// false

14|0some()
some()：判断数组中是否存在满足条件的项，只要有一项满足条件，就会返回true。

　　　　书写格式：arr.some()

var arr = [1, 2, 3, 4, 5];

var arr2 = arr.some(function(x) { return x < 3; });

console.log(arr2); 　　　　　　　　//true

var arr3 = arr.some(function(x) { return x < 1; });

console.log(arr3); 　　　　　　　　// false



VUE
事件总线  用于两个组件之间的通讯

    this.$bus.$emit 发布信息

    this.$bus.$on    接受信息

    this.$bus.$off    移除事件监听



   ps:



```javascript

// main.js

const bus = new Vue()

Vue.prototype.$bus = bus



// comp1

{

   name: 'comp1',

   created(){

     this.$bus.$on('openCategoryLivingRoom', this.loadIFrame)

   },

   beforeDestroy () {

    this.$bus.$off('openCategoryLivingRoom', this.loadIFrame)

   } 

}



// comp2

{

   name: 'comp2',

   methods:{

      m1() {

         this.$bus.$emit('openCategoryLivingRoom', params)

      }

   }

}

```





store（仓库）

  vuex 包含有五个基本的对象：



    state：存储状态。也就是变量；

   

    getters：派生状态。也就是set、get中的get，有两个可选参数：state、getters分别可以获取state中的变量和其他的getters。外部调用方式：store.getters.personInfo()。就和vue的computed差不多；

   

    mutations：提交状态修改。也就是set、get中的set，这是vuex中唯一修改state的方式，但不支持异步操作。第一个参数默认是state。外部调用方式：store.commit('SET_AGE', 18)。和vue中的methods类似。

   

    actions：和mutations类似。不过actions支持异步操作。第一个参数默认是和store具有相同参数属性的对象。外部调用方式：store.dispatch('nameAsyn')。

   

    modules：store的子模块，内容就相当于是store的一个实例。调用方式和前面介绍的相似，只是要加上当前子模块名，如：store.a.getters.xxx()。



  使用

     /store/index,js

            import Vue from 'vue'

    import Vuex from 'vuex'

    import modules from './modules'



    Vue.use(Vuex)



    const store = new Vuex.Store({

    modules

    })



    export default store



    /store/modules/.../                  /***子模块***/



 使用例子：

   import { mapGetters, mapMutations, mapstates } from 'vuex'

{

    name: 'useVuexSample',

    watch: {

      'newA': {

         handler: function(old, new) {

           // do something when value changed

         }

      }

    },

    computed: {

      ...mapStates({

         'a': 'newA'

      }),

      ...mapGetters({

         'getA': 'newA1'

      })

    },

    methods: {

       ...mapMutations({

          'setA': 'setA'

       })

    }

}

3.	导出excel表
引用插件：
import FileSaver from 'file-saver'
import XLSX from 'xlsx'
官方github：https://github.com/SheetJS/sheetjs。
XLSX对象
引入js文件后，会在window对象上挂载一个全局对象XLSX，所有的操作函数都通过这个全局对象调用。
workbook对象与worksheet对象
workbook对象是描述一个excel文件的基本对象。
workbook对象中，主要包含了以下内容：
*      SheetNames数组：excel文件包含的所有sheets名称列表
*      Sheets数组：excel文件包含的所有sheets的内容（worksheet对象列表）
 
worksheet对象则包含了一个sheet表格的详细内容。
 
对象中包含的内容包括：
*    !ref：单元格范围
*    !rows：表格行属性
*    !cols：单元格属性
*    !merges：单元格合并
*    A1/B1/C1...：对应excel中的每一个具体的单元格
想要生成一个excel文件，需要首先构建出一个workbook对象，然后再对这个对象进行其他的操作。
workbook对象与worksheet对象看起来比较复杂，很多人看到后会比较焦虑：这些难道要我自己去组织吗？其实，其中包含的很多项都是可以通过内置方法自动生成的，所以不要太担心。
构建一个worksheet对象
在我的项目中，需求是生成一个带有格式要求的excel表格，这里咱们先来说说表格内容部分的生成。
xlsx.js提供了多种函数，将不同结构的数据转换成为worksheet对象，包括：
*    XLSX.utils.aoa_to_sheet：数组转换为worksheet
*    XLSX.utils.json_to_sheet：json对象转换为worksheet
*    XLSX.utils.table_to_sheet：表格转换为worksheet
我的项目中，根据项目情况选择了由数组转换为worksheet的方式，这里我就详细说一下如何构建符合要求的数据数组并将其转换为worksheet对象，其他的方式大家可以移步官方github去查看。

基本的数组结构为：
let arr = [
  [单元格A1内容,单元格A2内容,单元格A3内容,...],
  [单元格B1内容,单元格B2内容,单元格B3内容,...],
  [单元格B1内容,单元格B2内容,单元格B3内容,...],
  ...
]
let arr = [
    ['户主姓名', '性别', '文化程度', '手机号码', 'GPS位置', 'null', '户籍所在地', 'null', 'null', 'null'],
    [null, null, null, null, '纬度', '经度', '盟', '旗', '苏木', '嘎查']
];
这个时候，我们得到的结果是这个样子的：
 
接下来，我们要做的就是合并单元格，比如A1和B1、A2和B2、A5和A6等。
我们需要向生成的ws对象中手动的加入属性!merges及其对应的数组。
ws['!merges'] = [
    {s: {r: 0, c: 0}, e: {r: 1, c: 0}},
    {s: {r: 0, c: 1}, e: {r: 1, c: 1}},
    {s: {r: 0, c: 2}, e: {r: 1, c: 2}},
    {s: {r: 0, c: 3}, e: {r: 1, c: 3}},
    {s: {r: 0, c: 4}, e: {r: 0, c: 5}},
    {s: {r: 0, c: 6}, e: {r: 0, c: 9}}
]
数组的每一项，代表其中一个单元格的合并要求。每一个对象中：s代表合并的起始位置，e代表合并的结束位置。
在s中，r代表行数，c代表列数。
比如数组的第一个对象，表达的含义为：以0行0列（对应单元格A1）作为起始，以1行0列（对应单元格A2）作为结束，合并这些单元格。
行高与列宽
当某一个单元格字符数过长的时候，显示的内容会超过单元格边界，非常影响用户的阅读，这个时候，需要通过调整行高或者列宽来保证表格便于阅读。
我们需要向生成的ws对象中手动加入属性!cols来控制列宽，!rows来控制行高。
例如：
ws['!cols'] = [
    {wpx: 90},
    {wpx: 50},
    {wpx: 60},
    {wpx: 100},
    {wpx: 100},
    {wpx: 100},
    {wpx: 70},
    {wpx: 100},
    {wpx: 100},
    {wpx: 100}
];
数组的每一项对应需要控制的列，比如下标0的项代表第一列（A列）的宽度。
wpx表示屏幕像素值，wch代表英文字符数，还有一个width可选。

生成和导出EXCEL：
//导出excel相关函数
 sheet2blob: function(sheet, sheetName) {
     sheetName = sheetName || 'sheet1';
     let workbook = {
         SheetNames: [sheetName],
         Sheets: {}
     };
     workbook.Sheets[sheetName] = sheet;
     // 生成excel的配置项
     let wopts = {
         bookType: 'xlsx', // 要生成的文件类型
         bookSST: false, // 是否生成Shared String Table，官方解释是，如果开启生成速度会下降，但在低版本IOS设备上有更好的兼容性
         type: 'binary'
     };
     let wbout = XLSX.write(workbook, wopts);
     let blob = new Blob([s2ab(wbout)], {type:"application/octet-stream"});
     // 字符串转ArrayBuffer
     function s2ab(s) {
         let buf = new ArrayBuffer(s.length);
         let view = new Uint8Array(buf);
         for (let i=0; i!=s.length; ++i) view[i] = s.charCodeAt(i) & 0xFF;
         return buf;
     }
     return blob;
 },

//下载
 openDownloadXLSXDialog: function(url, saveName){
     if(typeof url == 'object' && url instanceof Blob){
         url = URL.createObjectURL(url); // 创建blob地址
     }
     let aLink = document.createElement('a');
     aLink.href = url;
     aLink.download = saveName || ''; // HTML5新增的属性，指定保存文件名，可以不要后缀，注意，file:///模式下不会生效
     let event;
     if(window.MouseEvent) event = new MouseEvent('click');
     else{
         event = document.createEvent('MouseEvents');
         event.initMouseEvent('click', true, false, window, 0, 0, 0, 0, 0, false, false, false, false, 0, null);
     }
     aLink.dispatchEvent(event);
 }

 let blob = sheet2blob(ws, '户主信息');
 openDownloadXLSXDialog(blob, '123.xlsx');



布局

 
1.	flex居左
2.	不换行
•	float
•	<div class="div1">123</div>
•	<div class="div2">456</div>
•	<style>
•	.div1 {
•	float: left;
•	}
•	.div2 {
•	float: left;
•	}
•	</style>
•	inline-block
•	
•	
•	<div class="div1">123</div>
•	<div class="div2">456</div>
•	<style>
•	.div1 {
•	display: inline-block;
•	}
•	.div2 {
•	display: inline-block;
•	}
•	</style>
•	flex
•	
•	
•	<div class="parent">
•	<div class="div1">123</div>
•	<div class="div2">456</div>
•	</div>
•	<style>
•	.parent {
•	display: flex;
•	}
•	</style>

4.	元素居中
display:flex
align-item:center
5.	监听窗体变化

