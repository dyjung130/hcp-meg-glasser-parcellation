# What is this about?
Tutorial on how to do parcellation (i.e., Glasser parcellation: https://www.nature.com/articles/nature18933) and downsample to the needed resolution.

First, install Connectome Workbench on your computer (https://www.humanconnectome.org/software/connectome-workbench).

Once it is done, go to your command line tool of preference, follow the steps:

## 1. From the official the HCP S1200, resample labels
```
wb_command -label-resample Q1-Q6_RelatedValidation210.CorticalAreas_dil_Final_Final_Areas_Group_Colors.32k_fs_LR.dlabel.nii S1200.R.sphere.32k_fs_LR.surf.gii
```
(I have data with the repository but you can download the data (e.g., 1-Q6_RelatedValidation210.CorticalAreas_dil_Final_Final_Areas_Group_Colors.32k_fs_LR.dlabel.nii) from https://balsa.wustl.edu/qKxv).

## 2. Create a new sphere at 4k and set structure (you can find this from the official workbench website)
```
wb_command -surface-create-sphere 4000 R.sphere.4k_fs_LR.surf.gii
```
```
wb_command -surface-flip-lr R.sphere.4k_fs_LR.surf.gii L.sphere.4k_fs_LR.surf.gii 
```
```
wb_command -set-structure R.sphere.4k_fs_LR.surf.gii CORTEX_RIGHT
```
```
wb_command -set-structure L.sphere.4k_fs_LR.surf.gii CORTEX_LEFT
```

## 3. Export the label table (not sure if this is needed though but it is a good way to see the labels)
```
wb_command -label-export-table L.HCP-MMP1.32k_fs_LR.label.gii glasser_labels_table.txt 
```

## 4. Resample the midthickness from 32k to 4k using the spheres (with BARYCENTRIC method)
```
wb_command -surface-resample S1200.L.midthickness_MSMAll.32k_fs_LR.surf.gii  L.sphere.32k_fs_LR.surf.gii L.sphere.4k_fs.surf.gii BARYCENTRIC S1200.L.midthickness_MSMAll.4k_fs_LR.surf.gii
```
```
wb_command -surface-resample S1200.R.midthickness_MSMAll.32k_fs_LR.surf.gii  R.sphere.32k_fs_LR.surf.gii R.sphere.4k_fs.surf.gii BARYCENTRIC S1200.R.midthickness_MSMAll.4k_fs_LR.surf.gii
```

## 5. With the sphere and midthickness, resample the label to 4k
```
wb_command -label-resample L.HCP-MMP1.32k_fs_LR.label.gii L.sphere.32k_fs_LR.surf.gii L.sphere.4k_fs_LR.surf.gii ADAP_BARY_AREA L.HCP-MMP1.4k_fs_LR.label.gii -largest -area-surfs S1200.L.midthickness_MSMAll.32k_fs_LR.surf.gii S1200.L.midthickness_MSMAll.4k_fs_LR.surf.gii
```
```
wb_command -label-resample R.HCP-MMP1.32k_fs_LR.label.gii R.sphere.32k_fs_LR.surf.gii R.sphere.4k_fs_LR.surf.gii ADAP_BARY_AREA R.HCP-MMP1.4k_fs_LR.label.gii -largest -area-surfs S1200.R.midthickness_MSMAll.32k_fs_LR.surf.gii S1200.R.midthickness_MSMAll.4k_fs_LR.surf.gii
```

## 6. Combine the left and right 
```
wb_command -cifti-create-label glasser_parcellation.4k_fs_LR.dlabel.nii -left-label L.HCP-MMP1.4k_fs_LR.label.gii -right-label R.HCP-MMP1.4k_fs_LR.label.gii
```
