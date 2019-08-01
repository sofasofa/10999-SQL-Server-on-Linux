# Module 7: High Availability
- [Module 7: High Availability](#module-7-high-availability)
  - [Lesson 1: Log shipping](#lesson-1-log-shipping)
    - [Demo 1: Log shipping with SQL Server on Linux](#demo-1-log-shipping-with-sql-server-on-linux)
      - [Create two SQL Server on Ubuntu VMs in Azure](#create-two-sql-server-on-ubuntu-vms-in-azure)
      - [Connect to the primary Linux VM](#connect-to-the-primary-linux-vm)
      - [Configure the primary network share](#configure-the-primary-network-share)
      - [Connect to the secondary Linux VM](#connect-to-the-secondary-linux-vm)
      - [Configure the secondary network share](#configure-the-secondary-network-share)
      - [Configure the primary database](#configure-the-primary-database)
      - [Configure the secondary database](#configure-the-secondary-database)
      - [Verify that log shipping is functioning](#verify-that-log-shipping-is-functioning)
  - [Lesson 3: Availability groups](#lesson-3-availability-groups)
    - [Demo 1: Create an Always On availability group](#demo-1-create-an-always-on-availability-group)
      - [Connect to the Primary Linux VM](#connect-to-the-primary-linux-vm)
      - [Configure the primary server](#configure-the-primary-server)
      - [Configure the secondary server](#configure-the-secondary-server)
      - [Configure the availability group](#configure-the-availability-group)
      - [Install and configure Pacemaker](#install-and-configure-pacemaker)
      - [Delete the Azure virtual machine](#delete-the-azure-virtual-machine)

## Lesson 1: Log shipping

### Demo 1: Log shipping with SQL Server on Linux

#### Create two SQL Server on Ubuntu VMs in Azure

1. Ensure that the **MT17B-WS2016-NAT**, **10999A-MIA-DC** and **10999A-MIA-SQL** virtual machines are running, and then log on to **10999A-MIA-SQL** as **ADVENTUREWORKS\\Student** with the password **Pa55w.rd**.

2. Using Microsoft Internet Explorer®, type **https://portal.azure.com** to display the Azure portal. Enter your Microsoft Azure Pass credentials to log on.

3. In Microsoft Azure, in the left menu, click **+ Create a resource**, in the **Search** box, type **SQL Server 2017 on Ubuntu1604**, and then press Enter.

4. On the **SQL Server 2017 on Ubuntu1604** blade, in the **Select a software plan** list, click **Free SQL Server License: SQL Server 2017 Developer on Ubuntu 16.04**, and then click **Create**.

5. On the **Create a virtual machine** blade, enter the following information, and then click **Next : Disks \>**:

    -   **Subscription**: Your Azure subscription

    -   **Resource group (Create new)**: 10999A

    -   **Virtual machine name**: P10999\<*YourInitials\>\<Today's Date*\>, for example **P10999Ace2017100**

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

11. In the left menu, click **Virtual machines**.

12. On the **Virtual machines** blade, click the **P10999\<*YourInitials\>\<Today's Date*\>** server.

13. On the **P10999\<*YourInitials\>\<Today's Date*\>** blade, under **Settings**, click **Networking**.

14. On the **Networking** blade, click **Add inbound port rule**.

15. In the **Add inbound security rule** blade, in the **Destination port ranges** box, type **1433**.

16. In the **Name** box, type **MS_SQL**, and then click **Add**.

17. Repeat steps 3 to 16 to create a second virtual machine with the following changes:

    -   **Virtual machine name**: S10999\<*YourInitials\>\<Today's Date*\>, for example **S10999Ace2017100**

    -   In step 7, make sure the **Virtual network** and **Subnet** values are the same value as you selected for the first virtual machine.

#### Connect to the primary Linux VM

1. In the left menu, click **Virtual machines**.

2. On the **Virtual machines** blade, click the **P10999\<*YourInitials\>\<Today's Date*\>** server.

3. On the **P10999\<*YourInitials\>\<Today's Date*\>** blade, next to the **Public IP** **address** value, click **Copy to clipboard**.

4. Start **Notepad**, type **Primary Public IP address**, press Enter, press Ctrl+V to paste the public IP address, and then press Enter.

5. In Internet Explorer, on the **P10999\<*YourInitials\>\<Today's Date*\>** blade, next to the **Private IP** **address** value, click **Copy to clipboard**.

6. Start **Notepad**, type **Primary Private IP address**, press Enter, press Ctrl+V to paste the private IP address, and then press Enter.

7. In Notepad, select the **Primary Public IP** from the file, and press Ctrl+C. Leave Notepad running for later reference.

8. On the Desktop, double-click **putty.exe - Shortcut** (for instructions on installing PuTTY, see the *Prerequisites* section of the *Module Overview* for this module).

9. In the **Open File - Security Warning** dialog box, click **Run**.

10. In the **PuTTY Configuration** dialog box, right-click in the **Host Name (or IP address)** box, click **Paste**, and then click **Open**.

11. If a **PuTTY Security Alert** message box appears, click **Yes**.

12. In the **\<*ip address*\> - PuTTY** window, type **student**, press Enter, type **Pa55w.rdDemo**, and then press Enter. If your login attempt is successful, you will be presented with a prompt starting:
    ```
    student@P10999<YourInitials><Today's Date>:~$
    ```

13. In the PuTTY session, enable the firewall, type the following command, and then press Enter:
    ```
    sudo ufw enable
    ```

14. At the **Command may disrupt existing ssh connections** prompt, type **y**, and then press Enter.

15. In the PuTTY session, to open the port of the SQL Server service, type the following command, and then press Enter:
    ```
    sudo ufw allow 1433
    ```

16. In the PuTTY session, to open the port of the SSH service, type the following command, and then press Enter:
    ```
    sudo ufw allow 22
    ```

17. In the PuTTY session, to open the port of the Samba service, type the following command, and then press Enter:
    ```
    sudo ufw allow 445
    ```

18. In the PuTTY session, to allow the endpoint port communication between the server instances, type the following command, and then press Enter:
```
sudo ufw allow 5022
```

19. In the PuTTY session, to verify the state of the SQL Server service, type the following command, and then press Enter:
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

20. In the PuTTY session, to change the **sa** password, type the following command, and then press Enter:
    ```
    sudo /opt/mssql/bin/mssql-conf set-sa-password
    ```

21. In the PuTTY session, at the **Enter the SQL Server system administrator password** prompt, type **Pa55w.rd**, and then press Enter.

22. In the PuTTY session, at the **Confirm the SQL Server system administrator password** prompt, type **Pa55w.rd**, and then press Enter.

    **Note**: If there is an error in the output, delete the resource group, and start the demo from the beginning.

23. In the PuTTY session, to start SQL Server, type the following command, and then press Enter:
    ```
    sudo systemctl start mssql-server
    ```

24. In the PuTTY session, to enable the **mssql-server-agent** package, type the following command, and then press Enter:
    ```
    sudo /opt/mssql/bin/mssql-conf set sqlagent.enabled true
    ```

25. In the PuTTY session, to restart the SQL Server service, type the following command, and then press Enter:
    ```
    sudo systemctl restart mssql-server
    ```

#### Configure the primary network share

1. In the PuTTY session, to install Samba, type the following command, and then press Enter:
    ```
    sudo apt-get install -y samba
    ```

    The last line of the output should begin **Processing triggers for**.

    If this command returns an error message (any line that begins **E:**), complete the following:

    1. Type the following command, then press Enter:
        ```
            sudo apt-get update
        ```

    2. Rerun the first command in this step. You must successfully install Samba before continuing.

2. In the PuTTY session, to create a directory for log shipping backups and set security, type the following command, and then press Enter:
    ```
    sudo mkdir /var/opt/mssql/tlogs
    ```

3. In the PuTTY session, type the following command, and then press Enter:
    ```
    sudo chown mssql:mssql /var/opt/mssql/tlogs
    ```

4. In the PuTTY session, type the following command, and then press Enter:
    ```
    sudo chmod 0700 /var/opt/mssql/tlogs
    ```

5. In the PuTTY session, to edit the Samba config file, type the following command, and then press Enter:
    ```
    sudo nano /etc/samba/smb.conf
    ```

6. Press Alt+/ to go to the end of the file, type the following command, and then press Enter:
    ```
    [tlogs]
    path=/var/opt/mssql/tlogs
    available=yes
    read only=yes
    browsable=yes
    public=yes
    writable=no
    ```

7. Press Ctrl+X, press Y, and then press Enter.

8. In the PuTTY session, to create a Samba user for **mssql**, type the following command, and then press Enter:
    ```
    sudo smbpasswd -a mssql
    ```

9. At the **New SMB password** prompt, type **Pa55w.rd123**, press Enter.

10. At the **Retype new SMB password** prompt, type **Pa55w.rd123**, and then press Enter.

11. In the PuTTY session, to restart the Samba services, type the following command, and then press Enter:
    ```
    sudo systemctl restart smbd.service nmbd.service
    ```

12. In the PuTTY session, to close your PuTTY session, type **exit**, and then press Enter.

#### Connect to the secondary Linux VM

1. In Internet Explorer, in the left menu, click **Virtual machines**.

2. On the **Virtual machines** blade, click the **S10999\<*YourInitials\>\<Today's Date*\>** server.

3. On the **S10999\<*YourInitials\>\<Today's Date*\>** blade, next to the **Public IP** **address** value, click **Copy to clipboard**.

4. In Notepad, type **Secondary Public IP address**, press Enter, press Ctrl+V to paste the public IP address, and then press Enter.

5. In Internet Explorer, on the **S10999\<*YourInitials\>\<Today's Date*\>** blade, next to the **Private IP** **address** value, click **Copy to clipboard**.

6. In Notepad, type **Secondary Private IP address**, press Enter, press Ctrl+V to paste the private IP address, and then press Enter.

7. In Notepad, select the **Secondary Public IP** from the file, and press Ctrl+C. Leave Notepad running for later reference.

8. On the Desktop, double-click **putty.exe - Shortcut**.

9. In the **Open File - Security Warning** dialog box, click **Run**.

10. In the **PuTTY Configuration** dialog box, right-click in the **Host Name (or IP address)** box, click **Paste**, and then click **Open**.

11. If a **PuTTY Security Alert** message box appears, click **Yes**.

12. In the **\<*ip address*\> - PuTTY** window, type **student**, press Enter, type **Pa55w.rdDemo**, and then press Enter. If your login attempt is successful, you will be presented with a prompt starting:
    ```
    student@S10999<YourInitials><Today's Date>:~$
    ```

13. In the PuTTY session, enable the firewall, type the following command, and then press Enter:
    ```
    sudo ufw enable
    ```

14. At the **Command may disrupt existing ssh connections** prompt, type **y**, and then press Enter.

15. In the PuTTY session, to open the port of the SQL Server service, type the following command, and then press Enter:
    ```
    sudo ufw allow 1433
    ```

16. In the PuTTY session, to open the port of the SSH service, type the following command, and then press Enter:
    ```
    sudo ufw allow 22
    ```

17. In the PuTTY session, to open the port of the Samba service, type the following command, and then press Enter:
    ```
    sudo ufw allow 445
    ```

18. In the PuTTY session, to allow the endpoint port communication between the server instances, type the following command, and then press Enter:
    ```
    sudo ufw allow 5022
    ```

19. In the PuTTY session, to verify the state of the SQL Server service, type the following command, and then press Enter:
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

20. In the PuTTY session, to change the **sa** password, type the following command, and then press Enter:
    ```
    sudo /opt/mssql/bin/mssql-conf set-sa-password
    ```

21. In the PuTTY session, at the **Enter the SQL Server system administrator password** prompt, type **Pa55w.rd**, and then press Enter.

22. In the PuTTY session, at the **Confirm the SQL Server system administrator password** prompt, type **Pa55w.rd**, and then press Enter.

    **Note**: If there is an error in the output, delete the resource group, and start the demo from the beginning.

23. In the PuTTY session, to start SQL Server, type the following command, and then press Enter:
    ```
    sudo systemctl start mssql-server
    ```

24. In the PuTTY session, to enable the **mssql-server-agent** package, type the following command, and then press Enter:
    ```
    sudo /opt/mssql/bin/mssql-conf set sqlagent.enabled true
    ```

25. In the PuTTY session, to restart the SQL Server service, type the following command, and then press Enter:
    ```
    sudo systemctl restart mssql-server
    ```

#### Configure the secondary network share

1. In the PuTTY session, to install the CIFS client, type the following command, and then press Enter:
    ```
    sudo apt-get install -y cifs-utils
    ```

2. In the PuTTY session, to create a credential file, type the following command, and then press Enter:
    ```
    sudo nano /var/opt/mssql/.log-ship-creds
    ```

3. In the PuTTY session, type the following command, and then press Enter:
    ```
    username=mssql
    domain=
    password=Pa55w.rd123
    ```

4. Press Ctrl+X, press Y, and then press Enter.

5. In the PuTTY session, to create a mount point and configure ownership, type the following command, and then press Enter:
    ```
    sudo mkdir /var/opt/mssql/tlogs
    ```

6. In the PuTTY session, type the following command, and then press Enter:
    ```
    sudo chown root:root /var/opt/mssql/tlogs
    ```

7. In the PuTTY session, type the following command, and then press Enter:
    ```
    sudo chmod 0550 /var/opt/mssql/tlogs
    ```

8. In the PuTTY session, type the following command, and then press Enter:
    ```
    sudo chown root:root /var/opt/mssql/.log-ship-creds
    ```

9. In the PuTTY session, type the following command, and then press Enter:
    ```
    sudo chmod 0660 /var/opt/mssql/.log-ship-creds
    ```

10. In the PuTTY session, to make the mount permanent, type the following command, and then press Enter:
    ```
    sudo nano /etc/fstab
    ```

11. In the PuTTY session, press ALT+/, and then type the following command:
    ```
    #log shipping share
    //<primary_private_ip>/tlogs /var/opt/mssql/tlogs cifs credentials=/var/opt/mssql/.log-ship-creds,ro,uid=mssql,gid=mssql 0 0
    ```

    Replace **\<primary_private_ip\>** with the **Primary Private IP address** from Notepad.

12. In the PuTTY session, press Ctrl+X, press Y, then press Enter.

13. In the PuTTY session, type the following command, and then press Enter:
    ```
    sudo mount -a
    ```

14. In the PuTTY session, type **exit**, and then press Enter.

#### Configure the primary database

1. In Notepad, select the **Primary** **Public IP address** from the file, and press Ctrl+C.

2. On the Taskbar, click **Microsoft SQL Server Management Studio 17**.

3. In the **Connect to Server** dialog box, click **Cancel**.

4. On the **File** menu, point to **Open**, and then click **File**.

5. In the **File name** box, type **D:\\Demofiles\\Mod07\\Demo01\\primary.sql**, and then click **Open**.

6. In the **Connect to Database Engine** dialog box, in the **Server name** box, press Ctrl+V.

7. In the **Authentication** list, click **SQL Server Authentication**.

8. In the **Login** box, type **sa**.

9. In the **Password** box, type **Pa55w.rd**, and then click **Connect**.

10. In Notepad, select the **Secondary Private IP** **address** from the file, and press Ctrl+C.

11. In Microsoft SQL Server Management Studio 17, click anywhere in **primary.sql**, and then press Ctrl+Shift+M.

12. In the **Specify values for Template Parameters** dialog box, in the **Value** box, press Ctrl+V, and then click **OK**.

13. Review the contents of the **primary.sql** file, and then click **Execute**.

#### Configure the secondary database

1. In Notepad, select the **Secondary Public IP address** from the file, and press Ctrl+C.

2. In Microsoft SQL Server Management Studio 17, on the **File** menu, point to **Open**, and then click **File**.

3. In the **File name** box, type **D:\\Demofiles\\Mod07\\Demo01\\secondary.sql**, and then click **Open**.

4. On the **Query** menu, point to **Connection**, and then click **Change Connection**.

5. In the **Connect to Database Engine** dialog box, in the **Server name** box, press Ctrl+V.

6. In the **Authentication** list, click **SQL Server Authentication**.

7. In the **Login** box, type **sa**.

8. In the **Password** box, type **Pa55w.rd**, and then click **Connect**.

9. In Notepad, select the **Primary** **Private IP address** from the file, and press Ctrl+C.

10. In Microsoft SQL Server Management Studio 17, click anywhere in **secondary.sql**, and then press Ctrl+Shift+M.

11. In the **Specify values for template parameters** dialog box, in the **Value** box, press Ctrl+V, and then click **OK**.

12. Review the contents of the **secondary.sql** file, and then click **Execute**.

#### Verify that log shipping is functioning

1. In Notepad, select the **Primary** **Public IP address** from the file, and press Ctrl+C.

2. In Microsoft SQL Server Management Studio 17, click **File**, click **Open**, and then click **File**. In the **File name** box, type **D:\\Demofiles\\Mod07\\Demo01\\verify.sql**, and then click **Open**.

3. On the **Query** menu, point to **Connection**, and then click **Change Connection**.

4. In the **Connect to Database Engine** window, click in the **Server name** box then press Ctrl+V.

5. In the **Authentication** box, select **SQL Server Authentication**.

6. In the **Login** box, type **sa**.

7. In the **Password** box, type **Pa55w.rd**, then click **Connect**.

8. Select the command under the comment that begins **Step 1**, then click **Execute**.

9. Wait for 60 seconds before continuing.

10. In Notepad, select the **Secondary** **Public IP address** from the file, and press Ctrl+C.

11. In Microsoft SQL Server Management Studio 17, on the **Query** menu, point to **Connection**, and then click **Change Connection**.

12. In the **Connect to Database Engine** window, click in the **Server name** box then press Ctrl+V.

13. In the **Authentication** box, select **SQL Server Authentication**.

14. In the **Login** box, type **sa**.

15. In the **Password** box, type **Pa55w.rd**, then click **Connect**.

16. Select the command under the comment that begins **Step 2**, then click **Execute**. The query should return rows from the **test1** table.

17. Close Microsft SQL Server Management Studio17 without saving any changes.

18. Leave Notepad and Internet Explorer open for the next demonstration.

---

## Lesson 3: Availability groups

### Demo 1: Create an Always On availability group

#### Connect to the Primary Linux VM

-   If you have the public and private addresses of your VMs in Notepad, you can skip to the next task, otherwise complete the following steps.

    1. In the left menu, click **Virtual machines**.

    2. On the **Virtual machines** blade, click the **P10999\<*YourInitials\>\<Today's Date*\>** server.

    3. On the **P10999\<*YourInitials\>\<Today's Date*\>** blade, next to the **Public IP** **address** value, click **Copy to clipboard**.

    4. Start **Notepad**, type **Primary Public IP address**, press Enter, press Ctrl+V to paste the public IP address, and then press Enter.

    5. In Internet Explorer, on the **P10999\<*YourInitials\>\<Today's Date*\>** blade, next to the **Private IP** **address** value, click **Copy to clipboard**.

    6. In Notepad, type **Primary Private IP address**, press Enter, press Ctrl+V to paste the private IP address, and then press Enter.

    7. In Internet Explorer, in the left menu, click **Virtual machines**.

    8. On the **Virtual machines** blade, click the **S10999\<*YourInitials\>\<Today's Date*\>** server.

    9. On the **S10999\<*YourInitials\>\<Today's Date*\>** blade, next to the **Public IP** **address** value, click **Copy to clipboard**.

    10. In Notepad, type **Secondary Public IP address**, press Enter, press Ctrl+V to paste the public IP address, and then press Enter.

    11. In Internet Explorer, on the **S10999\<*YourInitials\>\<Today's Date*\>** blade, next to the **Private IP** **address** value, click **Copy to clipboard**.

    12. In Notepad, type **Secondary Private IP address**, press Enter, press Ctrl+V to paste the private IP address, and then press Enter.

    13. Leave Notepad running for later reference.

#### Configure the primary server

1. In Notepad, select the **Primary** **Public IP address** from the file, and press Ctrl+C.

2. On the Desktop, double-click **putty.exe - Shortcut**.

3. In the **Open File - Security Warning** dialog box, click **Run**.

4. In the **PuTTY Configuration** dialog box, right-click in the **Host Name (or IP address)** box, click **Paste**, and then click **Open**.

5. If a **PuTTY Security Alert** message box appears, click **Yes**.

6. In the **\<*ip address*\> - PuTTY** window, type **student**, press Enter, type **Pa55w.rdDemo**, and then press Enter. If your login attempt is successful, you will be presented with a prompt starting:
    ```
    student@S10999<YourInitials><Today's Date>:~$
    ```

7. In the PuTTY session, type the following command, and then press Enter:
    ```
    sudo nano /etc/hosts
    ```

8. Press End, and then press Enter.

9. In Notepad, select the **Primary** **Private IP address** from the file, and press Ctrl+C.

10. Right-click the window to paste the **Primary** **Private IP address**, press Space, type **P10999\<*YourInitials\>\<Today's Date*\>**, and then press Enter.

11. In Notepad, select the **Secondary** **Private IP address** from the file, and press Ctrl+C.

12. In the PuTTY session, right-click the window to paste, press Space, and then type **S10999\<*YourInitials\>\<Today's Date*\>**.

13. Press Ctrl+X, press Y, and then press Enter.

14. In the PuTTY session, to enable high availability support, type the following command, and then press Enter:
    ```
    sudo /opt/mssql/bin/mssql-conf set hadr.hadrenabled 1
    ```

15. In the PuTTY session, type the following command, and then press Enter:
    ```
    sudo systemctl restart mssql-server
    ```

16. In Notepad, select the **Primary** **Public IP address** from the file, and press Ctrl+C.

17. On the Taskbar, click **Microsoft SQL Server Management Studio 17**.

18. In the **Connect to Server** dialog box, click **Cancel**.

19. On the **File** menu, point to **Open**, and then click **File**.

20. In the **File name** box, type **D:\\Demofiles\\Mod07\\Demo02\\primary_ag.sql**, and then click **Open**.

21. In the **Connect to Database Engine** window, in the **Server name** box, press Ctrl+V.

22. In the **Authentication** list, click **SQL Server Authentication**.

23. In the **Login** box, type **sa**.

24. In the **Password** box, type **Pa55w.rd**, and then click **Connect**.

25. To set up a database for use with the demo, select the code under the comment that begins **Step P1**, and then click **Execute**.

26. To enable the AlwaysOn XEvents session, select the code under the comment that begins **Step P2**, and then click **Execute**.

27. To create a mirroring user, select the code under the comment that begins **Step P3**, and then click **Execute**.

28. To create a certificate, select the code under the comment that begins **Step P4**, and then click **Execute**.

29. To create an endpoint, select the code under the comment that begins **Step P5**, and then click **Execute**.

30. In Notepad, select the **Secondary** **Private IP address** from the file, and press Ctrl+C.

31. In the PuTTY session, to copy the certificate to the second node, type the following command, and then press Enter:
    ```
    sudo scp /var/opt/mssql/data/dbm_certificate.pvk /var/opt/mssql/data/dbm_certificate.cer student@<Private IP>:/tmp/
    ```

    Replace the **\<*Private IP*\>** value with the **Secondary** **Private IP** address from the file in Notepad.

32. In the PuTTY session, type **yes**, and then press Enter.

33. In the PuTTY session, type **Pa55w.rdDemo**, and then press Enter.

34. In the PuTTY session, type **exit**, and then press Enter to close PuTTY.

#### Configure the secondary server

1. In Notepad, select the **Secondary** **Public IP** **address** from the file, and press Ctrl+C.

2. On the Desktop, double-click **putty.exe - Shortcut**.

3. In the **Open File - Security Warning** dialog box, click **Run**.

4. In the **PuTTY Configuration** dialog box, right-click in the **Host Name (or IP address)** box, click **Paste**, and then click **Open**.

5. If a **PuTTY Security Alert** message box appears, click **Yes**.

6. In the **\<*ip address*\> - PuTTY** window, type **student**, press Enter, type **Pa55w.rdDemo**, and then press Enter. If your login attempt is successful, you will be presented with a prompt starting:
    ```
    student@S10999<YourInitials><Today's Date>:~$
    ```

7. In the PuTTY session, type the following command, and then press Enter:
    ```
    sudo /opt/mssql/bin/mssql-conf set hadr.hadrenabled 1
    ```

8. In the PuTTY session, to restart the SQL Server, type the following command, and then press Enter:
    ```
    sudo systemctl restart mssql-server
    ```

9. In Notepad, select the **Primary** **Private IP address** from the file, and press Ctrl+C.

10. In the PuTTY session, type the following command, and then press Enter:
    ```
    sudo nano /etc/hosts
    ```

11. In the PuTTY session, press End, and then press Enter.

12. In the PuTTY session, right-click the window to paste, press Space, type **P10999\<*YourInitials\>\<Today's Date*\>**, and then press Enter.

13. In Notepad, select the **Secondary** **Private IP address** from the file, and press Ctrl+C.

14. In the PuTTY session, right-click the window to paste, press Space, and then type **S10999\<*YourInitials\>\<Today's Date*\>**.

15. Press Ctrl+X, press Y, and then press Enter.

16. In the PuTTY session, to copy the certificate files to the correct location, type the following command, and then press Enter:
    ```
    sudo mv /tmp/dbm_certificate.* /var/opt/mssql/data/
    ```

17. In the PuTTY session, to grant the **mssql** user ownership of the files, type the following command, and then press Enter:
    ```
    sudo chown mssql:mssql /var/opt/mssql/data/dbm_certificate.cer
    ```

18. In the PuTTY session, type the following command, and then press Enter:
    ```
    sudo chown mssql:mssql /var/opt/mssql/data/dbm_certificate.pvk
    ```

19. In the PuTTY session, type **exit**, and then press Enter.

20. In Notepad, select the **Secondary** **Public IP address** from the file, and press Ctrl+C.

21. In Microsoft SQL Server Management Studio 17, on the **File** menu, point to **Open**, and then click **File**.

22. In the **File name** box, type **D:\\Demofiles\\Mod07\\Demo02\\secondary_ag.sql**, and then click **Open**.

23. On the **Query** menu, point to **Connection**, and then click **Change Connection**.

24. In the **Connect to Database Engine** dialog box, in the **Server name** box, press Ctrl+V.

25. In the **Authentication** list, click **SQL Server Authentication**.

26. In the **Login** box, type **sa**.

27. In the **Password** box, type **Pa55w.rd**, and then click **Connect**.

28. To enable the AlwaysOn XEvents session, select the code under the comment that begins **Step S1**, and then click **Execute**.

29. To create a mirroring user, select the code under the comment that begins **Step S2**, and then click **Execute**.

30. To restore the certificate, select the code under the comment that begins **Step S3**, and then click **Execute**.

31. To create an endpoint, select the code under the comment that begins **Step S4**, and then click **Execute**.

#### Configure the availability group

1. In Microsoft SQL Server Management Studio 17, in the **primary_ag,sql** window, press Ctrl+Shift+M.

2. In the **Specify Values for Template Parameters** window, in the upper **Value** box, type **P10999\<YourInitials\>\<Today's Date\>**.

3. In the lower **Value** box, type **S10999\<YourInitials\>\<Today's Date\>**, and then click **OK**.

4. Select the code under the comment that begins **Step P6**, and then click **Execute**.

5. In the **secondary_ag.sql** window, to join the secondary server to the availability group, select the code under the comment that begins **Step S5**, and then click **Execute**.

6. In the **primary_ag,sql** window, to add the **ag_demo** database to the availability group, select the code under the comment that begins **Step P7**, and then click **Execute**.

7. In the **secondary_ag.sql** window, to monitor the status of the availability group, select the code under the comment that begins **Step S6**, and then click **Execute**. The **ag_demo** database should appear in the list of databases.

8. Close Microsft SQL Server Management Studio17 without saving any changes.

#### Install and configure Pacemaker

1. In Notepad, select the **Primary Public IP address** from the file, and press Ctrl+C.

2. On the Desktop, double-click **putty.exe - Shortcut**.

3. In the **Open File - Security Warning** dialog box, click **Run**.

4. In the **PuTTY Configuration** dialog box, right-click in the **Host Name (or IP address)** box, click **Paste**, and then click **Open**.

5. If a **PuTTY Security Alert** message box appears, click **Yes**.

6. In the **\<*ip address*\> - PuTTY** window, type **student**, press Enter, type **Pa55w.rdDemo**, and then press Enter. If your login attempt is successful, you will be presented with a prompt starting:
    ```
    student@S10999<YourInitials><Today's Date>:~$
    ```

7. In the PuTTY session, to configure Pacemaker, type the following commands, and then press Enter:
    ```
    sudo apt install pcs
    ```

8. In the PuTTY session, type **y**, and then press Enter.

9. In the PuTTY session, to configure Pacemaker, type the following commands, and then press Enter:
    ```
    sudo pcs cluster destroy
    sudo systemctl enable pcsd
    sudo systemctl start pcsd
    sudo systemctl enable pacemaker
    ```

    If the last command generates an error **update-rc.d: error: pacemaker Default-Start contains no runlevels, aborting.** it's safe to ignore.

10. In the PuTTY session, to set the password for the **hacluster** user, type the following command, and then press Enter:
    ```
    sudo passwd hacluster
    ```

11. In the PuTTY session, type **Pa55w.rd**, and then press Enter.

12. In the PuTTY session, type **Pa55w.rd**, and then press Enter.

13. In the PuTTY session, to create a file to hold the Pacemaker SQL Server credentials, type the following command, and then press Enter:
    ```
    echo 'pacemaker' >> ~/pacemaker-passwd
    echo 'Pa55w.rd' >> ~/pacemaker-passwd
    sudo mv ~/pacemaker-passwd /var/opt/mssql/secrets/passwd
    sudo chown root:root /var/opt/mssql/secrets/passwd
    sudo chmod 400 /var/opt/mssql/secrets/passwd
    ```

14. In Notepad, select the **Secondary** **Private IP** **address** from the file, and press Ctrl+C.

15. In the PuTTY session, type the following command, and then press Enter:
    ```
    ssh student@<Private IP>
    ```

    Replace the **\<*Private IP*\>** value with the **Secondary** **Private IP** **address** from the file in Notepad.

16. In the PuTTY session, type **yes**, and then press Enter.

17. In the PuTTY session, type **Pa55w.rdDemo**, and then press Enter.

18. In the PuTTY session, to configure Pacemaker, type the following commands, and then press Enter:
    ```
    sudo apt install pcs
    ```

19. In the PuTTY session, type **y**, and then press Enter.

20. In the PuTTY session, type the following commands, and then press Enter:
    ```
    sudo pcs cluster destroy
    sudo systemctl enable pcsd
    sudo systemctl start pcsd
    sudo systemctl enable pacemaker
    ```

    If an error message is generated, ignore it.

21. In the PuTTY session, to create a file to hold the Pacemaker SQL Server credentials, type the following command, and then press Enter:
    ```
    echo 'pacemaker' >> ~/pacemaker-passwd
    echo 'Pa55w.rd' >> ~/pacemaker-passwd
    sudo mv ~/pacemaker-passwd /var/opt/mssql/secrets/passwd
    sudo chown root:root /var/opt/mssql/secrets/passwd
    sudo chmod 400 /var/opt/mssql/secrets/passwd
    ```

22. In the PuTTY session, to set the password for the **hacluster** user, type the following command, and then press Enter:
    ```
    sudo passwd hacluster
    ```

23. In the PuTTY session, type **Pa55w.rd**, and then press Enter.

24. In the PuTTY session, type **Pa55w.rd**, and then press Enter.

25. In the PuTTY session, to allow the communication between the server instances, type the following command, and then press Enter:
    ```
    sudo ufw allow <primary_private_ip>
    ```

    Replace **\<primary_private_ip\>** with the **Primary Private IP address** from Notepad.

26. In the PuTTY session, type **exit**, and then press Enter.

27. In the PuTTY session, type the following commands, and then press Enter:
    ```
    sudo pcs cluster auth <primary name> <secondary name> -u hacluster -p Pa55w.rd
    ```

    Replace **\<*primary name*\>** with **P10999\<*YourInitials\>\<Today's Date*\>**, and replace **\<*secondary name*\>** with **S10999\<*YourInitials\>\<Today's Date*\>**.

28. In the PuTTY session, type the following commands, and then press Enter:
    ```
    sudo pcs cluster setup --name agcluster <primary name> <secondary name>
    ```

    Replace **\<*primary name*\>** with **P10999\<*YourInitials\>\<Today's Date*\>**, and replace **\<*secondary name*\>** with **S10999\<*YourInitials\>\<Today's Date*\>**.

29. In the PuTTY session, to start the cluster, type the following command, and then press Enter:
    ```
    sudo pcs cluster start --all
    ```

30. In the PuTTY session, to disable fencing, type the following command, and then press Enter:
    ```
    sudo pcs property set stonith-enabled=false
    ```

31. In the PuTTY session, to allow the cluster to start automatically even if the primary node fails, type the following command, and then press Enter:
    ```
    sudo pcs property set start-failure-is-fatal=false
    ```

32. In the PuTTY session, to create the availability group cluster resource, type the following command, and then press Enter:
    ```
    sudo pcs resource create ag_cluster ocf:mssql:ag ag_name=ag1 --master meta notify=true
    ```

33. In the PuTTY session, to create a virtual IP cluster resource, type the following command, and then press Enter:
    ```
    sudo pcs resource create virtualip ocf:heartbeat:IPaddr2 ip=<ip>
    ```

    Replace **\<*ip*\>** with an unassigned address in the private subnet used by your virtual machines. For example, if your virtual machines have the private IP addresses 10.0.0.4 and 10.0.0.5, **10.0.0.100** would be a suitable choice for the virtual IP.

    Configuring a public virtual IP address in Azure is beyond the scope of this module.

34. In the PuTTY session, to add a colocation constraint, type the following command, and then press Enter:
    ```
    sudo pcs constraint colocation add virtualip ag_cluster-master INFINITY with-rsc-role=Master
    ```

35. In the PuTTY session, to add an ordering constraint, type the following command, and then press Enter:
    ```
    sudo pcs constraint order promote ag_cluster-master then start virtualip
    ```

36. In the PuTTY session, to start virtualip, type the following command, and then press Enter:
    ```
    sudo pcs resource debug-start virtualip
    ```

37. In the PuTTY session, to confirm that the cluster is operational, connect to the cluster virtual IP, type the following command, and then press Enter:
    ```
    /opt/mssql-tools/bin/sqlcmd -S <ip> -U sa -P Pa55w.rd
    ```

    Replace **\<*ip*\>** with your cluster virtual IP. If the cluster is functioning, **sqlcmd** should connect.

38. In the PuTTY session, type **select @@servername**, and then press Enter.

39. In the PuTTY session, to confirm the name of the server you have connected to, type **GO**, and then press Enter.

40. In the PuTTY session, to close **sqlcmd**, type **exit**, and then press Enter.

41. In the PuTTY session, to close **PuTTY**, type **exit**, and then press Enter.

> **Note**: If you have problems configuring Pacemaker, check the status of the following:
> -   pcsd daemon on both nodes with the **sudo systemctl status pcsd** command.
> -   Logs for **pcsd** go to **/var/log/pcsd/pcsd.log** and **/var/log/syslog**.
> -   Check cluster status with **sudo pcs status**.

#### Delete the Azure virtual machine

1. In Internet Explorer, on the left blade of the Azure portal, click **Resource groups**.

2. On the **Resource groups** blade, click **10999A**.

3. On the **10999A** blade, click **Delete resource group**.

4. On the **Are you sure you want to delete"10999A"?** blade, type **10999A**, and then click **Delete**. This deletes the virtual machines.

5. If there is a **NetworkWatcherRG** resource group present, repeat steps 2 to 4 to delete.

6. Close all open programs and windows.
