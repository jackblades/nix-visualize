# Nix Dependency Visualizer

Script that automates the generation of pretty dependency graphs from the output of ``nix-store -q --graph <package>``.

## Command Line Options

## Configuration Files

## Graph Layout Algorithm

Packages are sorted vertically such that all packages are above everything that they depend upon, and horizontally so that they are close to their direct requirements, while not overlapping more than is necessary.

### Vertical Positioning:  Levels

 Since dependency trees are acyclic, it is possible to sort the tree so that *every package appears below everything it depends on*.  The first step of the graph layout is to perform this sort, which I refer to in the code as adding "levels" to packages.  The bottom of the tree, level *n*, consists of any packages that can be built without any external dependencies.  The level above that, level *n-1* contains any packages that can be built using only packages on level *n*.  The level above that, *n-2*, contains any packages that can be built using only packages on levels *n-1* and *n*.  In this way, all packages on the tree sit above any of their dependencies, and the package we're diagramming out sits at the top of the tree.

<img src="images/levels.png" width="50%">


#### Adding vertical offsets

In order to keep labels legible, after putting the packages on levels, some of them are given a small vertical offset.  This is done by sorting each level by x-position, and then


### Horizontal Positioning

Initially the horizontal positions for packages are chosen randomly, but the structure of the underlying graph is made clearer if we try to optimize for two things:

1. A package should be vertically aligned with the things it depends upon (i.e. the nodes on the level above it that it is linked to), in order to minimize edge crossing as far as possible
2. A package should try not to be too close to another package on the same level, so as not to have nodes overlap.
