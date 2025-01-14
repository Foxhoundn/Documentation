---
ID_PAGE: 22082
PG_TITLE: 09. Sprites
---

# Sprites

In this tutorial, we are going to learn how to manipulate Sprites. Sprites are 2D image/animation, and we will use them to display an image with alpha channel. Sprites always face the camera.

Nowadays, sprites are often used to display animated characters, and for particles, and to simulate 3D complex objects like trees.

Individual sprites are collected together in a single image file called a spritesheet or texture atlas. 

* A uniform spritesheet is one where all the sprites are exactly the same size and arranged in order in the file. When you read the term spritesheet in the documentation you can usually assume that it is referring to a uniform spritesheet. A uniform spritesheet is overseen by a [Sprite Manager](#sprite-manager).
* A packed spritesheet is one where the sprites can be of different sizes and often packed in such a way as to minimize the overall size of the file. Usually the full term of _packed spritesheet_ will be used for such a spritesheet. A packed spritesheet is overseen by a [Sprite Packed Manager](#sprite-packed-manager). This is available from BJS version 4.1

For sprites the use of one of these managers is mandatory, even for one sprite. They optimize GPU resources by grouping in one place multiple instances of a sprite.

![Elements](/img/how_to/Sprites/08.png)

_Final result_


## Sprite Manager

For sprites of the same size you use

```javascript
// Create a sprite manager
var spriteManagerTrees = new BABYLON.SpriteManager("treesManager", "Assets/Palm-arecaceae.png", 2000, 800, scene);
```

When creating a sprite manager, you have to decide a few parameters:
* Name: a name for this manager.
* The 2D image URL (most of the time, you will want use an image format which contain alpha channel, like .PNG).
* The capacity of this manager : the maximum number of instances in this manager (in our example, we could create 2000 instances of trees).
* The cell size, corresponding to the size of your image, like we’ll see below. Please note that cellSize could be a number or an object made of a width and height properties (Later on you will be able to also specify `spriteManager.cellWidth` and `spriteManager.cellHeight`)
* The actual scene, to which we will add this manager.

To give another example, look at this snippet:
```javascript
var spriteManagerPlayer = new BABYLON.SpriteManager("playerManager","Assets/Player.png", 2, {width: 64, height: 64}, scene);
```

This time, we only want 2 instances, and we said that our sprite’s size is 64x64. Here is what our image looks like:

![Elements](/img/how_to/Sprites/08-1.png)

Each image of a sprite must be contained in a 64 pixel square, no more no less.

## Sprite Packed Manager

For sprites of varying sizes you need an image file and a JSON file containing the positional data of the sprites in the packed spritesheet. The image file and the JSON file should have the same name and be in the same folder, eg pack1.png and pack1.json.

For example:
```javascript
var spm = new BABYLON.SpritePackedManager("spm", "pack1.png", 4, scene);
```
The parameters are:
* Name: a name for this manager.
* The 2D image URL (most of the time, you will want use an image format which contain alpha channel, like .PNG).
* The capacity of this manager : the maximum number of instances in this manager (in our example 4).
* The scene, to which we will add this manager.

It is also possible to reference an existing JSON object, of the correct format, directly. In this case the JSON object is passed as an additional parameter. For example:

For example:
```javascript
var spm = new BABYLON.SpritePackedManager("spm", "pack1.png", 4, scene, JSONObject);
```

### Packed Format

![Packed Spritesheet](/img/how_to/Sprites/pack1.png);

The JSON format for the above file is based on that produced using the _TexturePacker_ app with output file framework set to JSON(Hash) and Trim to None and Allow Rotation to Off. For the above packed spritesheet _TexturePacker_ outputs

```javascript
{   "frames": {
	
		"eye.png": {
			"frame": {"x":0,"y":148,"w":400,"h":400},
			"rotated": false,
			"trimmed": false,
			"spriteSourceSize": {"x":0,"y":0,"w":400,"h":400},
			"sourceSize": {"w":400,"h":400}
		},
		"redman.png": {
			"frame": {"x":0,"y":0,"w":55,"h":97},
			"rotated": false,
			"trimmed": false,
			"spriteSourceSize": {"x":0,"y":0,"w":55,"h":97},
			"sourceSize": {"w":55,"h":97}
			},
		"spot.png": {
			"frame": {"x":199,"y":0,"w":148,"h":148},
			"rotated": false,
			"trimmed": false,
			"spriteSourceSize": {"x":0,"y":0,"w":148,"h":148},
			"sourceSize": {"w":148,"h":148}
		},
		"triangle.png": {
			"frame": {"x":55,"y":0,"w":144,"h":72},
			"rotated": false,
			"trimmed": false,
			"spriteSourceSize": {"x":0,"y":0,"w":144,"h":72},
			"sourceSize": {"w":144,"h":72}
		}
	},
	"meta": {
		"app": "https://www.codeandweb.com/texturepacker",
		"version": "1.0",
		"image": "pack1.png",
		"format": "RGBA8888",
		"size": {"w":400,"h":548},
		"scale": "1",
		"smartupdate": "$TexturePacker:SmartUpdate:c5944b8d86d99a167f95924d4a62d5c3:3ed0ae95f00621580b477fcf2f6edb75:5d0ff2351eb79b7bb8a91bc3358bcff4$"
	}
}
```

SpritePackedManager only uses the frame property for each sprite so the minimal JSON format is

```javascript
{   "frames": {
		"eye.png": {
			"frame": {"x":0,"y":148,"w":400,"h":400}
		},
		"redman.png": {
			"frame": {"x":0,"y":0,"w":55,"h":97}
			},
		"spot.png": {
			"frame": {"x":199,"y":0,"w":148,"h":148}
		},
		"triangle.png": {
			"frame": {"x":55,"y":0,"w":144,"h":72}
		}
	}
}
```

## Create A Sprite Instant

For both managers, we can create instances of a sprite linked to a manager. Creating an instance is as simple as:

```javascript
var sprite = new BABYLON.Sprite("sprite", manager);
```

which uses the first sprite on the sheet

Using a uniform spritesheet and SpriteManager you indicate which sprite to use will cellIndex, counting from the top sprite on the left in order right and down.

For example

```javascript
var sprite = new BABYLON.Sprite("sprite", manager);
sprite.cellIndex = 2;
```

Using a packed spritesheet and SpritePackedManager you can either use cellIndex, as above, or the cellRef, the name of sprite. 

For example
```javascript
var sprite = new BABYLON.Sprite("sprite", manager);
sprite.cellRef = "spot.png";
```

You can change its size, orientation or reflection:
```javascript
sprite.size = 0.3;
sprite.angle = Math.PI/4;
sprite.invertU = -1;
```

Starting with Babylon.js v2.1, you can define the sprite's width and height:
```
sprite.width = 0.3;
sprite.height = 0.4;
```

you can manipulate it like any other meshes:
```javascript
sprite.position.y = -0.3;
```

## Sprite Animation

One of the advantages of sprites is animations. The most straight forward way is to use a uniform spritesheet and SpriteManager. You only have to load one large image file which will contain all animation images, one next to another. Just be careful to respect the pixel size that you have specified in the manager.
For example with this spritesheet of players:

![Elements](/img/how_to/Sprites/08-2.png)

We can use this to animate our players in more than 40 positions, depending upon the situation (walking, jumping,…). 

If you want to begin the animation, simply call this function:
```javascript
player.playAnimation(0, 43, true, 100);
```
The player sprite will be animated from frame 0 to frame 43. The third parameter is indicating if this animation will loop or not, true to loop. The last parameter is the delay between the frames (the smaller it is, the faster the animation).

* [Playground Example Animation of Uniform Sprites**]( https://www.babylonjs-playground.com/?8).

It is also possible to use playAnimation with sprites from a packed spritesheet. It is worth double checking that the sprites to animate are consecutive and in the correct order in the JSON file.

## Packed SpriteSheet Playground Examples

* [Playground Example Direct JSON Object Full TexturePacker Format](https://www.babylonjs-playground.com/#K5KTWA)
* [Playground Example Direct JSON Object Minimum Format](https://www.babylonjs-playground.com/#K5KTWA#1)
* [Playground Example JSON File](https://www.babylonjs-playground.com/#K5KTWA#2)
* [Playground Example JSON File Multiple Sprites](https://www.babylonjs-playground.com/#K5KTWA#4)
* [Playground Example JSON File Animated Slide Show](https://www.babylonjs-playground.com/#K5KTWA#5)

## Next Step
Having learnt about sprites, so it’s time to move on to use them in a classic effect in 3D : [particles](/babylon101/Particles).

# Further Reading

[Mesh Overview](/features/Shapes)
