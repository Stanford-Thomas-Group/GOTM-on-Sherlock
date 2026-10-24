# GOTM-on-Sherlock
How to install and run GOTM on Sherlock. Much of this is taken from the GOTM github https://github.com/gotm-model/code


# Installation
## Create a GOTM directory
I recommend creating a directory for GOTM inside your personal folder in `$GROUP_HOME`.
```
mkdir gotm
cd gotm
```
Now clone the code from the github:
```
git clone --recurse-submodules https://github.com/gotm-model/code.git
```

We need to load the necessary modules. Make sure you start from a clean environment to avoid any conflicts. 
```
ml --force purge
ml devel netcdf netcdf-fortran cmake ninja
```
You can check that your modules are correct by typing `ml list`, you should see: 
```
Currently Loaded Modules:
  1) devel         (S)   5) netcdf/4.8.1           9) munge/0.5.16          13) szip/2.1.1                17) cmake/3.31.4
  2) system              6) cuda/11.5.0      (g)  10) openmpi/4.1.2  (g,m)  14) libxml2/2.9.4             18) gcc/14.2.0
  3) openssl/3.0.7       7) ucx/1.12.1       (g)  11) pnetcdf/1.12.3 (m)    15) netcdf-c/4.9.0       (m)  
  4) curl/8.4.0          8) libfabric/1.14.0      12) hdf5/1.12.2    (m)    16) netcdf-fortran/4.5.4 (m)
```

## Option 1: Ninja and CMake  
First, load the ninja module with `ml ninja`. In your `gotm` directory, type
```
cmake -G Ninja -B build -S code -DGOTM_USE_FABM=off
```
If you don't plan to do biogeochemical modeling, don't install FABM as it takes up extra space. 

This creates a new folder `build` with the necessary information for compiling the code.
If something goes wrong it is always safe to completely remove the build folder and start all over.

## Option 2: CMake only 
Create a `build` directory and navigate into it
```
mkdir build
cd build
```
Run CMake. If you don't plan to do biogeochemical modeling, don't install FABM as it takes up extra space. 
```
cmake ~/gotm/code -DGOTM_USE_FABM=off
```

## Compiling the code

If the cmake command did not result in any errors we are ready to actually compile the code into an executable. In your ~/gotm directory
```
cmake --build build
```
This will take a little while but you should end up with a compiled GOTM executable in the build folder. 

# Installing the GOTM executable 
The installation also is done by CMake:
```
cmake --install build --prefix code
```
Test if it works by calling the gotm executable:
```
~/gotm/code/gotm 
```
You should see something like the following:
```
 ------------------------------------------------------------------------
 GOTM started on 2026/10/23 at 16:59:46
 ------------------------------------------------------------------------
    initialize_gotm
 ------------------------------------------------------------------------
 FATAL ERROR: Configuration file gotm.yaml not found.
    To generate a file with default settings, specify --write_yaml gotm.yaml.
    For more information, run gotm with -h.
STOP 2
```
You can install it in any folder, but for convenience you might want to install it in a folder in the PATH - in which case you can just type - gotm - in any folder.
```
cmake --install build --prefix <folder_in_path>
```
To test if it works open a new terminal window and execute - `gotm`.

# Running Example Code
First, we want to clone the examples repository into our gotm folder.
```
git clone --recurse-submodules https://github.com/gotm-model/cases.git
```
Now, navigate into one of the example folders and run the executable
```
cd cases/ows_papa
gotm
```
For this particular case, output is stored in `ows_papa_keps_meteo.nc`
The name of this file corresponds to the turbulence closure (k-epsilon) and the surface forcing used (meteo = observations from Station Papa)
By default, GOTM is configured by the `gotm.yaml` file in the folder where the executable is called.
You can run the simulation with different input parameters by specifying a `.yaml` file. For example:
```
gotm gotm_kw_era5.yaml
```
Will run the simulation with a k-omega closure and forced with ERA 5 data. The output is stored in `ows_papa_kw_era5.nc`

Note that the cvmix examples will not work with the build. For that we need to include the CVMix module when building GOTM by including 
```
-DGOTM_USE_CVMIX=true
```
when running CMake.

More info at https://gotm.net/blog/cvmix/





