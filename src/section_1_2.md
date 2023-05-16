# 使用 Vamp-IR

以下是一个非常基本的 Vamp-IR 程序的例子：

```haskell
// This is a comment!
def x = 10;

x = 10;
```

在这里，可以看到 Vamp-IR 中可用的两个主要顶级命令。 第一行定义了一个常量 `x`，并为其分配了值`10`。 名称必须以字母或下划线开头，后面可以包含任意数量的字母、数字或下划线。 第二行是一个预期为真的等式。 每个算术电路都被解释为一个命题。 具体来说，它是与生成它的文件中出现的所有方程式的真实性相对应的命题。 在这种情况下，编译电路将仅检查 `10 = 10`。 请注意，每一行都必须以分号结尾。 Vamp-IR 通常不关心空格或换行符（除了分隔单个标记的空格）；如果删除所有换行符并将所有内容放在一行中，则此示例也是一样的。

当然，要编译此电路，Vamp-IR 需要启动并运行。 首先克隆 Vamp-IR 的目录。

```bash
$ git clone git@github.com:anoma/vamp-ir
```

Vamp-IR 在 Rust 中实现，可以非常轻松地使用 Rust Cargo 从源代码编译。

```bash
$ cd vamp-ir
$ cargo build
```

这将在 Vamp-IR 目录中的 `/target/debug/vamp-ir` 中创建 Vamp-IR 二进制文件。

Vamp-IR 本身不具备任何加密功能。这意味着一些特定的参数，例如字段大小，不能由 Vamp-IR 确定，而是在编译时决定。 要编译此电路，必须首先选择一个目标。 对于这个例子，我会选择 PLONK。

为了这个例子，我假设我们的示例程序中的行被保存到一个名为 `ex1.pir` 的文件中，该文件存储在 Vamp-IR 主目录中一个名为 `examples` 的新文件夹中。

```bash
$ mkdir examples
$ printf "def x = 10;\n\nx = 10;">examples/ex1.pir
```

请注意，该文件以 `.pir` 结尾，这是 Vamp-IR 文件的标准扩展名。 要将其编译成 PLONK 电路，必须首先设置公共参数。

```bash
$ target/debug/vamp-ir plonk setup -o examples/params.pp

> * Setting up public parameters...
> * Public parameter setup success!
```

这将在我们的 examples 目录中创建文件 `params.pp`。 `-o` 参数表示输出文件，相当于 `--output`。 现在可以创建与我们的文件关联的电路。

```bash
$ target/debug/vamp-ir plonk compile -u examples/params.pp \
                                     -s examples/ex1.pir \
                                     -o examples/circuit.plonk

> * Compiling constraints...
> ** Inferring types...
> x[2]: int
> * Reading public parameters...
> * Synthesizing arithmetic circuit...
> * Serializing circuit to storage...
> * Constraint compilation success!
```

这将在 examples 目录下的 `circuit.plonk` 文件中创建我们的编译电路。 `-u` 参数表示通用参数文件，相当于 `--universal-params`。 `-s` 参数表示源文件，等同于 `--source`。

请注意，已定义表达式的类型是在编译期间推断的。 Vamp-IR 有一个简单的类型系统，大部分是隐式的。 稍后将对此进行更详细的解释。 在这个简单的示例中，`x` 被推断为 `int`，即在编译期间将被解释为字段元素的整数。

现在可以开始创建关于电路正确性的零知识证明。

```bash
$ target/debug/vamp-ir plonk prove -u examples/params.pp \
                                   -c examples/circuit.plonk \
                                   -o examples/proof.plonk

> * Reading arithmetic circuit...
> * Soliciting circuit witnesses...
> * Reading public parameters...
> * Proving knowledge of witnesses...
> * Serializing proof to storage...
> * Proof generation success!
```

这将在 `examples` 目录下的 `proof.plonk` 文件中创建我们的编译证明。 `-c` 参数表示一个电路文件，相当于 `--circuit`。

人们最不愿做的事莫过于验证电路。

```bash
$ target/debug/vamp-ir plonk verify -u examples/params.pp \
                                    -c examples/circuit.plonk \
                                    -p examples/proof.plonk

> * Reading arithmetic circuit...
> * Reading zero-knowledge proof...
> * Public inputs:
> * Reading public parameters...
> * Verifying proof validity...
> * Zero-knowledge proof is valid
```

这将验证刚刚创建的证明。在这种情况下，我们刚刚验证了 10 = 10 的零知识证明。`-p` 参数表示证明文件，相当于 `--proof`。

除了 `help` 之外，现在已经使用了所有可用的 Vamp-IR 命令；`setup`、`compile`、`prove` 和 `verify`。这些命令定义了当前与 Vamp-IR 交互的所有方法。这是一个非常简单的系统。
