使用react构建单页面应用时需要根据不同的url跳转到对应的渲染模板，这时就需要用到React提供的路由功能。

**React依赖：**
1）react-router
2）react-router-dom

`react-router`: 实现了路由的核心功能，而react-router-dom依赖react-router，
`react-router-dom`: 基于`react-router`，加入了在浏览器运行环境下的一些功能：
          `Link`组件，会渲染一个`a`标签；
          `BrowserRouter`组件，使用`pushState`和`popState`事件构建路由；
          `HashRouter`组件，使用`window.location.hash`和`hashchange`事件构建路由。

`react-router-native`: 基于`react-router`，类似`react-router-dom`，加入了`react-native`运行环境下的一些功能。



BrowserRouter
<BrowserRouter> 使用 HTML5 提供的 history API (pushState, replaceState 和 popstate 事件) 来保持 UI 和 URL 的同步。

<BrowserRouter
  basename={string}
  forceRefresh={bool}
  getUserConfirmation={func}
  keyLength={number}
>
>basename: string
>所有位置的基准 URL。如果你的应用程序部署在服务器的子目录，则需要将其设置为子目录。basename 的正确格式是前面有一个前导斜杠，但不能有尾部斜杠。
>例子：

<BrowserRouter basename="/calendar">
  <Link to="/today" />
</BrowserRouter>
效果：

<a href="/calendar/today" />
forceRefresh: bool
如果为 true ，在导航的过程中整个页面将会刷新。一般情况下，只有在不支持 HTML5 history API 的浏览器中使用此功能。
例子：

const supportsHistory = 'pushState' in window.history;
<BrowserRouter forceRefresh={!supportsHistory} />
getUserConfirmation: func
用于确认导航的函数，默认使用 window.confirm。例如，当从 /a 导航至 /b 时，会使用默认的 confirm 函数弹出一个提示，用户点击确定后才进行导航，否则不做任何处理。译注：需要配合 <Prompt> 一起使用。

// 这是默认的确认函数
const getConfirmation = (message, callback) => {
  const allowTransition = window.confirm(message);
  callback(allowTransition);
}

<BrowserRouter getUserConfirmation={getConfirmation} />
KeyLength：数字，设置Location.Key的长度；

<HashRouter>
使用 URL 的 hash 部分（即 window.location.hash）来保持 UI 和 URL 的同步。

<HashRouter
  basename={string}
  getUserConfirmation={func}
  hashType={string}  
>
><App />
></HashRouter>
>basename,getUserConfirmation和BrowserRouter功能一样

hashType: string
window.location.hash 使用的 hash 类型，有如下几种：
slash - 后面跟一个斜杠，例如 #/ 和 #/sunshine/lollipops
noslash - 后面没有斜杠，例如 # 和 #sunshine/lollipops
hashbang - Google 风格的 ajax crawlable，例如 #!/ 和 #!/sunshine/lollipops
默认为 slash。

<Link>
为你的应用提供声明式的、可访问的导航链接。
注：必须要有to属性，否则页面报错

this.props.history.push({ pathname:"/inbox", query:{ name:"inbox", myas:"哈哈" } }) 
<Link to="/about" >About</Link>
to: string
一个字符串形式的链接地址，通过 pathname、search 和 hash 属性创建。
<Link to='/courses?sort=name' />

to: object
一个对象形式的链接地址，可以具有以下任何属性：
pathname - 要链接到的路径
search - 查询参数
hash - URL 中的 hash，例如 #the-hash
state - 存储到 location 中的额外状态数据

<Link to={{
  pathname: '/courses',
  search: '?sort=name',
  hash: '#the-hash',
  query: {
id: '',
}
  state: {
    fromDashboard: true
  }
}} />
replace: bool
当设置为 true 时，点击链接后将替换历史堆栈中的当前条目，而不是添加新条目。默认为 false。

<Link to="/courses" replace />
<NavLink>
一个特殊版本的 <Link>，它会在与当前 URL 匹配时为其呈现元素添加样式属性。

<NavLink to="/about">About</NavLink>
activeClassName: string
当元素处于激活状态时应用的类，默认为 active。它将与 className 属性一起使用。

<NavLink to="/faq" activeClassName="selected">FAQs</NavLink>
activeStyle: object
当元素处于激活状态时应用的样式。

const activeStyle = {
  fontWeight: 'bold',
  color: 'red'
};

<NavLink to="/faq" activeStyle={activeStyle}>FAQs</NavLink>
<Prompt>
用于在位置跳转之前给予用户一些确认信息。当你的应用程序进入一个应该阻止用户导航的状态时（比如表单只填写了一半），弹出一个提示。

import { Prompt } from 'react-router-dom';

<Prompt
  when={formIsHalfFilledOut}
  message="你确定要离开当前页面吗？"
/>
message: string
当用户试图离开某个位置时弹出的提示信息。

<Prompt message="你确定要离开当前页面吗？" />
message: func
将在用户试图导航到下一个位置时调用。需要返回一个字符串以向用户显示提示，或者返回 true 以允许直接跳转。

<Prompt message={location => {
  const isApp = location.pathname.startsWith('/app');

  return isApp ? `你确定要跳转到${location.pathname}吗？` : true;
}} />
when: bool
在应用程序中，你可以始终渲染 <Prompt> 组件，并通过设置 when={true} 或 when={false} 以阻止或允许相应的导航，而不是根据某些条件来决定是否渲染 <Prompt> 组件。
译注：when 只有两种情况，当它的值为 true 时，会弹出提示信息。如果为 false 则不会弹出。

<Prompt when={true} message="你确定要离开当前页面吗？" />
<Redirect>
使用 <Redirect> 会导航到一个新的位置。新的位置将覆盖历史堆栈中的当前条目，例如服务器端重定向（HTTP 3xx）。

import { Route, Redirect } from 'react-router-dom';

<Route exact path="/" render={() => (
  loggedIn ? (
    <Redirect to="/dashboard" />
  ) : (
    <PublicHomePage />
  )
)} />
to: string
要重定向到的 URL。

<Redirect to="/somewhere/else" />
to: object
要重定向到的位置，其中 pathname 可以是能够理解的任何有效的 URL 路径。

<Redirect to={{
  pathname: '/login',
  search: '?utm=your+face',
  state: {
    referrer: currentLocation
  }
}} />
例中的 state 对象可以在重定向到的组件中通过 this.props.location.state 进行访问。

push: bool
如果为 true，重定向会将新的位置推入历史记录，而不是替换当前条目。

<Redirect push to="/somewhere/else" />
<Route>
exact：bool
如果为 true，则只有在 path 完全匹配 location.pathname 时才匹配。

<Route exact path="/one" component={OneComponent} />
path: string

<Route path="/users/:id" component={User} />
没有定义 path 的 <Route> 总是会被匹配。

render: func
使用 render 可以方便地进行内联渲染和包装，而无需进行上文解释的不必要的组件重装。
// 方便的内联渲染

<Route path="/home" render={() => <div>Home</div>} />
component
指定只有当位置匹配时才会渲染的 React 组件，该组件会接收 route props 作为属性。

const User = ({ match }) => {
  return <h1>Hello {match.params.username}!</h1>
}
<Route path="/user/:username" component={User} />
children: func
有时候不论 path 是否匹配位置，你都想渲染一些内容。在这种情况下，你可以使用 children 属性。除了不论是否匹配它都会被调用以外，它的工作原理与 render 完全一样。
children 渲染方式接收所有与 component 和 render 方式相同的 route props，除非路由与 URL 不匹配，不匹配时 match 为 null。这允许你可以根据路由是否匹配动态地调整用户界面。如下所示，如果路由匹配，我们将添加一个激活类：

const ListItemLink = ({ to, ...rest }) => (
  <Route path={to} children={({ match }) => (
    <li className={match ? 'active' : ''}>
      <Link to={to} {...rest} />
    </li>
  )} />
)

<ul>
  <ListItemLink to="/somewhere" />
  <ListItemLink to="/somewhere-else" />
</ul>
这对动画也很有用：

<Route children={({ match, ...rest }) => (
  {/* Animate 将始终渲染，因此你可以利用生命周期来为其子元素添加进出动画 */}
  <Animate>
    {match && <Something {...rest} />}
  </Animate>
)} />
警告：<Route component> 和 <Route render> 优先于 <Route children>，因此不要在同一个 <Route> 中同时使用多个。
组件中
1) path
每个 Route 都需要定义一个 path 属性，当使用 BrowserRouter 时，path 用来描述这个Router匹配的 URL 的pathname;当使用 HashRouter时，path 用来描述这个 Route 匹配的 URL 的 hash。例如，使用 BrowserRouter 时，<Route path=''foo' /> 会匹配一个 pathname 以 foo 开始的 URL （如: http://example.com/foo）。当 URL 匹配一个 Route 时，这个 Route 中定义的组件就会被渲染出来。

2) match
当 URL 和 Route匹配时，Route 会创建一个 match 对象作为 props 中的一个 属性传递给被渲染的组件。这个对象包含以下4个属性。
（1）params: Route的 path 可以包含参数，例如 <Route path="/foo/:id" 包含一个参数 id。params就是用于从匹配的 URL 中解析出 path 中的参数，例如，当 URL = 'http://example.ocm/foo/1' 时，params= {id: 1}。
（2）isExact: 是一个布尔值，当 URL 完全匹时，值为 true; 当 URL 部分匹配时，值为 false.例如，当 path='/foo'、URL="http://example.com/foo" 时，是完全匹配; 当 URL="http://example.com/foo/1" 时，是部分匹配。
（3）path: Route 的 path 属性，构建嵌套路由时会使用到。
（4）url: URL 的匹配的方式

3) history对象
history对象通常具有以下属性和方法：
length : （number）历史堆栈中的条目数
action : （string）当前动作（PUSH，REPLACE或POP）
location : (object) 当前位置。
具有以下属性：
pathname: URL的路径
search: URL查询字符串
hash: URL哈希片段
state: 位置特定的状态被提供给例如。当这个位置被推到堆栈上时，push（路径，状态）。仅在浏览器和内存历史记录中可用。
push : (path, [state]) - (function) 将新条目推入历史堆栈
replace : (path, [state]) - (function) 替换历史堆栈上的当前条目
go(n) : (function) 将历史堆栈中的指针移动n个条目
goBack() : (function) 相当于 go(-1)
goForward() : (function) 相当于 go(1)
block : (function) 防止导航

<Switch>
用于渲染与路径匹配的第一个子 <Route> 或 <Redirect>。
这与仅仅使用一系列 <Route> 有何不同？
<Switch> 只会渲染一个路由。相反，仅仅定义一系列 <Route> 时，每一个与路径匹配的 <Route> 都将包含在渲染范围内。考虑如下代码：

<Route path="/about" component={About} />
<Route path="/:user" component={User} />
<Route component={NoMatch} />
如果 URL 是 /about，那么 <About>、<User> 和 <NoMatch> 将全部渲染，因为它们都与路径匹配。这是通过设计，允许我们以很多方式将 <Route> 组合成我们的应用程序，例如侧边栏和面包屑、引导标签等。
但是，有时候我们只想选择一个 <Route> 来呈现。比如我们在 URL 为 /about 时不想匹配 /:user（或者显示我们的 404 页面），这该怎么实现呢？以下就是如何使用 <Switch> 做到这一点：

import { Switch, Route } from 'react-router';

<Switch>
  <Route exact path="/" component={Home} />
  <Route path="/about" component={About} />
  <Route path="/:user" component={User} />
  <Route component={NoMatch} />
</Switch>
现在，当我们在 /about 路径时，<Switch> 将开始寻找匹配的 <Route>。我们知道，<Route path="/about" /> 将会被正确匹配，这时 <Switch> 会停止查找匹配项并立即呈现 <About>。同样，如果我们在 /michael 路径时，那么 <User> 会呈现。
这对于动画转换也很有用，因为匹配的 <Route> 与前一个渲染位置相同。

<Fade>
  <Switch>
    {/* 这里只会渲染一个子元素 */}
    <Route />
    <Route />
  </Switch>
</Fade>

<Fade>
  <Route />
  <Route />
  {/* 这里总是会渲染两个子元素，也有可能是空渲染，这使得转换更加麻烦 */}
</Fade>
location: object
用于匹配子元素而不是当前历史位置（通常是当前的浏览器 URL）的 location 对象。

children: node
所有 <Switch> 的子元素都应该是 <Route> 或 <Redirect>。只有第一个匹配当前路径的子元素将被呈现。
<Route> 组件使用 path 属性进行匹配，而 <Redirect> 组件使用它们的 from 属性进行匹配。没有 path 属性的 <Route> 或者没有 from 属性的 <Redirect> 将始终与当前路径匹配。
当在 <Switch> 中包含 <Redirect> 时，你可以使用任何 <Route> 拥有的路径匹配属性：path、exact 和 strict。from 只是 path 的别名。
如果给 <Switch> 提供一个 location 属性，它将覆盖匹配的子元素上的 location 属性。

<Switch>
  <Route exact path="/" component={Home} />
  <Route path="/users" component={Users} />
  <Redirect from="/accounts" to="/users" />
  <Route component={NoMatch} />
</Switch>
withRouter
您可以通过withRouter高阶组件访问历史对象的属性和最接近的<Route>的匹配。随着路由每次路由改变时，路由器会重新渲染其组件，路径与<路径>渲染道具：{match，location，history}相同。

源链接：https://segmentfault.com/a/1190000019178411