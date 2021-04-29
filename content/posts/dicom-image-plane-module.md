---
title: DICOM Image Plane Module
tags: ["dicom", "3D", "linear algebra"]
categories: dicom
date: 2021-04-29
---

대부분의 단일 DICOM 이미지는 2차원 단면 이미지입니다.
CT나 MRI와 같은 3차원 영상도 많은 단면들이 각각 개별 DICOM 파일로 만들어지고 이를 하나의 시리즈로 묶어서 보게 되죠.
이 단면들로부터 3D recon (재구성)을 한다던가
[cross reference](https://www.radiantviewer.com/dicom-viewer-manual/cross-reference_lines.html) 기능을 구현하고자 한다던가
하는 3차원 공간 좌표에 대한 작업이 필요한 경우, 우리는 각 단면이 실제 3차원 공간에서 어떤 위치에 있었는지를 알아야 합니다.

![plane-location](/plane-location.png "어느 단면을 찍은 이미지일까요?")
