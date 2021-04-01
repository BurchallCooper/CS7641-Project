# Deep Learned Stereo Human Pose Estimation and Laser Designation for a non-Lethal Targeting System

## Introduction

Advances in deep learning allow for accurate real-time pose estimation[1][2][3] which can be applied to security applications for the deterrence of intruders with potential hostile intentions, and the non-lethal neutralization of these hostile actors.  This project describes the full system implementation focusing primarily on the targeting design of the system using deep learning for pose estimation, stereography for ranging, and laser designation to implement a closed loop targeting system.  

The architecture of the security system described in this paper has 3 distinct functions: Threat Detection, Targeting, and Threat Neutralization.

- Threat Detection. Threat detection may be as simple as a perimeter violation where some unidentified individual crosses a predefined boundary into a controlled area.   However, it may be much more sophisticated where a weapon is detected in the hand of an individual by the threat detection module.  Ideally, this module would be sufficiently intelligent to differentiate between friendlies and hostiles carrying weapons.  

- Targeting. The purpose of the targeting system is to aim a non-lethal weapon for threat neutralization.  The targeting system must be sufficiently accurate to only to aim the weapon to disable and not permanently injure.  Of primary concern is avoiding sensitive areas such as the eyes.

- Threat Neutralization. Threat neutralization is achieved in two ways.  Realization by an individual that he is being actively targeted (in our system with a laser) may provide sufficient deterrence that no further action is required.    However, in systems where threat neutralization is implemented as a non-lethal weapon the targeting laser aims the weapon.  The weapon proposed for this system would fire rubber bullets.


<p align="center"><img src="https://raw.githubusercontent.com/BurchallCooper/CS7641-Project/gh-pages/System.png" alt="system drawing" height="600" width="600" /></p>
<p align="center"> Figure 1: System Design </p>


Figure 1 illustrates the system architecture.  Two sets of cameras are used.  One set is for stereo pose estimation to provide ranging for the vision system, and the second set is outfitted with optical bandpass filters centered at the laser frequency to filter out ambient light.  The second set also provides ranging data and control of the weapon’s gimble.  The 532 nm targeting laser is mounted to the barrel of the weapon. 

This approach allows for the cameras to be mounted on a stationary platform and only the targeting laser and weapon move for targeting.  The advantage is the sensitive camera equipment is protected, but at the expense of making the system more complicated.  

## Problem definition
The operation of the vision system and laser system are independent and the output results are combined for targeting. The sequence of events for the vison system is as follows:
1.	Each vision camera simultaneously captures the image of the target. Two images are required to use stereo techniques to calculate the range to the target.
2.	Human skeletal poses are extracted from the stereo images.  The idea is to be able to accurately target any portion of the human body.
3.	The images are grouped in pairs using non-supervised learning. An image may have a large number of potential targets and the stereo calculation requires accurate pairing of the targets for ranging.  The nearest neighbor of the vector created by the pose estimates pairs the data between images.
4.	The disparity between each skeletal component in the image pairs allows the distance to that skeletal component to be calculated. (See Figure 2 below for stereo depth calculation)

Similarly, for the laser cameras:
1.	Each laser camera simultaneously captures the image of the target. Similar to the vision cameras, two images are required to use stereo techniques to calculate the range.  
2.	The laser target is detected in the image.  In this case the range is to the detected laser point in the image which is unambiguous as the detectpr only returns one point for each image.
3.	The disparity between the laser target in each image allows the distance to the target to be calculated.  This is identical to the vision case.

Now that we have the ouptuts from the vision and the laser system, we have 3-d coordinate data for each skeletal component and 3-d coordinate data for the laser target.  In a full system implementation, a control system would minimize the error between vision and laser coordinates by controlling the gimbal and the target would be acquired.  In this proof of concept we are simply detecting that the laser and selected vision component are aligned.

<p align="center"><img src="https://raw.githubusercontent.com/BurchallCooper/CS7641-Project/gh-pages/StereoEquation.png" alt="system drawing" height="400" width="400" /></p>
<p align="center"> Figure 2: Stereo Equation Derivation[6] </p>
 
The primary challenge in this architecture is implementing the target detection and human skeletal pose estimation of the target.  The human pose estimate must be accurate for precise aiming of the device that fires the rubber bullets.  It must also be fast for instant targeting and tracking of the target's motion.

## Data Collection and Neural Network Training

The origninal algorithm was trained on two manually annotated datasets of 1000 images developed by Google[4], with each of the images having 1–2 people in the scene.  The first dataset consisted of a wide variety of human poses with a wide range of variations in appearance, clothing, human pose, illumination, image quality, and background while the second was comprised of yoga/fitness poses only.  For this project, the network training was achieved through tranfer learning where the base network was the Google trained network, and we repurposed the learned features, or transfered them, to a our network for the target dataset.

Neural Network testing was conducted against two different databases:  Microsoft Common Objects in Context (COCO)[5] and Fallen People Data Set (FPDS)[7].  COCO is a large-scale object detection, segmentation, and captioning dataset with over 250,000 images where most of the images in outdoor environments.  FPDS on the other hand, consists of 6982 images, with a total of 5023 falls and 2275 non falls corresponding to people in conventional situations (standing up, sitting, lying on the sofa or bed, walking, etc). Most of the FPDS images were captured in indoor environments.

The test setup consisted of the 4 cameras mounted as shown in Figure 3 where the left and right cameras are mounted such that the world coordinates of the cameras have the same x-cooridinates and slightly offset y-cooridnates.  Achieving good alignment of the cameras was a significant challenge 
and in practice would limit the performance of the system. As shown in the figure, a mannequin was used for calibration and test of the system.

<p align="center"><img src="https://raw.githubusercontent.com/BurchallCooper/CS7641-Project/gh-pages/TestSetup.jpg" alt="system drawing" height="400" width="400" /></p>
<p align="center"> Figure 3: Test Setup </p> 

## Methods

For this project, BlazePose[4], a convolutional neural network developed by Google and architected for human pose estimation was selected to provide the pose estimations for each of the stereo camera pairs.  This solution was developed for real-time inference and requires minimal computational resources. 

Much of the research work done to date for human body pose estimation implements the COCO topology[5], which consists of 17 landmarks across the torso, arms, legs, and face. The Google implementation expands this number to 33 body keypoints.  For this project 2 instances of the algorithm generate over 30 frames per second in real-time.

During inference, a detector-tracker setup is employed (see Figure 4), which has good real-time performance on a variety of landmark prediction tasks. The Google pipeline consists of a lightweight body pose detector followed by a pose tracker network. The tracker predicts keypoint coordinates, the presence of the person on the current frame, and the refined region of interest for the current frame. When the tracker indicates that there is no human present, the detector network is run on the next frame. [4]

<p align="center"><img src="https://raw.githubusercontent.com/BurchallCooper/CS7641-Project/gh-pages/InferencePipeline.png" alt="system drawing" height="300" width="400" /></p>
<p align="center"> Figure 4: Inference Pipeline [4]</p>

The pose estimation component of the google system predicts the location of the 33 person keypoints, and uses the person alignment proposal provided by the first stage of the pipeline.

<p align="center"><img src="https://raw.githubusercontent.com/BurchallCooper/CS7641-Project/gh-pages/PosePoints.png" alt="system drawing" height="400" width="400" /></p>
<p align="center"> Figure 5: Keypoint topology[5] </p>

The network consists a a combined heatmap, offset, and regression approach, as shown in Figure 6.  The heatmap and offset loss is used only in the training stage and removes the corresponding output layers from the model before running the inference.  The heatmap supervises the lightweight embedding, which is then utilized by the regression encoder network.  

The Google implementation actively utilize skip-connections between all the stages of the network to achieve a balance between high-level and low-level features. However, the gradients from the regression encoder are not propagated back to the heatmap-trained features (note the gradient-stopping connections in Figure 6). 

<p align="center"><img src="https://raw.githubusercontent.com/BurchallCooper/CS7641-Project/gh-pages/SystemArchitecture.png" alt="system drawing" height="500" width="700" /></p>
<p align="center"> Figure 6: Network Architecture </p> 

## Results

Figures 7 and 8 demonstrated the operation of the proof of concept model.  The top two images are stereo vission images, and the bottom two are the stereo laser images.  Figure 7 demonstrates operation with a fixed target while moving a laser near the selected landmarks which is just the torso of the mannequin in this case.  Figue 8 dmonstrates operation where the laser is fixed and target is moving.  In real time the detected human pose skeletons in the camera's view is overlayed on the subject with the 33 Key Points labeled. When the laser targets the area near the yellow dot in the image, the dot turns red and a red circle is displayed around the dot. 

The experimental setup is only intended to test the operation of the neural network and the targeting of a particular human pose landmark.  Ultimately, the objective is to mount the laser on a gimbal and have a control system adjust the aiming of the laser to continuosly track the targeted pose landmark.  Adding the gimbal and the control loop are beyond the scope of this phase of the project.

The laser detection worked well indoors with varied lighting conditions.  Some testing was done with outdoor lighting conditions.  It was noted that with outdoor bright sunlight that has significant green spectral content this approach does not work well.  However, green lasers were all that were readily available, and worked well for this proof of concept. 

It was discovered that pose detection required the human target to occupy approximately 50% of the frame in either the x or y dimension.  The range of the current system is limited to under 4 meters.  The architecture of the targeting system will need to be modified to incorporate a preprossing stage to solve this problem.  This preprocessor would need to detect the target and zoom the image for the pose detector to work optimally.  With only electronic zoom the distance could be increased to 20 meters with the existing cameras.  Optical zoom would be needed for long distances.

<p align="center"><img src="https://raw.githubusercontent.com/BurchallCooper/CS7641-Project/gh-pages/manni.gif" alt="Targeting" height="800" width="800" /></p>
<p align="center"> Figure 7: Example of Targeting </p> 


Testing against the FPDS[7] dataset  resulted in one shot detection in 57% of the images.  On the surface this would appear to be a low number, but these were very difficult to detect occluded poses.  Additionally, this was one shot and the application would be running at 30 frames per second making the likelihood of detection much more likely for a target that is in motion.  With the confidence level set to 0.5, the neural network worked very well in correctly finding the pose. For 1708 images where a person was detected, there was only 1 failure where the pose was incorrectly formed.

Testing against COCO[5] was more problematic.  If there was a person present in the image, the pose estimator worked well in providing the pose estimate.  But often in scenes with no humans present and animals were present, the neural network would misclassify the animal as human and provide an estimated pose as shown in figure 9. In this case  a rooster was identified as the hostile actor.  However, it is a good pose estimate for a rooster. Also, the network would occasionally misclassify an inanimate object in an outdoor scene as human and provide a pose estimate.

<p align="center"><img src="https://github.com/BurchallCooper/CS7641-Project/blob/gh-pages/annotated_image490.png?raw=true" alt="Targeting" height="300" width="300" /></p>
<p align="center"> Figure 8: Failure of Neural Network </p> 


## Discussion

This project incorporates both recent advances in deep learning applied to real-time human pose estimation, and classical techniques for unsupervised learning to implement targeting for a complex security system.  The targeting system requires accurate real-time identification of the subject’s entire skeletal frame, single target isolation, accurate distance measurement to the target, and accurate aiming.


## References

1.    G. Papandreou, T. Zhu, N. Kanazawa, A. Toshev, J. Tompson, C. Bregler, K. Murphy, "Towards Accurate Multi-Person Pose Estimation in the Wild", Proceedings of the IEEE Conference on Computer Vision and Pattern Recognition (CVPR), 2017, pp. 4903-4911
2.    J. Huang et al., “Speed/accuracy trade-offs for modern convolutional object detectors”, CVPR 2017  
3.    Z. Cao, T. Simon, S.-E. Wei, Y. Sheikh, “Realtime Multi-Person 2D Pose Estimation using Part Affinity Fields”, CVPR 2017
4.    V. Bazarevsky, I. Grishchenko, https://ai.googleblog.com/2020/08/on-device-real-time-body-pose-tracking.html
5.    Lin TY. et al. (2014) Microsoft COCO: Common Objects in Context. In: Fleet D., Pajdla T., Schiele B., Tuytelaars T. (eds) Computer Vision – ECCV 2014. ECCV 2014. Lecture Notes in Computer Science, vol 8693. Springer, Cham. https://doi.org/10.1007/978-3-319-10602-1_48
6.    F. Dellaert, "Introduction to Computer Vision", Georgia Tech CS 6476 Fall 2020 edition 
7.    M. Antonello,  C. Antonello, M. Marco, P. Marco, M. Emanuele, "Fast and Robust detection of fallen people from a mobile robot", Intelligent Robots and Systems (IROS), 2017 IEEE/RSJ International Conference, http://agamenon.tsc.uah.es/Investigacion/gram/papers/fall_detection/

[edit](https://github.com/BurchallCooper/CS7641-Project/edit/gh-pages/index.md)    
