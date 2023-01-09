- `npx create-react-app name`
## Emmett
- `sfc`: stateless function component
- 



- `<button onClick=(() => handleClick(arg1, arg2))>`
- for variables that update in view:
	- `const [var, setVar] = useState(initialVarValue)`
		- var can be anything
		- Must `import { useState } from 'react';`
- List variables:
	- use list.map((item) => {
		JSX
	})
	- Include key={} and inside key must be combo of variables that are unique for all list items
- Props:
	- Pass data to child component
	- just add `name=data` to tag
	- reference: add `{name, name2}` in input for component function
	- tip: use `.filter((item) => item.author == "test")` on prop to filter
	- Pass functions if needing to work on data inside components
- `useEffect(() => {}, [])` runs code on every render
	- Avoid using `set` functions inside useeffect
	- pass in array in second argument of variables that when changed should trigger that useeffect
	- Good way to fetch data on page load
	- If using useEffect: use {data && JSX} to display only if data is not null
	- Return a function: runs the function returned when component is unloaded 
- Hooks:
	- Start with `use` like `useFetch`
	- function, return with object of reference items,
- Routing:
	- `import { BrowserRouter as Router, Route, Switch } from 'react-router-dom';`
	- In app.js, surround in router and setup routes:
```jsx
  <Router>
      <div className="App">
      <Navbar />
      <div className="content">
        <Switch>
          <Route exact path="/">
            <Home></Home>
          </Route>
          <Route path="/create">
            <Create></Create>
          </Route>
        </Switch>
      </div>
    </div>
  </Router>
```
-  Add link to keep local routing:
	- replace a with Link tag, like `<Link to="/">Home</Link>`