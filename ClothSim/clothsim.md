# Assignment 4 Cloth Sim
Aakash Parikh CS 184-aat

## Overview
This project involves building a physical simulator for a cloth model. This model is interesting because a cloth is not a rigid object. We model the cloth using springs and add forces and collision constraints to the model. We also build a simulator to animate the object. At the end of the project, we implement various shaders using GLSL.

## Part 1: Masses and springs


Here we build our cloth as a grid of point masses and springs. For our implementation we use 3 types of springs,structural, shearing and bending. Each of these springs connects different point masses, and holds the cloth together. Here is an image illustrating those types of springs: 
![](https://i.imgur.com/ppyBRyQ.jpg)

Below is an example of how a cloth behaves with these springs in place. Note that the cloth is pinned at its top corners, and these images were taken after gravity was implemented in the next part.

| Only Shearing Constraints | Without Shearing Constraints|
| -------- | -------- |
| ![](https://i.imgur.com/M6W0Zrr.png)     |  ![](https://i.imgur.com/PIcc2HC.png)|

#### All Spring Constants
![](https://i.imgur.com/JdL3gNu.png)

## Part 2 Simulation via numerical integration

In this second part of the project,  we add simulation of forces to each of the point masses in our grid. We note that every object in the grid is acted on by the force of gravity (`external_accelerations`) but we also need to add the spring force. This is done by simply calculating the spring force for every pair of connected masses. We sum the all of the forces on each spring. 

Our next step is to integrate to calculate the new position of each point mass. We use Verlet integration and add a small damping term in order to update our positions. 

Finally, we ensure that our position changes are not overly drastic. We do this by constraining our position update to be no more than 10% greater than its `rest_length`
![](https://i.imgur.com/yp34urd.png)
*spring equation*


![](https://i.imgur.com/yaTU0DE.png)
*verlot integration*

* As our spring constant increases, we note that the cloth is much more  rigid for high ks compared to the low.  


| Low Ks | High Ks |
| -------- | -------- |
|![](https://i.imgur.com/7NAMr9M.png) | ![](https://i.imgur.com/vWaxchL.png) |

* At a higher density, the cloth is heavier, and you notice that the center between the two pins drags down.

| Low Density | High Density |
| -------- | -------- |
|![](https://i.imgur.com/lRLXIZM.png)| ![](https://i.imgur.com/w7C0WCW.png)|

*  Low damping falls very fast and swings, High damping slowly falls into place


| Low Damping | High Damping |
| -------- | -------- |
|![](https://i.imgur.com/hPlrR2M.png)|![](https://i.imgur.com/nnxxBJD.png)|

#### 4 pinned corner exampe

 ![](https://i.imgur.com/YeaQwIQ.png)


## Part 3
In this part we handle collisions with other objects. In a general sense, if a point mass passes through an object, we calculate where the point mass should have first made contact with an object and then update its position to be slightly over that position

#### Cloth over a sphere
Intersection with a sphere is fairly easy to do, as we can calcualte the distance between the point and the center of the sphere. If it is below a certain *r* we know it has passed the surface. Then we extend a ray in the direction of the point from the center of the sphere, and push the point outwards in that direction. 

* As spring constant decreases the cloth loses its rigidity around the sphere


| Default ks = 5,000 | ks = 500 | ks = 50,000 |
| -------- | -------- | -------- |
| ![](https://i.imgur.com/3ohgLZk.png)     | ![](https://i.imgur.com/q9K9fDc.png)     | ![](https://i.imgur.com/B0ER0fS.png)     |

#### Cloth over a plane 

It is slightly more complicated to tell if a cloth has passed through a plane. For this we draw a vector from the `old_position` to the new `position` and check if this vector crosses the plane. In particular we find some scalar *t* such that the projection of *vector_origin + t * vector_dir* on the normal vector of the plane is equal to 0. If this occurs between t = [0,1], it happened in the last time step and we need to apply a correction.

![](https://i.imgur.com/uee4snN.png)

## Part 4 Handling self-collisions
In the previous part we handled collisions with external objects, in this part we handle self collisions. In order to do this in an efficient manner, we must make sure we are not checking every single pair of point masses. We do this by using a hash to bucket each of the points based on the general region they are in. Then within each of these buckets we collide wiht a pair of point masses. If they are within 2 * `thickness` appart, we add a correction to separate them. 

#### Cloth falling on itself various stages
| ![](https://i.imgur.com/zLr4Hqa.png) |![](https://i.imgur.com/jJAnLLd.png) |
| -------- | -------- |
| ![](https://i.imgur.com/gkkrCU8.png)     | ![](https://i.imgur.com/utC7w6W.png)     |

* At high ks, cloth supports itself more, fewer folds. At low ks there are much more smaller folds, since cloth cannot support itself.


|High ks   | Low ks |
| -------- | -------- |
|![](https://i.imgur.com/5E0SQwu.png) |![](https://i.imgur.com/NpTdltT.png)|

#### Low Density Falling
* At high density rolls up very tightly, at low density once the cloth hit it cannot support weight and spreads out very quickly

![](https://i.imgur.com/gaTvZqF.jpg =320x)


## Part 5
In this part we use GLSL shaders to quickly add color to an object.  We use two types of shaders in this: vertex shaders, where we can modify the position and normal vector of a vertex, and fragment shaders where we output a [r,g,b,a] vector, where the last `alpha` term is not used in this project. For each vertex the outputs of the vertex shaders are used by the fragment shaders to decide what color it should be.

#### Blinn-Phong Shading model
The Blinn Phong Shading model utilizes a combination of 3 types of shading. The first is Ambient only which is the basic color of the object. Next is Diffuse shading, which is how diffuse objects look like under lighting. This is determined by the illumination from the source (which degrades over distance) scaled by a constant and the dot product of the surface normal and the direction of the light ray. The final type is specular, which adds a highlight on shiny objects. For this we calculate a half vector that is halfway between the viewing direction and the light ray. Then we use a very similar equation to the diffuse where the output is instead scaled by the dot product of a surface normal and this half vector. When put together Blinn Phong is a very realistic shading model.

| Ambient Only | Diffuse Only |
| -------- | -------- |
| ![](https://i.imgur.com/XPKwMk3.png)     | ![](https://i.imgur.com/yGZwXxE.png)     |

| Spectral only | Blinn-Phong |
| -------- | -------- |
| ![](https://i.imgur.com/M5u1ZWr.png)     | ![](https://i.imgur.com/FzxMcSv.png)     |

ka = 0.1, kd = 0.8, ks = 0.5

![](https://i.imgur.com/RVIIPKK.jpg =400x)

#### Texture Mapping
Texture mapping simply samples a texture based on the *uv* texture space coordanate

#### Bump Mapping
In bump mapping we adjust the shading of the object depending on how much the height changes if we move the point slightly. Based on this, we calculate a new local space normal, and use this for Blinn Phong shading. 


| Bump mapping on the cloth | Bump mapping on the sphere |
| -------- | -------- | 
| ![](https://i.imgur.com/YVAX9FO.png) | ![](https://i.imgur.com/5R0gGp8.png)|


#### Displacement Mapping
Displacement mapping extends bump mapping,by not only changing the shading, but also changing the actual position of the vertex based on the height.

| Displacement mapping on the cloth | Displacement mapping on the sphere |
| -------- | -------- |
| ![](https://i.imgur.com/ofBK5dk.png) | ![](https://i.imgur.com/UmIarQ6.png)|


Personally, I found that while the displacement mapping may be more realistic, the effect is much too drastic and the bump mapping gives off enough of the appearance of the texture to make it look like it is there.


#### Varying Coarseness
I found that for the particular texture I used, the coarseness did not have too much of an impace. But you can notice that some of the noisy transitions in the first column is gone in the second. For displacement mapping this also means there is less jaggies, and the transition between two heights is smoother.

| -o 16 -a 16  | -o 128 -a 128 |
| -------- | -------- |
| ![](https://i.imgur.com/4hibHtf.png)| ![](https://i.imgur.com/GA7IFjW.png)|
|![](https://i.imgur.com/cImpmNh.png)| ![](https://i.imgur.com/TmhDbp5.png)
|


#### Mirror Shading 
This is done by using the vector from the camera pos to the surface position and reflecting it over the normal to get the incoming direction. Then we sample from the environment map. 

 ![](https://i.imgur.com/AqAUMId.png) *Cloth above sphere*
 ![](https://i.imgur.com/Vm550VD.png) *Cloth falling on sphere*
 ![](https://i.imgur.com/IFXqkOB.jpg) *Cloth over sphere*


