# 在将数据集导入 Power BI 数据模型之前，先对其应用机器学习

场景如下：你供职于马萨诸塞州波士顿市的一家房地产分析公司。你的数据科学家被要求协助预测波士顿的房价中位数。她使用 Kaggle 上的波士顿房价数据集，分别用 R 和 Python 开发了一个机器学习模型，并将模型保存在共享目录中供你使用。你将利用这些模型对一个包含待预测房价房屋列表的新数据集进行评分。

该新数据集包含 14 个变量，但数据科学家开发的模型仅使用了其中 4 个。以下是模型使用的四个变量：

*   `CRIM`：城镇人均犯罪率
*   `RM`：每套住宅的平均房间数
*   `TAX`：每 10,000 美元的全额财产税率
*   `LSTAT`：人口中低社会地位阶层的百分比

你将分别为 Power BI 创建 R 和 Python 脚本来执行评分。首先，从 R 开始。

## 使用 R 预测房屋价值

将数据科学家开发的机器学习模型应用到你的 Power BI 数据模型中是直接了当的。在 R 中执行此操作的步骤如下所述。本书不会涵盖如何构建实际模型，因为这超出了本书的范围。重点将放在如何应用为你创建的模型上。以下是具体步骤。

### 步骤 1：让数据科学家与你共享模型

R 模型是一个可以保存到磁盘的对象。数据科学家构建模型后，她可以将模型保存到磁盘，并以共享任何其他文件的方式与你共享。在 R 中将模型序列化到磁盘的过程非常简单，只需一行代码即可完成。代码如下：

```
saveRDS(model, "model.rds")
```

上述代码将数据科学家创建并存储在名为 `model` 的变量中的模型，使用 `saveRDS()` 函数以 `.rds` 文件格式保存到当前工作目录的磁盘中。`saveRDS()` 函数是基础包的一部分。

### 步骤 2：加载 tidyverse 包

在此步骤中，你将开始编写用于评分数据的脚本。此脚本中你只需要 `tidyverse` 包，但大部分操作都可以通过 R 的基础功能完成。使用以下代码加载 `tidyverse` 元包：

```
library(tidyverse)
```

### 步骤 3：加载模型对象和待评分数据集

使用以下代码完成此操作：

```
model <- readRDS("./Model/model.rds")
boston_housing <- read_csv("./Data/BostonHousingData.csv")
```

`readRDS()` 函数允许你从磁盘反序列化模型，以便在脚本中使用。你使用了 `readr` 包中的 `read_csv()` 函数，将需要评分的数据读取到名为 `boston_housing` 的 R 数据框中。

### 步骤 4：对数据框进行子集化，使其仅包含模型所需的列

数据集有 13 个变量，但模型只需要其中 4 个。使用以下代码从 `boston_housing` 数据框中提取所需的变量子集：

```
model_data <- boston_housing[,c("crim","rm","tax","lstat")]
```

方括号后逗号前的空白告诉 R 你想要包含所有行。逗号后的字符向量告诉 R 你想要提取哪些列。因此，上述代码生成一个数据框，其中包含 `boston_housing` 数据框的所有行，但仅包含字符向量中列出的列。现在，你通过 `model_data` 数据框获得了一个仅包含模型所需数据的数据集。

### 步骤 5：将模型应用于数据集以预测房屋价值中位数

使用以下代码完成此任务：

```
pred_medv <- predict(model, model_data)
```

上述代码返回一个向量，其中包含传递给它的每个观测值的房屋价值中位数预测。

观测值的正式定义是对自然世界中可观测现象的质量和数量的记录。因此，在此示例中，可观测现象是你拥有的关于每个待预测房屋的信息。

请注意，传递给 `predict()` 函数的数据框必须包含模型所需的变量（列）。

### 步骤 6：将预测值添加到原始数据集

步骤 5 中的代码仅返回了一个数据向量，其本身毫无意义。你需要将其与原始数据集合并。使用以下代码可以轻松完成此操作：

```
final_output <- cbind(model_data,pred_medv)
```

该代码使用 `cbind()` 函数将一列附加到 `model_data` 数据框的末尾，然后将结果数据框赋值给名为 `final_output` 的变量。`cbind()` 中的 `c` 代表列（column）。在此例中，它将向量 `pred_medv` 作为新列绑定到 `model_data` 数据框，并将新列命名为该向量的名称，即 `pred_medv`。

### 步骤 7：将整个 R 脚本复制到 Power BI 数据模型中

最终脚本如下面的代码清单 9-1 所示。

```
library(tidyverse)
model <- readRDS("./Models/model.rds")
boston_housing <- read_csv("./Data/BostonHousingInfo.csv")
model_data <- boston_housing[, c("crim","rm","tax","lstat")]
pred_medv <- predict(model, model_data)
final_output <- cbind(model_data, pred_medv)
代码清单 9-1
用于预测波士顿房价的 R 脚本
```

和之前一样，复制脚本，然后在 Power BI 中依次点击 `获取数据` ➤ `更多` ➤ `其他`。接着，点击 `R 脚本`，将脚本粘贴到 Power BI 的 `R 编辑器` 中。之后，点击 `确定`。Power BI 会将 `final_output` 数据框作为一个数据集暴露给你，你可以将其导入 Power BI 数据模型。你可以将其与 Power BI 数据模型中的其他数据集合并，创建使用预测值的度量值，或使用评分数据创建可视化。

## 使用 Python 预测房屋价值

在本节中，你同样将预测波士顿房价，但这次使用 Python 而非 R。与 R 示例一样，重点将放在如何应用为你创建的模型上，因为模型的创建超出了本书的范围。以下是 Python 中的步骤。

### 步骤 1：让数据科学家与你共享模型

与 R 类似，Python 模型也是一个可以保存到磁盘的对象，因此可以像之前共享 R 模型一样与你共享。在 Python 中将模型序列化到磁盘的过程非常简单，导入 `joblib` 库后只需一行代码即可完成。代码如下：

```
import joblib
joblib.dump(model,"model")
```

上述代码使用 `joblib` 库中的 `joblib.dump()` 函数，将数据科学家为你创建的模型保存到当前工作目录的磁盘中。模型保存到磁盘后，数据科学家可以通过共享存储库（如 `OneDrive` 或 `Dropbox`）与你共享该模型。在更专业的机构中，可能会使用称为 `MLOps` 的流程，在这种情况下，模型可能通过 `GitHub` 或 `Azure DevOps` 等存储库与你共享。

### 步骤 2：加载脚本所需的 Python 库

这是你实际开始构建将在 Power BI 中执行评分的脚本的步骤。以下是 Python 脚本导入部分所需的代码：

```
import os
import joblib
from sklearn import preprocessing
import pandas as pd
```

`joblib` 库用于反序列化数据科学家与你共享的模型，`scikit-learn` 中的 `preprocessing` 模块为你提供了转换数据以准备评分的工具，而 `pandas` 库将处理你的数据框需求。



### 步骤 3：加载模型对象与波士顿房价数据集

你可以使用以下代码实现：

```
model = joblib.load("../Models/model_Python.pkl")
boston_housing = pd.read_csv("../Data/BostonHousingInfo.csv")
```

`joblib.load` 函数与我们在 R 语言中使用的 `readRDS` 函数非常相似。它允许你从磁盘反序列化模型，以便在脚本中使用。你使用了 `pandas` 中的 `read_csv` 方法，将需要评分的数据读取到 `pandas` 数据框中。请注意，两个文件路径均以 `".."` 开头。在文件路径前加上 `".."`，可以让你从当前工作目录的上一级开始引用相对路径。`Models` 和 `Data` 文件夹与 `Python` 文件夹处于同一层级，而 `Python` 文件夹正是当前工作目录所指向的文件夹。使用 `".."` 可以让你向上跳转到 `Chapter09` 文件夹，从而访问 `Models` 文件夹和 `Data` 文件夹中的内容。

### 步骤 4：从 `boston_housing` 数据框中提取所需信息

你使用的数据集包含 13 个变量，但模型只需要其中 4 个。从 `boston_housing` 数据框中提取所需变量后，你需要对它们进行标准化处理。

`scikit-learn` 中的模型通常要求进行标准化。在机器学习中，当处理具有不同量纲的变量时（正如本例中使用的变量），通常建议进行标准化。当你使用不同量纲的变量开发模型时，数值较大的变量会占据更大权重，并可能导致模型产生偏差。`preprocessing` 模块中的 `scale()` 函数将所有变量置于同一量纲下，从而缓解了这一问题。以下是一个 YouTube 视频链接，进一步解释了标准化的概念：[`www.youtube.com/watch?v=sh_tLn1phfc`](http://www.youtube.com/watch%253Fv%253Dsh_tLn1phfc)。

你使用以下代码对模型所需的数据进行子集选取和标准化处理：

```
model_data = preprocessing.scale(
boston_housing[["crim","rm","tax","lstat"]])
```

我们先分解传递给 `scale()` 函数的代码，然后解释 `scale()` 函数的作用。以下是传递给 `scale()` 函数的代码：

```
boston_housing[["crim","rm","tax","lstat"]].
```

这段代码对 `boston_housing` 数据框进行子集选取，使其仅包含方括号内列表所定义的列（这些列用于对数据框进行子集选取）。接着，将得到的子集数据框传递给 `scale()` 函数进行标准化处理。标准化完成后，所有变量将处于同一量纲。

### 步骤 5：将模型应用于预处理后的数据，预测房价中位数

你可以通过以下代码完成此任务：

```
pred_medv = model.predict(model_data)
```

上述代码返回一个数组，其中包含传递给它的每个观测值的房价中位数预测值。输出结果被赋值给一个名为 `pred_medv` 的变量。请注意，`model_data` 数据框必须包含 `model` 模型所需的变量，这一点非常重要。

### 步骤 6：将预测结果添加到原始数据集中

步骤 6 的代码仅返回了一个数据数组，其本身毫无意义。你需要将其与原始数据集合并。你可以使用以下代码轻松实现：

```
final_output = boston_housing.loc[:,["crim","rm","tax","lstat"]]
final_output["pred_medv"] = pred_medv
```

你希望将传递给 Power BI 的数据集命名为 `final_output`。为此，你首先创建一个名为 `final_output` 的数据框，它是 `boston_housing` 数据框的一个子集。你只需要 `boston_housing` 数据框中的 `crim`、`rm`、`tax` 和 `lstat` 列。

你将使用 `boston_housing` 数据框的 `iloc` 方法来选取这些列。`iloc` 方法允许你同时选取数据框的行和列。第一个参数用于选取行，第二个参数用于选取列。在本例中，你需要 `boston_housing` 数据框中的所有行，因此你使用 `:` 符号告知 `pandas`。请注意，如果你想要前三行，则应使用 `0:2`。你将起始行的整数索引放在冒号左侧，将结束行的整数索引放在冒号右侧。由于 Python 使用基于 0 的索引，因此范围从 0 开始。下一个参数用于告知 `pandas` 你想要返回哪些列。如果你想要返回所有列，可以使用 `:`；但如果你想要返回列的子集，则可以使用包含所需列名的 `list`，正如本例中所做的那样。最后，你使用以下代码向 `final_output` 数据框追加一个名为 `pred_medv` 的新列：`final_output["pred_medv"] = pred_medv`。

### 步骤 7：将整个 Python 脚本复制到 Power BI 的 Python 脚本编辑器中

最终脚本如清单 9-2 所示。

```
import os
import pandas as pd
import joblib
from sklearn import preprocessing
model = joblib.load("../Models/model_Python.pkl")
boston_housing = pd.read_csv("../Data/BostonHousingInfo.csv")
model_data = preprocessing.scale(boston_housing[["crim","rm","tax","lstat"]])
pred_medv = model.predict(model_data)
final_output = boston_housing.loc[:,["crim","rm","tax","lstat"]]
final_output["pred_medv"] = pred_medv
清单 9-2
用于预测波士顿房价的 Python 脚本
```

与之前的示例一样，你进入 `获取数据` ➤ `更多` ➤ `其他`，然后点击 `Python 脚本`。接着，将上述脚本粘贴到 `Python 编辑器` 中，然后点击 `确定`。`final_output` 数据框将作为数据集暴露给 Power BI，并可被引入 Power BI 数据模型。

如有需要，包含预测值的字段可以在 Power Query 中进一步增强，使其在添加到数据模型之前更具价值。例如，如果一家房地产公司使用此模型，他们可以将预测的房价进行分箱处理。例如，预测价格低于 10 万的房屋可归入 `<10 万` 箱，预测价格在 10 万到 20 万之间的房屋可归入 `10–20 万` 箱，预测价格在 20 万到 30 万之间的房屋可归入 `20–30 万` 箱，以此类推。进行此类分箱处理可能有助于企业深入了解其业务状况。



