FreeSurfer Overlays on HCP Parcellation
=======================================
This site explains, how to use FreeSurfer overlay files, which are
typically in the .mgh file format, in wb_view (HCP's Workbench Viewer)
together with the HCP Parcellation by Glasser et al. (2016).

- FreeSurfer overlay files are in the fsaverage coordinate space with 164k
  vertices.
- HCP files have 32k vertices and use a different file format.

So there are two things that need to be done:

1. Convert the 164k fsaverage space to 32k HCP space.
2. Convert the file format .mgh to .shape.gii

Left hemisphere conversion
--------------------------
The following bash script converts a given left hemisphere mgh file to be
used in wb_view with the HCP Parcellation as overlay:

.. code-block:: bash

  #!/bin/bash
  
  mris_convert -c ./$1 \
  convFiles/fsaverage.lh.inflated \
  output/${1%.mgh}.func.gii
  
  wb_command -metric-resample output/${1%.mgh}.func.gii \
  convFiles/fsaverage_std_sphere.L.164k_fsavg_L.surf.gii \
  convFiles/fs_LR-deformed_to-fsaverage.L.sphere.32k_fs_LR.surf.gii ADAP_BARY_AREA \
  output/${1%.mgh}.L.32k_fs_LR.func.gii -area-metrics \
  convFiles/fsaverage.L.midthickness_va_avg.164k_fsavg_L.shape.gii \
  convFiles/fs_LR.L.midthickness_va_avg.32k_fs_LR.shape.gii

Necessary files
---------------
The necessary convert files for the left hemisphere are:

- fsaverage.lh.inflated (fsaverage/surf/lh.inflated)
- fs_LR-deformed_to-fsaverage.L.sphere.32k_fs_LR.surf.gii
- fsaverage_std_sphere.L.164k_fsavg_L.surf.gii
- fsaverage.L.midthickness_va_avg.164k_fsavg_L.shape.gii
- fs_LR.L.midthickness_va_avg.32k_fs_LR.shape.gii

If you need the file fsaverage.lh.inflated, copy it yourself:

.. code-block:: bash

  cp /usr/local/freesurfer/subjects/fsaverage/surf/lh.inflated ./fsaverage.lh.inflated

If you need the file fsaverage_std_sphere.L.164k_fsavg_L.surf.gii, create
it yourself:

.. code-block:: bash

  mris_convert /usr/local/freesurfer/subjects/fsaverage/surf/lh.inflated ./fsaverage_inflated.L.164k_fsavg_L.surf.gii
  mris_convert /usr/local/freesurfer/subjects/fsaverage/surf/rh.inflated ./fsaverage_inflated.R.164k_fsavg_R.surf.gii

The other files come with the HCP project.

Right hemisphere conversion
---------------------------
For right hemisphere files, use the appropriate convert files and this
script:

.. code-block:: bash

  #!/bin/bash
  
  mris_convert -c ./$1 \
  convFiles/fsaverage.rh.inflated \
  output/${1%.mgh}.func.gii
  
  wb_command -metric-resample output/${1%.mgh}.func.gii \
  convFiles/fsaverage_std_sphere.R.164k_fsavg_R.surf.gii \
  convFiles/fs_LR-deformed_to-fsaverage.R.sphere.32k_fs_LR.surf.gii ADAP_BARY_AREA \
  output/${1%.mgh}.R.32k_fs_LR.func.gii -area-metrics \
  convFiles/fsaverage.R.midthickness_va_avg.164k_fsavg_R.shape.gii \
  convFiles/fs_LR.R.midthickness_va_avg.32k_fs_LR.shape.gii

Further help
------------
Help on workbench function can be found here:
https://www.humanconnectome.org/software/workbench-command

Help on FreeSurfer functions can be found by calling the function with -h
as suffix.
