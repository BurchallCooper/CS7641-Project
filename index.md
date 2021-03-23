# Deep Learned Stereo Human Pose Estimation and Laser Designation for a non-Lethal Targeting System

## Introduction

Advances in deep learning allow for accurate real-time pose estimation[1][2][3] which can be applied to security applications for the deterrence of intruders with potential hostile intentions, and the non-lethal neutralization of these hostile actors.  This project will describe the full system implementation focusing primarily on the targeting design of the system using deep learning for pose estimation, stereography for ranging, and laser designation to implement a closed loop targeting system.  

The architecture of the security system described in this paper has 3 distinct functions: (1) threat detection, (2) targeting, and (3) threat neutralization.

1. Threat Detection. Threat detection may be as simple as a perimeter violation where some unidentified individual crosses a predefined boundary into a controlled area.   However, it may be much more sophisticated where a weapon is detected in the hand of an individual by the threat detection module.  Ideally, this module would be sufficiently intelligent to differentiate between friendlies and hostiles carrying weapons.  

2. Targeting. The purpose of the targeting system is to aim a non-lethal weapon for threat neutralization.  The targeting system must be sufficiently accurate to only to aim the weapon to disable and not permanently injure.  Of primary concern is avoiding sensitive areas such as the eyes.

3. Threat Neutralization. Threat neutralization is achieved in two ways.  Realization by an individual that he is being actively targeted (in our system with a laser) may provide sufficient deterrence that no further action is required.    However, in systems where threat neutralization is implemented as a non-lethal weapon the targeting laser aims the weapon.  The weapon proposed for this system would fire rubber bullets.


<p align="center"><img src="https://raw.githubusercontent.com/BurchallCooper/CS7641-Project/gh-pages/System.png" alt="system drawing" height="400" width="400" /></p>
<p align="center"> Figure 1: System Design </p>


Figure 1 illustrates the system architecture.  Two sets of cameras are used.  One set is for stereo pose estimation to provide ranging for the vision system, and the second set is outfitted with optical bandpass filters centered at the laser frequency to filter out ambient light.  The second set also provides ranging data and control of the weapon’s gimble.  The 532 nm targeting laser is mounted to the barrel of the weapon. 

This approach allows for the cameras to be mounted on a stationary platform and only the targeting laser and weapon to move for targeting.  The advantage is the sensitive camera equipment is protected, but at the expense of making the system more complicated.  

## Problem definition
The operation of the system is as follows:
1.	Each vision camera simultaneously captures the image of the target
2.	Human skeletal poses are extracted from the stereo images
3.	The images are grouped in pairs using non-supervised learning 
4.	The disparity between each skeletal component in the image pairs allows the distance to that skeletal component to be calculated. (See Figure 2 below for stereo depth calculation)

Similarly, for the laser cameras:
1.	Each laser camera simultaneously captures the image of the target
2.	The laser target is detected in the image
3.	The disparity between the laser target in each image allows the distance to the target to be calculated

We then have 3-d coordinate data for each skeletal component and 3-d coordinate data for the laser target.  The control system minimizes the error between vision and laser coordinates by controlling the gimbal and the target is thus acquired.

<p align="center"><img src="https://raw.githubusercontent.com/BurchallCooper/CS7641-Project/gh-pages/StereoEquation.png" alt="system drawing" height="400" width="400" /></p>
<p align="center"> Figure 2: Stereo Equation Derivation </p>
 
The primary challenge in this architecture is implementing the target detection and human skeletal pose estimation.  For this project, BlazePose[?], a convolutional neural network architected for human pose estimation, developed by Google was selected to provide the pose estimations for each of the stereo camera pairs.  This solution is tailored for real-time inference and requires minimal computational resources.  During inference, the algorithm produces 33 body keypoints for a single person.  For this project 2 instances of the algorithm must generate over 30 frames per second in real-time.

## Methods

During inference, we employ a detector-tracker setup (see Figure 1), which shows excellent real-time perfor- mance on a variety of tasks such as hand landmark pre- diction [3] and dense face landmark prediction [6]. Our pipeline consists of a lightweight body pose detector fol- lowed by a pose tracker network. The tracker predicts key- point coordinates, the presence of the person on the cur- rent frame, and the refined region of interest for the current frame. When the tracker indicates that there is no human present, we re-run the detector network on the next frame

<p align="center"><img src="https://raw.githubusercontent.com/BurchallCooper/CS7641-Project/gh-pages/InferencePipeline.png" alt="system drawing" height="300" width="400" /></p>
<p align="center"> Figure 3: Inference Pipeline </p>

The pose estimation component of our system predicts the location of all 33 person keypoints, and uses the person alignment proposal provided by the first stage of the pipeline.

<p align="center"><img src="https://raw.githubusercontent.com/BurchallCooper/CS7641-Project/gh-pages/PosePoints.png" alt="system drawing" height="400" width="400" /></p>
<p align="center"> Figure 4: Keypoint topology </p>

The network consists a a combined heatmap, offset, and regression ap- proach, as shown in Figure 4. We use the heatmap and offset loss only in the training stage and remove the correspond- ing output layers from the model before running the infer- ence. Thus, we effectively use the heatmap to supervise the lightweight embedding, which is then utilized by the regres- sion encoder network. This approach is partially inspired by Stacked Hourglass approach of Newell et al. [9], but in our case, we stack a tiny encoder-decoder heatmap-based net- work and a subsequent regression encoder network.

We actively utilize skip-connections between all the stages of the network to achieve a balance between high- and low-level features. However, the gradients from the regression encoder are not propagated back to the heatmap- trained features (note the gradient-stopping connections in Figure 4). We have found this to not only improve the heatmap predictions, but also substantially increase the co- ordinate regression accuracy. 

<p align="center"><img src="https://raw.githubusercontent.com/BurchallCooper/CS7641-Project/gh-pages/SystemArchitecture.png" alt="system drawing" height="400" width="400" /></p>
<p align="center"> Figure 5: Network Architecture </p> 

## Data Collection and Neural Network Training

To evaluate our model’s quality, we chose OpenPose [4] as a baseline. To that end, we manually annotated two in- house datasets of 1000 images, each with 1–2 people in the scene. The first dataset, referred to as AR dataset, consist of a wide variety of human poses in the wild, while the sec- ond is comprised of yoga/fitness poses only
This dataset provides stereo image pairs in a wide range of variations in appearance, clothing, human pose, illumination, image quality, baseline separation of the cameras, and background. 

The test setup consisted of the 4 cameras mounted as shown in Figure 6.  A mannequin was used for calibration and test of the system.

<p align="center"><img src="https://raw.githubusercontent.com/BurchallCooper/CS7641-Project/gh-pages/TestSetup.jpg" alt="system drawing" height="400" width="400" /></p>
<p align="center"> Figure 6: Test Setup </p> 

## Results

The baseline for the project will be to implement non-real-time ranging with the Stereo Human Pose Estimation Dataset, SHPED. The stretch goal will add targeting to the system.   In real time the detected human pose skeletons in the camera view will be shown as an overlay to the subjects.  The subject's targeted skeletal region will be identified in the image and the distance to the subject displayed.  

<p align="center"><img src="https://raw.githubusercontent.com/BurchallCooper/CS7641-Project/gh-pages/manni.gif" alt="Targeting" height="400" width="400" /></p>
<p align="center"> Figure 7: Example of Targeting </p> 

## Discussion

This project incorporates both recent advances in deep learning applied to real-time human pose estimation, and classical techniques for unsupervised learning to implement targeting for a complex security system.  The targeting system will require accurate real-time identification of the subject’s entire skeletal frame, single target isolation, accurate distance measurement to the target, and accurate aiming.

## References

1.    G. Papandreou, T. Zhu, N. Kanazawa, A. Toshev, J. Tompson, C. Bregler, K. Murphy, Towards Accurate Multi-Person Pose Estimation in the Wild, Proceedings of the IEEE      Conference on Computer Vision and Pattern Recognition (CVPR), 2017, pp. 4903-4911
2.    J. Huang et al., “Speed/accuracy trade-offs for modern convolutional object detectors”, CVPR 2017  
3.    Z. Cao, T. Simon, S.-E. Wei, Y. Sheikh, “Realtime Multi-Person 2D Pose Estimation using Part Affinity Fields”, CVPR 2017
4.    V. Bazarevsky, I. Grishchenko, https://ai.googleblog.com/2020/08/on-device-real-time-body-pose-tracking.html


[edit](https://github.com/BurchallCooper/CS7641-Project/edit/gh-pages/index.md)    
 
![image](https://user-images.githubusercontent.com/6483635/112152775-08560d80-8bb9-11eb-85e9-ce0f0b63f06f.png)

