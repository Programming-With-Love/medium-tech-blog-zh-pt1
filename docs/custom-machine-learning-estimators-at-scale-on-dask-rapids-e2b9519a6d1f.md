# Dask & RAPIDS 上的大规模定制机器学习估算器

> 原文：<https://medium.com/capital-one-tech/custom-machine-learning-estimators-at-scale-on-dask-rapids-e2b9519a6d1f?source=collection_archive---------4----------------------->

## 如何构建与 Scikit-learn、Dask 和 RAPIDS 集成的可重用组件

![](img/0dc53b8b72085f79996018505bcd11df.png)

在构建可重用的数据科学和机器学习代码时，开发人员通常需要围绕现有的开源库(如 scikit-learn)添加定制的业务逻辑。这些定制可以执行数据预处理、以特定方式分割数据或者实现专有算法。自定义逻辑导致需要理解和维护更多的代码，这增加了复杂性并带来了风险。这篇博客文章将讨论如何利用 [scikit-learn 库的 API](https://scikit-learn.org/stable/developers/develop.html) 来添加这样的定制，以最大限度地减少代码、减少维护、方便重用，并提供利用 [Dask](https://dask.org/) 和 [RAPIDS](https://rapids.ai/) 等技术[扩展](/capital-one-tech/dask-and-rapids-the-next-big-things-for-data-science-and-machine-learning-at-capital-one-d4bba136cc70)的能力。

# 为什么在建模代码时很难遵循 scikit-learn API？

任何机器学习努力的最终目标通常是尽快获得给定数据集的最佳模型。在机器学习中，太多的时间花费在数据处理、模型训练和验证上，以至于在这个过程中创建的代码的设计和可维护性有时会被忽略。在一个资源有限的世界里，也许这种优先排序是有意义的。毕竟，将投入生产的是模型，而不是产生它的代码。在银行和其他金融机构中，模型在投入生产之前必须经过验证过程，其中可重复性至关重要。一旦模型被部署，生成它的代码通常会在 git 存储库中几周、几个月甚至几年不动。直到有一天，一个模型开发人员，可能是也可能不是最初的作者，会回到代码中来更新模型。他们将大大受益于一个经过良好测试和记录的易于理解的存储库，这样他们可以快速开始工作，从而节省时间和成本。

在实践中，遵循标准并应用它们可能具有挑战性，因为

*   标准在应用之前必须被理解。
*   对你的问题应用一个标准需要努力。

克服这些挑战需要深思熟虑，需要宝贵的时间，而这些时间在数据科学项目中可能并不充裕。PyData 生态系统由许多不断发展的标准和 API 组成，因此很难跟上这些变化。所有这些都会减慢模型开发过程，因此阻力最小的方法是使用包装在您自己的设计中的最适合您的应用程序的库。然而，这通常会导致一些问题，比如由于用户错误或重复代码导致的数据泄露，因为设计不够模块化，不允许扩展。在后一种情况下，维护开销很高，因为代码库越来越大越来越复杂，即使使用 Dask 和 RAPIDS，修复 bug 或扩展也变得非常困难。

# sci kit-学习示例

我们将使用 scikit-learn [文档](https://scikit-learn.org/stable/tutorial/statistical_inference/putting_together.html#pipelining)中的以下示例来说明本文中的观点。

```
from sklearn import datasets
from sklearn.decomposition import PCA
from sklearn.linear_model import LogisticRegression
from sklearn.pipeline import Pipeline
from sklearn.model_selection import GridSearchCV, train_test_split
import numpy as np

X_digits, y_digits = datasets.load_digits(return_X_y=True)

# Define a pipeline to search for the best combination of PCA truncation
# and classifier regularization.
pca = PCA()
# set the tolerance to a large value to make the example faster
logistic = LogisticRegression(max_iter=10000, tol=0.1)
pipe = Pipeline(steps=[('pca', pca), ('logistic', logistic)])
# Parameters of pipelines can be set using ‘__’ separated parameter names:
param_grid = {
    'pca__n_components': [5, 15, 30, 45, 64],
    'logistic__C': np.logspace(-4, 4, 4),
}

search = GridSearchCV(pipe, param_grid, n_jobs=-1)

X_train, X_test, y_train, y_test = train_test_split(X_digits, y_digits, random_state=123)

search.fit(X_train, y_train)

best = search.best_estimator_

print(f"Training set score: {best.score(X_train, y_train)}")
print(f"Test set score: {best.score(X_test, y_test)}")
```

输出:

```
Training set score: 1.0
Test set score: 0.9666666666666667
```

# 定制 Scikit-learn 示例

假设我们想修改这个例子，以便在 PCA 步骤之后改变数据。也许我们有一组总是想从另一个数据源中包含进来的特性，或者需要对数据进行标记。我们可以通过移除管道并添加一个函数来对`X`执行变异来做到这一点。

```
def mutate(X):
    """Mutates X"""
    # ... do something ...
    return X

pca = PCA(n_components=search.best_params_['pca__n_components'])
logistic = LogisticRegression(
    max_iter=10000, tol=0.1, C=search.best_params_['logistic__C'])

X_train, X_test, y_train, y_test = train_test_split(
    X_digits, y_digits, random_state=123)

X_train = pca.fit_transform(X_train, y_train)
X_train = mutate(X_train)
logistic = logistic.fit(X_train, y_train)

X_test = pca.transform(X_test) # <- Don't call fit again!
X_test = mutate(X_test) # <-Don’t forget to call mutate on X_test!

print(f"Training set score: {logistic.score(X_train, y_train)}")
print(f"Test set score: {logistic.score(X_test, y_test)}")
```

输出:

```
Training set score: 1.0
Test set score: 0.9666666666666667
```

这允许在不要求用户了解 scikit-learn 管道的情况下进行定制。然而，如果你与其他团队合作或者为其他团队开发一个库，这种模式很快就会出现问题。这段代码可以被封装到一个类中，然后被多次复制。其中一些类甚至可以稍微修改一下，以增加扩展的功能或规模。此外，如果您在其中一个类中发现了一个 bug，您可能有很多地方可以修复它。*狩猎快乐！*

考虑通过添加包含变异逻辑的定制估计器来扩展 scikit-learn API 的替代方法。

```
from sklearn.base import BaseEstimator, TransformerMixin
from abc import ABCMeta

class Mutate(TransformerMixin, BaseEstimator, metaclass=ABCMeta):
    def fit(self, X, y):
        return self

    def transform(self, X):
        """Mutates X"""
        # ... do something ...
        return X

pca = PCA(n_components=search.best_params_['pca__n_components'])
logistic = LogisticRegression(max_iter=10000, tol=0.1, C=search.best_params_['logistic__C'])

pipe = Pipeline(steps=[('pca', pca), ('mutate', Mutate()), ('logistic', logistic)])

X_train, X_test, y_train, y_test = train_test_split(X_digits, y_digits, random_state=123)

pipe.fit(X_train, y_train)
print(f"Training set score: {pipe.score(X_train, y_train)}")
print(f"Test set score: {pipe.score(X_test, y_test)}")
```

输出:

```
Training set score: 1.0
Test set score: 0.9666666666666667
```

通过添加一个定制的估计器，我们以一种允许我们将它插入到流水线的确切步骤中的方式封装了变异。这种方法的另一个好处是，我们只需要维护一个类，它可以随着时间的推移而增长，以包含更多的功能和规模。

```
def transform(self, X):
    """Mutates X"""
    # ... do something ...
    if is_dask_collection(X):
        # Do Dask things
    elif is_rapids_collection(X):
        # Do RAPIDS things
    return X
```

这种方法的主要缺点，也可能是为什么没有被采用的原因，是开发人员需要对“scikit-learn”有足够的理解，以便将问题纳入 API。

# Dask & RAPIDS 中现有的可伸缩自定义估算器

实际上这不是一个新的模式。事实上，在 PyData 社区中，我们已经有了大量自定义可伸缩估计器的例子。 [dask-ml](https://ml.dask.org/) 是一个 scikit-learn 扩展库，它使用 dask 扩展数据并执行并行计算。它为 scikit-learn 估算器提供了许多替代产品。

下面是 dask-ml 的玩具示例管道的样子。

```
from dask.distributed import Client, progress
client = Client(processes=False, threads_per_worker=4,
                n_workers=1, memory_limit='3GB')from dask_ml.datasets import make_classification
from dask_ml.decomposition import PCA
from dask_ml.linear_model import LogisticRegression
from dask_ml.model_selection import GridSearchCV, train_test_split
from sklearn.pipeline import Pipeline  # <-- using the sklearn pipeline
import numpy as np

X, y = make_classification(n_samples=1000, n_features=20,
                           chunks=100, n_informative=4,
                           random_state=0)

# Define a pipeline to search for the best combination of PCA truncation
# and classifier regularization.
pca = PCA()
# set the tolerance to a large value to make the example faster
logistic = LogisticRegression(fit_intercept=False, max_iter=10000, tol=0.1)
pipe = Pipeline(steps=[('pca', pca), ('logistic', logistic)])
# Parameters of pipelines can be set using ‘__’ separated parameter names:
param_grid = {
    'pca__n_components': [5, 15, 30, 45, 64],
    'logistic__C': np.logspace(-4, 4, 4),
}

search = GridSearchCV(pipe, param_grid, n_jobs=-1)

X_train, X_test, y_train, y_test = train_test_split(X, y, random_state=123)

search.fit(X_train, y_train)

best = search.best_estimator_

print(f"Training set score: {best.score(X_train, y_train)}")
print(f"Test set score: {best.score(X_test, y_test)}")
```

或者，你可以使用 [cuML](https://docs.rapids.ai/api/cuml/stable/) 的插件来扩展 NVIDIA GPU 的 RAPIDS。

```
from dask.distributed import Client
from dask_cuda import LocalCUDACluster
from cuml.dask.datasets.classification import make_classification

cluster = LocalCUDACluster()
client = Client(cluster)

X, y = make_classification(n_samples=1000, n_features=20,
                           chunks=100, n_informative=4,
                           random_state=0)from cuml.dask.decomposition import PCA
from cuml.linear_model import LogisticRegression

pca = PCA()
# set the tolerance to a large value to make the example faster
logistic = LogisticRegression(max_iter=10000, tol=0.1)
pipe = Pipeline(steps=[('pca', pca), ('logistic', logistic)])
```

# 设计您自己的 scikit-learn 评估工具，与 Dask & RAPIDS 一起扩展

至此，我们已经展示了如何以两种方式扩展相同的管道，您可能会看到一种模式的出现。数据结构和建模算法依赖于相同的底层对应库，但是彼此之间是松散耦合的。换句话说，我们已经将数据加载逻辑从计算中分离出来，它依赖于类似于*数组的*或*数据框架* API。在引擎盖下，我们看到 Dask 估计器知道如何处理 Dask 集合，而 cuML 估计器知道如何处理 RAPIDS 集合。如果我们使用与估算器相匹配的库来读取数据，一切都会正常。我们是否可以按照这种模式构建自己的评估器，以一种在 Dask 和 RAPIDS 上都可伸缩的方式封装定制的业务逻辑？

首先，让我们看看内存、分布式和加速框架读取数据的方法。

```
import pandas as pd
df = pd.read_csv(...)

import dask.dataframe as dd
df = dd.read_csv(...)

import cudf as cdf
df = cdf.read_csv(...)
```

请注意，所有这三个库都提供了可以用作抽象层的替代功能，将读取数据归纳到内存和数据操作中。一旦我们有了一个类似于 *dataframe 的*结构，我们就可以定义一个估算器，对数据 API 进行仔细的假设。

```
from mylib import CustomEstimator
est = CustomEstimator(**params)
est.fit(df)
```

或者，如果我们希望在阵列接口上实现标准化，我们估计器的拟合方法可以接受 X 和 y。我们发现，*类似数组的* API 在三个框架中对于 scikit-learn 任务更加一致，这使得我们的定制估算器代码对于扩展更加通用。下面我们展示了对类似于*数组的*接口的`CustomEstimator`用法的一个略微修改的版本。

```
from mylib import CustomEstimator
est = CustomEstimator(**params)
X, y = df[features].values, df[target].values
est.fit(X, y)
```

# 定制 sci kit-学习估计器

了解了将数据读取和操作从业务逻辑中分离出来的基本设计之后，让我们看一个例子，看看这在实践中可能是什么样子。下面，我们定义了自己的自定义交叉验证类`CustomSearchCV`，它实现了遵循 scikit-learn API 的模型训练逻辑的内存版本。

```
from sklearn.base import BaseEstimator, clone
from sklearn.model_selection import train_test_split
import copy
import pandas as pd
import numpy as np

class CustomSearchCV(BaseEstimator):

    def __init__(self, estimator, cv, logger):
        self.estimator = estimator
        self.cv = cv
        self.logger = logger

    def fit(self, X, y=None, **fit_kws):
        if isinstance(X, pd.DataFrame):
            X = X.values
        # Insert more guards here!

        X_base, X_holdout, y_base, y_holdout = train_test_split(
            X, y, random_state=123)

        self.split_scores_ = []
        self.holdout_scores_ = []
        self.estimators_ = []            

        for train_idx, test_idx in self.cv.split(X_base, y_base):
            X_test, y_test = X_base[test_idx], y_base[test_idx]
            X_train, y_train = X_base[train_idx], y_base[train_idx]

            estimator_ = clone(self.estimator)
            estimator_.fit(X_train, y_train, **fit_kws)

            self.logger.info("... log things ...")
            self.estimators_.append(estimator_)
            self.split_scores_.append(estimator_.score(X_test, y_test))            
            self.holdout_scores_.append(
                estimator_.score(X_holdout, y_holdout))

        self.best_estimator_ = \
                self.estimators_[np.argmax(self.holdout_scores_)]
        return self
```

`CustomSearchCV`可以很好地与现有的估计器一起工作，比如`sklearn.model_selection.RepeatedKFold`和`xgboost.XGBRegressor`。用户甚至可以定义自己的折叠类，并将其注入到我们的估计器中。下面显示了一个用法示例。

```
from sklearn.model_selection import RepeatedKFold
import xgboost as xgb

from mylib import make_classifier_data, logger

X, y = make_classifier_data(
    n_samples=100_000,
    n_features=100,
    response_rate=0.25,
    predictability=0.25,
    random_state=123,
)

cv = RepeatedKFold(n_splits=2, n_repeats=2, random_state=2652124)
clf = CustomSearchCV(xgb.XGBClassifier(n_jobs=-1), cv, logger)

clf.fit(X, y)
clf.best_estimator_
```

# 使用 Dask 扩展

`CustomSearchCV`可以使用 Dask 集合，只需对 fit 方法稍作修改。首先，创建一个 Dask 客户机来连接到您的集群。

```
from dask.distributed import Client, progress

client = Client()
client
```

然后添加以下逻辑来检查输入数据，以确定它是否是 Dask 集合。

```
from dask.base import is_dask_collection
import dask.dataframe as dd
...
    def fit(self, X, y=None, **fit_kws):
        if isinstance(X, dd.DataFrame):
            X = X.to_dask_array(lengths=True)
        elif isinstance(X, pd.DataFrame):
            X = X.values
        if is_dask_collection(X):
            from dask_ml.model_selection import train_test_split
        else:
            from sklearn.model_selection import train_test_split
        ...
```

现在，我们可以使用 Dask 读取(或生成)数据，并将支持 Dask 的估计器注入到我们的`CustomSearchCV`对象中。在这种情况下，我们注入`xgb.dask.DaskXGBClassifier`。

```
from sklearn.model_selection import RepeatedKFold
import xgboost as xgb

from mylib import logger, make_classifier_data

X, y = make_classifier_data(
    n_samples=100_000,
    n_features=1000,
    response_rate=0.25,
    predictability=0.25,
    random_state=123,
    dask_ml=True,
    chunks=1000
)

cv = RepeatedKFold(n_splits=2, n_repeats=2, random_state=2652124)
clf = CustomSearchCV(xgb.dask.DaskXGBClassifier(), cv, logger)

clf.fit(X, y)
clf.best_estimator_
```

# 使用 GPU 扩展

## 单个 GPU

在对 CustomSearchCV 进行类似的修改后，我们可以通过用`tree_method="gpu_hist"`初始化`xgb.XGBClassifier`来在单个 GPU 上执行训练。在这种情况下，我们不需要修改数据读取(或生成)，因为 XGBoost 知道如何将数据移动到 GPU 上。

```
from sklearn.model_selection import RepeatedKFold
from mylib import make_classifier_data, logger
import xgboost as xgb

X, y = make_classifier_data(
    n_samples=100_000,
    n_features=1000,
    response_rate=0.25,
    predictability=0.25,
    random_state=123
)
cv = RepeatedKFold(n_splits=2, n_repeats=2, random_state=2652124)
xgb_clf = xgb.XGBClassifier(n_jobs=-1, tree_method="gpu_hist")
clf = CustomSearchCV(xgb_clf, cv, logger)
clf.fit(X, y)
clf.best_estimator_
```

## 单节点、多 GPU

许多系统都有多个 GPU，可以使用 Dask 和 RAPIDS 组合成一个主机集群。下面，我们用`tree_method="gpu_hist"`初始化`xgb.dask.DaskXGBClassifier`，并将其连接到一个`dask_cuda.LocalCUDACluster`。默认情况下，`LocalCUDACluster`会为主机上的每个 GPU 添加一个 cuda-worker (GPU worker)。如果我们在一个有八个 GPU 的系统上运行这段代码，我们将有一个有八个工作线程的集群。 [NVLink](https://www.nvidia.com/en-us/design-visualization/nvlink-bridges/) 和 [Apache Arrow](https://arrow.apache.org/) 允许在 GPU 之间进行极其高效的分布式数据访问。

此外，随着 GPU 内存的填满，数据将溢出到系统内存，这通常比 GPU 上可用的内存大得多。这使得单节点、多 GPU 计算非常适合许多数据科学和机器学习问题。下面的例子显示了没有修改`CustomSearchCV`类的用法。

```
from sklearn.model_selection import RepeatedKFold
from mylib import make_classifier_data, logger

import xgboost as xgb

cv = RepeatedKFold(n_splits=2, n_repeats=2, random_state=2652124)
dclf = xgb.dask.DaskXGBClassifier(tree_method="gpu_hist")
clf = CustomSearchCV(dclf, cv, logger)from dask.distributed import Client
from dask_cuda import LocalCUDACluster

cluster = LocalCUDACluster()
client = Client(cluster)X, y = make_classifier_data(
    n_samples=100_000,
    n_features=1000,
    response_rate=0.25,
    predictability=0.25,
    random_state=123,
    dask_ml=True,
    chunks=1000
)
X = X.persist()

dclf.client = client
clf.fit(X, y)
clf.best_estimator_
```

运行这段代码时，检查 Dask 仪表板和 GPU 利用率。请注意，Dask 集群正忙于工作，然后在 GPU 利用率达到峰值时暂停。这里我们在系统内存和 CPU 上分割 Dask 中的数据，然后在 GPU 上训练 [XGBoost](https://xgboost.readthedocs.io/en/latest/tutorials/model.html) 模型。未来的改进是在 GPU 上执行训练分割。

`CustomSearchCV`估计器可以包含定制逻辑，用于可伸缩的超参数调优，或者执行一些修剪、正则化和早期停止技术，这些技术在之前关于[控制 XGBoost 模型](https://www.capitalone.com/tech/machine-learning/how-to-control-your-xgboost-model/)的帖子中已经讨论过。

# 笔记

以下是一些需要考虑的额外注意事项:

*   在 fit 过程的早期对内部数据结构的类数组接口进行标准化，可以降低与 Dask 和 RAPIDS 的伸缩相关的复杂性。
*   确保从数据帧中提取的 X 值仅包含用于训练的特征，并将标签分离为 1d 数组或 pd.Series。
*   确保 X 值不包含用于分段的列，如日期。
*   尽量减少数据帧到数组的转换，以避免性能瓶颈。
*   方法应该返回与输入类型匹配的集合。
*   开发人员应该使用`sklearn.utils.estimator_checks`中的 check_estimator 助手函数来验证他们的定制估算器是否符合 API。

# 结论

在本文中，我们讨论了向 scikit-learn 建模代码添加自定义功能的模式。我们发现，通过遵循 scikit-learn API，我们可以最小化定制并将缩放逻辑封装在一个地方。随着时间的推移，这降低了维护成本，并为开发人员提供了如何将他们的代码集成到生态系统中的示例。遵循一个标准的 API 允许我们共享估算器，并组合它们来服务于许多用例。

*原载于*[*https://www.capitalone.com*](https://www.capitalone.com/tech/machine-learning/custom-machine-learning-estimators-on-dask-and-rapids/)*。*

*披露声明:2021 资本一。观点是作者个人的观点。除非本帖中另有说明，否则 Capital One 不隶属于所提及的任何公司，也不被这些公司认可。使用或展示的所有商标和其他知识产权是其各自所有者的财产。*