---
layout: post
title: "jQuery Callbacks"
date: 2019-06-06
tag: jQuery
---

jQuery Callbacks 是一个多用途的回调函数列表对象，提供一个强大的方法来管理回调函数队列

常见处理队列的的方法

etc

```javascript
function dowithList (List, cb) {
	setTimeout(function() {
		var task = List.shift();
		task();
		if (List.length > 0) {
			setTimeout(function() {
				dowithList(List, cb);
			}, 1000);
		} else {
			cb && cb();
		}
	}, 25);
}

dowithList([
  function a() {console.log('a');},
  function b() {console.log('b');},
  function c() {console.log('c');}
]);
```

jQuery提供给我们的方式：

```javascript
var callbacks = $.Callbacks();
var a = function() {
	console.log('a');
};
var b = function() {
	console.log('b');
};
callbacks.add(a);
callbacks.add(b);
callbacks.fire();
// outputs: a b
```

上面的执行，简单明了，jQuery提供了一个强大的回调列表对象，来管理回调函数队列。

整体思想依靠pub/sub(发布订阅的模式)来管理回调函数队列的添加与触发执行的操作。

```javascript
// 最简单的pub/sub
var Observer = {
	list: [],
	sub: function(fn) {
		this.list.push(fn);
	},
	pub: function(value) {
		for (var i=0; i<this.list.length; i++) {
			this.list[i](value);
		}
	}
}

// test
var a = function(value) {
	console.log('a: ', value);
}
Observer.sub(a);
Observer.pub(1);
// outputs: a: 1

```

jQuery 里面Callbacks实现的代码也很少，主要以下代码：

```javascript
jQuery.Callbacks = function(optisons) {
  // Convert options from String-formatted to Object-formatted if needed
  // (we check in cache first)
  options = typeof options === "string" ?
    createOptions( options ) :
    jQuery.extend( {}, options );
  
  	var // Flag to know if list is currently firing
      firing,

      // Last fire value for non-forgettable lists
      memory,

      // Flag to know if list was already fired
      fired,

      // Flag to prevent firing
      locked,

      // Actual callback list
      list = [],

      // Queue of execution data for repeatable lists
      queue = [],

      // Index of currently firing callback (modified by add/remove as needed)
      firingIndex = -1,

      // Fire callbacks
      fire = function() {

      },
      self = {
      ...
      };
  return self;
}
```

jQuery.Callbacks返回了一个对象，里面封装回调对象队列一系列的处理逻辑。总结下主要是以下方法

```
add(fn | arr) // 添加函数到回调队列
fire(value) // 触发回调队列函数的执行的触发函数
remove(fn) // 从回调队列中去掉某条数据
has(fn) // 当前数据是否存在于回调队列
empty() // 清空回调队列
disable() // 禁止触发.fire 和 .add方法，并且清空所有的callbacks和值
lock() // 锁住.fire方法，忽略正在构建的执行
locked() // 判断最近的一次是否执行了所有的回调函数
```

从add 到 fire 主要经过这些方法调用

<img src="/images/posts/jQuery/jq-add-to-fire.png" style="width: 300px; text-align: left; margin: 0;" />


self上的add方法可以单独拿来说说

```javascript
add: function() {
  if ( list ) {

    // If we have memory from a past run, we should fire after adding
    if ( memory && !firing ) {
      firingIndex = list.length - 1;
      queue.push( memory );
    }

    ( function add( args ) {
      jQuery.each( args, function( _, arg ) {
        if ( isFunction( arg ) ) {
          if ( !options.unique || !self.has( arg ) ) {
          	list.push( arg );
          }
        } else if ( arg && arg.length && toType( arg ) !== "string" ) {

          // Inspect recursively
          add( arg );
        }
      } );
    } )( arguments );

    if ( memory && !firing ) {
    	fire();
    }
  }
  return this;
}
```

现在对上面的代码稍微进行说明，里面有个立即执行的函数，作用是接收外面传入的`arguemtns`，可以接收单个函数，也可以是函数数组，把接收的值push入list回调队列。



下面继续分析fire方法

```javascript
// 首先是self 的 fire
// Call all the callbacks with the given arguments
var self = {
  // Call all callbacks with the given context and arguments
  fireWith: function( context, args ) {
    if ( !locked ) {
      args = args || [];
      args = [ context, args.slice ? args.slice() : args ];
      queue.push( args );
      if ( !firing ) {
        fire();
      }
    }
    return this;
  },
	fire: function() {
    self.fireWith( this, arguments );
    return this;
  },
}

// Callbacks内定义的fire函数
// Fire callbacks
fire = function() {

  // Enforce single-firing
  locked = locked || options.once;

  // Execute callbacks for all pending executions,
  // respecting firingIndex overrides and runtime changes
  fired = firing = true;
  for ( ; queue.length; firingIndex = -1 ) {
    memory = queue.shift();
    while ( ++firingIndex < list.length ) {

      // Run callback and check for early termination
      if ( list[ firingIndex ].apply( memory[ 0 ], memory[ 1 ] ) === false &&
          options.stopOnFalse ) {

        // Jump to end and forget the data so .add doesn't re-fire
        firingIndex = list.length;
        memory = false;
      }
    }
  }

  // Forget the data if we're done with it
  if ( !options.memory ) {
    memory = false;
  }

  firing = false;

  // Clean up if we're done firing for good
  if ( locked ) {

    // Keep an empty list if we have data for future add calls
    if ( memory ) {
      list = [];

      // Otherwise, this object is spent
    } else {
      list = "";
    }
  }
},

```

触发过程为: self.fire-self.fireWith-fire

fireWith的作用是接收参数，放入queue，这个queue在外层fire中会使用到，主要是给list回调队列里的函数作为参数传递出去，这样之前add进来的函数，就可以触发了，触发的动作是：

```javascript
// Run callback and check for early termination
if ( list[ firingIndex ].apply( memory[ 0 ], memory[ 1 ] ) === false &&
options.stopOnFalse ) {

  // Jump to end and forget the data so .add doesn't re-fire
  firingIndex = list.length;
  memory = false;
}
```



$.Callbacks('once')

确保这个回调队列的函数只执行一次

```javascript
var foo = function(value) {
	console.log('foo: ' + value);
};

// another function to also be added to the list
var bar = function(value) {
	console.log('bar: ' + value);
};

var callbacks = $.Callbacks('once');
// add the function "foo" to the list
callbacks.add([foo, bar]);

// fire the items on the list
callbacks.fire('hello');
callbacks.fire('world');
// outputs foo: hello bar: hello 后面的fire不执行了
```

改变这个行为是由于

```javascript
// Callbacks的fire方法，配置了once参数，会设置optins.once = true
// Enforce single-firing
locked = locked || options.once;

// 第一次fire完，会检查是否有锁住的情况
// Clean up if we're done firing for good
if ( locked ) {

  // Keep an empty list if we have data for future add calls
  if ( memory ) {
    list = [];

    // Otherwise, this object is spent
  } else {
    list = "";
  }
}
// list 回调队列会被重置
// 后续再调用fire就不会有输出了
```

$.Callbacks('unique') 确保一次只能添加一个回调

```javascript
var foo = function(value) {
	console.log('foo: ' + value);
};

var callbacks = $.Callbacks();
callbacks.add([foo, foo]);
callbacks.fire('hello');
// outputs foo hello 只输出一次
```

下面附上Callbacks的源码:

```
jQuery.Callbacks = function( options ) {

    // Convert options from String-formatted to Object-formatted if needed
    // (we check in cache first)
    options = typeof options === "string" ?
        createOptions( options ) :
        jQuery.extend( {}, options );

    var // Flag to know if list is currently firing
        firing,

        // Last fire value for non-forgettable lists
        memory,

        // Flag to know if list was already fired
        fired,

        // Flag to prevent firing
        locked,

        // Actual callback list
        list = [],

        // Queue of execution data for repeatable lists
        queue = [],

        // Index of currently firing callback (modified by add/remove as needed)
        firingIndex = -1,

        // Fire callbacks
        fire = function() {

            // Enforce single-firing
            locked = locked || options.once;

            // Execute callbacks for all pending executions,
            // respecting firingIndex overrides and runtime changes
            fired = firing = true;
            for ( ; queue.length; firingIndex = -1 ) {
                memory = queue.shift();
                while ( ++firingIndex < list.length ) {

                    // Run callback and check for early termination
                    if ( list[ firingIndex ].apply( memory[ 0 ], memory[ 1 ] ) === false &&
                        options.stopOnFalse ) {

                        // Jump to end and forget the data so .add doesn't re-fire
                        firingIndex = list.length;
                        memory = false;
                    }
                }
            }

            // Forget the data if we're done with it
            if ( !options.memory ) {
                memory = false;
            }

            firing = false;

            // Clean up if we're done firing for good
            if ( locked ) {

                // Keep an empty list if we have data for future add calls
                if ( memory ) {
                    list = [];

                // Otherwise, this object is spent
                } else {
                    list = "";
                }
            }
        },

        // Actual Callbacks object
        self = {

            // Add a callback or a collection of callbacks to the list
            add: function() {
                if ( list ) {

                    // If we have memory from a past run, we should fire after adding
                    if ( memory && !firing ) {
                        firingIndex = list.length - 1;
                        queue.push( memory );
                    }

                    ( function add( args ) {
                        jQuery.each( args, function( _, arg ) {
                            if ( isFunction( arg ) ) {
                                if ( !options.unique || !self.has( arg ) ) {
                                    list.push( arg );
                                }
                            } else if ( arg && arg.length && toType( arg ) !== "string" ) {

                                // Inspect recursively
                                add( arg );
                            }
                        } );
                    } )( arguments );

                    if ( memory && !firing ) {
                        fire();
                    }
                }
                return this;
            },

            // Remove a callback from the list
            remove: function() {
                jQuery.each( arguments, function( _, arg ) {
                    var index;
                    while ( ( index = jQuery.inArray( arg, list, index ) ) > -1 ) {
                        list.splice( index, 1 );

                        // Handle firing indexes
                        if ( index <= firingIndex ) {
                            firingIndex--;
                        }
                    }
                } );
                return this;
            },

            // Check if a given callback is in the list.
            // If no argument is given, return whether or not list has callbacks attached.
            has: function( fn ) {
                return fn ?
                    jQuery.inArray( fn, list ) > -1 :
                    list.length > 0;
            },

            // Remove all callbacks from the list
            empty: function() {
                if ( list ) {
                    list = [];
                }
                return this;
            },

            // Disable .fire and .add
            // Abort any current/pending executions
            // Clear all callbacks and values
            disable: function() {
                locked = queue = [];
                list = memory = "";
                return this;
            },

            disabled: function() {
                return !list;
            },

            // Disable .fire
            // Also disable .add unless we have memory (since it would have no effect)
            // Abort any pending executions
            lock: function() {
                locked = queue = [];
                if ( !memory && !firing ) {
                    list = memory = "";
                }
                return this;
            },
            
            locked: function() {
                return !!locked;
            },

            // Call all callbacks with the given context and arguments
            fireWith: function( context, args ) {
                if ( !locked ) {
                    args = args || [];
                    args = [ context, args.slice ? args.slice() : args ];
                    queue.push( args );
                    if ( !firing ) {
                        fire();
                    }
                }
                return this;
            },

            // Call all the callbacks with the given arguments
            fire: function() {
                self.fireWith( this, arguments );
                return this;
            },

            // To know if the callbacks have already been called at least once
            fired: function() {
                return !!fired;
            }
        };

    return self;
};
```

总结：jQuery的Callbacks其实很简洁，只有不到200行代码，核心就是通过观察者模式管理回调队列，建立了一套程序的通信机制。