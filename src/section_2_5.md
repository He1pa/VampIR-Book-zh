# 列表

Vamp-IR 提供内置的列表，可以通过类似于 Haskell 或 ML 等函数式语言中的列表的方式对其进行操作。 空列表用 `[]` 表示，而列表构造函数用 `:` 表示。 列表没有语法糖，因此必须显式地以 `[]` 结束。

```haskell
def exList = 1:2:3:[];

exList = 1:2:3:[];
```

正如您所看到的，就像元组一样，我们可以并行检查多个事物。 主要区别在于类型。 在证明生成过程中，我们应该看到以下内容；

```bash
> exList: [int]
```

方括号表示 `exList` 是 `int` 的列表。 当然，不要与也包围类型变量的方括号混淆。 变量类型列表有两层方括号。 与元组不同，每个元素都被假定都具有相同的类型。 这允许与 `iter` 进行类型安全的互操作。

```haskell
def hd (h:t) = h;
def tl (h:t) = t;
def nth l n = hd (iter n tl l);

nth (1:2:3:4:[]) 2 = 3;
```

正如您所看到的，我们可以对列表进行模式匹配，就像对元组进行模式匹配一样。 但是，没有办法对列表进行情况分析(case)，而是由程序员来确保所有模式都有效。 如果尝试 `hd []`，他们将在电路生成过程中收到错误，提示模式 `(h : t)` 无法匹配 `[]`。

然而，这并不意味着在递归操作列表时必须始终标明列表的长度。 相反，Vamp-IR 提供了 `fold` 函数来递归操作列表。 `fold` 相当于函数式编程语言中常见的右折叠函数，例如 `foldr`。 它需要三个参数；

1. 折叠列表。
2. 将列表元素与递归操作结合起来的函数
3. 在空列表上返回的基值。

作为一个基本示例，我们可以对列表的元素求和

```haskell
def plus x y = x + y;
def sum l = fold l plus 0;

sum (1:2:3:4:[]) = 10;
```

有了这些，就可以定义最常见的列表操作函数。

```haskell
def cons x y = x:y;
def append xs l = fold xs cons l;

def take_rec take (h:t) = h:(take t);
def take n = iter n take_rec (fun x {[]});

def map_rec f x acc = (f x):acc;
def map f xs = fold xs (map_rec f) [];

def zip_rec x z (y:ys) = (x, y):(z ys);
def zip xs = fold xs zip_rec (fun x {[]});

def zipWith_rec f x z (y:ys) = (f x y):(z ys);
def zipWith f xs = fold xs (zipWith_rec f) (fun x {[]});

def reverse l = fold l (fun x r { append r (x:[]) }) [];

def last l = hd (reverse l);
```

有些限制在大多数语言中都不存在。 列表不能直接比较。 Instead, list comparisons will be zipped into underlying field comparisons. If this isn't possible, an error will occur as a field element will try to be compared to nothing. 例如，`1:[] = 1:2:[]` 不会创建无效的证明； 相反，我们会收到一个错误，表明相等性根本无效。

Unlike with tuples, one cannot use them for witness solicitation. `x = 1:2:3:[];` for an unbounded `x` will produce an error indicating that `x` is an undefined list.
