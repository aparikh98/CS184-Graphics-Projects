# CS184 Graphics Projects Spring 2019


[Rasterizer](https://htmlpreview.github.io/?https://github.com/aparikh98/CS184-Graphics-Projects/blob/master/Rasterizer/rasterizer.html): This project involves many tools required to rasterize images in order to render vector graphics. We begin with rendering shapes as simple triangles, supersampling for antialiasing, and transforming these triangles. The project also includes sampling and using Barycentric coordinates for texture mapping. We have implemented bilinear and trilinear sampling.

[PathTracer 1](https://htmlpreview.github.io/?https://github.com/aparikh98/CS184-Graphics-Projects/blob/master/Pathtracer1/pathtracer1.html) and [Pathtracer 2](https://htmlpreview.github.io/?https://github.com/aparikh98/CS184-Graphics-Projects/blob/master/Pathtracer1/pathtracer1.html): We build a pathtracing algorithm to render scenes with physical lighting. In part 2, we extend this in order to account for various types of materials, lighting and depth of field camera effects. This allows us to render much more interesting images. We will discuss adding reflection and refraction for glass and mirror materials, implementation of a microfacet model, and adding a depth of field effect using the thin lens model.

[MeshEdit](https://htmlpreview.github.io/?https://github.com/aparikh98/CS184-Graphics-Projects/blob/master/MeshEdit/meshedit.html): Often objects are graphically modelled as a mesh. In this project we explore how we can represent a mesh using the Half Edge data structure, and develop many tools to edit mesh structures such as splitting, flipping and subdividing meshes. We also look at Bezier curves and how we can evaluate them using de Casteljau's methods. This project was especially interesting because it provided a deeper look into the geometry of shapes, and was a good insight into how many object modelling tools work today.

[Cloth Sim](/ClothSim/clothsim.md): This project involves building a physical simulator for a cloth model. This model is interesting because a cloth is not a rigid object. We model the cloth using springs and add forces and collision constraints to the model. We also build a simulator to animate the object. At the end of the project, we implement various shaders using GLSL.




