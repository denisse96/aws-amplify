# Taller AWS - AMPLIFY WIT 

This project was bootstrapped with [Create React App](https://github.com/facebook/create-react-app).

## 1. Clone this repository 


By Running 

### `git clone git@github.com:denisse96/aws-amplify.git`

Check if everything is working by running

### `npm start`
Open [http://localhost:3000](http://localhost:3000) to view it in the browser.

## 2. Download de AWS Amplify CLI 

### `npm install -g @aws-amplify/cli`

This allows you to push and configure your amplify settings. 
Make sure you are logged in in your aws account. 

## 3. Configure  amplify

### `amplify configure`

Create the IAM Role we need to make our project. 

## 4. Initialize the project

### `amplify init`

Select all the default options. 
When asked: `Please choose the profile you want to use` be sure to selected the IAM role we previously created. 
Check your console and check the resources it has created. 

## 5. Add auth to your project 

### `amplify add auth`

Select all the default options. 

## 6. Push your changes 

### `amplify push`

## 7. Install UI Amplify components 

### `npm install --save aws-amplify @aws-amplify/ui-react`


Modify your App.js with the following code 
```
import Amplify from 'aws-amplify';
import awsconfig from './aws-exports';
import { AmplifySignOut, withAuthenticator } from '@aws-amplify/ui-react';

Amplify.configure(awsconfig);

function App() {
    return (
        <div className="App">
            <header className="App-header">
                <AmplifySignOut />
                <h2>My App Content</h2>
            </header>
        </div>
    );
}

export default withAuthenticator(App);

```



