# Tactile data - HDF5 Files
Data has been stored using the [h5py](https://docs.h5py.org/en/stable/) python package (pythonic interface to the HDF5 binary data format). Refer to this link for installation guidelines and basic tutorials.

The tactile data for each grasped object is stored in independent files.


This files are opened/closed similarly to other data file formats.

```python
import h5py

hf = h5py.File('slipDataset_SpoolSolder_tactile.h5', 'r') # Read mode

hf.close() # Don't forget to close your files
```

Data in this files is organized in Groups. From a Python perspective, they operate somewhat like dictionaries. In this case the “keys” are the names of group members, and the “values” are either sub-Groups or Dataset objects.
In order to inspect the contents of this files we can use ```visit()``` attribute.

```python
def print_all_groups(name):  # Prints every sub-group
    print (name) 
    return

def print_experiments_groups(name):  # Prints sub-groups up to 4 levels
    if len(name.split('/')) == 4:
        print (name) 
    return

hf.visit(print_experiments_groups)
```
You should see the following.

```
GGCNN2/SpoolSolder/Pose1/Exp1
GGCNN2/SpoolSolder/Pose1/Exp10
GGCNN2/SpoolSolder/Pose1/Exp2
GGCNN2/SpoolSolder/Pose1/Exp3
GGCNN2/SpoolSolder/Pose1/Exp4
GGCNN2/SpoolSolder/Pose1/Exp5
GGCNN2/SpoolSolder/Pose1/Exp6
GGCNN2/SpoolSolder/Pose1/Exp7
GGCNN2/SpoolSolder/Pose1/Exp8
GGCNN2/SpoolSolder/Pose1/Exp9
GGCNN2/SpoolSolder/Pose2/Exp1
GGCNN2/SpoolSolder/Pose2/Exp10
GGCNN2/SpoolSolder/Pose2/Exp2
GGCNN2/SpoolSolder/Pose2/Exp3
GGCNN2/SpoolSolder/Pose2/Exp4
GGCNN2/SpoolSolder/Pose2/Exp5
GGCNN2/SpoolSolder/Pose2/Exp6
GGCNN2/SpoolSolder/Pose2/Exp7
GGCNN2/SpoolSolder/Pose2/Exp8
GGCNN2/SpoolSolder/Pose2/Exp9
GGCNN2/SpoolSolder/Pose3/Exp1
GGCNN2/SpoolSolder/Pose3/Exp10
GGCNN2/SpoolSolder/Pose3/Exp2
GGCNN2/SpoolSolder/Pose3/Exp3
GGCNN2/SpoolSolder/Pose3/Exp4
GGCNN2/SpoolSolder/Pose3/Exp5
GGCNN2/SpoolSolder/Pose3/Exp6
GGCNN2/SpoolSolder/Pose3/Exp7
GGCNN2/SpoolSolder/Pose3/Exp8
GGCNN2/SpoolSolder/Pose3/Exp9
```
In each HDF5 file, the tactile data is organized in different Groups (essentially sub-folders), for each of grasp experiment. To check the datasets of each of these experiments we can use the following.
 
```python
def print_single_experiment_groups(name):  # Prints sub-groups for 1 grasp experiment
    if len(name.split('/')) > 4:
        if name.split('/')[2] == 'Pose1' and name.split('/')[3] == 'Exp1':
            print (name) 
    return

hf.visit(print_single_experiment_groups)
```
Outputs
```
GGCNN2/SpoolSolder/Pose1/Exp1/tactile_changes_label
GGCNN2/SpoolSolder/Pose1/Exp1/tactile_data_raw
GGCNN2/SpoolSolder/Pose1/Exp1/tactile_slips_label
GGCNN2/SpoolSolder/Pose1/Exp1/tactile_timestamps
```

We see that for each Experiment we have 4 different Datasets. They can be accessed has elements of a dictionary. Each Dataset contains the following data:
* tactile_data_raw - Collected uSkin tactile data. Each of the **18** sensitive pins of sensor returns **3** signals (Normal and Shear Forces). For experiment 1 a total of **4138** samples have been collected.
* tactile_timestamps - Timestamps (in ms) for each of the tactile samples.
* tactile_timestamps - Manually annotated slip labels for each of the tactile samples.
* tactile_changes_label - For convenience, we have also included a label array indicating if there was a significant overall diference in the tactile imprint magnitude readings regarding the previous timestamp.

```python
In [1]: hf['GGCNN2/SpoolSolder/Pose1/Exp1/tactile_changes_label']                                                                                                                                                                     
Out[1]: <HDF5 dataset "tactile_changes_label": shape (4138,), type "<i8">

In [2]: hf['GGCNN2/SpoolSolder/Pose1/Exp1/tactile_data_raw']                                                                                                                                                                          
Out[2]: <HDF5 dataset "tactile_data_raw": shape (18, 3, 4138), type "<f8">

In [3]: hf['GGCNN2/SpoolSolder/Pose1/Exp1/tactile_slips_label']                                                                                                                                                                       
Out[3]: <HDF5 dataset "tactile_slips_label": shape (4138,), type "<i8">

In [4]: hf['GGCNN2/SpoolSolder/Pose1/Exp1/tactile_timestamps']                                                                                                                                                                        
Out[4]: <HDF5 dataset "tactile_timestamps": shape (4138,), type "<f8">
```

Using ```numpy``` python package, each dataset is easily imported.

```python
import numpy as np

data = np.array(hf['GGCNN2/SpoolSolder/Pose1/Exp1/tactile_data_raw'])
data.shape

Out[5]: (18, 3, 4138)
```

The uSkin tactile data is organized as follows:

* ```data[:,0,:]```: Measurements along the X-axis (shear force) for each pin and for each timestamp.
* ```data[:,1,:]```: Measurements along the Y-axis (shear force) for each pin and for each timestamp.
* ```data[:,2,:]```: Measurements along the Z-axis (Normal force) for each pin and for each timestamp.

Each individual pin of the sensor is accessed as follows:

![uskin_cans]
![uskin]

* ```data[0,:,:]```: 3-axis measurements for a single pin (C1-SDA1)
* ```data[1,:,:]```: 3-axis measurements for a single pin (C1-SDA2)
* ```data[2,:,:]```: ... (C1-SDA3)
* ```data[3,:,:]```: ... (C2-SDA1)
* ```data[4,:,:]```: ... (C2-SDA2)
* ```data[5,:,:]```: ... (C2-SDA3)
* ```data[6,:,:]```: ... (C3-SDA1)
* ```data[7,:,:]```: ... (C3-SDA2)
* ```data[8,:,:]```: ... (C3-SDA3)
* ```data[9,:,:]```: ... (C4-SDA1)
* ```data[10,:,:]```: ... (C4-SDA2)
* ```data[11,:,:]```: ... (C4-SDA3)
* ```data[12,:,:]```: ... (C5-SDA1)
* ```data[13,:,:]```: ... (C5-SDA2)
* ```data[14,:,:]```: ... (C5-SDA3)
* ```data[15,:,:]```: ... (C6-SDA1)
* ```data[16,:,:]```: ... (C6-SDA2)
* ```data[17,:,:]```: ... (C6-SDA3)


Finally, to load the labeling dataset:
```python
labels = np.array(hf['GGCNN2/SpoolSolder/Pose1/Exp1/tactile_slips_label'])
labels.shape

Out[6]: (4138,)
```

Inspecting the dataset in detail we see that each sample is labeled with one of 6 possible tags:

```python
# Some code to compute statistics on the labels
y = np.bincount(labels) 
tag = np.nonzero(y)[0] 

for a, b in zip(tag,y[tag]): 
    print("Label {} appears {} times ({:.2f}%)".format(a, b, b/labels.shape[0]))

Out[7]: 
Label 0 appears 1818 times (0.44%)
Label 1 appears 475 times (0.11%)
Label 2 appears 16 times (0.00%)
Label 3 appears 42 times (0.01%)
Label 4 appears 1626 times (0.39%)
Label 5 appears 34 times (0.01%)
Label 6 appears 127 times (0.03%)
```

Each tag corresponds to a specfic event during the object manipulation:

* ```0:``` Object is not being held/Object is being held and robot arm is static. Slip is NOT percieved;
* ```1:``` Object is being held, robot arm is static. SLIP is perceived;
* ```2:``` Object is RELEASED by robotic gripper;
* ```3:``` Object is GRASPED by robotic gripper;
* ```4:``` Object is being held while robotic arm is executing some motion. Slip is NOT percieved;
* ```5:``` Object is being held while robotic arm is executing some motion. SLIP is percieved;
* ```6:``` Other tactile events (ignore this for now).

Tactile slip events are tagged as either ```1``` or ```5```. As you can see, data is unbalanced on the number of slip events vs. no-slip events.

[uskin_cans]: https://github.com/ARQ-CRISP/slip_detection_dataset_2021/blob/main/images/uskin_cans.png "uskin_pins_canID.png"
[uskin]: https://github.com/ARQ-CRISP/slip_detection_dataset_2021/blob/main/images/sensor.jpg "sensor.png"
<!-- [uskin_cans]: ../images/uskin_cans.png "uskin_pins_canID.png"
[uskin]: ../images/sensor.jpg "sensor.png" -->
