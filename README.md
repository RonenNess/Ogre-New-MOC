# Ogre-New-MOC
A redesign of the Ogre MOC lib (Minimal Ogre Collision) with performance boost

## What is it

This utility provide minimal collision detection for Ogre3d based projects. 
Its based on the old MOC utility, but with performance improvements.

A video demonstrating the collision lib can be found here:

[![IMAGE ALT TEXT](http://img.youtube.com/vi/eWitZPEyfCw/0.jpg)](http://www.youtube.com/watch?v=eWitZPEyfCw "New-MOC example")

[Note: the utility only detect collision, to prevent objects penetration you need to write the code yourself]

### Features

- Polygon based collision, i.e. accurate collision that is not based on bounding box but on actual shape (like in MOC).
- Collision detection type for different entities (bounding box, sphere, or accurate).
- Static geometry.
- Support ogre's query flags.
- Automatically prevent self-collision.
- Nearest collision or First hit collision modes.

### Performance

As a basic performance test I rendered a test scene which included ~825 static entities with accurate collision type (200 walls & trees, 625 ground tiles) + some NPCs running around.
The scene was rendered multiple times with the same camera and objects, but with different amount of collision queries per frame (random rays checking collision around).

The following are the FPS per queries amount (FPS were pretty stable):

- No collision queries (eg prformance of just drawing the scene): 274 FPS.
- 50 queries per frame: 274 FPS (no performance hit).
- 100 queries per frame: 188 FPS.
- 200 queries per frame: 102 FPS.

### Caveats
- Don't support terrains collision, unless they are converted to an entity with mesh.
- Ignore Ogre query flag *types* (note: types means billboard, terrain, entity... query flags are supported, types are not.).
- No skeleton-based animation supported (but node movement / rotation / scaling is supported).

### Disclaimer

This code is pretty old and may be slightly ugly. It works pretty well though, as I recall.

## How To Use

To use this utility first include the source code in your project: NewMOC.h and NewMOC.cpp. 
Now you can instantiate a 'CollisionTools' manager, which is the main object we use to test collision:

```cpp
// create a new collision tools manager
CollisionTools* collision = new CollisionTools();
```

To test collision with entities you need to register them first, so the collision tools will know them.
To register a simple entity:

```cpp
// COLLISION_ACCURATE means accurate polygon-based collision.
// if accurate collision is not important for this specific entity, you can use COLLISION_BOX or COLLISION_SPHERE
collision->register_entity(entity, COLLISION_ACCURATE);
```

Or if your entity is static, eg its trasnformations never going to change in the future, you can register it as a static entity (much better performance):

```cpp
// register a static entity. its much faster, but future transformations won't apply on it.
collision->register_static_entity(entity, position, orientation, scale, COLLISION_ACCURATE);
```

And if you ever want to destroy an entity and make it no longer collideable, use ```remove_entity```:

```cpp
// remove an entity from collision tools
collision->remove_entity(entity);
```

Now that your entities are properly registered, you can start using collision:

```cpp
// ray is the ray to test collision for.
// query mask is ogre query flags.
// ignoreThis is an optional pointer to an entity we want to skip (for example, if you test detection for the player you'd want to skip its own mesh from collision).
// maxDistance is maximum distance for collision.
// stopOnFirstPositive if true, will return first result hit. if false, will return nearest (more overhead).
SCheckCollisionAnswer ret = collision->check_ray_collision(ray, queryMask, ignoreThis, maxDistance, stopOnFirstPositive);

// check if we found collision:
if (ret.collided) 
{
	// handle collision here..
}
```

## Old Repo

This repo is an export of an old lib I had hosted on google code.
The old repo can be found here: https://code.google.com/archive/p/new-minimal-ogre-collision/. 
