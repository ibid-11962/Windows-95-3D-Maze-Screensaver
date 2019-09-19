# Windows 95 3D Maze Screensaver

In windows 95 (and a few later versions of Windows) there was a screensaver that rendered and then solved a 3D maze with a a few interactive obstacles. This project is a recreation of that screensaver using WebGL and Javascript. It was created in Spring 2019. The matrix libraries used are [from here](https://github.com/esangel/WebGL/tree/master/Common) and under the MIT License.

The project can be tested live [here](https://ibid-11962.github.io/Windows-95-3D-Maze-Screensaver/)

For comparison, a YouTube sample of the original screensaver can be found at https://www.youtube.com/watch?v=7-yMd9Kt4mQ

# Features that have been implemented:

**Maze construction:**

The maze is stored as a 2D array of "cells" with each cell being a four int array describing it's four walls. This is randomly generated with recursive backtracking, based on an algorithm I found at https://www.dstromberg.com/2013/07/tutorial-random-maze-generation-algorithm-in-javascript/.

This array is looked at when determining the next move and when initially sending the vertex data to the GPU.

**Maze Textures:**

The walls, floor and ceiling each have specific textures. 

These files were extracted from the original screensaver.

Most of the wall is red brick, but occasionally there is an image displayed on the wall, taken from a standard rendering example image that must have been used in the OpenGL manual Microsoft used.

The ceiling texture was a 33x33 and this doesn't work well with WebGL because it isn't a Power of Two. So I used GIMP to create the 99x99 image that corresponds to one cell of the maze and then upscaled it to a 128x128 image.

**Camera:**

The lookat function from Maze.js is used, with the eye corresponding to the current location in the maze and the at being the sum of the position vector and the direction vector.

**Movement:**

There is a nextMove() function that determines the next step based on the current position, movement, and direction. The goal is to always follow the right wall.

0. if in the middle of moving or turning, continue moving or turning.
1. if just-turned and front is open - forward
2. if right is open - rotate right
3. if front is open - forward
4. if left is open - rotate left
5. otherwise (i.e. dead end) - rotate right (which will bring you to a state where the right side is open)

This function is used by both the camera and the rat, but the rat calls it twice as often as the camera so they move at different speeds and it's possible to cross.

**Polyhedra Objects:**

There are several objects floating throughout the maze. These positions (and of the 2D texture objects) are chosen at initialization and stored locally in javascript variables. 

There are four types of polyhedra visible: tetrahedrons, octahedrons, octahedrons, and dodecahedrons. Dodecahedrons are a little bit harder than the other three because the sides aren't triangular. The coloring is done by having each face be a slightly darker shade of gray than the previous face. This results in a very similar look to the original so it seems certain that Microsoft used a method like this.

The effect of hitting any polyhedra is the same. Movement freezes, and the up vector is rotated 90 degrees about the current direction the camera was faced in. Then the polyhedra is removed from the map.

**2D Textures Objects:**

There are a few textures that appear in the middle of the maze. I couldn't figure out how to extract these from the screensaver, so I extracted them from a unity clone of the screensaver I found online.

Because these are 2D I have them always rotate with the camera so the normal will always face the user when moving towards them.

There are four of these textures.

  - OpenGL floating text. 

    I considered changing this to WebGL, but I left it like the original for accuracy.

  - Start button. 

    This appears in the square immediately in front of the camera. (The camera is rotated at initialization until it is facing an open cell.)

  - Smileface
  
    This marks the end of the maze. When you hit it the walls come down (in reverse of the opening animation), the initialization is repeated, and then new walls go up.
	
  - Rat
   
    This moves throughout the maze on it's on. It doesn't do anything when you touch it.
	
**Mouse Movement**

When you move the mouse, the screensaver "exits" to desktop. 
This is based on 98.js from https://github.com/1j01/98 , but all the functionality has been removed.
	
**Lighting**

There is some basic lighting implemented that can be toggled on and off by pressing spacebar. Points further from the camera appear darker. This wasn't part of the original screensaver.

# Note

These have been tested to run on Google Chrome remotely, but will not run locally. They seem to run locally on Firefox though.

