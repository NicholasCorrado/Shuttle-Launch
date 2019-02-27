### "Sorry, something went wrong. Reload?"

As it turns out, GitHub sporadically has trouble rendering notebooks. 
This is a recurring problem with a lot of users, but fortunately, this issue eventually fixes itself; you just have to wait.
In the meantime, you can use [nbviewer](https://nbviewer.jupyter.org/) to view the notebooks. 
Click the nbviewer link, paste the URL of the notebook that won't render into the search bar, and then hit "Go!".
That should do the trick!

# Shuttle-Launch

This is a fun project where try to launch a space shuttle into 

## File descriptions



## Important assumptions

* We set the origin as the center of earth.
* We approximate the earth as a sphere (the telltale sign of a true physicist).
* Parameters x and y are relative to the origin (not relative to the earth’s surface). Hence, (0 m, 0 m) is not recognized as a point on the earth’s surface, while (0 m, 6371e3 m) is recognized as a surface point (the radius of earth is 6371 km).
* Launches occur at the equator.
* The shuttle orbits clockwise around earth.
* The axes are positioned such that the earth's rotation is purely in the xy plane.
* The earth's rotation is also clockwise.

## A brief description of all functions

Function list:
* **rk4_step and rk4:** These functions work together to integrate a given function using the fourth-order Runge-Kutta method. A full explanation and derivation of this algorithm is discussed in the textbook.
  *	**force_gravity:** This function calculates the force the shuttle experiences due to gravity. First, the force magnitude is calculated, and then it is split into its x and y components.
*	**air_density:**	This function returns the air density at a certain radial height above earth’s surface. An equation modeling air density as a function of height pulled from Wikipedia is used here. The equation is slightly modified—shifted so that it works with our coordinate system
*	**force_drag:**	This function calculated the force the shuttle experiences due to drag. The x and y components are calculated explicitly. This function calls the air_density function, since the drag force depends on air density.
*	**acceleration:**	This function sums up all of the forces and divides this net force by the mass of the shuttle (which is changing in time). Since the forces are returned in x and y components, the acceleration is also returned in x and y components. 
*	**mass:**	This function returns the mass of the shuttle at a given time. The majority of the shuttle's mass is due to fuel stored in its primary and secondary boosters. When these fire, the fuel is burned, and hence the shuttle's mass will depleted over time. This is a piecewise function. There are two functions that bear this title, and they serve different purposes.
  *	The first mass function to appear does not burn the secondary fuel. This function is used only to help determine when to start and stop the secondary boosters (See approximate_boost_info). Once the start/stop time is calculated, we can run the final simulation using this information, which will allow the shuttle to reach orbit
  *	The second mass function does burn the secondary fuel. This function is used in the final simulation that plots trajectory of the shuttle during launch and orbit. 
*	**approximate_boost_info:** This function runs the simulation without firing the secondary boosters in order to estimate when the secondary boosters should start and stop. The built-in odeint function is used to integrate during this simulation. A thorough explanation is provided in a markdown cell just before the function in the notebook.
*	**F:** This function sets up our ode system for integration. 
*	**print_init:** This function simply prints out all relevant constants and initial conditions of the launch.
*	**test:** This function runs the final simulation and plots a variety of relations, including the trajectory (2D and 3D) and various velocities vs time. It calls all of the functions above and integrates using the fourth-order Runge-Kutta method. 

