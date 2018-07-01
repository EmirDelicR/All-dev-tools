# AllDevTools
Dev-tools and best practice

# SSH

How SSH work

To connect to extrnal server/host ssh {user}@{host}

```code
ssh root@167.99.146.57
// IP address or host name
```

####Symmetrical Encryption

Uses one secret key for encryption and decryption.

####Asymmetrical Encryption

Uses two separate keys for encryption and decryption (Public and private).

[Secret Key Exchange (Diffie-Hellman)](https://www.youtube.com/watch?v=NmM9HA2MQGI)

[Diffie Hellman -the Mathematics bit](https://www.youtube.com/watch?v=Yjrfm_oRO0w)

[Key Exchange Problems](https://www.youtube.com/watch?v=vsXMMT2CqqE&t=)

[Elliptic Curves](https://www.youtube.com/watch?v=NF1pwjL9-DE)


#### SSH functions

```code
The SSH command consists of 3 distinct parts:
	ssh {user}@{host}
	// {user} represents the account you want to access.
	// {host} refers to the computer you want to access.
Commands :
	ssh <virtualMaschine>         	--> login to virtual machine
	ssh emir@virtualmachine.com
	exit 			      	--> exit / logout

	// upload app on server
	rsync -av . emir@virtualmachine.com:~/newapp    -> ~ is home directory
	
	// Password list of ssh
	
	// Local machine
	cd .ssh
	ssh-keygen -C "emir@test.com"
	// OR
	ssh-keygen -t rsa -b 4096 -C "emir@test.com"
	// you get id_rsa and id_rsa.pub
	// id_rsa is private key
	// id_rsa.pub is public key

	cat id_rsa.pub | pbcopy
	// this copy key to clipboard
        // if throwe error use 
        sudo apt-get install -y xclip
        sudo vim ~/.zshrc
        //Set alias
        //alias pbcopy='xclip -selection clipboard'
        //alias pbpaste='xclip -selection clipboard -o'
        // stil have problem use:
        xclip -selection clipboard id_rsa.pub
        ssh-add 

	// Remote machine 
	// make on youre machin ssh file
	mkdir .ssh
	cd .ssh
	nano authirused_keys
	// paste content that you copy and press ctrl+X , Y , Enter

	// Set promisions
	cd ..
	chmod 700 .ssh/
	chmod 600 .ssh/*

	// backup config file
	sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config.bak
	
	// change config setup
	sudo nano /etc/ssh/sshd_config

	// in nano find 
	#PasswordAuthentication yes 
	// and set to
	PasswordAuthentication no

	// restart server
        sudo service ssh restart	
```

# PERFORMANCE

#### Network 

1. Minimize text (JS,CSS,HTML) - Use Webpack or UglifyJS
2. Minimize Images

###### Images optimizations

Reduce PNG with TinyPNG
Reduce JPEG with JPEG-OPTIMIZER
Use CDNs like imgix - www.imgix.com
Remove image metadata - www.verexif.com

[Media Queries](https://css-tricks.com/snippets/css/media-queries-for-standard-devices/)

[Media Queries Cheat Sheet](http://www.bsidestudios.com/blog/media-queries-common-sizes-cheat-sheet)

###### Critical Render Path

[Prefetching](https://css-tricks.com/prefetching-preloading-prebrowsing/)

## React

[Components](https://mdbootstrap.com/react/components/breadcrumb/)

```code
sudo npm install create-react-app -g
create-react-app <name>

Inside that directory, you can run several commands:

  npm start
    Starts the development server.

  npm run build
    Bundles the app into static files for production.

  npm test
    Starts the test runner.

  npm run eject
    Removes this tool and copies build dependencies, configuration files
    and scripts into the app directory. If you do this, you can’t go back!
```

```javascript
// stateless component:

import React from 'react';
import Logo from '../../assets/images/logo.png';
import classes from './Logo.css';

const logo = ( props ) => (
    <div className={classes.Logo}>
        <img src={Logo} alt="Logo" />
    </div>
);

export default logo; 

<----------------------------------------->
// Components with state
import React, { Component } from 'react';
import classes from "./style.css";

class ClassName extends Component {
state = {
    purchasing: false,
}

componentDidMount() {
    this.props.onInitIngredients();
}

render () {
    return (
        <Aux>
            <Modal show={this.state.purchasing} modalClosed={this.purchaseCancleHandler} >
            </Modal>
        </Aux>
	);
}
};

export default ClassName;

<------------------------------------------------->
// Using map function
import React from 'react';
import Card from '../Card/Card';
import { robots } from '../../../data/robots';

const CardList = () => {
    const cardsArray = robots.map((robot) => {
        return (
			<Card 
				key={robot.id} 
				id={robot.id} 
				name={robot.name} 
				email={robot.email} />
		)
    });
    return (
        <div>
            {cardsArray}
        </div>
    )
};


export default CardList;

```

#### Error Boundry

```javascript
import React, { Component } from 'react';

class ErrorBoundry extends Component {
  state = {
    hasError: false
  }

  componentDidCatch(error, info) {
      this.setState({hasError: true})
  }
  render() {
    if(this.state.hasError) {
        return <h1>Upps. Error occured.</h1>
    }
    return this.props.children

  }
}

export default ErrorBoundry;
```

#### Redux

-Good for managing large state.
-Useful for sharing data between containers
-Predictable state management using the 3 principles:
	1. Single source of truth - one single big object
	2. State is read only 
	3. Changes using pure functions

Flux pattern

Action -> Dispatcher -> Store -> View

```console
npm install --save redux
npm install --save react-redux
```

###### Redux Middleware

```javascript
// index.js
import { createStore, applyMiddleware } from 'redux';

// middleware
const logger = store => {
    return next => {
        return action => {
            console.log('[Middleware] Dispatching: ', action);
            const result = next(action);
            console.log('[Middleware] next state: ', store.getState());
            return result;
        }
    }
};

// Store
const store = createStore(robotsReducer, applyMiddleware(logger));
```

###### Async Actions

```console
npm install --save redux-thunk
```

```javascript
// Library for ansyc code
// index.js
import thunk from 'redux-thunk';

const store = createStore(rootReducer, composeEnhancers(applyMiddleware(logger, thunk)));

actions.js

export const requestRobots = () => dispatch => {
    dispatch({ type: actionTypes.REQUEST_ROBOTS_PENDING });
    fetch('https://jsonplaceholder.typicode.com/users')
    .then(response => response.json())
    .then(data => dispatch({ type: actionTypes.REQUEST_ROBOTS_SUCCESS, payload: data}))
    .catch(err => dispatch({ type: actionTypes.REQUEST_ROBOTS_FAILED, payload: err }));
};

//reducers.js
export const requestRobots = (state = initialStateRobots, action) => {
    switch (action.type) {
        case actionTypes.REQUEST_ROBOTS_PENDING:
            return updateObject(state, { isPending: true });
        case actionTypes.REQUEST_ROBOTS_SUCCESS:
            return updateObject(state, { robots: action.payload, isPending: false });
        case actionTypes.REQUEST_ROBOTS_FAILED:
            return updateObject(state, { error: action.payload, isPending: false });
        default:
            return state;
    }
};

// Use combine reducers
// index.js
import { searchRobots, requestRobots } from './store/reducers/reducers';
// Combine reducers
const rootReducer = combineReducers({
    search: searchRobots,
    request: requestRobots
});

// Store
const store = createStore(rootReducer, applyMiddleware(thunk, logger));

// in App.js
componentDidMount() {
  this.props.onRequestRobots()
}

const mapStateToProps = state => {
  return {
    searchField: state.search.searchField,
    robots: state.request.robots,
    isPending: state.request.isPending,
    errro: state.request.error
  }
};

const mapDispatchToProps = dispatch => {
  return {
     onSearchChange: (event) => dispatch(actions.setSearchField(event.target.value)),
     onRequestRobots: () => dispatch(actions.requestRobots())
  };
};
```

###### React Tools

1. [React Router](https://reacttraining.com/react-router/web/example/basic)
2. [Ramda](https://ramdajs.com/docs/) 
3. [Lodash](https://lodash.com/docs/)
4. [Gatsby.js](https://www.gatsbyjs.org/)
5. [Material-ui](https://material-ui.com/getting-started/supported-components/)
6. [Semantic-ui](https://semantic-ui.com/introduction/getting-started.html)
7. [React-reselect](https://github.com/reduxjs/reselect)
8. [Redux Saga](https://github.com/redux-saga/redux-saga)

###### Webpack 4 

[webpack](https://webpack.js.org/)

[eslint](https://eslint.org/docs/user-guide/configuring#configuration-file-formats)

[Webpack config tool](https://webpack.jakoblind.no/)
```console
npm install --save-dev webpack webpack-dev-server webpack-cli
npm install --save-dev babel-core babel-loader babel-preset-env babel-preset-stage-2 babel-preset-react
npm install react react-dom
npm install --save-dev eslint
npm install --save-dev babel-eslint
npm install --save-dev eslint-config-airbnb eslint-plugin-import eslint-plugin-jsx-a11y
```

```javascript
// in package.json
"script": {
    "start": "webpack-dev-server --config ./webpack.config.js --mode development"
}
"babel": {
    "presets": {
        "env",
        "stage-2",
        "react"
    }
}
// in webpack.config.js
module.export = {
    entry: [
        './src/index.js'
    ],
    output: {
        path: __dirname + '/dist',
        publicPath: '/',
        filename: 'bundel.js'
    },
    devServer: {
        contentBase: './dist'
    },
    module: {
        rules: [
        {
            test: /\.(js|jsx)$/,
            exclude: /node_modules/,
            use: ['babel-loader']
        },
        {
            test: /\.(.js|.jsx)$/,
            exclude: /node_modules/,
            use: ['eslint-loader']
        }
        ]
    },
    resolve: {
        extensions: ['.js', '.jsx']
    }
}

// in .eslintrc
{
    parser: "babel-eslint",
   "rules": {
       "no-console": "error"
   },
   "extends": ["airbnb-base"] 
}
```
```console
npm start
```

