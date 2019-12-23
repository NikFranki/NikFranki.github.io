---
layout:     post
title:      "jQuery Deferred"
subtitle:   ""
date:       2019-06-08
author:     "franki"
header-img: "images/post-bg-js-module.jpg"
tags:
    - 前端开发
    - jQuery
    - 源码分析
---

在 es6 promise 还未全面普及的情况下，jQuery 的 Deferred 是很好用来解决耗时操作任务(setTimeout、ajax 等)，deferred 字面意思是递延、延迟的意思。常见的耗时的操作或者说异步操作都是通过回调函数处理返回的结果，利用返回的结果在回调函数做其他的操作，若是有超过两个的异步操作，且每一步都依赖上一步的异步任务完成的情况下，就会产生我们常说的回调地狱问题。

于是 jQuery 就创造了 Deferred 这个方法，用来简化异步操作，使得异步任务处理起来像同步任务一样，可以线性编码，提高代码的可读性、健壮性、可维护性。

下面用流程图介绍 Deferred 主要组成部分：

- jQuery.Deferred 函数主要结构

![jQuery.Deferred-func](/images/posts/jQuery/jQuery.Deferred-func.png)

- 内部元组的结构

![tuple](/images/posts/jQuery/tuple.png)

- 内部 promise 对象的结构

![promise](/images/posts/jQuery/promise.png)

- deferred 对象结构

![deferred](/images/posts/jQuery/deferred.png)

下面围绕以上的涉及到的方法、属性细化讲解

进入一个小 demo

```javascript
var d = $.Deferred();

setTimeout(function() {
    d.resolve(22);
}, 0);

d.then(function(v) {
    console.log(v);
});
```

流程图为

![deferred-then-resolve](/images/posts/jQuery/deferred-then-resolve.png)

分解流程首先通过

```javascript
$.Deferred();
```

构建出 deferred 对象，里面如上流程 4 所示。

接着 deferred 对象调用 then 方法

```javascript
d.then(function(v) {
    console.log(v);
});
```

这部分值得好好分析一下

```javascript
function(onFulfilled, onRejected, onProgress) {
    var maxDepth = 0;
    function resolve(depth, deferred, handler, special) {
        return function() {
            var that = this,
                args = arguments,
                mightThrow = function() {
                    var returned, then;

                    // Support: Promises/A+ section 2.3.3.3.3
                    // https://promisesaplus.com/#point-59
                    // Ignore double-resolution attempts
                    if (depth < maxDepth) {
                        return;
                    }

                    returned = handler.apply(that, args);

                    // Support: Promises/A+ section 2.3.1
                    // https://promisesaplus.com/#point-48
                    if (returned === deferred.promise()) {
                        throw new TypeError(
                            'Thenable self-resolution'
                        );
                    }

                    // Support: Promises/A+ sections 2.3.3.1, 3.5
                    // https://promisesaplus.com/#point-54
                    // https://promisesaplus.com/#point-75
                    // Retrieve `then` only once
                    then =
                        returned &&
                        // Support: Promises/A+ section 2.3.4
                        // https://promisesaplus.com/#point-64
                        // Only check objects and functions for thenability
                        (typeof returned === 'object' ||
                            typeof returned ===
                                'function') &&
                        returned.then;

                    // Handle a returned thenable
                    if (isFunction(then)) {
                        // Special processors (notify) just wait for resolution
                        if (special) {
                            then.call(
                                returned,
                                resolve(
                                    maxDepth,
                                    deferred,
                                    Identity,
                                    special
                                ),
                                resolve(
                                    maxDepth,
                                    deferred,
                                    Thrower,
                                    special
                                )
                            );

                            // Normal processors (resolve) also hook into progress
                        } else {
                            // ...and disregard older resolution values
                            maxDepth++;

                            then.call(
                                returned,
                                resolve(
                                    maxDepth,
                                    deferred,
                                    Identity,
                                    special
                                ),
                                resolve(
                                    maxDepth,
                                    deferred,
                                    Thrower,
                                    special
                                ),
                                resolve(
                                    maxDepth,
                                    deferred,
                                    Identity,
                                    deferred.notifyWith
                                )
                            );
                        }

                        // Handle all other returned values
                    } else {
                        // Only substitute handlers pass on context
                        // and multiple values (non-spec behavior)
                        if (handler !== Identity) {
                            that = undefined;
                            args = [returned];
                        }

                        // Process the value(s)
                        // Default process is resolve
                        (special || deferred.resolveWith)(
                            that,
                            args
                        );
                    }
                },
                // Only normal processors (resolve) catch and reject exceptions
                process = special
                    ? mightThrow
                    : function() {
                            try {
                                mightThrow();
                            } catch (e) {
                                if (
                                    jQuery.Deferred
                                        .exceptionHook
                                ) {
                                    jQuery.Deferred.exceptionHook(
                                        e,
                                        process.stackTrace
                                    );
                                }

                                // Support: Promises/A+ section 2.3.3.3.4.1
                                // https://promisesaplus.com/#point-61
                                // Ignore post-resolution exceptions
                                if (depth + 1 >= maxDepth) {
                                    // Only substitute handlers pass on context
                                    // and multiple values (non-spec behavior)
                                    if (handler !== Thrower) {
                                        that = undefined;
                                        args = [e];
                                    }

                                    deferred.rejectWith(
                                        that,
                                        args
                                    );
                                }
                            }
                        };

            // Support: Promises/A+ section 2.3.3.3.1
            // https://promisesaplus.com/#point-57
            // Re-resolve promises immediately to dodge false rejection from
            // subsequent errors
            if (depth) {
                process();
            } else {
                // Call an optional hook to record the stack, in case of exception
                // since it's otherwise lost when execution goes async
                if (jQuery.Deferred.getStackHook) {
                    process.stackTrace = jQuery.Deferred.getStackHook();
                }
                window.setTimeout(process);
            }
        };
    }

    return jQuery
        .Deferred(function(newDefer) {
            // progress_handlers.add( ... )
            tuples[0][3].add(
                resolve(
                    0,
                    newDefer,
                    isFunction(onProgress)
                        ? onProgress
                        : Identity,
                    newDefer.notifyWith
                )
            );

            // fulfilled_handlers.add( ... )
            tuples[1][3].add(
                resolve(
                    0,
                    newDefer,
                    isFunction(onFulfilled)
                        ? onFulfilled
                        : Identity
                )
            );

            // rejected_handlers.add( ... )
            tuples[2][3].add(
                resolve(
                    0,
                    newDefer,
                    isFunction(onRejected)
                        ? onRejected
                        : Thrower
                )
            );
        })
        .promise();
}
```

看代码得知，调用 then 方法后最终还是返回 deferred 对象，保证了链式调用，传入了一个回调函数，这个回调函数在 jQuery.deferred 调用的时候会被执行，这时候就会把对应的 resolve 返回的处理方法添加到 callbacks 的回调队列中，保证 resolve 的时候，回调队列会把添加进的回调函数执行。

resolve 调用

```javascript
deferred[tuple[0]] = function() {
    deferred[tuple[0] + 'With'](
        this === deferred ? undefined : this,
        arguments
    );
    return this;
};

// deferred.notifyWith = list.fireWith
// deferred.resolveWith = list.fireWith
// deferred.rejectWith = list.fireWith
deferred[tuple[0] + 'With'] = list.fireWith;

list.fireWith 其实就是callbacks的对象调用了
```

以上是本人对于 jQuery 的 Deffered 浅析，具体很多用法，要具体比照 api，得出具体的调用过程，以及内部的实现原理，实话说，这部分还是蛮复杂的。

Deferred 全部源码如下：

```javascript
function(func) {
    var tuples = [
        // action, add listener, callbacks,
        // ... .then handls, argument index, [final state]
        [
            'notify',
            'progress',
            jQuery.Callbacks('memory'),
            jQuery.Callbacks('memory'),
            2
        ],
        [
            'resolve',
            'done',
            jQuery.Callbacks('once memory'),
            jQuery.Callbacks('once memory'),
            0,
            'resolved'
        ],
        [
            'reject',
            'fail',
            jQuery.Callbacks('once memory'),
            jQuery.Callbacks('once memory'),
            1,
            'rejected'
        ]
    ],
    state = 'pending',
    promise = {
        state: function() {
            return state;
        },
        always: function() {
            deferred.done(arguments).fail(arguments);
            return this;
        },
        catch: function(fn) {
            return promise.then(null, fn);
        },

        // Keep pipe for back-compat
        pipe: function(/* fnDone, fnFail, fnProgress */) {
            var fns = arguments;

            return jQuery
                .Deferred(function(newDefer) {
                    jQuery.each(tuples, function(i, tuple) {
                        // Map tuples (progress, done, fail) to arguments (done, fail, progress)
                        var fn =
                            isFunction(fns[tuple[4]]) &&
                            fns[tuple[4]];

                        // deferred.progress(function() { bind to newDefer or newDefer.notify })
                        // deferred.done(function() { bind to newDefer or newDefer.resolve })
                        // deferred.fail(function() { bind to newDefer or newDefer.reject })
                        deferred[tuple[1]](function() {
                            var returned =
                                fn && fn.apply(this, arguments);
                            if (
                                returned &&
                                isFunction(returned.promise)
                            ) {
                                returned
                                    .promise()
                                    .progress(newDefer.notify)
                                    .done(newDefer.resolve)
                                    .fail(newDefer.reject);
                            } else {
                                newDefer[tuple[0] + 'With'](
                                    this,
                                    fn ? [returned] : arguments
                                );
                            }
                        });
                    });
                    fns = null;
                })
                .promise();
        },
        then: function(onFulfilled, onRejected, onProgress) {
            var maxDepth = 0;
            function resolve(depth, deferred, handler, special) {
                return function() {
                    var that = this,
                        args = arguments,
                        mightThrow = function() {
                            var returned, then;

                            // Support: Promises/A+ section 2.3.3.3.3
                            // https://promisesaplus.com/#point-59
                            // Ignore double-resolution attempts
                            if (depth < maxDepth) {
                                return;
                            }

                            returned = handler.apply(that, args);

                            // Support: Promises/A+ section 2.3.1
                            // https://promisesaplus.com/#point-48
                            if (returned === deferred.promise()) {
                                throw new TypeError(
                                    'Thenable self-resolution'
                                );
                            }

                            // Support: Promises/A+ sections 2.3.3.1, 3.5
                            // https://promisesaplus.com/#point-54
                            // https://promisesaplus.com/#point-75
                            // Retrieve `then` only once
                            then =
                                returned &&
                                // Support: Promises/A+ section 2.3.4
                                // https://promisesaplus.com/#point-64
                                // Only check objects and functions for thenability
                                (typeof returned === 'object' ||
                                    typeof returned ===
                                        'function') &&
                                returned.then;

                            // Handle a returned thenable
                            if (isFunction(then)) {
                                // Special processors (notify) just wait for resolution
                                if (special) {
                                    then.call(
                                        returned,
                                        resolve(
                                            maxDepth,
                                            deferred,
                                            Identity,
                                            special
                                        ),
                                        resolve(
                                            maxDepth,
                                            deferred,
                                            Thrower,
                                            special
                                        )
                                    );

                                    // Normal processors (resolve) also hook into progress
                                } else {
                                    // ...and disregard older resolution values
                                    maxDepth++;

                                    then.call(
                                        returned,
                                        resolve(
                                            maxDepth,
                                            deferred,
                                            Identity,
                                            special
                                        ),
                                        resolve(
                                            maxDepth,
                                            deferred,
                                            Thrower,
                                            special
                                        ),
                                        resolve(
                                            maxDepth,
                                            deferred,
                                            Identity,
                                            deferred.notifyWith
                                        )
                                    );
                                }

                                // Handle all other returned values
                            } else {
                                // Only substitute handlers pass on context
                                // and multiple values (non-spec behavior)
                                if (handler !== Identity) {
                                    that = undefined;
                                    args = [returned];
                                }

                                // Process the value(s)
                                // Default process is resolve
                                (special || deferred.resolveWith)(
                                    that,
                                    args
                                );
                            }
                        },
                        // Only normal processors (resolve) catch and reject exceptions
                        process = special
                            ? mightThrow
                            : function() {
                                    try {
                                        mightThrow();
                                    } catch (e) {
                                        if (
                                            jQuery.Deferred
                                                .exceptionHook
                                        ) {
                                            jQuery.Deferred.exceptionHook(
                                                e,
                                                process.stackTrace
                                            );
                                        }

                                        // Support: Promises/A+ section 2.3.3.3.4.1
                                        // https://promisesaplus.com/#point-61
                                        // Ignore post-resolution exceptions
                                        if (depth + 1 >= maxDepth) {
                                            // Only substitute handlers pass on context
                                            // and multiple values (non-spec behavior)
                                            if (handler !== Thrower) {
                                                that = undefined;
                                                args = [e];
                                            }

                                            deferred.rejectWith(
                                                that,
                                                args
                                            );
                                        }
                                    }
                                };

                    // Support: Promises/A+ section 2.3.3.3.1
                    // https://promisesaplus.com/#point-57
                    // Re-resolve promises immediately to dodge false rejection from
                    // subsequent errors
                    if (depth) {
                        process();
                    } else {
                        // Call an optional hook to record the stack, in case of exception
                        // since it's otherwise lost when execution goes async
                        if (jQuery.Deferred.getStackHook) {
                            process.stackTrace = jQuery.Deferred.getStackHook();
                        }
                        window.setTimeout(process);
                    }
                };
            }

            return jQuery
                .Deferred(function(newDefer) {
                    // progress_handlers.add( ... )
                    tuples[0][3].add(
                        resolve(
                            0,
                            newDefer,
                            isFunction(onProgress)
                                ? onProgress
                                : Identity,
                            newDefer.notifyWith
                        )
                    );

                    // fulfilled_handlers.add( ... )
                    tuples[1][3].add(
                        resolve(
                            0,
                            newDefer,
                            isFunction(onFulfilled)
                                ? onFulfilled
                                : Identity
                        )
                    );

                    // rejected_handlers.add( ... )
                    tuples[2][3].add(
                        resolve(
                            0,
                            newDefer,
                            isFunction(onRejected)
                                ? onRejected
                                : Thrower
                        )
                    );
                })
                .promise();
        },

        // Get a promise for this deferred
        // If obj is provided, the promise aspect is added to the object
        promise: function(obj) {
            return obj != null
                ? jQuery.extend(obj, promise)
                : promise;
        }
    },
    deferred = {};

    // Add list-specific methods
    jQuery.each(tuples, function(i, tuple) {
        var list = tuple[2],
            stateString = tuple[5];

        // promise.progress = list.add
        // promise.done = list.add
        // promise.fail = list.add
        promise[tuple[1]] = list.add;

        // Handle state
        if (stateString) {
            list.add(
                function() {
                    // state = "resolved" (i.e., fulfilled)
                    // state = "rejected"
                    state = stateString;
                },

                // rejected_callbacks.disable
                // fulfilled_callbacks.disable
                tuples[3 - i][2].disable,

                // rejected_handlers.disable
                // fulfilled_handlers.disable
                tuples[3 - i][3].disable,

                // progress_callbacks.lock
                tuples[0][2].lock,

                // progress_handlers.lock
                tuples[0][3].lock
            );
        }

        // progress_handlers.fire
        // fulfilled_handlers.fire
        // rejected_handlers.fire
        list.add(tuple[3].fire);

        // deferred.notify = function() { deferred.notifyWith(...) }
        // deferred.resolve = function() { deferred.resolveWith(...) }
        // deferred.reject = function() { deferred.rejectWith(...) }
        deferred[tuple[0]] = function() {
            deferred[tuple[0] + 'With'](
                this === deferred ? undefined : this,
                arguments
            );
            return this;
        };

        // deferred.notifyWith = list.fireWith
        // deferred.resolveWith = list.fireWith
        // deferred.rejectWith = list.fireWith
        deferred[tuple[0] + 'With'] = list.fireWith;
    });

    // Make the deferred a promise
    promise.promise(deferred);

    // Call given func if any
    if (func) {
        func.call(deferred, deferred);
    }

    // All done!
    return deferred;
}
```
