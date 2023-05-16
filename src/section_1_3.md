# 证明有效性与交互

变量的值不需要提前给出。如果我们的文件改为

```haskell
x = 10;
```

在不声明 `x` 的值的情况下，Vamp-IR 会将此未绑定变量解释为需要在证明生成期间指定的输入。 如果将其保存在文件 `ex2.pir` 中并编译成电路，可以看到 Vamp-IR 会在需要时请求输入。

```bash
$ printf "x = 10;">examples/ex2.pir
$ target/debug/vamp-ir plonk compile -u examples/params.pp \
                                     -s examples/ex2.pir \
                                     -o examples/circuit2.plonk

> * Compiling constraints...
> [...]
> * Constraint compilation success!

$ target/debug/vamp-ir plonk prove -u examples/params.pp \
                                   -c examples/circuit2.plonk \
                                   -o examples/proof2.plonk

> * Reading arithmetic circuit...
> * Soliciting circuit witnesses...
> ** x[2] (private): 

$ 9

> * Reading public parameters...
> [...]
> * Proof generation success!
```

它要求 `x` 的私有值，对此我输入 `9`。 这次应该创建一个无效证明，因为 9 不等于 10。如果有人尝试验证证明，他们会发现它是无效的。

```bash
$ target/debug/vamp-ir plonk verify -u examples/params.pp \
                                    -c examples/circuit2.plonk \
                                    -p examples/proof2.plonk

> * Reading arithmetic circuit...
> [...]
> * Verifying proof validity...
> * Result from verifier: Err(ProofVerificationError)
```

正如所看到的，给出了一个验证错误。
