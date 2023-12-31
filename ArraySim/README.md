# Array Simulation

This array simulation aims to simulate the power output of a solar car array across a variety of azimuth/elevation positions. It first generates effective irradiances on cells and simulates the electrical layout of the array via. a simulink model.

1. Generate an n x 3 csv that describes sun position and irradiances to simulate. The first two columns indicate azimuth and elevation respectively while the third column indicates irradiance in W/m^2. This can be done by either running ```dayAzElIrr.m``` in ./A in order to simulate the car's power input throughout a day or manually generating one. The former is generally used in order to compute array layout performances against one another and the latter is used in order to compute a lookup table for the selected array. 

    - Convention: Azimuth is taken to be degrees clockwise from the nose of the solar car. The stl of the car MUST be oriented such that the nose of the car points in one of the +/- x, y directions while the canopy must be pointing in the +z direction.

2. Generate an n x d csv that stores the irradiance in W/m^2 for each cell at each azimuth/elevation/irradiance combination in step 1. Canopy and array shading are taken into account by using an external ray-tracing algorithm in order to compute obstructions from the sun to each cell of the array. This csv can be generated by running ```./B/Run/main(257, "-x", "wscIrr.csv", "./ArraySTLs/canopy.stl", "./ArraySTLs/NA", "./wscAngles.csv")```. Before this, you must compile the mesh ray tracing library by going to ./src_matlab and running mexall.m which compiles the mex code and copies it to ./B/Run

3. Get total power output in W by feeding the output csv from step 2 into a custom simulink model that simulates either bypass diodes or mppt controllers. This simulink model must be custom made for each array layout in order to adhere to string configurations. The folders in step C are an example of a mppt configuration while the folders in step D are an example of a bypass diode configuration

# Limitations

- Canopy is interpreted as opaque in step 2. In practice, this isn't too big of an issue since a large amount of the volume would be blocked by the driver and rollcage.
- Solar cells are used for 100% of their area. However, most silicon cells have a very small gap between the edge of the cell and the actual edge of the silicon
- Step 3 does not factor in performance under temperature
- Step 2 is slow due to the inability to parallelize the .mex file