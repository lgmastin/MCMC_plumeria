# MCMC_plumeria
Scripts for setting up and running Monte Carlo simulations of plumeria_wd

# Description
This repository contains the following scripts that can be used to set up an run large numbers of plumeria_wd simulations in parallel and compile the results into a table.  Source code is in the directory "src", input files used for an example run are in the directory "input_files", and example output, with subdirectories, are in the folder "example_output"

# Source code.
ESP_generator.py  writes a table of inputs, one row row each plumeria run.  Some of the inputs, particularly mass eruption rate and exit velocity, are randomly chosen using a uniform probability distribution funciton.

MakeInput.f90 is a Fortran program that reads the output table generated by ESP_generator.py, and writes an input file that can be read by plumeria_wd.  The execuatalbe is called using a runtime argument that is the run number.

read_output.py  is a python script that reads an output file generated by plumeria_wd, extracts key inputs and outputs such as mass eruption rate, total plume height, neutral buoyancy height etc. and writes them to a summary table.  Runtime execuation takes two arguments: the run number, and the name and location of the summary table.

MCMC_plumeria.sh is a bash shell script that sets up run directories, calls MakeInput to generate input files in those directories, and runs plumeria_wd in parallel each directory.  Once the runs are finished, it moves the output files to specific output directories, and calls read_output.py to add results to the summary table.

# Example input files
gfs_sounding.txt is a meteorological sounding which is read by plumeria_wd during each run.  If such a sounding is used in the plumeria runs, MCMC_plumeria.sh creates a soft link to this file in each run directory.

input_table_H2O0.00_mg0.03.txt   example table of inputs created by ESP_generatory.py.

#Example output files
Within the directory "example_output", the subdirectory "H2O0.00_mg0.03" was created for one set of simulations.  The name of this directory was specified in MCMC_plumeria.sh on or near line 43.  Subdirectories named "output_files", "run_logs", and "summary_table" are automatically created by MCMC_plumeria.sh, and files moved to them after each set of simulations.  

H2O0.00_mg0.03/output_files contains plumeria output files, named "Run00001.txt", "Run00002.txt" etc., after the run number
H2O0.00_mg0.03/run_logs contains plumeria run loggs, named "Run00001.txt", "Run00002.txt" etc., after the run number
H2O0.00_mg0.03/summary_table contains the summary table compiled by read_output.py

# Model setup
Running these simulations requires a compiled executable of plumeria_wd.  It has the name plume_wd and is assumed to be in some folder that we'll call ${PLUMEPROG}.  For the MCMC simulations, a good practice is to set up a directory (we'll call it ${MAINDIR}) where souorce files, run directories, and output directories can reside.  Within that directory should be three subdirectories:  "scripts", "RunDirs", and "run_output".  Here's the procedure for creating the directories and setting up the process:

1. Create the directories
  mkdir ${MAINDIR}
  cd ${MAINDIR}
  mkdir scripts RunDirs run_output
  cd scripts                                 # move to the file "scripts" and clone this repository
  git clone https://github.com/lgmatin/MCMC_plumeria

2. Create a new input summary file
     --move ${MAINDIR}/scripts/src and start modifying scripts to run on your system:
  cd ${MAINDIR}/scripts/src
  vi ESP_generator.py          
     --Within ESP_geneator.py, modify lines 9-16 and 27-31 to your desired input parameters.  Then run the script
  python ESP_generator.py
     --This will write a new input file to the directory "input_files".

3. Modify MakeInput.f90 to read from the new summary file
  vi MakeInput.f90
     --Line 25 specifies the name of the input file to read.  Change this to your input file.  Then recompile, and test
  gfortran -o MakeInput MakeInput.f90
  ./MakeInput 00001
     --This should 
        --cate a new input file in the current directory, and write out a line of inputs to sdout.  Verify that they agree with the inputs specified for run #1.

###############################  4. Modify MakeInput.f90 to read from the new summary file  ##########################



