
 MV3D
======

A 3D rendering plugin for RPG Maker MV.


If you are making a game with this plugin, please consider supporting my
patreon.  
https://www.patreon.com/cutievirus  
You can also unlock some patron-only features by becoming a patron, such as
Dynamic Shadows.  
A list of patrons can be found at the bottom of this file.

## Getting started

To use the plugin on a new or existing project, download [plugin.zip] and
extract the files into your project directory.
Then, load `babylon.js` and `mv3d-babylon.js` as plugins in that order.

[plugin.zip]:
https://github.com/Dread-chan/MV3D/blob/master/plugin.zip


Now when you run your game, the map should be rendered in 3D.

The A3 and A4 tiles will be rendered as walls. You can also change the height
of tiles using regions and terrain tags.

By default, regions 1-7 are configured to affect the height.

Terrain tag 1 is configured to use a cross shape, so tiles with this tag will
stand up like a tree.

Terrain tag 2 is configured to use a fence shape. Try putting this tag on the
fence autotiles that come with MV.

The regions and terrain tags can be reconfigured however you want.

---

## Configuration

Tileset and map configurations are placed in the note area.
Event configurations are placed either in the event note or in comments.
Region and terrain tags are configured through the plugin parameters.

In order for the plugin to recognize configurations, they need to be wrapped
properly.  
Tileset and map configurations need to be wrapped in a <mv3d> </mv3d> block,
while event configurations need to be inside a <mv3d:  > tag.  
Region and terrain tag configurations don't need to wrapped at all.

### Using configuration functions

Configuration functions are used to configure tilesets, regions, terrain tags,
events, and maps.

The following is an example of a basic configuration function. Each function
is passed a list of parameters.

    top(A1,0,0)

Some functions can take a large number of parameters. In these cases the
parameter list is separated into groups. A vertical bar can be used instead of
a comma to jump to the next group. You can also jump to a parameter by name
using a colon.  
Here are some examples:

    ceiling(A4,0,0|2)
    camera(0,60,dist:5,height:0.75)

Parameter groups can also have names, and can be jumped to in the same way you
jump to a named parameter.

---

## Tileset Configuration

Each line in the tileset configuration should start by identifying the tile
you want to configure, followed by a colon, and then a list of configuration
functions.

Choosing a tile is done with the format `img,x,y`, where img is name of the
tileset image (A1, A2, B, C, etc.), and x and y are the position of that tile
on the tileset. For example, `A2,0,0` would be the top left A2 tile. On the
outdoor tileset, this would be the grass autotile.

Here are some exapmle tileset configurations.  

This first one will make our outdoor water sink into the ground a little.

    <mv3d>
    A1,0,0:top(A1,0,0),side(A1,31,54,31,14),depth(0.3),float(0.1)
    </mv3d>

But this example won't look very good if we place the water at the edge of a
cliff, so let's configure it to use a waterfall texture on the outside walls.

    <mv3d>
    A1,0,0:top(A1,0,0),side(A1,1,1),inside(A1,31,54,31,14),depth(0.3),float(0.1)
    </mv3d>


### Setting tile textures

A tile can have up to four different textures. These are the Top texture,
the Side texture, the Inside texture, and the Bottom texture.  
If unset, the inside texture will be the same as the side texture and the 
bottom texture will be the same as the top texture.  
Inside textures are only used if a tile has a depth() specified.

Each texture can be set with the respective top(), side(), inside(), and
bottom() function. Texture() can be used to set both the top and side texture.

Parameter list:
img,x,y,w,h|alpha|glow[anim]animx,animy

The number of parameters passed to the first group will alter the behavior of
these functions.  

With only 2 parameters, it will act as an offset. For example, top(0,1) will
use the texture of the tile below the configured tile.  

With 3 parameters, you specify the tileset image name and tile coordinates of
the tile to use the texture from. For example, top(A1,0,0) will use the top
left tile of the A1 tileset image.  

With 5 parameters, you specify a region with pixel coordinates on the
specified tileset image. For example, top(A1,24,72,48,48).  

The alpha parameter can be used to make the texture partially transparent. Or,
you can set alpha to 1 to turn on alpha blending.  
Examples: texture(|1), texture(alpha:0.5)

The glow parameter will make the texture glow in the dark. Good for lava.  
Example: texture(||1), texture(glow:1)

[Anim] is a named parameter group for defining custom animated tiles.  
The number supplied to animx and animy will be the offset used by the
animation. The final offset will be equal to the anim offset times the
current frame.  
Animx has frames 0,1,2,1, while animy has frames 0,1,2.  
Examples: texture(|||1,0), texture(anim:0,1)



### Setting tile height

Height and depth can be used to set the height of the tile. A tile's height
is equal to its height minus its depth. Tiles with depth will use their inside
texture on their sides. Depth is good for making pits.  
Examples: height(2), depth(1)  

The float function will make water vehicles float above the surface. If you've
given your water tiles some depth(), this will unsink your ships.  
Example: float(0.3)

The fringe function will change the height of your star tiles.  
Example: fringe(2)



### Changing tile shapes

There are a few special shapes which can be given to your tiles using the
shape() function. Valid shapes are FLAT, FENCE, CROSS, and XCROSS.  
Examples: shape(fence), shape(xcross)



### Slope Tiles

Slope tiles can be used to make hills or stairs to move between elevations.
The slope tiles will automatically try to choose their direction. Directional
passage can be used to prevent the slope tile from facing certain directions.  
Example: slope(1)

---


## Event Configuration

Event configurations can be placed in either the event note or comments, and
mush be wrapped in a <mv3d:   > tag.
A few configuration functions, such as pos, will behave slightly differently
depending on whether they're placed in the note or comments.

Here's some examples.

First let's make an event that displays on the edge of a wall.

    <mv3d:shape(fence),scale(0.9,1.3),y(0.51),rot(0),z(0)>

Next let's attach a flashlight to an event.

    <mv3d:flashlight(white,2,6,30)>



### Adjust event position

The x() and y() functions will offset the x and y position of the event's
mesh.  
Example: y(0.51)

The height() function will adjust the event's height above the ground.  
Example: height(2)

The z() function will set the event's absolute z position, ignoring ground
level.  
Example: z(0)

The pos() function will change the position of the event. This can be used to
make two events occupy the same space. Prefix numbers with + to use relative
coordinates.  
Examples: pos(1,2), pos(+0,+-1)



### Change event shape

Use the shape function to set what shape the event should use. Valid event
shapes are FLAT, SPRITE, TREE, FENCE, CROSS, and XCROSS.  
Example: shape(tree)

The scale function can change the size of the event.  
Examples: scale(2,2), scale(1.5,3)

The rot function will rotate the event's mesh. Doesn't work with Sprite or
Tree shapes.  
0 is south, 90 is east, 180 is north, and 270 is west.  
Example: rot(45)



### Event Lights

The lamp function attaches a point light to the event.
Parameter list: color,intensity,range
Examples: lamp(white,0.5,1), lamp(#ff8888,1,3)


The flashlight function attaches a spotlight to the event.
Parameter list: color,intensity,range,angle|yaw,pitch
Examples: flashlight(#ffffff,2,6,30), flashlight(red,2,6,45|90)


The height and offset of the lights can be set with lightHeight() and
lightOffset().  
Examples: lightHeight(0.5), lightOffset(0,1.01)



### Other event settings

You can set whether the event is affected by bush tiles using the bush
function.  
Examples: bush(true), bush(false)

You can disable or change the size of the event's shadow using the shadow
function.  
Examples: shadow(0), shadow(3)

The alpha function is used to make the event partially transparent or to turn
on alpha blending.  
Examples: alpha(0.5), alpha(1)

The dirfix function will set whether the event rotates depending on the camera
angle.  
Examples: dirfix(true), dirfix(false)

---

## Map Configuration

Map configuration goes in the note area of the map settings. Configurations
should be placed in an <mv3d></mv3d> block, which should contain a list of
configuration functions.

Some of these configurations apply when the map is loaded, while others affect
how the map is rendered.

---

### Light and Fog

The light function will set the ambient light level for the map.  
The ambient function is an alias for the light function.  
Examples: light(white), ambient(gray), ambient(#222222), light(rebeccaPurple)

The fog function sets the color and distance of the fog.  
Parameter list: color|near,far  
Examples: fog(white|10,20), fog(#e1feff), fog(black|20,30)



### Camera Settings

The camera function can be used to set various properties of the camera,
including the rotation, distance, height, and projection mode.  
Parameter list: yaw,pitch|dist|height|mode  
Examples: camera(0,60,dist:5,height:0.75), camera(45,45),
    camera(mode:orthographic), camera(mode:perspective)



### Setting a ceiling for the map

The ceiling function sets a ceiling texture and height, and also supports 
all the other properties from the texture function like alpha and animation.  
Parameter List:  
img,x,y,w,h|height|alpha|glow[anim]animx,animy  
Example: ceiling(A4,0,0|2)



### Other map settings

The edge function sets whether to render walls at the map edge.  
Examples: edge(true), edge(false)

The disable function will turn off the 3D rendering for the map.  
Example: disable()


---


## Plugin Commands

In this documentation, the parts surrounded with angle bracks like <n> are
parameters.  
<n> means number.  
<t> means time.  

Some commands (like lamp and flashlight) act on a character. By default the
target character will be the current event.
You can define your own target using the following syntax:

    mv3d ＠target rest of the command

If the second word in the command starts with `＠`, that will be interpreted
as the target.

Valid targets:

- ＠p or ＠player: Targets $gamePlayer.
- ＠e0, ＠e1, ＠e2, ＠e25 etc: Targets event with specified id.
- ＠f0, ＠f1, ＠f2, etc: Targets first, second, third follower, etc.
- ＠v0, ＠v1, ＠v2: Boat, Ship, Airship.

Some parameters can be prefixed with + to be set relative to the current
value.

For example:

    mv3d camera yaw +-45 0.5

---

    mv3d camera pitch <n> <t>
    mv3d camera yaw <n> <t>
    mv3d camera dist <n> <t>
    mv3d camera height <n> <t>

Sets the camera properties, where <n> is the new value and <t> is the time to
interpolate to the new value.   
Prefix <n> with + to modify the current value instead of setting a new
value.

---

    mv3d camera mode <mode>

Set camera mode to PERSPECTIVE or ORTHOGRAPHIC

---

    mv3d rotationMode <mode>

Set the keyboard control mode for rotating the camera.

Modes:

- OFF - Can't rotate camera with keyboard.
- AUTO - A&D while in 1st person mode. Otherwise Q&E.
- Q&E - Rotate camera with Q&E keys.
- A&D - Rotate camera with left & right or A&D. Move with Q&E.

---

    mv3d pitchMode <true/false>

Allow player to control camera pitch with pageup and pagedown.

---

    mv3d fog color <color> <t>
    mv3d fog near <n> <t>
    mv3d fog far <n> <t>
    mv3d fog dist <near> <far> <t>
    mv3d fog <color> <near> <far> <t>

<t> is time.  
Prefix values with + to modify the current values instead of setting new
values.

---

    mv3d light <color> <t>
    mv3d ambient <color> <t>

Sets the color for the ambient light.

---

    mv3d ＠t lamp color <color> <t>
    mv3d ＠t lamp intensity <n> <t>
    mv3d ＠t lamp dist <n> <t>
    mv3d ＠t lamp <color> <intensity> <dist> <t>

---

    mv3d ＠t flashlight color <color> <t>
    mv3d ＠t flashlight intensity <n> <t>
    mv3d ＠t flashlight dist <n> <t>
    mv3d ＠t flashlight angle <deg> <t>
    mv3d ＠t flashlight pitch <deg> <t>
    mv3d ＠t flashlight yaw <deg> <t>
    mv3d ＠t flashlight <color> <intensity> <dist> <angle> <t>

Angle is beam width of the flashlight.

---

    mv3d camera target ＠t <t>

Change the camera's target.
Camera will transition to the new target over time <t>.

---

    mv3d camera pan <x> <y> <t>
    mv3d pan <x> <y> <t>

Pans the camera view, relative to current target.

---

    mv3d disable
    mv3d enable

Turns 3D rendering on or off.

---

### Vehicle Commands

    mv3d <vehicle> big <true/false>
    mv3d <vehicle> scale <n>
    mv3d <vehicle> speed <n>
    mv3d airship ascentspeed <n>
    mv3d airship descentspeed <n>
    mv3d airship height <n>

The vehicles are boat, ship, and airship.  
"Big" vehicles can't be piloted too close to walls. This can be useful to
avoid clipping with vehicles with large scales.   
Speed of the vehicle should be 1-6. It works the same as event speed.   
A higher airship can fly over higher mountains. Perhaps you could let the
player upgrade their airship's height and speed.


---

## Patrons:

- Whitely
- Yorae Rasante
- Izybelle
- A Memory of Eternity
- Pumpkin Boss
- JosephSeraph
- HuskyTrooper
- rpgmakerunion.ru
- CrysHistory
- Fyoha
- GamesOfShadows
- 中华国哥
- 冬空 橙
