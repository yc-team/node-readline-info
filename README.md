node-readline-info
==================

参考：

* [源码地址](https://github.com/joyent/node/blob/master/lib/readline.js)

直接require('readline')就可以使用这个模块。
Readline可以逐行读取一个流(比如：process.stdin)

注意：一旦你调用了这个模块，你的node程序不会终止，知道你关闭了这个接口。

下面有一个程序例子让你正常退出：

```javascript
var readline = require('readline');

var rl = readline.createInterface({
    input: process.stdin,
    output: process.stdout
});

rl.question("What is your name?", function(name){
    console.log("hello:", name);

    //close()
    rl.close();
});
```


### readline.createInterface(options)

创建一个readline的接口实例。接受一个Object类型的参数，有如下的值：

* input      （必须）要监听的可读stream
* output     （必须）要写入readline的可写stream
* completer  （可选）Tab自动补全的函数，见下面例子。
* terminal 



为了接受用户输入，createInterface通常和process.stdin和process.stdout一起来。

```javascript
var readline = require('readline');
var rl = readline.createInterface({
   input: process.stdin,
   output: process.stdout 
});
```



### rl.setPrompt(prompt,length)

设置提示，比如你在命令行执行node，你可用看到>，这个就是node的提示


### rl.prompt([preserveCursor])

将当前调用rl.setPrompt方法设置的内容放在新的一行，给用户新的地方来输入。
把preserveCursor可以防止输入的光标被置在第一个位置


示例：

```javascript
//先设置,5这样的化，输入光标出现在y上面
rl.setPrompt("zhangyaochun", 5);
//再调用
rl.prompt();
```


### rl.question(query,callback)

预先提示指定的query，然后用户应答后触发指定的callback。
显示指定的query给用户后，当用户的应答被输入后，就触发了指定的callback



使用的例子：

```javascript
interface.question('What is your job?', function(job){
    console.log('your job is ' + job);
});
```


### rl.pause()

暂停readline的input流，有需要也可以恢复。


### rl.resume()

恢复readline的input流


### rl.close()

关闭接口的实例，放弃对input和output流的控制。close事件会被同时触发。



### rl.write(data,[key])

往output流里面写指定的data。

只有当terminal是TTY的时候才可用，key是一个对象字面量

使用的例子：

```javascript
rl.write('hello,my name is zhangyaochun');

//这样按住ctrl+u的化会删掉前面所有输入的
//TODO:其他值呢
rl.write(null, {ctrl: true, name: 'u'});
```


## 事件：

### line

在input流接受了一个\n -- 通常当用户输入了enter键或者return键。可以用来很好地监听用户输入。

监听line的例子：

```javascript
rl.on('line', function(cmd){
    console.log('You just entered: ' + cmd);
});
```


### pause

当input流被暂停了，就会触发pause，同样当input流未被暂停，但收到SIGCONT也会触发。

监听pause的例子：

```javascript
rl.on('pause', function(cmd){
    console.log('Emitted when close() is called.');
});
```


### close

当执行rl.close()的时候触发。



### SIGINT

当input流收到ctrl+c会被触发



示例：

```javascript
rl.on('SIGINT', function(){
    rl.question('Are you sure you want to exit?', function(answer){
        if (answer.match(/^y(es)?$/i)) {
            rl.pause();
        }
    });
});
```


### SIGTSTP

> 在Windows上面没有作用


示例：

```javascript
rl.on('SIGTSTP', function(){
    rl.prompt(); 
});
```


### SIGCONT

> 在Windows上面没有作用


示例：

```javascript
rl.on('SIGCONT', function(){
    rl.prompt(); 
});
```


### readline.cursorTo(stream, x, y)

### readline.moveCursor(stream, dx, dy)


### readline.clearLine(stream, dir)

参数dir有如下可选值：

* -1  光标左右的内容
* 1   光标右边的内容
* 0   整个一行