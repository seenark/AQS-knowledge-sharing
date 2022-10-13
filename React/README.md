# React Basic

## Create Project
- use vite to create react + typescript project
- add eslint 
 ```bash
npm init @eslint/config
```
- add package  `eslint-plugin-react-hooks`
```bash
npm install eslint-plugin-react-hooks --save-dev
```

edit `.eslintrc.cjs`
```js
{
  "extends": [
    // ...
    "plugin:react-hooks/recommended"
  ]
}
```

may be need `vite-plugin-eslint`
`vite.config.ts`
```ts
import { defineConfig } from 'vite';
import reactRefresh from '@vitejs/plugin-react-refresh';
import eslint from 'vite-plugin-eslint';

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [eslint(), reactRefresh()],
});
```

---
## Rendering an Element in the DOM

`index.html`
```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <link rel="icon" type="image/svg+xml" href="/vite.svg" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Vite + React + TS</title>
  </head>
  <body>
    <div id="root"></div>
    <script type="module" src="/src/main.tsx"></script>
  </body>
</html>

```

`main.tsx`
```tsx
import React from 'react'
import ReactDOM from 'react-dom/client'
import App from './App'
import './index.css'

ReactDOM.createRoot(document.getElementById('root') as HTMLElement).render(
  <React.StrictMode>
    <App />
  </React.StrictMode>
)
```

![[Pasted image 20221011174324.png]]

---
# Component
**Recommended to use Functional component**
1. should be a pure function
2. name of the Component should be Capitalize
3. must return only one object

`App.tsx`
```tsx
import './App.css'

function App() {

  return (
    <h1>App Component</h1>
  )
}

export default App

```


`App.tsx`
```tsx
import './App.css'

function MyComponent() {
  return <h3>My Component</h3>
}


function App() {

  return (
    <>
      <h1>App Component</h1>
      <MyComponent />
    </>
  )
}

export default App

```

---
## React Fragment
`App.tsx`
```tsx
function App() {

  return (
    <>
      <h1>App Component</h1>
      <MyComponent />
    </>
  )
}
```

---
## Props (read only)
prop can be 
- string number 
- object
- function

`App.tsx`
```tsx
import './App.css'

interface MyComponentProps {
  name: string
}

function MyComponent(props: MyComponentProps) {
  return <h3>My name is {props.name}</h3>
}

function App() {
  return (
    <>
      <h1>App Component</h1>
      <MyComponent name={"John"} />
    </>
  )
}

export default App
```

---
## Component2
`App.tsx`
```tsx
import { FunctionComponent } from 'react';
import './App.css'

interface MyComponentProps {
  name: string
}

const MyComponent: FunctionComponent<MyComponentProps> = (props) => {
  return <h2>My name is {props.name}</h2>
}

function App() {
  return (
    <>
      <h1>App Component</h1>
      <MyComponent name={"John"} />
    </>
  )
}

export default App
```

---
## Nested Component
should use **_PropsWithChildren\<Props interface\>_** 

`App.tsx`
```tsx
import { FC, PropsWithChildren } from "react";
import "./App.css";

interface MyComponent2Props {
	num: number;
}

const MyComponent2: FC<MyComponent2Props> = (props) => {
	return <div>Component number {props.num}</div>;
};

interface MyComponentProps {
	name: string;
}
const MyComponent: FC<PropsWithChildren<MyComponentProps>> = (props) => {
	return (
		<>
			<h2 style={{ color: "red", cursor: "pointer" }}>
				My name is {props.name}
			</h2>
			<div>Below is children</div>
			{props.children}
		</>
	);
};

function App() {
	return (
		<>
			<h1>App Component</h1>
			<MyComponent name={"John"}>
				<MyComponent2 num={2} />
			</MyComponent>
		</>
	);
}

export default App;
```

---
## Bring component to another file

`src/components/MyComponent.tsx`
```tsx
import { FC, PropsWithChildren } from "react";

interface MyComponentProps {
	name: string;
}
const MyComponent: FC<PropsWithChildren<MyComponentProps>> = (props) => {
	return (
		<>
			<h2 style={{ color: "red", cursor: "pointer" }}>
				My name is {props.name}
			</h2>
			{props.children}
		</>
	);
};

export default MyComponent;

```

`src/components/MyComponent2.tsx`
```tsx
import { FC } from "react";

interface MyComponent2Props {
	num: number;
}

const MyComponent2: FC<MyComponent2Props> = (props) => {
	return <div>Component number {props.num}</div>;
};

export default MyComponent2;

```

## Import Component
`App.tsx`
```tsx
import "./App.css";
import MyComponent from "./components/MyComponent";
import MyComponent2 from "./components/MyComponent2";

function App() {
	return (
		<>
			<h1>App Component</h1>
			<MyComponent name={"John"}>
				<div>Below is children</div>
				<MyComponent2 num={2} />
			</MyComponent>
		</>
	);
}

export default App;

```

---
# Style
## Inline style
`App.tsx`
```tsx
const MyComponent: FC<MyComponentProps> = (props) => {
  return (
	  <h2 style={{ color: "red", cursor: "pointer" }}>My name is {props.name}</h2>
	)
}
```

`App.tsx`
```tsx
import { FC, PropsWithChildren } from "react";

interface MyComponentProps {
	name: string;
}
const MyComponent: FC<PropsWithChildren<MyComponentProps>> = (props) => {
	const styled = {
		color: "red",
		cursor: "pointer",
		backgroundColor: "grey",
	};
	return (
		<>
			<h2 style={styled}>My name is {props.name}</h2>
			{props.children}
		</>
	);
};

export default MyComponent;

```

### Dynamic Style
`App.tsx`
```tsx
import { FC, PropsWithChildren } from "react";

interface MyComponentProps {
	name: string;
}
const MyComponent: FC<PropsWithChildren<MyComponentProps>> = (props) => {
	const styled = {
		color: props.name === "John" ? "blue" : "red",
		cursor: "pointer",
		backgroundColor: "grey",
	};
	return (
		<>
			<h2 style={styled}>My name is {props.name}</h2>
			{props.children}
		</>
	);
};

export default MyComponent;

```

---
## separate css file & how to use Class
`MyComponent2.module.css`
```css
.box {
    background-color: green;
}
.text-large {
    font-size: 40px;
}
```

use css class with **className** attribute

`MyComponent2.tsx`
```tsx
import { FC } from "react";
import css from "./MyComponent2.module.css";

interface MyComponent2Props {
	num: number;
}

const MyComponent2: FC<MyComponent2Props> = (props) => {
	return (
		<div className={css.box}>
			Component number <span className={css["text-large"]}>{props.num}</span>
		</div>
	);
};

export default MyComponent2;

```

put all class to variable
`MyComponent2.tsx`
```tsx
import { FC } from "react";
import css from "./MyComponent2.module.css";

interface MyComponent2Props {
	num: number;
}

const MyComponent2: FC<MyComponent2Props> = (props) => {
	const myClass = [css.box, css["text-large"], "text-green"].join(" ");
	return (
		<div className={myClass}>
			Component number <span>{props.num}</span>
		</div>
	);
};

export default MyComponent2;

```

### Dynamic class
`MyComponent2.module.css`
```css
.box {
    background-color: green;
}

.box2 {
    background-color: yellow;
}

.text-large {
    font-size: 40px;
}
```

`MyComponent2.tsx`
```tsx
import { FC } from "react";
import css from "./MyComponent2.module.css";

interface MyComponent2Props {
	num: number;
}

const MyComponent2: FC<MyComponent2Props> = (props) => {
	const myClass = [css["text-large"]];
	if (props.num === 2) {
		myClass.push(css.box2);
	}
	return (
		<div className={myClass.join(" ")}>
			Component number <span>{props.num}</span>
		</div>
	);
};

export default MyComponent2;

```


---

# Method & Event binding
we can use **_onClick_** to do on click event listener for the button
there are other events
eg: 
- onBlur
- onFocus
- onChange
- onInput
- onSubmit

`MyComponent.tsx`
```tsx
import { FC } from "react";
import css from "./MyComponent2.module.css";

interface MyComponent2Props {
	num: number;
}

const MyComponent2: FC<MyComponent2Props> = (props) => {
	const myClass = [css["text-large"]];
	if (props.num === 2) {
		myClass.push(css.box2);
	}

	function onClick() {
		console.log("clicked");
	}

	return (
		<div className={myClass.join(" ")}>
			Component number <span>{props.num}</span>
			<button onClick={onClick}>click me</button>
		</div>
	);
};

export default MyComponent2;

```

---
# State

`MyComponent.tsx`
```tsx
import { FC, PropsWithChildren } from "react";

interface MyComponentProps {
	name: string;
}
const MyComponent: FC<PropsWithChildren<MyComponentProps>> = (props) => {
	const state = {
		surname: "Jud",
		age: 22,
	};
	return (
		<>
			<h2>
				My name is {props.name} {state.surname}
			</h2>
			<span>age: {state.age}</span>
			{props.children}
		</>
	);
};
export default MyComponent;

```

`MyComponent.tsx`
```tsx
import { FC, PropsWithChildren, useState } from "react";

interface MyComponentProps {
	name: string;
}
const MyComponent: FC<PropsWithChildren<MyComponentProps>> = (props) => {
	const [persons, setPersons] = useState<{ firstname: string; surname: string }[]>([
		{
			firstname: "John",
			surname: "Jud",
		},
	]);
	return (
		<>
			<h2>
				My name is {persons[0].firstname} {persons[0].surname}
			</h2>
			{props.children}
		</>
	);
};

export default MyComponent;

```

### set state
```tsx
import { FC, PropsWithChildren, useState } from "react";

interface MyComponentProps {
	name: string;
}
const MyComponent: FC<PropsWithChildren<MyComponentProps>> = (props) => {
	const [persons, setPersons] = useState<{ firstname: string; surname: string }[] >([
	{ firstname: "John", surname: "Jud" }
	]);

	 function setNewPerson() {
		setPersons([
			{
				firstname: "James",
				surname: "Blunt",
			},
		]);
	}
	return (
		<>
			<h2>
				My name is {persons[0].firstname} {persons[0].surname}
			</h2>
			<button onClick={setNewPerson}>Add Person</button>
			{props.children}
		</>
	);
};

export default MyComponent;

```


### set state using setState callback 
must use this method when the state is Object or Array
```tsx
import { FC, PropsWithChildren, useState } from "react";

interface MyComponentProps {
	name: string;
}
const MyComponent: FC<PropsWithChildren<MyComponentProps>> = (props) => {
	const [persons, setPersons] = useState<{ firstname: string; surname: string }[] >([{ firstname: "John", surname: "Jud"}]);

	function addPerson() {
		setPersons((persons) => [
			...persons,
			{ firstname: "James", surname: "Blunt" },
		]);
	}
	return (
		<>
			<h2>
				My name is {persons[0].firstname} {persons[0].surname}
			</h2>
			<button onClick={addPerson}>Add Person</button>
			{props.children}
		</>
	);
};

export default MyComponent;

```

## recomended to use callback with set state
why?
`MyComponent.tsx`
```tsx
import { FC, useEffect, useState } from "react";

interface MyComponentProps {}

const MyComponent: FC<MyComponentProps> = (props) => {
	const [count, setCount] = useState(0);

	function changeCount(amount: number) {
		setCount(count + amount);
	}

	return (
		<div>
			<button onClick={(e) => changeCount(-1)}>-</button>
			<div>{count}</div>
			<button onClick={(e) => changeCount(1)}>+</button>
		</div>
	);
};

export default MyComponent;

```

this example work correctly 

what if I do like this
```tsx
import { FC, useEffect, useState } from "react";

interface MyComponentProps {}

const MyComponent: FC<MyComponentProps> = (props) => {
	const [count, setCount] = useState(0);

	function changeCount(amount: number) {
		setCount(count + amount);
		setCount(count + amount); // <------------- add this line 
	}

	return (
		<div>
			<button onClick={(e) => changeCount(-1)}>-</button>
			<div>{count}</div>
			<button onClick={(e) => changeCount(1)}>+</button>
		</div>
	);
};

export default MyComponent;

```

It should increase or decrease by double but it still increase or decrease by one only
this is why I recommended to use callback when update state value 

so change code to be like this 
```tsx
import { FC, useEffect, useState } from "react";

interface MyComponentProps {}

const MyComponent: FC<MyComponentProps> = (props) => {
	const [count, setCount] = useState(0);

	function changeCount(amount: number) {
		setCount((currentCount) => currentCount + amount); // <----- this
		setCount((currentCount) => currentCount + amount); // <----- and this
	}

	return (
		<div>
			<button onClick={(e) => changeCount(-1)}>-</button>
			<div>{count}</div>
			<button onClick={(e) => changeCount(1)}>+</button>
		</div>
	);
};

export default MyComponent;

```




---
## Value binding

create **useState** and binding setState with **onChange**

```tsx
import { FC, PropsWithChildren, useState } from "react";

interface MyComponentProps {
	name: string;
}
const MyComponent: FC<PropsWithChildren<MyComponentProps>> = (props) => {
	const [text, setText] = useState("");

	return (
		<>
			<h1>My Text is {text}</h1>
			<input type="text" onChange={(e) => setText(e.target.value)} />
		</>
	);
};

export default MyComponent;

```


If \<input /> is in another component how we handle it?

`MyComponent.tsx`
```tsx
import { FC, PropsWithChildren, useState } from "react";

interface MyComponentProps {
	onTextChange: (text: string, num: number) => void;
}
const MyComponent: FC<PropsWithChildren<MyComponentProps>> = (props) => {
	const [num, setNum] = useState(0);
	const [text, setText] = useState("");

	function sendStatesToParent() {
		props.onTextChange(text, num);
	}

	return (
		<>
			<input type="text" onChange={(e) => setText(e.target.value)} />
			<input
				type="number"
				value={num}
				onChange={(e) => setNum(+e.target.value)}
			/>
			<button onClick={sendStatesToParent}>Sent to Parent</button>
		</>
	);
};

export default MyComponent;

```

`App.tsx`
```tsx
import { useState } from "react";
import "./App.css";
import MyComponent from "./components/MyComponent";
import MyComponent2 from "./components/MyComponent2";

function App() {
	const [values, setValues] = useState("");

	function reset() {
		setValues("");
	}

	function handleMyComponentTextChange(text: string, num: number) {
		setValues(`Component Text: ${text} & num: ${num}`);
	}

	return (
		<div style={{ display: "flex", flexDirection: "column" }}>
			<h1>App Component</h1>
			<MyComponent onTextChange={handleMyComponentTextChange}>
				<div>Below is children</div>
				<MyComponent2 num={2} />
			</MyComponent>
			<hr />
			<button onClick={reset}>Reset</button>
			<h2>{values}</h2>
		</div>
	);
}

export default App;

```

if We need to reset sthe tate in child component how can we do it?
we can not do that 
we need to create states in parent component and then send setState to child component instead

`MyComponent.tsx`
```tsx
import { FC, PropsWithChildren, useState } from "react";

interface MyComponentProps {
	setText: (text: string) => void;
	num: number;
	setNum: (num: number) => void;
}
const MyComponent: FC<PropsWithChildren<MyComponentProps>> = (props) => {
	return (
		<>
			<input type="text" onChange={(e) => props.setText(e.target.value)} />
			<input
				type="number"
				value={props.num}
				onChange={(e) => props.setNum(+e.target.value)}
			/>
		</>
	);
};

export default MyComponent;

```

`App.tsx`
```tsx
import { useState } from "react";
import "./App.css";
import MyComponent from "./components/MyComponent";
import MyComponent2 from "./components/MyComponent2";

function App() {
	const [num, setNum] = useState(0);
	const [text, setText] = useState("");
	function reset() {
		setText("");
		setNum(0);
	}

	return (
		<div style={{ display: "flex", flexDirection: "column" }}>
			<h1>App Component</h1>
			<MyComponent setText={setText} setNum={setNum} num={num}>
				<div>Below is children</div>
				<MyComponent2 num={2} />
			</MyComponent>
			<hr />
			<button onClick={reset}>Reset</button>
			<h2>
				Text: {text} & Num: {num}
			</h2>
		</div>
	);
}

export default App;

```

---
# Conditional
## use const with if 
`App.tsx`
```tsx
import { useState } from "react";
import "./App.css";

function App() {
	const [num, setNum] = useState(5);

	let h2 = <h2>This number is lower or equal 5</h2>;
	if (num > 5) {
		h2 = <h2>This number is greater than 5</h2>;
	}

	return (
		<div>
			<button onClick={() => setNum((n) => (n += 1))}>+</button>
			<h1>{num}</h1>
			{h2}
			<button onClick={() => setNum((n) => (n -= 1))}>-</button>
		</div>
	);
}

export default App;

```
## use ternary expression

`App.tsx`
```tsx
import "./App.css";

function App() {
	const [num, setNum] = useState(0);

	return (
		<div>
			<button onClick={() => setNum((n) => (n += 1))}>+</button>
			<h1>{num}</h1>
			<h2>
				{num > 5
					? "This number is greater than 5"
					: "This number is lower or equal 5"}
			</h2>
			<button onClick={() => setNum((n) => (n -= 1))}>-</button>
		</div>
	);
}

export default App;

```


## use logical operators
`App.tsx`
```tsx
import { useState } from "react";
import "./App.css";

function App() {
	const [num, setNum] = useState(5);

	return (
		<div>
			<button onClick={() => setNum((n) => (n += 1))}>+</button>
			<h1>{num}</h1>
			<h2>
				{num > 5 && "This number is greater than 5"}
				{num <= 5 && "This number is lower or equal 5"}
			</h2>
			<button onClick={() => setNum((n) => (n -= 1))}>-</button>
		</div>
	);
}

export default App;

```

---

# For loop
**key** attribute is important
## use map in jsx

`App.tsx`
```tsx
import "./App.css";

function App() {
	const footballTeams = [
		{ name: "Arsenal" },
		{ name: "Liverpool" },
		{ name: "Man U" },
		{ name: "Chelsea" },
	];

	return (
		<div>
			<select>
				{footballTeams.map((team) => (
					<option key={team.name} value={team.name}>
						{team.name}
					</option>
				))}
			</select>
		</div>
	);
}

export default App;


```


## use constant
`App.tsx`
```tsx
import "./App.css";

function App() {
	const footballTeams = [
		{ name: "Arsenal" },
		{ name: "Liverpool" },
		{ name: "Man U" },
		{ name: "Chelsea" },
	];

	const options = footballTeams.map((team) => {
		return (
			<option key={team.name} value={team.name}>
				{team.name}
			</option>
		);
	});

	return (
		<div>
			<select>{options}</select>
		</div>
	);
}
export default App;


```


---
# DOM Ref

if we have an \<input />
and \<button>

when we click on button let focus on the \<input>
this situation we need to use ref

`App.tsx`
```tsx
import { useRef } from "react";
import "./App.css";

function App() {
	const inputRef = useRef<HTMLInputElement>(null);
	function onClick() {
		inputRef.current?.focus();
	}
	return (
		<div>
			<input ref={inputRef} type="text" />
			<button onClick={onClick}>focus on input</button>
		</div>
	);
}

export default App;

```


we can use ref to determine the input value instead of useState

`App.tsx`
```tsx
import { ChangeEvent, useRef, useState } from "react";
import "./App.css";

function App() {
	const inputRef = useRef<HTMLInputElement>(null);
	const inputRef2 = useRef<HTMLInputElement>(null);

	const [values, setValues] = useState("");

	function onSubmit(e: ChangeEvent<HTMLFormElement>) {
		e.preventDefault();
		setValues(
			`ref1 ${inputRef.current?.value} ref2: ${inputRef2.current?.value}`,
		);
	}

	return (
		<div>
			<form onSubmit={onSubmit}>
				<input ref={inputRef} type="text" />
				<input ref={inputRef2} />
				<button type="submit">submit</button>
			</form>

			<h1>{values}</h1>
		</div>
	);
}

export default App;

```


---
in case of \<input> is in another component but we want to ref it how can we do?


Child component we need to wrap it using **forwardRef** and Actual component we need to keep props interface available in typescript so we need to use **ForwardRefRenderFunction\<RefType, PropsType>**
then wrap it with **forwardRef** later

`MyComponent.tsx`
```tsx
import { forwardRef, ForwardRefRenderFunction } from "react";

interface MyComponentProps {}

const MyComponentRenderFn: ForwardRefRenderFunction<
	HTMLInputElement,
	MyComponentProps
> = (props, ref) => {
	return (
		<>
			<input ref={ref} type="text" />
		</>
	);
};

const MyComponent = forwardRef(MyComponentRenderFn);

export default MyComponent;

```

`App.tsx`
```tsx
import { ChangeEvent, createRef, useRef, useState } from "react";
import "./App.css";
import MyComponent from "./components/MyComponent";

function App() {
	const inputRef = createRef<HTMLInputElement>();

	const [values, setValues] = useState("");
	function showValue() {
		setValues(`input value: ${inputRef.current?.value}`);
	}
	return (
		<div>
			<MyComponent ref={inputRef} />
			<button onClick={showValue}>show</button>
			<hr />
			<h1>{values}</h1>
		</div>
	);
}

export default App;

```

# React Hooks
## Effect

react component is **PURE Function**

so anything that affected the result of a function that is **effects**  

**_example_**
*pure function* 
```ts
function add(a: number, b: number) {
	return a + b;
}
```

*impure function*
```ts
let additionalNumber = 10;
function add(a: number, b: number) {
	a += additionalNumber; // <------- side effect
	return a + b;
}
```


In the real world application we could not build app with pure functional components
in some components will be have side effects
so we need to place side effect in **useEffect**


## useEffect

useEffect() will be call after component mounted
we have to pass function as an argument

`MyComponent.tsx`
```tsx
import { forwardRef, ForwardRefRenderFunction, useEffect } from "react";

interface MyComponentProps {}

const MyComponentRenderFn: ForwardRefRenderFunction<
	HTMLInputElement,
	MyComponentProps
> = (props, ref) => {
	useEffect(() => {
		console.log("My Compnent rendered");
	});

	return (
		<>
			<input ref={ref} type="text" />
		</>
	);
};

const MyComponent = forwardRef(MyComponentRenderFn);

export default MyComponent;

```

![[Pasted image 20221012030201.png]]

we will use useEffect() like this
`MyComponent.tsx`
```tsx
import { forwardRef, FC, useEffect, useState } from "react";

interface MyComponentProps {}

interface IFootballTeams {
	name: string;
	coach: string;
}

const MyComponent: FC<MyComponentProps> = (props) => {
	const [footballTeams, setFootballTeams] = useState<IFootballTeams[]>([]);

	useEffect(() => {
		console.log("My Compnent rendered");
		async function fetchFootballTeams() {
			const res = await fetch("/football.json");
			const teams: IFootballTeams[] = await res.json();
			console.log(teams);
			setFootballTeams(teams);
		}
		fetchFootballTeams();
	});

	return <>MyComponent</>;
};

export default MyComponent;

```

**But** you will see MyComponent get **re-render infinitely**
so we have to provide the **dependencies** in **Second Argument** in the useEffect() like this

`MyComponent.tsx`
```tsx
import { forwardRef, FC, useEffect, useState } from "react";

interface MyComponentProps {}

interface IFootballTeams {
	name: string;
	coach: string;
}

const MyComponent: FC<MyComponentProps> = (props) => {
	const [footballTeams, setFootballTeams] = useState<IFootballTeams[]>([]);

	useEffect(() => {
		console.log("My Compnent rendered");
		async function fetchFootballTeams() {
			const res = await fetch("/football.json");
			const teams: IFootballTeams[] = await res.json();
			console.log(teams);
			setFootballTeams(teams);
		}
		fetchFootballTeams();
	}, [setFootballTeams]); // <--------- dependencies

	return <>MyComponent</>;
};

export default MyComponent;

```

useEffect() will call the **callback()** when dependencies change their value


see below example case
`MyComponent.tsx`
```tsx
import { FC, useEffect, useState } from "react";

interface MyComponentProps {}

const MyComponent: FC<MyComponentProps> = (props) => {
	const [firstName, setFirstName] = useState("");
	const [lastName, setLastName] = useState("");
	const [isDarkMode, setIsDarkMode] = useState(false);
	const person = { firstName, lastName };

	useEffect(() => {
		console.log(person);
	}, [person]);

	return (
		<div style={{ backgroundColor: isDarkMode ? "grey" : "white" }}>
			<input
				type="text"
				value={firstName}
				onChange={(e) => setFirstName(e.target.value)}
			/>
			<input
				type="text"
				value={lastName}
				onChange={(e) => setLastName(e.target.value)}
			/>
			<div>
				<label htmlFor="darkmode">Darkmode</label>
				<input
					type="checkbox"
					id="darkmode"
					checked={isDarkMode}
					onChange={(e) => setIsDarkMode(e.target.checked)}
				/>
			</div>
			<hr />
			<div>{JSON.stringify(person)}</div>
		</div>
	);
};

export default MyComponent;

```

the code above is loggin person in useEffect()
when firstName or lastName value changed 
but when isDarkMode value changed useEffect() also re-render 

this sittuation bring us to useMemo()


---
# useMemo()

`MyComponent.tsx`
```tsx
import { FC, useEffect, useMemo, useState } from "react";

interface MyComponentProps {}

const MyComponent: FC<MyComponentProps> = (props) => {
	const [firstName, setFirstName] = useState("");
	const [lastName, setLastName] = useState("");
	const [isDarkMode, setIsDarkMode] = useState(false);

	const person = useMemo(() => {  // <---- useMemo
		return { firstName, lastName };
	}, [firstName, lastName]); // <--- have dependencies as well

	useEffect(() => {
		console.log(person);
	}, [person]);

	return (
		<div style={{ backgroundColor: isDarkMode ? "grey" : "white" }}>
			<input
				type="text"
				value={firstName}
				onChange={(e) => setFirstName(e.target.value)}
			/>
			<input
				type="text"
				value={lastName}
				onChange={(e) => setLastName(e.target.value)}
			/>
			<div>
				<label htmlFor="darkmode">Darkmode</label>
				<input
					type="checkbox"
					id="darkmode"
					checked={isDarkMode}
					onChange={(e) => setIsDarkMode(e.target.checked)}
				/>
			</div>
			<hr />
			<div>{JSON.stringify(person)}</div>
		</div>
	);
};

export default MyComponent;

```


---

# useCallback()

useCallback() is use in the same case as useMemo() but it will memorize the function
`MyComponent.tsx`
```tsx
import React from "react";
import { FC, useEffect, useState } from "react";

interface IFootballTeam {
	name: string;
	coach: string;
}

const MyComponent: FC = () => {
	const [teams, setTeams] = useState<IFootballTeam[]>([]);

	async function fetchFootballteams() {
		const res = await fetch("/football.json");
		const json: IFootballTeam[] = await res.json();
		setTeams(json);
	}

	useEffect(() => {
		fetchFootballteams();
	}, [fetchFootballteams]);

	return (
		<div>
			{teams.map((t) => (
				<div key={t.name}>
					{t.name} - {t.coach}
				</div>
			))}
		</div>
	);
};

export default MyComponent;

```

above example 
you will see that look normal in browser but if you open deveoper tools in **Network** tab you will see browser fetching data inifinitely

why?
because when **fetchFootballteams()** is got all data the function set all data to state **setTeams**  here state is changed so component is re-render as React behavior does
then every is building again, the function fetchFootballteasm() as well so useEffect() will trigger again becuase it's dependencies like function fetchFootballteams() is changed not (it on different pointer on memory)
In this situation useCallback() come to rescue

so we use useCallback() like this
```tsx
import React, { useCallback } from "react";
import { FC, useEffect, useState } from "react";

interface IFootballTeam {
	name: string;
	coach: string;
}

const MyComponent: FC = () => {
	const [teams, setTeams] = useState<IFootballTeam[]>([]);

	const fetchFootballteams = useCallback(async () => { // <--- useCallback()
		const res = await fetch("/football.json");
		const json: IFootballTeam[] = await res.json();
		setTeams(json);
	}, []); // <---- no any dependencies

	useEffect(() => {
		fetchFootballteams();
	}, [fetchFootballteams]);

	return (
		<div>
			{teams.map((t) => (
				<div key={t.name}>
					{t.name} - {t.coach}
				</div>
			))}
		</div>
	);
};

export default MyComponent;

```

---
# useEffect() like a pro

check this example
`MyComponent.tsx`
```tsx
import { useEffect } from "react";
import { FC, useState } from "react";

interface IFootballTeam {
	name: string;
	coach: string;
}

const MyComponent: FC = () => {
	const [search, setSearch] = useState("");

	const [display, setDisplay] = useState("");

	useEffect(() => {
		(async () => {
			const url = `http://localhost:3000/${search}`;
			const result = await fetch(url);
			const json: IFootballTeam = await result.json();
			console.log(json);
			setDisplay(json.coach);
		})();
	}, [search]);

	return (
		<div>
			<h1>{display}</h1>
			<button onClick={() => setSearch("TOT")}>Search TOT</button>
			<button onClick={() => setSearch("Man U")}>Search Man U</button>
		</div>
	);
};

export default MyComponent;

```

this component is working normally 
an api `localhost:3000` is a mocking of a server that takes a long time to respond in this case is 3 second

in the browser when we click search TOT the manager of TOT will show after 3 second

but 
when we click on search Man U then we thought Oh!! I clicked on the wrong button, I have to click on Search TOT 
then we click on search TOT again to correct the action
what happen here the name of Man U manager is showing then followed by the corrected team TOT manager is showing after 

in this case, we have to cancel the fetching before
**AbortController** is the thing we need
```tsx
import { useEffect } from "react";
import { FC, useState } from "react";

interface IFootballTeam {
	name: string;
	coach: string;
}

const MyComponent: FC = () => {
	const [search, setSearch] = useState("");

	const [display, setDisplay] = useState("");

	useEffect(() => {
		const abortController = new AbortController() // <---- this
		const abortSignal = abortController.signal // <------- this
		async function fetchFootballTeam() {
			const url = `http://localhost:3000/${search}`;
			const result = await fetch(url, {signal: abortSignal});
			const json: IFootballTeam = await result.json();
			console.log(json);
			setDisplay(json.coach);
		}
		fetchFootballTeam()
		return () => { // <------ also need cleanup callback
			abortController.abort()
		}
	}, [search]);

	return (
		<div>
			<h1>{display}</h1>
			<button onClick={() => setSearch("TOT")}>Search TOT</button>
			<button onClick={() => setSearch("Man U")}>Search Man U</button>
		</div>
	);
};

export default MyComponent;

```

**Axios** is a popular fetcher as well

```tsx
import { useEffect } from "react";
import { FC, useState } from "react";
import axios from "axios"

interface IFootballTeam {
	name: string;
	coach: string;
}

const MyComponent: FC = () => {
	const [search, setSearch] = useState("");

	const [display, setDisplay] = useState("");

	useEffect(() => {
		const CancelToken = axios.CancelToken // <----- this
		const source = CancelToken.source()   // <----- this
		async function fetchFootballTeam() {
			const url = `http://localhost:3000/${search}`;
			const result = await axios.get(url, {cancelToken: source.token} );
			const json: IFootballTeam = result.data;
			console.log(json);
			setDisplay(json.coach);
		}
		fetchFootballTeam()
		return () => {
			source.cancel("Cancelled by user") // <---- this
		}
	}, [search]);

	return (
		<div>
			<h1>{display}</h1>
			<button onClick={() => setSearch("TOT")}>Search TOT</button>
			<button onClick={() => setSearch("Man U")}>Search Man U</button>
		</div>
	);
};

export default MyComponent;

```

---
# Custom Hook

the custom hook is a function that does not return HTML Elements
it works like a React component but has no any render of HTML
sure custom hooks can use other hooks as well

from above example I will move fetchFootballTeam() into custom hook

`useFootball.ts`
```ts
import { useCallback, useEffect, useState } from "react";
import axios, { CancelToken } from "axios"

interface IFootballTeam {
    name: string;
    coach: string;
}

export function useFootball() {
    const [search, setSearch] = useState("")
    const [display, setDisplay] = useState("");

    const fetchFootballTeam = useCallback(async (cancelToken: CancelToken) => {
        const url = `http://localhost:3000/${search}`;
        const result = await axios.get(url, { cancelToken: cancelToken });
        const json: IFootballTeam = result.data;
        console.log(json);
        setDisplay(json.coach)
    }, [search])

    useEffect(() => {
        const CancelToken = axios.CancelToken
        const source = CancelToken.source()
        fetchFootballTeam(source.token)
        return () => {
            source.cancel("Cancelled by user")
        }
    }, [fetchFootballTeam]);
    return { display, setSearch }
}

```

so our component code is reduced 

`MyComponent.tsx`
```tsx
import { FC } from "react";
import { useFootball } from "../hooks/football";


const MyComponent: FC = () => {
	const {setSearch, display} = useFootball()

	return (
		<div>
			<h1>{display}</h1>
			<button onClick={() => setSearch("TOT")}>Search TOT</button>
			<button onClick={() => setSearch("Man U")}>Search Man U</button>
		</div>
	);
};

export default MyComponent;

```


---
# Store
there are a lot of store management in React world
eg. 
- Redux toolkit
- React context
- MobX
- Zustand
- Recoil
- Jotai
- Nanostore
- RxJS as well hahaaaa

in this lecture I will use [**Jotai**](https://jotai.org/)
In my opinion Jotai is very easy to use



I will use above example to be use **display** state to be a store

`football.store.ts`
```ts
import { atom } from "jotai"

export const displayAtom = atom("")
```

`football.hook.ts`
```ts
import { useCallback, useEffect, useState } from "react";
import axios, { CancelToken } from "axios"
import { useAtom } from "jotai"
import { displayAtom } from "../store/football";

interface IFootballTeam {
    name: string;
    coach: string;
}

export function useFootball() {
    const [search, setSearch] = useState("")
    // const [display, setDisplay] = useState("");
    const [display, setDisplay] = useAtom(displayAtom) // <--- use atom like useState

    const fetchFootballTeam = useCallback(async (cancelToken: CancelToken) => {
        const url = `http://localhost:3000/${search}`;
        const result = await axios.get(url, { cancelToken: cancelToken });
        const json: IFootballTeam = result.data;
        console.log(json);
        setDisplay(json.coach)
    }, [search, setDisplay])

    useEffect(() => {
        const CancelToken = axios.CancelToken
        const source = CancelToken.source()
        fetchFootballTeam(source.token)
        return () => {
            source.cancel("Cancelled by user")
        }
    }, [fetchFootballTeam]);
    return { setSearch }
}

```

`MyComponent.tsx`
```tsx
import { FC } from "react";
import { useFootball } from "../hooks/football";
import { useAtom} from "jotai"
import { displayAtom } from "../store/football";

const MyComponent: FC = () => {
	const {setSearch} = useFootball()
	const [display] = useAtom(displayAtom) // <--- useAtom same as in hook but here is ignore write the store

	return (
		<div>
			<h1>{display}</h1>
			<button onClick={() => setSearch("TOT")}>Search TOT</button>
			<button onClick={() => setSearch("Man U")}>Search Man U</button>
		</div>
	);
};

export default MyComponent;

```

---
### Jotai another example

`name.store.ts`
```ts
import { atom } from "jotai"

export const firstNameAtom = atom("")
export const lastNameAtom = atom("")
```

`MyComponent.tsx`
```tsx
import { FC } from "react";
import { useAtom } from "jotai";
import { firstNameAtom, lastNameAtom } from "../store/name.store";

const MyComponent: FC = () => {
	const [, setFirstName] = useAtom(firstNameAtom);
	const [, setLastName] = useAtom(lastNameAtom);

	return (
		<div>
			<div>
				<label htmlFor="firstname">First Name</label>
				<input type="text" id="firstname" onChange={(e) => setFirstName(e.target.value)} />
			</div>
			<div>
				<label htmlFor="lastname">Last Name</label>
				<input type="text" id="lastname" onChange={(e) => setLastName(e.target.value)} />
			</div>
		</div>
	);
};

export default MyComponent;

```

we can combine atom to other atom 
from above example I will make fullName by combine firstName and lastName

`name.store.ts`
```ts
import { atom } from "jotai"

export const firstNameAtom = atom("")
export const lastNameAtom = atom("")
export const fullNameAtom = atom(
    (get) => `${get(firstNameAtom)} ${get(lastNameAtom)}` // <---- read only atom
)
```

`App.tsx`
```tsx
import React from "react";
import "./App.css";
import MyComponent from "./components/MyComponent";
import { useAtom } from "jotai"
import { firstNameAtom, lastNameAtom, fullNameAtom } from "./store/name.store";

function App() {
	const [firstName] = useAtom(firstNameAtom)
	const [lastName] = useAtom(lastNameAtom)
	const [fullname] = useAtom(fullNameAtom)
	return (
		<div>
			<h1>First name: {firstName}</h1>
			<h1>Last name: {lastName}</h1>
			<h1>Full name: {fullname}</h1>
			<MyComponent />
		</div>
	);
}

export default App;

```

if you need devtools for debuggin Jotai please go to [**Jotai website**](https://jotai.org/docs/api/devtools)

# Forms

the most popular React forms libary is [**Formik**](https://formik.org/) and [**react-hook-form**](https://react-hook-form.com/)

We will use **react-hook-form** only 
Why do I decide use react-hook-form please checkout [this](https://blog.logrocket.com/react-hook-form-vs-formik-comparison/)

dependencies
```bash
npm install react-hook-form zod @hookform/resolvers
```

`App.tsx`
```tsx
import { FC } from "react";
import { useForm } from "react-hook-form"
import { z } from "zod"
import { zodResolver } from "@hookform/resolvers/zod"
const MyComponent: FC = () => {

	const schema = z.object({
		firstname: z.string().min(2, { message: "length of first name should >= 2" }),
		lastname: z.string().min(2, { message: "length of last name should >= 2" }),
		age: z.number({ required_error: "missing age", invalid_type_error: "age should be number" }).positive(),
		email: z.string().email({ message: "invalid email" }),
		phone: z.number()
	})

	const { register, handleSubmit, formState: { errors } } = useForm<z.infer<typeof schema>>({ resolver: zodResolver(schema), mode: "all" })


	const onSubmit = handleSubmit((data) => {
		console.log("data", data)
	}, (err) => {
		console.log(err)
	})

	return (
		<form onSubmit={onSubmit}>
			<div>
				<label htmlFor="firstname">First Name</label>
				<input type="text" id="firstname" {...register("firstname")} defaultValue="John" />
				<span style={{ display: errors.firstname ? "block" : "none" }}>Error: {errors.firstname?.message}</span>
			</div>
			<div>
				<label htmlFor="lastname">Last Name</label>
				<input type="text" id="lastname" {...register("lastname")} defaultValue="Jud" />
			</div>
			<div>
				<label htmlFor="age">Age</label>
				<input type="text" id="age" {...register("age", { valueAsNumber: true })} />
			</div>
			<div>
				<label htmlFor="email">Email</label>
				<input type="text" id="email" {...register("email")} />
			</div>
			<div>
				<label htmlFor="phone">phone</label>
				<input type="text" id="phone" {...register("phone", { valueAsNumber: true })} defaultValue={0} />
			</div>
			<button type="submit">Submit</button>
		</form>
	);
};

export default MyComponent;

```

# React router
please check [react-router-dom example](https://reactrouter.com/en/main/start/tutorial)

dependencies
```bash
npm install react-router-dom
```

create new About page
`About.tsx`
```tsx
import { useNavigate } from "react-router-dom";

const AboutPage: React.FunctionComponent = () => {
    const navigate = useNavigate()
    return (
        <>
            <h1>About</h1>
            <button onClick={() => navigate("/")}>back to home</button>
        </>
    )
};

export default AboutPage;

```

`App.tsx`
```tsx
import { Link } from "react-router-dom";
import "./App.css";
import MyComponent from "./components/MyComponent";

function App() {
	return (
		<div>
			<MyComponent />
			<Link to="/about">Go to About</Link>
		</div>
	);
}

export default App;

```

register routes
`main.tsx`
```tsx
import React from 'react'
import ReactDOM from 'react-dom/client'
import { createBrowserRouter, RouterProvider } from 'react-router-dom'
import AboutPage from './About'
import App from './App'
import './index.css'

const routers = createBrowserRouter([
  {
    path: "/",
    element: <App />
  },
  {
    path: "/about",
    element: <AboutPage />
  }
])

ReactDOM.createRoot(document.getElementById('root') as HTMLElement).render(
  <React.StrictMode>
    <RouterProvider router={routers} />
  </React.StrictMode>
)

```



















