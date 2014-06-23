---
layout: post
title: "IOS Concurrency"
date: 2014-06-23 11:59:37 +0800
comments: true
categories: 
---

Block和函数指针的区别: 函数指针是对一个函数地址的引用，这个函数在编译时就已经确定了。而block是一个函数对象，是在程序运行过程中产生的。在一个作用域中生成的block对象分配在栈上，和其他所有分配在栈上的对象一样，离开这个作用域就不存在了。

dispatch_async_f 功能，我们能够提交指针到程序定义的上下文，用于给定义的C功能函数调用。

Executing Non-UI Related Tasks Synchronously with GCD:

{%codeblock%}
dispatch_queue_t concurrentQueue = 
  dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT,0);

dispatch_async(concurrentQueue,^{
  __block UIImage *image = nil;

  dispatch_sync(concurrentQueue,^{
    /*Download the image here */
  });

 dispatch_sync(dispatch_get_main_queue(), ^{
    /* Show the image to the user here on the main queue */
  });
});
{%endcodeblock%}
