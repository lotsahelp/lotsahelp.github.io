---
layout: post
title: Missing Biometric devices Node in Device Manager
date: '2020-05-21 12:09:55 -0500'
categories:
- Windows
---
If you have a Windows Hello compatible device (face or finger-print) and you are unable to use it, make sure Device Manager has a node for Biometric devices (see below). I have a Windows Hello Face capable laptop and lost the ability to login with my face. This node was no longer there. After some back and forth with tech support, they were finally able to fix it. I'm blogging this since I could never find a good answer searching on my own and want to document the fix.

![Device Manager showing Biometric devices](/images/bio-device-manager.png)

First make sure your camera still works using your preferred app. If not, you'll want to fix that first. Next browse to the folder "C:\Windows\System32\WinBioPlugIns\FaceDriver" and right-click the two .inf files (Setup Information) and Install them. Go back into Settings -> Accounts -> Sign-in Options and Windows Hello Face should be available again.

![FaceDriver folder](/images/facedriver.png)

![Sign-in options settings page](/images/signin-options.png)
