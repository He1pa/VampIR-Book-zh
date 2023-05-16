## 为什么使用 Vamp-IR?

Vamp-IR 的核心是一种用于在有限域上定义算术电路的语言。 它旨在成为支持许多基于算术电路的证明系统的通用中间语言。任何打算编译为算术电路的高级语言都可以将 Vamp-IR 作为中间语言。

<table>
    <tr>
        <td style = "width: 75%; padding: 0; border: none">
            <p>
                对中间语言的需求是显而易见的。 如果没有足够的中间表达，系统必须单独支持每个所需的证明系统。 这创建了一个如右图所示的生态系统，其中互连所需的总工作量是二次方的。 这也会造成更多的故障和不一致。
            </p>
        </td>
        <td style = "border: none">
            <img style = "height: 100%" src="./diagrams/diagram-1-1.svg">
        </td>
    </tr>
</table>
<table>
    <tr>
        <td style = "width: 25%; border: none">
            <img style = "height: 100%" src="./diagrams/diagram-1-2.svg">
        </td>
        <td style = "width: 75%; padding: 0; border: none">
            <p>
                通过适当的中间表示，完成生态系统所需的工作总量只是线性的。 这可以在左图中描绘的生态系统中看到。 语言可以不以特定的证明系统为目标，而是以中间表示为目标。然后，这种语言就可以支持该中间表示所支持的每个证明系统。
            </p>
        </td>
    </tr>
</table>

Vamp-IR 的目标是通过提供一个最小的和富有表现力的接口来描述大多数现代零知识证明系统使用的核心数据结构来满足这一需求。为此，Vamp-IR 旨在灵活且易于使用，但不提供自己的任何加密功能。 它不预设证明系统的任何特定实现或设计。Vamp-IR 文件非常通用，它们甚至可以用于使用算术电路但本质上不是密码的应用程序。
