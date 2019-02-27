### "Sorry, something went wrong. Reload?"

As it turns out, GitHub sporadically has trouble rendering notebooks. 
This is a recurring problem with a lot of users, but fortunately, this issue eventually fixes itself; you just have to wait.
In the meantime, you can use [nbviewer](https://nbviewer.jupyter.org/) to view the notebooks. 
Click the nbviewer link, paste the URL of the notebook that won't render into the search bar, and then hit "Go!".
That should do the trick!

# Shuttle-Launch

**The Question:** Let's say we know the basic information about one of the United States' glorious space shuttles. Is it possible for us to develop a launch maneuver that would get the shuttle into orbit--more specifically, in orbit with the International Space Station--using fairly accurate kinematic models? 

**The Answer:** A resounding "Yes!" See the Shuttle_Simulation.ipynb file for an example simulation (and a detailed explanation of the process). Future considerations and inaccuracies are discussed in the "Discussion" section at the end of this readme.

## File descriptions

@todo

## Basic assumptions/definitions

* We set the origin as the center of earth.
* We approximate the earth as a sphere (the telltale sign of a true physicist).
* Parameters x and y are relative to the origin (not relative to the earth’s surface). Hence, (0 m, 0 m) is not recognized as a point on the earth’s surface, while (0 m, 6371e3 m) is recognized as a surface point (the radius of earth is 6371 km).
* Launches occur at the equator.
* The shuttle orbits clockwise around earth.
* The axes are positioned such that the earth's rotation is purely in the xy plane.
* The earth's rotation is also clockwise.

## Shuttle specifications

Please note that these shuttle's specifications are in fact accurate values for a real space shuttle used by the United States. All specifications listed below can be found on the [this Space Shuttle Wikipedia article](https://en.wikipedia.org/wiki/Space_Shuttle) or in links on the sidebar of that Wikipedia article. Thrust mechanisms were inspired by information contained in [this Rocket Engine Wikipedia article](https://en.wikipedia.org/wiki/Rocket_engine).

* The shuttle's cross-sectional area is assumed to be circular with a radius of 8.7 m.
* The shuttle's drag coeffient $C_d$ is 0.1.
* The shuttle's mass is 80,000 kg.
* The mass of the shuttle's primary boosters is 1,200,000 kg.
* The mass of the shuttle's ssecondary boosters is 100,000  kg.
* Primary boosters burn for a total of 127 seconds.
* The force from the secondary boosters is 5,000,000 N.

We leave the following two "specifications" as parameters:

* The force from the primary thrusters. We let the user input whatever thrust force they want.
* How long the secondary boosters burn. In this simulation, the burn time for the secondary boosters depends on how much thrust was provided by the primary boosters. If the primary boosters give a lot of thrust, the secondary boosters won't need to burn for that long, but if the primary boosters give a bit less thrust, the seconday boosters will need to burn on for a bit longer. Hence, it is possible for the shuttle to make it into orbit without usin up all of its secondary fuel.
## A brief description of all functions

Function list:
* **rk4_step and rk4:** These functions work together to integrate a given function using the fourth-order Runge-Kutta method. A full explanation and derivation of this algorithm is discussed in the textbook.
* **force_gravity:** This function calculates the force the shuttle experiences due to gravity. First, the force magnitude is calculated, and then it is split into its x and y components.
* **air_density:**	This function returns the air density at a certain radial height above earth’s surface. An equation modeling air density as a function of height pulled from Wikipedia is used here. The equation is slightly modified—shifted so that it works with our coordinate system
* **force_drag:**	This function calculated the force the shuttle experiences due to drag. The x and y components are calculated explicitly. This function calls the air_density function, since the drag force depends on air density.
* **acceleration:**	This function sums up all of the forces and divides this net force by the mass of the shuttle (which is changing in time). Since the forces are returned in x and y components, the acceleration is also returned in x and y components. 
* **mass:**	This function returns the mass of the shuttle at a given time. The majority of the shuttle's mass is due to fuel stored in its primary and secondary boosters. When these fire, the fuel is burned, and hence the shuttle's mass will depleted over time. This is a piecewise function. There are two functions that bear this title, and they serve different purposes.
	* The first mass function to appear does not burn the secondary fuel. This function is used only to help determine when to start and stop the secondary boosters (See approximate_boost_info). Once the start/stop time is calculated, we can run the final simulation using this information, which will allow the shuttle to reach orbit
	* The second mass function does burn the secondary fuel. This function is used in the final simulation that plots trajectory of the shuttle during launch and orbit. 
* **approximate_boost_info:** This function runs the simulation without firing the secondary boosters in order to estimate when the secondary boosters should start and stop. The built-in odeint function is used to integrate during this simulation. A thorough explanation is provided in a markdown cell just before the function in the notebook.
* **F:** This function sets up our ode system for integration. 
* **print_init:** This function simply prints out all relevant constants and initial conditions of the launch.
* **test:** This function runs the final simulation and plots a variety of relations, including the trajectory (2D and 3D) and various velocities vs time. It calls all of the functions above and integrates using the fourth-order Runge-Kutta method. 

## ODE system

Please see Shuttle_Simulation.ipynb for the ODE system.

## Discussion

Generally speaking, the results were solid; the we were able to get the shuttle into orbit, and we were able to do so for a wide range of primary thrust forces. However our orbital heights were significantly higher than expected. When we simulated a launch with specifications similar to that used for a mission to the International Space Station, we got about 1000 km higher than the height of the ISS!

What's causing that? The next section offers much insight into that question. In brief, it is due to a simplification in the forces acting on the shuttle. In reality, the forces are significantly stronger. 
The next logical question to ask is why does the orbital radius vary sinusoidally in time? The first explanation that came to mind was that this might be due to approximations made during integration. However, this proved to be incorrect. We did a rerun of simulation 1 but with three times as many points and the results did not change at all. So, there must be something else causing these relatively small oscillations.

As it turns out, the secondary thrusters are the source of these oscillations. Now would be a good time to look at the markdown cell in the code explaining the approximate_boost_info function. 

The start and stop time for the secondary boosters were calculated based off of the maximum radial height achieved without them. What we did not account for was the fact that firing the secondary boosters cause a tangential acceleration which in turn causes the radius to increase as well. 

In brief, the maximum radial height achieve with the secondary boosters is greater than that achieved without them. So, the shuttle is traveling at a speed required for a circular orbit at a height slightly smaller than it's actual height. Hence, the orbit is slightly elliptical. This isn't a huge problem, because all real orbits are at least slightly elliptical. This also explains why the radial velocity oscillates around 0 and why the tangential velocity oscillate around ~7500 m/s.

## Future Directions

With this project we were able to achieve a stable orbit from being given a force for the primary thrusters.  All other variables are determined from that number, reaching any number of different orbit heights.  What would make the program more usable in a situation would be to let an orbital height be inputted by the user.  The program could be made to calculate the minimum of fuel needed in the primary and secondary thrusters to reach the stated height and required velocity for orbit.  This would involve many more lines of code along with changing up how the entire process works, but it does seem doable. This would be a much more logical thing to do—input our goal and calculate the required specifications rather than inputting the specifications and calculating the results.
	
There were other variables that we more or less ignored in order to pursue the goal of actually getting the shuttle into orbit, either because it was too complicated for us to code or we didn’t know how it worked.  One was the presence of atmospheric density, which would apply additional drag on to the shuttle.  We only accounted for the air density within the troposphere, not the layers of atmosphere we must pass through when exiting the atmosphere.  

Another was our failure to account for how the drag changed once the shuttle had hit supersonic speeds, as the amount of drag no longer behaves regularly and is instead stronger by a large margin.  The drag coefficient increases and peaks and Mach 1, then decreases and levels out at .4.  This is still stronger than the drag coefficient we were using the entire time.  Doing this would involve us choosing which drag coefficient was being used, based on the velocity, along with creating a function for its calculation.

The general launch process was unrealistic. When our primary thrusters ran out of fuel, we made it as if its container "burned" too. In reality, just the fuel would burn, and the containers would be jettisoned afterwards. So, the decreasing mass was not accurately implemented. Furthermore, fuel was not continuously burned. There was a large gap between when the primary thrusters stopped and the secondary thrusters started. This is simply unrealistic. In real launches, boosters are continuously fired and they do not stop firing until the shuttle is actually in orbit.

On the topic of extra variables we didn’t account for is that we treated the earth as a uniform mass for the sake of gravity calculations.  This is not how the earth is set up, since its's density changes throughout.

Another thing that could have been done better were we not generalizing so many of the variables, is the tilting of the shuttle.  For our system the shuttle begins its slow tilt the moment it launches, becoming horizontal once the primary thrusters have fully run out of fuel.  In reality it is a much more hyperbolic function, barely tilting at all during the first stage of the shuttle before the solid fuel booster is released from the system.  The variance between our systems implemented is great, mostly due to the fact that our shuttle, although simple, is horribly inefficient.  This more complicated and realistic process is something that would have to be implemented if fuel conservation were ever to be a concern.
