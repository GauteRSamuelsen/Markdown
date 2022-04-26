Return to [front page](./README.md).

# User Manual - Unreal Engine mation Matching plugin

## Table of contents

Markdown arguing here, wont create the table of contents.

## Intro to setup

This guide will help you get a basic player-controlled character, animated using motion matching up and running in game. There is also an NPC variant that mostly follows the same procedures, with the difference being how input is handled.

Start by importing the Motion Matching plugin into Unreal Engine. For now the plugin supports Unreal Engine version 4.27.2.

### Plugin contains

- MotCharacter - character class
- Motion Field - “database” and editor-window
- Motion Matching node for animation blueprints

> See also documents regarding Character rig and Animation specs & User guide.

## Unreal Input setup

Input mapping used in our project settings.

![Example of input setup](./SetupPictures/InputMap.png "Picture of unreal engine input setup")

## Character setup

In the Unreal Engine content browser
- Create a character based on the C++ class "MotCharacter" and give it a suitable name.
- Create an animation blueprint, based on a “regular” animation instance.
- In the “Mesh” section of your blueprint instance of your character, specify the skeletal mesh and the animation blueprint, which uses the same skeletal mesh and includes the motion matching node in its animgraph.

![Example of character assets needed](./SetupPictures/CharacterSetup.png "Picture of character setup")

![Example of character blueprint setup](./SetupPictures/CharacterBlueprintSetup.png "Picture of character setup")

## Event graph character setup

In the event graph for the character, the most important part is feeding the motion matching algorithm a target location. The motion matching goal will be the location the system moves towards. This becomes a moving target whenever the player starts moving.

> This example is for a player-driven character, but replacing the input would make it work for npc's.

![Example of character eventgraph setup](./SetupPictures/CharEventGraph.png "Picture of character eventgraph setup")

> To control camera, place these nodes anywhere in the character event graph blueprint.
![Example of character camera eventgraph setup](./SetupPictures/CameraInput.png "Picture of character camera eventgraph setup")

> Visualizing the input in the game-scene is also a great idea and can be done like this after the goal is built.
> ![Example of character eventgraph setup](./SetupPictures/CharInputVis.png "Picture of character eventgraph setup")

## Motion field setup

The “Motion field” is a custom data structure, the motion field becomes a database over the “available” animations for the motion matching system. The Motion Field stores the positions, rotations and velocities of the root- and selected skeletal bones, sampled from the pre-made animations provided to it.

To create a Motion Field you right click in the content browser and choose animation -> Motion Field.

![Where to find motion fields](./SetupPictures/MotionFieldSetup.png "Picture of motion field setup")

When creating, follow the prompts and create the Motion Field based on the skeleton and the tracked bones you want. The system automatically uses the “root” bone of the skeleton, but you can select additional bones.

![Example settings for motion field setup](./SetupPictures/MotionFieldSettings.png "Picture of motion field settings")

If you have a limited or only want to test functionality, select few or no motion bones to debug the system.
For a “normal” amount of animation data, selecting the two feet yields good results.
Feel free to experiment further with this. If you have a huge set of data, for example with motion capture data, then selecting the hip and or neck bone might yield exceptional results. 

> If the character does not appear in the motion field. Place an animation asset with the skeletal mesh you want to create a Motion Field of in a game-scene.

## Adding animations

In the “Motion Field editor” click the button to the right, “Add an animation”. Select the animations you want the character to be able to pull from and use in-game. Grouping motion fields by “state” is a good idea to organize and get ideal results. “Macro” curation of animations is talked about later. But for now grouping motion fields by idle/walk, jumping, climbing, and running makes sure you get the expected animations for the different states.

![Example of for motion field editor](./SetupPictures/AddAnimation.png "Picture of motion field editor")

To edit how granular and how many motion keys(animation poses) you want the database to sample click on the “Set properties” button. A good starting value is 0,1. The smaller the number, the more samples to pull from and the larger the data-set becomes. A higher number leaves fewer points for the motion matching to match against, and can result in choppier transitions.
A limitation of the system is that the tagging part of it only tags in motion steps, not to actual frames of animation. A smaller timestep means more precision and better results, but can be a bit cluttered and difficult to “read”, especially on smaller screen sizes.

![Example of for motion field "Set properties"](./SetupPictures/SetProperties.png "Picture of motion field's set properties panel")

When the properties are set, and animations imported, click “ProcessAll”, and the Motion Field is ready for use. For the character to animate, the animation blueprint motion matching node needs to know what motion field to pull from. This is the next step.

## Animation Blueprint setup

In the animation blueprint editor, right-click and create a motion matching node. Click the Motion Matching node, then enter the details panel on the right and select the desired Motion Field for the character.

![Example of motion field "Set properties"](./SetupPictures/AnimationBlueprintAnimGraph.png "Picture of motion field's set properties panel")

> In the animation blueprint’s “Root Motion mode” setting, selecting “Root motion from everything” makes the character move in the game-scene using the root motion data. But if you want to move the character using other code, then ignoring root motion will make the system animate, but not move the character in the scene. With “Root motion from everything” the system will find animations to get the character to the “goal” built in character blueprint.

## Animation blueprint event graph setup

The animation blueprint event graph really only needs to do a couple of things.
- Get a reference to the blueprint instance of itself
- Get and update the motion matching target

![Example of motion field "Set properties"](./SetupPictures/AnimationEventGraph.png "Picture of motion field's set properties panel")

## Multiple Motion Fields per Character

To create more “states” of animation it is advised to create more motion fields per character and switch/blend between them as needed. For example in the animation blueprint to have multiple motion matching nodes, and switch between them using logic from the character controller.

In the picture example the character switches to a motion field with run animations. This makes sure that the normal walk animations don't accidentally pick run animations and opposite.

This type of logic can also be used for example when jumping, strafing or when using other distinct forms of movement. Think of this as a “macro” curation, broad strokes.

> Example of simple blending logic

![Example of multiple motion fields for one character](./SetupPictures/MultipleMotionFieldsExample.png "Picture of multiple motion fields for one character")

## Debug hud setup

To toggle the debug hud, this can be included into the level blueprint to create a toggle using "m" or your own input setup.

![Debug HUD Blueprint](./SetupPictures/HUDSetup.png "Picture of HUD setup blueprint")

Return to [front page](./README.md).