# slip_detection_dataset_2021
Tactile data collected in the wild, during autonomous robotic pick-and-place tasks. See video bellow for a short description of experimental and data collection procedure.

<a href="https://www.youtube.com/watch?v=rdjgSdl7oQU" target="_blank"><img src="http://img.youtube.com/vi/rdjgSdl7oQU/0.jpg" 
alt="Tactile Slip Detection in the Wild Leveraging Distributed Sensing of Both Normal and Shear Forces" width="240" height="180" border="10" /></a>

The data was collected using an EZGripper mounted on a Universal Robot UR5 arm. One of the fingers of the gripper was replaced with a dedicated structure that includes the uSkin tactile sensor, ensuring that it does not interfere with the gripper operation. A Kinect2 was placed perpendicularly above the workbench, and the captured data was used to autonomously generate grasps through [GGCNN2](https://journals.sagepub.com/doi/full/10.1177/0278364919859066).

The [uSkin sensor](https://ieeexplore.ieee.org/abstract/document/8307485?casa_token=RghI6jquxKsAAAAA:PYxylx6reYD-enQwc1N99BtJVf0_Mh7EW4yGBc3zJyx_t0SI0VE6osIWL6rtN8vFOdv9XTk) has 18 sensitive pins distributed in a 3x6 layout. Data is collected at the frequency of 180Hz. Each pin measures the magnetic field changes induced by the displacements of a small magnet (Hall effect principle) 
lodged inside a deformable silicone rubber dome.
Inspired by the human finger counterpart, a piece of fabric is placed on top of the sensor, creating an artificial skin that reacts to stretches and friction.

uSkin Tactile Sensor:

![alt text][uskin_vis]

Each pin is able to measure the normal and shear forces individually applied to it, and the complete sensor tactile sample corresponds to the distributed measurments of the normal and shear forces aplied to its surface.


Experimental [data](https://github.com/ARQ-CRISP/slip_detection_dataset_2021/tree/main/data) has been collected and labelled for 3 objects: A spool of solder, a Brush and a Screwdriver. 
Each object was placed in three different poses in a 50x50cm workspace, and for each object pose the experiment is repeated 10 times, leading to 30 grasps per object.

Each of these experiments can be extracted independently from the HDF5 files provided for each object.


[uskin_vis]: https://github.com/ARQ-CRISP/slip_detection_dataset_2021/tree/main/images/sensor_vis.png "sensor_vis.png"
<!-- [uskin_vis]: images/sensor_vis.png "sensor_vis.png" -->
