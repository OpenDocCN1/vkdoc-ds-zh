### 第 4 章：自定义类型

第 2 章“数据类型与结构”讨论了内置类型，包括容器。在本章中，我们将研究如何创建用户定义的类型。

类型与结构

让我们在 Julia 语言的背景下澄清这两个术语。

用通俗的英语来说，一个对象的*类型*指的是描述该对象的一组特征。例如，一个 sheet 类型的对象可以用其尺寸、重量、大小或颜色来描述。

Julia 中的所有值都是属于给定类型的真实对象（它们是给定类型的各个“实例”）。

Julia 类型包括所谓的*原始类型*（由固定数量的比特组成，如所有数值类型，如`Int64`、`Float64`和`Char`）和*复合类型*或*结构*，其中对象的特征是使用多个字段和可变数量的比特来描述的。

*结构*和*原始类型*都可以由用户定义，并且是分层组织的。*结构*大致对应于其他语言中所谓的*类*。

在处理类型时，有两个运算符特别重要：

- `::`运算符用于约束对象属于给定类型。例如，`a::B`意味着“a 必须是 B 类型”。
- `<:`运算符具有类似含义，但稍微宽松一些，因为对象可以是给定类型的任何子类型。例如，`A<:B`意味着“A 必须是 B 的子类型”，即 B 是“父”类型，A 是它的“子”类型。

使用这些运算符的主要原因是为了确认程序按预期方式工作。在某些情况下，提供这些额外信息也可以提高性能。

#### 4.1 原始类型定义

用户定义的原始类型使用关键字`primitive type`及其名称和所需的比特数来定义：

```
primitive type [名称] [比特数] end
```

例如：

```julia
primitive type My10KBBuffer 81920 end
```

Julia 的一个（当前）限制是比特数必须是 8 的倍数且小于 8388608。

也可以选择指定父类型，如下所示：

```
primitive type [名称] <: [超类型] [比特数] end
```

请注意，具有相同比特数的两个用户定义类型的内部表示是完全相同的。唯一改变的是它们的名称，但这是一个重要的区别：当这些类型的对象作为参数传递时，函数的行为方式会改变，也就是说，在整个程序中使用命名类型来区分它们，而不是它们的实现。

#### 4.2 结构定义

要定义结构，请使用关键字`mutable struct`，为结构指定一个名称，指定字段，并使用`end`关键字结束定义：

```julia
mutable struct MyOwnType
    field1
    field2::String
    field3::Int64
end
```

请注意，虽然你可以选择将每个单独的字段定义为给定类型（例如，`field3::Int64`），但不能将字段定义为给定类型的子类型（例如，`field3<:Number`）。为此，你可以在结构定义中使用模板：

```julia
mutable struct MyOwnType{T<:Number}
    field1
    field2::String
    field3::T
end
```

使用模板，结构的定义会在首次构造一个其`field3`为`T`类型的对象时动态创建。

用于注释单个字段的类型可以是原始类型（如前面的示例），也可以是对另一个结构的引用（请参见后面的示例）。

还要注意，与 Julia 不同，与其他高级语言（例如 Python）不同，你不能在定义结构后添加或删除字段。如果你需要此功能，则必须使用字典，但请注意，你将牺牲这种灵活性来换取更差的性能。

相反，为了获得性能（但同样牺牲了灵活性），你可以在`struct`前省略`mutable`关键字。这样，一旦创建了该类型的对象，其字段就不能再更改（即，结构默认是不可变的）。

请注意，可变对象（如数组）在不可变结构中仍然是可变的。

#### 4.3 对象初始化与使用

定义自定义类型（原始类型或结构）后，你可以从该类型初始化任意数量的对象：

```julia
myObject = MyOwnType("something","something",10)
a = myObject.field3 # 10
myObject.field3 = 20 # 仅当 myObject 是可变结构时
```

请注意，你必须按照结构定义中指定的相同顺序使用值来初始化对象。你不能按名称初始化对象（为此，请考虑使用具名元组）：

```julia
MyOwnType(field3 = 10, field1 = "something", field2 = "something") # 错误！
```

要访问对象字段或更改其值，你可以像大多数其他语言一样，使用`object.field`语法。

函数与结构

请注意，处理对象的函数不是在类型定义中定义或声明的，即它们不与特定类型相关联。

这意味着，在 Julia 中，你不需要像调用对象方法那样使用`myobj.func(x,y)`，而是将对象作为参数传递，例如`func(myobj,x,y)`，无论它是作为第一个、第二个还是更后面的参数。

#### 4.4 抽象类型与继承

你可以使用关键字`abstract type`创建抽象类型。抽象类型不允许包含任何字段，也不能从中实例化对象。相反，带有字段的具体类型可以被定义为它们的子类型，并从它们实例化对象。一个允许抽象类实际持有字段的问题（请参见 https://github.com/JuliaLang/julia/issues/4935）目前是开放的，可能会在 Julia 的下一个主要版本中实现。

你可以创建整个抽象类型层次结构，尽管当前不支持多重继承（一个类型是多个类型的子类型）：

### 4.4.1 在 Julia 中实现面向对象范式

Julia 既支持继承也支持组合模型，尽管支持程度不同。

继承就是你刚才看到的，当一个类型的层次结构通过将一个类型声明为另一个类型的子类型来获得时。你也看到了在 Julia 中使用继承的局限性，即只能继承行为（而不是结构）。

那么，如何在 Julia 中实现面向对象模型呢？嗯，Julia 社区的偏好是使用组合而非继承。

组合是指你将给定类型的一个字段声明为另一个复合类型的对象。通过这个（被引用的）对象，你就可以访问另一个类型的字段。

考虑以下示例，你首先定义一个通用的 `Person` 结构，然后再定义两个更具体的 `Student` 和 `Employee` 结构：

```julia
struct Shoes
    shoesType::String
    colour::String
end

struct Person
    myname::String
    age::Int64
end

struct Student
    p::Person
    school::String
    shoes::Shoes
end

struct Employee
    p::Person
    monthlyIncomes::Float64
    company::String
    shoes::Shoes
end
```

你没有使用继承将 `Student` 和 `Employee` 声明为 `Person` 的子类型，而是使用组合为两者都分配了一个 `Person` 类型的字段 `p`。正是由于这个字段，你无需重复两者共有的字段。

类型必须先定义，然后才能用于引用对象。因此，`Shoes` 的定义必须放在 `Students` 和 `Employee` 定义之前。

然后，你可以创建专门类型的实例，要么先创建引用的对象，要么在专门类型的构造函数中内联创建：

```julia
gymShoes = Shoes("gym","white")
proShoes = Shoes("classical","brown")

Marc = Student(Person("Marc",15),"Divine School",gymShoes)
MrBrown = Employee(Person("Brown",45),3200.0,"ABC Corporation Inc.", proShoes)
```

最后，你可以使用多重分派为专门类型提供定制实现，并通过点（`.`）运算符的链式使用来访问引用的对象和/或通用字段：

```julia
function printMyActivity(self::Student)
    println("嗨！我是 $(self.p.myname)，我在 $(self.school) 学校学习，我穿 $(self.shoes.colour) 颜色的鞋子")
end

function printMyActivity(self::Employee)
    println("你好。我的名字是 $(self.p.myname)，我在 $(self.company) 公司工作，我穿 $(self.shoes.colour) 颜色的鞋子")
end

printMyActivity(Marc) # 嗨！我是 Marc，...
printMyActivity(MrBrown) # 你好。我的名字是 MrBrown，...
```

虽然明智地使用继承在实际中可以满足你可能遇到的许多建模设计情况，但 Julia 核心语言在表现力方面确实有所欠缺，因为你无法直接考虑和/或区分对象之间关系的不同概念，如特化（例如，`Person→Student`）、组合（例如，`Person→Arm`）和弱关系（例如，`Person→Shoes`）。

为了解决这个不足，已经发布了一些第三方包（本书未讨论）来提高 Julia 在这个领域的灵活性，例如用于模拟多重继承的 `SimpleTraits` 包，或用于自动将字段声明从父类型复制到子类型的 `OOPMacro.jl`。

### 4.5 与类型相关的一些有用函数

为了完成关于用户定义类型的讨论，你可能会发现以下与类型相关的函数很有用：

- `supertype(MyType)`：返回一个类型的父类型
- `subtypes(MyType)`：列出一个类型的所有子类型
- `fieldnames(MyType)`：查询一个结构的所有字段
- `isa(obj,MyType)`：检查 `obj` 是否是 `MyType` 类型
- `typeof(obj)`：返回 `obj` 的类型
- `eltype(obj)`：返回集合的内部元素类型，例如数组的内部元素

