# 颤动:下拉按钮部件

> 原文：<https://medium.com/globant/flutter-dropdown-button-widget-267d8e11a8cc?source=collection_archive---------0----------------------->

![](img/a4883dc9d7cac86492eef21d93016b1c.png)

*   下拉按钮是一个材料设计按钮，用于从项目列表中进行选择。
*   该按钮显示当前选定的项目以及一个箭头，该箭头打开用于选择另一个项目的菜单。
*   它使用户选择项目变得容易。下拉按钮让用户从许多项目中进行选择。
*   type `T`是每个下拉项目所代表的值的类型。
*   项目中的每个 DropDownMenuItem 必须专用于相同的类型参数。
*   下拉按钮示例:-

```
class _DropDownList extends State<DropDownList> {
  String dropdownValue = 'One';

  @override
  Widget build(BuildContext context) {
    return DropdownButton<String>(
      value: dropdownValue,
      icon: Icon(Icons.*arrow_drop_down_circle*),
      iconSize: 30,
      elevation: 16,
      style: TextStyle(color: Colors.*teal*),
      underline: Container(
        height: 2,
        color: Colors.*black*,
      ),
      onChanged: (String newValue) {
        setState(() {
          dropdownValue = newValue;
        });
      },
      items: <String>['One', 'Two', 'Three', 'Four']
          .map<DropdownMenuItem<String>>((String value) {
        return DropdownMenuItem<String>(
          value: value,
          child: Text(value),
        );
      }).toList(),
    );
  }
}
```

*   在 DropDownButton 中有一个 onChanged 回调函数，它更新定义下拉列表值的状态。
*   注意:如果 **onChanged** callback 为空或者 [items](https://api.flutter.dev/flutter/material/DropdownButton/items.html) 的列表为空，那么下拉按钮将被禁用，即它的箭头将显示为灰色，并且不响应输入。

快乐阅读:)