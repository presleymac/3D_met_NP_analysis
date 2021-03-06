# 3D_met_NP_analysis
Image processing scripts written to analyze physiology and nanoparticle delivery to micrometastases. Published by Kingston et al. in "Assessing micrometastases as a target for nanoparticles using 3D microscopy and machine learning” . 2019. 

The following image analysis functions are used to analyze the mean nanoparticle intensity and cell distance to the nearest blood vessel from 3D flourescent lightsheet microscopy images. The workflow is broken down into the following steps:

# Nuclei, blood vessel and micrometastasis segmentation
Step 1: Conversion of .czi files into multipage .tiff images of the individual microscopy channels. 

The function 'convert_czi_to_ometiff_revised_func' is used to convert any files with the .czi extension into a downsampled multipage tiff image. The image is downsampled in the x and y resolution to achieze isotropic resolution with the z dimension. This facilitates downstream analysis by reducing the image size. The input for this function is 'data_folder' - the directory containing the .czi files. 

Step 2: Pre-processing: Local and global intensity corrections of the nuclei, blood vessel and micrometastasis channels. 

The function 'pre_process_func' is used to normalize the flourescent intensities across the image volume for the nuclei, blood vessel and micrometastasis channels. The inputs required are: 'nuclei' - a multipage tiff, or similar of the original nuclei channel, 'vessels' - a multipage tiff or similar of the original blood vessel image, 'micromets' - a multipage tiff or similar of the original micrometastasis image, 'save_dir' - the directory where the ouput files are saved, and 'sample_name' - the name of the sample to be processed. The ouputs are 'pre_nuclei', 'pre_vessels' and 'pre_micromet' - written as multipage tiff files of the intensity normalized nuclei, blood vessel and micrometastasis channels respectively. 

Step 3: ilastik image segmentation: Ilastik uses a random forest classifer based on 37 filters to segment nuclei, blood vessels, and micrometastses from the pre-processed nuclei, blood vessel and micrometastasis images. 

More information on ilastik can be found at: https://www.ilastik.org/ or https://github.com/ilastik. The pixel classication tool was trained for at least 30 mins using all 37 default image filters with each of the pre-processed nuclei, blood vessel and micrometastasis images. Once satisfactory performance was seen for the binary segmentation of each of the nuclei, blood vessel and micrometastasis channels this segmentation was batch applied to other images aquired from the same tissue and animal. The output is a labelled segmetnated images of each of the nuclei, blood vessel and micrometastasis channels as multipage tiff files. 

Step 4: Post-processing: Elimination of artifacts generated by the ilastik segmentation for the nuclei, blood vessel and micrometastasis channels. 

This function removes artifacts that appeared after the ilastik segmentation. The inputs required are: 'pre_nuclei' - the pre-processed nuclei channel which is used to define the tissue boundary in the image, 'seg_nuclei' - a multipage tiff, or similar of the ilastik segmented nuclei channel, 'seg_vessels' - a multipage tiff or similar of the ilastik segmented blood vessel image, 'seg_micromets' - a multipage tiff or similar of the ilastik segmented micrometastasis image, 'save_dir' - the directory where the ouput files are saved, and 'sample_name' - the name of the sample to be processed. The ouputs are 'post_nuclei', 'post_nuclei_dilate', 'post_vessels', and 'post_micromet' - the post processed binary segmentation of the nuclei, dilated nuclei, blood vessels and micrometastases written as multipage tiff files.  

# Micrometastais and tumour analysis of nanoparticle delivery and cell distance to blood vessels

Micrometastasis analysis: Analysis from the segmented dilated nuclei, blood vessel and micrometastasis channels combined with the nanoaprticle intensity channel. 
The 'met_analysis_func' function analyzes the nanoaprticle delivery and physiology of individual labelled micrometastases. 

The inputs required are: 'pre_nuclei' - the pre-processed nuclei channel which is used to define the tissue boundary in the image, 'post_nuclei' - a multipage tiff, or similar of the binary post-processed segmented and dilated nuclei channel, 'post_vessels' - a multipage tiff or similar of the the binary post-processed and segmented blood vessel channel, 'post_micromets' - a multipage tiff or similar of the the binary post-processed and segmented micrometastasis channel, 'nanoparticle_ch' - a multipage tiff or similar intensity image of the nanoaprticle channel, 'save_dir' - the directory where the ouput files are saved, and 'sample_name' - the name of the sample to be processed. The outputs from this analysis are: 'met_cell_np_int' - a multipage tiff of the mean nanoparticle intensity per cell in each micrometastasis, 'met_cell_dist' - a multipage tiff of the cell distance to the nearest blood vessel in each micrometastasis, and 'met_dilate_nuclei_label' - a multipage tiff of the labbeld and dilated cells in each micrometastasis. For each labelled micrometastasis a .csv file is output with the coordiates, mean nanoparticle intensity and distance to the nearest blood vessel of each cell within that specific micrometastasis. For all labbeled individual micrometastases the coordinates, volume, surface area, and mean nanoparticle intensity is output in a single .csv file. 

Tumour analysis: Analysis from the segmented dilated nuclei, and blood vessel channels combined with the nanoaprticle intensity channel. 
The 'tumour_analysis_func' function analyzes the nanoaprticle delivery and physiology of tumour cells. 

The inputs required are: 'pre_nuclei' - the pre-processed nuclei channel which is used to define the tissue boundary in the image, 'post_nuclei' - a multipage tiff, or similar of the binary post-processed segmented and dilated nuclei channel, 'post_vessels' - a multipage tiff or similar of the the binary post-processed and segmented blood vessel channel,'nanoparticle_ch' - a multipage tiff or similar intensity image of the nanoaprticle channel, 'save_dir' - the directory where the ouput files are saved, and 'sample_name' - the name of the sample to be processed. The outputs from this analysis are: 'met_cell_np_int' - a multipage tiff of the mean nanoparticle intensity per cell in each micrometastasis, 'met_cell_dist' - a multipage tiff of the cell distance to the nearest blood vessel in each micrometastasis, and 'met_dilate_nuclei_label' - a multipage tiff of the labbeld and dilated cells in each micrometastasis. For each all labelled cells a .csv file is output with the coordiates, mean nanoparticle intensity and distance to the nearest blood vessel of each cell within the tumour image. For the entire tumour image, volume, surface area, and mean nanoparticle intensity is output in a single .csv file. 

# Prediciton models

All prediciton models are stored in the .mat file 'svm_quad_all_prediciton_models.mat'. The models can be used by executing the command
'yfit = svm_quad_mean_np_int.predictFcn(testdata);'. yfit will contain the output values and 'svm_quad_mean_np_int' can be replaced with the respective prediciton function for each nanoaprticle delivery metric: 'svm_quad_mean_np_int' to predict the mean nanoparticle intensity per micrometastasis, 'svm_quad_nppos_cell_density' to predict the density of nanoparticle positive cells per micrometastasis, and 'svm_quad_mean_numnppos_cells' to predict the number of nanoparticle positive cells per micrometastasis. 


*****************************************************
LICENSE

This software license is the 2-clause BSD license plus a third clause that prohibits redistribution and use for commercial purposes without further permission from the authors of this work (Kingston et al).

Redistribution and use in source and binary forms, with or without modification, are permitted provided that the following conditions are met:

Redistributions of source code must retain the above copyright notice, this list of conditions and the following disclaimer.

Redistributions in binary form must reproduce the above copyright notice, this list of conditions and the following disclaimer in the documentation and/or other materials provided with the distribution.

Redistributions and use for commercial purposes are not permitted without the written permission of the authors of this work (Kingston et al). For purposes of this license, commercial purposes are the incorporation of the software into anything for which you will charge fees or other compensation or use of the software to perform a commercial service for a third party.

THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS" AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT HOLDER OR CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
