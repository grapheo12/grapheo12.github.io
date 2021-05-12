+++
title = "Simulating Motion under Gravitation"
date = 2020-07-20

[taxonomies]
tags = ["javascript", "p5.js"]
+++

![Even 2 body problems can lead to this](/img/chaos.png)

Let's be honest!
I am falling in love with the p5.js framework.
It makes my algorithms and simulations so beautifully presentable!
And that too with so minimum amount of boilerplate.

After building a [Mandelbrot set simulator](/blog/tech/mandelbrot-set/),
I thought, why to stop here, let's build something related to Physics,
which I loved very much during my +2 days.
Then the idea of creating the Gravitation simulator came into my mind.

<!-- more -->

## The Physics Involved

Gravitation comes under (classically) [Central forces](https://en.wikipedia.org/wiki/Classical_central-force_problem).

The magnitude of gravitational force between 2 point masses $m_1$ and $m_2$ separated by a displacement of magnitude $r$ is given by:

$$
F = G \cdot \frac{m_1 \cdot m_2}{r ^ 2}
$$

where $G$ is the [universal gravitational constant](https://en.wikipedia.org/wiki/Gravitational_constant), with value $$G = 6.674 \cdot 10 ^ {-11} m ^ {3} kg ^ {-1} s ^ {-2}$$.

Combining this with Newton's famous 2nd law,
the magnitude of acceleration $a$ of $m_1$ is given by:

$$
a = \frac{F}{m_1} = G \cdot \frac{m_2}{r ^ 2}
$$

Now, actually in space, every celestial body moves towards every other due to the Gravitation.
That'd indeed be an incredible multi-body dynamics problem to solve.
But our scope is pretty simple.
We'll just consider $m_1$ to be movable. Other bodies would be stationary.

Gravitation is always attractive (Shhhh! GTR fans).
What that means is, if, by some magic, $m_2$ was pinned down at a fixed point,
$m_1$ will always move TOWARDS $m_2$ with the acceleration given above.

Note that the acceleration is independent of $m_1$,
so whether we take a rock or a planet,
the path traced out by them would be the same.

## Approximations and rescalings

The Gravitation is very weak as a force.
Using $G$ directly in the simulation is not practical.
So for the simulation, I use a different scaling constant,
which I pick by hand
and constrain the acceleration values within some handpicked
bounds, to do away with the problem of division by zero,
when $r=0$.

Another cheating that I do in the simulation,
is that I keep all object's mass as 1.
For the simulator,
$1$ body of mass $m$, is the same as,
$m$ bodies of mass $1$ located at the same point.

I also work in 2D plane and decompose the accelerations
in X and Y axes using the slope of the line joining 2 bodies.

So, if $m_1$ (moving mass) is located at $(x_1,y_1)$ and $m_2$ (fixed mass) is located at $(x_2,y_2)$ ,
the components used are (with a scaling factor of 100):

$$
a_x = \frac{100 \cdot (x_2 - x_1)}{r ^ {\frac{3}{2}}}
$$

$$
a_y = \frac{100 \cdot (y_2 - y_1)}{r ^ {\frac{3}{2}}}
$$

I also clip $a_x$ and $a_y$ between some bounds.

The total acceleration of the mass $m_1$ is the component-wise sum of accelerations over
all the fixed masses. That is to say, at a given time:

$$
\vec{a}_{total} = \Sigma a_x \hat{i} + \Sigma a_y \hat{j}
$$

I'll drop the sum sign from here on.

The last approximation has to do with
how I calculate the velocity and position in the next timestep.

In reality, velocity would be a nice and smooth time integral
of the acceleration and the displacement would be a
time integral of the velocity.

Here I approximate integral by sum.

I take a small timestep $\Delta t$ (in simulation, it has value 1).
Then calculate:

$$
\vec{v}_{next} = \vec{v}_0 + \vec{a} \cdot \Delta t
$$

$$
\vec{r}_{next} = \vec{r}_0 + \vec{v}\_{next} \cdot \Delta t
$$

Of course, the calculations are done component-wise.

## The code, finally

With all the above considerations in mind,
let's jump right into the code.

{{gist(src="grapheo12/1b03fe4f8f43909e8ea7bcdfb798ed4e" file="index.html")}}

The `index.html` loads the `p5.js` library as usual.
Notice that I have created fields for initial position and velocity
as well as buttons for starting and resetting the simulation.

The main juice of the code is `sketch.js`:
{{gist(src="grapheo12/1b03fe4f8f43909e8ea7bcdfb798ed4e" file="sketch.js" >}}

- `particles` array contains the location of the fixed unit masses;
- `position` and `velocity` correspond to the moving mass.
- `started` and `stuck` are simulation states.
- `lines` contains the set of points along the path traced by the moving mass.

In the `draw` function, I check if the mouse is pressed WITHIN THE CANVAS,
I add that location to the `particles` array.

However if `started` is set to `true`, I no longer add bodies,
rather I proceed on calculating the next position at every frame.

Finally, I draw the fixed particles as green circles
and the moving particle as a red body which leaves a red trace.

This logic works fine, until the red body from very far off comes
very near a green body.
In this process it gains tremendous velocity and shoots
out of the screen.
To prevent that from happening,
the `stuck` variable is set to `true` whenever such stuff happens.
And once set to `true`, the particle's position is not updated,
it remains stuck.

This sticking behaviour is controlled by the metric in Line 40.
The value to which it is set in the code hardly has any effect.
Change the threshold to 1 or 10 to see the sticking effect.

## Bonus

So you just read the whole boring blog!
You deserve a bonus. (Or did you just click the Bonus link in the Contents!).

Here is the full fledged gravity simulator.
Play with it!
Share your interesting trajectories in the comments section.

<br>
<div style="display: flex; flex-direction: row;">
    <script src="https://cdnjs.cloudflare.com/ajax/libs/p5.js/0.10.2/p5.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/p5.js/0.10.2/addons/p5.sound.min.js"></script>
    <div style="display:flex; flex-direction:column;">
        <input type="number" max="600" min="0" id="posX" placeholder="posX">
        <input type="number" max="600" min="0" id="posY" placeholder="posY">
        <input type="number" max="1000" min="-1000" id="velX" placeholder="velX">
        <input type="number" max="1000" min="-1000" id="velY" placeholder="velY">
        <button onclick="start()">Start</button>
        <button onclick="reset()">Reset</button>
    </div>
    <div id="canvasDiv"></div>
    <script>
        let particles = [];
        let position = [100, 100];
        let velocity = [0, 0];
        let started = false;
        let stuck = false;
        let lines = [];
        let canvas;
        function start() {
            started = true;
        }
        function reset(){
            started = false;
            stuck = false;
            particles = [];
            lines = [];
        }
        function setup(){
            canvas = createCanvas(600, 600);
            canvas.parent("canvasDiv")
        }
        const timestep = 1;
        function draw(){
            let mindist = 100000000;
            background(200);
            if (!started){
                if (mouseIsPressed){
                    if (mouseX >= 0 && mouseX <= 600 && mouseY >= 0 && mouseY <= 600)
                        particles.push([mouseX, mouseY]);
                    console.log(particles)
                }
                position[0] = Number(document.getElementById("posX").value);
                position[1] = Number(document.getElementById("posY").value);
                velocity[0] = Number(document.getElementById("velX").value);
                velocity[1] = Number(document.getElementById("velY").value);
            }else{
                let acc = [0, 0];
                particles.forEach(elt => {
                    let dist_sqr = (elt[0] - position[0]) * (elt[0] - position[0]) + (elt[1] - position[1]) * (elt[1] - position[1]); 
                    if (dist_sqr / (velocity[0] * velocity[0] + velocity[1] * velocity[1]) < 0.1) stuck = true;
                    if (dist_sqr < mindist) mindist = dist_sqr;
                    acc[0] += constrain(10 * (elt[0] - position[0]) / (dist_sqr * Math.sqrt(dist_sqr)), -10, 10);
                    acc[1] += constrain(10 * (elt[1] - position[1]) / (dist_sqr * Math.sqrt(dist_sqr)), -10, 10);
                });
                if (!stuck){
                    velocity[0] += acc[0] * timestep;
                    velocity[1] += acc[1] * timestep;
                    position[0] += velocity[0] * timestep;
                    position[1] += velocity[1] * timestep;
                    lines.push([...position]);
                }
            }
            particles.forEach(elt => {
                fill(100, 200, 100);
                ellipse(elt[0], elt[1], 10, 10);
            });
            for (let i = 0; i < lines.length - 1; i++){
                stroke(255, 0, 0);
                line(lines[i][0], lines[i][1], lines[i+1][0], lines[i+1][1]);
            }
            stroke(0, 0, 0);
            fill(255, 0, 0);
            rect(position[0], position[1], 5, 5);
        }
    </script>
</div>

