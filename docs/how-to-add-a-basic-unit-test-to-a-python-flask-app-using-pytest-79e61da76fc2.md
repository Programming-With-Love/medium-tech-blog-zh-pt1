# 如何使用 Pytest 向 Python Flask 应用程序添加基本单元测试

> 原文：<https://medium.com/globant/how-to-add-a-basic-unit-test-to-a-python-flask-app-using-pytest-79e61da76fc2?source=collection_archive---------0----------------------->

![](img/f46eca599c37bdd38069cc15ca75da52.png)

我们都同意我们需要给我们的应用程序增加一些测试，对吗？如果你不相信，请看看这篇关于[单元测试和重构](/globant/unit-tests-refactoring-should-i-bother-e7b3e65e19c9)的文章。在这篇小文章中，我们将向您展示如何使用 [Pytest](https://docs.pytest.org/en/latest/) 向一个非常基本的 [Flask](https://flask.palletsprojects.com/en/1.1.x/) 应用程序添加一个单独的测试。

**奖励:**我们还将向您展示如何将 Github Actions CI 添加到您的 repo 中。

假设在我们的 home ( `/`)路线中有一个简单的`hello world`响应，就像:

```
from flask import Flask, jsonify

app = Flask(__name__)

@app.route('/')
def index():
    return jsonify({'hello': 'world'})

if __name__ == '__main__':
    app.run(debug=True)
```

因此，让我们首先创建一个`tests`目录和包含以下内容的`conftest.py`文件:

```
import pytestfrom app import app as flask_app @pytest.fixture
def app():
    yield flask_app @pytest.fixture
def client(app):
    return app.test_client()
```

这个文件将初始化我们的 Flask 应用程序和所有你需要的[夹具](https://docs.pytest.org/en/latest/fixture.html)。

现在，Pytest 会发现你所有的测试文件，让我们在同一个目录下创建一些带有`test_`前缀的测试文件。在这种情况下，我们将测试路由是否以预期的 hello world dict 作出响应。

```
import json def test_index(app, client):
    res = client.get('/')
    assert res.status_code == 200
    expected = {'hello': 'world'}
    assert expected == json.loads(res.get_data(as_text=True))
```

就是这样！现在，您可以用下面的代码行运行测试:

```
python -m pytest
```

[上面的命令看起来不典型的原因是我们需要将目录添加到当前的`sys.path`中。](https://docs.pytest.org/en/latest/usage.html#calling-pytest-through-python-m-pytest)

# 奖金

要设置 [GitHub actions](https://github.com/features/actions) 并为您的自述文件获取珍贵的徽章，您只需将以下任务添加到您的 YAML 文件中

```
- name: Install Dependencies
      run: |
        python -m pip install --upgrade pip
        pip install -r requirements.txt
    - name: Run Tests
      run: |
        python -m pytest
```

如果你想把它作为你的应用程序的参考，你可以在这个[样本回购](https://github.com/po5i/flask-mini-tests)中看到完整的 [YAML 文件](https://github.com/po5i/flask-mini-tests/blob/master/.github/workflows/flask.yml)。

我们希望这个小教程在你启动一个新项目或者只是在一个现有的应用程序中添加测试时会有用。