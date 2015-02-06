#tanks-for-all-the-fish

![Tanks for all the fish](./art/Logo.png)

This is a tank simulation game but also maybe it controls tanks in real life
like in wargames?

Write an AI, compete against other AIs, and battle for glorious victory!


## Installation
```bash
npm i
npm start
npm i -g webpack
webpack -w
```

visit [http://localhost:3000](http://localhost:3000)


## Writing an AI

A tank AI is a function that takes in the state of the world and returns an
object describing how you want your tank to act.

### The Game State
The game state is an object describing the position, velocity and rotation of
all tanks and bullets in the game. You can use this information to dodge
bullets, chase specific tanks, try to kill the tank with the lowest health, or
whatever else you want! The game state is an object that looks like this:

```JavaScript
{
  width: 800, // width of the arena
  height: 800, // height of the arena
  tanks: [ // array of all tanks in the game, including your own
    {
      x: 100, // x position. left is 0, increases as you go right
      y: 100, // y position. top is 0, increases as you go down
      velocity: 1, // between -1 and 1. tanks can go backwards too!
      rotation: 3.14, // between 0 and 2 * Math.PI. In radians. 0 is facing right.
      health: 3, // tanks start with 3, and die if they reach 0.
                 // each hit by a bullet removes 1 health.
      timeUntilShoot: 3000 // time in milliseconds until the tank can shoot again
      name: 'Yolo Swaggins' // tank name, so you can tell whose it is!
    }
  ],
  bullets: [ // an array of all bullets in the game
    {
      x: 100,
      y: 100,
      velocity: 20, // bullets are always the same velocity
      rotation: 1.56 // in radians, follows the unit circle
    }
  ]
}
```

### The AI Result
Your AI function should return an object that looks like this:

```JavaScript
{
  velocity: 1, // a number between -1 and 1 describing how fast your tank moves each tick
  angularVelocity: 0.3, // a number between -1 and 1 describing how fast your tank
                        // rotates. positive is clockwise, negative is
                        // counterclockwise.
  shoot: true // a boolean saying if your tank should shoot this tick or not.
              // note: you will only be able to shoot if your timeUntilShoot is 0
              // returning true when you can't shoot won't do anything
}
```

### An Example Function

Here is an example AI function. It just slowly turns clockwise and shoots as
often as possible, ignoring all state.

```JavaScript

function ai(worldState) {
  return {shoot: true, velocity: 0.5, angularVelocity: 0.2};
}

ai.aiName = 'FISHY BUSINESS';
module.exports = ai;
```
### Local Development

- To add a tank AI to your game, save it into the `./ais` folder.
- To temporarily exclude an AI that is in your `./ais` folder, add "disabled" to its name like so
 - my-cool-ai.disabled.js
- To create AI clones for testing, add a number to its name like so
 - my-cool-ai.5.js.


## A RAD EXAMPLE

```JavaScript
memory = {
		turn : 0,
		targetIndex : -1,
		targetpos : {x:0,y:0},
};

var funky = function (state) {

	var self = this;	
	
	var m = memory;
	
	m.turn++;
	//console.log(m.turn);
		
	//	utils
	function isMe(t)
	{
		if (typeof(t) === 'undefined')
		{
			console.log("what?");
		}
		if (t.x === self.x && t.y === self.y)
			return true;
		else
			return false;
	}
	function pickTarget()
	{
		var pickIndex = 0;
		for (var i = 0; i < state.tanks.length; i++)
		{
			if (isMe(state.tanks[i]))
				continue;
			return i;
		}
		return -1;
	}
	function updateTargetIndex(targetIndex)
	{
		if (targetIndex < 0 || targetIndex >= state.tanks.length)
			return pickTarget();
		if (isMe(state.tanks[targetIndex]))
			return pickTarget();
		return targetIndex;	//	keep it
	}
	function updateTargetTracking(targetIndex)
	{
		var t = state.tanks[targetIndex];
		m.targetpos = {x:t.x, y:t.y};
	}
	
	//	logic
	//	defaults
	var avel = 1;
	var lvel = 1;
	
	m.targetIndex = updateTargetIndex(m.targetIndex);
	if (m.targetIndex >= 0)
	{
		updateTargetTracking(m.targetIndex);
		
		//	turn (right) toward target.
		var dy = m.targetpos.y - this.y;
		var dx = m.targetpos.x - this.x;
		if (Math.sqrt(dx*dx+dy*dy) < 20)
		{
			//close enough...
			m.targetpos.x = Math.random() * state.width;
			m.targetpos.y = Math.random() * state.height;
			dx = dy = 1;
		}
		var desAngle = Math.atan2(dy, dx);
		var dAngle = desAngle - this.rotation;
		var onAmount = Math.abs(dAngle);
		avel = dAngle;
		lvel = 1;
		// hey
		if (onAmount < 0.1)
			lvel = 1;
	}
	
	return {angularVelocity: avel, shoot: true, velocity: lvel};
	
	//return {angularVelocity: Math.random() * 2 - 1, shoot: true, velocity: 1};
	
};
```
