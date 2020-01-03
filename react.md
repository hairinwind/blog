
## inteview questions
https://www.youtube.com/watch?v=uRkS5Dqf8ic&t=717s

## virtual DOM
//TODO
Updating DOM is expensive. 
with virtual DOM, it can update the DOM as less as possible

## react lifecycle
- Mounting
- constructor()
- static getDerivedStateFromProps()
- render()
- componentDidMount()
Udpating
- static getDerivedStateFromProps()
- shouldComponentUpdate()
- render()
- getSnapshotBeforeUpdate()
- componentDidUpdate()
Unmounting
- componentWillUnmount()
https://reactjs.org/docs/react-component.html

## why use arrow function
Arrow function does not have its own "this", while a regular function does. If you want a regualr function to take "this" from its lexical scope, you have to bind this from its parent. 
In the loginHandle below, it is class property. Render is the class function. 
The arrow function does not 
```
class App extends Component {
	loginHandle = ()=> {
		this.setState(...)
	}
	render() {
		return '<div>...</div>'
	}
}
```

## React.PureComponent
only work for class component

## class component , functional component or presentation component
React.memo

## error boundaries
https://reactjs.org/docs/error-boundaries.html
//TODO

## react pattern
- context-api pattern
- render props
- presentation component pattern
https://www.youtube.com/watch?v=4OzC5InCkLY
https://reactpatterns.com/
//TODO

## css-in-js pattern
css is javascript object in React.
in-line css
//TODO

## React.Fragment
```
render() {
	return (
		<React.Fragment>
			<ChildA />
			<ChildB />
			...
		</React.Fragment>
		);
}
```

## suspense to do the lazy loading
split the code to different modules, so that some of them can be lazy loaded.
//TODO

## redux
Centralizing your application's state
//TODO

## redux middleware
//TODO

## redux-saga vs redux-thunk

## react performance tuning
- find out unnecessary rerendering, put the check logic in shouldComponentUpdate()
- if package size is too big, use lazy loading
- 

## tutorial 
https://www.taniarascia.com/getting-started-with-react/  
https://www.valentinog.com/blog/redux/  
https://www.valentinog.com/blog/hooks/  
https://react-redux.js.org/api/hooks  
https://www.valentinog.com/blog/babel/ 
https://github.com/reduxjs/redux-thunk  
https://redux-saga.js.org/  





