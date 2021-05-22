---
title: Creating 3D Worlds with Rust
header:
  image: "/images/city.jpg"
categories:
  - Rust
  - Game Development
tags:
  - Rust
  - Game Development
published: false
---

It is useful when writing a game to separate the description of the world from rendering, windowing, and anything operating system specific. In this article, we will be building a description of a 3D world. This can then be passed to a renderer and even serialized/deserialized!

The final product will look like this:

```rust
#[derive(Default, Serialize, Deserialize)]
pub struct World {
    #[serde(serialize_with = "serialize_ecs", deserialize_with = "deserialize_ecs")]
    pub ecs: Ecs,
    pub physics: WorldPhysics,
    pub scene: Scene,
    pub animations: Vec<Animation>,
    pub materials: Vec<Material>,
    pub textures: Vec<Texture>,
    pub geometry: Geometry,
    pub fonts: HashMap<String, SdfFont>,
}
```

Let's begin! :)

## Creating the World

> "If you wish to make an apple pie from scratch, you must first invent the universe"
> ~ Carl Sagan

One easy way to keep code decoupled and reusable is to isolate it to its own library. Let's create a new library for our description of the game world.

```bash
cargo new --lib world
cd world
touch src/world.rs
```

And declare our `world` module in `lib.rs`:

```rust
mod world;

pub use self::world::*;
``` 

And finally declare our `world` struct in `world.rs`:

```rust
struct World;
```

## Entity Component Systems

Entity component systems have been [covered](https://en.wikipedia.org/wiki/Entity_component_system) in great [detail](https://www.youtube.com/watch?v=W3aieHjyNvw) in many other [places](https://gameprogrammingpatterns.com/component.html), so I'll omit the details here. To summarize, an entity component system has three distinct parts:

* Entities
  * A value representing a distinct object in the game world
* Components
  * Data that can be associated with an entity to store state
* Systems
  * Logic that manipulates components, typically by iterating over entities that contain a particular combination of components

The advantage of using an ECS is that the programmer does not have to declare particular game objects that are strongly typed and inflexible. ECS favors composition over inheritance. The programmer can declare an entity, associate components with it, and then use systems to manipulate the entities in the game world. This is a powerful technique!

Thankfully, there are multiple excellent ECS crates in the Rust community. For our purposes, we will use the [legion](https://github.com/amethyst/legion) crate.

Let's add the library to our `Cargo.toml`:

```toml
[dependencies]
legion = "0.4.0"
```

Now we can add the ECS to our game world.

```rust
pub type Ecs = legion::World;
pub type Entity = legion::Entity;

pub struct World {
    pub ecs: Ecs,
}
```

## Serializing the Game World

Before building too much of the game world, we will want to start supporting serializing and deserializing the world. This will allow us to save and load the game world for storing levels. This allows more possibilities like having a level editor, since you can instantiate the world, edit it, then serialize it to a file (which can then be loaded again later!).

For serialization/deserialization we can use the [serde](https://github.com/serde-rs/serde) crate and the [bincode](https://github.com/bincode-org/bincode) format.

Let's add a few dependencies to our `Cargo.toml`.

```toml
[dependencies]
...
bincode = "1.3.3"
serde = "1.0.125"
lazy_static = "1.4.0"
```

Now we can derive the `Serialize` and `Deserialize` traits.

```rust
#[derive(Default, Serialize, Deserialize)]
pub struct World {
    #[serde(serialize_with = "serialize_ecs", deserialize_with = "ecs")]
    pub ecs: Ecs,
}

lazy_static! {
    pub static ref COMPONENT_REGISTRY: Arc<RwLock<Registry<String>>> = {
        let mut registry = Registry::default();
        Arc::new(RwLock::new(registry))
    };
    pub static ref ENTITY_SERIALIZER: Canon = Canon::default();
}

fn serialize_ecs<S>(ecs: &Ecs, serializer: S) -> Result<S::Ok, S::Error>
where
    S: Serializer,
{
    let registry = (&*COMPONENT_REGISTRY)
        .read()
        .expect("Failed to get the component registry lock!");
    ecs.as_serializable(legion::any(), &*registry, &*ENTITY_SERIALIZER)
        .serialize(serializer)
}

fn deserialize_ecs<'de, D>(deserializer: D) -> Result<Ecs, D::Error>
where
    D: Deserializer<'de>,
{
    (&*COMPONENT_REGISTRY)
        .read()
        .expect("Failed to get the component registry lock!")
        .as_deserialize(&*ENTITY_SERIALIZER)
        .deserialize(deserializer)
}
```