---
layout: post
title: "Getting state from multiple reducers with redux"
modified:
categories: blog
excerpt: using mapStateToProps to use multiple reducers
tags: [react, react-redux]
date: 2016-11-25T15:39:55-04:00
---

As your app gets bigger, your actions and reducers files are going to get longer and longer until there's a giant list of constants, and the initial state is difficult to reason about. The bigger your initial state, the more likely it is that you're going to have duplicated functionality, especially with a team of developers. So you think to yourself, "Self, you're so handsome/pretty. And I should break these actions out into several grouped files. But how should I do this?" 

Your first thought is going to be to break them out by page of the application that you're building. Resist this temptation, because it's going to create much more work for you later. If the action creators and their corresponding reducers are grouped by page, that are going to be difficult to pull apart later. There's a bunch of loosely related actions and state, and as the product requirements grow, you'll just add them to the end of the file. 

But what happens if the stakeholders want to move some widget to another page because it makes more sense to the end user? Then you have to hunt all through the files (and many display components) and update everything piecemeal. Or (more likely) you'll just never have time for a refactor because of deadlines and the files will get all lopsided as different pages of the app have more features developed than others. 

Take a minute to think about your application, and break it up by domain. Group the actions and state by looking at how you've set up your data on the backend. 

Let's say you're building a movie database where users can comment about how great/awful the movies are. At first you'd think there should be an `Account` state for the user's account screen, and a `Forum` actions file that controls loading of comments for each movie page. Instead, think about the entities at play here. `User`, `Movie`, `Comment`. This is much more flexible in the long run, and much easier on which to test and bring another developer up to speed.

so now your file structure might look something like this:

```
├── users/
│   ├── UserReducer.js
│   ├── UserActions.js
│   ├── UserProfile.js
├── movies/
│   ├── MovieReducer.js
│   ├── MovieActions.js
├── comments
│   ├── CommentReducer.js
│   ├── CommentActions.js
├── mainReducer.js
├── store.js
```

So according to the [redux docs](http://redux.js.org/docs/recipes/reducers/UsingCombineReducers.html) we `combineReducers` in `mainReducer.js` like so:

```javascript
import { combineReducers } from 'redux';
import users from './users/UserReducer';
import movies from './movies/MovieReducer';
import comments from './comments/CommentReducer';

export default combineReducers({
  users,
  movies,
  comments
});
```

So assuming you have ES6 transpiling for your React apps, say you had a component that showed a user's profile and listed some comments that the user had made recently.

```javascript
import React, {PropTypes} from 'react';
import {connect} from 'react-redux';
import {fetchComments} from './UserActions';

class UserProfile extends React.Component {
  static propTypes = {
    userId: PropTypes.string.isRequired
  }
  
  componentWillMount() {
    this.props.dispatch(fetchComments(this.props.userId));
  }

  render() {
    // any properties from the UserReducer state will be available on this.props.users
    // and comment state will be on this.props.comments
    return (
      <div>
        <p>User Name: {this.props.user.username}</p>
        <ul>
          {this.props.comments.userComments.map(comment => {
            return <li>{comment.body}</li>
          })}
        </ul>
      </div>
    );
  }
}

const mapStateToProps = state => {
  return {
    users: state.users,
    coments: state.comments
  }
}

export default connect(mapStateToProps)(UserProfile);

```

or, if you're using the ES7 decorator syntax:

```javascript
@connect(({users, comments}) => ({users, comments}))
export default class UserProfile extends React.Component {
  ...
```

So it's fairly straightforward to break up your reducers into smaller files. It also makes it quite easy to see what parts of the application state you're touching since they're all referenced in one place in your `mapStateToProps` function.
