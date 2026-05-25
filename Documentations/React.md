# Notes

{ } → used to say: “hey, this is a JS expression, don’t render it as normal html”.

Functions:

```jsx
// normal function:
function test() {
	// code goes here
};

// Arrow Function:
const test = (props) => {
	// code goes here
};
```

### Styles:

we can put styles explicid in stylesheet files, or we can apply it inside each compontent or even inside an xml attribute and none of them all is non-usual:

```jsx
const Header = () => {
	return (
		<header style={
			backGroundColor: 'mediumBlue',
			color: '#ff',
		}>
			<h1>Hello World!</h1>
		</header>
}

export default Header;

// or the other way:
const Header = () => {
	const headerStyle = {
			backGroundColor: 'mediumBlue',
			color: '#ff',
	}
	
	return (
		<header style={headerStyle}>
			<h1>Hello World!</h1>
		</header>
	)
}

export default Header;
```

### Click Events:

Anonymous Functions:

✅ `() => handleEvent(params)`

> You're defining a function that will only run when the event happens (e.g. a click).
> 
> 
> It’s like saying:
> 
> “When the button is clicked, run this function that calls `handleEvent(params)`.”
> 

❌ `handleEvent(params)`

> You're calling the function immediately, during the rendering phase of the component.
> 
> 
> It doesn't wait for the user to click — it just runs **right away** and whatever it returns is passed to `onClick`, which breaks the expected behavior.
> 

---

### Think of it like this:

- `onClick={handleEvent(5)}` → calls the function **now**.
- `onClick={() => handleEvent(5)}` → tells React: **“Here’s a function to run later when clicked.”**

### useState Hook:

```jsx
import { useState } from 'react';

const Content = () => {
	const [name, setName] = useState('AbdElrhman');

	const handleRandomNameChange = () => {
		const names = ['AbdELrhman', 'Hassan', 'Omar'];
		const int = Math.floor(Math.random() * names.length);
		setName(names[int]);
	};

	const handleInputChange = (e) => {
		setName(e.target.value);
	};

	return (
		<div>
			<p>{name}</p>
			<button onClick={handleRandomNameChange}>Change Name</button>
			<input type="text" onChange={handleInputChange} placeholder="Type a new name" />
		</div>
	);
};

export default Content;
```

### Lists And Keys:

Maping items in jsx:

```jsx
import { useState } from 'react';

const Content = () => {
	const [items, setItems] = useState([
		{
			id: 1,
			name: "AbdElrhman",
			age: 19,
			checked: false,
		},
		{
			id: 2,
			name: "Mohamed",
			age: 20,
			checked: false,
		},
		{
			id: 3,
			name: "Omar",
			age: 18,
			checked: false,
		},
	]);
	
	return (
		<div>
			<ul>
				{items.map((item) => (
					<li className="item" key={item.id}> {/* className="item": this helps us with css: .item {} */}
						<input
							type="checkbox"
							checked={item.checked}
						/>
						<label>{item.name}</label>
						<button>Delete</button>
					</li>
				))}
			</ul>
		</div>
	);
};

export default Content;
```

```jsx
// creating a shallow copy of an array from a state:
const handleCheck = (id) => {
	const listItems = [...items];
};

// checking while maping:
const handleCheck = (id) => {
	const listItems = items.map((item) => item.id === id ?
	 {...item, checked: !items.checked} : item));
	 
	 setItems(listItems);
};
```

local storage:

```jsx
// saving item to local storage:
localStorage.setItem('shoppingList', JSON.stringify(listItems));
// this will save the data to place in the local storage called 'shoppingList' if it is there,
// and will create this place if doesn't exist.

// getting the item;
localStorage.getItem('shoppingList');
// Example with useState:
const [items, setItems] = useState(JSON.parse(localStorage.getItem('shoppingList')));

// a better recommended way:
const [items, setItems] = useState(() => {
	const saved = localStorage.getItem('shoppingList');
	return saved ? JSON.parse(saved) : [];
})
```

**Note:**

- **`JSON.stringify()`**: Converts JavaScript lists and objects to a JSON string.
- **`JSON.parse()`**: Converts a JSON string back into a JavaScript object, list, number, boolean, or other valid JavaScript value.

### Props and Props Drilling:

props can be passed through react components so that we can give access of a data to more than one compentent easily:

```jsx
const App = () => {
	return (
		<Header title="Groceries" creator="AbdElrhman" /> // now here we have given some props to this component.
		<Body />
		<Footer />
	);
}

// now inside the file of the compenent we can easily recieve those props:
const Header = (props) => {
	
	return (
		<header>
			<h1>{props.title}</h1>
		</header>
	)
}

// we can also destructure the props and use them directly in the following way:
const Header = ({ title, creator }) => {
	
	return (
		<header>
			<h1>{title}</h1>
		</header>
	)
}
```

Default props syntax:

```jsx
const Header = ({ title, creator }) => {
	
	return (
		<header>
			<h1>{title}</h1>
		</header>
	)
}

Header.defaultProps = {
	title: "Default Title"
}
```

### Use Effect:

```jsx
useEffect(() => {
	console.log("load time")
}, [])

// use effect hook runs only once when the application loads
// if you add variables or tates to the dependency array [], use effect will also run when any of them change
```

**Note: Use Effect runs when all components has already loaded and rendered.**

**Note: Be careful about endless use effect loops:**

```jsx
useEffect(() => {
	setItems([]);
}, [items])

// this will cause an endless loop becaue the useEffect Hook will be re-called again and again everytime list itmes state
```

### Fetch Api:

```jsx
// example on fetching data when the app starts:
useEffect(() => {
	const fetchItems = async() => {
		try {
			const response = await fetch(API_URL);
			
			// error handeling:
			if (!response.ok) throw Error('Did not receive expected data');
			
			const listItems = await response.json();
			console.log(listItems);
			setItems(listItems); // update the state of the data.
		} catch (err) {
			console.log(err.stack); // .stack helps us see all the error details, but we can use .message to see the error message only
		} finally {
			setIsLoading(false);
		}
	}
	// now call the function:
	(async () => await fetchItems())();
}, [])
```

### 🔹 Normal Arrow Function

A regular arrow function is synchronous — it returns a value **immediately**.

```jsx
const add = (a, b) => a + b;

console.log(add(2, 3)); // Output: 5

```

---

### 🔹 Async Arrow Function

An `async` arrow function always returns a **Promise**, even if you return a plain value. It allows use of the `await` keyword inside.

```jsx
const addAsync = async (a, b) => a + b;

console.log(addAsync(2, 3)); // Output: Promise { 5 }

addAsync(2, 3).then(result => console.log(result)); // Output: 5

```

You can also `await` other async operations (e.g. `fetch`, database call, etc.):

```jsx
const fetchData = async () => {
  const res = await fetch('https://api.example.com');
  const data = await res.json();
  return data;
};

```

---

### 🔸 Summary Table

| Feature              | Normal Arrow Function | Async Arrow Function |
| -------------------- | --------------------- | -------------------- |
| Return type          | Any value             | Always a Promise     |
| Uses `await` inside? | ❌ Not allowed         | ✅ Allowed            |
| Async operations?    | Must use `.then()`    | Can use `await`      |
| Error handling       | Try/catch won't work  | Can use `try/catch`  |

### Setting timeOut:

```jsx
setTimeout(() => {
	(async () => await fetchItems())();
}, 2000)
```

### CRUD Operations:

```jsx
const apiRequest = async(url = '', optionsObj = null, errMsg = null) => {
	try {
		const response = await fetch(url, optionsObj);
		if (!response.ok) throw Error('Please reload the app');
	} catch (err) {
		errMsg = err.message;
	} finally {
		return errMsg;
	}
}

// using the api request function that we have created:
const postOptions = {
	method: 'POST',
	headers: {
		'Content-Type': 'application/json'
	},
	body: JSON.stringfy(myNewData)
}
const result = await apiRequest(API_URL, postOptions);
if (result) setFetchError(result);
```

**Note:**

- **`POST` method is used when we want to add data that didn’t exist before.**
- **`PUT` or `PATCH` methods are used when we want to edit data that did exist previously.**
    - `PUT`: replace the entire record.
    - `PATCH`: update only specific fields.
- **`DELETE` method is used when we want to delete data that did exist previously.**

Using update "PATCH" method:
```jsx
const apiRequest = async (url = '', optionsObj = null) => {
	try {
		const response = await fetch(url, optionsObj);
		if (!response.ok) throw Error('Please reload the app');
		return null; // No error
	} catch (err) {
		return err.message;
	}
}

const handleUpdate = async (id) => {
	const myItem = listItems.filter((item) => item.id === id);
	if (myItem.length === 0) return; // optional safety check

	const updateOptions = {
		method: 'PATCH',
		headers: {
			'Content-Type': 'application/json',
		},
		body: JSON.stringify({ checked: !myItem[0].checked }),
		// we are using myItem[0] even we know that this is only one item;
		// this is because filter will not return an object, it will still
		// return an array even if it has only one object.
	};

	const reqUrl = `${API_URL}/${id}`;
	const result = await apiRequest(reqUrl, updateOptions);
	if (result) setFetchError(result);
};

```

JS Object flatting:
```jsx
function flattenObject(obj) {  
    return Object.entries(obj)
    .map(([key, value]) => `${key}: ${value}`)
    .join(', ');  
}
```

### React Router

in main.jsx:
```jsx
// here we don't all the routes, we only add the enetry point route, not all the routes:
import React from 'react'
import { createRoot } from 'react-dom/client'
import { BrowserRouter } from 'react-router-dom'

import App from './App.jsx'

createRoot(document.getElementById('root')).render(
    <React.StrictMode>
        <BrowserRouter>
            <App />
        </BrowserRouter>
    </React.StrictMode>
)

```

in App.jsx:
```jsx
import React from 'react'
import { Routes, Route } from 'react-router-dom'
import './App.css'

// Import your pages/components
import Header from './Header'
import Nav from './Nav'
import Footer from './Footer'
import Home from './Home'
import NewPost from './NewPost'
import PostPage from './PostPage'
import About from './About'
import Missing from './Missing'

function App() {
  return (
    <div className="App">
      {/* Common UI on every page */}
      <Header />
      <Nav />

      {/* Define routes for different pages */}
      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/post" element={<NewPost />} />
        <Route path="/post/:id" element={<PostPage />} />
        <Route path="/about" element={<About />} />
        {/* Catch-all for unmatched routes */}
        <Route path="*" element={<Missing />} />
      </Routes>

      {/* Footer common to all pages */}
      <Footer />
    </div>
  )
}

export default App

```

### React Router Hooks And Links
##### Synchronous Two-Way Binding Between React State and Input Field
```jsx
<form className='search-form' onSubmit={(e) => e.preventDefault()}>  
	<input  
		id="search"  
		type='text'  
		placeholder='Search Posts'  
		value={search}  
		onChange={(e) => setSearch(e.target.value)}  
	/>  
</form>
```

- The `onChange` event handler detects when the user types or changes the input.
    
- Inside `onChange`, the function `(e) => setSearch(e.target.value)` updates the React state variable (`search`) with the current input value.
    
- The input element uses `value={search}` to bind its displayed content to this state variable.
    
- This creates a **synchronous loop** where:
    
    - User input triggers `onChange`.
        
    - `setSearch` updates the state.
        
    - The updated state reflects immediately in the input via `value={search}`.
        
- This pattern keeps React state and the input field **always in sync**, ensuring the UI reflects the latest data and React controls the input’s value.

#### useParams Hook:

If you are redirected to a new page via a link that contains a route parameter, you can access that parameter on the new page using the `useParams` hook like this:
```jsx
// Define the route with a parameter (e.g., id)
<Route path="/post/:id" element={<PostPage />} />

// Create a link that navigates to the route with a specific id
<Link to={`/post/${post.id}`}>Go to post</Link>

// Inside PostPage component, extract the id parameter from the URL
import { useParams } from 'react-router-dom';

function PostPage() {
  const { id } = useParams();
  
  // Now you can use the id parameter in your component logic
  return <div>Post ID: {id}</div>;
}

```

#### The difference between filter and find:

`find`:
- Returns **the first element** that matches the condition.
    
- Returns the **element itself** (e.g., object, number, string).
    
- If **not found**, returns `undefined`.
```jsx
const post = posts.find(post => post.id === id);
// post is an object or undefined
```
 
 `filter`:
- Returns a **new array** of **all matching elements**.
    
- Always returns an array — it could be empty, have one item, or more.
```jsx
const filteredPosts = posts.filter(post => post.id === id);
// filteredPosts is an array

```

### Axios API
fetching the data with useEffect hook:
```jsx
useEffect(() => {
	const fetchPosts = async () => {
		try {
			const response = await api.get('/posts');
			setPosts(response.data);
		} catch (err) {
			if (err.response) {
				console.log(err.response.data);
				console.log(err.response.status);
				console.log(err.response.headers);
			} else {
				console.log(`Error: ${err.message}`);
			}
		}
	}
	fetchPosts();
}, [])
```

CRUD Operations:
```jsx
// get request:
const response = await api.get('/posts');
// delete request:
await api.delete(`posts/${id}`);
// post request:
const response = await api.post('/posts', newPost);
// response in post request will be the same as the passed data for the request "newPost" if the rsponse case was in 200 or 300 ranges.
```

### Custom Hooks
Example Custom hook:
```jsx
// useAxiosFetch:
import axios from 'axios';
import { useState, useEffect } from 'react';

const useAxiosFetch = (dataUrl) => {
    const [data, setData] = useState([]);
    const [fetchError, setFetchError] = useState(null);
    const [isLoading, setIsLoading] = useState(null);
    
    useEffect(() => {
        let isMounted = true;
        const source = axios.CancelToken.source();
        
        const fetchData = async (url) => {
            setIsLoading(true);
            try {
                const response = await axios.get(url, {
                    cancelToken: source.token
                });
                if (isMounted) {
                    setData(response.data);
                    setFetchError(null);
                }
            } catch (err) {
                if (isMounted) {
                    setFetchError(err.message);
                    setData([]);
                }
            } finally {
                if (isMounted) {
                    setIsLoading(false);
                }
            }
        }
        
        fetchData(dataUrl);
        
        const cleanUp = () => {
            isMounted = false;
            source.cancel();
        }
        
        return cleanUp;
    }, [dataUrl]);
    
    return { data, fetchError, isLoading };
}

export default useAxiosFetch;

```

### React Context:
React context is a better way that is used for props instead of the normal props drilling, this context would be more organized and efficient for larger scale apps:
```jsx
import { createContext, useState, useEffect } from 'react'

// 1. create context:
const DataContext = createContext();

// 2. create data provider component:
const DataProvider = ({ children }) => {
	// here we can put all out app states:
	const [value, setValue] = useState("default");

	return (
		<DataContext.Provider value={{
			 value, setValue 
			 // ....
		 }}>
			{children}
		</DataContext.Provider>
	);
};

export default DataContext;

// in the app component:
import { DataContext } from './context/dataContext';
// in the return section:
<DataProvider>
	<Header />
	<Body />
	<Footer />
</DataProvider>



// now inside any component you can access the states:
import { useContext } from 'react';
import { DataContext } from './context/dataContext';

const Body = () => {
	{ value, setValue } = useContext(DataContext);
	return (
		<h1>Body</h1>
	);
};

export default Body;
```

### Prev State in React:
```jsx
const [count, setCount] = useState(0);

const add = () => {
	setCount(prev => prev + 1);
}
const subtract = () => {
	setCount(prev => prev - 1);
}
```

This is a preferred way that other normal way: `setCount(count + 1)`
We can also use the prev state way in other data types; (arrays, objects... etc)
```jsx
const [arr, setArr] = useState([]);

const addElement = (newElement) => {
	setArr(prev => [...prev, newElement]);
}
```

