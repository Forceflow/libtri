# libtri
Library for working with the .tri file format. The .tri file format consists of a text-based header (extension .tri) containing the relevant model info and a binary data file (extension .tridata) containing the actual model data..

You can read the header info into a *TriData* struct using the methods in *tri_tools.h*. To read the actual data from the .tridata file, you can use the *TriReader.h* class.

## Tri File Format
The .tri file format is a very simple internal format to make abstraction of all the pitfalls and peculiarities of the plethora of model input formats in the wild. It basicly lists all triangles as one long list, with optional triangle properties (normals, colors ...) right behind the triangle definition. It was created with regards to streamability: all triangle information is stored sequentially, so you don't need to load the whole file in memory to process triangles. It's not as space-efficient as other formats, but is trivial to understand and parse.

###Tri Header
A typical .tri header file is text-based and looks like this. All keyword - values lines are separated by a newline:
```
#tri 1
ntriangles 8254150
geo_only 1
bbox  -2540.83 -2586 -15957 2699.43 2654.25 -10716.8
END
```
All elements are required.
* **#tri (version_number)**: (int) Tri version number.
* **ntriangles (n)**: (size_t) Total number of triangles.
* **geo_only (0 or 1)**: (bool) Indicate whether or not the data is geometry-only (for binary voxelization).
* **bbox (min_x min_y min_z max_x max_y max_z)**: (floats) Minimum and maximum vector of bounding box of the mesh. Bounding box must be cubical, or your model will be stretched during voxelization.
* **END**: Indicating the end of the header file.

### Tri data file
A .tridata file is a very simple binary file which just contains a list of the x, y and z coordinates of the triangle vertices (v0, v1 and v2), followed by the optional payload per triangle (normal, texture information, ...).

In case of geometry-only (or, confusingly called binary, as in binary voxelization) .tridata files, the layout per triangle looks like this:

* vertex 0 (3 x 32 bit float)
* vertex 1 (3 x 32 bit float)
* vertex 2 (3 x 32 bit float)

In case of a payload .tridata file, the layout per triangle is followed by

* normal (3 x 32 bit float)
* vertex 0 color (3 x 32 bit float)
* vertex 1 color (3 x 32 bit float)
* vertex 2 color (3 x 32 bit float)
