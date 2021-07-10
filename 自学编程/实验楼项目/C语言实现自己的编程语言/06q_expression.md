# q_expression

### 一、课程简介

#### 1.1 实验简介

接下来的篇章中我们将介绍一些新的特性，下表中展示了这些特性，在本节课中我们主要学习 `Q-Expression` ：

> ```bash
> Syntax                添加新的语法规则
> Representation        添加新的变量类型
> Parsing                添加新的函数解析语法树
> Semantics            添加新的函数评估和操作这些特性
> ```

#### 1.2 实验知识点

- 内建函数
- 宏命令
- 断言

#### 1.3 实验环境

- Xfce 终端
- GCC
- Gedit

#### 1.4 适合人群

本课程适合有 C 语言基础，想做练手项目的同学，可以有效的学习内建函数，使用宏和断言相关知识，学习新的表达式，做一些有趣的事情。

#### 1.5 代码获取

你可以通过下面命令将本课程里的所有源代码下载到实验楼环境中，作为参照对比进行学习。

```bash
wget http://labfile.oss.aliyuncs.com/courses/670/q_expression.c
```

**请尽量按照实验步骤自己写出 C 语言程序，请确认文件保存在目录：“/home/shiyanlou/mpc-master/” 下。**

### 二、实验步骤

这一节我们将学习一种新的 lisp 数据类型 `Q-Expression` ，它并不会做计算的操作，而是对其他的 `lisp` 变量比如数字，符号，和其他的 `S-Expressions` 等进行存储和操作。

> 在其他的 `lisp` 中 `Q-Expressions` 并不存在， 其他的 `lisp` 使用宏命令，我们这里使用 `Q-Expressions` 来代替宏命令，甚至比宏命令更好。

`Q-Expressions` 非常的像 `S-Expressions` ，它们唯一的区别是 `Q-Expressions` 用 {} ，而 `S-Expressions` 用 () ，具体的语法区别如下：

> ```c
> mpc_parser_t* Number = mpc_new("number");
> mpc_parser_t* Symbol = mpc_new("symbol");
> mpc_parser_t* Sexpr  = mpc_new("sexpr");
> mpc_parser_t* Qexpr  = mpc_new("qexpr");
> mpc_parser_t* Expr   = mpc_new("expr");
> mpc_parser_t* Lispy  = mpc_new("lispy");
> 
> mpca_lang(MPCA_LANG_DEFAULT,
>   "                                                    \
>     number : /-?[0-9]+/ ;                              \
>     symbol : '+' | '-' | '*' | '/' ;                   \
>     sexpr  : '(' <expr>* ')' ;                         \
>     qexpr  : '{' <expr>* '}' ;                         \
>     expr   : <number> | <symbol> | <sexpr> | <qexpr> ; \
>     lispy  : /^/ <expr>* /$/ ;                         \
>   ",
>   Number, Symbol, Sexpr, Qexpr, Expr, Lispy);
> ```

我们还需要把新的规则及时释放：

> ```c
> mpc_cleanup(6, Number, Symbol, Sexpr, Qexpr, Expr, Lispy);
> ```

#### 2.1 阅读 Q-Expression

因为 `Q-Expression` 很像 `S-Expression` ，所以在代码上我们可以重用很多，在原先 `Q-Expression` 的基础上添加新的元素即可：

> ```c
> enum { LVAL_ERR, LVAL_NUM, LVAL_SYM, LVAL_SEXPR, LVAL_QEXPR };
> ```

我们还需要给 `Q-Expression` 变量添加一个构造器：

> ```c
> lval* lval_qexpr(void) {
>   lval* v = malloc(sizeof(lval));
>   v->type = LVAL_QEXPR;
>   v->count = 0;
>   v->cell = NULL;
>   return v;
> }
> ```

输出和清理 `Q-Expression` 类似于 `S-Expression` ，我们只需添加一些行：

> ```c
> void lval_print(lval* v) {
>   switch (v->type) {
>     case LVAL_NUM:   printf("%li", v->num); break;
>     case LVAL_ERR:   printf("Error: %s", v->err); break;
>     case LVAL_SYM:   printf("%s", v->sym); break;
>     case LVAL_SEXPR: lval_expr_print(v, '(', ')'); break;
>     case LVAL_QEXPR: lval_expr_print(v, '{', '}'); break;
>   }
> }
> ```

> ```c
> void lval_del(lval* v) {
> 
>   switch (v->type) {
>     case LVAL_NUM: break;
>     case LVAL_ERR: free(v->err); break;
>     case LVAL_SYM: free(v->sym); break;
> 
>     /* If Qexpr or Sexpr then delete all elements inside */
>     case LVAL_QEXPR:
>     case LVAL_SEXPR:
>       for (int i = 0; i < v->count; i++) {
>         lval_del(v->cell[i]);
>       }
>       /* Also free the memory allocated to contain the pointers */
>       free(v->cell);
>     break;
>   }
> 
>   free(v);
> }
> ```

在原先的改变的基础上，我们还需要再修改 `lval_read` 来读取 `Q-Expressions` ，因为它们的代码很像，所以在原先 `lval_read` 代码的基础上添加以下的代码，就可以从语法树中读取了：

> ```c
> if (strstr(t->tag, "qexpr"))  { x = lval_qexpr(); }
> ```

因为这部分没有添加太多的新元素，在原先的代码上修改了一点，所以直接用原先的代码改就好了，完整的代码请你自己构建 q_expression_v0.c ，然后重新编译。

**注意 q_expression_v0.c 须放在 /home/shiyanlou/mpc-master/ 目录下：**

**请参照 1.5 节的链接内容，编写源程序 q_expression_v0.c。**

编译后运行的结果如下：

![6-2.3-1](https://doc.shiyanlou.com/document-uid735639labid2205timestamp1527821227476.png)

#### 2.2 内建函数

现在我们可以读取 `Q-Expressions` 了，现在却不能操作它，做一些计算的工作，所以我们需要添加一些函数来处理这个问题。

首先我们可以定义一些内建函数来操作我们的列表，我们可以定义一些简洁的方法，来帮助我们处理这些数据，具体信息如下：

> ```bash
> list    需要一个或多个参数，并返回一个 q-expression
> head    需要一个 q-expression 参数,并返回只有第一个元素的 q-expression
> tail    需要一个 q-expression 参数,并返回除第一个元素余下的 q-expression
> join    需要多个q-expression 参数,并返回它们的组合 q-expression
> eval    需要一个 q-expression 参数，并将它进行计算
> ```

就像那些数学符号一样，我们需要将这些字符串也作为符号，因此需要将它们加入到代码中：

> ```
> mpca_lang(MPCA_LANG_DEFAULT,
>   "                                                        \
>     number : /-?[0-9]+/ ;                                  \
>     symbol : \"list\" | \"head\" | \"tail\"                \
>            | \"join\" | \"eval\" | '+' | '-' | '*' | '/' ; \
>     sexpr  : '(' <expr>* ')' ;                             \
>     qexpr  : '{' <expr>* '}' ;                             \
>     expr   : <number> | <symbol> | <sexpr> | <qexpr> ;     \
>     lispy  : /^/ <expr>* /$/ ;                             \
>   ",
>   Number, Symbol, Sexpr, Qexpr, Expr, Lispy)
> ```

#### 2.3 Head & Tail

我们的内建函数很像 `builtin_op` ，所以编写 `head` 和 `tail` 时可以参考编写 `lval_take` 和 `lval_pop` 。我们分析一下 `builtin_head` 它就是删除下标为 1 到最后的所有元素，而 `builtin_tail` 则是删除第一个元素即可：

> ```c
> lval* builtin_head(lval* a) {
>   /* Check Error Conditions */
>   if (a->count != 1) {
>     lval_del(a);
>     return lval_err("Function 'head' passed too many arguments!");
>   }
> 
>   if (a->cell[0]->type != LVAL_QEXPR) {
>     lval_del(a);
>     return lval_err("Function 'head' passed incorrect types!");
>   }
> 
>   if (a->cell[0]->count == 0) {
>     lval_del(a);
>     return lval_err("Function 'head' passed {}!");
>   }
> 
>   /* Otherwise take first argument */
>   lval* v = lval_take(a, 0);
> 
>   /* Delete all elements that are not head and return */
>   while (v->count > 1) { lval_del(lval_pop(v, 1)); }
>   return v;
> }
> ```

> ```c
> lval* builtin_tail(lval* a) {
>   /* Check Error Conditions */
>   if (a->count != 1) {
>     lval_del(a);
>     return lval_err("Function 'tail' passed too many arguments!");
>   }
> 
>   if (a->cell[0]->type != LVAL_QEXPR) {
>     lval_del(a);
>     return lval_err("Function 'tail' passed incorrect types!");
>   }
> 
>   if (a->cell[0]->count == 0) {
>     lval_del(a);
>     return lval_err("Function 'tail' passed {}!");
>   }
> 
>   /* Take first argument */
>   lval* v = lval_take(a, 0);
> 
>   /* Delete first element and return */
>   lval_del(lval_pop(v, 0));
>   return v;
> }
> ```

#### 2.4 宏命令和断言

`head` 和 `tail` 函数编写好了，但是我们不能对代码做错误分析，我们可以使用宏来做这件事。宏可以在代码被编译前对代码进行检查，如果有问题，就提前退出，并返回错误信息。

我们可以使用断言 `LASSERT` 来帮助宏命令更好的发挥作用。我们的断言中有三个参数，分别为 `args` ，`cond` ，`err` ，通过它来帮助我们进行错误分析：

```cpp
#define LASSERT(args, cond, err) \
  if (!(cond)) { lval_del(args); return lval_err(err); }
```

我们可以通过这个方法节省很多代码，让代码更简洁和高效，之后的检查错误的代码也可以写的很简洁了。

#### 2.4.1 Head & Tail

在我们的 `head` 和 `tail` 代码中加上断言，提高代码的效率：

> ```
> lval* builtin_head(lval* a) {
>   LASSERT(a, a->count == 1,
>     "Function 'head' passed too many arguments!");
>   LASSERT(a, a->cell[0]->type == LVAL_QEXPR,
>     "Function 'head' passed incorrect type!");
>   LASSERT(a, a->cell[0]->count != 0,
>     "Function 'head' passed {}!");
> 
>   lval* v = lval_take(a, 0);
>   while (v->count > 1) { lval_del(lval_pop(v, 1)); }
>   return v;
> }
> ```

> ```
> lval* builtin_tail(lval* a) {
>   LASSERT(a, a->count == 1,
>     "Function 'tail' passed too many arguments!");
>   LASSERT(a, a->cell[0]->type == LVAL_QEXPR,
>     "Function 'tail' passed incorrect type!");
>   LASSERT(a, a->cell[0]->count != 0,
>     "Function 'tail' passed {}!");
> 
>   lval* v = lval_take(a, 0);
>   lval_del(lval_pop(v, 0));
>   return v;
> }
> ```

#### 2.4.2 List & Eval

`builtin_list` 这个函数很简单，就是将 `S-Expression` 转为 `Q-Expression `，并返回它。`builtin_eval` 和 `builtin_list` 刚好反向，它将 `Q-Expression` 转为 `S-Expression `：

> ```
> lval* builtin_list(lval* a) {
>   a->type = LVAL_QEXPR;
>   return a;
> }
> ```

> ```
> lval* builtin_eval(lval* a) {
>   LASSERT(a, a->count == 1,
>     "Function 'eval' passed too many arguments!");
>   LASSERT(a, a->cell[0]->type == LVAL_QEXPR,
>     "Function 'eval' passed incorrect type!");
> 
>   lval* x = lval_take(a, 0);
>   x->type = LVAL_SEXPR;
>   return lval_eval(x);
> }
> ```

#### 2.5 Join

join 函数是我们最后需要定义的函数，`builtin_join `很像 `builtin_op` ，它首先检查参数里的 `Q-Expressions ` 有没有错误，没有就将它们合并，返回。`lval_join` 函数将两个 `lval` 组合，返回合并的结果：

```
lval* builtin_join(lval* a) {

  for (int i = 0; i < a->count; i++) {
    LASSERT(a, a->cell[i]->type == LVAL_QEXPR,
      "Function 'join' passed incorrect type.");
  }

  lval* x = lval_pop(a, 0);

  while (a->count) {
    x = lval_join(x, lval_pop(a, 0));
  }

  lval_del(a);
  return x;
}
lval* lval_join(lval* x, lval* y) {

  /* For each cell in 'y' add it to 'x' */
  while (y->count) {
    x = lval_add(x, lval_pop(y, 0));
  }

  /* Delete the empty 'y' and return 'x' */
  lval_del(y);
  return x;
}
```

#### 2.6 查找内建函数

我们已经完成了所有的内建函数，先在就是需要在必要的时候调用它们，我们可以使用 `strcmp` 和 `strstr` ：

> ```cpp
> lval* builtin(lval* a, char* func) {
>   if (strcmp("list", func) == 0) { return builtin_list(a); }
>   if (strcmp("head", func) == 0) { return builtin_head(a); }
>   if (strcmp("tail", func) == 0) { return builtin_tail(a); }
>   if (strcmp("join", func) == 0) { return builtin_join(a); }
>   if (strcmp("eval", func) == 0) { return builtin_eval(a); }
>   if (strstr("+-/*", func)) { return builtin_op(a, func); }
>   lval_del(a);
>   return lval_err("Unknown Function!");
> }
> ```

接着在计算部分 `lval_eval_sexpr` 添加代码调用 内建函数：

> ```cpp
> lval* result = builtin(v, f->sym);
> lval_del(f);
> return result;
> ```

好了，完成以上的编写。**注意 q_expression.c 须放在 /home/shiyanlou/mpc-master/ 目录下：**

**请参照 1.5 节的链接内容，编写源程序 q_expression.c。**

编译后运行的结果如下：

![6-2.6-1](https://doc.shiyanlou.com/document-uid735639labid2205timestamp1527821566544.png)

### 三、实验总结

本节我们在原先代码的基础上添加了新的表达式，函数，学习了内建函数。通过宏和断言提高了开发的效率，我们的程序越来越丰富了。