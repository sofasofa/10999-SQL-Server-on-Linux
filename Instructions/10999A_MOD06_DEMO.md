# Module 6: Migrating Databases to SQL Server on Linux and Disaster Recovery
- [Module 6: Migrating Databases to SQL Server on Linux and Disaster Recovery](#module-6-migrating-databases-to-sql-server-on-linux-and-disaster-recovery)
  - [Lesson 1: Backup and restore](#lesson-1-backup-and-restore)
    - [Demo 1: Backup and restore](#demo-1-backup-and-restore)
      - [Create a SQL Server on Ubuntu VM in Azure](#create-a-sql-server-on-ubuntu-vm-in-azure)
      - [Configure the virtual machine](#configure-the-virtual-machine)
      - [Back up the database on Windows](#back-up-the-database-on-windows)
      - [Move the backup file to Linux](#move-the-backup-file-to-linux)
      - [Restore the backup to Linux](#restore-the-backup-to-linux)
  - [Lesson 2: Database export and import](#lesson-2-database-export-and-import)
    - [Demo 1: Database export and import](#demo-1-database-export-and-import)
      - [Export a database](#export-a-database)
      - [Import a database](#import-a-database)
  - [Lesson 3: SQL Server Migration Assistant](#lesson-3-sql-server-migration-assistant)
    - [Demo 1: Using SQL Server Migration Assistant](#demo-1-using-sql-server-migration-assistant)
  - [Lesson 4: Bulk Copy Program](#lesson-4-bulk-copy-program)
    - [Demo 1: Using bcp](#demo-1-using-bcp)
      - [Export data with bcp from Linux](#export-data-with-bcp-from-linux)
      - [Export data with bcp from Windows](#export-data-with-bcp-from-windows)
      - [Import data with bcp from Linux](#import-data-with-bcp-from-linux)
      - [Import data with bcp from Windows](#import-data-with-bcp-from-windows)
  - [Lesson 5: SQL Server Integration Services](#lesson-5-sql-server-integration-services)
    - [Demo 1: Using SSIS](#demo-1-using-ssis)
      - [Examine an SSIS project definition](#examine-an-ssis-project-definition)
      - [Run an SSIS Package on Linux](#run-an-ssis-package-on-linux)
      - [Delete the virtual machine](#delete-the-virtual-machine)

## Lesson 1: Backup and restore

### Demo 1: Backup and restore

#### Create a SQL Server on Ubuntu VM in Azure

1. Ensure that the **MT17B-WS2016-NAT**, **10999A-MIA-DC** and **10999A-MIA-SQL** virtual machines are running, and then log on to **10999A-MIA-SQL** as **ADVENTUREWORKS\\Student** with the password **Pa55w.rd**.

2. Using Microsoft Internet Explorer®, type **https://portal.azure.com** to display the Azure portal. Enter your Microsoft Azure Pass credentials to log on.

3. In Microsoft Azure, in the left menu, click **+ Create a resource**, in the **Search** box, type **SQL Server 2017 on Ubuntu1604**, and then press Enter.

4. On the **SQL Server 2017 on Ubuntu1604** blade, in the **Select a software plan** list, click **Free SQL Server License: SQL Server 2017 Developer on Ubuntu 16.04**, and then click **Create**.

5. On the **Create a virtual machine** blade, enter the following information, and then click **Next : Disks \>**:

    -   **Subscription**: Your Azure subscription

    -   **Resource group (Create new)**: 10999A

    -   **Virtual machine name**: 10999A\<*YourInitials\>\<Today's Date*\>, for example **10999AAce2017100**

    -   **Region**: Select the location that is geographically nearest to you

        **Note**: If a message shows that "location is not supported" then please try with any of the below locations:

        -   East US

        -   South Central US

        -   West Europe

        -   Southeast Asia

        -   West US2

    -   **Image**: Free SQL Server License: SQL Server 2017 Developer on Ubuntu 16.04

    -   **Size**: Standard A2 v2

    -   **Authentication type**: Password

    -   **User name**: student

    -   **Password**: Pa55w.rdDemo

    -   **Confirm password**: Pa55w.rdDemo

6. On the **Disks** page, in the **OS disk type** list, click **Standard HDD**, and then click **Next : Networking \>**.

7. On the **Networking** page, next to **Public inbound ports**, click **Allow selected ports**.

8. In the **Select inbound ports** list, select the **SSH** check box, and then click **Next : Management \>**.

9. On the **Management** page, change the value of the following settings, and then click **Review + create**:

    -   **Boot diagnostics**: Off

    -   **Enable auto-shutdown**: Off

10. On the **Review + create** page, review the summary, and then click **Create**. Deployment might take a few minutes to complete.

#### Configure the virtual machine

1. In the left menu, click **Virtual machines**.

2. On the **Virtual machines** blade, click the **10999A\<*YourInitials\>\<Today's Date*\>** server.

3. On the **10999A\<*YourInitials\>\<Today's Date*\>** blade, under **Settings**, click **Networking**.

4. On the **Networking** blade, click **Add inbound port rule**.

5. In the **Add inbound security rule** blade, in the **Destination port ranges** box, type **1433**.

6. In the **Name** box, type **MS_SQL**, and then click **Add**.

7. On the **Networking** blade, click **Overview**.

8. On the **10999A\<*YourInitials\>\<Today's Date*\>** blade, next to the **Public IP** **address** value, click **Copy to clipboard**.

9. On the Desktop, double-click **putty.exe - Shortcut** (for instructions on installing PuTTY, see the *Prerequisites* section of the *Module Overview* for this module).

10. In the **Open File - Security Warning** dialog box, click **Run**.

11. In the **PuTTY Configuration** dialog box, right-click in the **Host Name (or IP address)** box, click **Paste**, and then click **Open**.

12. If a **PuTTY Security Alert** message box appears, click **Yes**.

13. In the **student@10999A\<*YourInitials\>\<Today's Date*\>:~** window, type **student**, press Enter, type **Pa55w.rdDemo**, and then press Enter. If your login attempt is successful, you will be presented with a prompt starting:
    ```
    student@10999A<YourInitials><Today's Date>:~$
    ```

14. In the PuTTY session, enable the firewall, type the following command, and then press Enter:
    ```
    sudo ufw enable
    ```

15. At the **Command may disrupt existing ssh connections** prompt, type **y**, and then press Enter.

16. In the PuTTY session, to open the port of the SQL Server service, type the following command, and then press Enter:
    ```
    sudo ufw allow 1433
    ```

17. In the PuTTY session, to open the port of the SSH service, type the following command, and then press Enter:
    ```
    sudo ufw allow 22
    ```

18. In the PuTTY session, to verify the state of the SQL Server service, type the following command, and then press Enter:
    ```
    systemctl status mssql-server
    ```

    If SQL Server is inactive, the output will include a line that begins:
    ```
    Active: inactive (dead)
    ```

    If SQL Server is active, type the following command, and then press Enter:
    ```
    sudo systemctl stop mssql-server
    ```

19. In the PuTTY session, to change the **sa** password, type the following command, and then press Enter:
    ```
    sudo /opt/mssql/bin/mssql-conf set-sa-password
    ```

20. In the PuTTY session, at the **Enter the SQL Server system administrator password** prompt, type **Pa55w.rd**, and then press Enter.

21. In the PuTTY session, at the **Confirm the SQL Server system administrator password** prompt, type **Pa55w.rd**, and then press Enter.

    **Note**: If there is an error in the output, delete the resource group, and start the demo from the beginning.

22. In the PuTTY session, to start SQL Server, type the following command, and then press Enter:
    ```
    sudo systemctl start mssql-server
    ```

23. In the PuTTY session, to enable the **mssql-server-agent** package, type the following command, and then press Enter:
    ```
    sudo /opt/mssql/bin/mssql-conf set sqlagent.enabled true
    ```

24. In the PuTTY session, to restart the SQL Server service, type the following command, and then press Enter:
    ```
    sudo systemctl restart mssql-server
    ```

#### Back up the database on Windows

1. Using File Explorer, in the **D:\\Demofiles\\Mod06** folder, right-click **Setup.cmd**, and then click **Run as administrator**.

2. In the **User Account Control** dialog box, click **Yes**, and then wait for the script to finish.

3. Start Microsoft SQL Server Management Studio 17, and connect to the **MIA-SQL** database engine using Windows authentication.

4. In Microsoft SQL Server Management Studio 17, click **New Query**.

5. On the **SQLQuery1.sql** pane, type the following command, and then click **Execute**:
    ```
    BACKUP DATABASE Sales_BR TO DISK = 'D:\Demofiles\Mod06\sales_br.bak' WITH COMPRESSION
    ```

6. In the **D:\\Demofiles\\Mod06** folder, verify that the **sales_br.bak** file exists.

#### Move the backup file to Linux

1. Start Windows PowerShell as Administrator.

2. At the PowerShell prompt, type the following command, and then press Enter:
    ```
    cd c:\putty
    ```

    The instructions in this step assume that you have downloaded **pscp.exe** as outlined in the module prerequisites topic and copied it to **C:\\putty**. If **pscp.exe** is in another location, type that path instead of **C:\\putty**.

3. In Internet Explorer, on the **10999A\<*YourInitials\>\<Today's Date*\>** blade, next to the **Public IP** **address** value, click **Copy to clipboard**.

4. At the PowerShell prompt, type the following command, replacing ***\<IP Address\>*** with the **Public IP** **address** from the previous step, and then press Enter:
    ```
    .\pscp D:\Demofiles\Mod06\sales_br.bak student@<IP Address>:/tmp/sales_br.bak
    ```

5. At the PowerShell prompt, type **Pa55w.rdDemo**, and then press Enter.

6. When the copy is complete, close the PowerShell window.

#### Restore the backup to Linux

1. In Microsoft SQL Server Management Studio 17, on the **File** menu, point to **Open**, and then click **File**.

2. In the **Open File** dialog box, in the **File name** box, type **D:\\Demofiles\\Mod06\\Demo01\\restore.sql**, and then click **Open**.

3. In Internet Explorer, on the **10999A\<*YourInitials\>\<Today's Date*\>** blade, next to the **Public IP** **address** value, click **Copy to clipboard**.

4. On the **Query** menu, point to **Connection**, and then click **Change Connection**.

5. In the **Connect to Database Engine** dialog box, right-click in the **Server name** box, and then click **Paste**.

6. In the **Authentication** box, click **SQL Server Authentication**.

7. In the **Login** box, type **sa**, then in the **Password** box, type **Pa55w.rd**, and then click **Connect**.

8. Select the command under the comment that begins **Step 1** to view the backup header metadata, and then click **Execute**.

9. Select the command under the comment that begins **Step 2** to view the files in the backup, and then click **Execute**.

10. Select the command under the comment that begins **Step 3** to restore the backup, and then click **Execute**.

11. Select the command under the comment that begins **Step 4** to verify that the **Sales_BR** database appears in **sys.databases**, and then click **Execute**.

12. Leave Internet Explorer, Microsoft SQL Server Management Studio 17, and PuTTY open for the next demonstration.

---

## Lesson 2: Database export and import

### Demo 1: Database export and import

#### Export a database

1. In Microsoft SQL Server Management Studio 17, in Object Explorer, expand **Databases**, right-click **Sales_IE**, point to **Tasks**, and then click **Export Data-tier Application**.

2. In the **Export Data-tier Application 'Sales_IE'** dialog box, on the **Introduction** page, click **Next**.

3. On the **Export Settings** page, on the **Settings** tab, in the **Save to local disk** box, type **D:\\Demofiles\\Mod06\\sales_ie.bacpac**.

4. On the **Advanced** tab, in the **Select tables for data export** box, expand the tree to demonstrate that you can exclude some tables from the export, and then click **Next**.

5. On the **Summary** page, review the settings, and then click **Finish**.

6. On the **Results** page, when the export completes, click **Close**.

7. In File Explorer, go to **D:\\Demofiles\\Mod06**, and verify that the **sales_ie.bacpac** file exists.

#### Import a database

1. In Internet Explorer, on the **10999A\<*YourInitials\>\<Today's Date*\>** blade, next to the **Public IP** **address** value, click **Copy to clipboard**.

2. In Microsoft SQL Server Management Studio 17, in Object Explorer, click **Connect**, and then click **Database Engine**.

3. In the **Connect to Server** dialog box, right-click in the **Server name** box, and then click **Paste**.

4. In the **Authentication** box, click **SQL Server Authentication**.

5. In the **Login** box, type **sa**, then in the **Password** box, type **Pa55w.rd**, and then click **Connect**.

6. Under the node for your Azure VM, right-click **Databases**, and then click **Import Data-tier Application**.

7. In the **Import Data-tier Application** dialog box, on the **Introduction** page, click **Next**.

8. On the **Import Settings** page, in the **Import from local disk** page, type **D:\\Demofiles\\Mod06\\sales_ie.bacpac**, and then click **Next**.

9. On the **Database Settings** page, examine the settings, and then click **Next**.

10. On the **Summary** page, review the settings, and then click **Finish**.

11. On the **Results** page, when the import completes, click **Close**.

12. In Object Explorer, under the node for your Azure VM, expand **Databases**, and verify that the **sales_ie** database appears.

13. Leave Internet Explorer, Microsoft SQL Server Management Studio 17, and PuTTY open for the next demonstration.

---

## Lesson 3: SQL Server Migration Assistant

### Demo 1: Using SQL Server Migration Assistant

1. In Internet Explorer, to download SSMA for Access, go to **http://aka.ms/ssmaforaccess**, and then click **Download**.

2. On the **Choose the download you want** page, select the **SSMAforAccess_8.2.0.msi** check box, and then click **Next**.

3. In the Internet Explorer message box, click **Allow once**.

4. In the Internet Explorer message box, click **Run**.

5. In the **SSMA for Access Setup** dialog box, click **Next**.

6. On the **Required Component(s) Missing** page, click **Download**.

7. In Internet Explorer, click **Download**, select the **accessruntime_4288-1001_x64_en-us.exe** check box, and then click **Next**.

8. In the Internet Explorer message box, click **Allow once**.

9. In the Internet Explorer message box, click **Run**, and then wait for the download to complete.

10. In the **User Account Control** dialog box, click **Yes**.

11. In the **Microsoft Access Runtime 2016** dialog box, select the **I accept the terms of this agreement** check box, and then click **Continue**.

12. When the installation is complete, click **Close**.

13. Close Internet Explorer.

14. In the **SSMA for Access Setup** dialog box, on the **Required Component(s) Missing** page, click **Next**.

15. On the **End-User License Agreement** page, click **I accept the agreement**, and then click **Next**.

16. On the **Choose Setup Type** page, click **Complete**.

17. On the **Ready to Install** page, clear all the check boxes, and then click **Install**.

18. In the **User Account Control** dialog box, click **Yes**.

19. On the **SSMA for Access Installation Complete** page, click **Finish**.

20. On the desktop, double-click **Microsoft SQL Server Migration Assistant for Access** to start SSMA.

21. In the **Migration Wizard** dialog box, click **Close**.

22. On the **File** menu, click **New Project**.

23. In the **New Project** dialog box, in the **Name** box, type **SSMA_Demo**.

24. In the **Location** box, type **D:\\Demofiles\\Mod06**, then in the **Migrate To** list, click **SQL Server 2017**, and then click **OK**.

25. On the **File** menu, click **Add Databases**.

26. In the **Open** dialog box, in the **File name** box, type **D:\\Demofiles\\Mod06\\Demo03\\Northwind.accdb**, and then click **Open**.

27. On the **Access Metadata Explorer** pane, expand **Access-metadata**, expand **Databases**, expand **Northwind**, and then select **Tables** to migrate all the tables in the database to SQL Server.

28. In Internet Explorer, on the **10999A\<*YourInitials\>\<Today's Date*\>** blade, next to the **Public IP** **address** value, click **Copy to clipboard**.

29. In SSMA for Access, click **Connect to SQL Server**.

30. In the **Connect to SQL Server** dialog box, right-click in the **Server name** box, and then click **Paste**.

31. In the **Database** box, type **Northwind**.

32. In the **User name** box, type **sa**, and in the **Password** box, type **Pa55w.rd**, and then click **Connect**.

33. In the **Error** dialog box, click **Yes** to create the database.

34. On the **Access Metadata Explorer** pane, right-click on **Tables**, and then click **Create Report**.

35. When the report is generated, in the **How do you want to open this file?** dialog box, ensure **Internet Explorer** is selected, and then click **OK**.

36. In the Internet Explorer message box, click **Allow blocked content** to display the full report.

    Observe that the report gives a summary of errors and an estimated time to manually correct the errors.

37. On the left pane, expand **Tables**, and then click **Customers** to demonstrate the identified errors.

38. Close the Internet Explorer window displaying the report.

39. In SSMA for Access, to generate the SQL Server schema, click **Convert Schema**.

40. On the **Access Metadata Explorer** pane, expand **Tables**, and then click **Customers**.

    Observe that the view on the SQL Server Metadata Explorer pane switches to the **Customers** table. On the upper right pane, in the **Table** tab, observe that you see the schema of the source table.

41. On the **Type Mapping** tab, observe that you see how the source system data types that have no direct equivalent in SQL Server are mapped to a SQL Server data type. Rows in yellow are project defaults; these can be overridden at any level in the source database tree.

42. On the **Properties** tab, observe the table properties.

43. On the **Storage Options** tab, observe that you can change the table type that will be created in SQL Server.

44. On the **Data** tab, observe that you can view the table data.

45. On the lower right pane, on the **Table** tab, observe that you see the schema of the table created in the target SQL Server.

46. On the **SQL** tab, observe that you can review the Transact-SQL commands that were used to create the table in SQL Server; scroll down in the tab and observe that SSMA errors, warnings, and information are included as comments.

47. In Microsoft SQL Server Management Studio 17, in Object Explorer, under the node for your Azure virtual machine, right-click **Databases**, and then click **Refresh**.

48. Expand **Northwind**, and then expand **Tables**. Observe that the tables have not yet been created in SQL Server.

49. In SSMA for Access, on the **SQL Server Metadata Explorer** pane, right-click **Tables**. Observe that options exist to generate a Script or an SSDT project file, and then click **Synchronize with Database**.

50. In the **Synchronize with the Database** dialog box, to view the list of tables to be migrated, expand **Tables**, and then click **OK**.

51. In Microsoft SQL Server Management Studio 17, in Object Explorer, under the node for your Azure virtual machine, right-click **Tables**, and then click **Refresh**. Observe that the tables have been created in SQL Server.

52. Right-click **dbo.Customers**, and then click **Select Top 1000 Rows**. Observe that the table is empty; no data has been migrated.

53. In SSMA for Access, on the **Access Metadata Explorer** pane, right-click **Customers**, and then click **Migrate Data**.

54. In the **Data Migration Report** dialog box, click **Close**.

55. On the **SQL Server Metadata Explorer** pane, click **Customers**, then on the lower right pane, on the **Data** tab, observe that you can view the data as it appears in SQL Server.

56. On the **SQL Server Metadata Explorer** pane, click **Employees** to observe that only the data for the **Customers** table has been migrated.

57. In Microsoft SQL Server Management Studio 17, in Object Explorer, right-click **dbo.Customers**, and then click **Select Top 1000 Rows**. Observe that the table now contains data.

58. In SSMA for Access, to migrate the remaining data, on the **Access Metadata Explorer** pane, right-click **Tables**, and then click **Migrate Data**.

59. In the **Operation Prerequisites Not Met** dialog box, read the warning message, and then click **Continue**.

60. In the **Data Migration Report** dialog box, click **Close**.

61. On the **SQL Server Metadata Explorer** pane, click **Employees**, then on the lower right pane, on the **Data** tab, observe that the **Employees** table now contains data.

62. Close SSMA without saving any changes.

63. Leave Microsoft SQL Server Management Studio 17, and Internet Explorer open for the next demonstration.

---

## Lesson 4: Bulk Copy Program

### Demo 1: Using bcp

#### Export data with bcp from Linux

1. In Microsoft SQL Server Management Studio 17, on the **File** menu, point to **Open**, and then click **File**.

2. In the **Open File** dialog box, in the **File name** box, type **D:\\Demofiles\\Mod06\\Demo04\\Sales\_BCP.sql**, and then click **Open**.

3. In Internet Explorer, on the **10999A\<*YourInitials\>\<Today's Date*\>** blade, next to the **Public IP** **address** value, click **Copy to clipboard**.

4. In Microsoft SQL Server Management Studio 17, on the **Query** menu, point to **Connection**, and then click **Change Connection**.

5. In the **Connect to Database Engine** dialog box, right-click in the **Server name** box, and then click **Paste**.

6. In the **Authentication** box, click **SQL Server Authentication**.

7. In the **Login** box, type **sa**, then in the **Password** box, type **Pa55w.rd**, and then click **Connect**.

8. On the **Sales_BCP.sql** tab, click **Execute** to set up the **Sales_BCP** database.

9. In the PuTTY session, to export data from the **Sales_BCP.dbo.Customers** table, type the following command, and then press Enter:
    ```
    /opt/mssql-tools/bin/bcp dbo.Customer out /tmp/Customer.dat -S localhost -U sa -P Pa55w.rd -c -d Sales_BCP
    ```

10. In the PuTTY session, when the export has completed, type **cat /tmp/Customer.dat**, and then press Enter to view the contents of the file.

#### Export data with bcp from Windows

1. In Internet Explorer, on the **10999A\<*YourInitials\>\<Today's Date*\>** blade, next to the **Public IP** **address** value, click **Copy to clipboard**.

2. Start Windows PowerShell as Administrator.

3. At the PowerShell prompt, type the following command, replacing ***\<IP Address\>*** with the Public IP address, and then press Enter:
    ```
    $server="<IP Address>"
    ```

4. At the PowerShell prompt, to export data from the **Sales_BCP.dbo.Customers** table, type the following command, and then press Enter:
    ```
    bcp dbo.Customer out D:\Demofiles\Mod06\Customer.dat -S $server -U sa -P Pa55w.rd -c -d Sales_BCP
    ```

5. At the PowerShell prompt, when the export has completed, type **Get-Content D:\\Demofiles\\Mod06\\Customer.dat**, and then press Enter to view the contents of the file.

#### Import data with bcp from Linux

1. At the PowerShell prompt, type the following command, and then press Enter:
    ```
    cd c:\putty
    ```

    The instructions in this step assume that you have downloaded **pscp.exe** as outlined in the module prerequisites topic and copied it to **C:\\putty**. If **pscp.exe** is in another location, type that path instead of **c:\\putty**.

2. At the PowerShell prompt, to copy the import file to the VM, type the following command, and then press Enter:
    ```
    .\pscp D:\Demofiles\Mod06\Demo04\Import\forex.txt ("student@" + $server + ":/tmp/forex.txt")
    ```

3. At the PowerShell prompt, when you are prompted for a password, type **Pa55w.rdDemo**, and then press Enter.

4. In the PuTTY session, type the following command, and then press Enter:
    ```
    /opt/mssql-tools/bin/bcp dbo.ExchangeRate in /tmp/forex.txt -S localhost -U sa -P Pa55w.rd -c -t"," -r"\r\n" -d Sales_BCP
    ```

5. In Microsoft SQL Server Management Studio 17, click **New Query**.

6. On the new query tab, type the following command, and then click **Execute**:
    ```
    SELECT COUNT(*) FROM Sales_BCP.dbo.ExchangeRate;
    ```

    The query should return the result **47425**.

#### Import data with bcp from Windows

1. At the PowerShell prompt, to load another copy of the **forex.txt** file into **Sales_BCP.dbo.ExchangeRate**, type the following command, and then press Enter:
    ```
    bcp dbo.ExchangeRate in D:\Demofiles\Mod06\Demo04\Import\forex.txt -S $server -U sa -P Pa55w.rd -c -t"," -d Sales_BCP
    ```

2. In Microsoft SQL Server Management Studio 17, click **Execute** to return the count of rows in the **ExchangeRate** table. The query should return the result **94850**.

3. Leave PowerShell, Internet Explorer, Microsoft SQL Server Management Studio 17, and PuTTY open for the next demonstration.

---

## Lesson 5: SQL Server Integration Services

### Demo 1: Using SSIS

#### Examine an SSIS project definition

1. Start Microsoft Visual Studio 2015.

2. In Microsoft Visual Studio, on the **File** menu, point to **Open**, and then click **Project/Solution**.

3. In the **Open Project** dialog box, go to **D:\\Demofiles\\Mod06\\Demo05\\SSIS**, and then double-click **SSIS.sln**.

4. In the **Project Password** dialog box, type **Pa55w.rd**, and then click **OK**.

5. In Solution Explorer, observe that the solution contains one project that in turn contains one package, and then double-click **export_product.dtsx**.

6. On the **export_product.dtsx [Design]** pane, on the **Control Flow** tab, observe that the package contains a single data flow task named **Export Product data**.

7. To move to the **Data Flow** tab, double-click **Export Product data**.

8. On the **Data Flow** tab, observe that the data flow has a single source-**ADO Net Source**-connected to a single destination-**Flat File Destination**. The sources are configured by the connection managers listed on the **Connection Managers** pane.

9. On the **SSIS** menu, click **Variables**.

10. On the **Variables** pane, observe that the package has two variables-**OutputFile** and **ServerName**.

11. On the **Build** menu, click **Build Solution**.

12. Close Microsoft Visual Studio without saving any changes.

13. In File Explorer, go to **D:\\Demofiles\\Mod06\\Demo05\\SSIS\\SSIS**, and observe the **export_product.dtsx** package file has a size of **36 KB**.

14. Go to **D:\\Demofiles\\Mod06\\Demo05\\SSIS\\SSIS\\bin\\Development**, and observe that **SSIS.ispac** has a size of **8 KB**.

#### Run an SSIS Package on Linux

1. In Microsoft SQL Server Management Studio 17, on the **File** menu, point to **Open**, and then click **File**.

2. In the **Open File** dialog box, in the **File name** box, type **D:\\Demofiles\\Mod06\\Demo05\\Sales_SSIS.sql**, and then click **Open**.

3. In Internet Explorer, on the **10999A\<*YourInitials\>\<Today's Date*\>** blade, next to the **Public IP** **address** value, click **Copy to clipboard**.

4. In Microsoft SQL Server Management Studio 17, on the **Query** menu, point to **Connection**, and then click **Change Connection**.

5. In the **Connect to Database Engine** dialog box, right-click in the **Server name** box, and then click **Paste**.

6. In the **Authentication** box, click **SQL Server Authentication**.

7. In the **Login** box, type **sa**, then in the **Password** box, type **Pa55w.rd**, and then click **Connect**.

8. On the **Sales_SSIS.sql** tab, click **Execute** to set up the **Sales_SSIS** database.

9. Close Microsoft SQL Server Management Studio 17 without saving any changes.

10. In Windows PowerShell, at the PowerShell prompt, type the following command, and then press Enter:
    ```
    cd c:\putty
    ```

    The instructions in this step assume that you have downloaded **pscp.exe** as outlined in the module prerequisites topic and copied it to **C:\\putty**. If **pscp.exe** is in another location, type that path instead of **c:\\putty**.

11. At the PowerShell prompt, to copy the package file to the VM, type the following command, and then press Enter:
    ```
    .\pscp D:\Demofiles\Mod06\Demo05\SSIS\SSIS\export_product.dtsx ("student@" + $server + ":/tmp/export_product.dtsx")
    ```

12. At the PowerShell prompt, when you are prompted for a password, type **Pa55w.rdDemo**, and then press Enter.

13. At the PowerShell prompt, to copy the Project file to the VM, type the following command, and then press Enter:
    ```
    .\pscp D:\Demofiles\Mod06\Demo05\SSIS\SSIS\bin\Development\SSIS.ispac ("student@" + $server + ":/tmp/SSIS.ispac")
    ```

14. At the PowerShell prompt, when you are prompted for a password, type **Pa55w.rdDemo**, and then press Enter.

15. Close Windows PowerShell.

16. In PuTTY, at the PuTTY prompt, to configure SSIS, type the following command, and then press Enter:
    ```
    sudo /opt/ssis/bin/ssis-conf setup
    ```

17. At the PuTTY prompt, type **2**, and then press Enter.

18. At the PuTTY prompt, type **yes**, and then press Enter.

19. At the PuTTY prompt, to run the package, type the following command, and then press Enter:
    ```
    /opt/ssis/bin/dtexec /F /tmp/export_product.dtsx /Set '\Package.Variables[OutputFile];c:\tmp\out.package.txt' /Set '\Package.Variables[ServerName];localhost' /DE 'Pa55w.rd'
    ```

    Observe that the output file parameter is specified as a Windows-style path.

20. At the PuTTY prompt, to view the output file, type **cat /tmp/out.package.txt**, and then press Enter.

21. At the PuTTY prompt, to run the package from the project, type the following command, and then press Enter:
    ```
    /opt/ssis/bin/dtexec /Project 'c:\tmp\SSIS.ispac' /Package export_product.dtsx /Set '\Package.Variables[OutputFile];c:\tmp\out.project.txt' /Set '\Package.Variables[ServerName];localhost' /DE 'Pa55w.rd'
    ```

    Observe that the project path is specified as a Windows-style path (in single quotes to comply with bash escaping rules).

22. At the PuTTY prompt, to view the output file, type **cat /tmp/out.project.txt**, and then press Enter.

23. At the PuTTY prompt, type **exit**, and then press Enter.

24. Close any open Windows Explorer windows.

#### Delete the virtual machine

1. In Internet Explorer, on the left blade of the Azure portal, click **Resource groups**.

2. On the **Resource groups** blade, click **10999A**.

3. On the **10999A** blade, click **Delete resource group**.

4. On the **Are you sure you want to delete"10999A"?** blade, type **10999A**, and then click **Delete**. This deletes the virtual machines.

5. If there is a **NetworkWatcherRG** resource group present, repeat steps 2 to 4 to delete.

6. Close all open programs and windows.
