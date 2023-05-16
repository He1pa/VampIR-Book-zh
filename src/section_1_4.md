# 选择一个后端

后端是根据执行时的第一个参数自动选择的。如果我们想要生成 Halo2 电路，我们可以使用 `halo2` 代替 `plonk`。 重复之前的[例子](section_1_2.md), 我们可以完成以下操作来生成 Halo2 电路。

```bash
$ target/debug/vamp-ir halo2 compile -s examples/ex1.pir \
                                     -o examples/circuit.halo2
                                     
$ target/debug/vamp-ir halo2 compile -s examples/ex1.pir \
                                     -o examples/circuit.halo2

> [...]
> * Constraint compilation success!

$ target/debug/vamp-ir halo2 prove -c examples/circuit.halo2 \
                                   -o examples/proof.halo2

> [...]
> * Proof generation success!

$ target/debug/vamp-ir halo2 verify -c examples/circuit.halo2 \
                                    -p examples/proof.halo2

> [...]
> * Zero-knowledge proof is valid
```

请注意，在编译 Halo2 电路时，通用参数没有“-u”参数。

另请注意，Vamp-IR 不关心生成文件的扩展名； 它们可以命名为用户选择的任何名称。

目前，Vamp-IR 支持以下证明系统；
1. PLONK（`plonk`）
2. Halo2（`halo2`）

<p style="color:red;">TODO：控制证明的后端，例如 使用 -m 命令。</p>

