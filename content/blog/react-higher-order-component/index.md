---
title: React higher order components
date: "2018-01-01"
description: How to encapsulate state and write pure React components.
---
React’s Higher Order Component brings mixin functionality to the frontend development world.

For a web service to load the content, we used to do those callback hell, and manage those state randomly. Now we have a better solution. With React Higher Order Component, you can mixing the AJAX call functionality on the fly.

Here is a example code:

```js
import React from 'react';

export function withJson(WrappedComponent, url, field, extra = {}){
    return class extends React.Component {
        constructor(props){
            super(props);
            this.state = {
                data: {},
            };

            this.setData = this.setData.bind(this);
        }

        setData(data){
            this.setState({data: data});
        }

        componentDidMount(){
            fetch(url).then(resp => resp.json()).then(this.setData);
        }

        render(){
            let props = Object.assign({}, extra);
            props[field] = this.state.data;
            return <WrappedComponent {...props} />;
        }
    }
};
```

This is a function that can retrieve a json from the server and feed it back into a React Component as a props. 

Now let me give you a example of making a AJAX request on the fly:

```js
render() {
    const WrappedNavHeader = withRouter(NavHeader);
    const WrappedRecentPosts = withJson(RecentPosts, '/blogs.json', 'posts');

    const DataPost = (props) => {
      return (<Post {...props.data} />);
    }
    
    return (
      <Layout>
        <WrappedNavHeader items={header_items} handleSwitch={this.handleMenuClick}/>
        <Content style={{ padding: '0 50px', marginTop: 64, minHeight:'100vh'}}>
          <Route exact path='/' render={() => <WrappedRecentPosts />} />
          <Route path='/post/:id' render={props => {
            let WrappedPost = withJson(DataPost, `/${props.match.params.id}.json`, 'data');
            return (<WrappedPost />);
          }} />
        </Content>
        <Footer style={{ textAlign: 'center' }}>
          Yiping Deng ©2018 All rights reserved.
        </Footer>
      </Layout>
    );
```

As you can see, in the code above, you create a new Component called **WrappedRecentPosts**. This component is different from our original *PureComponent. *We mixed in the AJAX call, which obvious contains some internal states. (This request is fired by fetch API provided by all major browsers, and behind the scene is a Promise which runs asynchronously.
