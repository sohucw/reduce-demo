Redux详解

Redux虽然经常和Flux相提并论，但是其实并不能看做等价，各种Flux实现都是为了把数据和组件进行分离，从而让组件可以像后端程序请求数据库一样去管理数据，并通过数据绑定库来更新视图。
Redux不仅实现了数据和组件的分离，还可以把应用中所有的标记状态纳入到统一的管理体系中，将前端编程做了范式上的统一：前端的交互被抽象成了应用状态的不断变化，视图只是当前状态的展现形式。因此我认为不管项目中是否会用到，都是一种非常值得学习的思想。
MapReduce概念

Redux实际是借鉴了MapReduce的理念，从名字上就可以看出端倪。
MapReduce 最早是分布式计算中的一个概念，大名鼎鼎的hadoop就是应用MapReduce原理进行构建的。
一个大致贴切的例子就是全国的空气质量统计：
河南： PM2.5: 120, PM10: 100, 权重1
河北： PM2.5: 150, PM10: 90, 权重0.9  
......
把全国33省的数据通过算法可以得到最终的总指数： 65（假设）
那么我在每个省 都部署一台机器和监控设备，计算本省的数据，然后上报一个标准的中间处理结果，这个就是Map（映射）的过程。
在北京的某台机器上对各省上报的中间结果进行实时汇总，这样就能得到实时的全国总指数，这就是reduce（约减）的过程。
页面状态：

页面永远只有一个current state，也就是上面例子中最终的总指数，例如：
    页面框架 + 数据1
    页面框架 + 数据2
    页面框架 + 弹层 + 数据2
    ......
但是一个页面是由多个组件构成的，每个组件的操作都会改变这个current state。
可以认为，每个组件上绑定的动作都是一个Map，这个动作会产生一个中间结果例如：
    actionResult = { type: 'actionName', id: 1, name: 'yourName' }
这个中间结果作为参数传递给reducer处理的过程就是MapReduce中reduce的概念, reducer处理过后，得到最终应用的current state，即最终的计算结果,再由具体view层实现将这个state映射为最终的视图。
    reducer(actionResult) => current state => current view
使用redux构建一个todo应用

1、根据需求分析页面状态

举个例子：
状态：

默认列表状态
数据loading状态
模态框打开状态
数据加载失败状态
查看/编辑状态
当前激活元素/currentId
动作：

添加(text)
删除(itemId)
标记完成(itemId)
编辑(itemId, text)
清除所有
2、编写action

    function addTodo(text) {
      return { type: 'ADD_TODO', text: text }
    }

    function delTodo(id) {
      return { type: 'DEL_TODO', id: id }
    }

    function editTodo(id) {
      return { type: 'EDIT_TODO', id: id }
    }
    ......
3、创建一个reducer来处理状态

    var initialState = [
      {
        data: []
        id: 0
      }
    ] //初始state

    function todoReducer (state, action) {
      if (state === undefined) {      
        state = initialState;
      }
      switch (action.type) {
        case 'ADD_TODO':
          state.data.push({id: state.id + 1, text: action.text})
          state.id = state.id + 1;
          return state
        case 'DEL_TODO':
          ...
          return state
        default:
          return state
      }
    } //根据action，处理并返回新的状态
4、创建一个store来保存current state

    var store = Redux.createStore(todoReducer);
5、使用dispatch来触发action

    store.dispatch(addTodo('redux-jQuery-demo'));
6、view层根据state进行改变

我们可以写一个渲染函数，把state传进去就行了，渲染层我们可以采用任意框架和类库
    store.dispatch(actions.addTodo('redux-jQuery-demo'));
    render(store.getState());
Demo示例

目前Redux的demo项目几乎都是ES6 + Babel进行构建的，而且都是以React作为例子，如果没有接触过React和ES6的同学看起来会比较吃力。但其实redux是可以用到任何一款开发框架下的，使用Angular、Riot、甚至无框架都可以用到Redux。
于是我做了一个绝大多数人都能看懂的示例, 没有react，没有ES6, 不需要安装和编译：