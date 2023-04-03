# 简单的 Rails CRUD 应用程序，带有 React 前端，使用 React-Rails gem

> 原文：<https://medium.com/quick-code/simple-rails-crud-app-with-react-frontend-using-react-rails-gem-b708b89a9419?source=collection_archive---------0----------------------->

在 Flatiron 学校的训练营期间，我学会了如何创建使用 Rails API 后端和 React 前端的应用程序，这只是结合使用这两种技术的可能方式之一。我决定研究一个不同的场景，在这个场景中，开发人员利用 gem“react-Rails”来直接在 Rails javascript 资产管道中创建和管理 React 组件。

我找到的最好的一篇文章是赫里斯托·乔尔杰夫写的:[https://www . plural sight . com/guides/ruby-ruby-on-rails/building-a-crud-interface-with-react-and-ruby-on-rails](https://www.pluralsight.com/guides/ruby-ruby-on-rails/building-a-crud-interface-with-react-and-ruby-on-rails)。然而，在工作之后，我注意到它主要使用了旧的 React 语法，以及过时的“refs”用法。这篇博客的目标是展示一个构建非常基本的 CRUD 应用程序的类似教程，但是稍微更新了一下 React 语法。

## 用一个模型创建新的 Rails 应用程序

我们将开始创建一个新的 Rails 应用程序，运行以下命令:

```
rails new fruits-app
```

下一个标题到 Gemfile 并添加`gem 'react-rails'`，下一次运行

`bundle install`

然后

`rails g react:install`

React:安装我们在这里使用的生成器会自动将 React JS 库添加到您的资产管道中。

我们将要使用的模型是**水果**，所以让我们继续生成它:

```
rails g model Fruit name:string description:text
```

跳转到 db/seeds.rb 文件并创建一些示例项目:

```
fruits = ['Mango', 'Pineapple', 'Passion fruit', 'Dragonfruit']fruits.each{|fruit| Fruit.create(name: fruit, description: "I am a delicious #{fruit}.")}
```

迁移数据库并设定种子:

```
rake db:migrate db:seed
```

**控制器**

我们只需要对应用程序控制器做一个调整。因此，在 app/controllers/application _ controller . Rb 中粘贴以下代码行:

```
class ApplicationController < ActionController::Base
 **protect_from_forgery with: :null_session**
end
```

我们的应用程序是基于 API 的，因此控制器文件夹结构要求我们遵循 API 版本规范的命名空间约定，例如:app/controllers/api/v1。对 API 进行版本控制意味着将来可以对其进行更改，而不会破坏原始版本。

因此，让我们在 app/controllers 中创建这两个降序文件夹(api 和 v1 ),并在其中创建一个名为`fruits_controller.rb`的新文件

在我们的水果控制器中，让我们定义 CRUD 动作。

```
class Api::V1::FruitsController < ApplicationController
  def index
    render json: Fruit.all
  end

  def create
    fruit = Fruit.create(fruit_params)
    render json: fruit
  end

  def destroy
    Fruit.destroy(params[:id])
  end

  def update
    fruit = Fruit.find(params[:id])
    fruit.update_attributes(fruit_params)
    render json: fruit
  end

  private

  def fruit_params
    params.require(:fruit).permit(:id, :name, :description)
  end
end
```

## 路由控制器

让我们浏览 config/routes.rb 并设置所有必要的 CRUD 路径:

```
Rails.application.routes.draw do 
  namespace :api do 
    namespace :v1 do 
     resources :fruits, only: [:index, :create, :destroy, :update]
    end 
  end 
end
```

此时，如果您在浏览器中导航到[http://localhost:3000/API/v1/fruits . json](http://localhost:3000/api/v1/fruits.json)，您应该会看到 JSON 和我们的 4 个水果。酷！

现在让我们在相应的新文件`app/controllers/home_controller.rb`中再创建一个名为 Home 的控制器，它将只负责一个索引页面，我们将在这里执行所有的 React 魔法。

```
class HomeController < ApplicationController
  def index
  end
end
```

让我们回到`config/routes.rb`一会儿，添加我们的主页索引页面作为根:

```
root to: 'home#index'
```

现在我们的索引页面只需要一个相应的视图。在`app/views`中创建一个新的“home”文件夹，并向其中添加 index.html.erb 文件:

`app/views/home/index.html.erb`

该文件将只包含一行利用了 **react_component** 视图助手的代码，该助手随**‘react-rails’**gem 一起提供。现在还不行，但过一会儿一切都会变得有意义。

`<%= react_component 'Main' %>`

**创建我们的第一个 React 组件**

创建一个名为`app/assets/javascripts/components/_main.js.jsx`的文件

我们将遵循创建无状态/表示性 React 组件的常用语法，我们将称之为 **Main** :

```
const Main = (props) => {
    return(
      <div>
        <h1>Fruits are great!</h1>
      </div>
    )
}
```

在浏览器中转至 **localhost:3000** ，亲自查看我们的第一个组件已加载到页面上。相当酷！

**显示所有水果**

让我们创建下一个显示水果列表的组件。创建一个名为`app/assets/javascripts/components/_all_fruits.js.jsx`的新文件，并定义一个名为 **AllFruits** 的新组件:

```
class **AllFruits** extends **React.Component** {render(){
  return(
    <div>
      <h1>To do: List of fruits</h1>
    </div>
    )
  }
}
```

接下来，让我们将这个组件放入我们的主组件中，并确保它成功加载到页面中。

```
const **Main** = (props) => {
    return(
      <div>
        <h1>Fruits are great!</h1>
        **<AllFruits />**
      </div>
    )
}
```

下一步是为 **AllFruits** 组件创建一个状态，并从我们的 API 中获取所有的水果，最后使用 this.setState()更新在构造函数中创建的空状态。在下一步中，我们将添加处理获取请求的构造函数和 componentDidMount()生命周期挂钩:

```
class **AllFruits** extends **React.Component** {

 ** constructor(props) {
    super(props);
    this.state = {
      fruits: []
    };
  }** **componentDidMount(){
    fetch('/api/v1/fruits.json')
      .then((response) => {return response.json()})
      .then((data) => {this.setState({ fruits: data }) });
  }** render(){
    return(
      <div>
        <h1>To do: List of fruits</h1>
      </div>
     )
   }
}
```

最后，我们将更新 **AllFruits** 组件的 render()方法，以迭代状态中的每个水果，显示每个项目的名称和描述。

```
render(){
 **var fruits = this.state.fruits.map((fruit) => {
  return(
   <div key={fruit.id}>
    <h1>{fruit.name}</h1>
    <p>{fruit.description}</p>
   </div>
  )
 })** return(
  <div>
   **{fruits}**
  </div>
 )
}
```

你会注意到我们使用了一个额外的 **key** 属性，我们将其设置为等于 **fruit.id** 。这是我们以后引用相似项目列表中的特定项目的方式，也是 React 中的一个需求。强烈推荐 2021 年[学 React](https://blog.coursesity.com/best-react-js-tutorials/) 。前往 localhost:3000 查看我们最初的水果清单！

**添加新水果**

在我们创建新的水果组件和表单之前，让我们暂停一下，稍微修改一下我们的应用程序结构。我们希望有一个主要组件来处理**水果**状态的所有变化。让我们再创建一个名为 Body 的更高级别的**容器**组件，并移动带有 state 的构造函数，以及 componentDidMount()从 **AllFruits** 获取部分代码到 **Body** 。创建一个新文件`/app/assets/javascripts/components/_body.js.jsx`

```
class **Body** extends **React.Component** {constructor(props) {
    super(props);
    this.state = {
      fruits: []
    };
  }componentDidMount(){
    fetch('/api/v1/fruits.json')
      .then((response) => {return response.json()})
      .then((data) => {this.setState({ fruits: data }) });
  }render(){
    return(
      <div>
        **<AllFruits fruits={this.state.fruits} />**
      </div>
    )
  }
}
```

更新的 AllFruits 组件可以修改为无状态/表示性的:

```
**const AllFruits = (props) => {**var fruits = **props.fruits**.map((fruit) => {
    return(
      <div key={fruit.id}>
        <h1>{fruit.name}</h1>
        <p>{fruit.description}</p>
      </div>
    )
  })return(
      <div>
       ** {fruits}**
      </div>
    )
**}**
```

更新的主要组件:

```
const Main = (props) => {
    return(
      <div>
        <h1>Fruits are great!</h1>
       ** <Body />**
      </div>
    )
}
```

我们现在从这个更高层次的 Body 组件内部渲染 AllFruits 组件，从而将水果作为道具传递给它。检查你的浏览器，看看你是否正确地完成了所有的事情，然后我们的水果清单就会显示出来。

现在，我们可以向页面添加一个用于创建新水果的表单。创建一个新文件`/app/assets/javascripts/components/_new_item.js.jsx`并用一个包含两个字段的简单表单定义一个新组件 NewFruit:

```
const **NewFruit** = (props) => { let formFields = {}

  return(
    <form>
    ** <input ref={input => formFields.name = input} placeholder='Enter the name of the item'/>
     <input ref={input => formFields.description = input} placeholder='Enter a description' />
     <button>Submit</button>**
    </form>
  )
}
```

你注意到那些`ref = {input => formFields.name = input}`了吗？类似于我们迭代中的**键**，我们使用**引用**来访问某个 DOM 元素。在这种情况下，它将允许我们抓住我们的投入的价值。在我们的应用程序中，我将表单输入存储在一个对象 formFields 中，这样我们就可以稍后使用 onClick()事件处理程序将字段值传递给父组件，我们将把该事件处理程序放在 button 中。

```
class **Body** extends **React.Component** {constructor(props) {
    ...
  }componentDidMount(){
    ...
  }render(){
    return(
      <div>
        **<NewFruit />**
        <AllFruits fruits={this.state.fruits} />
      </div>
    )
  }
}
```

将 **NewFruit** 组件放入 **Body** 中，您将看到我们新制作的表单出现在浏览器中。

下一步是让我们的**提交**按钮工作。

在 Body 组件中，我们将创建 handleFormSubmit()函数，它从表单中接收名称和描述作为参数。现在，它将只是 console.log()参数，让我们看看它是否全部工作。我们还将把这个 handleFormSubmit()函数作为道具传递给 **NewFruit** 组件。

```
class **Body** extends **React.Component** {constructor(props) {
  ...
  **this.handleFormSubmit = this.handleFormSubmit.bind(this)**
}**handleFormSubmit(name, description){
    console.log(name, description)
}**componentDidMount(){
   ...
  }render(){
    return(
      <div>
        <NewFruit **handleFormSubmit={this.handleFormSubmit}**/>
        <AllFruits fruits={this.state.fruits}  />
      </div>
    )
  }
}
```

至于我们的 NewFruit 组件，我们所要做的就是向表单标签添加一个 Submit 事件处理程序，它将从字段中传递值作为道具。我还将我们的事件(e)作为参数传递，以便在表单提交后清除字段:

```
const **NewFruit** = (props) => {

    let formFields = {}

    return(
      <form **onSubmit={ (e) => {** e.preventDefault();
            **props.handleFormSubmit(
              formFields.name.value,
              formFields.description.value
            ); 
            e.target.reset();}
        }**>
        <input ref={input => formFields.name = input} placeholder='Enter the name of the item'/>
        <input ref={input => formFields.description = input} placeholder='Enter a description' />
        <button>Submit</button>
      </form>
    )
}
```

让我们回到身体，实际上处理新水果的整个创作。

```
class **Body** extends **React.Component** { constructor(props) {
    super(props);
    this.state = {
      fruits: []
    };
   ** this.handleFormSubmit = this.handleFormSubmit.bind(this)
    this.addNewFruit = this.addNewFruit.bind(this)**
  } **handleFormSubmit(name, description){
    let body = JSON.stringify({fruit: {name: name, description:   description} })** **fetch('**[**http://localhost:3000/api/v1/fruits'**](http://localhost:3000/api/v1/fruits')**, {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json'
      },
      body: body,
    }).then((response) => {return response.json()})
    .then((fruit)=>{
      this.addNewFruit(fruit)
    })

  }** **addNewFruit(fruit){
    this.setState({
      fruits: this.state.fruits.concat(fruit)
    })
  }**componentDidMount(){
    fetch('/api/v1/fruits.json')
      .then((response) => {return response.json()})
      .then((data) => {this.setState({ fruits: data }) });
  }render(){
    return(
      <div>
        <NewFruit handleFormSubmit={this.handleFormSubmit}/>
        <AllFruits fruits={this.state.fruits}  />
      </div>
    )
  }
}
```

那里刚刚发生了很多事情。在 **handleFormSubmit()** 内部，我们现在使用 **fetch()** 向后端发送一个 POST 请求，其中包含我们新的水果数据。一旦返回 promise，我们就调用新函数 **handleAddNewFruit()** ，该函数将返回的 Fruit 对象作为参数，并使用 this.setState()更新状态，以便立即将这个新的 fruit 加载到页面。相当整洁！现在你可以打开浏览器，亲自看看它是如何工作的。

**删除一个水果**

为我们的水果模型实现完整 CRUD 功能的下一步是处理水果删除。

到目前为止，我们已经将所有的水果作为一个列表加载到 all fruits 中。但是，将水果作为一个单独的实体并将其移动到一个单独的水果组件中是一个很好的做法。就这么办吧！

创建一个新文件`app/assets/javascripts/components/_fruit.js.jsx`。下面是我们的水果组件现在的样子:

```
class **Fruit** extends **React.Component**{

  render(){
    return(
      <div>
        <h1>{**this.props.fruit.name**}</h1>
        <p>{**this.props.fruit.description**}</p>
      </div>
    )      
  }
}
```

如你所见，我们使用 this.props.fruit 加载名称和描述。这意味着我们将从父 AllItems 组件中传递整个水果对象作为道具，如下所示:

```
const **AllFruits** = (props) => {var fruits = props.fruits.map((fruit) => {
    return(
      <div key={fruit.id}>
       **<Fruit fruit={fruit}/>**
      </div>
    )
  })return(
      <div>
        {fruits}
      </div>
    )
}
```

再次检查浏览器，看看您是否正确地实现了所有的更改。页面看起来应该和以前完全一样，所有的水果应该和我们新的水果表单一起显示。

现在让我们给水果添加**删除**按钮。

```
class **Fruit** extends **React.Component**{

  render(){
    return(
      <div>
        <h1>{this.props.fruit.name}</h1>
        <p>{this.props.fruit.description}</p>
        **<button onClick={() => this.props.handleDelete()}>Delete</button>**
      </div>
    )      
  }
}
```

我们还没有创建 **handleDelete()** 函数，但是它将作为一个道具从 Body 传递给 AllItems，然后传递给 Fruit 组件。让我们继续在 Body 组件中定义该函数，在构造函数中绑定它，并作为一个 prop 传递给 AllFruits:

```
class Body extends React.Component {constructor(props) {
    ...
   ** this.handleDelete = this.handleDelete.bind(this)**
  } ** handleDelete(id){
    fetch(`**[**http://localhost:3000/api/v1/fruits/${id}`**](http://localhost:3000/api/v1/items/${id}`)**, 
    {
      method: 'DELETE',
      headers: {
        'Content-Type': 'application/json'
      }
    }).then((response) => { 
        console.log('Item was deleted!')
      })
  }**render(){
    return(
      <div>
        <NewFruit handleFormSubmit={this.handleFormSubmit}/>
        <AllFruits fruits={this.state.fruits} **handleDelete={this.handleDelete}**/>
      </div>
    )
  }
}
```

我们的 handleDelete()的工作方式如下:它接收 fruit 的 id，使用 **fetch()** 和(目前)console.logs **向具有该 id 的 fruit route 发送一个 **DELETE** 请求，“项目已删除！”**。

我们接下来的步骤:

1.  确保所有水果通过**手柄删除**作为水果组件的道具。

```
const **AllFruits** = (props) => {var fruits = props.fruits.map((fruit) => {
    return(
      <div key={fruit.id}>
       <Fruit fruit={fruit} **handleDelete={props.handleDelete}**/>
      </div>
    )
  })...
}
```

2.从水果组件中传递水果的 **id** 。

```
class **Fruit** extends **React.Component**{

  render(){
    return(
      <div>
        <h1>{this.props.fruit.name}</h1>
        <p>{this.props.fruit.description}</p>
        <button **onClick={() => this.props.handleDelete(this.props.fruit.id)}**>Delete</button>
      </div>
    )      
  }
}
```

3.创建一个处理从状态中删除项目的函数，这样它会立即从页面中删除，而无需刷新。

```
class Body extends React.Component {constructor(props) {
    ...
    this.handleDelete = this.handleDelete.bind(this)
    **this.deleteFruit = this.deleteFruit.bind(this)**
  }...handleDelete(id){
    fetch(`[http://localhost:3000/api/v1/items/${id}`](http://localhost:3000/api/v1/items/${id}`), 
    {
      method: 'DELETE',
      headers: {
        'Content-Type': 'application/json'
      }
    }).then((response) => { 
        **this.deleteFruit(id)**
      })
  }**deleteFruit(id){
    newFruits = this.state.fruits.filter((fruit) => fruit.id !== id)
    this.setState({
      fruits: newFruits
    })
  }**componentDidMount(){
  ...
  }render(){
   return(
      <div>
        <NewFruit handleFormSubmit={this.handleFormSubmit}/>
        <AllFruits fruits={this.state.fruits} **handleDelete={this.handleDelete}**/>
      </div>
    )
  }
}
```

此外，除了 React， [ruby on rails 教程](https://blog.coursesity.com/best-ruby-on-rails-tutorials/)可以帮助你更自由地探索应用开发过程。就是这样！前往浏览器，删除所有你讨厌的水果！

**编辑一个水果**

为了完成 CRUD 功能，我们只剩下编辑/更新水果的能力了。让我们这样做吧。

让我们修改我们的水果组件，创建一个带有' **editable'** 属性的状态，并添加一个编辑按钮。

```
class **Fruit** extends **React.Component**{**constructor(props){
    super(props);
    this.state = {
      editable: false
    }
  }**

  render(){
    return(
      <div>
        <h1>{this.props.fruit.name}</h1>
        <p>{this.props.fruit.description}</p>
       ** <button>{this.state.editable? 'Submit' : 'Edit'}</button>**
        <button onClick={() => this.props.handleDelete(this.props.fruit.id)}>Delete</button>
      </div>
    )      
  }
}
```

接下来，我们将修改渲染水果的方式。根据 state. **editable** 值，它将呈现名称和描述或输入字段来编辑该水果。

```
class Fruit extends React.Component{constructor(props){
    super(props);
    this.state = {
      **editable: false**
    }
  }

  render(){
   ** let name = this.state.editable ? <input type='text' ref={input => this.name = input} defaultValue={this.props.fruit.name}/>:<h3>{this.props.fruit.name}</h3>
    let description = this.state.editable ? <input type='text' ref={input => this.description = input} defaultValue={this.props.fruit.description}/>:<p>{this.props.fruit.description}</p>**
    return(
      <div>
       ** {name}**
       ** {description}**
        <button>{this.state.editable? 'Submit' : 'Edit'}</button>
        <button onClick={() => this.props.handleDelete(this.props.fruit.id)}>Delete</button>
      </div>
    )      
  }
}
```

接下来，让我们向编辑按钮添加一个 onClick()事件处理程序，并创建处理编辑的实际函数:

```
class Fruit extends React.Component{constructor(props){
    super(props);
    this.state = {
      editable: false
    }
    **this.handleEdit = this.handleEdit.bind(this)**
  } **handleEdit(){
    this.setState({
      editable: !this.state.editable
    })
  }**

  render(){
    let name = this.state.editable ? <input type='text' ref={input => this.name = input} defaultValue={this.props.fruit.name}/>:<h3>{this.props.fruit.name}</h3>
    let description = this.state.editable ? <input type='text' ref={input => this.description = input} defaultValue={this.props.fruit.description}/>:<p>{this.props.fruit.description}</p>
    return(
      <div>
        {name}
        {description}
        <button **onClick={() => this.handleEdit()}**>{this.state.editable? 'Submit' : 'Edit'}</button>
        <button onClick={() => this.props.handleDelete(this.props.fruit.id)}>Delete</button>
      </div>
    )      
  }
}
```

现在，这个按钮所做的只是在点击时改变我们的**可编辑**属性。

就像删除操作一样，实际的更新操作以及对后端的 PUT 请求将发生在更高级别的组件主体中。因此，让我们创建发送 PUT 请求并相应更新水果状态的函数。

```
class Body extends React.Component { constructor(props) {
    ...
    **this.handleUpdate = this.handleUpdate.bind(this);
    this.updateFruit = this.updateFruit.bind(this)**
  } **handleUpdate(fruit){
    fetch(`**[**http://localhost:3000/api/v1/fruits/${fruit.id}`**](http://localhost:3000/api/v1/fruit/${fruit.id}`)**, 
    {
      method: 'PUT',
      body: JSON.stringify({fruit: fruit}),
      headers: {
        'Content-Type': 'application/json'
      }
    }).then((response) => { 
        this.updateFruit(fruit)
      })
  }** **updateFruit(fruit){
    let newFruits = this.state.fruits.filter((f) => f.id !== fruit.id)
    newFruits.push(fruit)
    this.setState({
      fruits: newFruits
    })
  }** componentDidMount(){
    fetch('/api/v1/items.json')
      .then((response) => {return response.json()})
      .then((data) => {this.setState({ items: data }) });
  }render(){
    return(
      <div>
        <NewItem handleSubmit = {this.handleSubmit} />
        <AllItems items={this.state.items} handleDelete = {this.handleDelete} **handleUpdate = {this.handleUpdate}**/>
      </div>
    )
  }
}
```

我们再次定义了两个函数: **handleUpdate()** 将水果作为参数并向后端发送 PUT 请求，以及 **updateFruit()** 在浏览器中用更新的信息无缝更新水果。 **handleUpdate()** 作为道具传递给 AllItems，AllItems 将其作为道具传递给 Fruit:

```
const AllFruits = (props) => {var fruits = props.fruits.map((fruit) => {
    return(
      <div key={fruit.id}>
       <Fruit fruit={fruit} handleDelete={props.handleDelete} **handleUpdate={props.handleUpdate}**/>
      </div>
    )
  })...
}
```

快到了！！！剩下的工作就是创建一个合适的水果对象，并将其从水果组件传递给 handleUpdate 函数，该函数在水果的 handleEdit 函数中调用。

```
class Fruit extends React.Component{constructor(props){
    ...
    **this.handleEdit = this.handleEdit.bind(this)**
  }handleEdit(){
   **if(this.state.editable){
      let name = this.name.value
      let description = this.description.value
      let id = this.props.fruit.id
      let fruit = {id: id, name: name, description: description}
      this.props.handleUpdate(fruit)
    }**
    this.setState({
      editable: !this.state.editable
    })
  }

  render(){
    let name = this.state.editable ? <input type='text' ref={input => this.name = input} defaultValue={this.props.fruit.name}/>:<h3>{this.props.fruit.name}</h3>
    let description = this.state.editable ? <input type='text' ref={input => this.description = input} defaultValue={this.props.fruit.description}/>:<p>{this.props.fruit.description}</p>
    return(
      <div>
        {name}
        {description}
        <button onClick={() => this.handleEdit()}>{this.state.editable? 'Submit' : 'Edit'}</button>
        <button onClick={() => this.props.handleDelete(this.props.fruit.id)}>Delete</button>
      </div>
    )      
  }
}
```

就是这样！现在你可以用你的水果完成所有的脏动作了。这篇博文中有很多代码，我们在每一步都做了大量的修改，所以如果你想看完整的应用程序代码，请点击这里:

[https://github . com/nothing is funny/fruits-crud-react-rails-app](https://github.com/nothingisfunny/fruits-crud-react-rails-app)

感谢你留下来，如果你对如何改进这个应用程序有任何建议，或者如果你发现任何错误/错别字，请留下评论！

这篇博客的灵感来自:[https://www . plur alsight . com/guides/ruby-ruby-on-rails/building-a-crud-interface-with-react-and-ruby-on-rails](https://www.pluralsight.com/guides/ruby-ruby-on-rails/building-a-crud-interface-with-react-and-ruby-on-rails)