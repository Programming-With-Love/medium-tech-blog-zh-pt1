# 编写一次，在 Android 上到处运行测试

> 原文：<https://medium.com/androiddevelopers/write-once-run-everywhere-tests-on-android-88adb2ba20c5?source=collection_archive---------2----------------------->

![](img/b042bcf8205e597154427ae7bafe8644.png)

在今年的谷歌 I/O 大会上，我们推出了 AndroidX 测试，这是 [Jetpack](https://developer.android.com/jetpack/) 的一部分。今天我们很高兴地宣布 [v1.0.0](https://developer.android.com/training/testing/release-notes) 最终版和 Robolectric v4.0 一起发布。作为 1.0.0 版的一部分，所有的 AndroidX 测试现在都是[开源](https://github.com/android/android-test)。

AndroidX Test 提供跨测试环境的通用测试 API，包括仪器和机器人测试。它包括现有的 Android JUnit 4 支持、Espresso view 交互库和几个新的关键测试 API。这些 API 可用于真实和虚拟设备上的仪器测试。从 Robolectric 4.0 开始，它们也可以用于本地 JVM 测试。

考虑下面的用例，我们启动登录屏幕，输入有效的用户名和密码，并确保我们被带到主屏幕。

```
@RunWith(AndroidJUnit4::class)
class LoginActivityTest { @Test fun successfulLogin() {
    // GIVEN
    val scenario = 
        ActivityScenario.launch(LoginActivity::class.java) // WHEN
    onView(withId(R.id.user_name)).perform(typeText(“test_user”))
    onView(withId(R.id.password))
        .perform(typeText(“correct_password”))
    onView(withId(R.id.button)).perform(click()) // THEN
    assertThat(getIntents().first())
        .hasComponentClass(HomeActivity::class.java)
 }
}
```

让我们逐步完成测试:

1.  我们使用新的 [ActivityScenario](https://developer.android.com/reference/androidx/test/core/app/ActivityScenario) API 来启动 LoginActivity。这将创建活动，并使其进入恢复状态，在此状态下，用户可以看到该活动并准备好输入。练习 Scenario 处理与系统的所有同步，并为您应该测试的常见场景提供支持，例如您的应用程序如何处理被系统销毁和重新创建的情况。
2.  我们使用 Espresso view 交互库在两个文本字段中输入文本，然后单击 UI 中的一个按钮。与 ActivityScenario 类似，Espresso 为您处理多线程和同步，并提供一个可读且流畅的 API 来编写测试。
3.  我们使用新的[intents . getintents()](https://developer.android.com/reference/androidx/test/espresso/intent/Intents.html#getIntents())Espresso API，该 API 返回捕获的意图列表。然后，我们使用 IntentSubject.assertThat()来验证捕获的意图，这是新的 Android 真理扩展的一部分。Android Truth 扩展提供了一个富于表现力和可读性的 API 来验证基本 Android 框架对象的状态。

这个测试可以在使用 Robolectric 或任何物理或虚拟设备的本地 JVM 上运行。

要在 Android 设备上运行它，请将它放在您的“androidTest”源根目录中，并附带以下依赖项:

```
androidTestImplementation(“androidx.test:runner:1.1.0”)
androidTestImplementation(“androidx.test.ext:junit:1.0.0”)
androidTestImplementation(“androidx.test.espresso:espresso-intents:3.1.0”)
androidTestImplementation(“androidx.test.espresso:espresso-core:3.1.0”)
androidTestImplementation(“androidx.test.ext:truth:1.0.0”)
```

在物理或虚拟设备上运行让您确信您的代码可以正确地与 Android 系统交互。然而，随着测试用例数量的增加，您开始牺牲测试执行时间。您可能决定只在真实设备上运行一些较大的测试，而在模拟器上运行大量较小的单元测试，比如 Robolectric，它可以在本地 JVM 上更快地运行测试。

要使用 Robolectric simulator 在本地 JVM 上运行测试，请将测试放在“test”源根目录中，并将以下行添加到 gradle.build 中:

```
testImplementation(“androidx.test:runner:1.1.0”)
testImplementation(“androidx.test.ext:junit:1.0.0”)
testImplementation(“androidx.test.espresso:espresso-intents:3.1.0”)
testImplementation(“androidx.test.espresso:espresso-core:3.1.0”)
testImplementation(“androidx.test.ext:truth:1.0.0”)
testImplementation (“org.robolectric:robolectric:4.0”)android {
    testOptions.unitTests.includeAndroidResources = true
}
```

模拟器和仪器之间测试 API 的统一打开了许多令人兴奋的可能性！我们也在 Google I/O 上宣布的 Project Nitrogen 将允许您在运行时环境之间无缝地移动测试。这意味着您将能够测试针对新的 AndroidX 测试 API 编写的测试，并在本地 JVM、真实或虚拟设备，甚至是基于云的测试平台(如 Firebase Test Lab)上运行它们。这将为开发人员提供获得快速、准确、可行的应用质量反馈的机会，我们对此感到非常兴奋。

最后，我们很高兴地宣布，所有 AndroidX 组件都是完全开源的，我们期待着您的贡献。

# 阅读更多

文档:【https://developer.android.com/testing 

发行说明:

*   AndroidX 测试:[https://developer . Android . com/training/testing/release-notes](https://developer.android.com/training/testing/release-notes)
*   https://github.com/robolectric/robolectric/releases/

https://github.com/robolectric/robolectric

安卓系统测试:[https://github.com/android/android-test](https://github.com/android/android-test)