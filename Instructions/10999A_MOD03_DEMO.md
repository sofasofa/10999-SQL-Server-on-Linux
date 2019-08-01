# Module 3: Installing Additional Components
- [Module 3: Installing Additional Components](#module-3-installing-additional-components)
  - [Lesson 1: SQL Server Agent](#lesson-1-sql-server-agent)
    - [Demo 1: Installing SQL Server Agent](#demo-1-installing-sql-server-agent)
      - [Create an Ubuntu 16.04 LTS VM in Azure](#create-an-ubuntu-1604-lts-vm-in-azure)
      - [Connect to the Linux VM](#connect-to-the-linux-vm)
      - [Enable SQL Server Agent](#enable-sql-server-agent)
  - [Lesson 2: SQL Server Full-Text Search](#lesson-2-sql-server-full-text-search)
    - [Demo 1: Installing SQL Server Full-Text Search](#demo-1-installing-sql-server-full-text-search)
      - [Install Full-Text Search](#install-full-text-search)
  - [Lesson 3: SQL Server Integration Services](#lesson-3-sql-server-integration-services)
    - [Demo 1: Install SSIS](#demo-1-install-ssis)
      - [Install and configure SSIS](#install-and-configure-ssis)
      - [Delete the virtual machine](#delete-the-virtual-machine)

## Lesson 1: SQL Server Agent

### Demo 1: Installing SQL Server Agent

#### Create an Ubuntu 16.04 LTS VM in Azure

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

#### Connect to the Linux VM

1. In the left menu, click **Virtual machines**.

2. On the **Virtual machines** blade, click the **10999A\<*YourInitials\>\<Today's Date*\>** server.

3. On the **10999A\<*YourInitials\>\<Today's Date*\>** blade, next to the **Public IP** **address** value, click **Copy to clipboard**.

4. On the Desktop, double-click **putty.exe - Shortcut** (for instructions on installing PuTTY, see the *Prerequisites* section of the *Module Overview* for this module).

5. In the **Open File - Security Warning** dialog box, click **Run**.

6. In the **PuTTY Configuration** dialog box, right-click in the **Host Name (or IP address)** box, click **Paste**, and then click **Open**.

7. If a **PuTTY Security Alert** message box appears, click **Yes**.

8. In the **student@10999A\<*YourInitials\>\<Today's Date*\>:~** window, type **student**, press Enter, type **Pa55w.rdDemo**, and then press Enter. If your login attempt is successful, you will be presented with a prompt starting:
    ```
    student@10999A<YourInitials><Today's Date>:~$
    ```

#### Enable SQL Server Agent

1. In the PuTTY session, to verify the state of the SQL Server service, type the following command, and then press Enter:
    ```
    systemctl status mssql-server
    ```

    If SQL Server is running, the output will include a line that begins:
    ```
    Active: active (running)
    ```

    If SQL Server is inactive, type the following command, and then press
    Enter:
    ```
    sudo systemctl start mssql-server
    ```

2. In the PuTTY session, to enable the **mssql-server-agent** package, type the following command, and then press Enter:
    ```
    sudo /opt/mssql/bin/mssql-conf set sqlagent.enabled true
    ```

3. In the PuTTY session, to restart the SQL Server service, type the following command, and then press Enter:
    ```
    sudo systemctl restart mssql-server
    ```

4. In the PuTTY session, to verify the state of the SQL Server service, type the following command, and then press Enter:
    ```
    systemctl status mssql-server
    ```
    The output will include a line that begins
    ```
    Active: active (running)
    ```

5. Leave your PuTTY session running for the next demonstration.

---

## Lesson 2: SQL Server Full-Text Search

### Demo 1: Installing SQL Server Full-Text Search

#### Install Full-Text Search

1. In the PuTTY session, type the following command, and then press Enter:
    ```
    sudo apt-get update
    ```

2. In the PuTTY session, to install the **mssql-server-fts** package, type the following command, and then press Enter:
    ```
    sudo apt-get install mssql-server-fts
    ```

3. In the PuTTY session, to restart the SQL Server service, type:
    ```
    sudo systemctl restart mssql-server
    ```

4. In the PuTTY session, to verify the state of the SQL Server service, type the following shell command:
    ```
    systemctl status mssql-server
    ```
    If SQL Server is running, the output will include a line that begins:
    ```
    Active: active (running)
    ```

5. Leave your PuTTY session running for the next demonstration.

---

## Lesson 3: SQL Server Integration Services

### Demo 1: Install SSIS

#### Install and configure SSIS

1. In the PuTTY session, type the following command, and then press Enter:
    ```
    sudo apt-get update
    ```

2. In the PuTTY session, to install or update the **mssql-server-is** package, type the following command, and then press Enter:
    ```
    sudo apt-get install mssql-server-is
    ```

3. In the PuTTY session, if prompted, type **y**, and then press Enter.

4. In the PuTTY session, to begin configuring the **mssql-server-is** package, type the following command, and then press Enter:
    ```
    sudo /opt/ssis/bin/ssis-conf setup
    ```

5. At the prompt to select an edition, type **1**, and then press Enter. This selects the Evaluation edition.

6. At the prompt to accept the license terms, type **Yes**, and then press Enter

7. In the PuTTY session, to verify the state of the SQL Server service, type the following shell command:
    ```
    systemctl status mssql-server
    ```

    If SQL Server is running, the output will include a line that begins:
    ```
    Active: active (running)
    ```

8. In the PuTTY session, type **exit**, and then press Enter to close your PuTTY session.

#### Delete the virtual machine

1. In Internet Explorer, on the left blade of the Azure portal, click **Resource groups**.

2. On the **Resource groups** blade, click **10999A**.

3. On the **10999A** blade, click **Delete resource group**.

4. On the **Are you sure you want to delete\"10999A\"?** blade, type **10999A**, and then click **Delete**. This deletes the virtual machines.

5. If there is a **NetworkWatcherRG** resource group present, repeat steps 2 to 4 to delete.

6. Close all open programs and windows.
