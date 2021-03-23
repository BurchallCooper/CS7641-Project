# Deep Learned Stereo Human Pose Estimation and Laser Designation for a non-Lethal Targeting System

## Introduction

Advances in deep learning allow for accurate real-time pose estimation[1][2][3] which can be applied to security applications for the deterrence of intruders with potential hostile intentions, and the non-lethal neutralization of these hostile actors.  This project will describe the full system implementation focusing primarily on the targeting design of the system using deep learning for pose estimation, stereography for ranging, and laser designation to implement a closed loop targeting system.  

The architecture of the security system described in this paper has 3 distinct functions: (1) threat detection, (2) targeting, and (3) threat neutralization.

Threat Detection

Threat detection may be as simple as a perimeter violation where some unidentified individual crosses a predefined boundary into a controlled area.   However, it may be much more sophisticated where a weapon is detected in the hand of an individual by the threat detection module.  Ideally, this module would be sufficiently intelligent to differentiate between friendlies and hostiles carrying weapons.  

Targeting

The purpose of the targeting system is to aim a non-lethal weapon for threat neutralization.  The targeting system must be sufficiently accurate to only to aim the weapon to disable and not permanently injure.  Of primary concern is avoiding sensitive areas such as the eyes.
Threat Neutralization
Threat neutralization is achieved in two ways.  Realization by an individual that he is being actively targeted (in our system with a laser) may provide sufficient deterrence that no further action is required.    However, in systems where threat neutralization is implemented as a non-lethal weapon the targeting laser aims the weapon.  The weapon proposed for this system would fire rubber bullets.

Overview:

<p align="center"><img src="https://raw.githubusercontent.com/BurchallCooper/CS7641-Project/gh-pages/System.png" alt="system drawing" height="400" width="400" class="center" /></p>

Figure 1 illustrates the system architecture.  Two sets of cameras are used.  One set is for stereo pose estimation to provide ranging for the vision system, and the second set is outfitted with optical bandpass filters centered at the laser frequency to filter out ambient light.  The second set also provides ranging data and control of the weapon’s gimble.  The 532 nm targeting laser is mounted to the barrel of the weapon. 

This approach allows for the cameras to be mounted on a stationary platform and only the targeting laser and weapon to move for targeting.  The advantage is the sensitive camera equipment is protected, but at the expense of making the system more complicated.  

## Problem definition

The architecture of the security system described in this paper has 3 distinct functions: (1) threat detection, (2) targeting, and (3) threat neutralization.  

Threat detection may be as simple as a perimeter violation where some unidentified individual crosses a predefined boundary into a controlled area.   However, it may be much more sophisticated where a weapon is detected in the hand of an individual by the threat detection module.  Ideally, this module would be sufficiently intelligent to differentiate between friendlies and hostiles carrying weapons.  

The targeting function performs has two purposes.  Realization by an individual that he is being actively targeted may sufficient deterrence that no further action is required.  However, in systems where threat neutralization is implemented as a function the targeting laser aims some type of non-lethal weapon for threat neutralization. 

Threat neutralization for discussion purposes in this project is envisioned as using rubber bullets.  The purpose of the targeting system is to not only to aim the rubber bullet, but more specifically aim the bullet at the lower body extrimeties to disable and not permanently injure.  



## Methods

This work will use deep learned pose estimation techniques to capture the human pose skeletons of multiple subjects within the camera’s view.  The pose estimation will result in a set of connected two dimensional coordinates for each person detected.   Stereography coupled with non-supervised learning techniques will then augment this data with depth information for each subject.  The Stereo Human Pose Estimation Dataset, SHPED, will be used for the offline testing with images.  This dataset provides stereo image pairs in a wide range of variations in appearance, clothing, human pose, illumination, image quality, baseline separation of the cameras, and/or background. 

If baseline goals are met, operation in real-time will be attemped and a 532 nm laser will used for targeting.   The system will have a second set of stereographic cameras outfitted with 532 nm bandpass filters to filter out ambient light and allow detection of the targeted portion of the human pose skeleton.

## Potential results

The baseline for the project will be to implement non-real-time ranging with the Stereo Human Pose Estimation Dataset, SHPED. The stretch goal will add targeting to the system.   In real time the detected human pose skeletons in the camera view will be shown as an overlay to the subjects.  The subject's targeted skeletal region will be identified in the image and the distance to the subject displayed.  

## Discussion

This project incorporates both recent advances in deep learning applied to real-time human pose estimation, and classical techniques for unsupervised learning to implement targeting for a complex security system.  The targeting system will require accurate real-time identification of the subject’s entire skeletal frame, single target isolation, accurate distance measurement to the target, and accurate aiming.

## References

1.    G. Papandreou, T. Zhu, N. Kanazawa, A. Toshev, J. Tompson, C. Bregler, K. Murphy, Towards Accurate Multi-Person Pose Estimation in the Wild, Proceedings of the IEEE      Conference on Computer Vision and Pattern Recognition (CVPR), 2017, pp. 4903-4911
2.    J. Huang et al., “Speed/accuracy trade-offs for modern convolutional object detectors”, CVPR 2017  
3.    Z. Cao, T. Simon, S.-E. Wei, Y. Sheikh, “Realtime Multi-Person 2D Pose Estimation using Part Affinity Fields”, CVPR 2017
4.    V. Bazarevsky, I. Grishchenko, https://ai.googleblog.com/2020/08/on-device-real-time-body-pose-tracking.html


[edit](https://github.com/BurchallCooper/CS7641-Project/edit/gh-pages/index.md)    
 

