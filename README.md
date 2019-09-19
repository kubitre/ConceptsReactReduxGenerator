# Codogenerator for code js

I currently have 2 different approaches for codogenerate jsx and js code
- [Codogenerator for code js](#codogenerator-for-code-js)
- [description of the problem](#description-of-the-problem)
- [Stack technologies:](#stack-technologies)
- [Native Declaration](#native-declaration)
  - [Output Generated of Native way:](#output-generated-of-native-way)
- [Transporter Language](#transporter-language)

# description of the problem
React, a modern front-end developer, spends a lot of time creating basic skeletons of containers and components. I have ideas how this problem developer can resolve. He will be able to use one of the above approaches to describe containers and data for them. both approaches will require a special tool from the developer, the working mechanisms of which will be described later. 
In Native Declaration developer should use yaml syntax for declaring containers, datas, actions. It will be generate new files in you root project dir.

# Stack technologies:
- React
- Redux
- React-Redux
- Redux-Thunk

# Native Declaration

An example of declarative description of components, data used in them and data operations, as well as global storage:
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
            - myfunc1: `func(new_logotype)|CHANGE_LOGOTYPE|{
                return {
                    "logotype": new_logotype
                }
            }`
            - myfunc2: `func()|REMOVE_USER_NAME|{
                return {
                    "user_name": null
                }
            }`
containers:
    Container:
        funcs: 
            - render: `func() {
                store.actions.myfunc1("test")
                return (
                    <div className="container" style_gen={
                        "display": "flex",
                        "flex-direction": "row"
                    }>
                       {store.data.test}
                    </div>
                )
            }`
        stores:
            - store: store.Container

```

Generate this files:
- ./Containers/Container/
  - index.jsx
  - style.scss
- ./Store/Containers/Container/
  - actions.js
  - consts.js
  - reducer.js

And also edit you global reducer combiner
to observe how the output will look, refer to: [output_generate](#output-generated-of-native-way)

## Output Generated of Native way:
Data in Files: 
- ../Containers/Container/index.jsx:
```jsx
import React, {Component} from 'react';
import {connect} 'react-redux';
import {bindActionCreators} from 'redux';

// ACTIONS
import * as ContainerActions from '../../../Store/Containers/MyContainer1/actions.js';

class Container extends Component {
    render = () => {
        this.props.my_container_actions.ACTION_myfunc1("test");
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
        "my_container_actions": bindActionCreators(ContainerActions, dispatch)
    }
}
```

- ../Containers/Container/style.scss:
```css
.container{
    "display": "flex",
    "flex-direction": "row"
}
```

- ../Store/Containers/Container/actions.js:
```js
import {CONST_GEN_CHANGE_LOGOTYPE, CONST_GEN_REMOVE_USER_NAME} from './consts.js';

export function ACTION_myfunc1(new_logotype) {
    return {
        "type": CONST_GEN_CHANGE_LOGOTYPE,
        "payload": {
            "logotype": new_logotype,
        }
    }
}

export function ACTION_myfunc2() {
    return {
        "type": CONST_GEN_REMOVE_USER_NAME,
        "payload": {
            "user_name": null,
        }
    }
}
```
- ../Store/Containers/Container/consts.js:
```js
const prefix = "{{GLOBAL_PREFIX}}_Container_";

export const CONST_GEN_CHANGE_LOGOTYPE = prefix + "CHANGE_LOGOTYPE";
export const CONST_GEN_REMOVE_USER_NAME = prefix + "REMOVE_USER_NAME";

- ../Store/Containers/Container/reducer.js:
import {CONST_GEN_CHANGE_LOGOTYPE, CONST_GEN_REMOVE_USER_NAME} from './consts.js';

const initState = {
    "test": ""
}

export default function {GLOBAL_INTERFACE_NAME}MyContainer1_state(state=initState, action) {
    const {type, payload} = action;

    switch(type){
        case CONST_GEN_CHANGE_LOGOTYPE:
            return {...state, payload}
        case CONST_GEN_REMOVE_USER_NAME:
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
import {{GLOBAL_PREFIX}}_Container_Reducer from '../Containers/Container/reducer.js'


combineReducers({
    ...,
    {{GLOBAL_PREFIX}}_Container_Reducer,
})
```

# Transporter Language

example of a special transporter language:

index.gen_jsx
```bash
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
import {CONST_GEN_const} from './consts.js';

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
import {CONST_GEN_const} from './consts.js';

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