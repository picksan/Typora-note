# s_expression

### 一、课程简介

#### 1.1 实验简介

lisp 因为在数据和代码之间的差距小而出名，它们都使用同样的结构来表示。因此可以让它们做许多更有力的事情而其他语言无法完成。如果我们想发挥这门语言最大的功效，就需要将读取输入和计算输入的模块进行切分。

我们这节课程的代码只会比之前的代码多一点，因为需要先将它内部的原理进行分析。这被称为重构，它将对我们后面的编程有很多的帮助。就像吃饭一样，等待食物到桌子上不意味着浪费时间，反而等待食物的感觉可能比吃食物更有意思。

为了存储我们的程序，需要创建一个内部的列表结构，它将由数字、符号、和其他列表组成。在 `lisp` 中这种结构称为 `S-Expression` 。在 `S-Expression` 中，将开始的元素作为操作符，其后的元素作为操作的对象，然后计算都到结果。

通过 `S-Expression` 的学习，我们终于扣响了 `lisp` 的大门。

#### 1.2 实验知识点

- 堆栈
- 指针
- 代码重构

#### 1.3 实验环境

- Xfce 终端
- GCC
- Gedit

#### 1.4 适合人群

本课程适合有 C 语言基础，想做练手项目的同学，可以有效的学习堆栈，如何在代码中运用好指针来重构代码等相关知识，做一些有趣的事情。

#### 1.5 代码获取

你可以通过下面命令将本课程里的所有源代码下载到实验楼环境中，作为参照对比进行学习。

```bash
wget http://labfile.oss.aliyuncs.com/courses/670/s_expression.c
```

**请尽量按照实验步骤自己写出 C 语言程序，请确认文件保存在目录：“/home/shiyanlou/mpc-master/” 下。**

### 二、实验步骤

指针在 C 中是很复杂的部分，在我们之后的编写中，90% 的时间都会用到它，所以这也是个学习指针的好机会。

我们之所以需要使用指针是由函数的调用造成的。在 C D中调用一个函数是总是通过值传递的，就是说它们拷贝了它们自己进行传递。这对于 `int` ,`long` ，`char` 和 `struct` 类型的比如 `lval` 确实是对的，而且大部分的时候这么用都可以，但是有时候这也会产生一些问题。

比如我们有一个很大的 `struct` 类型的变量，它占据了很大的内存，当我们使用它的时候，就需要拷贝一次，这样长期以后会对资源造成浪费，显然我们不应该这么做，使用指针的好处就体现出来了。

通过地址来代替实际的数据，我们可以通过函数来获取和修改地址，这比直接拷贝数据要好很多，地址只是数字，它比记录数据更省资源，在内存资源固定的情况下，更高效的使用它们显得很重要。

指针使用 `*` 来表示，并通过 `->` 来获取取变量的元素值，变量可以通过 `&` 来获取自己的地址。

#### 2.1 堆栈

我之前说过，内存可以看作由一连串 `lists` 组成的 ，实际上它可以想象成两个模型，栈和堆。

可能你们之前用过堆栈，或者对它们并不熟悉。在 C 中，堆栈的用法可以很复杂，但是并不是说学习它们就很困难。它们不过就是两种在内存中记录数据的方式。

#### 2.1.1 栈

程序运行时就是在栈里面，它也是临时数据和变量存放的地方，每次我们调用函数都会通过栈来存储，当程序完成的时候，它就会释放掉内存资源等待下一次的调用。

我喜欢把栈理解为造房子，每一次我们建造一些新的东西，就需要通过栈获取足够的材料，工具。当我们的工作完成之后，不再需要这些东西了，就可以把它们释放掉，等待下一次的调用。

#### 2.1.2 堆

堆在内存中用于存储生命周期更长的数据。这部分内存数据需要人为的创建和释放。分配一个新的内存时需要用 `malloc` 函数。这个函数将在内存中获取一定大小的空间，并返回一个指针。

当我们使用完这块内存的时候需要调用 `free` 来释放掉资源。使用堆比栈要复杂一些，因为堆需要手动删除资源，如果没有及时的释放资源，可能会造成内存泄露。

#### 2.2 解析表达式

因为现在我们要使用 `S-Expression` 而不是波兰表达式，就需要修改解析器。

> `S-Expression` 是很简单的，它就是由数字，操作符和其他 `S-Expression` 组成。在原先解析器代码上做修改即可，并将之前 `operator` 规则改为 `symbol` ，这是一个添加更多的操作符，变量和函数的方法。

> ```
> mpc_parser_t* Number = mpc_new("number");
> mpc_parser_t* Symbol = mpc_new("symbol");
> mpc_parser_t* Sexpr  = mpc_new("sexpr");
> mpc_parser_t* Expr   = mpc_new("expr");
> mpc_parser_t* Lispy  = mpc_new("lispy");
> 
> mpca_lang(MPCA_LANG_DEFAULT,
>   "                                          \
>     number : /-?[0-9]+/ ;                    \
>     symbol : '+' | '-' | '*' | '/' ;         \
>     sexpr  : '(' <expr>* ')' ;               \
>     expr   : <number> | <symbol> | <sexpr> ; \
>     lispy  : /^/ <expr>* /$/ ;               \
>   ",
>   Number, Symbol, Sexpr, Expr, Lispy);
> ```

在使用完之后记得保存退出：

> ```
> mpc_cleanup(5, Number, Symbol, Sexpr, Expr, Lispy);
> ```

#### 2.3 表达式的结构

我们需要用一种方法来存储 `S-Expression` ，因此我们需要添加两个新的变量到 `enum` 中，第一个是 `LVAL_SYM` 和 `LVAL_SEXPR` ：

> ```
> enum { LVAL_ERR, LVAL_NUM, LVAL_SYM, LVAL_SEXPR };
> ```

> `S-Expression` 由许多其他的变量组成，因次我们将使用一个双指针变量 `cell` 来存储 `lval*` ，这就是 `lval**` ；
>
> 而且还需要记录有多少个 `lval*` ，用 `count` 作为计数器；
>
> 用 `err` 来记录错误的信息，这将使我们的报错信息更加的完整，高效。

> ```c
> typedef struct lval {
>   int type;
>   long num;
>   /* Error and Symbol types have some string data */
>   char* err;
>   char* sym;
>   /* Count and Pointer to a list of "lval*" */
>   int count;
>   struct lval** cell;
> } lval;
> ```

#### 2.4 代码重构

我们可以使用 `lval*` 类型而不是 `lval` 来进行操作，这将提升代码的效率，使用 `malloc` 和 `sizeof` 来分配足够的内存空间。当我们使用完堆之后，记得 `free` 所有的资源：

> ```c
> //指针指向一个新的数字
> lval* lval_num(long x) {
>   lval* v = malloc(sizeof(lval));
>   v->type = LVAL_NUM;
>   v->num = x;
>   return v;
> }
> ```

> ```
> //指针指向一个 error
> lval* lval_err(char* m) {
>   lval* v = malloc(sizeof(lval));
>   v->type = LVAL_ERR;
>   v->err = malloc(strlen(m) + 1);
>   strcpy(v->err, m);
>   return v;
> }
> ```

> ```
> //指针指向一个新的 Symbol
> lval* lval_sym(char* s) {
>   lval* v = malloc(sizeof(lval));
>   v->type = LVAL_SYM;
>   v->sym = malloc(strlen(s) + 1);
>   strcpy(v->sym, s);
>   return v;
> }
> ```

> ```
> //指针指向一个新的 Sexpr
> lval* lval_sexpr(void) {
>   lval* v = malloc(sizeof(lval));
>   v->type = LVAL_SEXPR;
>   v->count = 0;
>   v->cell = NULL;
>   return v;
> }
> ```

`NULL `是一个特别的值指向地址 0， 在很多时候用于没有赋值和空的指针。这里的 `strlen(s)+1` 是因为最后一个字符为 ’\0‘ ，因此需要 +1，在 C\C++ 中，这是一种很好的存储字符串方法。

我们还需要编写一个函数来删除 `lval` ，这个函数将帮助我们释放内存资源，只有我们良好的使用它，就不会造成内存泄漏：

> ```c
> void lval_del(lval* v) {
> 
>   switch (v->type) {
>     /* Do nothing special for number type */
>     case LVAL_NUM: break;
> 
>     /* For Err or Sym free the string data */
>     case LVAL_ERR: free(v->err); break;
>     case LVAL_SYM: free(v->sym); break;
> 
>     /* If Sexpr then delete all elements inside */
>     case LVAL_SEXPR:
>       for (int i = 0; i < v->count; i++) {
>         lval_del(v->cell[i]);
>       }
>       /* Also free the memory allocated to contain the pointers */
>       free(v->cell);
>     break;
>   }
> 
>   /* Free the memory allocated for the "lval" struct itself */
>   free(v);
> }
> ```

#### 2.5 阅读表达式

我们将读取程序，并且计算结果.

- 首先将抽象语法树转为 `S-Expression` ，接着用我们的 lisp 规则计算结果。

- 为了达到计算的目的，我们首先将递归地检索每个节点，通过 `tag` ，和 `contents` 的值区分每一个变量。

  > ```c
  > lval* lval_read_num(mpc_ast_t* t) {
  >   errno = 0;
  >   long x = strtol(t->contents, NULL, 10);
  >   return errno != ERANGE ?
  >     lval_num(x) : lval_err("invalid number");
  > }
  > 
  > lval* lval_read(mpc_ast_t* t) {
  > 
  >   /* If Symbol or Number return conversion to that type */
  >   if (strstr(t->tag, "number")) { return lval_read_num(t); }
  >   if (strstr(t->tag, "symbol")) { return lval_sym(t->contents); }
  > 
  >   /* If root (>) or sexpr then create empty list */
  >   lval* x = NULL;
  >   if (strcmp(t->tag, ">") == 0) { x = lval_sexpr(); }
  >   if (strstr(t->tag, "sexpr"))  { x = lval_sexpr(); }
  > 
  >   /* Fill this list with any valid expression contained within */
  >   for (int i = 0; i < t->children_num; i++) {
  >     if (strcmp(t->children[i]->contents, "(") == 0) { continue; }
  >     if (strcmp(t->children[i]->contents, ")") == 0) { continue; }
  >     if (strcmp(t->children[i]->contents, "}") == 0) { continue; }
  >     if (strcmp(t->children[i]->contents, "{") == 0) { continue; }
  >     if (strcmp(t->children[i]->tag,  "regex") == 0) { continue; }
  >     x = lval_add(x, lval_read(t->children[i]));
  >   }
  > 
  >   return x;
  > }
  > ```

- 为了添加新的 `S-Expression`，我们创建了一个函数 `lval_add` 。

  > ```c
  > lval* lval_add(lval* v, lval* x) {
  >   v->count++;
  >   v->cell = realloc(v->cell, sizeof(lval*) * v->count);
  >   v->cell[v->count-1] = x;
  >   return v;
  > }
  > ```

- 接着用 `realloc` 来重新分配空间。

#### 2.6 输出表达式

我们已经很接近最终的改变了，现在需要修改输出的模块，输出 `S-Expressions` ，为了输出结果，我们创建了一个函数遍历所有的表达式的子表达式：

> ```
> void lval_expr_print(lval* v, char open, char close) {
>   putchar(open);
>   for (int i = 0; i < v->count; i++) {
> 
>     /* Print Value contained within */
>     lval_print(v->cell[i]);
> 
>     /* Don't print trailing space if last element */
>     if (i != (v->count-1)) {
>       putchar(' ');
>     }
>   }
>   putchar(close);
> }
> 
> void lval_print(lval* v) {
>   switch (v->type) {
>     case LVAL_NUM:   printf("%li", v->num); break;
>     case LVAL_ERR:   printf("Error: %s", v->err); break;
>     case LVAL_SYM:   printf("%s", v->sym); break;
>     case LVAL_SEXPR: lval_expr_print(v, '(', ')'); break;
>   }
> }
> 
> void lval_println(lval* v) { lval_print(v); putchar('\n'); }
> ```

这两个函数相互调用，看起来很复杂，实际上比较容易理解，而且后面还会用到，所以好好记住哦！

在我们的主循环中，现在可以删掉不必的资源了，然后调用阅读表达式和输出表达式：

> ```
> lval* x = lval_read(r.output);
> lval_println(x);
> lval_del(x);
> ```

#### 2.7 计算表达式

在计算中如果出现了错误，就调用 `lval_take` 。

在 `S-Expression` 中，如果有多个子节点需要需要分析，创建了一个 `lval_pop` 函数，如果一个表达式我们已经检索过了，分析过了，可以用 `builtin_op` 来处理我们的计算。

如果表达式不合法，我们将直接跳过它，并返回一个错误信息：

> ```c
> lval* lval_eval_sexpr(lval* v) {
> 
>   /* Evaluate Children */
>   for (int i = 0; i < v->count; i++) {
>     v->cell[i] = lval_eval(v->cell[i]);
>   }
> 
>   /* Error Checking */
>   for (int i = 0; i < v->count; i++) {
>     if (v->cell[i]->type == LVAL_ERR) { return lval_take(v, i); }
>   }
> 
>   /* Empty Expression */
>   if (v->count == 0) { return v; }
> 
>   /* Single Expression */
>   if (v->count == 1) { return lval_take(v, 0); }
> 
>   /* Ensure First Element is Symbol */
>   lval* f = lval_pop(v, 0);
>   if (f->type != LVAL_SYM) {
>     lval_del(f); lval_del(v);
>     return lval_err("S-expression Does not start with symbol!");
>   }
> 
>   /* Call builtin with operator */
>   lval* result = builtin_op(v, f->sym);
>   lval_del(f);
>   return result;
> }
> ```

> ```c
> lval* lval_eval(lval* v) {
>   /* Evaluate Sexpressions */
>   if (v->type == LVAL_SEXPR) { return lval_eval_sexpr(v); }
>   /* All other lval types remain the same */
>   return v;
> }
> ```

这里还有两个函数没有介绍，`lval_pop` 和 `lval_take` ，它们主要用于内存资源使用后的回收问题：

> ```c
> lval* lval_pop(lval* v, int i) {
>   /* Find the item at "i" */
>   lval* x = v->cell[i];
> 
>   /* Shift memory after the item at "i" over the top */
>   memmove(&v->cell[i], &v->cell[i+1],
>     sizeof(lval*) * (v->count-i-1));
> 
>   /* Decrease the count of items in the list */
>   v->count--;
> 
>   /* Reallocate the memory used */
>   v->cell = realloc(v->cell, sizeof(lval*) * v->count);
>   return x;
> }
> ```

> ```c
> lval* lval_take(lval* v, int i) {
>   lval* x = lval_pop(v, i);
>   lval_del(v);
>   return x;
> }
> ```

我们还需要定义函数 `builtin_op` ，它类似于 `eval_op` ，但是这里我们会做更严格的错误检查，就算是小问题也需要返回错误信息：

> ```
> lval* builtin_op(lval* a, char* op) {
> 
>   /* Ensure all arguments are numbers */
>   for (int i = 0; i < a->count; i++) {
>     if (a->cell[i]->type != LVAL_NUM) {
>       lval_del(a);
>       return lval_err("Cannot operate on non-number!");
>     }
>   }
> 
>   /* Pop the first element */
>   lval* x = lval_pop(a, 0);
> 
>   /* If no arguments and sub then perform unary negation */
>   if ((strcmp(op, "-") == 0) && a->count == 0) {
>     x->num = -x->num;
>   }
> 
>   /* While there are still elements remaining */
>   while (a->count > 0) {
> 
>     /* Pop the next element */
>     lval* y = lval_pop(a, 0);
> 
>     if (strcmp(op, "+") == 0) { x->num += y->num; }
>     if (strcmp(op, "-") == 0) { x->num -= y->num; }
>     if (strcmp(op, "*") == 0) { x->num *= y->num; }
>     if (strcmp(op, "/") == 0) {
>       if (y->num == 0) {
>         lval_del(x); lval_del(y);
>         x = lval_err("Division By Zero!"); break;
>       }
>       x->num /= y->num;
>     }
> 
>     lval_del(y);
>   }
> 
>   lval_del(a); return x;
> }
> ```

现在计算机可以处理我们的程序了，在开始之前，还需要一点代码来操作整个流程：

> ```
> lval* x = lval_eval(lval_read(r.output));
> lval_println(x);
> lval_del(x);
> ```

**注意 s_expression.c 须放在 /home/shiyanlou/mpc-master/ 目录下：**

**请参照 1.5 节的链接内容，编写源程序 s_expression.c。**

编译后运行的结果如下：

![5-2.7-1](https://doc.shiyanlou.com/document-uid735639labid2201timestamp1527760966227.png)

### 三、实验总结

本章最主要的目的就是将原程序中的代码加上指针。提高它在内存中的资源使用效率，这也是 C\C++ 程序中很重要的一点，学会合理使用指针，将代码写的好看又实用。而且还学习了堆栈，它们也是很重要很常见的知识，在操作系统中有很好的介绍。