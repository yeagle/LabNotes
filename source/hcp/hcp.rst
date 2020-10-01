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

Necessary files
---------------
The necessary convert files for the left hemisphere are:

- fsaverage.lh.inflated (fsaverage/surf/lh.inflated)
- fs_LR-deformed_to-fsaverage.L.sphere.32k_fs_LR.surf.gii
- fsaverage_std_sphere.L.164k_fsavg_L.surf.gii
- fsaverage.L.midthickness_va_avg.164k_fsavg_L.shape.gii
- fs_LR.L.midthickness_va_avg.32k_fs_LR.shape.gii

If you need the file fsaverage.lh.inflated, copy it yourself from your FreeSurfer directory (FreeSurfer needs to be installed):

.. code-block:: bash

  cp /usr/local/freesurfer/subjects/fsaverage/surf/lh.inflated ./fsaverage.lh.inflated
  cp /usr/local/freesurfer/subjects/fsaverage/surf/rh.inflated ./fsaverage.rh.inflated

If you need the file fsaverage_std_sphere.L.164k_fsavg_L.surf.gii, create
it yourself:

.. code-block:: bash

  mris_convert /usr/local/freesurfer/subjects/fsaverage/surf/lh.inflated ./fsaverage_inflated.L.164k_fsavg_L.surf.gii
  mris_convert /usr/local/freesurfer/subjects/fsaverage/surf/rh.inflated ./fsaverage_inflated.R.164k_fsavg_R.surf.gii

The other files come with the HCP project:
https://github.com/Washington-University/HCPpipelines/tree/master/global/templates/standard_mesh_atlases/resample_fsaverage

.. code-block:: bash

  #!/bin/bash
  
  wget https://raw.githubusercontent.com/Washington-University/HCPpipelines/master/global/templates/standard_mesh_atlases/resample_fsaverage/fs_LR-deformed_to-fsaverage.L.sphere.32k_fs_LR.surf.gii
  wget https://raw.githubusercontent.com/Washington-University/HCPpipelines/master/global/templates/standard_mesh_atlases/resample_fsaverage/fs_LR-deformed_to-fsaverage.R.sphere.32k_fs_LR.surf.gii
  wget https://raw.githubusercontent.com/Washington-University/HCPpipelines/master/global/templates/standard_mesh_atlases/resample_fsaverage/fsaverage_std_sphere.L.164k_fsavg_L.surf.gii
  wget https://raw.githubusercontent.com/Washington-University/HCPpipelines/master/global/templates/standard_mesh_atlases/resample_fsaverage/fsaverage_std_sphere.R.164k_fsavg_R.surf.gii
  wget https://raw.githubusercontent.com/Washington-University/HCPpipelines/master/global/templates/standard_mesh_atlases/resample_fsaverage/fsaverage.L.midthickness_va_avg.164k_fsavg_L.shape.gii
  wget https://raw.githubusercontent.com/Washington-University/HCPpipelines/master/global/templates/standard_mesh_atlases/resample_fsaverage/fsaverage.R.midthickness_va_avg.164k_fsavg_R.shape.gii
  wget https://raw.githubusercontent.com/Washington-University/HCPpipelines/master/global/templates/standard_mesh_atlases/resample_fsaverage/fs_LR.L.midthickness_va_avg.32k_fs_LR.shape.gii
  wget https://raw.githubusercontent.com/Washington-University/HCPpipelines/master/global/templates/standard_mesh_atlases/resample_fsaverage/fs_LR.R.midthickness_va_avg.32k_fs_LR.shape.gii

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

Working with Workbench
----------------------
To visualize your data with the parcellation by Glasser et. al (2016),
you need to download the Parcellation here:
https://balsa.wustl.edu/sceneFile/show/Zvk4

Open HCP's Workbench Viewer and load the scene:

.. code-block:: bash

  wb_view

It can be done in one command:

.. code-block:: bash

  wb_view -scene-load ./Glasser_et_al_2016_HCP_MMP1.0_qN_RVVG/HCP_PhaseTwo/Glasser_et_al_2016_HCP_MMP1.0_StudyData/Glasser_et_al_2016_HCP_MMP1.0_6_AllAreasMap.scene 1 

In the Viewer, activate the overlay toolbox:

.. code-block:: none

  View > Overlay Toolbox > Attach to Bottom

Next, disable the preselected overlays, to get a clean, uncolored brain.
Only the area borders and area names should now be visible. 
Now, we want to add our own overlay.

.. code-block:: none

  File > Open > Metric Files > overlay-file.func.gii > Open

.. note::

  If you get an error about missing file structure (File "overlay-file.func.gii" has missing or
  invalid structure, select it's structure), simply select CortexLeft as
  the apropriate file structure and press OK.

The opened overlay file can now be selected in the Overlay Toolbox as an
overlay. Set the On tick to enable it and click on the preferences icon
(the wrench symbol), to change its appearance: in the palette tab, select
"cool-warm" as "Name" for the palette. You can define the values
showed by color by selecting "Fixed" and using the values "-1.3"
and "-2.0" (corresponding to 0.05 and 0.01 significane threshold) for the
"Neg Min" and "Neg Max" fields respectively. 
Depending on the values you select, look at the diagram on the top right to
see which values correspond to which color.

Now some of the names of the areas are hard to read as they are white, we
need to change them to be black. To do this first select the "Annotate"
Mode on the top right (Clickable button). Then enable the Features Toolbox:

.. code-block:: none

  View > Features Toolbox > Attach to Right

Select all of the annotations with a tick by holding shift, and then change
their color to black in the Annotate area, that got visible after enabling
the "Annotate" Mode.

That's it, now you can do the same for all needed hemispheres and export
the images you want. To capture an image, adjust the viewer area as
wanted and then select the following:

.. code-block:: none

  File > Capture Image...

You can change the tiles of the viewer field here: 

.. code-block:: none

  View > Tile Tabs Configuration > Create and Edit...

Further help
------------
Help on workbench function can be found here:
https://www.humanconnectome.org/software/workbench-command

Help on FreeSurfer functions can be found by calling the function with -h
as suffix.
