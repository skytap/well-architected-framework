Gathering Infrastructure and/or Performance data

For Power (IBMi, AIX/VIOS, Linux)

**Goal** -- Shorten the time required to optimize the performance in
Skytap's cloud.

**Background** -- The **[only way]{.ul}** to ensure the best performance
experience for any IBM Power customer in Skytap's infrastructure is to
have a complete understanding of the customer current hardware
configuration(s) and performance data. This includes Power model,
PowerVM settings, disk models and configuration, memory allocation(s)
and performance metrics for all the above.

Most customer(s) don't have the knowledge or access to easily gather the
data we need, especially performance data. The options below are steps,
with pros and cons, to help customer gather either infrastructure and/or
performance data.

At a minimum, we should insist on infrastructure data, this would help
us configure each VM that is close to the current configuration. (This
assumes the current configuration is optimally configure, which is rare)

**IBM Power Systems (All OSs)**

**HMC Scanner**

**Pros** -- Contains the hardware infrastructure configuration for all
system connected to the HMC

**Cons** -- Memory - only allocation data-no performance data. Disk --
only allocation data-no performance data. NOTE: some performance data is
available but by default is not collected. Once enabled should wait at
least 1 week before collecting data.

**Output** -- Excel spreadsheet -- customer send to Skytap

Requirements: HMC Scanner is a java program that can be download from
the following link. Customer must have access and PW for hscroot.

<https://www.ibm.com/support/pages/hmc-scanner-power-server-config-and-performance-stats>

Example of Windows command:

C:\\user\\rwatson\\downloads\\hmcscanner.bat \<HMC IP> -p xxxxxx -stats
-sanitize

**IBMi**

**Option 1** -- Save file of \*MGTCOL object

**Pros** -- Contains 1-5 days of Collection Service data which has
**ALL** infrastructure and performance data for each partition

**Cons** - Large file(s) = 1-5GB from each partition. Analyst must
upload to an IBMi partition, restore and use performance tools,
Navigator for i, or 3^rd^ party products to analyze

**Output** -- IBMi save file from each partition

Examples of IBMi commands from each partition

crtsavf qgpl/pfrcustnan

savobj obj(\*all) lib(qpfrdata) dev(\*dev) savf(qgpl/pfrcustnam)
dtacpr(\*yes)

**Option 2** -- Using Must Gather Tools

**Pros** -- Nothing to install. Customers follow instructions below on
each partition. Option 'b' disk data is a small output \~2-10MB

**Cons** -- 70+ text file. Must be V7R2 and above. Snapshot of current
configuration \~xxGB

**Output** -- Zip file called - \<system name>\_SYSSNAP_202108271403.zip
or /tmp/diskmetrics\_\<sysnam>.zip for disk only data

1\. Without installing any software - Little or no historical data

  a. If customer is at V7R2 IBMi or later do the following:

      1. Go to the Must Gather Tools menu - GO QMGTOOLS/MG

      2. First check for any updates, option 13

      3. Select option 3 - Collection Services

      4. Select option 2 - Copy Performance Data - This option will
create a save file of 1 or more Collection Services data.   The save
file will be \'xx\' GB in size. This will give us the infrastructure and
performance data from that one day

          Set option Save file to \"Y\" and hit enter

          Select the first QxxxHHMMSS member not in use - enter

       5. Send Skytap the save file that is created in the IFS in
directory /tmp/IBMCSDATA/IBMDATA002

   b. If all we need is the disk metrics then do the following:

       1. Go to the Must Gather Tools menu - GO QMGTOOLS/MG

       2. Select option 3 - Collection Services

       3. Select option 15 - Collect disk metrics from CS

       4. Next screen just hit enter

       5. This will create a zip file in /tmp/diskmetrics\_\<sysnam>.zip

       6. Send Skytap the diskmetrics zip file

**Option 3** -- Performance Navigator from HS

**Pros** -- Automates the historical collection of infrastructure and
performance data -- Small file to send \~1-10MB. Exclusive What-If
function to model workload to different system. Track every job and user
resource consumption history. Helps to address customer performance
questions. Free access to infrastructure data and high-level CPU
utilization and disk space (both installed and used)

**Cons** -- Access to full options requires license.

2\. Collecting historical data with no overhead and very little disk
space

   a. download a Windows Client and IBMi host code using WinScp or
Filezilla from [ftp.mpginc.com](http://ftp.mpginc.com/).  Download both
perfnav.exe and pn400.sav

   b. Click on perfnav.exe to install the Windows component.   This is a
self-extracting install program that just puts an ICON on the desktop

   c. The first time you launch perfnav, it will prompt you to install
the IBMi host code pn400.sav.   Reply YES.  A PN400 install window will
appear

   d. In tab 1.local, browse to the pn400.sav file you downloaded.  Then
follow tab 2-5 on each partition

   e. The next day I start Perfnav and reply NO to the PN400.sav
install.   In the System Information window, enter the system name or IP
address of the first LPAR and hit NEW.   NOTE:  This will create a
connection to the partitions using ACS ODBC driver

   d. Connect to all the other lpars via the FIle / New IBMi, enter
system name or IP address and click NEW

   e. Once connected to all lpars, go to File/SOS, highlight the first
system name and Shift Click the last system name.  THen click Save to
Disk.   This will create one file for each partition called
sos_serial#xx.GpD.   NOTE the directory in the log.

   f. Then just email Skytap the sos files.   They are usually very
small 1-3MB.

Other References:

<https://www.ibm.com/support/pages/mustgather-copying-and-sending-collection-services-performance-data-files-previous-day>  

<https://www.ibm.com/support/pages/qmgtools-must-gather-data-collector-users-guide> - 

<https://www.ibm.com/support/pages/node/684977> - How to obtain and
install QMGTOOLS

<https://www.ibm.com/support/pages/node/706461> - System Snapshot 

**AIX/VIOS**

**Option 1** -- Collecting Topas data

**Pros** -- By default AIX keeps 7 days of historical Topas data.
Contains infrastructure and performance data. Includes graphic view of
all data using Google graphs - Free

**Cons** -- Only 7 days and little data regarding memory or disk

**Output** -- 1 file per partition called
pninfo_topas\_\<hostname>\_date_time.tar.gz

1.  Using WinScp or FileZilla, download rackdata.tar.gz from
    [ftp.mpginc.com](ftp://ftp.mpginc.com)

2.  SFTP or FTP to /tmp on each lpar

3.  Unzip and untar - cd /tmp;gunzip -f rackdata.tar.gz;tar -xvf
    rackdata.tar;./rackdata.sh -T

4.  Send file in /tmp called pninfo_topas\_*hostname*\_2021mmdd_mmss.tar
    from each lpar

File should be between 5-10MB.

**Option 2** -- Collecting existing nmon data

**Pros** -- If customer has existing nmon data, we can use this data.
Nmon data has more info than Topas. Usually more than 7 days. Customer
normally consolidate nmon data to single shared directory. So, 1 script
will gather data from all partitions - Free

Cons -- File could be large depending on amount of history. \~10-25GB.
-- No graphical view

**Output** -- 1 file containing all partition in shared directory called
pninfo_multiple_AIX.tar.gz

1.  Using WinScp or FileZilla, download pnbuild.tar.gz from
    [ftp.mpginc.com](ftp://ftp.mpginc.com)

2.  SFTP or FTP to same directory where existing nmon exists.

3.  Unzip and untar - cd /tmp;gunzip -f pnbuild.tar.gz:tar -xvf
    pnbuild.tar; ./pnbmpgd2gzip.sh

4.  Send file called pninfo_multiple_AIX.tar.gz to Skytap

**Option 3** -- Start Collecting historical nmon data

**Pros** -- Starts the management of the historical collection of nmon.
Free Includes graphic view of last 7 day with Google graphs

**Cons** -- Will have to wait a week or two to receive data

**Output** -- 1 file per partition called pninfo\_\<hostname>.AIX.tar.gz

1.  Using WinScp or FileZilla, download powernav.tar.gz from
    [ftp.mpginc.com](ftp://ftp.mpginc.com)

2.  SFTP or FTP to /tmp on each partition.

3.  Unzip and untar - cd /tmp;gunzip -f powernav.tar.gz;tar -xvf
    powernav.tar; ./install.sh

4.  Send file in /usr/local/mpg called pninfo\_\<hostname>\_AIX.tar.gz
    to Skytap

**Linux**

**Only option**

**Pros** -- Starts the management of the historical collection of nmon.
Free Includes graphic view of last 7 day with Google graphs

**Cons** -- Will have to wait a week or two to receive data

**Output** -- 1 file per partition called
pninfo\_\<hostname>.Linux.tar.gz

1.  Using WinScp or FileZilla, download powernav.tar.gz from
    [ftp.mpginc.com](ftp://ftp.mpginc.com)

2.  SFTP or FTP to /tmp on each partition.

3.  Unzip and untar - cd /tmp;gunzip -f powernav.tar.gz;tar -xvf
    powernav.tar; ./install.sh

4.  Send file in /usr/local/mpg called pninfo\_\<hostname>\_Linux.tar.gz
    to Skytap
