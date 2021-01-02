---
toc: true
layout: post
use_math: true
description: What is dicom? And OHIF Viewer Walkthrough.
categories: [medicine]
title:  Introduction to DICOM Standard
image: images/dicom-intro/cover.jpg
---
[Dicom Website!](https://www.dicomstandard.org/)

**DICOM = Digital Imaging and Communications in Medicine**

DICOM standard includes a file format definition and a network communications protocol that uses TCP/IP to communicate between systems.

# Abstract

Dicom standard was conceptualized and implemented when CT (computed tomography) Scans were developed. It takes special care of not losing the information when translating the medical images and other data to digital format. This is done so as to keep it as close to original as possible. 

> The main objective of this new standard was to create an open platform for the communication of medical images and related data.


# Introduction

The contents of the DICOM standard go far beyond a definition of an exchange format for medical image data.

DICOM defines:
- data structures (formats) for medical images and related data,
- network oriented services, e. g.
    - image transmission
    - query of an image archive (PACS)
    - print (hardcopy)
    - RIS - PACS - modality integration
- formats for storage media exchange
- requirements for conforming devices and programs

<center>
<img src="https://www.softneta.com/wp-content/uploads/2019/02/SendToPACS-converting-files-to-dicom-workflow.png">
</center>

> PACS (Picture Archiving and Communication System) provides economical storage of, and convenient access to, images from multiple modalities (source machine types).


> RIS (Radiology Information System) is a computerized database used by radiology departments to store, manipulate, and distribute patient radiological data and imagery.

<center>
<img src="https://assets.extrahop.com/images/infographics/dicom-protocol-workflow.png">
</center>

PACS-RIS integration improves the flow of images for the radiologist. They communicate using some set of commands (called HL7) concerned with Admission/Discharge/Transfer (ADT) and Order/Entry.

## DICOM Data Structures

Dicom consists of list of image attributes which contain vast amount of image and medical information:
- patient information (name, sex, identification number)
- modality and imaging procedure information (device parameters, calibration, radiation dose, contrast media)
- image information (resolution, windowing)

Dicom goes to the lengths and defines significance of each data element in multitude of cases. It defines if an attribute is required, optional or important for certain cases. But this feature comes at a cost. 
1. Image objects are frequently incomplete : There is inconsistency in filling all the fields with the data. Some fields in image objects are often are left blank and some are filled with incorrect data. 
2. Another problem occurs when displaying an image on a device that is made from different manufacturer, because different imaging equipment use different amplitude ranges and the same number of allocated bits. In that case, images can be displayed as underexposed or overexposed with poor contrast, so those parameters should be adjusted manually.

## DICOM Network Services

This service is based over client-sever concept. They (Dicom applications) establish connection to exchange information. In addition to image transmission, there are other features too:

- Image Archive Service: search images in a PACS archive by certain criteria (patient, time of creation of the images, modality etc.) and to selectively download images from this archive.
- Print Service: gives access to cameras and printers over a network.
- Modality Worklist Service: Download updated information regarding patient using above decribed PACS-RIS system.

### Patient Model

Queries to image archives (PACS) are made in 4 level of DICOM hierarchy:


![](https://1.bp.blogspot.com/-FcaeZix9gSQ/TtgAwmRrxlI/AAAAAAAAKvc/neMY9qqoy6U/s1600/DICOM-DATA-MODEL.png "DICOM data model")


<!-- <center>
<img src="https://1.bp.blogspot.com/-FcaeZix9gSQ/TtgAwmRrxlI/AAAAAAAAKvc/neMY9qqoy6U/s1600/DICOM-DATA-MODEL.png">
</center>
<center>DICOM data model</center> -->

Patient has  studies, studies have series which are scans and scans may have multiple instances or images (which are slices in CT scans).

1. Patient Level
    - How many studies are there for this patient
    - How many Series are there for this patient (in all studies)
    - How many Instances (images) are there for this patient (in all series)
2. Study Level (Patient and Study roots)
    - How many Series are there in this study
    - How many Instances (images) are there in this study (in all series)
3. Series Level
    - How many Instances (images) are there in this series
4. Instance Level

<center>

<img src="/blog/images/dicom-intro/query.png">
<!-- <table style="width:100%">
<thead>
<tr>
<th>Attribute Name</th>
<th>Tag</th>
<th>Attribute Description</th>
</tr>
</thead>

<tbody>
<tr>
<td>Number of Patient Related Studies</td>
<td>(0020,1200)</td>
<td>The number of studies that match the Patient level Query/Retrieve search criteria</td>
</tr>

<tr>
<td>Number of Patient Related Series</td>
<td>(0020,1202)</td>
<td>The number of series that match the Patient level Query/Retrieve search criteria</td>
</tr>

<tr>
<td>Number of Patient Related Instances</td>
<td>(0020,1204)</td>
<td>The number of composite object instances that match the Patient level Query/Retrieve search criteria</td>
</tr>

<tr>
<td>Number of Study Related Series</td>
<td>(0020,1206)</td>
<td>The number of series that match the Study level Query/Retrieve search criteria</td>
</tr>

<tr>
<td>Number of Study Related Instances</td>
<td>(0020,1208)</td>
<td>The number of composite object instances that match the Study level Query/Retrieve search criteria</td>
</tr>

<tr>
<td>Number of Series Related Instances</td>
<td>(0020,1209)</td>
<td>The number of composite object instances in a Series that match the Series level Query/Retrieve search criteria</td>
</tr>

<tr>
<td>SOP Classes in Study</td>
<td>(0008,0062)</td>
<td>The SOP Classes contained in the Study.</td>
</tr>
</tbody>
</table> -->

</center>

Above table is sourced from [here](https://www.medicalconnections.co.uk/kb/counting-studies-series-and-instances/)


## Media Exchange

DICOM defines **application profiles** which defines how media is exchanged:
- Encoding formats and compression schemes used (e. g. only uncompressed or loss-less JPEG)
- Storage medium used
- Images from which modalities may be present on the medium (X-Ray Angiography images, etc)

DICOM directory: each dicom medium contains this directory which contains information (patient name, modality, unique identifiers etc.) for all images which are captured on the medium. 

## Device Conformance

Each DICOM supporting device must also specify which DICOM services and options are supported, which extensions and peculiarities have been implemented by the vendor, and how the device communicates with other DICOM systems.


# DICOM File Format
The DICOM standard is divided in 2 parts:

A DICOM file consists of a header and image data sets

<center>

<img src="/blog/images/dicom-intro/packet.jpg">

</center>

**Preamble** is used to access the images and other data in DICOM file.

**Prefix** contains the string “DICM” as uppercase characters.

**Data Set** is the representation of real world information.

**Data Elements**. There are 5 types of Data elements: 
- Type 1 Required Data elements, 
- Type 1C Conditional Data Elements, 
- Type 2 Required Data Elements, 
- Type 2C Conditional Data Elements
- Type 3 optional Data Elements.


# Clinical Terms

Some clinical terms to be aware of!

## Slice Thickness

Slice thickness refers to the (often axial) resolution of the scan.
> Patient Position (0018, 5100) will tell you if the patient was scanned head-first supine, feet-first prone, head-first prone, etc. Instance Number (0020, 0013), also commonly known as slice number, contains no information about spatial location and isn't even guaranteed to be unique. Slice Location (0020, 1041) is useful, if it exists, but you can't count on it always existing because it's a Type 3 (optional) attribute. To have a robust solution, you need to use Image Position Patient (0020, 0032) together with Image Orientation Patient (0020, 0037) and Patient Position (0018, 5100) to properly order the slices in space. Image Position Patient gives you a vector from the origin to the center of the first transmitted pixel of the image. Image Orientation Patient gives you vectors for the orientation of the rows and columns of the image in space. Patient Position tells you how the patient was placed on the table relative to the coordinate system.

Sourced from [stackoverflow](https://stackoverflow.com/questions/6597843/dicom-slice-ordering)



## Hu Scaling (Hounsfield scale)
HU is a quantitative scale for describing radiodensity. HU's is standardized across all CT scans regardless of the scanner detector. 

$$HU = 1000 * \frac{\mu - \mu_{water}}{\mu_{water} - \mu_{air}}$$

Where $\mu$ is linear attenuation coefficient.


<center>


<table class="wikitable" style="width:100%">

<tbody><tr>
<th colspan="2">Substance</th>
<th>HU
</th></tr>
<tr>
<td colspan="2"><a href="/wiki/Air" class="mw-redirect" title="Air">Air</a>
</td>
<td>−1000
</td></tr>
<tr>
<td colspan="2"><a href="/wiki/Fat" title="Fat">Fat</a>
</td>
<td>−120 to −90
</td></tr>
<tr>
<td colspan="2">Soft tissue on <a href="/wiki/Contrast_CT" title="Contrast CT">contrast CT</a>
</td>
<td>+100 to +300
</td></tr>
<tr>
<td rowspan="2"><a href="/wiki/Bone" title="Bone">Bone</a></td>
<td>Cancellous</td>
<td>+300 to +400
</td></tr>
<tr>
<td>Cortical</td>
<td>+1800 to +1900
</td></tr>
</tbody></table>


</center>

Here's a quick list of a few useful ones, sourced from [Wikipedia](https://en.wikipedia.org/wiki/Hounsfield_scale).

## Windowing
Since it is difficult to recognize 4000 shades of gray easily, we use windowing. It limits the number of Hounsfield units that are displayed. 

For example, if we want to examine the soft tissue in one CT scan we can use a window level of 40 and a window Width of 80 this will cover 40 units below and above the window level and the tissues with CT numbers outside this range will appear either black or white. A narrow range provides a higher contrast.

The **window width** is the range of the grayscale that can be displayed. The center of grayscale range is referred to as the **window level**.

<center>
<img src="https://i0.wp.com/myctregistryreview.com/wp-content/uploads/2019/08/wwwl-graphic.jpg?resize=1024%2C791&ssl=1">
</center>

You can go throught [this blog](https://kevalnagda.github.io/ct-windowing) to learn more about windowing.

# Walkthrough Ohif Viewer

This section contains steps on how to use ohif viewer for viewing dicom file based medical records (studies, series, instances).

## Homepage
Here you see the studies you have uploaded. Each study have series and these series have instances / images.
<center>
<img src="/blog/images/dicom-intro/ohif-home.png">
</center>

Studies can be uploaded using `+` button on right. Example is shown below.

<center>
<img src="/blog/images/dicom-intro/ohif-home.gif">
</center>

## Details on Scan View

<center>
<img src="/blog/images/dicom-intro/ct-scan-view.png">
</center>

## Calibrating Windowing
<center>
<img src="/blog/images/dicom-intro/ohif-change-WL.gif">
</center>

Vertical mouse movement changes window level and horizontal movement changes window width.

## Visualization of slices

<center><img src="/blog/images/dicom-intro/ohif-slice.gif"></center>

## 2D Multiplanar Reconstruction (MPR)

<center><img src="/blog/images/dicom-intro/ohif-2d-mpr.png"></center>

The MPR tool provided within the Viewer can be used to reconstruct images in orthogonal planes (coronal, sagittal, axial or oblique, depending on what the base image plane is). This can help to create a visualization of the anatomy which was not possible using base images alone.

## Report view

<center><img src="/blog/images/dicom-intro/ohif-report.png"></center>

Report view has report on doc/pdf format displayed in viewer.

# References

- ["Significance of digital imaging and communication in medicine in digital imaging". digitmedicine.com](https://www.digitmedicine.com/article.asp?issn=2226-8561;year=2015;volume=1;issue=2;spage=63;epage=66;aulast=Gupta)
- [Dicom Website](https://www.dicomstandard.org/)  
- [Dicom @ OFFIS](https://dicom.offis.de/)
- ["Picture archiving and communication system". medico-eng.com](http://www.medico-eng.com/en/products.php?id=71)
- ["The case for RIS/PACS integration". PUBMED NCBI](https://pubmed.ncbi.nlm.nih.gov/12080929/)
- ["Counting Studies, Series and Instances". medicalconnections.co.uk](https://www.medicalconnections.co.uk/kb/counting-studies-series-and-instances/)


