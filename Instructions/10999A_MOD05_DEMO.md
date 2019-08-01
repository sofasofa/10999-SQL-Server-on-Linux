# Module 5: Managing Database Solutions on SQL Server for Linux
- [Module 5: Managing Database Solutions on SQL Server for Linux](#module-5-managing-database-solutions-on-sql-server-for-linux)
  - [Lesson 1: Connect to SQL Server on Linux](#lesson-1-connect-to-sql-server-on-linux)
    - [Demo 1: Connecting to SQL Server on Linux](#demo-1-connecting-to-sql-server-on-linux)
      - [Create a SQL Server on Ubuntu VM in Azure](#create-a-sql-server-on-ubuntu-vm-in-azure)
      - [Configure the virtual machine](#configure-the-virtual-machine)
      - [Connect with Microsoft SQL Server Management Studio 17](#connect-with-microsoft-sql-server-management-studio-17)
      - [Connect with Microsoft Visual Studio](#connect-with-microsoft-visual-studio)
      - [Connect from Visual Studio Code](#connect-from-visual-studio-code)
  - [Lesson 2: Managing SQL Server on Linux](#lesson-2-managing-sql-server-on-linux)
    - [Demo 1: Managing SQL Server on Linux](#demo-1-managing-sql-server-on-linux)
      - [Manage SQL Server with SSMS](#manage-sql-server-with-ssms)
      - [Manage SQL Server with PowerShell](#manage-sql-server-with-powershell)
      - [Configure Database Mail and SQL Server Agent alerts](#configure-database-mail-and-sql-server-agent-alerts)
      - [Delete the virtual machine](#delete-the-virtual-machine)

## Lesson 1: Connect to SQL Server on Linux

### Demo 1: Connecting to SQL Server on Linux

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

13. In the **student\@10999A\<*YourInitials\>\<Today's Date*\>:~** window, type **student**, press Enter, type **Pa55w.rdDemo**, and then press Enter. If your login attempt is successful, you will be presented with a prompt starting:
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

17. In the PuTTY session, to verify the state of the SQL Server service, type the following command, and then press Enter:
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

18. In the PuTTY session, to change the **sa** password, type the following command, and then press Enter:
    ```
    sudo /opt/mssql/bin/mssql-conf set-sa-password
    ```

19. In the PuTTY session, at the **Enter the SQL Server system administrator password** prompt, type **Pa55w.rd**, and then press Enter.

20. In the PuTTY session, at the **Confirm the SQL Server system administrator password** prompt, type **Pa55w.rd**, and then press Enter.

    **Note**: If there is an error in the output, delete the resource group, and start the demo from the beginning.

21. In the PuTTY session, to start SQL Server, type the following command, and then press Enter:
    ```
    sudo systemctl start mssql-server
    ```

22. In the PuTTY session, to enable the **mssql-server-agent** package, type the following command, and then press Enter:
    ```
    sudo /opt/mssql/bin/mssql-conf set sqlagent.enabled true
    ```

23. In the PuTTY session, to restart the SQL Server service, type the following command, and then press Enter:
    ```
    sudo systemctl restart mssql-server
    ```

#### Connect with Microsoft SQL Server Management Studio 17

1. In Internet Explorer, on the **10999A\<*YourInitials\>\<Today's Date*\>** blade, next to the **Public IP** **address** value, click **Copy to clipboard**.

2. On the Taskbar, click **Microsoft SQL Server Management Studio 17**.

3. In the **Connect to Server** dialog box, right-click in the **Server name** box, and then click **Paste**.

4. In the **Authentication** list, click **SQL Server Authentication**.

5. In the **Login** box, type **sa**, then in the **Password** box, type **Pa55w.rd**, and then click **Connect**.

6. In Object Explorer, right-click the server IP address, and then click **New Query**.

7. In the **SQLQuery1.sql** pane, type the following command, and then click **Execute**:
    ```
    SELECT @@SERVERNAME as servername, @@VERSION as version;
    ```

    Review the results.

8. Leave SSMS open for the next demonstration.

#### Connect with Microsoft Visual Studio

1. On the Taskbar, click **Microsoft Visual Studio 2015**.

2. If the **Update Available** dialog box appears at any point, click **Not now**.

3. On the **File** menu, point to **New**, and then click **File**.

4. In the **New File** dialog box, click **Sql File**, and then click **Open**.

5. In the **SqlQuery_1.sql** pane, type the following command, and then click **Execute**:
    ```
    SELECT @@SERVERNAME as servername, @@VERSION as version;
    ```

6. In Internet Explorer, on the **10999A\<*YourInitials\>\<Today's Date*\>** blade, next to the **Public IP** **address** value, click **Copy to clipboard**.

7. In Microsoft Visual Studio, in the **Connect** dialog box, right-click in the **Server Name** box, and then click **Paste**.

8. In the **Authentication** list, click **SQL Server Authentication**.

9. In the **Login** box, type **sa**, in the **Password** box, type **Pa55w.rd**, and then click **Connect**.

10. In Server Explorer, right-click **Data Connections**, and then click **Add Connection**.

11. In the **Choose Data Source** dialog box, in the **Data source** box, click **Microsoft SQL Server**.

12. Ensure the **Data provider** list has the value **.NET Framework Data Provider for SQL Server**, and then click **Continue**.

13. In the **Add Connection** dialog box, right-click in the **Server name** box, then click **Paste**.

14. In the **Authentication** list, click **SQL Server Authentication**.

15. In the **Login** box, type **sa**, then in the **Password** box, type **Pa55w.rd**.

16. In the **Select or enter a database name** list, click **master**, and then click **OK**.

17. In Server Explorer, expand the **\<server name\>.master.dbo** node to show the object types.

18. Right-click the **\<server name\>.master.dbo** node, and then click **New Query**. Observe that a new connection has been opened to the database in a new query window.

19. Close Microsoft Visual Studio without saving any changes.

#### Connect from Visual Studio Code

1. In Internet Explorer, open a new tab, go to **https://code.visualstudio.com/download**, and then click the link to download the **Windows** release.

2. In the message box, click **Run**.

3. In the **Setup - Visual Studio Code** dialog box, click **Next**.

4. On the **License Agreement** page, click **I accept the agreement**, and then click **Next**.

5. On the **Select Destination Location** page, click **Next**.

6. On the **Select Start Menu Folder** page, click **Next**.

7. On the **Select Additional Tasks** page, click **Next**.

8. On the **Ready to Install** page, click **Install**.

9. On the **Completing the Visual Studio Code Setup Wizard** page, click **Finish**. Visual Studio Code should start.

10. In Visual Studio Code, on the **View** menu, click **Extensions**.

11. In the **Search** box, type **mssql**.

12. In the results list, click **SQL Server (mssql)**, and then click **Install**.

13. On the **File** menu, click **New File**.

14. In the bottom right of the VS Code window, click **Plain Text**.

15. In the **Select Language Mode** box, type **sql**, and then press Enter.

16. In Internet Explorer, on the **10999A\<*YourInitials\>\<Today's Date*\>** blade, next to the **Public IP** **address** value, click **Copy to clipboard**.

17. On the **View** menu, click **Command Palette**, and then type **ms sql**.

18. In the command list, click **MS SQL: Connect**, and then click **Create connection profile**.

19. Press Ctrl+V, and then press Enter.

20. At the **\[Optional\] Database to connect** prompt, press Enter.

21. Click **SQL Login**, type **sa**, and then press Enter.

22. Type **Pa55w.rd**, and then press Enter.

23. At the **Save Password?** prompt, click **Yes**.

24. At the **\[Optional\] Enter a name from this profile** prompt, press Enter.

25. In the **Untitled-1** tab, type the following:
    ```
    SELECT @@SERVERNAME as servername, @@VERSION as version;
    ```

26. On the **View** menu, click **Command Palette**, and then type **ms sql**.

27. In the command list, click **MS SQL: Execute Query**. Examine the results.

28. Close Visual Studio Code without saving any changes.

29. Leave Internet Explorer, and SSMS open for the next demonstration.

---

## Lesson 2: Managing SQL Server on Linux

### Demo 1: Managing SQL Server on Linux

#### Manage SQL Server with SSMS

1. In Microsoft SQL Server Management Studio 17, in Object Explorer, under the node for your server, expand **Management**, expand **SQL Server Logs**, and then double-click the node with the name that begins **Current**.

2. In the **Log File Viewer**, examine the logs, and then click **Close**.

3. In Object Explorer, under the node for your server, expand **Databases**, right-click **Databases**, and then click **New Database**.

4. In the **New Database** dialog box, on the **General** page, in the **Database name** box, type **ssms_db**.

5. On the **Options** page, demonstrate all the options available, click **Script**, and then click **OK**.

6. In Object Explorer, under **Databases**, observe that the **ssms_db** node appears. Examine the database creation script generated in the query pane, and then close the script.

7. In Object Explorer, expand **ssms_db** to show its child nodes, and then collapse **ssms_db**.

8. Right-click **ssms_db**, and then click **Delete**.

9. In the **Delete Object** dialog box, click **OK**.

10. Right-click the top-level server name node, then click **Activity Monitor**.

11. In the **Activity Monitor** pane, click **Processes**, and then click **Resource Waits**.

12. Close the Activity Monitor.

#### Manage SQL Server with PowerShell

> **Note**: You will find the scripts for this demo can be found on **10999A-MIA-SQL** at **D:\\Demofiles\\Mod05\\Demo02\\powershell_demo.txt**.

1. Start PowerShell as an administrator.

2. At the PowerShell prompt, to install the SqlServer module, type the following command, and then press Enter:
    ```
    Install-Module -Name SqlServer -AllowClobber
    ```

3. At the PowerShell prompt, type **y**, and then press Enter.

4. At the PowerShell prompt, type **a**, and then press Enter.

5. At the PowerShell prompt, type the following command, and then press Enter:
    ```
    Import-Module -name SqlServer
    ```
6. In Internet Explorer, on the **10999A\<*YourInitials\>\<Today's Date*\>** blade, next to the **Public IP** **address** value, click **Copy to clipboard**.

7. At the PowerShell prompt, type the following command, replacing ***\<IP Address\>*** with the **Public IP** **address** from the previous step, and then press Enter:
    ```
    $serverInstance = "<IP Address>"
    ```

8. At the PowerShell prompt, type the following command, and then press Enter:
    ```
    $credential = Get-Credential
    ```

9. In the **Windows PowerShell credential request** dialog box, in the **User name** box, type **sa**. In the **Password** box, type **Pa55w.rd**, and then click **OK**.

10. At the PowerShell prompt, to view the log, type the following command, and then press Enter:
    ```
    Get-SqlErrorLog -ServerInstance $serverInstance -Credential $credential -Before (Get-Date).ToUniversalTime() | Out-GridView
    ```

11. Examine the log, and then close the window.

12. At the PowerShell prompt, to create a database, type the following command, and then press Enter:
    ```
    [System.Reflection.Assembly]::LoadWithPartialName('Microsoft.SqlServer.Smo') | out-null
    ```

13. At the PowerShell prompt, type the following command, and then press Enter:
    ```
    $server = New-Object ('Microsoft.SqlServer.Management.Smo.Server') $serverInstance
    ```

14. At the PowerShell prompt, type the following command, and then press Enter:
    ```
    $server.ConnectionContext.LoginSecure=$false
    ```

15. At the PowerShell prompt, type the following command, and then press Enter:
    ```
    $server.ConnectionContext.set_Login($credential.UserName)
    ```

16. At the PowerShell prompt, type the following command, and then press Enter:
    ```
    $server.ConnectionContext.set_SecurePassword($credential.Password)
    ```

17. At the PowerShell prompt, type the following command, and then press Enter:
    ```
    $db = New-Object Microsoft.SqlServer.Management.Smo.Database($server, "ps_db")
    ```

18. At the PowerShell prompt, type the following command, and then press Enter:
    ```
    $db.Create()
    ```

19. At the PowerShell prompt, to enumerate the databases on the instance, type the following command, and then press Enter:
    ```
    $server.Databases | Foreach-Object {$_.name}
    ```

    Observe that **ps_db** appears in the list.

20. At the PowerShell prompt, to delete the database, type the following command, and then press Enter:
    ```
    $db.Drop()
    ```

21. At the PowerShell prompt, to show that the database was removed, type the following command, and then press Enter:
    ```
    $server.Databases | Foreach-Object {$_.name}
    ```

    Observe that **ps_db** is not in the list.

22. At the PowerShell prompt, type **exit**, and then press Enter.

#### Configure Database Mail and SQL Server Agent alerts

1. In Microsoft SQL Server Management Studio 17, on the **File** menu, point to **Open**, and then click **File**.

2. In the **Open File** dialog box, in the **File name** box, type **D:\\Demofiles\\Mod05\\Demo02\\dbmail.sql**, and then click **Open**.

3. To enable **Database Mail**, select the code under the comment that begins **Step 1**, and then click **Execute**.

4. To create an email account, select the code under the comment that begins **Step 2**, and then click **Execute**. Notice that this demo uses a local SMTP server at port **8000**.

5. To create a default profile, select the code under the comment that begins **Step 3**, and then click **Execute**.

6. To grant permissions to the profile, select the code under the comment that begins **Step 4**, and then click **Execute**.

7. To connect the account to the profile, select the code under the comment that begins **Step 5**, and then click **Execute**.

8. In PuTTY, in the PuTTY session, type **python3**, and then press Enter.

9. In the PuTTY session, type the following command, and then press Enter:
    ```
    import smtpd, asyncore
    ```

10. In the PuTTY session, type the following command, and then press Enter:
    ```
    server = smtpd.DebuggingServer(('localhost', 8000), None)
    ```

11. In the PuTTY session, type the following command, and then press Enter:
    ```
    asyncore.loop()
    ```

12. In SSMS, to send a test email, select the code under the comment that begins **Step 6b**, and then click **Execute**.

13. In the PuTTY session, after a few seconds, the mail message should be displayed. The message body will not be directly legible, because it is encoded in base64.

14. In the PuTTY session, press Ctrl+Z.

15. In the PuTTY session, to configure the default mail profile, type the following command, and then press Enter:
    ```
    sudo /opt/mssql/bin/mssql-conf set sqlagent.databasemailprofile dbmail_default
    ```

16. In the PuTTY session, type the following command, and then press Enter:
    ```
    sudo systemctl restart mssql-server.service
    ```

17. In the PuTTY session, type **fg %1**, and then press Enter to restart the dummy SMTP server.

18. In SSMS, to create an operator to receive alerts, select the code under the comment that begins **Step 8**, and then click **Execute**.

19. To create a simple job, select the code under the comment that begins **Step 9**, and then click **Execute**.

20. To create a notification when the job completes, select the code under the comment that begins **Step 10**, and then click **Execute**.

21. To run the job, select the code under the comment that begins **Step 11**, and then click **Execute**.

22. In the PuTTY session, after a few seconds, a mail message should be displayed with a subject that begins **[The job succeeded.]**. The message body will not be directly legible, because it is encoded in base64.

23. In the PuTTY session, press Ctrl+Z.

24. In the PuTTY session, type **kill %1**, and then press Enter.

25. In the PuTTY session, type **exit**, and then press Enter.

26. Close Microsoft SQL Server Management Studio 17 without saving any changes.

#### Delete the virtual machine

1. In Internet Explorer, on the left blade of the Azure portal, click **Resource groups**.

2. On the **Resource groups** blade, click **10999A**.

3. On the **10999A** blade, click **Delete resource group**.

4. On the **Are you sure you want to delete"10999A"?** blade, type **10999A**, and then click **Delete**. This deletes the virtual machines.

5. If there is a **NetworkWatcherRG** resource group present, repeat steps 2 to 4 to delete.

6. Close all open programs and windows.
