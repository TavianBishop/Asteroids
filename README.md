Asteroids

A browser-based recreation of the classic Asteroids arcade game, built from scratch in vanilla JavaScript using the HTML5 Canvas API. No frameworks, no libraries, just <canvas>, requestInterval-style game loop math, and trigonometry.

Play it live

Gameplay

Pilot a ship through an asteroid field, blast rocks into smaller pieces, and rack up points before you run out of lives. Asteroids wrap around the screen edges, and destroying one splits it into two smaller, faster-moving fragments until they're small enough to disappear entirely.

ActionKeyRotate leftLeft ArrowRotate rightRight ArrowThrustUp ArrowShootSpacebar

Your high score is saved locally in the browser, so it persists between sessions.

Features


Physics-based movement — acceleration, momentum, and configurable friction instead of fixed-speed movement, so the ship drifts and coasts like it would in space.
Procedurally generated asteroids — each asteroid is a randomly jagged polygon (randomized vertex count and offsets) so no two look identical.
Splitting asteroid mechanic — large asteroids break into medium ones, medium into small, each tier worth more points and moving faster.
Collision detection — circle-based hit detection between lasers/asteroids and the ship/asteroids.
Lives, invulnerability & blinking — after respawning, the ship is briefly invulnerable and blinks to signal it, a nod to the original arcade feel.
Explosion animations — layered, fading circles simulate a ship/asteroid explosion rather than just disappearing.
Leveling system — clearing all asteroids on screen increases the asteroid count and speed for the next level.
Persistent high score — stored via localStorage so it survives page reloads.


Built with


HTML5 Canvas API for all rendering (no DOM elements for game objects)
Vanilla JavaScript — no external libraries or frameworks
A setInterval-driven game loop running at a fixed 30 FPS


Technical implementation

Game loop

The game runs on a fixed-timestep loop via setInterval(update, 1000 / FPS), with FPS set to 30. Every tick, the update() function:


Clears the canvas by repainting a black background rectangle
Applies thrust/friction to update ship velocity
Redraws the ship (or its explosion, if mid-collision)
Draws and advances all active lasers
Checks laser-asteroid and ship-asteroid collisions
Draws and moves all asteroids
Wraps any object that crosses a screen edge to the opposite side
Renders score, high score, lives, and any active on-screen text (e.g. "Level 2", "Game Over")


Keeping all state mutation and rendering inside one tick-driven function avoids the need for a virtual DOM or external state library — at 30 FPS the redraw cost is trivial for this scene complexity.

Ship movement

The ship doesn't move at a fixed speed in the direction it's facing. Instead, pressing the thrust key adds a small acceleration vector (SHIP_THRUST) along the ship's current heading angle, while a friction constant continuously decays existing velocity:

jsship.thrust.x += SHIP_THRUST * Math.cos(ship.a) / FPS;
ship.thrust.y -= SHIP_THRUST * Math.sin(ship.a) / FPS;

This is what gives the ship its drifting, momentum-based feel rather than the snappy stop-on-a-dime movement you'd get from directly setting position.

Asteroid generation & jaggedness

Each asteroid is a randomly generated polygon rather than a perfect circle. The game picks a random number of vertices (ROIDS_VERT), then for each vertex applies a randomized radius offset bounded by a ROIDS_JAG (jaggedness) constant. The polygon is drawn by walking those vertices around a circle of evenly spaced angles, scaled by their individual offsets — producing organic, irregular rock shapes from a small amount of math.

Splitting & collision detection

Collision checks use simple circle-based distance detection (distBetweenPoints) rather than pixel-perfect or polygon collision, comparing the distance between two objects' centers against the sum of their radii. This is cheap to compute and accurate enough given the visual scale of the game.

When a laser hits an asteroid:


Large asteroids split into two medium asteroids
Medium asteroids split into two small asteroids
Small asteroids are destroyed outright


Each tier awards a different point value, with smaller (harder to hit) asteroids worth more.

Screen wraparound

Rather than walls or a bounded camera, every object (ship, asteroids, lasers) that exits one edge of the canvas is repositioned to enter from the opposite edge, recreating the toroidal "wrap-around" space of the original arcade game.

Invulnerability & blinking

After respawning, the ship is invulnerable for a set duration and visually blinks on and off at a fixed interval to signal this state to the player, preventing instant repeat deaths while keeping the rule visible rather than hidden.

Running locally

Since this is a single self-contained HTML file with no build step or dependencies, you can run it by simply opening the file in a browser:

bashgit clone https://github.com/tavianbishop/asteroids.git
cd asteroids
open index.html   # or just double-click the file

Possible future improvements


Sound effects for shooting, thrusting, and explosions
Mobile/touch controls
A start screen and pause functionality
UFO enemies, as in the original arcade game
