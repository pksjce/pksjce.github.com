---
layout: post
category : lessons
tags :
tagline: eval all the way
---

{% include JB/setup %}

##Objective

The objective of this exercise is to build a working javascript console on the browser. 

##Analysis

The functionality of this console must be as follows -  
1. Should interpret only code that is input into the console box.This maybe single line or multiple lines.
2. Should maintain the history of what was interpreted before and print this below the console.
3. Should show any errors thrown during evaluation.


##Solution

###Trial 1

####Test cases -  
(Initial) 
1. var s = 1; --> undefined  
2. s --> 1;  
3. z --> Error:z is not defined.

The first thought on interpreting javascript is to use eval() method and start printing its results.  
Add a try/catch to catch any errors and display them;

####Javascript
{%highlight javascript%}
	
$(document).ready(function(){
	var consoley = $('#console');
	consoley.keypress(function(evt){
		if(evt.keyCode === 13){
			var js = consoley.val();
			var x;
			var type = 'result';
            
			try{
				x = eval(js);
			}catch(e){
				x = e.message;
				type= 'error';
			}
			$('#userjs').html(js);
			var ans = make_nice(x, type);
			$('#answer').html(ans);
		}
	});
});

function make_nice(x, type){
	if(type == "result"){
		if(x === undefined){
			return "undefined";
		}	
	} else{
		return "Error: " + x;
	}
    return x;
	
}
{%endhighlight%}

####HTML
{%highlight HTML%}
	<html>
		<head>
			<script src='jquery.js'>
			</script>
			<script src='main.js'>
			</script>
		</head>
		<body>
			<div>
				<input type = 'text' id='console'>
			</div>
			<div id='userjs'>
			</div>
			<div id='answer'>
			</div>
		</body>
	</html>
{%endhighlight%}


Since undefined cannot be printed, I had to make a <code>make_nice</code> method where I set <code>undefined</code> as the output.

This method passes the first and third test cases but fails the second one.
The reason for this as per [Context for evals](https://weblogs.java.net/blog/driscoll/archive/2009/09/08/eval-javascript-global-context), is that eval is executed in the scope of the callback function. This scope is different every different time the callback function is called. Hence the result of the first eval was effectively "deleted" and new scope was created for the next event callback. The solution for this is to call eval with scope that persists and in our case global scope.

###Trial 2

Call <code>eval()</code> as <code>window.eval()</code>

With no change in html lets change the keypress callback to 

####Javascript
{%highlight javascript%}
	
	consoley.keypress(function(evt){
		if(evt.keyCode === 13){
			var js = consoley.val();
			var x;
			var type = 'result';
            
			try{
				x = eval(js);
			}catch(e){
				x = e.message;
				type= 'error';
			}
			$('#userjs').html(js);
			var ans = make_nice(x, type);
			$('#answer').html(ans);
		}
	});

{%endhighlight%}

This change seems to have done the trick and is passing all 3 of our test cases.  
Let's create another test case.
Add below snippet of code to the javascript of the page.
{%highlight javascript%}
	var s = 1;
{%endhighlight%}

Now we see that we are able to override the value of <code>s</code> in our mock console. This is highly undesirable behaviour. We do not want users of our console to be able to modify the objects on the page itself and thus mess with the functionality of the page.

Solutions for this could be -   

###Trial 3  

Create a separate object and execute eval in its scope.

{%highlight javascript%}
var s = 2;
$(document).ready(function(){
	var consoley = $('#console');
	consoley.keypress(function(evt){
		if(evt.keyCode === 13){
			var js = consoley.val();
			var x;
			var type = 'result';
            var mockConsole = new mock_console();
			try{
				x = mockConsole.log(js);
			}catch(e){
				x = e.message;
				type= 'error';
			}
			$('#userjs').html(js);
			var ans = make_nice(x, type);
			$('#answer').html(ans);
		}
	});
});

var mock_console = function(){
	return {
		log:function(str){
			var result = eval.call(this, str);
			return result;
		}
	}
}
{%endhighlight%}

This still does not solve our problem because the global scope can still be modified through this function.


###Trial 4  

After a lot of search, the only way to escape context of the page alltogether is to execute the eval in an iframe. This is the only place where another html page can be created and still be accessed by our page.

So I created an iframe by   
{%highlight HTML%}
	<iframe id='myframe' style='display:none'>
	</iframe>
{%endhighlight%}

By changing my mockconsole to point to iframe's contentWindow, we have -   
{%highlight javascript%}
	var mockConsole = document.getElementById('myframe').contentWindow;
	try{
		x = mockConsole.eval(js);
	}catch(e){
		x = e.message;
		type= 'error';
	}
{%endhighlight%}  

Now, when we try to evaluate <code>s</code>, we are not able to access the globally defined <code>s = 2</code>  

Its a pass!



