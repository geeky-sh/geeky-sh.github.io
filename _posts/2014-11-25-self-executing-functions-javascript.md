---
layout: post
title: Self-Executing functions in Javascript
---

# Self executing functions in Javascript


### Reason for using self-executing functions:


Sometimes, we would want to write a complex function in javascript. In order to write that we would make use of multiple functions that would independently serve as blackboxes. We would need to define tons of variables to acccomplish the logic. But to the user, we woould only want to expose one or two functions to be used by him. Also, we don't want our variables to pollute the global namespace. 

Now, comes the utility of self-executing functions. There are two ways we can define them

```javascript
!function(){}();

(function())();
```

A function when placed inside parenthesis or beside ! can be visualized to send a return value. The value in the parenthesis beside the function can be seen as the one which is passed to the function. Normally, this value is the global variable to which we want our new function to get attached to. For example if I want to add a function named `addColor` to the jquery `$`, I would do this

```javascript
!function($){
  $.addColor = function(){
    $('div').css('color', 'red')
  }
}(window.jQuery)
```

Though this is not a good way to add a jquery function (we should use extend instead), but it illustrates my point. Also any variables defined in the function can't be accessed in the browser i.e they are not defined in the global namespace. 

Normally, every jquery plugin is defined this way.  

Also the reason why this is called a self-executing function is because we don't have to call the function to execute. It is defined and executed simultaneously




#### References:
http://stackoverflow.com/questions/15745871/what-does-function-window-jquery-means
http://stackoverflow.com/questions/2937227/what-does-function-jquery-mean
http://stackoverflow.com/questions/9856288/jquery-and-twitter-bootstrap-function-explanation
http://stackoverflow.com/questions/592396/what-is-the-purpose-of-a-self-executing-function-in-javascript