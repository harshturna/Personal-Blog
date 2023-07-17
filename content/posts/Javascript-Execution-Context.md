+++
author = "Harsh"
title = "JavaScript's Execution Context"
date = "2022-12-12"
description = "Why do we care about the Execution Context?"
tags = [
    "javascript internals",
    "web",
    "web development",
    "javascript"
]
+++

#### _Why do we care about the Execution Context?_

Execution context forms the basis of understanding critical Javascript concepts such as hoisting and scope. <br>As such, execution context will help us understand some quirks and "weird" behaviour of JavaScript.

### What is Execution Context?

Before looking at execution context in action, let's first define what execution context is. <br>
Execution context is a wrapper containing information about the code currently being evaluated and executed. In other words, it's the environment in which the code is executed.

#### Types of Execution context

1. Global Execution Context
2. Function Execution Context

This post will cover the Global Execution Context in detail as the knowledge of the Global Execution Context lays the groundwork for understanding Function execution context and the need for it. <br>

From here on, when we mention Execution Context, we will be referring to the Global Execution Context.

### Global Execution Context

When a javascript file starts running, a base execution context is created. The base execution context is called the Global execution context. <br>
When the Global execution context is created, we get access to a few things; **the Global Object **and **'this'**. Let's look at the Global Object and 'this' in practice.<br>

- **Create a boilerplate HTML file and an empty javascript file and link them.**
  ![e-demo-html.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1666564231687/jW3x7Lnrs.png align="left")

  > Link the HTML file to an empty javascript file (as done on line 13)

- **Open the HTML file in a browser and open up the console in the dev tools.**

  ![Screen Shot 2022-10-23 at 5.37.28 PM.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1666564666853/aL4BAQl_3.png align="left")

- **Type window in the console**

  ![Screen Shot 2022-10-23 at 5.41.16 PM.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1666564895314/Cfeovi_x3.png align="left")
  **window** is the browser's global object which gets created anytime a javascript file runs (Node and other javascript runtime environments have different Global Objects).

- **Let's now look at the keyword 'this'. Type this in the console**
  ![Screen Shot 2022-10-23 at 5.44.48 PM.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1666565104238/eLPe6Zh0y.png align="left")
  Notice, **'this'** is the same as the browser's Global Object - window, i.e. 'this' is a reference to the window object. <br>
  So, anytime the Global execution context is created, a global object (window, in the case of the browser) is created, and its reference is passed to 'this'.
  > You can look at the Node's global object by opening a Node REPL and typing 'this'

### Phases of Execution Context

Before looking at how things change in the execution context when we write some code inside our javascript file, let's first understand the Phases of Execution Context.

#### Phase 1: Creation Phase

1. The global object is created in the creation phase (We already looked at the global object).
2. 'this' is created, and it is bound to the global object.
3. The memory heap is set up, and variables, and function declarations are stored inside the memory heap (We will come back and look at this in detail).

#### Phase 2: Execution Phase

1. Code is executed line by line:

- Values are assigned to variables
- Functions are invoked (or called).

### Creation phase and Execution phase in practice

Let's finally write some code in our javascript file and understand the creation and execution phases.

![e-demo-js1.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1666565900818/8YN230SEW.png align="left")

> Variable int is declared with var intentionally. We will look at ES6's let and const later.

Let's save and run our file, and see what we get in the console.

![Screen Shot 2022-10-23 at 6.00.11 PM.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1666566032302/TNRuWEMXI.png align="left")

**We get the expected output - **

1. The value of variable int is printed to the console.
2. The log statement inside the loggerFunction is printed to the console.

Now, let's change our code a bit.

![e-demo-js2.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1666566306922/srElV9nlP.png align="left")

**We made the following changes - **

1. We are logging the variable int to the console before it is declared and initialized.
2. We are invoking the loggerFunction before it is declared.

Before running the code, let's understand this code inside the phases of execution code. <br>
We already know that in the **creation phase**, a memory heap is set up within which variables, and function declarations are stored.

**Creation Phase in our code**,

1. window object is created, and its reference is set to 'this'
2. variable int and loggerFunction declarations are stored inside the memory heap. <br>

In the **execution phase**, the code is executed line by line. In this phase, values are assigned to variables and functions are invoked.

**Execution Phase in our code, **

1. The variable int is logged to the console (**NOTE:** int has not yet been assigned any value)
2. Once the int is logged to the console, the next line assigns value to the variable int.
3. loggerFunction is invoked.

Let's run our code and look at the output in the console:

![Screen Shot 2022-10-23 at 6.09.54 PM.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1666566609212/8xltSYwW4.png align="left")

**Let's understand what happened here -**

1. **The value of variable int is undefined:** We did not get an error for logging the variable int to the console before it was declared and initialized because during the creation phase (before even a single line is executed), the declaration of variable int was stored in the memory.<br>
   The variable int is set to **'undefined'** because it has not yet been assigned any value (assignment happens in the execution phase).

2. **The loggerFunction is invoked before it is declared:** We were able to invoke the loggerFunction before it was declared because, in the creation phase, function declarations are stored in the memory, so, we had access to the loggerFunction declaration when it was invoked.

> The behaviour we saw with our variable and function declaration is what we call **Hoisting**.

#### Let and Const are not Hoisited.

> Variables declared with let and const are not hoisted.

Let's declare our variable int with let and see the output.

![e-demo-js3.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1666566995903/oUAeLGpEZ.png align="left")

When we run the code we get the error:

![Screen Shot 2022-10-23 at 6.17.11 PM.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1666567053999/0AWFr2joS.png align="left")

As mentioned above, this is because let and const are not hoisted.
We will learn about Hoisting in detail in another post.

---

### globalThis

We now know that different javascript environments have different global objects. Browser's global object is the window, which is different from Node's global object, which might be different from other environments. <br>
When a program is written for multiple environments, it becomes complicated to consistently refer to the global object of an environment. <br>
This was solved with the introduction of globalThis in ES2020. You can learn more about globalThis [here](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/globalThis).
