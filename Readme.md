#  Fusion AI




- [What Is Fusion AI](#what-is-fusion-ai)


## What is Fusion AI?

Fusion AI is a plugin designed for Unity that allows the creation of artificial intelligence characters  by providing a basis of systems used in games for decades. Fusion AI helps to create and visualize the behavior of AI characters potentializing the development and allowing that programmers jump straight into practice.

Apart from demonstrating the **practical** use of Fusion AI, this documentation teaches basic **theoretical** information about several techniques used in game AI to help developers achieve the best result possible.

## Theory of Game AI

### AI Personality

When the subject is to create an AI character we always think that the main goal is to be as realistic as possible, but is that really necessary or even desired to your character?

 A lot of times the characters need behaviors that are easy to understand, to allow players to learn their behavior to create strategies, sometimes complex and realistic routines can make the character too unpredictable or hard to understand. 

That means that the programming and behavior of your AI character must come from your need in game design, always thinking on "what is the goal of having this character in my game" and how its programming must be to better reach that goal.



### Different Methods to Achieve a Result
Fusion AI uses a collection of techniques on a modular model that allows for the developer to use the best possible solution for each character. These techniques were collected from several academic papers and books.

The different modules that exist in Fusion AI are: **decision**,  **movement** and **objective**.




### Decision Making Modules
#### State Machine
##### Description
A state machine consists of different pre-programmed states (actions and behaviors)  and transitions between them. The character starts in an initial state and executes the behavior described in that state until the conditions to a transition are true and the state is altered.
##### Best Use Cases
State machines are better used on characters with predictable and simple behaviors. The creation of complex state machines can result on realistic and dynamical characters, but all interactions between states have to be completely pre-determined by developers, that have full control of the agent behavior. 

The main advantage of the state machine is the level of refinement that is possible to achieve, since transitions between two states are pre-planned.

The disadvantages of the state machine are lack of realism and intelligence, since it can't react to player actions that aren't  planned.

##### Example
A soldier character is in a round state, moving from point A to B. He stays in that state until a player arrives (trigger), then the soldier enters attack mode (another state) and will continue to behave that way until another trigger to other state is true.



#### Decision Tree
##### Description
A decision tree iterates through several questions to arrive on a result behavior that better matches the current needs of a character. There are no explicit transitions and all behaviors are independent.

##### Best Use Cases
It's the simplest and most direct way to execute a character's behavior. It is better used on quick decisions or for characters that instantly change behavior based on game's situations. Can be really useful on quick paced games and characters.

##### Example
A minion on a MOBA game that attacks enemy minions by default, but needs to change its target by the approximation of enemy players, or other game elements that require focusing, making the minion calculate what is the most important target and change its focus immediately.



#### Behavior Tree
##### Description
A behavior tree is composed of tasks laid out on hierarchical execution order. A character has a initial task and must complete it before moving on to the next task, following the direction indicated by tree nodes. There are three different node types on a behavior tree.

Task nodes have the actual behavior that is going to be executed and have its result tested.
Sequencers execute all its children until one of them fails.
Selectors try to execute all its children to find one that succeeds.


##### Best Use Cases
The goal of a behavior tree is to create complex and realistic behaviors to a character. The tasks are described on a hierarchy order of execution, meaning that the developer can simulate multiple steps of a larger action that must happen before the final task succeeds. By comparison to other methods, the behavior tree defines a more realistic character, but rarely better optimized.

##### Example
An agent, when trying to enter a room first tries to turn the door handle, then finds out that the door is locked. She tries to unlock it, and if she fails she will try to barge down the door. If everything fails she will try to go through a window.

This behavior is realistic in comparison to how a real life agent would act, but is not greatly optimized in comparison to just going through the window in the first place. This is a great example of how the needs of the game design impacts on the programming of the AI characters.

### Movement Modules
#### Unity Navigation Mesh
Fusion AI natively applies unity navmesh system. This system bakes the terrain and game world as polygons, with pre-calculated demarcations of all the areas that characters can move through.  Characters calculate the most optimal path to its destination.

It is a efficient way to move a character, but at the cost of realism and immersion. Characters possess knowledge of all the terrain and game world and always find the best possible route to a destination. Navmesh can also be difficult to use with moving obstacles and a dynamic game world.
#### Raycast Movement
Dynamic navigation system oriented by "rays" that detect elements that are near the character. The route is constantly recalculated and changes according to the final distance to the destination and the presence of other characters.

Better realism of movement, similar to the expected navigation of real life characters that perceive the environment around them, but not very efficient. The route used can be not the most optimized or may even not reach its destination.

## Implementation on Fusion AI
### Base Implementation and Workflow
To add a Fusion AI functionality to a character, simply add the Fusion AI Character script to it, then you can select the desired modules for each part of your character. Changing modules will swap the necessary scripts automatically and you should not try to add or remove these scripts manually.

To edit decision modules (state machine, decision tree and behavior tree) go to Unity menu's > Window > Fusion AI > Desired Editor. Note that the editors works simultaneously with the inspector of the current selected game object. Selecting a window on any editor will update the Fusion AI Character inspector and allow you to edit values.

Movement and Objective modules do not use a separate editor and can be tweaked directly on the inspector of the equivalent script. 
### Decision Making Modules
#### State Machine
State machines are composed of states and transitions.
##### Right Click Editor Interactions 
| Element | Options  |
|--|--| 
|Blank Editor Space|Create State <br /> Clear All <br /> Recenter Screen| 
|Start Window| Set Initial State|
|State Window| Create Transition <br /> Duplicate State <br /> Delete State|
|Transition Arrow | Delete Transition|

##### State Properties
States will execute a [Unity Event](https://docs.unity3d.com/ScriptReference/Events.UnityEvent.html) when active, as well as two additional events when the state becomes active and when it stops executing, by transitioning to another state. A delay for each event call can be set up.
|Type|Name|Description|
|--|--|--|
|string|State Name|State name for exhibition|
|Unity Event| Action| Action to be executed in every state call|
|Unity Event| Entry Action| Action to be executed when the state becomes the current state|
|Unity Event| Exit Action| Action to be executed when the state stops being the current state|
|float| Minimal Action Time| Time between "Action" calls while state is active|
|float| Minimal Entry Action Time| Time delay after "Entry Action" call|
|float| Minimal Exit Action Time| Time delay after "Exit Action" call|


##### Transition Properties
Transitions use a bool function with no parameters as a [Delegate]([https://docs.microsoft.com/pt-br/dotnet/csharp/programming-guide/delegates/using-delegates](https://docs.microsoft.com/pt-br/dotnet/csharp/programming-guide/delegates/using-delegates)) to check the conditions of the trigger. Setting the appropriate name and target to ensure that Fusion AI finds this function is essential.
|Type|Name|Description|
|--|--|--|
|string|Transition Name| Transition name for organizational purposes|
|Mono Behaviour| Target|Target Mono Behaviour script for boolean trigger function|
|string| Trigger Function Name| Boolean type function name for trigger|
|bool| Always True| 	The transition will always trigger



 An example of implementation of a simple transition function is:

    public Transform player;
    
    public bool IsPlayerNearby()
    {
	    if((Vector3.Distance(player.position, transform.position) < 10)
	    {
		    return true;
	    }
	    else return false;
    }
#### Decision Tree
Decision Trees are composed of decisions and actions. The tree root is always executed from the beginning at every frame.
##### Right Click Editor Interactions 
| Element | Options  |
|--|--| 
|Blank Editor Space|Create Action<br /> Create Decision<br /> Clear All <br /> Recenter Screen| 
|Start Window| Set Root Node|
|Decision Window| Set If False Node <br /> Set If True Node <br /> Delete Decision|
|Action Window| Set Next Node <br /> Duplicate Action <br /> Delete Action|

##### Action Properties
When an action is executed, an [Unity Event](https://docs.unity3d.com/ScriptReference/Events.UnityEvent.html) is called. When the action finishes executing the next node set up will be called immediately on the same frame.

|Type|Name|Description|
|--|--|--|
|string| Action Name| Action name for exhibition|
|Unity Event| Action| Action to be called when node is activated|

##### Decision Properties
When a decision is executed, it will try a trigger bool [Delegate]([https://docs.microsoft.com/pt-br/dotnet/csharp/programming-guide/delegates/using-delegates](https://docs.microsoft.com/pt-br/dotnet/csharp/programming-guide/delegates/using-delegates)) function and use the result to  execute the next node on the tree, based of the connections "If False" and "If True".

**Warning:** Note that the "If False" connection is always on the left side of the decision node circle on the editor, while the "If True" is always on the right side.
|Type|Name|Description|
|--|--|--|
|string|Decision Name| Decision name for exhibition|
|Mono Behaviour|Target| Target Mono Behaviour script for boolean test function|
|string|Function Name |Boolean type function name for decision test

An example of implementation of a simple decision function is:

    public int life = 100;
    
    public bool IsLifeLow()
    {
	    if(life <= 10)
	    {
		    return true;
	    }
	    else return false;
    }
   
#### Behaviour Tree
A behaviour tree is composed of tasks, selectors and sequencers. This type of decision module is very useful for complex and multiple steps dependent behaviours. The logic behind selectors and sequencers is very simple and can be combined in multiple ways for powerful results.

##### Right Click Editor Interactions 
| Element | Options  |
|--|--| 
|Blank Editor Space|Create Task<br /> Create Selector<br /> Create Sequencer<br /> Clear All <br /> Recenter Screen| 
|Start Window| Set Tree Root|
|Selector and Sequencer Windows| Collapse <br /> Add Child <br /> Randomize Children <br /> Delete Window|
|Task Window| Duplicate Task<br /> Delete Task|

##### Task Properties
When a task is executed, it will try a trigger bool [Delegate]([https://docs.microsoft.com/pt-br/dotnet/csharp/programming-guide/delegates/using-delegates](https://docs.microsoft.com/pt-br/dotnet/csharp/programming-guide/delegates/using-delegates)) function and return the result to its parent node, where it can be used to determine the flow of the tree. The task action should be coded in a way that first executes the necessary behaviour and then test the necessary conditions of its success.

**Important:** If ` persistentTry` bool is enabled you can try to perform a Task for as long as you want, and the tree will continue to try a Task until you change the tree's ` finishTrying` bool to true. This can be done by referencing the behaviour tree and setting ` behaviourTreeReference.finishTrying = true;`.

|Type|Name|Description|
|--|--|--|
|string| Task Name| Task name for exhibition|
|Mono Behaviour| Target|Target Mono Behaviour script for boolean test function|
|string| Function Name| Boolean type function name for result return|
|bool| Persistent Try| Enables persistent attempts to run the tree, this will continue to try to perform a task every frame until the result is true or the tree's ` finishTrying` bool is manually set to false

An example of a Task function implementation is:

    public Transform door;
    
    public bool OpenDoor()
    {
	    //first execute the necessary behaviour
	    door.GetComponent<Door>().Open();
	    
	    //then execute the check for return value
	    if(door.GetComponent<Door>().isOpen) return true;
	    else returnf  false;
    }

Alternatively, the same logic can be coded in a more direct way:

    public Transform door;
    
    public bool OpenDoor()
    {
	    if(door.GetComponent<Door>().isUnlocked)
	    {
		    door.GetComponent<Door>().Open();
		    return true;
	    }
	    else return false;
    }

These two simple examples show that the way you implement the Task on a behaviour tree varies based on the way you code your game, and your personal coding preferences.

##### Sequencers and Selectors
Now that you better understand the base of the functionality of a behaviour tree (tasks and return values) you need to understand how sequencers and selectors works. Sequencers and selectors work in a similar way, but are opposite in comparison to the way they relate to their children.

**Selectors** can have multiple children (tasks, selectors or sequencers) and will execute the children until one of them returns as true. When this happens, the selector itself will return as true and this value will be used by its parent to continue the tree flow. If all of a selector's children return false then the selector itself will return false.

**Sequencers** can have multiple children (tasks, selectors or sequencers) and will execute the children until one of them fails and returns false. When this happens, the sequencer itself will return as false and this value will be used by its parent to continue the tree flow. If all of a sequencer's children return true then the sequencer itself will be a success and returns true.

By default, selectors and sequencers will execute its children in order (indicated by numbers on the editor), but you can change each of them to random if needed, to create a more dynamic and unpredictable behaviour when possible.

This means that you can combine sequencers, selectors and tasks to create a complex and ever-flowing behaviour to your tree. Selectors should be seen as a "try any possible solution" node, while Sequencers are "try to execute all these setps to a combined actions" node. 


