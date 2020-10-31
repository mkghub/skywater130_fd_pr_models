# skywater130_fd_pr_models
  Skywater-pdk spice models for opensource spice simulators Ngspice and Xyce. Modifications from https://github.com/google/skywater-pdk/tree/master/libraries/sky130_fd_pr

I have did some experiments on the spice files without 'option scale'. And have done some test simulations. Manual editing is required for some files, so the files in this repository are not complete and may have some hidden errors. So some regression tests are needed to have a confidence.


----------------------------------------------------------------------------------------------------------------------------------------------------
Processes for obtaining this files.
  - removed option scale statements
  - cell directory into one directory
  
    Anyway, I have never seen a spice model directory in a PDK with so large number of files.
  There are so many subfolders in 'cell' directory. Merged all the spice files into one directory. And moved the resulting new 'cell' directory under the 'model' directory. There are so many unnecessary files. Selecting really needed files only is needed.
  - Parameter scale changes
  
       - MOS
    
         parameter : w, l, ad, as, pd, ps, sa, sb, sd 
    
         There are more interim paramaters to modify in the equations for high voltage devices.
    
       - Bipolar 
   
         Area
  
       - Diode 
     
         Area, perim  
  
       - MIM 
  
         w, l
  
       - Resistor model equations
  
         The equations are somewhat complex for poly resistor models.
         I have commented out equations for statistical variations for clarity.
         The voltage dependence is commented out, since Xyce does not allow dynamic variables.
    
         And some model files are not complete and only give constant values. generic_nd and generic_pd models, for example, seems to be incomplete model files.
  
  - 'temp' variables changed to 'temper'
  In the input file, some statement such as the following is needed.
    
      .param temper=27
  
      This is only a temporary solution.

- 'vt' variable is used as a parameter variable in one file. This needs to be modified to 'vt0'.
  - paths into absolute paths
  - In line comments character '$' changed to ';' to support both tools and hspice, etc.
  
- Diode model
  - Level = 3.0 model is not supported for Xyce. Temporal solution is changing to Level=2.0
  






----------------------------------------------------------------------------------------------------------
  Note for Xyce compatibilities
  
  Summarizing again, there are a few compatibility issues remaining, after solving the option scale problem. 
Maybe developers of Xyce can give hints for best solutions?

- Issue 1:  Temperature variable

'temp' variable cannot be used in the equations.
A temporal solution is modifying 'temp' to 'temper' in the equations and defining 'temper' as a parameter on the input file. This method is OK for doing simulations on a fixed temperature but not OK for temperature sweep.
voltage dependent resistor models
Node voltages cannot be used in equations. In XDM manual, it is said that the solution for this problem is using behavioral current source models. For now I don't know how to incorporate both temperature dependence and voltage dependence. If we can neglect the voltage dependence of poly resistors it is OK.

- Issue 2:  Diode model don't support level 3 model

In diode model files, level = 3.0 is defined. For Xyce there's only level 1 and 2.

- Issue 3:  Environment variable in include path statement

For hspice and specter, an environment variable can be used in include path statements. For Xyce I don't know how to do it.
For example
.inclue "$sky130_fd_pr/models....."
works for hspice and specter but Xyce refuses it.
A temporary solution which I am using for now is defining symbolic link under the simulation directory such as 'sky130_fd_pr' and using it in the spice files.
.include "sky130_fd_pr/models..."

- Issue 4: 'vt' variable

This is a trivial problem. In one file, 'vt' is used as a parameter variable. This needs to be modified to something like 'vt0'.

- Issue 5: In line Comments

Changing '$' to ';' is OK for ngspice, Xyce, and hspice, etc.

- Issue 6: Model Binning

This is not an issue but can be an obstacle for a beginner.
The following statement is needed in the input file for using binning models.
.options parser model_binning=true
