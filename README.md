# What is this about?
Tutorial on how to do parcellation (i.e., Glasser parcellation: https://www.nature.com/articles/nature18933) and downsample to the needed resolution.

First, install Connectome Workbench on your computer (https://www.humanconnectome.org/software/connectome-workbench).

Once it is done, go to your command line tool of preference, follow the steps:

# 1. Keep the CIFTI separation as is:
wb_command -cifti-separate Q1-Q6_RelatedValidation210.CorticalAreas_dil_Final_Final_Areas_Group_Colors.32k_fs_LR.dlabel.nii COLUMN -label CORTEX_LEFT L.HCP-MMP1.32k_fs_LR.label.gii -label CORTEX_RIGHT R.HCP-MMP1.32k_fs_LR.label.gii

# 2. Make sure to download/use the standard 32k spheres:
# You'll need these standard spheres for consistent resampling
# (These should be obtained from HCP repository or standard_mesh_atlases)

# 3. Create 4k spheres correctly:

wb_command -surface-create-sphere 4000 R.sphere.4k_fs_LR.surf.gii
wb_command -set-structure R.sphere.4k_fs_LR.surf.gii CORTEX_RIGHT

wb_command -surface-flip-lr R.sphere.4k_fs_LR.surf.gii L.sphere.4k_fs_LR.surf.gii
wb_command -set-structure L.sphere.4k_fs_LR.surf.gii CORTEX_LEFT

# 4. Verify/set structure on the label files:
wb_command -set-structure L.HCP-MMP1.32k_fs_LR.label.gii CORTEX_LEFT
wb_command -set-structure R.HCP-MMP1.32k_fs_LR.label.gii CORTEX_RIGHT

# 5a. Resample the midthickness (make sure the 32k sphere files exist):
wb_command -surface-resample S1200.L.midthickness_MSMAll.32k_fs_LR.surf.gii L.sphere.32k_fs_LR.surf.gii L.sphere.4k_fs_LR.surf.gii BARYCENTRIC S1200.L.midthickness_MSMAll.4k_fs_LR.surf.gii

wb_command -surface-resample S1200.R.midthickness_MSMAll.32k_fs_LR.surf.gii R.sphere.32k_fs_LR.surf.gii R.sphere.4k_fs_LR.surf.gii BARYCENTRIC S1200.R.midthickness_MSMAll.4k_fs_LR.surf.gii

# 6a. Resample labels (corrected to match the sphere filename):
wb_command -label-resample L.HCP-MMP1.32k_fs_LR.label.gii L.sphere.32k_fs_LR.surf.gii L.sphere.4k_fs_LR.surf.gii ADAP_BARY_AREA L.HCP-MMP1.4k_fs_LR.label.gii -largest -area-surfs S1200.L.midthickness_MSMAll.32k_fs_LR.surf.gii S1200.L.midthickness_MSMAll.4k_fs_LR.surf.gii

wb_command -label-resample R.HCP-MMP1.32k_fs_LR.label.gii R.sphere.32k_fs_LR.surf.gii R.sphere.4k_fs_LR.surf.gii ADAP_BARY_AREA R.HCP-MMP1.4k_fs_LR.label.gii -largest -area-surfs S1200.R.midthickness_MSMAll.32k_fs_LR.surf.gii S1200.R.midthickness_MSMAll.4k_fs_LR.surf.gii

or 

# 5b. Resample the surface (CORRECTED):
# Left hemisphere
wb_command -surface-resample S1200.L.very_inflated_MSMAll.32k_fs_LR.surf.gii L.sphere.32k_fs_LR.surf.gii L.sphere.4k_fs_LR.surf.gii BARYCENTRIC S1200.L.very_inflated_MSMAll.4k_fs_LR.surf.gii

# Right hemisphere
wb_command -surface-resample S1200.R.very_inflated_MSMAll.32k_fs_LR.surf.gii R.sphere.32k_fs_LR.surf.gii R.sphere.4k_fs_LR.surf.gii BARYCENTRIC S1200.R.very_inflated_MSMAll.4k_fs_LR.surf.gii

# 6b. Resample labels (looks correct as is, but including for completeness):
wb_command -label-resample L.HCP-MMP1.32k_fs_LR.label.gii L.sphere.32k_fs_LR.surf.gii L.sphere.4k_fs_LR.surf.gii ADAP_BARY_AREA L.HCP-MMP1.4k_fs_LR.label.gii -largest -area-surfs S1200.L.very_inflated_MSMAll.32k_fs_LR.surf.gii S1200.L.very_inflated_MSMAll.4k_fs_LR.surf.gii

wb_command -label-resample R.HCP-MMP1.32k_fs_LR.label.gii R.sphere.32k_fs_LR.surf.gii R.sphere.4k_fs_LR.surf.gii ADAP_BARY_AREA R.HCP-MMP1.4k_fs_LR.label.gii -largest -area-surfs S1200.R.very_inflated_MSMAll.32k_fs_LR.surf.gii S1200.R.very_inflated_MSMAll.4k_fs_LR.surf.gii

# 7. Create the combined CIFTI with position information:
wb_command -cifti-create-label glasser_parcellation.4k_fs_LR.dlabel.nii -left-label L.HCP-MMP1.4k_fs_LR.label.gii -right-label R.HCP-MMP1.4k_fs_LR.label.gii
