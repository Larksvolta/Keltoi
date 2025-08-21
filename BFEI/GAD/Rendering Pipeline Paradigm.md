# Vertex Stage
To map 3D faces in a 2D plane and create faces. It's important to understand that at this stage the vertices aren't yet converted in pixels.

## Model view and transform
Remap the local coordinates (based on the center of pivot of a 3D model) of each object and cameras into world coordinates (the origin of a scene)

## Lighting
Calculates the RGB values of the vertices based on lighting materials and normals.
## Projection
Projects the 3D vertices into a 2D plane (screen).
## Assembly
Creates faces from the projected vertices.
## Clipping
If a face it's outside the screen gets deleted. If only vertices are outside the boundaries,  new vertices are created at the edge of the screen before deleting them.

# Rasterization stage
Converts the faces and vertices into pixels.

## Fragments generation
Creates a copy of the projected faces and determines which pixels fall within each face.

## Attribute interpolation
Gradient colour over pixels.

# Fragmentation stage
## Texturing
Textures are mapped onto the faces.
## Fragments testing
Determines which faces are in front of which.
## Fragments blending
Applies transparency to the fragments.

Isn't better to talk about fragments instead of faces at this stage?
