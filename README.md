# Assignment

1. Folder Structure

Components and pages:

```
/src
  /components
    RecipeForm.js
    RecipeList.js
    RecipeCard.js
    LoginForm.js
    PrivateRoute.js
  /pages
    Home.js
    AddRecipe.js
    EditRecipe.js
    Login.js
  /services
    api.js
  App.js
  index.js
  ...
```

2. Implementing Components

`LoginForm.js` (Component for login form)
```jsx
import React, { useState } from 'react';

const LoginForm = ({ onSubmit }) => {
  const [email, setEmail] = useState('');
  const [password, setPassword] = useState('');

  const handleSubmit = (e) => {
    e.preventDefault();
    onSubmit(email, password);
  };

  return (
    <form onSubmit={handleSubmit}>
      <input type="email" placeholder="Email" value={email} onChange={(e) => setEmail(e.target.value)} />
      <input type="password" placeholder="Password" value={password} onChange={(e) => setPassword(e.target.value)} />
      <button type="submit">Login</button>
    </form>
  );
};

export default LoginForm;
```

`RecipeList.js` (Component for displaying a list of recipes)
```jsx
import React from 'react';

const RecipeList = ({ recipes }) => {
  return (
    <div>
      {recipes.map(recipe => (
        <div key={recipe.id}>
          <h3>{recipe.title}</h3>
          <p>{recipe.description}</p>
          {/* Display more details like ingredients, instructions, etc. */}
        </div>
      ))}
    </div>
  );
};

export default RecipeList;
```

3. Implementing Pages

`Home.js` (Home page after login, displaying user's recipes)
```jsx
import React, { useState, useEffect } from 'react';
import RecipeList from '../components/RecipeList';
import { getRecipes } from '../services/api';

const Home = () => {
  const [recipes, setRecipes] = useState([]);

  useEffect(() => {
    // Fetch recipes from backend API
    const fetchRecipes = async () => {
      try {
        const fetchedRecipes = await getRecipes(); 
        setRecipes(fetchedRecipes);
      } catch (error) {
        console.error('Error fetching recipes:', error);
      }
    };

    fetchRecipes();
  }, []);

  return (
    <div>
      <h2>My Recipes</h2>
      <RecipeList recipes={recipes} />
    </div>
  );
};

export default Home;
```

`AddRecipe.js` (Page for adding a new recipe)
```jsx
import React, { useState } from 'react';
import RecipeForm from '../components/RecipeForm';
import { addRecipe } from '../services/api';

const AddRecipe = () => {
  const handleSubmit = async (formData) => {
    try {
      await addRecipe(formData); 
      alert('Recipe added successfully!');
      // Redirect or update state as needed
    } catch (error) {
      console.error('Error adding recipe:', error);
      alert('Failed to add recipe. Please try again.');
    }
  };

  return (
    <div>
      <h2>Add New Recipe</h2>
      <RecipeForm onSubmit={handleSubmit} />
    </div>
  );
};

export default AddRecipe;
```

5. API Service (services/api.js)

```js
const API_BASE_URL = 'http://localhost:3000/api';

export const login = async (email, password) => {
  // Example login API call
  const response = await fetch(`${API_BASE_URL}/login`, {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
    },
    body: JSON.stringify({ email, password }),
  });

  if (!response.ok) {
    const errorData = await response.json();
    throw new Error(errorData.message || 'Failed to login');
  }

  return await response.json();
};

export const getRecipes = async () => {
  // Example fetch recipes API call
  const response = await fetch(`${API_BASE_URL}/recipes`, {
    method: 'GET',
    headers: {
      'Authorization': `Bearer ${localStorage.getItem('token')}`,
    },
  });

  if (!response.ok) {
    const errorData = await response.json();
    throw new Error(errorData.message || 'Failed to fetch recipes');
  }

  return await response.json();
};

export const addRecipe = async (recipeData) => {
  // Example add recipe API call
  const response = await fetch(`${API_BASE_URL}/recipes`, {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      'Authorization': `Bearer ${localStorage.getItem('token')}`,
    },
    body: JSON.stringify(recipeData),
  });

  if (!response.ok) {
    const errorData = await response.json();
    throw new Error(errorData.message || 'Failed to add recipe');
  }
};
```
