# Combining Laser Designated Targeting and Deep Learned Stereo Human Pose Estimation to Enable non-Lethal Threat Deterrence and Neutralization

## Introduction

Advances in deep learning allow for accurate real-time pose estimation[1][2][3]which can be applied to security applications for the deterrence of intruders with potential hostile intentions, and the non-lethal neutralization of these hostile actors.  This project will describe the full system implementation shownn in Figure 1 focusing primarily on the targeting implemenation of the system which uses deep learning for pose estimation, stereography for ranging, and laser designation to implement a closed loop targeting system.  A proof of concept is implemented for this targeting system.

<p align="center">
 Figure 1: System Implementation
</p>

## Problem definition

The architecture of the security system described in this paper has 3 distinct functions: (1) threat detection, (2) targeting, and (3) threat neutralization.  In systems meant only for deterrence, this last function may not exist.

Threat detection may be as simple as a perimeter violation where some unidentified individual crosses a predefined boundary into a controlled area.   However, it may be much more sophisticated where a weapon is detected in the hand of an individual by the threat detection module.  Ideally, this module would be sufficiently intelligent to differentiate between friendlies and hostiles carrying weapons.

The targeting function performs has two purposes.  First, realization by an individual that he is being actively targeted may sufficient deterrence that no further action is required.  Second, in systems where threat neutralization exists as a function the targeting also performs aiming for the discharge of rubber bullets.

Threat neutralization for this system is envisioned as using rubber bullets.  The purpose of the targeting system is to not only aim the rubber bullet, but more specifically aim the bullet a the lower body extremeties to disable and not permanently injure.  

## Methods

This work will use deep learned pose estimation techniques to capture the human pose skeletons of multiple subjects within the camera’s view similar to Figure 2.  The pose estimation will result in a set of connected two dimensional coordinates for each person detected.   Stereography coupled with non-supervised learning techniques will then augment this data with depth information for each subject.  

A 532 nm laser will perform the targeting.   The system will have a second set of stereographic cameras outfitted with 532 nm bandpass filters to filter out ambient light and allow detection of the targeted portion of the human pose skeleton.

<p align="center">
  <img src="https://1.bp.blogspot.com/-nsLiFUVt6S4/XzVpLWay6VI/AAAAAAAAGXI/oPyuvuQEFcASODqPdT9dqptyUvUuGlTvACLcBGAsYHQ/s427/image3.gif" alt="Pose Estimation" >
</p>

<p align="center">
 Figure 2: Google's MediaPipe Pose Estimation[4]
</p>

## Potential results

The proof of concept will demonstrate the feasibility of the targeting portion of the system.   In real time the detected human pose skeletons in the camera view will be shown as an overlay to the subject.  The specific subject targeted area of the subject will be identified in the image and the distance to the subject displayed.

## Discussion

This project incorporates both recent advances in deep learning applied to real-time human pose estimation, and classical techniques for unsupervised learning to implement targeting for a complex security system.  The targeting system will require accurate real-time identification of the subject’s entire skeletal frame, single target isolation, accurate distance measurement to the target, and accurate aiming.

## References

1.    G. Papandreou, T. Zhu, N. Kanazawa, A. Toshev, J. Tompson, C. Bregler, K. Murphy, Towards Accurate Multi-Person Pose Estimation in the Wild, Proceedings of the IEEE      Conference on Computer Vision and Pattern Recognition (CVPR), 2017, pp. 4903-4911
2.    J. Huang et al., “Speed/accuracy trade-offs for modern convolutional object detectors”, CVPR 2017  
3.    Z. Cao, T. Simon, S.-E. Wei, Y. Sheikh, “Realtime Multi-Person 2D Pose Estimation using Part Affinity Fields”, CVPR 2017
4.    V. Bazarevsky, I. Grishchenko, https://ai.googleblog.com/2020/08/on-device-real-time-body-pose-tracking.html


[edit](https://github.com/BurchallCooper/CS7641-Project/edit/gh-pages/index.md) 


