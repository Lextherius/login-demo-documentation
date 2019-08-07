# Designs.ai authentication documentation

Designs.ai will be utilizing [AWS Cognito](https://aws.amazon.com/cognito/) to manage our users identity.
In this example we will be using Reactjs and [Amplify](https://aws.amazon.com/amplify/) the official javascript-sdk for AWS.

By the way this is just the documentation for the authentication, the actual config file and source are in a differrent private repo found [here](https://github.com/Lextherius/login-demo-source).


#### Configuration and Setup
1. Create a React project call login-demo
    ```
    npx create-react-app login-demo
    ```
2. Install the Amplify framework
    ```
    npm install aws-amplify
    ```
3. then add the following code into your index.js
    ```javascript
    // src/index.js
    import Amplify from 'aws-amplify'
    import config from './aws-config'
    Amplify.configure(config)
    ```
4. replace your app.js with the following code
    ```javascript
    // src/App.js
    // import useEffect hook
    import React, { useEffect } from 'react';
    import logo from './logo.svg';
    import './App.css';

    // import Hub
    import { Auth, Hub } from 'aws-amplify'

    function checkUser() {
    Auth.currentAuthenticatedUser()
        .then(user => console.log({ user }))
        .catch(err => console.log(err));
    }

    function signOut() {
    Auth.signOut()
        .then(data => console.log(data))
        .catch(err => console.log(err));
    }

    function App(props) {
    // in useEffect, we create the listener
    useEffect(() => {
        Hub.listen('auth', (data) => {
        const { payload } = data
        console.log('A new auth event has happened: ', data)
        if (payload.event === 'signIn') {
            console.log('a user has signed in!')
        }
        if (payload.event === 'signOut') {
            console.log('a user has signed out!')
        }
        })
    }, [])
    return (
        <div className="App">
        <header className="App-header">
            <img src={logo} className="App-logo" alt="logo" />
            <p>
            Edit <code>src/App.js</code> and save to reload.
            </p>
            <button onClick={() => Auth.federatedSignIn()}>Sign In</button>
            <button onClick={checkUser}>Check User</button>
            <button onClick={signOut}>Sign Out</button>
        </header>
        </div>
    );
    }

    export default App
    ```
5. Copy the provided aws-config.js and paste it into the ``/src`` directory
6. Next, configure a hostname for your this login-demo app, for example ``https://login-demo.loc``
    This step is really important, otherwise the following step might not work correctly.
    Please also take note that you will need to use ssl, it is fine to use a self generated cert for testing purpose but it will need to go through the https nevertheless.
7. Modify the ``/src/aws-config.js`` as needed
    **oauth.redirectSignIn** - must point to the domain that configure in **step #6**
    **oauth.redirectSignOut** - must point to the domain that configure in **step #6**
    **cookieStorage.storage** - must be set to the domain that configure in **step #6**
8. After that please let us know the domain that you have choose to use in **step #6** and we will whitelist that domain.
9. Build the React project
    ```
    npm run build
    ```
10. Finally point the host you configure in **step 6** to the ``/build/`` directory


#### Testing the login-demo app
1. Once you have completed the setup above, now it is time to test it out. Just go to the domain you set up previously (eg. ``https://login-demo.loc``) and you shall be greeted with the following screen.
    ![Appendix 1](/screenshot/appendix_01.PNG?raw=true "Appendix 1")
2. After clicking on the signin button, you shall be redirected to the following login screen
    ![Appendix 2](/screenshot/appendix_02.PNG?raw=true "Appendix 2")
3. Create an account if you don't have an one already, otherwise just login
4. Once login you will be redirected back to the previous screen and Amplify should automatically call the API to retrieve the tokens using the Authorize code returned by the login process.
5. Once done, open up your browser's console, then click on the check user button, if it return the user object then you know the user is authenticated.
    ![Appendix 2](/screenshot/appendix_03.PNG?raw=true "Appendix 3")

#### What's next?
Now that you have successfully authenticate the user, it then up to your system to :
1. store the access and refresh token
2. use this authentication mechanism to tied back with your own user management system to authorize their access (I am sure your system have a way to flag the user as signed in and allow them access to features that only signed in user have access to. If not.... then start building one.)
3. If you have any questions, please feel free to contact me. My name is Alex. If you don't already have my contact, then you are probably not the intended audience of this demo.