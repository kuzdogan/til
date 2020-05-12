# Context API

There are many cases when one needs to pass down a props deep down several components. Such as a `user` object `App.js --> Overview --> Page --> Component1 --> Component2` where we check the field `user._id`. 

Instead of passing down the variable it is possible to create a "Context" where the variable is centrally maintained. It's similar to Redux (They say but I have no experience with Redux yet).

First we need to create a Context in a seperate module: 

Context.js
```javascript
import React from 'react';

const Context = React.createContext(); // createContext takes initial value as optional argument 

export default Context;
```

Our user object will reside in `context.user`.

Then we assign the context a value in the topmost component. For example in App.js
```javascript
<Context.Provider value={{ user: this.state.user }}>
  <ChildComponent/>
</Context.Provider>
```

Notice the object notation. Then we can access the variable as such:

### Class Components

Component.js
```javascript
import React from 'react';
import Context from '../Context.js';

class Component extends React.Component {
  constructor(props){
    super(props);
  }

  static contextType = Context; // Access user Context

  render(){
    return(
      <div> User id is: {this.context.user._id} </div>
    )
  }
}
```

### Functional Components with useContext Hook
```javascript
import React, { useContext, useEffect, useState } from 'react';
import Context from '../Context.js';

const Component = (props) => {
  const { user } = useContext(Context);

  return (
    <div> User id is: {user._id} </div>
  )
}
```