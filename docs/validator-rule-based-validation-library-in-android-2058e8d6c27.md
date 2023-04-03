# 验证器| Android 中基于规则的验证库

> 原文：<https://blog.kotlin-academy.com/validator-rule-based-validation-library-in-android-2058e8d6c27?source=collection_archive---------0----------------------->

![](img/bee0307b3c5ae840b1914a4e4141575a.png)

最近，我为我的所有输入编写了一个基于规则的验证结构。它非常灵活且易于管理。今天我将试着解释一下**验证器**库。

**导入**

**验证器，**由 4 个组件组成:

*   IValidatable
*   可验证的
*   NotiftyType
*   验证器

## IValidatable

这是一个验证规则的接口。它包括输入、规则和当前错误文本。

还有，这里有一个函数叫做**is valid(notifytype)。**它必须覆盖实现 IValidatable 的函数。

`var input: String //input field value`

`var currentErrorText: String //invalid rule error text`

`val rules: MutableList<BaseValidatableRule> //input's validatable rules`

## 可验证的

ValidatableRule 从 **BaseValidatableRule 扩展而来。**现在可以使用通用规则进行输入验证了。

```
open class BaseValidatableRule (
    open val errorMessage: String,
    open val notifyType: NotifyType,
    open val rule: (input: String) -> Boolean
)
```

BaseValidatableRule 由 3 个属性组成:

*   错误消息
*   notifyType
*   规则→要返回的操作有效

## NotifyType

```
enum class NotifyType {
    ON_VALUE_CHANGE,
    ON_FOCUS_CHANGE,
    ON_FORM_SUBMIT
}
```

表示开始表单验证的一种常见方式。我们可能希望在不同的用户交互上验证输入:文本改变、焦点丢失或表单主操作点击。

## 验证器

神奇之处就在这里。一切都发生在验证器类中。它实现了 **IValidatable** 。

```
class Validator(
    override var onValidation(Booelan, String?, NotifyType) -> Unit
) : IValidatable
```

验证程序需要对构造函数进行操作。动作是给我们验证值。(isValid，errorText，notifyType)

```
constructor(
    vararg rules: BaseValidatableRule,
    onValidation: (Boolean, String?, NotifyType) -> Unit
) : this(onValidation) {
    this.rules.addAll(rules)
}
```

您可以使用另一个带有**规则列表(vararg →一个或多个规则)**和 **onValidation action、**的构造函数，但是我推荐使用 **Builder。**

类似于抽象类的验证器。它隐藏了普通的**逻辑的东西**。

**isValid** 函数与 notifyType **一起工作。**通过 notifyType 过滤规则**，开始匹配的验证控制。**

**如果 **filteredRules** 中任何规则的操作返回 false，currentErrorText** 将更新。

> 错误文本仅在经过筛选的规则下更改，但它是一个适用于所有规则的有效控件。

输入或 isFocused、validate 函数的每个值更改都将由相关的 NotifyType 触发。

*   输入更改→ NotifyType。开值变化
*   isFocused change → NotifyType 聚焦于变化

> 如果只选中 NotifyType，则必须调用 submitForm()函数。表单提交规则。它返回布尔 isValid 值。

## 建设者

**构建器，**用很少的步骤创建验证实例的类。

*   用**添加规则**添加一个或多个规则
*   用 **addCollector** 添加数据收集器(输入更新、焦点更新)
*   管理 **onValidate** 块中的验证结果
*   用 **build()** 获取验证实例

这就是关于**验证器的简要信息。如果你想了解更多，你应该去看看 Github 库。**

不要保留你的拉取请求或关于它的消息。如果你喜欢**验证器，**请滴👏还有⭐️ ⭐️

**Github:**

[](https://github.com/mustafayigitt/validator) [## GitHub - mustafayigitt/validator:通知输入字段的基于类型的验证。

### 通知输入字段的基于类型的验证。通过创建一个帐户为 mustafayigitt/validator 开发做贡献…

github.com](https://github.com/mustafayigitt/validator) 

**领英:**

[](https://www.linkedin.com/in/mustafayigitt/) [## Mustafa yi it-Android 开发者- Apsiyon | LinkedIn

### 查看 Mustafa Yiğ it 在全球最大的职业社区 LinkedIn 上的个人资料。穆斯塔法有 4 份工作列在…

www.linkedin.com](https://www.linkedin.com/in/mustafayigitt/) ![](img/e452e4fc7b1eea8b55443068d2db3db8.png)