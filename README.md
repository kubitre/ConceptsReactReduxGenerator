# Codogenerator for code js

In nowday i have 2 various concepts for codogenerate jsx and js code:
1. [Generate by native declaration]()
2. [Generate by trans-lang](#transpilational-language)

# Stack technologies:
- React
- Redux
- React-Redux
- Redux-Thunk

# Native Declaration

Example of Native declaration:
```yaml
store:
    Container:
        data: 
            - logotype: ""
            - user_name: "",
            - test: ""
        const:
            - CHANGE_LOGOTYPE
            - REMOVE_USER_NAME
        acttions:
            - myfunc1: func(new_logotype)|CHANGE_LOGOTYPE|{
                return {
                    "logotype": new_logotype
                }
            }
            - myfunc2: func()|REMOVE_USER_NAME|{
                return {
                    "user_name": null
                }
            }
        reducer: "implement"

containers:
    Container:
        funcs: 
            - render: 
            - updateData: 
        stores:
            - store: store.Container

components:
    Component:
        data: 
            - logotype: ""
            - user_name: "",
            - test: ""
        actions:
            - myFunc1: func() {
                console.log(logotype)
            }

```

# Transpilational Language

Example:

index.gen_jsx
```js
container MyContainer1 extends class.Container {
    funcCalc1 = (a, b) => {
        return a + b
    }

    render = () => {
        funcCalc1()
        store.actionSetup1()
        return {
            <div className="container">
                {store.data.test1}
            </div>
        }
    }

    store {
        data {
            "test": ""
        }
        consts {
            "const": "1"
        }
        actions {
            "actionSetup1": func() |myConst1| {
                return {
                    "test": "init"
                }
            },
            "actionSetup2": func(data1) |myConst1| {
                return {
                    "test": data1
                }
            }
        }
    }
}
```
After use generator, you should get this files:
```
../containers/MyContainer1:
- index.jsx
- style.scss
../store/containers/MyContainer1:
- actions.js
- consts.js
- reducer.js
```

Data in Files: 
- ../Containers/MyContainer1/index.jsx:
```jsx
import React, {Component} from 'react';
import {connect} 'react-redux';
import {bindActionCreators} from 'redux';

// ACTIONS
import * as MyContainer1Actions from '../../../Store/Containers/MyContainer1/actions.js';

class MyContainer1 extends Component {
    funcCalc1 = (a, b) => {
        return a + b
    }
    render = () => {
        funcCalc1()
        this.props.my_container_actions.ACTION_actionsSetup1();
        return (
            <div className="container" style_gen={
                "display": "flex",
                "flex-direction": "row"
            }>
                {this.props.test}
            </div>
        )
    }
}

function mapStateToProps(state) {
    return {
        "test": state.{{GLOBAL_PREFIX}}MyContainer1_state.test
    }
}

function mapDispatchToProps(dispatch) {
    return {
        "my_container_actions": bindActionCreators(MyContainer1Actions, dispatch)
    }
}
```

- ../Containers/MyContainer1/style.scss:
```css
.container{
    "display": "flex",
    "flex-direction": "row"
}
```

- ../Store/Containers/MyContainer1/actions.js:
```js
export function ACTION_actionsSetup1() {
    return {
        "type": CONST_GEN_const,
        "payload": {
            "test": "init"
        }
    }
}

export function ACTION_actionSetup2(data1) {
    return {
        "type": CONST_GEN_const,
        "payload": {
            "test": data1
        }
    }
}
```
- ../Store/Containers/MyContainer1/consts.js:
```js
const prefix = "{{GLOBAL_PREFIX}}_MyContainer1_";

export const CONST_GEN_const = prefix + "1";

- ../Store/Containers/MyContainer1/reducer.js:

const initState = {
    "test": ""
}

export default function {GLOBAL_INTERFACE_NAME}MyContainer1_state(state=initState, action) {
    const {type, payload} = action;

    switch(type){
        case CONST_GEN_const:
            return {...state, payload}
        default:
            return state;
    }
}
```
also inserted in your global reducer combiner
- ../Store/Reducer/index.js
This code:
```js
import combineReducers from 'redux';
...
// MyContainer1 reducer entry
import {{GLOBAL_PREFIX}}_MyContainer1_Reducer from '../Containers/MyContainer1/reducer.js'


combineReducers({
    ...,
    {{GLOBAL_PREFIX}}_MyContainer1_Reducer,
})
```