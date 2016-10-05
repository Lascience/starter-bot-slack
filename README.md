# Start coding your bot with Slack

This Starter Kit will help you start coding a bot connected to Slack with Recast.AI.

## Requirements

* Create an account on Recast.AI
* Create an account on Slack
* Set up your Recast.AI account

## Get your Recast Bot Token

* Log in to your Recast.AI account
* Create your Bot
* Create intents and fill them with some expressions
* Build your tree on BotBuilder in the 'Build' tab
* In the tab menu, click on the the little screw
* Here is the `request access token` you will need to configure your bot!

## Get your Slack bot Token

* Go to https://YOUR_ORGANISATION.slack.com/services/new/bot (replace it with the name of your team)
* Create a new bot and follow the procedure
* That's it, you can now get the code located in the API Token field!

## Launch the Bot

#### Complete the config.js

* Clone this Repository

```
git clone https://github.com/RecastAI/bot-slack.git
```

* Fill the config.js with your Tokens

```javascript
const config =
{
	recast: {
		token: 'RECAST TOKEN',
		language: 'en',
	},
	slack: {
		token: 'SLACK TOKEN',
	}
	port: 8080,
}
```

#### Run

* install the dependencies

```
npm install
```

* run your bot

```
npm run start
```

## Go further

Here is the heart of your bot, this function is called everytime your bot receive a message.
'res' is full of precious informations:

* use res.get('nameOfEntity') to extract an entity like a mail, a datetime etc...
* use res.action() to get the current action according to your botbuilder schema
* in action, you can find a done boolean to know if this action can be done according to the requirements (ex: signin needs login)
* use res.reply() to get the reply you've set for this action
* use res.replies() to get an array containing the reply set for the action && the following one if the next action can be done
* use res.nextActions() to get an array of all the following actions

```javascript
rtm.on(slackEvent.MESSAGE, (message) => {
  const user = rtm.dataStore.getUserById(message.user)
  const dm = rtm.dataStore.getDMByName(user.name).id

  recastClient.converse(message.text, { language: config.recast.language, converseToken: message.user })
  .then((res) => {
		const action = res.action
		const replies = res.replies

		if (!action) {
      console.log(`No action`)
      rtm.sendMessage('I didn\'t understand... Sorry :(', dm)
      return
    }

		console.log(`The action of this message is: ${action.slug}`)

		if (replies[0]) {
			console.log('current action has a reply')
			rtm.sendMessage(replies[0], dm)
		}

    if (action.done && replies[1]) {
			console.log('Action is done && next action has a reply')
			rtm.sendMessage(replies[1], dm)
    }
  })
  .catch(() => {
    rtm.sendMessage("I'm getting tired, let's talk later", dm)
  })
})
```

## Author

Hugo Cherchi - Recast.AI hugo.cherchi@recast.ai

You can follow us on Twitter at @recastai for updates and releases.
