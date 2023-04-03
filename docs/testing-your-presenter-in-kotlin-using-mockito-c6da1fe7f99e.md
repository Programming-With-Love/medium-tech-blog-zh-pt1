# 使用 Mockito 在 Kotlin 中测试您的演示者

> 原文：<https://medium.com/globant/testing-your-presenter-in-kotlin-using-mockito-c6da1fe7f99e?source=collection_archive---------4----------------------->

这是一个 ***非常基本的*** 示例，说明如何在演示器中测试您的功能。这里，通过 ***非常基本的*** ，我指的是在同一线程上处理数据而不是从一些 API 调用或数据库中获取数据的函数。我将在下一页介绍。

我举了一个非常简单的例子，点击一个按钮，在文本视图中显示用户的全名。

所以，你有一些明显的东西。

> 主要活动

```
class MainActivity : AppCompatActivity(), MainView {

    private lateinit var presenter: MainPresenter

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.*activity_main*)
        presenter = MainPresenter(this)
    }

    fun onClickFetchData(view: View){
        presenter.fetchData()
    }

    override fun setUserName(name: String) {
        tvName.*text* = name
    }
}
```

> 主演示者

我们只需要测试 presenter 中的一个函数。

注意:*没有 API 调用或在不同的线程上获取数据。为了测试这些函数，你需要使用一个叫做* [*的东西。我将在下一页讨论这个问题。*](https://static.javadoc.io/org.mockito/mockito-core/2.2.9/org/mockito/ArgumentCaptor.html)

```
class MainPresenter(val view: MainView) {

    fun fetchData() {
        val user = User("Rana Ranvijay","Singh")
        view.setUserName("${user.firstName} ${user.lastName}")
    }
}
```

> 主视图

```
interface MainView {
    fun setUserName(name: String)
}
```

> 用户

```
data class User(
        val firstName: String,
        val lastName: String)
```

> **build.gradle**

```
testImplementation 'junit:junit:4.12'
testImplementation 'org.mockito:mockito-core:2.23.0'
```

现在在***app/src/test/Java/<包>*** 中创建一个测试类

> 主演示者测试

```
import org.junit.Before
import org.junit.Test
import org.junit.runner.RunWith
import org.mockito.Mock
import org.mockito.Mockito
import org.mockito.junit.MockitoJUnitRunner

@RunWith(MockitoJUnitRunner::class)
class MainPresenterTest {

    @Mock
    lateinit var view: MainView

    lateinit var presenter: MainPresenter

    @Before
    fun setUp() {
        presenter = MainPresenter(view)
    }

    @Test
    fun testFetchData() {
        presenter.fetchData()
        Mockito.verify(view).setUserName("Rana Ranvijay Singh")
    }
}
```

还有**跑！**