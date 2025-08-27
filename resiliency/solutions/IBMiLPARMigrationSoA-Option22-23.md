---
title: IBM i workload migration using Go Save/Restore, Option 22-23
description: Skytap Cold Migration Solution - IBM i workload migration using Go Save/Restore, Option 22-23
author: Sarah Allen - Cloud Solutions Architect
---

# IBMi LPAR Migration to {{site.SoA}}
Updated February 2022

This guide is provided “as-is”. Information and views expressed in this document, including URL and other Internet website references, may change without notice and usage of the included material assumes this risk.

This document does not provide you with any legal rights to any intellectual property in any product. You may copy and use this document for your internal, reference purposes.

### Table of Contents <a name="toc"></a>

* [Key Takeaways](#takeaways)
* [Before you begin](#begin)
* [Determining size to be backed up on ISO optical image](#determineisosize)
* [Create virtual optical media](#createvom)
* [Save, Option 22 to virtual optical media](#gosave)
* [Create virtual tape media](#createvtm)
* [Deploy IBM i in {{site.Brand}} cloud](#deployinskytap)
* [Network configuration](#netconfig)
* [Go Restore, Option 23](#gorestore)
* [APPENDIX](#appendix)
  * [Example Commands](#examplecommands)
  * [Create virtual tape image catalog](#createvtic)


### Key Takeaways<a name="takeaways"></a>

The purpose of this document is to provide the steps and instructions
necessary to migrate an IBMi LPAR from on premise to an environment
within {{site.SoA}}.

There are various strategies that can be used to migrate IBMi LPARs to
{{site.Brand}}, including BRMS + ICC, restoration using a NIS server, and Go
Save/Restore with Option 22 and 23. This document will describe the
process to perform the migration using Go Save/Restore Option 22 and 23.

Supplemental links to {{site.Brand}} and IBM documentation will be included
throughout.

#### Before you begin<a name="begin"></a>

Pre-requisites:

-   Azure Storage Account

-   Install latest PTFs on IBM i

-   Utilized ASP should be less than 48% in IBM i LPAR. Amount of disk
    space can be reduced by doing clean up as mentioned in below link:

[[https://www.ibm.com/support/pages/reducing-system-asp-disk-space-dasd-storage-used]{.ul}](https://www.ibm.com/support/pages/reducing-system-asp-disk-space-dasd-storage-used)

IBM i Workload Migration

High level summary of steps

1.  Create Azure Subscription for {{site.SoA}}

> Link to {{site.Brand}} documentation:
> <https://help.skytap.com/creating-a-skytap-on-azure-account.html>

2.  Create {{site.Brand}} Users to support the POC

> Link to {{site.Brand}} documentation:
> <https://help.skytap.com/users-create.html#:~:text=Click%20Create%20New%20User.,also%20activates%20the%20user's%20browser>.

3.  Free up space on LPAR - Add extra disk storage to get usage below
    45%

4.  Create an Option 22 & 23 saves to internal disks

5.  Using azcopy or Storage Explorer transfer the backup to an Azure
    storage account

> Link to Microsoft Azure azcopy documentation:
> <https://docs.microsoft.com/en-us/azure/storage/common/storage-use-azcopy-v10>
>
> Azcopy SFTP documentation:
> <https://docs.microsoft.com/en-us/azure/storage/blobs/secure-file-transfer-protocol-support-how-to?tabs=azure-portal>

6.  Provide credentials to this Storage Account to {{site.Brand}}

> <https://help.skytap.com/kb-import-azure-blob-storage.html#how-do-i-find-the-details-for-my-microsoft-azure-blob-storage-container>

7.  Build shell LPARs in {{site.Brand}}

8.  Install LIC and initialize system

> <https://www.ibm.com/docs/en/i/7.4?topic=partition-installing-licensed-internal-code-new-logical>

9.  Disk configuration

10. OS installation

> <https://www.ibm.com/docs/en/power8?topic=systems-installing-i>

11. Download backup using azcopy or Storage Explorer to the LPAR

> <https://www.skytap.com/blog/vm-import-azure-blob-storage-with-skytap/>

12. Restore using Option 22 (*Detailed instructions below)*

13. Restore using Option 23 (*Detailed instructions below)*

14. High-level validation of restore

15. Creation of template for each restored LPAR

> Link to {{site.Brand}} documentation:
> <https://help.skytap.com/saving-an-environment-as-a-template.html>

16. Start each LPAR and confirm successful IPL

IBM i Workload Migration

Using Go Save/Restore, Option-22,23

1.  **Objective**:

-   Demonstrate how an IBM i workload can be migrated to {{site.Brand}} cloud
    using Go Save, Option 22,23.

-   Full-system recovery including LIC, OS installation

2.  **Pre-requisites:**

-   Install latest PTFs on IBM i

-   Utilized ASP should be less than 48% in IBM i LPAR. Amount of disk
    space can be reduced by doing clean up as mentioned in below link:

[[https://www.ibm.com/support/pages/reducing-system-asp-disk-space-dasd-storage-used]{.ul}](https://www.ibm.com/support/pages/reducing-system-asp-disk-space-dasd-storage-used)

![Diagram Description automatically
generated](I:\Repos\{{site.Brand}}\WAF\resiliency\migrationmedia2\/media/image1.png){width="6.268055555555556in"
height="8.646527777777777in"}

3.  **High level Steps:**

```{=html}
<!-- -->
```
1.  Save Option 22 on optical media to create an ISO image

2.  Save QGPL and QUSRSYS and append to existing ISO

3.  Save Remaining data to virtual tape(s) via option 23 save

4.  Restore Option 22 with ISO

5.  Restore QUSRSYS and QGPL to get network

6.  Restore remaining data via virtual tape

```{=html}
<!-- -->
```
4.  **Determining size to be backed up on ISO optical image**

```{=html}
<!-- -->
```
1.  Go Disktasks, Option-1 to collect disk space information

2.  OR, same can be run using RTVDSKINF command

3.  RTVDSKINF job will be submitted in WRKACTJOB, depending on the
    system size, command completion may vary system to system

4.  Once the job completes, print information using PRTDSKINF or Go
    Disktasks, Option 2.

5.  In report type, select library

6.  WRKSPLF, display PRTDSKINF report

7.  Review QUSRSYS, QGPL, QSYS, LIC and IBM libraries size. Accordingly,
    create the optical media size

```{=html}
<!-- -->
```
5.  **Create virtual optical media**

[[https://www.ibm.com/docs/en/i/7.2?topic=storage-setting-up-virtual-optical]{.ul}](https://www.ibm.com/docs/en/i/7.2?topic=storage-setting-up-virtual-optical) 

1.  CRTIMGCLG IMGCLG(skytap30GB) DIR(\'/skytap30GB\') 

2.  ADDIMGCLGE IMGCLG(skytap30GB) FROMFILE(\*NEW) TOFILE(skytap30GB.iso)
    IMGSIZ(30000)

3.  CRTDEVOPT DEVD(skyopt30gb) RSRCNAME(\*VRT)

4.  VRYCFG CFGOBJ(skyopt30gb) CFGTYPE(\*DEV) STATUS(\*ON)

5.  LODIMGCLG IMGCLG(skytap30GB) DEV(skyopt30gb)

6.  INZOPT NEWVOL(MYVOLUMEID) DEV(skyopt30gb) CHECK(\*NO)
    TEXT(DESCRIPTION)

```{=html}
<!-- -->
```
6.  **Save, Option 22 to virtual optical media**

```{=html}
<!-- -->
```
1.  Bring system to restricted state using ENDSBS \*ALL \*IMMED

2.  Go Save, Option22 and take below parameters on the command

![A screenshot of a computer Description automatically generated with
medium
confidence](I:\Repos\{{site.Brand}}\WAF\resiliency\migrationmedia2\/media/image2.png){width="6.134722222222222in"
height="3.0555293088363955in"}

![A screenshot of a computer Description automatically generated with
medium
confidence](I:\Repos\{{site.Brand}}\WAF\resiliency\migrationmedia2\/media/image3.png){width="6.134830489938758in"
height="2.8847222222222224in"}

1.  End subsystems again to save QGPL and QUSRSYS

2.  Save QGPL and QUSRSYS and append to existing ISO in Optical device

3.  SAVLIB, F4 to prompt: (QGPL first, then QUSRSYS)

-   Choose Existing Optical device

-   F10 for more options

-   Change parameters: 

-   Spooled file data = \*NONE, to not include output queues 

-   Save file data = \*NO 

7.  **Create virtual tape media**

Create virtual tape image Catalog for Option 23 data

[[https://www.ibm.com/docs/en/i/7.3?topic=tape-setting-up-virtual-storage]{.ul}](https://www.ibm.com/docs/en/i/7.3?topic=tape-setting-up-virtual-storage)

1.  CHGATR OBJ(\'/tape/catalog1\') ATR(\*ALWSAV) VALUE(\*Yes)
    Subtree(\*All)

2.  Determine tape size based on over-all data size, you must pre-create
    each tape required for the total Option 23 save.

3.  Bring system to restricted state and start Go SAVE, Option23

```{=html}
<!-- -->
```
8.  **Deploy IBM i in {{site.Brand}} cloud**

```{=html}
<!-- -->
```
1.  Transfer the Optical ISO image to IBM i NFS server and follow the
    steps for IBM i recovery using IBM i NFS server(separate doc.
    attached)

2.  On Install LIC screen, take Option 2 to Install LIC and Initialize
    system

> ![Graphical user interface, text Description automatically generated
> with medium
> confidence](I:\Repos\{{site.Brand}}\WAF\resiliency\migrationmedia2\/media/image4.png){width="6.268055555555556in"
> height="1.4965277777777777in"}

3.  Post LIC installation, do disk configuration

4.  Initialize non configured disks and add to ASP

5.  Install OS using below option:

![A screenshot of a computer Description automatically generated with
medium
confidence](I:\Repos\{{site.Brand}}\WAF\resiliency\migrationmedia2\/media/image5.png){width="6.268055555555556in"
height="2.875in"}

6.  Enable automatic configuration -- Y on set major system option
    screen.

![Graphical user interface Description automatically generated with low
confidence](I:\Repos\{{site.Brand}}\WAF\resiliency\migrationmedia2\/media/image6.png){width="6.0in"
height="1.4256944444444444in"}

7.  Change following on next screen:

-   Start print writers = N

-   Start system to restricted state = Y

-   Set major system options = Y

-   Define or change system at IPL = Y

8.  Select Option 3 twice to change the following system values:

-   QALWOBJRST \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ \*ALL

-   QFRCCVNRST \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ 0

-   QINACTITV \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ \*NONE

-   QJOBMSGQFL \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ \*PRTWRAP

-   QJOBMSGQMX \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ 30 (minimum, 64
    recommended)

-   QLMTDEVSSN \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ 0

-   QLMTSECOFR \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ 0

-   QMAXSIGN \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ \*NOMAX

-   QPFRADJ \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ 2

-   QPWDEXPITV \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ \*NOMAX

-   QSCANFSCTL \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ \*NOPOSTRST

-   QVFYOBJRST \_\_\_\_\_\_\_\_\_\_\_\_\_\_\_ 1

9.  Press F3 twice and change the QSECOFR password. *Note: the current
    QSECOFR password will be QSECOFR (capital letters)*

10. Restore QGPL and QUSRSYS

-   RSTLIB, F4 to prompt

-   Select QGPL and QUSRSYS

-   Select existing optical Device

-   Select ALWOBJDIF as \*All and MBROPT as \*All

11. Go Restore, Option 22

```{=html}
<!-- -->
```
9.  **Network configuration**

```{=html}
<!-- -->
```
1.  WRKHDWRSC \*CMN, check resource name of ethernet port

2.  Delete existing Ethline

3.  Create new Ethline

4.  Configure default route and DNS

5.  IPL the LPAR in normal mode

```{=html}
<!-- -->
```
10. **Go Restore, Option 23**

```{=html}
<!-- -->
```
1.  Create virtual tape image catalog

2.  Add virtual tape(s) to image catalog in directory above

3.  Load image catalog to virtual tape drive

4.  GO Restore Option 23 from virtual tape image catalog above 

5.  Choose virtual tape device, and other parameters as below:

![A screenshot of a computer Description automatically generated with
medium
confidence](I:\Repos\{{site.Brand}}\WAF\resiliency\migrationmedia2\/media/image7.png){width="5.916666666666667in"
height="2.2604166666666665in"}

6.  Check for job log once the restore is completed using DSPJOBLOG

7.  Save the job log using DSPJOBLOG Output(\*Print)

8.  Restore any not saved object

9.  Data Restoration is complete at this point

10. Check PTF status and change the system values as required
