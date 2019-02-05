# structuring-your-react-app

I have been working on React for more than two years and whenever I start developing a React application the very first thing comes in my mind is “how to structure this application”. And there are a lot of opinions by different developers around the world. If you look at the official documentation or if you ask anyone from a React core team or look at the official documentation for this question:
“Is there a recommended way to structure React projects?”

This is what you will get in return.

> React doesn’t have opinions on how you put files into folders. That said there are a few common approaches popular in the ecosystem you may want to consider.

I myself, whenever I was about to start working on a new application from scratch I was very confused about, what structure to follow. And it takes a lot of time to think because for me it’s very important to organize my project because in future if someone else is going to join my team it will be easier for them to understand the code.

After looking at different recommendations and going through several structures, _I finally created my own structure_. And you know what the good thing is? **It’s actually suitable for every kind of application**. Whether you are working on a very large application or just a college project. It will help you in structuring your application in a way that you can actually reuse a lot of your components and logic.

First of all, I want to show you my structure than I will describe why I created this and how your files should look like.

```
├── build
├── public
├── src
│       ├── /configs
│       ├── /actions
│                  ├── user.action.js
│                  ├── company.action.js
│       ├── /assets
│                  ├── /images
│       ├── /components
│                  ├── /Forms
│                           ├── LoginForm.js
│                           ├── SignupForm.js
│                  ├── /Shared
│                           ├── Button.js
│                           ├── Input.js
│                  ├── /NotFound
│                           ├── NotFound.js
│       ├── /containers
│                  ├── Root.js
│                  ├── /Company
│                           ├── Company.index.js
│                           ├── Company.style.scss
│       ├── /services
│                  ├── verb.services.js
│                  ├── user.services.js
│                  ├── company.services.js
│       ├── /reducers
│                  ├── user.reducer.js
│                  ├── company.reducer.js
│                  ├── index.js
│       ├── /store
│                  ├── configureStore.js
│       ├── /styles
│                  ├── _fonts.scss
│                  ├── _colors.scss
│                  ├── styles.scss
│       ├── /types
│       ├── routes.js
│       ├── index.js
│       ├── utils.js
```

So, this is the file structure I have been using and it’s pretty clear and allows me to write reusable components in a pretty awesome manner.

The main folder here is the `src`, it’s the folder where you put all of your components and configurations.

### configs

This is the folder where you put all of your **configurations**. Like if you are using some third party service and they require you to add some sort of `API KEY` in every request so this is the folder where you can add these variables inside a file named `index.js`

### actions

_Actions are pure JavaScript objects_ but I see a lot of people are using them differently, instead of the recommended way. Your actions should only contain a type and a payload(data). What I do is I create a different file for different features inside the action folder. So you can create files like `user.action.js` for actions related to the user and so on. And your action file should only contain the actions and their types only. Like you can see in the Gist below.

```
// action types.
export const actions = {
  SET_USER_INFO: "SET_USER_INFO",
  SET_ALL_USERS: "SET_ALL_USERS",
  SET_CURRENT_USER: "SET_CURRENT_USER",
};

// set's the user to redux.
export const setUser = (data) => ({
  type: actions.SET_USER_INFO,
  data
});

// set's all the users to redux.
export const setAllUsers = (data) => ({
  type: actions.SET_ALL_USERS,
  data
});

// Set's current user in redux.
export const setCurrentUser = (data) => ({
  type: actions.SET_CURRENT_USER,
  data
});
```

### assets

Assets should contain all the assets of your applications like `images`, `icons` etc.

### components

Components directory should contain all of the components. If you are familiar with redux than you probably have a concept of containers and components. _Containers are basically those components who have direct access to the application state and components don’t_.
There are a lot of components that you can put in this directory. But what I do and recommend everyone to do, is to arrange your components according to the category like all of your forms should be in `forms` directory and all of those components which are shared among multiple components of your application like Input, Button, TextArea, etc. They should be placed inside a `shared` directory.

### containers

Containers are those components who have direct access to the state of the application. So if you are accessing your application state directory in your component then that component should be placed inside a containers directory. _Usually, I create containers for features_. Like, if I am creating a view where I am going to display all of the companies then I create a folder inside containers directory with name `Company` which contains two files first is called the `company.index.js` which contains all of my JSX and JS logic and the second one is called `company.style.scss` where I put all the styles related to this view. I use SASS in most of my projects but if you are using CSS you can just name it `company.style.css` .

### services

This directory contains all of my network requests. I usually create one file named `verbs.services.js` where I create requests like `getRequest` which takes the URL and returns a promise. In this way, you don’t have to set headers again and again and it also reduces the number of lines you have to code. Other files are depending on your features. Like for company you can create a service file named `company.services.js` which will contain all the services for company feature, as you can see in the following Gist.

```

import { openNotificationWithIcon } from 'utils/notification';
import api from 'utils/api';
import { setAllCompanies } from '../actions/company.action';

export const getAllCompanies = (resolve, reject) => {
  return (dispatch) => {
    getRequest(`${api.url}company`)
      .then(({ data }) => {
        dispatch(setAllCompanies(data));
        resolve();
      }).catch((error) => {
        openNotificationWithIcon('error', 'Error!', error);
        reject();
      });
  };
};

export const createCompany = (data, dataSource, resolve, reject) => {
  return (dispatch) => {
    postRequest(`${api.url}company/`,data)
      .then(({ data }) => {
        const company = data;
        const newSource = [...dataSource];
        newSource.push(company);

        dispatch(setAllCompanies(newSource));
        openNotificationWithIcon('success', 'Success!', `Company created successfully.`);

        resolve();

      }).catch((error) => {
        openNotificationWithIcon('error', 'Error!', error);
        reject();
      });
  };
};
```

### reducers

This directory should contain all of your reducers. According to the official docs of redux. “_The **reducer** is a pure function that takes the previous state and action, and returns the next state.”_ but I see a lot of people they perform a lot of calculations inside this function and write a lot of code instead of just taking the action and returning the new state. They perform a lot of calculations which I don’t like. What I do is I perform all the calculations inside the services and make the reducers as clean as possible. You can create reducers according to the features like `company.reducer.js` . This directory also contains an `index.js` file where you basically combine all the reducers. Following Gists will illustrate more about this.

```
import { actions } from 'actions/company.action';

// Initial State
const INITIAL_STATE = {
  allCompanies: null,
  currentCompany: null
};

// Company Reducer.
export default (state = INITIAL_STATE, { type, data }) => {
  switch (type) {
    case actions.SET_ALL_COMPANIES:
      return { ...state, allCompanies: data };
    case actions.SET_CURRENT_COMPANY:
      return { ...state, currentCompany: data };
    default:
      return state;
  }
}
```

### store

This directory contains your store configurations. I usually create a file named `configureStore.js` inside this directory and create configurations inside this file.

styles
This directory contains all the styles which are used in different style files. If you are familiar with SASS then you can actually create mixins, placeholder, and variables inside this directory and you can name them like `_mixins.scss`, `_colors.scss`, etc.

### types

This directory contains your flow types. You can create files like `base.types.js` and `redux.types.js` inside this directory which will contain type for specific parts of your applications.

### routes.js

This file contains all the routes of your application. As you can see in the following Gist.

```
import Dashboard from './Dashboard/Dashboard.index';
import Users from './Users/Users.index';
import Company from './Company/Company.index';
import Profile from './Profile/Profile.index';

// router configuration.
let routes = [
  {
    path: '/dashboard',
    name: 'Project Dashboard',
    component: Dashboard,
    exact: true,
    strict: true
  },
  {
    path: '/dashboard/users',
    name: 'Users',
    component: Users,
    exact: true,
    strict: true
  },
  {
    path: '/dashboard/company',
    name: 'Company',
    component: Company,
    exact: true,
    strict: true
  },
  {
    path: '/dashboard/profile',
    name: 'Profile',
    component: Profile,
    exact: true,
    strict: true
  }
];

export default routes;
```

### utils.js

This file contains all the functions which are shared across different features, services, and components of your application.

## Conclusion

This is the file structure I have been using for developing different React applications and it’s very helpful for me and it allows me to code in a cleaner way. _Again this is just my opinion, I am not saying that this the best way and there isn’t any other way of doing it_. It’s just my opinion and I developed this structure after reviewing different structures people are using for React. I spent a lot of time creating this structure and it helped me a lot in so many projects.
