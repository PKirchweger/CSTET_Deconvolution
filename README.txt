Instructions how to run the Deconvolution script: 


1. Naming of the files:

The script looks for a specific pattern in the filename: currently (19.01.2022) this is: '*_rec.mrc'
Tomogram: 
- thisisthetomogram_rec.mrc
Tilt file (for single-axis tomograms):
- thisisthetomogram_rec.tlt
Tilt files (for dual-axis tomograms):
- thisisthetomogram_rec_a.tlt
- thisisthetomogram_rec_b.tlt
patchcorr log file: 
- thisisthetomogram_rec_patchcorr.log


2. Generate a PSF: 
The script looks for the PSF file (called PSF_'PixelSize'nm.mrc in a folder called "PSFs" in the defined homedirectory. 

if the pixelsize of the tomogram is 40.84 A (or 4.084nm), the PSF is called:
- PSF_4.084nm.mrc

The new version only needs a PSF with the size of 1x1x1000 pixels (This makes the script faster (The limit is now only the deconvolution step)). 

##############
Create a PSF in Fiji:
The plugin we've used is 'Deconvolution PSF 3D'. 

In the menu, 
index of refraction 1.000
Numerical Aperture 0.0012 (PK: equivalent to the convergance angle)
Wavelength 0.002 for 300 kV, 0.0025 for 200 kV
Longitudinal Spherical Aberration, leave at 0
Image pixel spacing <set, e.g., to the pixel size of the tomogram in nm/pixel> 
Slice spacing (same as above)
Width, Height, Depth (to match the tomogram, NEW: 1x1x1000 pixels)
Normalization Sum = 1
Title PSF_PixelSize_1x1x1000.mrc

This PSF will be suitable for all imaging done under the same conditions. 
#############

3. BEFORE running the script: 

- Adapt the input_values.txt: currently (12.12.2022) this looks like this:

Dual_Axis = Yes
Number_of_Iterations = 100
Smoothingfactor = 0.001,0.005,0.1,1
Auxiliary_smoothing = 200
Do_summed_FFT = No
Do_summed_PS = Yes

### EMU1 and EMU2 each have 376 GB Memory, em-unit has 264 GB
Memory_MB = 350000



- check if Priism is active
- check if IMOD is active 
- (I IS IMPORTANT TO LOAD PRIISM FIRST AND IMOD SECOND, BECAUSE THE SCRIPT RELIES ON THE IMOD HEADER PROGRAM!!!)
- change the scriptname in the script in the "input_files" part
- make sure to use python 3(.9.5)


4. Running the script: 

We use nohup to have it run in the background. Command: 
nohup python deconscript.py > yourlogfile.log


5. While running the script

The script creates a folder 'deconv_thisisthetomogram_rec' and saves all the resuling files in this folder.
It copies the following files to the folder: 
- the script 
- the input_values.txt
- the tomogram
- the .tlt file(s)
- (in case of the dual-axis deconvolution: the patchcorr log file)

If there are problems:
The script writes a logfile called decon_thisisthetomogram_rec.log where it saves the commands and the important details of the individual runs. If the script is executed as written above, a second logfile of the whole run is written. Check both of these logfiles to find out where it crashes. 


6. After running the script: 

Files saved by the script: 
- decon_thisisthetomogram_rec.log: This is the log file
- FanOut_thisisthetomogram_rec.mrc: This is the summed rotated PSFs
- FanOut_thisisthetomogram_rec.otf: This is the FanOut translated into Fourier space
- thisisthetomogram_rec_inv_scaled.mrc: the inverted and scaled tomogram
- decon-sSMOOTH-ncITER-lamsAUXSM-thisisthetomogram_rec.mrc: the deconvolved tomogram
- plotfile-sSMOOTH-ncITER-lamsAUXSM-thisisthetomogram_rec.mrc: a plotfile written during the deconvolution
- avg_2D_FFT*: if Do_summed_FFT = Yes, then the script calculates a summed FFT of the input tomogram and each of the deconvoluted tomograms (Please note: if there are an uneven number of z-slices, FFT doesn't work, only PS works)
- avg_2D_PS*: if Do_summed_PS = Yes, then the script calculates a summed powerspectrum of the input tomogram and each of the deconvoluted tomograms (this is a good measure if the Fanout looks good, can be overlayed e.g. in ChimeraX)






