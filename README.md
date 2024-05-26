# Tower Experiment: A Template for OpenFAST Experimentation

This library provides a solution for managing experiments with the OpenFAST simulation tool [OpenFAST](https://github.com/OpenFAST/openfast?tab=readme-ov-file) using MatLab.  This code is a general template, fit to be modified for many different experiments.

# Description:

This library/framework solves the problem faced when running numerical experiments with OpenFAST; changing the input files in the appropriate manner takes a lot of effort.  With a large number of inputs, selecting only those pertinent to a particular study is difficult.  This library is a flexible, DIY inspired approach for running experiments.  It separates the structuring of the experimental design and the analysis of the results from the editing of the input files.  It is modular, using many helper functions which can be adapted to handle any input file type, enabling this framework to apply to different turbine setups and experiment designs.

The heart of the library are two files.
- `ExpTableGenerator.m`
- `ExpDriver.m`

Once the user has configured these files (and the helper functions), running the experiment and organizing the results for further analysis is simple.  Simply run the `ExpTableGenerator.m` then run the `ExpDriver.m`.

This framework streamlines OpenFAST input-output handling, while keeping the data organized.  The user specifies an experiment to run by giving the input (independent) variables and settings for each test in the experiment as a tablematrix/table (each row is a different test, each column a different input).  Then the framework will set up and run the simulations required, organizing the outputs channels (dependent variables) requested by the user into a table that mirrors the input table provided.  The result is a data-folder and a table of outputs from the experiment, in the same row-order as the input table.

# Instructions:

Note: Requires:
- MatLab
- OpenFAST excecutable (adjust the path in the `testdriver.m` file)
- Properly compiled DLL file (see the ServoDyn file)

Before running scripts, first copy the ***`Template_IEA-15-240-RWT-Monopile` folder and rename it `Simulate`***
Also, provide an inflow wind file to run tests on (and change `ExpTableGenerator.m` accordingly)

## Step by Step

Here we will explain how to go from desiging the input table, to modifying the helper functions, to handling how the outputs are organized into a final table.

### Step One: Table Generator 

The `ExpTableGenerator.m` file uses the selected OpenFAST inputs given to make an InputTable for each experiment.  The resulting table is saved as a text file and will be read by the `ExpDriver.m` script.  Each column represents an independent variable that will trigger a change in the files for OpenFAST to read.  Each test is a row of the table; a unique configuration of the inputs.  Taken together, all of the rows of the table will help to answer some question about wind turbine engineering.  Follow the comments within the table generator file in order to see where to make changes.

- Line 5: Determines the name of the experiment (must match the name given in `ExpDriver.m`
- Line 16: The *invarNames* refer to the independent variables, or columns in the experiment.  Some of them can be simply a part of the experimental set-up, and not variables at all.  Like in the given example, `windfileID` is the name of the wind file to simulate on
- Line 22: If wanted, the user can read in design points from another source and then do additional formating/manipulation in this file.
- Line 25: Determine the number of tests to be run.
- Line 27: This is the number of inputs (it could also be the number of independent variables).
- Line 46: We save the table to a text file.

### Step Two: How to Run a Simulation

The `ExpDriver.m` file takes the input table from `ExpTableGenerator.m` to set up and run OpenFAST tests.  The results will be found in a newly created Data folder in a sub-folder with the name given by the experiment's name.

- Line 8: This line sets the experiment name.  It must match the experiment name specified in line 5 of `ExpTableGenerator.m`.
- Line 10: This file name must match the template files for the desired OpenFAST experiment configuration (in this example the IEA-15-240-RWT-Monopile).
- Line 12: Set which row of the input table to start with and which row of the input table to stop after.  This allows for the same input table to be run in parallel on several CPUs at once.  (Though this would require additional post-processing to combine the results together at the end).
- Line 14: Set the number of seconds for each test.
- Line 16: Set the number of seconds from the end to start calculating things like mean, standard deviation, etc.
- Line 18: Set the test time step.
- Line 20: If true, then the `.out` file will be deleted to save memory.
- Line 22: If true, then the big result table (a copy of the time-series of each requested channel found in the `.out` file but as a text table) is deleted.
- Line 46: This is where the set-up begins, and where the OpenFAST executable is called.
- Line 50: Note, when running, this code generates a file that can be used to quickly access the contents of the experiment data subfolders.  This is helpful when performing quality checks or looking at results of individual runs.  It is also how this script creates the result folders and tables.
- Line 80: After the result tables for each test are made, then a list of output variables to be calculated is sent to the 'combineResults.m' function, which is found in the 'funcs' folder.  In this example, these are just names, but one could set these up as key words to automatically call different helper functions to explore different types of output analysis.
- Line 87: This line moves the statusFile to the experiment subfolder.

Now we can go in depth on some of the key helper functions to explain how they work and how they can be modified.

### Step Three: Set-Up Function



### Step Four: Anatomy of a Helper Function 



### Step Five: Output Channel Control



### Step Six: Setting up Driver



### Step Seven: Setting up the Parameters of the Result Table



### Process for Running

- Run `ExpTableGenerator.m` to generate a table of inputs for each test.
- Run `ExpDriver.m` to set up the `Simulate` folder and run OpenFAST on each test, gathering up the results from all the tests into a data table, before moving the data table and the `StatusFile.txt` to the experiment folder within the Data folder.

# Example: Morris Method Applied with 4 Inputs

This application is already set up in these files.  Follow the following steps to recreate results.
1. Run `ExpTableGenerator.m`
2. Run `ExpDriver.m`
3. Run ONLY the second section and beyond within `MorrisMethod.iypn`.  Follow the comments to make changes to look at different outputs.
