# 揭秘科特林密封类

> 原文：<https://blog.kotlin-academy.com/demystifying-kotlin-sealed-classes-e90f52610d78?source=collection_archive---------0----------------------->

![](img/b8a0e13da39809e9b04a1def2d5b8761.png)

Photo by [Marc Reichelt](https://unsplash.com/@mreichelt?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

有时在编程时，我们希望以一种清晰而有用的方式定义一些常量。也许我们也想把他们分组，使他们受到限制。你可能听说过 enums。这些都很棒，允许我们拥有常数。像这样:

```
enum class Vehicle() {
    Car,
    Bicycle,
    Truck
}
```

看起来棒极了！

但是，如果我们想在车辆枚举中添加更多的信息呢？比如轮子的数量？我们可以这样做:

```
enum class Vehicle(val wheels: Int) {
    Car(wheels = 4),
    Bicycle(wheels = 2),
    Truck(wheels = 4),
}
```

现在我们有了一个车轮计数，可以很容易地从 enum 中获得:

```
val wheels = Car.wheels // 4
```

但是您已经可以看到这种方法有一个限制。我们可以定义所有常量对象共有的属性。你可以添加速度、颜色或任何你喜欢的东西。这显然适用于大多数情况，但有时你想给一个对象添加更多的东西。不适用于其他人的东西。然后，您可能还想添加一个特定于对象的函数来处理其中的数据。这将是有用的，对不对？科特林密封类来了:

```
sealed class Vehicle(val wheels: Int) {

    object Car : Vehicle(wheels = 4) {}

    object Bicycle : Vehicle(wheels = 2) {}

    object Truck : Vehicle(wheels = 4) {}

}
```

上面的代码片段直接翻译成带有枚举的示例。但是我们不想要功能相同的东西，我们想要能让我们做更多事情的东西。我们有不同的工具，每一个在现实生活中可以做不同的事情，我们想以某种方式定义它。Enums 不允许我们这么做，但是密封类允许。

```
sealed class Vehicle(val wheels: Int) {

    object Car : Vehicle(wheels = 4) {

        fun getHorseSpeed(): String {
            return "100hp"
        }

    }

    object Bicycle : Vehicle(wheels = 2) {

        fun getType(): Int {
            return 1
        }

    }

    object Truck : Vehicle(wheels = 4) {

        fun getMaxLoad(): Int {
            return 3000
        }

    }

}
```

你看到了吗？现在我们在不同的类中有不同的函数，这些函数返回不同车辆的信息。整洁！

但是如果我们把这个再扩大一点呢？我们在这里和班级一起工作，对吗？当然啦！假设我们有一个未知的车辆，并希望它几乎像一个未来的模板，所以从扩展。您可以将开放类定义为密封类的一部分。像这样:

```
sealed class Vehicle(val wheels: Int) {

    object Car : Vehicle(wheels = 4) {

        fun getHorseSpeed(): String {
            return "100hp"
        }

    }

    object Bicycle : Vehicle(wheels = 2) {

        fun getType(): Int {
            return 1
        }

    }

    object Truck : Vehicle(wheels = 4) {

        fun getMaxLoad(): Int {
            return 3000
        }

    }

    open class UnknownVehicle(wheels: Int) : Vehicle(wheels) {}
}
```

请记住，你只能在同一个文件中定义子类，并且只能在知道密封类的范围内定义。

我们可以定义一个抽象函数来强制子类实现它吗？我们当然可以！

```
sealed class Vehicle(val wheels: Int) {

    object Car : Vehicle(wheels = 4) {

        fun getHorseSpeed(): String {
            return "100hp"
        }

        override fun getParkingSpotNumber(): Int {
            return 2
        }
    }

    object Bicycle : Vehicle(wheels = 2) {

        fun getType(): Int {
            return 1
        }

        override fun getParkingSpotNumber(): Int {
            return 5
        }
    }

    object Truck : Vehicle(wheels = 4) {

        fun getMaxLoad(): Int {
            return 3000
        }

        override fun getParkingSpotNumber(): Int {
            return 45
        }
    }

    open class UnknownVehicle(wheels: Int) : Vehicle(wheels) {
        override fun getParkingSpotNumber(): Int? {
            return null
        }
    }

    abstract fun getParkingSpotNumber(): Int?
}
```

这里它以一种普通的方式工作，我们有一个抽象函数，就像一个蓝图，它必须在从拥有它的类中扩展的类/对象中实现。

当我们在 when 语句中使用密封类时，它们的真正魅力就显现出来了。密封类中的每个类型充当一个案例，如果它被定义为一个对象，你甚至不需要使用" *is"* 关键字。同样，根据定义，密封类是受限制的，这就是为什么在定义所有情况时可以省略 *else* 分支。像这样:

```
fun getParkingSpot(vehicle: Vehicle): Int? {
    return when (vehicle) {
        Vehicle.Bicycle -> vehicle.getParkingSpotNumber()
        Vehicle.Car -> vehicle.getParkingSpotNumber()
        Vehicle.Truck -> vehicle.getParkingSpotNumber()
        is Vehicle.UnknownVehicle -> vehicle.getParkingSpotNumber()
    }
}
```

使用密封类时要考虑的一些事情:

*   密封类没有公共构造函数，但是它们有私有构造函数
*   密封类可以有子类，但是这些子类要么嵌套在定义中，要么在同一个文件中
*   密封类不能直接实例化

我希望密封类对你来说比我第一次看到它们时清楚得多。欢迎在评论中提出意见和/或建议。

*节日快乐！*

[![](img/d2e94b1350bc466b5f7351da59a2a1eb.png)](https://kt.academy/article)