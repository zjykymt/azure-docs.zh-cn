---
title: 自动训练时序预测模型
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习通过自动化机器学习来训练时序预测回归模型。
services: machine-learning
author: nibaccam
ms.author: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, contperfq1
ms.date: 08/20/2020
ms.openlocfilehash: 900e36ec3e508f9d3616cf0c0d19ea4ff067f775
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2020
ms.locfileid: "89144781"
---
# <a name="auto-train-a-time-series-forecast-model"></a>自动训练时序预测模型
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

本文介绍如何在 [Azure 机器学习 PYTHON SDK](https://docs.microsoft.com/python/api/overview/azure/ml/?view=azure-ml-py)中使用自动机器学习（AutoML）配置和培训时序预测回归模型。 

有关低代码体验，请参阅[教程：使用自动化机器学习预测需求](tutorial-automated-ml-forecast.md)，里面有关于在 [Azure 机器学习工作室](https://ml.azure.com/)中使用自动化机器学习的时序预测示例。

与传统时序方法不同，在自动 ML 中，过去的时序值将 "透视" 成为回归量与其他预测值的其他维度。 此方法会在训练过程中，将多个上下文变量及其关系彼此整合。 影响预测的因素有很多，因此该方法将自身与真实的预测场景很好地协调起来。 例如，在预测销售额时，历史趋势、汇率和价格相互作用，共同推动着销售结果。 

下面示例演示了如何：

* 准备用于时序建模的数据
* 在 [`AutoMLConfig`](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) 对象中配置特定的时序参数
* 使用时序数据运行预测

## <a name="prerequisites"></a>先决条件

在本文中，你需要： 

* Azure 机器学习工作区。 若要创建工作区，请参阅[创建 Azure 机器学习工作区](how-to-manage-workspace.md)。

* 本文假设读者基本熟悉如何设置自动化机器学习试验。 遵循[教程](tutorial-auto-train-models.md)或[操作方法](how-to-configure-auto-train.md)，了解基本的自动化机器学习试验设计模式。

## <a name="preparing-data"></a>准备数据

AutoML 内预测回归任务类型和回归任务类型之间最重要的区别在于，数据中包含的一项功能是表示有效时序的一项功能。 常规时序具有明确定义的一致频率，并且在连续时间范围内的每个采样点上都有一个值。 

将以下快照看作 `sample.csv` 文件。
此数据集是具有两个不同商店（A 和 B）的公司的每日销售数据。 

此外，还有一些功能适用于

 *  `week_of_year`：允许模型检测每周季节性。
* `day_datetime`：表示具有每日频率的干净时序。
* `sales_quantity`：用于运行预测的目标列。 

```output
day_datetime,store,sales_quantity,week_of_year
9/3/2018,A,2000,36
9/3/2018,B,600,36
9/4/2018,A,2300,36
9/4/2018,B,550,36
9/5/2018,A,2100,36
9/5/2018,B,650,36
9/6/2018,A,2400,36
9/6/2018,B,700,36
9/7/2018,A,2450,36
9/7/2018,B,650,36
```


将数据读取到 Pandas 数据帧中，然后使用 `to_datetime` 函数确保该时序的类型为 `datetime`。

```python
import pandas as pd
data = pd.read_csv("sample.csv")
data["day_datetime"] = pd.to_datetime(data["day_datetime"])
```

在这种情况下，数据已按时间字段 `day_datetime` 降序排序。 但在设置试验时，请确保所需的时间列按升序进行排序，从而生成有效的时序。 

以下代码： 
* 假定数据包含1000条记录，并在数据中作出确定性拆分，以创建定型和测试数据集。 
* 将标签列标识为 `sales_quantity` 。
* 将标签字段从中分隔 `test_data` 以构成 `test_target` 集。

```python
train_data = data.iloc[:950]
test_data = data.iloc[-50:]

label =  "sales_quantity"
 
test_labels = test_data.pop(label).values
```

> [!IMPORTANT]
> 在训练用于预测未来值的模型时，请确保在针对预期范围运行预测时可使用训练中用到的所有特征。 例如，在创建需求预测时，包含当前股票价格的特征可能大幅提升训练准确度。 但是，如果你打算使用较长的时间范围进行预测，则可能没法准确预测与未来的时序点相对应的未来股价值，模型准确性也会受到影响。

<a name="config"></a>

## <a name="training-and-validation-data"></a>定型和验证数据

您可以直接在对象中指定单独的定型集和验证集 `AutoMLConfig` 。   详细了解 [AutoMLConfig](#configure-experiment)。

对于时序预测，当你同时传递定型和验证数据时，将自动使用 **滚动起源交叉验证 (ROCV) ** ，并使用中的参数设置交叉验证折叠数 `n_cross_validations` `AutoMLConfig` 。 ROCV 使用原始时间点将时序分成训练数据和验证数据。 在时间内滑动原点会生成交叉验证折叠。 此策略保留时序数据完整性，并消除数据泄露的风险

![替换文字](./media/how-to-auto-train-forecast/ROCV.svg)

有关其他交叉验证和数据拆分选项，请参阅 [在 AutoML 中配置数据拆分和交叉验证](how-to-configure-cross-validation-data-splits.md)。


```python
automl_config = AutoMLConfig(task='forecasting',
                             n_cross_validations=3,
                             ...
                             **time_series_settings)
```

详细了解 AutoML 如何应用交叉验证，以 [防止过度调整模型](concept-manage-ml-pitfalls.md#prevent-over-fitting)。

## <a name="configure-experiment"></a>配置试验
[`AutoMLConfig`](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig?view=azure-ml-py) 对象定义自动化机器学习任务所需的设置和数据。 预测模型的配置与标准回归模型的设置相似，但特定于时间序列数据的某些特征化步骤和配置选项是存在的。 

### <a name="featurization-steps"></a>特征化步骤

在每一个自动化机器学习试验中，默认情况下都会将自动缩放和规范化技术应用于数据。 这些技术是 **特征化** 的一种类型，用于帮助 *特定* 的算法对不同规模的功能敏感。 详细了解[特征化 In AutoML](how-to-configure-auto-features.md#automatic-featurization)中的默认特征化步骤

但是，仅对任务类型执行以下步骤 `forecasting` ：

* 检测时序采样频率（例如每小时、每天、每周），并为缺失的时间点创建新记录以使序列连续。
* 通过向前填充估算目标列中缺少的值，通过列值中位数估算特征列中缺少的值
* 创建基于时序标识符的特征，以在不同序列中启用固定效果
* 创建基于时间的特征，以帮助学习季节性模式
* 将分类变量编码为数值数量

若要获取在这些步骤中创建的功能的摘要，请参阅 [特征化透明度](how-to-configure-auto-features.md#featurization-transparency)

> [!NOTE]
> 自动化机器学习特征化步骤（特征规范化、处理缺失数据，将文本转换为数字等）成为了基础模型的一部分。 使用模型进行预测时，将自动向输入数据应用在训练期间应用的相同特征化步骤。

#### <a name="customize-featurization"></a>自定义特征化

你还可以选择自定义特征化设置，以确保用于训练 ML 模型的数据和功能导致相关的预测。 

支持的任务自定义项 `forecasting` 包括：

|自定义|定义|
|--|--|
|列用途更新|重写指定列的自动检测到的特征类型。|
|转换器参数更新 |更新指定转换器的参数。 目前支持 Imputer（fill_value 和中值）。|
|删除列 |指定要从特征化中删除的列。|

若要通过 SDK 自定义 featurizations，请 `"featurization": FeaturizationConfig` 在 `AutoMLConfig` 对象中指定。 了解有关 [自定义 featurizations](how-to-configure-auto-features.md#customize-featurization)的详细信息。

```python
featurization_config = FeaturizationConfig()
# `logQuantity` is a leaky feature, so we remove it.
featurization_config.drop_columns = ['logQuantitity']
# Force the CPWVOL5 feature to be of numeric type.
featurization_config.add_column_purpose('CPWVOL5', 'Numeric')
# Fill missing values in the target column, Quantity, with zeroes.
featurization_config.add_transformer_params('Imputer', ['Quantity'], {"strategy": "constant", "fill_value": 0})
# Fill mising values in the `INCOME` column with median value.
featurization_config.add_transformer_params('Imputer', ['INCOME'], {"strategy": "median"})
```

如果使用 Azure 机器学习工作室进行试验，请参阅[操作方法文章](how-to-use-automated-ml-for-ml-models.md#customize-featurization)。

### <a name="configuration-settings"></a>配置设置

与回归问题类似，你要定义标准训练参数，例如任务类型、迭代次数、训练数据和交叉验证次数。 对于预测任务，还必须设置对试验有影响的其他参数。 

下表汇总了这些附加参数。 有关语法设计模式，请参阅 [参考文档](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig?view=azure-ml-py) 。

| 参数&nbsp;名称 | 说明 | 必选 |
|-------|-------|-------|
|`time_column_name`|用于指定输入数据中用于生成时序的日期时间列并推断其频率。|✓|
|`forecast_horizon`|定义要预测的未来的时段数。 范围以时序频率为单位。 单位基于预测器应预测出的训练数据的时间间隔，例如每月、每周。|✓|
|`enable_dnn`|[启用预测 dnn]()。||
|`time_series_id_column_names`|列名，用于唯一标识多行数据中具有相同时间戳的时序。 如果未定义时序标识符，则假定该数据集为一个时序。 要详细了解单个时序，请查看 [energy_demand_notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand)。||
|`target_lags`|要根据数据频率滞后目标值的行数。 此滞后表示为一个列表或整数。 默认情况下，在独立变量和依赖变量之间的关系不匹配或关联时，应使用滞后。 ||
|`feature_lags`| `target_lags`设置并将设置为时，自动 ML 会自动决定 lag 的功能 `feature_lags` `auto` 。 启用功能滞后有助于提高准确性。 默认情况下，功能滞后处于禁用状态。 ||
|`target_rolling_window_size`|要用于生成预测值的 *n* 个历史时间段，该值小于或等于训练集大小。 如果省略，则 *n* 为完整训练集大小。 如果训练模型时只想考虑一定量的历史记录，请指定此参数。 了解有关 [目标滚动窗口聚合](#target-rolling-window-aggregation)的详细信息。||


以下代码： 
* 将创建 `time-series settings` 为字典对象。 
* 将设置 `time_column_name` 为 `day_datetime` 数据集中的字段。 
* 将 `time_series_id_column_names` 参数定义为 `"store"` 。 这可以确保为数据创建 **两个单独的时序组** ;一种用于存储 A 和 B。
* 将设置 `forecast_horizon` 为50以预测整个测试集。 
* 将预测时段设置为10个周期 `target_rolling_window_size`
* 指定与参数一起使用两个句点的目标值的单个 lag `target_lags` 。 
* 设置 `target_lags` 为建议的 "自动" 设置，此设置会自动检测此值。

```python
time_series_settings = {
    "time_column_name": "day_datetime",
    "time_series_id_column_names": ["store"],
    "forecast_horizon": 50,
    "target_lags": "auto",
    "target_rolling_window_size": 10,
}
```

`time_series_settings`然后，将这些值传递到标准对象中，同时还会传入 `AutoMLConfig` `forecasting` 任务类型、主要指标、出口标准和定型数据。 

```python
from azureml.core.workspace import Workspace
from azureml.core.experiment import Experiment
from azureml.train.automl import AutoMLConfig
import logging

automl_config = AutoMLConfig(task='forecasting',
                             primary_metric='normalized_root_mean_squared_error',
                             experiment_timeout_minutes=15,
                             enable_early_stopping=True,
                             training_data=train_data,
                             label_column_name=label,
                             n_cross_validations=5,
                             enable_ensembling=False,
                             verbosity=logging.INFO,
                             **time_series_settings)
```

## <a name="optional-configurations"></a>可选配置

其他可选配置可用于预测任务，如启用深度学习和指定目标滚动窗口聚合。 

### <a name="enable-deep-learning"></a>启用深度学习

> [!NOTE]
> 自动机器学习中预测的 DNN 支持为 **预览版** ，不支持本地运行。

您还可以利用深度学习和深层神经网络（Dnn）来改进模型的分数。 通过自动化 ML 的深度学习，可预测单变量和多变量时序数据。

深度学习模型具有三个固有功能：
1. 可以从任意输入到输出映射进行学习
1. 支持多个输入和输出
1. 它们可以自动提取跨越较长序列的输入数据中的模式。 

若要启用深度学习，请 `enable_dnn=True` 在对象中设置 `AutoMLConfig` 。

```python
automl_config = AutoMLConfig(task='forecasting',
                             enable_dnn=True,
                             ...
                             **time_series_settings)
```
若要为 Azure 机器学习 studio 中创建的 AutoML 实验启用 DNN，请参阅 studio 操作指南中的 " [任务类型" 设置](how-to-use-automated-ml-for-ml-models.md#create-and-run-experiment)。

自动化 ML 作为推荐系统的一部分向用户提供原生时序和深度学习模型。 

模型| 说明 | 优点
----|----|---
Prophet（预览版）|Prophet 最适合用于受季节影响大且包含多个季节历史数据的时序。 若要利用此模型，请使用 `pip install fbprophet` 在本地安装它。 | 准确、快速、可靠地反应时序中的离群值、缺失数据和巨大变化。
Auto-ARIMA（预览版）|当数据为静止时，自动回归集成的移动平均线 (ARIMA) 性能最佳。 这意味着其统计属性（例如平均值和方差）在整个集中保持不变。 例如，如果你掷一枚硬币，那么无论是今天掷、明天掷还是明年掷，正面朝上的可能性都是 50%。| 适用于单变量系列，这是因为使用过去的值来预测未来的值。
ForecastTCN（预览版）| ForecastTCN 是一种神经网络模型，旨在处理最苛刻的预测任务，从而捕获数据中的非线性本地和全局趋势以及时序之间的关系。|可利用数据中的复杂趋势并轻松扩展到最大型的数据集。


查看[饮料制造预测笔记本](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb)，获取使用 DNN 的详细代码示例。


### <a name="target-rolling-window-aggregation"></a>目标滚动窗口聚合
通常，目标的最新值是预测程序能具有的最佳信息。  目标滚动窗口聚合允许添加数据值的滚动聚合作为功能。 通过生成和使用这些附加特征作为额外的上下文数据，可帮助提高训练模型的准确性。

例如，假设您想要预测能源需求。 你可能想要添加三天滚动窗口功能，以用于对加热空间进行热量更改。 在此示例中，通过 `target_rolling_window_size= 3` 在构造函数中设置来创建此窗口 `AutoMLConfig` 。 

该表显示在应用窗口聚合时产生的特征工程。 **最小值、最大**值和**总和**列基于定义的设置在三个滑动窗口上生成。 每行都有一个新的计算功能（如果时间戳为9月8日，2017 4： 00 (）使用9月 8 2017 日的 **需求值** 计算最大值、最小值和总和值： 00 (-3： 00 (。 3 这个窗口将移位填充其余行的数据。

![替换文字](./media/how-to-auto-train-forecast/target-roll.svg)


请查看使用[目标滚动窗口聚合特征](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)的 Python 代码示例。

## <a name="run-the-experiment"></a>运行试验 

`AutoMLConfig`准备好对象后，可以提交试验。 模型完成后，检索最佳的运行迭代。

```python
ws = Workspace.from_config()
experiment = Experiment(ws, "forecasting_example")
local_run = experiment.submit(automl_config, show_output=True)
best_run, fitted_model = local_run.get_output()
```

## <a name="forecasting-with-best-model"></a>用最佳模型进行预测

使用最佳模型迭代来预测测试数据集的值。

`forecast()`函数允许指定预测的开始时间，这不同于 `predict()` ，后者通常用于分类和回归任务。

在下例中，先将 `y_pred` 中的所有值替换为 `NaN`。 在这种情况下，预测源将位于定型数据的结尾。 但是，如果只将 `y_pred` 的后半部分替换为 `NaN`，则函数不会修改前半部分的数值，而会在后半部分预测 `NaN` 值。 函数将返回预测值和对齐的特征。

还可以在 `forecast()` 函数中使用 `forecast_destination` 参数，预测到指定日期为止的值。

```python
label_query = test_labels.copy().astype(np.float)
label_query.fill(np.nan)
label_fcst, data_trans = fitted_pipeline.forecast(
    test_data, label_query, forecast_destination=pd.Timestamp(2019, 1, 8))
```

计算 `actual_labels` 在实际值与中预测值之间 (RMSE) 的根本平均平方值 `predict_labels` 。

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(actual_labels, predict_labels))
rmse
```

现在确定了模型的整体准确性，最现实的下一步是使用模型来预测未知的未来值。 

提供与测试集 `test_data` 具有相同格式但具有未来日期时间的数据集，生成的预测集就是每个时序步骤的预测值。 假设数据集中最后的时序记录针对的是 2018/12/31。 若要预测次日的需求（或者小于或等于 `forecast_horizon` 的待预测时间段），请为每个商店创建 2019/01/01 的一条时序记录。

```output
day_datetime,store,week_of_year
01/01/2019,A,1
01/01/2019,A,1
```

重复执行必要的步骤，将此未来数据加载到数据帧，然后运行 `best_run.predict(test_data)` 以预测未来值。

> [!NOTE]
> 不能预测大于 `forecast_horizon` 的时间段数的值。 必须使用更大的时间范围对模型进行重新训练，才能预测当前时间范围之外的未来值。


## <a name="example-notebooks"></a>示例笔记本
请参阅[预测示例笔记本](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)，了解高级预测配置的详细代码示例，其中包括：

* [假日检测和特征化](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [滚动原点交叉验证](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [可配置滞后](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [滚动窗口聚合特征](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [DNN](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb)

## <a name="next-steps"></a>后续步骤

* 遵循[教程](tutorial-auto-train-models.md)了解如何通过自动化机器学习创建试验。
* 查看[适用于 Python 的 Azure 机器学习 SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) 参考文档。
