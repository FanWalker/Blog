---
title: JavaScript之命名函数表达式
date: 2017-08-30 20:03:03
tags:
	- JavaScript
---

所谓命名函数表达式就是被赋予了名字的函数表达式，和它相对的是匿名函数，从简单的角度来看，命名函数表达式可以在调试器或性能分析程序中描述函

数的名称，在调试过程中有着不小的作用，因为通常在跨浏览器开发中都会出现一些小毛病，命名函数表达式可以帮助我们解决它们。

先来看一下函数表达式与函数声明以及现代调试器如何处理它们之类的内容。

## 函数表达式与函数声明 ##

通常，我们可以通过函数表达式与函数声明来创建函数对象的方法。但是具体来说什么是函数表达式、什么是函数声明呢？在ECMA规范中明确了一点，函

数声明必须始终带有标志符——即函数名，而函数表达式可以省略这个标识符，如：
<!-- more -->
	
	//函数声明
	function 函数名称(参数:可选){ 函数体 }

	//函数表达式
	function 函数名称（可选）(参数:可选){ 函数体 }

但是如果函数表达式没有省略函数名，这时候函数声明与函数表达式就容易混淆。这时候ECMAScript是通过上下文来区分两者。比如，function foo(){} 是一

个赋值表达式的一部分，则认为它是一个函数表达式，如果 function foo(){} 被包含在一个函数体内，或者位于程序（的最上层）中，则将它作为一个函数声

明来解析：

    function foo(){}; // 声明，因为它是程序的一部分

    var bar = function foo(){}; // 表达式，因为它是赋值表达式（AssignmentExpression）的一部分

    new function bar(){}; // 表达式，因为它是New表达式（NewExpression）的一部分

    (function(){
       function bar(){}; // 声明，因为它是函数体（FunctionBody）的一部分
    })();

还有一种就是被包含在一对圆括号中的函数—— (function foo(){})。将这种形式看成表达式同样是因为上下文的关系：'(' 和 ')'构成一个分组操作符，而分组

操作符只能包含表达式：

    function foo(){}; // 函数声明

    (function foo(){}); // 函数表达式：注意它被包含在分组操作符中
  
    try {
      (var x = 5); // 分组操作符只能包含表达式，不能包含语句（这里的var就是语句）
    } catch(err) {
      // SyntaxError
    }

在使用eval对JSON进行执行的时候，JSON字符串通常被包含在一个圆括号里：eval('(' + json + ')')，这样做的原因就是因为分组操作符，也就是这对括号，

'(' 和 ')'会让解析器强制将JSON的花括号解析成表达式而不是代码块。

    try {
      { "x": 5 }; // "{" 和 "}" 会被解析成代码块
    } catch(err) {
      // SyntaxError
    }
  
    ({ "x": 5 }); // 分组操作符强制将"{" 和 "}"作为对象字面量来解析

声明和表达式的行为存在着十分微妙而又十分重要的差别。首先，函数声明会在任何表达式被解析和求值之前先行被解析和求值。即使声明位于源代码中的

最后一行，它也会先于同一作用域中位于最前面的表达式被求值。还是看个例子更容易理解。在下面这个例子中，函数 fn 是在 alert 后面声明的。但是，在 

alert 执行的时候，fn已经有定义了：

    alert(fn());

    function fn() {
      return 'Hello world!';
    }

**函数声明有一个重要的注意事项**：就是千万不要在条件语句中使用函数声明，而要使用函数表达式。因为通过条件语句控制函数声明的行为并未标准化，

因此不同环境下可能会得到不同的结果。
	
	  // 千万不要这样做！
	  // 有的浏览器会把foo声明为返回first的那个函数
	  // 而有的浏览器则会让foo返回second
	
	  if (true) {
	    function foo() {
	      return 'first';
	    }
	  }
	  else {
	    function foo() {
	      return 'second';
	    }
	  }
	  foo();

	  // 这种情况下要使用函数表达式：
	  var foo;
	  if (true) {
	    foo = function() {
	      return 'first';
	    };
	  }
	  else {
	    foo = function() {
	      return 'second';
	    };
	  }
	  foo();

函数声明的实际规则如下：

FunctionDeclaration（函数声明）只能出现在Program（程序）或FunctionBody（函数体）内。

从句法上讲，它们 不能出现在Block（块）（{ ... }）中，例如不能出现在 if、while 或 for 语句中。因为 Block（块） 中只能包含Statement（语句）， 而不能包含FunctionDeclaration（函数声明）这样的SourceElement（源元素）。

另一方面，唯一可能让Expression（表达式）出现在Block（块）中情形，就是让它作为ExpressionStatement（表达式语句）的一部分。但是，规范明确规定了ExpressionStatement（表达式语句）不能以关键字function开头。而这实际上就是说，FunctionExpression（函数表达式）同样也不能出现在Statement（语句）或Block（块）中（别忘了Block（块）就是由Statement（语句）构成的）。

## 命名函数表达式 ##

函数表达式实际上还是很常见的。Web开发中有一个常用的模式，即基于对某种特性的测试来“伪装”函数定义，从而实现性能最优化。由于这种伪装通常都

出现在相同的作用域中，因此基本上一定要使用函数表达式。毕竟，如前所述，不应该根据条件来执行函数声明：

	  var contains = (function() {
	    var docEl = document.documentElement;
	
	    if (typeof docEl.compareDocumentPosition != 'undefined') {
	      return function(el, b) {
	        return (el.compareDocumentPosition(b) & 16) !== 0;
	      }
	    }
	    else if (typeof docEl.contains != 'undefined') {
	      return function(el, b) {
	        return el !== b && el.contains(b);
	      }
	    }
	    return function(el, b) {
	      if (el === b) return false;
	      while (el != b && (b = b.parentNode) != null);
	      return el === b;
	    }
	  })();

提到命名函数表达式，很显然，指的就是有名字（技术上称为标识符）的函数表达式。在最前面的例子中，var bar = function foo(){};实际上就是一个以foo

作为函数名字的函数表达式。对此，有一个细节特别重要，请大家一定要记住，即这个名字只在新定义的函数的作用域中有效——规范要求标识符不能在外

围的作用域中有效：

	  var f = function foo(){
	    return typeof foo; // foo只在内部作用域中有效
	  };
	  // foo在“外部”永远是不可见的
	  typeof foo; // "undefined"
	  f(); // "function"

有名字的函数可以让调试过程更加方便。在调试应用程序时，如果调用栈中的项都有各自描述性的名字，那么调试过程带给人的就是另一种完全不同的感

受。

## 调试器中的函数名 ##

在函数有相应标识符的情况下，调试器会将该标识符作为函数的名字显示在调用栈中。有的调试器（例如Firebug）甚至会为匿名函数起个名字并显示出来，

让它们与那些引用函数的变量具有相同的角色。可遗憾的是，这些调试器通常只使用简单的解析规则，而依据简单的解析规则提取出来的“名字”有时候没有

多大价值，甚至会得到错误结果。

下面我们来看一个简单的例子：

	  function foo(){
	    return bar();
	  }
	  function bar(){
	    return baz();
	  }
	  function baz(){
	    debugger;
	  }
	  foo();
	
	  // 这里使用函数声明定义了3个函数
	  // 当调试器停止在debugger语句时，
	  // Firgbug的调用栈看起来非常清晰：
	  baz
	  bar
	  foo
	  expr_test.html()

我们可以清晰地知道foo调用了bar，而后者接着又调用了baz（而foo本身又在expr_test.html文档的全局作用域中被调用）。但真正值得称道的，则是

Firebug会在我们使用匿名表达式的情况下，替我们解析函数的“名字”：

	  function foo(){
	    return bar();
	  }
	  var bar = function(){
	    return baz();
	  }
	  function baz(){
	    debugger;
	  }
	  foo();
	
	  // 调用栈：
	  baz
	  bar()
	  foo
	  expr_test.html()

相反，不那么令人满意的情况是，当函数表达式复杂一些时（现实中差不多总是如此），调试器再如何尽力也不会起多大的作用。结果，我们只能在调用栈中

显示函数名字的位置上赫然看到一个问号：

	  function foo(){
	    return bar();
	  }
	  var bar = (function(){
	    if (window.addEventListener) {
	      return function(){
	        return baz();
	      }
	    }
	    else if (window.attachEvent) {
	      return function() {
	        return baz();
	      }
	    }
	  })();
	  function baz(){
	    debugger;
	  }
	  foo();
	
	  // 调用栈：
	  baz
	  (?)()
	  foo
	  expr_test.html()

此外，当把一个函数赋值给多个变量时，还会出现一个令人困惑的问题：

	  function foo(){
	    return baz();
	  }
	  var bar = function(){
	    debugger;
	  };
	  var baz = bar;
	  bar = function() { 
	    alert('spoofed');
	  }
	  foo();
	
	  // 调用栈：
	  bar()
	  foo
	  expr_test.html()

可见，调用栈中显示的是foo调用了bar。但实际情况显然并非如此。之所以会造成这种困惑，完全是因为baz与另一个函数——包含代码alert('spoofed');的函

数——“交换了”引用所致。实事求是地说，这种解析方式在简单的情况下固然好，但对于不那么简单的大多数情况而言就没有什么用处了。

归根结底，只有命名函数表达式才是产生可靠的栈调用信息的唯一途径。下面我们有意使用命名函数表达式来重写前面的例子。请大家注意，从自执行包装

块中返回的两个函数都被命名为了bar：

	  function foo(){
	    return bar();
	  }
	  var bar = (function(){
	    if (window.addEventListener) {
	      return function bar(){
	        return baz();
	      }
	    }
	    else if (window.attachEvent) {
	      return function bar() {
	        return baz();
	      }
	    }
	  })();
	  function baz(){
	    debugger;
	  }
	  foo();
	
	  // 这样，我们就又可以看到清晰的调用栈信息了！
	  baz
	  bar
	  foo
	  expr_test.html()

OK，我们又学到了一招。

参考文章：[命名函数表达式探秘](http://www.jb51.net/onlineread/named-function-expressions-demystified/)