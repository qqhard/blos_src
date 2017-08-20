title: 从jquery到react-redux
date: 2016-12-04 00:26:15
tags:
- flux
- mvc
- react
- redux  
- 前端
categories:
- 前端
---


通过velocity，结合hulu工程师关于react-redux的讲解，谈谈自己对react-redux的理解。

<!-- more -->

# Jquery与React

在我第一次接触前端的时候，入门教程讲的就是JQuery。前端的一大模式就是选择一个dom节点，设置某些属性，或挂了一个响应函数，响应函数里呢，也是这种模式，JQuery发现了这种模式，在节点选择和属性设置上提供了十分简洁的接口。

当Ajax得到极大发展后，前端愈发的复杂，这种选中和修改蔓生的代码，变得难以维护。在JQuery的模式下，状态是一种逻辑抽象，每种状态都有对应的视图，为了保证状态(前端逻辑)的正确性，我们要合理的修改视图(选中dom，修改dom)，当状态复杂之后，从视图的控制来保证状态正确，这绝对不是一件容易的事情。那更为合理的情况是不是应该是直接控制状态，来保证逻辑的正确，而状态到视图的转化，由某种方式保证其正确性？
React就是这种东西，它的理念就是UI = f(state)。它让我们显示地设计和控制state，而state到UI的转化由React代劳。我认为这是React比JQuery理念先进的地方。

另外，由于框架接管了state到UI的转化，为了使得这种转化变得更为高效，React在内存中建立了虚拟DOM，当state变化时先通过虚拟DOM计算，当视图真正变化时，再修改DOM。由于js内存计算的速度快过了dom修改，虚拟DOM使得页面获得了更好的性能。当然，还能说出一系列的优势，但我觉得那些不是主要的，例如组件化，是来自node时代发展过程中，思想和工具良好的加持。

# MVC与Flux

有了React之后，就能轻松构建复杂前端应用了吗？state越来越复杂，各组件的state相互牵连缠绕，例如，React 组件的父子通信可以通过props，但子父组件通信和兄弟组件通信都需要传递回调函数。似乎它也只是缓解了Jquery的窘境，只是支持复杂度量级的不同，而不具有根本上的扩展性。

为了可扩展性，我们需要引入某种模式。从PC时代到WEB时代，MVC绝对是客户端设计的不二模式。如果严格地践行MVC的约定，无论是JQuery还是React，我想都能解决扩展性问题。

在server render时代，方案是一个MVC框架加上一个模板渲染引擎，我们约定模型和视图通过控制器严格分离，用户视图的事件转化为http请求，路由到某个控制器函数，然后执行逻辑，更新模型和视图。在这之前，jsp+servlet+javabean的解决方案，难道不是MVC吗？问题是JSP有直接修改javabean的能力，所以同样都是MVC，前者更多依靠了框架约束，后者更多依靠了模式约定。前端越来越重，它所遇到的问题很类似。没有MVC框架时，我们依旧可以MVC，依靠的是程序员的自觉。但当时间流逝，人员更替，工期吃紧，这种约定总会被突破。

于是，Facebook在推出React的同时，提出了Flux模式，并且抨击了MVC，它说MVC会导致双向数据流，不利于理解和调试程序，实际上它所画的MVC图是错的，有人评论说，他们重新发明了真正的MVC，然后决定给它一个新名字。我觉得这是有意义的，它让MVC的概念清晰透彻，职责明确。

Flux由Action，Dispatcher，Store，View组成，其中只有一个Dispatcher，是所有Action的入口，其上挂满了Store注册的回调，当action到来后，执行相应回调更新Store中的状态，这个状态通过React来更新视图。他的优点是，Dispatcher把守了Store更新的权限，也就是单向数据流，另外保证了众多的Action->Store的顺序，使得程序易于理解和调试。这里注意，Dispatcher不是MVC的Controller，它只是通过约定的Action类型来判断执行哪个Store的回调函数，Controller实际上变成了Flux的回调，Model变成了Store中的数据。

Store保存全局状态，使得组件通信可以通过action(相当于发消息)，然后另一个从Store取状态(收消息)，避免了传递回调函数。它像一个消息总线，使得通信变得清晰和方便。

Flux的实现通过更严格的限制，使得代码的扩展性更好。

# Flux与Redux

Flux除了看起来有些复杂，已经很完美了。但是前端对工作效率和性能有了新的要求。

hot loader，即代码发生变化，不用重新启动，直接在浏览器上生效。Flux中Stroe既有逻辑又有数据，很难做到在更新逻辑的同时保留数据。

server render，由于前端变重，js文件变大，会有首屏加载慢的问题，另外是单纯浏览器端渲染不利于SEO，server render就变得很重要了。由于React提供了生成Html文档的接口，server端可以完成数据获取和渲染，得到html文档，但是为了使得和浏览器端同构，需要将React中的state‘缩水’后和文档一起发送到浏览器，再恢复成state，浏览器check一下，保证浏览器端和服务端渲染的一致。使用Flux之后，state保存在Flux的Store中，Store还混有逻辑，很难做到server render。

Redux作为Flux模式的一种实现，能有效的解决以上两个问题。Redux的思想是增加一个reducer，使得newState = reducer(action，oldState)，它是无状态的(把状态当成参数传进去)，以此来分离逻辑和状态。另外，Redux限制了，只有一个Store，确切说是只有state对象，那么其实Dispatcher看起来的意义不大了，直接将它变成一个dispatch方法给Store。

由于reducer是无状态的函数，更新逻辑state保持不变即可，hot loader问题迎刃而解。state从逻辑中分离，服务端又可以愉快地进行渲染了。

Redux和React是目前来看，比较般配的，但React是否需要Redux也需要看具体的业务需求。
