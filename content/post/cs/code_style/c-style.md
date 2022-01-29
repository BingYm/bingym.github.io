---
title: "C Programming Style"
date: 2022-01-29T17:40:11+08:00
categories: ["reprint"]
tags: ["C"]
draft: false
---

本文简短描述了 Linux 内核推荐的代码风格。代码风格是非常个性化的，但这是 Linux 内核必须维持的准则，对于很多其他领域的代码，该规范也具有参考意义。

首先，请打印出 GNU 代码规范，不要阅读，烧掉它们，这是一个很棒的象征性手势。

# 1 缩进

Tab 占 8 个字符，因此缩进也是 8 个字符。有一些异端运动试图减少缩进至 4 个字符（甚至 2 个字符），类似的尝试是将 PI 的值定义为 3。

> 原因：缩进的整体思想是明确定义控制块的开始和结束位置。特别是当你连续看屏幕 20 个小时时，你会发现当缩进值较大时，注意到缩进会更加轻松。

现在，有些人表示 8 个字符的缩进使代码向右移得太远，难以在宽度为 80 字符的终端屏幕上阅读。

这个问题的答案是：如果你需要三个以上的缩进级别，无论如何程序都会被搞砸，应当修改程序。

简而言之，8 字符缩进使代码更易于阅读，并且在函数嵌套过深时发出警告。注意这个警告。

减少 `switch` 语句中的多级缩进的首选方法是将 `switch` 与 `case` 标签在同一列对齐而不是缩进`case` 标签：

```C
switch (suffix) {
case 'G':
case 'g':
        mem <<= 30;
        break;
case 'M':
case 'm':
        mem <<= 20;
        break;
case 'K':
case 'k':
        mem <<= 10;
        /* fall through */
default:
        break;
}
```

除非要隐藏某些内容，否则不要将多个语句放在同一行上：

```C
if (condition) do_this;
  do_something_everytime;
```

也不要将多个任务放在同一行上。内核代码风格非常简单。避免使用复杂的表达式。

除了在注释、文档和 `Kconfig` 之外，空格都不能用于缩进，上述示例被故意破坏了。

得到一个体面的编辑，不要在行尾留空白。

# 2 打破长行和长字符串

代码风格作用于常用工具，使工具增加可读性和可维护性。

行的长度限制为 80 列，这是一个强优先限值。

除非该行超过 80 列会显著提高可读性并且不会隐藏信息，长度超过 80 列的语句将被分成合理的块。后代始终比父代短很多，并且基本上位于右侧。具有长参数列表的函数头也是如此。但是切勿破坏诸如`printk` 消息之类的用户可见的字符串，因为这会破坏为它们进行 `grep` 的能力。

# 3 放置大括号和空格

C 风格中经常出现的另一个问题是大括号的位置。

与缩进尺寸不同，没有什么技术上的原因让我们选择一种放置策略而不是另一种，但是正如 *Kernighan* 和 *Ritchie* 向我们展示的一样，首选方式是将开括号放在一行的最后，将闭括号放在新行：

```C
if (x is true) {
        we do y
}
```

这实用与所有非函数语句块（`if, switch, for, while, do`）。例如：

```C
switch (action) {
case KOBJ_ADD:
        return "add";
case KOBJ_REMOVE:
        return "remove";
case KOBJ_CHANGE:
        return "change";
default:
        return NULL;
}
```

但是有一个例外，即函数：函数的开括号在下一行的开头：

```C
int function(int x)
{
        body of function
}
```

全世界的异端人士都声称这种矛盾是矛盾的，但是所有有正确思想的人们都知道 *K*&*R* 是正确的。

此外，函数还是很特殊的（你不能将它们嵌套在 C 代码里）。

请注意，闭括号所在的行是空的，除非在其后接同一语句的延续，即 `while` 在 `do` 语句后或 `else`在 `if` 语句后，像这样：

```C
do {
        body of do-loop
} while (condition);
```

和这样：

```C
if (x == y) {
        ..
} else if (x > y) {
        ...
} else {
        ....
}
```

理由：*K*&*R*。

另外请注意：这种括号放置策略还可以在不损害可读性的前提下，最大程度地减少空（或几乎空）行的数量。

因此，由于屏幕上的新行供应是不可再生资源（请在此处考虑 25 行高的终端屏幕），你讲有更多的空行可以用来编写注释。

不要在使用单个语句的地方不必要地使用大括号。

```C
if (condition)
        action();
```

和：

```C
if (condition)
        do_this();
else
        do_that();
```

如果多个条件语句中只有一个分支是单个语句，则不适用该规则。在这种情况下，需要在所有的分支中都使用大括号：

```C
if (condition) {
        do_this();
        do_that();
} else {
        otherwise();
}
```

## 3.1 空格

Linux 内核风格使用空格的样式（主要）取决于函数与关键字的用法。

在大多数关键字之后使用一个空格。值得注意的例外是 `sizeof, typeof, alignof, __attribute__`，看起来有点像函数（通常在 Linux 中使用时需要加括号，虽然它们并没有要求，如在 `struct fileinfo info;` 声明之后使用 `sizeof info`。

因此，在这些关键字之后使用一个空格：

```C
if, switch, case, for, do, while
```

但这些例外：`sizeof, typeof, alignof, __attribute__`，例如：

```C
s = sizeof(struct file);
```

不要在带括号的表达式周围（内部）添加空格。这种不好的例子如下：

```C
s = sizeof( struct file );
```

在声明指针类型的数据或返回指针类型的函数时，

`*` 的首选用法是与数据名或函数名相邻，而不是与类型名相邻。例如：

```C
char *linux_banner;
unsigned long long memparse(char *ptr, char **retptr);
char *match_strdup(substring_t *s);
```

在大多数二元和三元运算符的两侧使用一个空格，例如：

```C
=  +  -  <  >  *  /  %  |  &  ^  <=  >=  ==  !=  ?  :
```

但一元运算符之后不需要空格：

```C
&  *  +  -  ~  !  sizeof  typeof  alignof  __attribute__  defined
```

后缀递增和递减一元运算符前没有空格：

```C
++  --
```

前缀递增和递减一元运算符后没有空格：

```C
++  --
```

使用 `.` 和 `->` 运算符也不需要空格。

不要在行尾留下尾随空格。某些带有智能缩进的编辑器会在适当的时候在新行的开头插入空格，从而让你可以立即开始输入下一行代码。但是，如果最终没有在新行放置代码（留空行），某些编辑器不会删除空格，最终导致包含尾随空格的行。

*Git* 会警告你有关引入尾随空格的补丁，并且可以有选择地为你剥离尾随空格。但是如果应用一系列补丁，可能会由于上下文的更改导致该系列的后续补丁失败。

# 4 命名

C 是一门简洁的语言，所以命名也应该如此。与 Modula-2 和 Pascal 程序员不同，C 程序员不会使用诸如

`ThisVariableIsATemporaryCounter` 之类的可爱命名。一个 C 程序员将命名该变量为 `tmp`，这样更容易编写，但同时也更难理解。

虽然不赞成使用大小写混合的命名方式，但描述性名称命名全局函数是必须的。

全局变量（仅在确实需要它们时才使用）与全局函数一样，都需要使用具有描述性的名称命名。如果现在有一个函数用来统计活跃用户数，应该将它命名为 `count_active_users()` 或类似的，而不应该命名为 `cntusr()`。

将函数的类型编码为名称（所谓的匈牙利命名法）是不好的——编译器无论如何都知道它们的类型并且可以检查它们，这样只会使程序员感到困惑。难怪 *MicroSoft* 写出了很多古怪的程序。

局部变量名和应该简单明了，切中要害。如果你有一些随机的整数循环变量，可以将它命名为 `i`。如果该程序没有可能被误解，命名为 `loop_counter` 是没有必要的。同样，`tmp` 几乎可以用于任何类型的保存临时值的变量。

如果你害怕混淆你的局部变量名，你会遇到另一个问题，它通常被称为“生长激素功能失调综合征”。参见第六章（函数）。

# 5 Typedefs

请不要使用诸如 `vps_t` 之类的类型别名。对结构体和指针使用此类别名是一种错误。当你在源码中看到

```C
vps_t a;
```

这是什么意思？

相反，如果是这样

```C
struct virtual_container *a;
```

你就能知道 `a` 实际上到底是什么。

许多人认为 `typedefs` 提高了可读性。但并不是这样，它们仅对以下内容有用：

1. > 完全不透明的对象（`typedef` 用于隐藏对象的内容）。

> 示例：`pte_t` 等不透明的对象，你只能通过合适的访问函数进行访问。

> 注意：不透明性和访问函数并不是一种好的方法。之所以将它们用于类似 `pte_t` 的对象，是因为它们确实存在不可访问的信息。

> 

1. > 明确整数类型，这种抽象有助于避免混淆 `int` 和 `long`。`u8/u16/u32` 是完美的 `typedefs`。

> 注意：

> 如果如果有任何量是 `unsigned long`，那就没有理由使用 `typedef unsigned long myflags_t`。

> 但是如果有明确的理由说明为什么在某些情况下它可能是 `unsigned long`，而在另一些配置下可能是 `unsigned long`，那就继续使用 `typedef`。

> 

1. > 当你使用 `sparse` 创建一个用于类型检查的新类型。

> 

1. > 在某些特殊情况下，与 C99 标准类型相同的新类型。

> 尽管只需要很短的时间就可以使眼睛和大脑适应像这样的标准类型 `uint32_t`，但是无论如何，有些人还是反对使用它们。

> 因此，*Linux* 特定类型 `u8/u16/u32/u64` 及其有符号类型被允许定义别名，尽管它们在新代码中不是必须的。

> 当编辑已使用一种或另一组类型的现有代码时，应当遵循该代码中的现有选择。

> 

1. > 在用户空间保持类型安全。

> 在用户空间可见的某些结构体中，我们不能使用 C99 标准类型，也不能使用类似 `u32` 的形式。因此，我们应当在所有与用户空间共享的结构的体中使用 `__u32` 和类似类型。

也许还有其他情况，但是一个基本规则是：除非你可以清楚地匹配上述规则之一，否则不要使用`typedef`。

通常，指针或者具有可以合理地直接访问元素的结构体永远都不应该使用 `typedef`。

# 6 函数

函数应该简洁而优美，每个函数只做一件事。它们应该适合一到两个文本（*ISO*/*ANSI* 屏幕的尺寸是 80 × 24）。

函数的最大长度与该函数的复杂度和缩进程度成反比。因此，如果你有一个概念上很简单的函数，但是有一个长而是简单的 `case` 语句，通过这些语句完成很多不同的小任务，那么让该函数更长是可行的。

但是，如果你有一个复杂的函数，并且怀疑一般人无法理解该函数的全部含义，那么你应该更加严格地遵守最大长度限制，并使用具有描述性名称的辅助函数（如果你认为该辅助函数对性能至关重要，可以将其设置为内联函数）。

函数的另一个度量是局部变量的数量，它们不应该超过 5-10 个，否则就是你的函数有什么问题。重新规划该函数，考虑将其拆分为较小的部分。人脑通常可以轻松地跟踪大约 7 种不同的事物，再多就会变得混乱。你知道自己很聪明，但是也许你想轻松理解 2 周前做的工作。

在源文件中，用一个空行分割函数。如果需要导出该函数，则 `EXPORT` 宏应该紧跟在函数结束的括号行之后。例如：

```C
int system_is_up(void)
{
        return system_state == SYSTEM_RUNNING;
}
EXPORT_SYMBOL(system_is_up);
```

函数原型应该包含参数名称及其数据类型。尽管这不是 C 语言所必需的，但我们建议在 *Linux* 中这样做，因为它是一种简单的为读者添加有效信息的方法。

# 7 函数集中退出

尽管有些人不赞同使用 `goto` 语句，但是编译器经常以无条件跳转指令的形式使用与 `goto` 语句等效的语句。

当函数可能从多个位置退出，并且必须执行一些常规工作（例如清理）时，`goto` 语句就会派上用场。如果不需要清理，则直接返回。

标签命名需要说明 `goto` 的功能或 `goto` 存在的原因。当一个 `goto` 是用来释放 `buffer` 的空间，一个好的标签命名是 `out_free_buffer:`。避免使用像 `err1:` 和 `err2:` 这样的 `GW-BASIC` 命名，因为当你添加或删除退出路径时，你需要重新命名它们，并且它们会使正确性验证变得非常困难。

使用 `goto` 的逻辑依据是：

- 无条件语句更容易理解和遵循；

- 减少嵌套；

- 防止在进行修改时没更新退出点而导致错误；

- 节省编译器优化冗余代码的工作。

```C
int fun(int a)
{
        int result = 0;
        char *buffer;

        buffer = kmalloc(SIZE, GFP_KERNEL);
        if (!buffer)
                return -ENOMEM;

        if (condition1) {
                while (loop1) {
                        ...
                }
                result = 1;
                goto out_free_buffer;
        }
        ...
out_free_buffer:
        kfree(buffer);
        return result;
}
```

注意常见的错误类型，例子如下：

```C
err:
        kfree(foo->bar);
        kfree(foo);
        return ret;
```

该错误是在某些退出路径上 `foo` 为`NULL`。此问题的解决方法是将其分为两个错误标签`err_free_bar:` 和 `err_free_foo:`：

```C
err_free_bar:
       kfree(foo->bar);
err_free_foo:
       kfree(foo);
       return ret;
```

理想情况下，你应该模拟错误以测试所有的退出路径。

# 8 注释

注释是一个好习惯，但是过度注释也带来一些坏处。永远不要尝试在注释中解释代码的工作方式——编写高质量的代码从而让工作方式显而易见是一种更好的选择，而不是浪费时间来解释低质量的代码。

通常，你的注释应该用来解释代码的功能，而不是解释代码如何实现这项功能。此外，尽量避免在函数体内添加注释——如果函数太过复杂以至于需要单独注释其中的某些部分，请重新阅读第 6 部分。你可以添加一些短注释以提醒或警告某些特别精巧（或丑陋）的地方，但请尽量避免。最好是将这些注释放在函数的开头，解释它做了什么，可能还包括它为什么这么做。

在注释内核 API 函数时，请使用 *kernel*−*doc* 格式。你可以在这里（Documentation/doc-guide/）看到更多细节。

多行注释推荐的风格如下：

```C
/*
 * This is the preferred style for multi-line
 * comments in the Linux kernel source code.
 * Please use it consistently.
 *
 * Description:  A column of asterisks on the left side,
 * with beginning and ending almost-blank lines.
 */
```

对于在 `net/` 和 `drivers/net/` 中的文件，多行注释的风格有一点不同：

```C
/* The preferred comment style for files in net/ and drivers/net
 * looks like this.
 *
 * It is nearly the same as the generally preferred comment style,
 * but there is no initial almost-blank line.
 */
```

对数据（无论是基本类型还是派生类型）进行注释也很重要。为此，每行仅对一个数据进行声明（对于多个数据声明，请不要使用逗号）。这为你留出了对每个数据进行简短评论的空间，以说明该数据的用途。

# 9 你搞砸了

这不是大问题，我们都有这样的经历。Unix 用户助手可能已经告诉你 `GNU emacs` 会自动帮你格式化 C 代码。你可能已经注意到，`GNU emacs` 确实可以做到这一点，但是使用默认值的实际体验并不好（实际上，它们比随机输入更糟糕）。

所以，你可以摆脱 `GNU emacs`，或者将其改为更合理的值。为此，你可以将下列内容粘贴到你的`.emacs` 配置文件中：

```C
(defun c-lineup-arglist-tabs-only (ignored)
  "Line up argument lists by tabs, not spaces"
  (let* ((anchor (c-langelem-pos c-syntactic-element))
         (column (c-langelem-2nd-pos c-syntactic-element))
         (offset (- (1+ column) anchor))
         (steps (floor offset c-basic-offset)))
    (* (max steps 1)
       c-basic-offset)))

(add-hook 'c-mode-common-hook
          (lambda ()
            ;; Add kernel style
            (c-add-style
             "linux-tabs-only"
             '("linux" (c-offsets-alist
                        (arglist-cont-nonempty
                         c-lineup-gcc-asm-reg
                         c-lineup-arglist-tabs-only))))))

(add-hook 'c-mode-hook
          (lambda ()
            (let ((filename (buffer-file-name)))
              ;; Enable kernel mode for the appropriate files
              (when (and filename
                         (string-match (expand-file-name "~/src/linux-trees")
                                       filename))
                (setq indent-tabs-mode t)
                (setq show-trailing-whitespace t)
                (c-set-style "linux-tabs-only")))))
```

这将使 

`emacs` 更好地与 `~/src/linux-trees` 目录下的内核风格的 C 文件兼容。

但是即使你无法使用 `emacs` 进行理想的格式化，仍然还有其他方法：使用 `indent`。

同样，`GNU indent` 有一些与 `GNU emacs` 相同的失效配置，这就是为什么你需要为其提供一些命令行选项。但这还不算太糟，因为即使是 `GNU indent` 的开发者也意识到 *K*&*R* 的权威（GNU 的开发者并不邪恶，他们只是在这类问题上受到严重误导），所以你只需要给 `indent` 提供选项 `-kr -i8`（`K&R, 8 character indents` 标准），或使用 `scripts/Lindent`，它的缩进风格是最新风格。`indent` 有很多选项，尤其是在对注释重新格式化的时候，你可能需要仔细看一下 `man` 手册。但请记住，`indent` 不是针对不良编程的解决方案。

# 	10 Kconfig 配置文件

对于源代码树中的所有 `Kconfig*` 配置文件，它们的缩进有所不同。`config` 定义下的行以一个`tab` 为单位缩进，而帮助文本以两个空格缩进。例如：

```C
config AUDIT
      bool "Auditing support"
      depends on NET
      help
        Enable auditing infrastructure that can be used with another
        kernel subsystem, such as SELinux (which requires this for
        logging of avc messages output).  Does not do system-call
        auditing without CONFIG_AUDITSYSCALL.
```

严重危险的特性（例如对某些文件系统的写支持）应该在其提示中突出表明这一点：

```C
config ADFS_FS_RW
      bool "ADFS write support (DANGEROUS)"
      depends on ADFS_FS
      ...
```

有关配置文件的完整文档，参见 `Documentation/kbuild/kconfig-language.txt`。

# 11 数据结构

对于在单线程环境内创建和销毁，并在线程之外可见的数据结构，应该使用引用计数。在内核中不存在垃圾回收（并且内核之外的垃圾回收是缓慢且低效的），这意味着你必须使用引用计数记录所有的使用情况。

引用计数意味着你可以避免加锁，允许多个用户并行访问数据结构，并且不必担心正在使用的数据结构突然消失（仅仅因为程序 `sleep` 了一会儿或者做了一会儿其他的事情）。

注意：加锁并不能代替引用计数。加锁用于保持数据结构的一致性，而引用计数是一种内存管理技术。通常二者都是必须的，不要相互混淆。

很多数据结构都有 2 级引用计数，当存在不同等级的使用者时，自级计数器统计子级使用者的数量，当子级计数器变为 0 时将全局计数器减 1。

这种多级引用计数的例子可以在内存管理（`struct mm_struct`: mm_users and mm_count）中找到，也可以在文件系统（`struct super_block`: s_count and s_active）中找到。

记住：如果其他线程可以发现你的数据结构，并且你没有使用引用计数，那么几乎可以肯定你的代码有 bug。

# 12 宏、枚举和 RTL

定义常量的宏名称和枚举中的标签均使用大写字母。

```C
#define CONSTANT 0x12345
```

最好使用枚举定义多个相关常量。

可以使用大写的宏名称，但是宏函数要用小写命名。

一般情况下，与宏函数相比更推荐使用内联函数。

应该将包含多条语句的宏放在 `do-while` 块中：

```C
#define macrofun(a, b, c)                       \
        do {                                    \
                if (a == 5)                     \
                        do_this(b, c);          \
        } while (0)
```

使用宏时应该避免下列情况：

1. 避免定义影响控制流的宏：

```C
#define FOO(x)                                  \
        do {                                    \
                if (blah(x) < 0)                \
                        return -EBUGGERED;      \
        } while (0)
```

上述代码是很糟糕的。它使用时看起来像函数调用，但是却能让调用它的函数退出，这会打断读者阅读代码时的思维。

1. 避免定义依赖于固定名称的局部变量的宏：

```C
#define FOO(val) bar(index, val)
```

上述代码看起来像是做了一件好事，但是当读者阅读这段代码时会感到很困惑，并且这段代码很容易被“看似无害的更改”破坏。

1. 避免定义带参并做左值的宏：`FOO(x) = y`

如果有人将 `FOO` 转换为内敛函数，会导致程序崩溃。

1. 避免依照优先级定义宏：

使用宏通过表达式定义常量时，需要将表达式用括号括起来。注意：带参数的宏同样需要考虑该问题。

```C
#define CONSTANT 0x4000
#define CONSTEXP (CONSTANT | 3)
```

1. 避免在宏函数中定义局部变量时的命名空间冲突：

```C
#define FOO(x)                          \
({                                      \
        typeof(x) ret;                  \
        ret = calc_ret(x);              \
        (ret);                          \
})
```

`ret` 是定义局部变量时的常用名，而 

`__foo_ret` 则不太可能与已有的局部变量发生冲突。

cpp 手册详尽描述了宏，gcc 内部手册还介绍了在内核中经常与汇编语言一起使用的 RTL。

# 13 打印内核消息

内核开发者喜欢被看作是有学问的人。一定要注意内核消息的拼写，给人留下好印象。不要使用类似`dont` 之类的蹩脚单词，使用`do not` 或 `don't` 代替它。

内核消息要简洁、清晰并且无歧义。

内核消息不必以句号结尾。

应避免打印带括号的数字，因为这样毫无意义。`linux/device.h` 中有许多驱动程序模型诊断宏，你应使用这些宏来确保消息与设备和驱动是匹配的，并且使用正确级别的宏：`dev_err()`、`dev_warn()`、`dev_info()` 等。对于与特定设备无关的消息，使用 `linux/printk.h` 中定义的 `pr_notice()`、`pr_info()`、`pr_warn()`、`pr_err()` 等。

一个相当大的挑战可能是输出友好的调试消息。这些友好的调试消息对远程故障排除将是一个巨大的帮助。但是打印调试消息的处理和打印非调试信息的处理是不同的。其他的 `pr_XXX()` 函数无条件打印，但 `pr_debug()` 不是，因为默认编译的情况是不包含的该消息的，除非定义了 `DEBUG` 或者设置了 `CONFIG_DYNAMIC_DEBUG`。`dev_dbg()` 也是如此，通常的约定是使用 `VERBOSE_DEBUG` 将 `dev_vdbg()` 的消息添加到由 `DEBUG` 启用的消息中。

许多子系统中通过 `Kconfig` 调试选项来开启相关 `Makefile` 中的 `-DDEBUG`。其他情况下在特定的文件中使用`#debug DEBUG` 定义 `DEBUG` 宏。当调试信息需要无条件打印，例如它已经在与调试相关的 `#ifdef` 中，可以使用`printk(KERN_DEBUG ...)`。

# 14 分配内存

内核提供以下通用的内存分配函数：`kmalloc()`，`kzalloc()`，`kmalloc_array()`，`kcalloc()`，`vmalloc()` 和 `vzalloc()`。关于它们更详细的信息，请参考 API 文档。

推荐的传递结构体大小的形式如下：

```C
p = kmalloc(sizeof(*p), ...);
```

使用 `struct name` 的形式损害可读性，并且当指针变量的类型发生了变化，但是传递给内存分配函数的与之相关的`sizeof` 没进行修改时，很容易引入 bug。

对 `void` 指针类型的返回值进行类型转换是多余的。将 `void` 指针转换为其它任何类型的指针是由 C 语言保证的。

推荐的分配数组的形式如下：

```C
p = kmalloc_array(n, sizeof(...), ...);
```

推荐的分配所有元素的初始值为 0 的数组的形式如下：

```C
p = kcalloc(n, sizeof(...), ...);
```

两种形式都需要检查分配大小为 `n * sizeof(...)` 的内存是否成功，当分配不成功时会返回 `NULL`。

# 15 内联隐患

人们似乎普遍认为 GCC 有一个神奇的“使我更快”的加速选项——`inline`。虽然可以适当地使用内联（比如作为替代宏的一种方法，参阅第 12 章），但通常情况下不是这样的。大量使用 `inline` 关键字会导致一个更大的内核，这会反过来会减慢整个系统的速度，因为这样会导致 CPU `icache` 的占用量会更大，并且用于页面缓存的内存会更少。页面缓存没命中会导致磁盘查找，这很容易就会花费掉 5 毫秒，然而有很多 CPU 周期本该可以进入这 5 毫秒。

一个合理的经验法则是不要对包含多于三行代码的函数进行内联。该规则的例外是：其中一个参数是编译时确定的常量，并且由于这个常量你知道编译器将能够在编译时优化函数的大部分内容。这种例外情况的好示例是 `kmalloc()` 内联函数。

人们经常争辩说给静态的并且只使用一次的函数增加内联是一个胜利，因为不用在空间上做权衡。尽管从技术上讲这是正确的，但实际上 GCC 有能力在没有帮助的情况下自动对它们进行内联。并且当第二个使用者出现时需要我们手动删除内联内容所导致的维护问题，超过了告诉 GCC 去做一些它已经做了的事情的潜在价值。

# 16 函数返回值和函数名称

函数可以返回许多不同种类的值，其中最常见的是返回表明函数执行是成功还是失败的值。表明成功或失败，可以使用整型值（负数代表失败，0 代表成功），或布尔值（0 代表失败，非 0 代表成功）。

将二者混合使用会给 bug 的排查带来麻烦。如果 C 语言能很好的区分 integers 和 booleans，那么编译器将会帮助我们发现这些错误，但事实并非如此。为防止此类 bug，请始终遵守以下约定：

- 如果函数名是一个动作或命令，函数应该返回 integer 类型的错误码；

- 如果函数名是谓语，函数应该返回 boolean 类型的错误码。

例如：

- `add work` 是一个命令，函数 `add_work()` 返回 0 代表成功，返回 `-EBUSY` 代表失败。

- `PCI device present` 是一个谓语，`pci_dev_present()` 函数在发现匹配的设备时返回 1 代表成功，返回 0 代表没发现匹配的设备。

所有的导出函数和公有函数必须遵守此约定。私有（静态）函数不需要严格遵守，但也建议这么做。

函数返回实际计算的结果（而不是表明函数是否计算成功）的函数不受此规则约束。通常它们通过返回一些超出范围的结果来表明失败。典型的例子是返回指针的函数，他们用 `NULL` 或 `ERR_PTR` 来报告失败。

# 17 不要重新实现内核已有的宏

内核头文件 

`include/linux/kernel.h` 包含了许多可以直接使用的宏，请直接使用它们，不要重新发明轮子。例如，你可以用下列宏计算数组的长度：

```C
#define ARRAY_SIZE(x) (sizeof(x) / sizeof((x)[0]))
```

类似的，你可以用下列宏计算结构体中某个成员的大小：

```C
#define FIELD_SIZEOF(t, f) (sizeof(((t*)0)->f))
```

如果你需要，还有进行严格类型检查的宏 `min()` 和 `max()`。请仔细阅读这个头文件，看看它提供了哪些你可以在代码中直接使用的宏，而不是在代码中重新实现。

# 18 编辑器和其他内容

某些编辑器可以解析嵌入在源文件中的、用特殊标记表示的配置信息。例如：emacs 可以解析源文件中按如下方式书写的配置信息：

```C
-*- mode: c -*-
```

或者按如下方式：

```C
/*
Local Variables:
compile-command: "gcc -DMAGIC_DEBUG_FLAG foo.c"
End:
*/
```

Vim 可以解析按如下方式书写的配置信息：

```C
/* vim:set sw=8 noet */
```

不要在源文件中包含这些信息。每个人都有自己的编辑器配置信息，你的源文件配置信息不应该覆盖它们，这包括用于缩进和模式配置的标记。每个人都可以使用他们的自定义模式，或者有其他的使缩进正确工作的神奇方法。

# 19 内敛汇编

在特定体系结构的代码中，你可能需要使用内联汇编与 CPU 或平台函数进行交互。当需要这么做时不用犹豫，但是能用 C 语言完成的工作请不要无缘无故地使用内联汇编。如果可能的话，你可以也应该使用 C 语言控制硬件。

考虑编写简单的、包裹内联汇编通用位的辅助函数，而不是重复编写只有轻微变化的函数。记住，内联汇编可以使用 C 语言的参数。

大型的、具有一定复杂度的汇编函数应该放在 `.S` 文件中，并且在 C 语言头文件中定义相关的 C 语言函数原型。汇编函数的 C 语言函数原型需要使用 `armlinkage`。你可能需要将汇编语句标记为 `volatile`，以防止防止 GCC 把他们优化掉，但不一定总是要这么做，而且多余的做法可能会限制优化。

当编写包含多条指令的单个内联汇编语句时，将每条指令单独放在带引号的字符串中并独占一行，并且除了最后一个字符串以外，所有字符串都以 `\n\t` 结尾，这样做才能使以汇编形式输出时下一条指令能正确地缩进。

```C
asm ("magic %reg1, #42\n\t"
     "more_magic %reg2, %reg3"
     : /* outputs */ : /* inputs */ : /* clobbers */);
```

# 20 条件编译

尽量不要在 `.c` 文件中使用预处理条件（`#if, #ifdef`），因为这样做会使代码难以阅读，更难理解代码的逻辑。相反，在头文件中使用预处理条件来定义那些在`.c` 文件中使用的函数，在 `#else` 中提供 `no-op stub` 的版本，然后在 `.c` 文件中无条件地调用这些函数。编译器将不会为调用 `stub` 生成任何代码，从而产生相同的效果，并且逻辑更容易理解。

最好编译出整个函数，而不是部分函数或部分表达式。与其将 `ifdef` 放在表达式中，不如将表达式的一部分或全部分解到一个单独的辅助函数中，然后将预处理条件应用于该函数。

如果某个函数或变量在特定的配置中可能不需要被使用，编译器会针对定义但没使用的变量发出警告，将该定义标记为 `__maybe_unused` 而不是用预处理条件包裹它（如果函数或变量总是不使用则应该删除它）。

在代码中尽量使用 `IS_ENABLE` 宏将 `Kconfig` 中的符号转换为 C 语言的布尔表达式，并且在普通的 C 语言条件语句中使用它：

```C
if (IS_ENABLED(CONFIG_SOMETHING)) {
        ...
}
```

编译器将不断地折叠这些条件，并像 `#ifdef` 一样包含或排除代码块，所以这不会增加任何运行时的开销。然而这种方法仍然允许 C 编译器查看其中的代码并检查其正确性（语法、类型、符号引用等）。因此当条件不满足是，块中代码引用的符号也不存在，这种情况下，你仍然需要使用 `#ifdef`。

在任何具有一定复杂度的 `#if` 或 `#ifdef` 块的末尾`#endif` 所在行的后面放置注释来标注使用的条件表达式，例如：

```C
#ifdef CONFIG_SOMETHING
...
#endif /* CONFIG_SOMETHING */
```

# 附录 参考

- The C Programming Language, Second Edition by Brian W. Kernighan and Dennis M. Ritchie. Prentice Hall, Inc., 1988. ISBN 0-13-110362-8 (paperback), 0-13-110370-9 (hardback).
- The Practice of Programming by Brian W. Kernighan and Rob Pike. Addison-Wesley, Inc., 1999. ISBN 0-201-61586-X.
- GNU manuals - where in compliance with K&R and this text - for cpp, gcc, gcc internals and indent, all available from http://www.gnu.org/manual/
- WG14 is the international standardization working group for the programming language C, URL: http://www.open-std.org/JTC1/SC22/WG14/
- Kernel process/coding-style.rst, by greg@kroah.com at OLS 2002: http://www.kroah.com/linux/talks/ols_2002_kernel_codingstyle_talk/html/
