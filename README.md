# Worms Simulation

The Worms program is a simulation of worms living their lives in a 2D plane called a board. See Figure 1: Examples of Worms On a Game Board.
Figure 1: Examples of Worms On a Game Board

## Board

The board is represented by a matrix of squares. Positions within the board are denoted by the natural number coordinates of each square in the matrix. Each square may contain zero or one element from the set {carrot, segment from non-alive worm} as well as zero or more worm segments of alive worms. See the Worms Section for a description of non-alive worm, alive worm, and worm segments. Carrots and segments of alive worms have a food value that nourishes any worm that eats them.
Three types of worm are possible: Vegetarian, Scissor, and Cannibal. Worms may be alive, dead, or eaten. Each worm is composed of one head segment and zero or more non-head segments. Each alive worm’s head may move in pseudo random directions by changing its position form one position in the board to an adjacent position within the board. If a worm segment moves off an edge of the board, the worm segment “wraps” around to the opposite side of the board. Non-head segments are ordered with respect to the head and other non- head segments in the same worm. Whenever a segment moves, the immediately subsequent segment if any in the same worm moves to occupy the position just vacated by the segment that moved. In other words, the head moves around the board and non-head segments follow the preceding segment.
Vegetarian worms, Scissor worms, and Cannibal worms may eat any carrot at the same position in the board as the worm’s head. Eaten carrots are removed from the board. Cannibal worms may also eat any entire alive worm if the alive worms is composed of one or more segments at the same position as the Cannibal worm’s head. Eaten worms are removed from the board. Scissor worms may “slice” any alive worm if the alive worm is composed of one or more segments at the same position as the Scissor worm’s head. When a worm is sliced, the sliced worm’s segment at the same position as the Scissor worm’s head is removed from the board. Any segments preceding the removed segment in the sliced worm remain part of the sliced worm. Any segments after the removed segment in the sliced worm may become part of a new worm with a head at the position of the segment immediately after the removed segment in the sliced worm.
Each alive worm has a stomach with food storage capacity proportional to the number of segments in the worm. Each time an alive worm moves, it consumes some of the food in the its stomach. Each time an alive worm eats, it increases the amount of food in the its stomach. If a worm’s stomach becomes empty, the worm becomes dead.

## “Proper" Object Oriented Programming (OOP) Form

The logic and data of the Worms simulation is encapsulated by three concrete public classes, WormsSim, Worm, and CursesWormsSimUIStrategy. An abstract class, AbstractWormsSimUIStrategy, is also provided as an “interface” for any class that provides display and user input for a Worms simulation.

### WormSim Class

WormSim encapsulates the board, some string “sayings”, and zero or more Worm instances. WormSim provides a runSimulation() method that when called starts or restarts a simulation with a pseudo random number of initial worms and a board containing a carrot in every square. Note: more worms than the initial number may come into existence while the simulation is running. Note: although simulation state is encapsulated by an instance of WormSim, the WormSim methods, runSimulation(), and createWorm(), must be called periodically to modify simulation state over time. Each created Worm instance is initialized to store a saying pseudo randomly selected from the WormSim’s saying. Additionally, the WormSim methods, tryToEatCarrotAt(), sliceVictimForWorm(), and eatVictimForWorm(), are called by instances of Worm encapsulated by WormSim.

*Design Notes:*

- Following the principle of separation of concerns, the WormSim class intentionally does not have any "drawing" or user input dependencies. It is anticipated that many different strategies for "drawing" a simulation board and the worms may eventually be implemented. Similarly, many different mechanisms for user input may eventually be implemented. WormSim is responsible for encapsulating board state irrespective and decoupled from drawing and user input implemented elsewhere. See Section Use of the Strategy Design Pattern for more information about the decoupling.
- Composition ("has-a") relationships are preferred over inheritance ("is-a") relationships between classes. For example, each WormsSim instance has an arbitrary number of Worm instances and has a board. WormsSim has no “virtual” methods suitable for inheritance based polymorphic dispatch.
- WormsSim participates in the Mediator design pattern: https://en.wikipedia.org/ wiki/Mediator_pattern For example, Worm instances interact with other Worm instances and the board exclusively via WomsSim.

## Use of the Strategy Design Pattern

The abstract AbstractWormsSimUIStrategy class (similar to a Java or C# Interface or an Objective-C Protocol) declares the methods needed by any class that displays WormsSim state to a user and/or accepts user input. AbstractWormsSimUIStrategy collaborates as part of an implementation of the Strategy Design Pattern: https://en.wikipedia.org/wiki/Strategy_pattern The assumption is that many different concrete strategies for displaying WormsSim state to a users and accepting user input may exist. For example, there may be a terminal (Curses) based textual display or a 2D vector graphics display or a 3D display. Similarly, user input may come from a keyboard or over a network connection or from a data file or from script.

### Worm Class

The Worm class encapsulates a worm that lives, eats, moves, and eventually dies or is eaten. Worm instances collaborate with a WormsSim instance to interact with the WormsSim ’s board and other Worm instances. In every case, the interaction between Worm instances is mediated (Mediator design pattern) by an instance of WormsSim i.e. The only way a worm instance can influence another worm instance or the board is via the methods of WormsSim.

*Design Notes:*

- Following the principle of separation of concerns, this class intentionally does not have any "drawing" dependencies. It is anticipated that many different strategies for "drawing" a sim worm may eventually be implemented. This class is responsible for encapsulating worm state irrespective of drawing implemented elsewhere.
- Composition ("has-a") relationships are preferred over inheritance ("is-a") relationships between classes. For example, each Worm has a reference to a UniqueWormType and an arbitrary number of segments. An alternative design might provide an abstract "Worm" class and several distinct subclasses to encapsulate each different “kinds" of worm. However, when replicating the functionality of pmateti@wright.edu's 2013 worms.cpp, the only aspects of a Worm that vary by “kind” may be represented using composition (has-a relationships).

*Note:* A private “inner” class accessible only within the Worm class encapsulates all differences between worm types. The kind of a worm is hidden from and irrelevant to code outside the implementation of the Worm class. It could be argued that if inheritance based polymorphism was used to enable worm kind specialization, hypothetical future Worm subclasses might be implemented as subclasses of Worm, but that is hardly the only or best approach: Future "kinds" could be accommodated through template specialization, the Visitor design pattern, the Command design pattern, or the Adaptor design pattern all of which exist to minimize coupling between code specifically by avoiding unnecessary inheritance.

### CursesWormsSimUIStrategy Class

Instances of CursesWormsSimUIStrategy provide NCurses based graphical display and user interaction of WormsSim instances.

*Design Notes:*

- CursesWormsSimUIStrategy implements the interface specified by the abstract AbstractWormsSimUIStrategy class and participates in the Strategy design pattern to decouple display and user input from simulation encapsulation.
=0- CursesWormsSimUIStrategy adheres to the practical advice from Scott Meyers, “More Effective C++: 35 New Ways to Improve Your Programs and Designs 1st Edition” Item 33, “Make non-leaf classes abstract” as elaborated to use the Template Method design pattern in “Virtuality: C/C++ Users Journal, 19, September 2001” http://www.gotw.ca/publications/mill18.htm. 

## Entry And Exit Assertions for Public Methods And Class Invariants

See Worms simulation source code for assertions checking pre and post conditions within public methods and verifying class invariants. Reference the html/index.html file submitted with this document for detailed information and links to formatted source code.
Suggested Z Axis Enhancement

There are several display related challenges when depicting worm segments that may occlude each other from user view in any 2D rendering of a hypothetical 3D board. To be effectively displayed to users, the users would likely need the ability to pan, zoom, and rotate their perspective of the 3D board.

Currently, when a worm enters the board, all of the worm’s segments conceptually start at the same position in the board. This roughly simulates a worm crawling onto the plane of the board from a non-existent other plane. In other words, assuming the user visible board is in a plane defined by X and Y orthogonal Axis, an imaginary Z Axis points in and out of the display perpendicular to the XY plane. It’s almost as if the worm segments start aligned with the Z Axis and then one by one enter the XY plane. This imaginary behavior could be coded into reality. The simulation could be modified to let worms crawl back out of the XY plane and therefore out of user view as well.

Currently, a carrot is placed in every board square at the start of a simulation run. As a result, simulations start with copious amounts of food available to worms. If a hypothetical 3D board was also fully populated with carrots at the start of each simulation, the total amount of food available to works could be astronomical. Worms might be able to live indefinitely with so much food available.
