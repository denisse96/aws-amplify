# Taller AWS - AMPLIFY WIT 

This project was bootstrapped with [Create React App](https://github.com/facebook/create-react-app).

## 1. Clone this repository 


By Running 

### `git clone git@github.com:denisse96/aws-amplify-workshop.git`

Check if everything is working by running

Change Directory 
` cd aws-amplify-workshop`
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
                <h2>Amplify Workshop</h2>
            </header>
        </div>
    );
}

export default withAuthenticator(App);

```


## 8. Create a GraphQL API 
### `amplify add api`
```
Select GraphQL 
Provide API name : songAPI 
Default Authorization : Amazon Cognito Pool 
Annotated GraphQL schema : No
Choose a template : Single Object
Edit Schema: Yes 
```

Replace the schema with the following data 
```
type Song @model {
	id: ID!
	title: String!
	description: String!
	filePath: String!
	owner: String!
}
```


## 9. Edit App.js
```
import React from "react";
import "./App.css";
import Amplify, { API, graphqlOperation, Storage } from "aws-amplify";
import awsconfig from "./aws-exports";
import { AmplifySignOut, withAuthenticator } from "@aws-amplify/ui-react";
import { listSongs } from "./graphql/queries";
import { updateSong } from "./graphql/mutations";

import { useState } from "react";
import { useEffect } from "react";
import ReactPlayer from "react-player";

import { Paper, IconButton } from "@material-ui/core";
import PlayArrowIcon from "@material-ui/icons/PlayArrow";
import FavoriteIcon from "@material-ui/icons/Favorite";
import PauseIcon from "@material-ui/icons/Pause";

Amplify.configure(awsconfig);

function App() {
	const [songs, setSongs] = useState([]);
	const [songPlaying, setSongPlaying] = useState("");
	const [audioURL, setAudioURL] = useState("");

	useEffect(() => {
		fetchSongs();
	}, []);

	const toggleSong = async (idx) => {
		if (songPlaying === idx) {
			setSongPlaying("");
			return;
		}

		const songFilePath = songs[idx].filePath;
		try {
			const fileAccessURL = await Storage.get(songFilePath, { expires: 60 });
			console.log("access url", fileAccessURL);
			setSongPlaying(idx);
			setAudioURL(fileAccessURL);
			return;
		} catch (error) {
			console.error("error accessing the file from s3", error);
			setAudioURL("");
			setSongPlaying("");
		}
	};

	const fetchSongs = async () => {
		try {
			const songData = await API.graphql(graphqlOperation(listSongs));
			const songList = songData.data.listSongs.items;
			console.log("song list", songList);
			setSongs(songList);
		} catch (error) {
			console.log("error on fetching songs", error);
		}
	};

	return (
		<div className="App">
			<header className="App-header">
				<AmplifySignOut />
				<h2>My App Content</h2>
			</header>
			<div className="songList">
				{songs.map((song, idx) => {
					return (
						<Paper variant="outlined" elevation={2} key={`song${idx}`}>
							<div className="songCard">
								<IconButton aria-label="play" onClick={() => toggleSong(idx)}>
									{songPlaying === idx ? <PauseIcon /> : <PlayArrowIcon />}
								</IconButton>
								<div>
									<div className="songTitle">{song.title}</div>
									<div className="songOwner">{song.owner}</div>
								</div>

								<div className="songDescription">{song.description}</div>
							</div>
							{songPlaying === idx ? (
								<div className="ourAudioPlayer">
									<ReactPlayer
										url={audioURL}
										controls
										playing
										height="50px"
										onPause={() => toggleSong(idx)}
									/>
								</div>
							) : null}
						</Paper>
					);
				})}
			</div>
		</div>
	);
}

export default withAuthenticator(App);
```
## 10. Add Missing libaries
### `npm i --save @material-ui/core @material-ui/icons`
### `npm i  ‘react-player’`

## 11. Modify the css
```
.App {
	text-align: center;
}

.App-logo {
	height: 10vmin;
	pointer-events: none;
}

.App-header {
	background-color: #282c34;
	min-height: 5vh;
	display: flex;
	align-items: center;
	justify-content: space-around;
	font-size: calc(10px + 2vmin);
	color: white;
}

.App-link {
	color: #61dafb;
}

.songList {
	display: flex;
	flex-direction: column;
}

.songCard {
	display: flex;
	justify-content: space-around;
	padding: 5px;
}

.songTitle {
	font-weight: bold;
}

.ourAudioPlayer {
	display: flex;
	justify-content: center;
}

```
## 11. Manually add a record to dynamoDB 

Go to dynamo db
create a record be sure to add 
createdAt
updatedAt 
To create this values use ``` new Date().toISOString()``` in the console



## 12. Add storage
### `Amplify add storage`
### `Amplify push`
Go to s3
Create a public directory 
Upload a song 
Modify the file path in dynamo 



## 13. Delete resources
###  `Amplify delete`


