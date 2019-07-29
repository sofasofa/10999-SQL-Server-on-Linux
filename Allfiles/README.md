# 10999A: SQL Server on Linux

The files for the Allfiles drive are not provided on the course VMs, but are stored in this folder on GitHub. This enables us to update the files more easily as the technology changes.

> **Important**: Many of these zips are multipart and cannot be unzipped easily using the native file compression features of File Explorer. To extract these files, you should use a utility such as 7-Zip, WinRAR, or WinZip.

The following steps must be completed to ensure the Allfiles function correctly in the VMs:

1. Download the zip for each module from this folder, and extract the files to the **D:** folder on the **10999A-MIA-DC** and **10999A-MIA-SQL** VMs. The file structure should be:
    ```
    D:\Demofiles\Mod01\...
    D:\Demofiles\Mod02\...
    D:\Demofiles\Mod03\...
    ... etc.
    D:\Labfiles\Lab01\...
    D:\Labfiles\Lab02\...
    D:\Labfiles\Lab03\...
    ... etc.
    ```

2. On both VMs, once the files are extracted, view the properties of the **D:** drive, and on the **Security** tab, grant the **Everyone** group **Full Control** access to the drive and all its subfolders.

3. In the **D:** drive, view the properties of each folder (**Demofiles**, **Labfiles**, and **Setupfiles**) in turn and clear the **Read-only** check box so that no files on the **D:** drive are set to read-only.
