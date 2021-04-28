---
title: DICOM SOP
tags: ["dicom"]
categories: dicom
date: 2021-04-28
---

# DICOM SOP

DICOM is a standard representing a diverse information, including not only various medical images
but also documents like Structured Report (SR) or PDF or even messages for communication.

Therefore `what kind of data is this DICOM file` should be known, and this is called SOP Class.
Let's have a look at an X-Ray image for example.

    (0008,0016) UI 1.2.840.10008.5.1.4.1.1.1.1        # 1, 28 SOP Class UID
