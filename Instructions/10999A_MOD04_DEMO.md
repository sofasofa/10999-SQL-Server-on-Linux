# Module 4: Configuring SQL Server on Linux
- [Module 4: Configuring SQL Server on Linux](#module-4-configuring-sql-server-on-linux)
  - [Lesson 1: Configure SQL Server with mssql-conf](#lesson-1-configure-sql-server-with-mssql-conf)
    - [Demo 1: Configuring SQL Server with environment variables](#demo-1-configuring-sql-server-with-environment-variables)
      - [Create a SQL Server on Ubuntu VM in Azure](#create-a-sql-server-on-ubuntu-vm-in-azure)
      - [Connect to the Linux VM](#connect-to-the-linux-vm)
      - [Carry out configuration](#carry-out-configuration)
      - [Delete the virtual machine](#delete-the-virtual-machine)
  - [Lesson 2: Configure a SQL Server on a Linux Docker image](#lesson-2-configure-a-sql-server-on-a-linux-docker-image)
    - [Demo 1: Persisting your data](#demo-1-persisting-your-data)
      - [Create a Docker on Ubuntu Server in Azure](#create-a-docker-on-ubuntu-server-in-azure)
      - [Connect to the new Ubuntu server by using PuTTY](#connect-to-the-new-ubuntu-server-by-using-putty)
      - [Pull the SQL Server Docker image](#pull-the-sql-server-docker-image)
      - [Mount a host directory as a data volume](#mount-a-host-directory-as-a-data-volume)
      - [Use a data volume container](#use-a-data-volume-container)
      - [Delete the virtual machine](#delete-the-virtual-machine-1)

## Lesson 1: Configure SQL Server with mssql-conf

### Demo 1: Configuring SQL Server with environment variables

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

#### Carry out configuration

1. In the PuTTY session, to view the current SQL Server configuration file, type the following command, and then press Enter:
    ```
    sudo cat /var/opt/mssql/mssql.conf
    ```

2. If you are prompted for the **sudo** password, type **Pa55w.rdDemo**, and then press Enter.

3. Examine the output of the command, and note that the file contains minimal configuration.

4. In the PuTTY session, to stop the SQL Server service, type the following command, and then press Enter:
    ```
    sudo systemctl stop mssql-server
    ```

    You need to stop SQL Server before rerunning **mssql-conf setup**.

5. In the PuTTY session, to verify the state of the SQL Server service, type the following command, and then press Enter:
    ```
    systemctl status mssql-server
    ```

    The output will include a line that begins
    ```
    Active: inactive (dead)
    ```

6. In the PuTTY session, to change the **sa** password, type the following command, and then press Enter:
    ```
    sudo /opt/mssql/bin/mssql-conf set-sa-password
    ```

7. In the PuTTY session, at the **Enter the SQL Server system administrator password** prompt, type **Pa55w.rd**, and then press Enter.

8. In the PuTTY session, at the **Confirm the SQL Server system administrator password** prompt, type **Pa55w.rd**, and then press Enter.

9. In the PuTTY session, to start configuration, type the following command, and then press Enter:
    ```
    sudo /opt/mssql/bin/mssql-conf setup
    ```

    Notice that an interactive prompt is displayed, asking you to select an edition.

10. Press Ctrl+C to cancel the setup process.

11. In the PuTTY session, to set up some environment variables and start configuration, type the following command, and then press Enter:
    ```
    sudo MSSQL_PID='Evaluation' MSSQL_MEMORY_LIMIT_MB=2048 /opt/mssql/bin/mssql-conf setup
    ```

12. In the PuTTY session, at the **Enter the SQL Server system administrator password** prompt, type **Pa55w.rd**, and then press Enter.

13. In the PuTTY session, at the **Confirm the SQL Server system administrator password** prompt, type **Pa55w.rd**, and then press Enter.

14. Notice that no interactive prompt is displayed, the edition is switched to Evaluation edition, and after a few moments, a message is displayed that SQL Server has started.

15. In the PuTTY session, to view the new SQL Server configuration file, type the following command, and then press Enter:
    ```
    sudo cat /var/opt/mssql/mssql.conf
    ```

    Notice that an entry for memory limit has been added to the file.

16. In the PuTTY session, type **exit**, and then press Enter.

#### Delete the virtual machine

1. In Internet Explorer, on the left blade of the Azure portal, click **Resource groups**.

2. On the **Resource groups** blade, click **10999A**.

3. On the **10999A** blade, click **Delete resource group**.

4. On the **Are you sure you want to delete"10999A"?** blade, type **10999A**, and then click **Delete**. This deletes the virtual machines.

5. If there is a **NetworkWatcherRG** resource group present, repeat steps 2 to 4 to delete.

6. Close all open programs and windows.

---

## Lesson 2: Configure a SQL Server on a Linux Docker image

### Demo 1: Persisting your data

#### Create a Docker on Ubuntu Server in Azure

1. Ensure that the **MT17B-WS2016-NAT**, **10999A-MIA-DC**, and **10999A-MIA-SQL** virtual machines are all running, and then log on to **10999A-MIA-SQL** as **ADVENTUREWORKS\\Student** with the password **Pa55w.rd**.

2. Using Microsoft Internet Explorer, type **https://portal.azure.com** to display the Azure portal. Enter your Microsoft Azure Pass credentials to log on.

3. In Microsoft Azure, click **+ Create a resource**, in the **Search** box, type **Docker Ubuntu**, and then press Enter.

4. On the **Showing All Results** page, click **Docker Community Edition With Ubuntu 16.04 Lts**.

5. On the **Docker Community Edition With Ubuntu 16.04 Lts** blade, click **Create**.

6. On the **Create a virtual machine** blade, enter the following information, and then click **Next : Disks \>**:

    -   **Subscription**: Your Azure Subscription

    -   **Resource group (Create new)**: 10999A

    -   **Virtual machine name**: 10999A\<*YourInitials\>\<Today's Date*\>, for example **10999AAce2017100**

    -   **Region**: Select the location that is geographically nearest to you.

        **Note**: If a message shows that "location is not supported" then please try with any of the below locations:

        -   East US

        -   South Central US

        -   West Europe

        -   Southeast Asia

        -   West US2

    -   **Image**: Docker Community Edition With Ubuntu 16.04 Lts

    -   **Size**: Basic A3

    -   **Authentication type**: Password

    -   **User name**: student

    -   **Password**: Pa55w.rdDemo

    -   **Confirm password**: Pa55w.rdDemo

7. On the **Disks** page, in the **OS disk type** list, click **Standard HDD**, and then click **Next : Networking \>**.

8. On the **Networking** page, in the **Configure network security group** box, click **Create new**, click **OK**, and then click **Next : Management \>**.

9. On the **Management** page, next to **Boot diagnostics**, click **Off**.

10. Next to **Enable auto-shutdown**, click **Off**, and then click **Review + create**.

11. On the **Review + create** page, in the **Preferred phone number** box, enter your phone number, and then click **Create**.

12. When the deployment is complete, on the left blade of the Azure portal, click **Virtual machines**.

13. On the **Virtual machines** blade, click **10999A\<*YourInitials\>\<Today's Date*\>**.

#### Connect to the new Ubuntu server by using PuTTY

1. On the **10999A\<*YourInitials\>\<Today's Date*\>** blade, next to the **Public IP** **address** value, click **Copy to clipboard**.

2. On the Desktop, double-click **putty.exe - Shortcut** (for instructions on installing PuTTY, see the *Prerequisites* section of the *Module Overview* for this module).

3. In the **Open File - Security Warning** dialog box, click **Run**.

4. In the **PuTTY Configuration** dialog box, right-click in the **Host Name (or IP address)** box, click **Paste**, and then click **Open**.

5. If a **PuTTY Security Alert** message box appears, click **Yes**.

6. In the **student@10999A\<*YourInitials\>\<Today's Date*\>:~** window, type **student**, press Enter, type **Pa55w.rdDemo**, and then press Enter. If your login attempt is successful, you will be presented with a prompt starting:
    ```
    student@10999A<YourInitials><Today's Date>:~$
    ```

#### Pull the SQL Server Docker image

1. In the PuTTY session, type the following command, and then press Enter:
    ```
    sudo docker pull microsoft/mssql-server-linux:2017-GA
    ```

2. Wait until the download is complete.

3. In the PuTTY session, to check that the image exists, type the following command, and then press Enter:
    ```
    sudo docker images
    ```

#### Mount a host directory as a data volume

1. In the PuTTY session, to create a new folder to use as a data volume, type the following command, and then press Enter:
    ```
    mkdir /tmp/container1
    ```

2. In the PuTTY session, to start a container using the folder you have created as a data volume, type the following command, and then press Enter:
    ```
    sudo docker run -e 'ACCEPT_EULA=Yes' -e 'MSSQL_SA_PASSWORD=Pa55w.rd' -v /tmp/container1:/var/opt/mssql --name demo1 -d microsoft/mssql-server-linux:2017-GA
    ```

3. In the PuTTY session, to create a database, type the following command, and then press Enter:
    ```
    sudo docker exec -it demo1 /opt/mssql-tools/bin/sqlcmd -S localhost -U sa
    ```

4. In the PuTTY session, when prompted for a password, type **Pa55w.rd**, and then press Enter.

5. In the PuTTY session, type the following commands, and then press Enter:
    ```
    CREATE DATABASE demodb;
    GO
    ```

6. In the PuTTY session, type the following commands, and then press Enter:
    ```
    SELECT name FROM sys.databases
    GO
    ```

    Observe that **demodb** is returned in the list of databases.

7. In the PuTTY session, type **exit**, and then press Enter.

8. In the PuTTY session, type the following command, and then press Enter:
    ```
    ls /tmp/container1
    ```

    Notice the directories that have been created.

9. In the PuTTY session, type the following command, and then press Enter:
    ```
    ls /tmp/container1/data
    ```

    Observe that the directory contains database data files and log files, including files for **demodb**.

10. In the PuTTY session, to delete the **demo1** container, type the following command, and then press Enter:
    ```
    sudo docker stop demo1
    ```

11. In the PuTTY session, type the following command, and then press Enter:
    ```
    sudo docker rm demo1
    ```

12. In the PuTTY session, type the following command, and then press Enter:
    ```
    ls /tmp/container1
    ```

    Notice that the directory used as a data volume is still present, even though the container has been removed.

13. In the PuTTY session, to create a new container that uses the same data volume, type the following command then press Enter:
    ```
    sudo docker run -e 'ACCEPT_EULA=Yes' -e 'MSSQL_SA_PASSWORD=Pa55w.rd' -v /tmp/container1:/var/opt/mssql --name demo2 -d microsoft/mssql-server-linux:2017-GA
    ```

14. In the PuTTY session, to demonstrate that the data has been used by the new container, type the following command, and then press Enter:
    ```
    sudo docker exec demo2 /opt/mssql-tools/bin/sqlcmd -S localhost -U sa -P Pa55w.rd -Q "SELECT name FROM sys.databases"
    ```

    Notice that the **demodb** database is listed.

15. In the PuTTY session, to delete the **demo2** container, type the following command, and then press Enter:
    ```
    sudo docker stop demo2
    ```

16. In the PuTTY session, type the following command, and then press Enter:
    ```
    sudo docker rm demo2
    ```

#### Use a data volume container

1. In the PuTTY session, to create a container using a data volume container, type the following command, and then press Enter:
    ```
    sudo docker run -e 'ACCEPT_EULA=Yes' -e 'MSSQL_SA_PASSWORD=Pa55w.rd' -v dvcontainer:/var/opt/mssql --name demo3 -d microsoft/mssql-server-linux:2017-GA
    ```

    The data volume will be created automatically.

2. In the PuTTY session, to confirm that the volume exists, type the following command, and then press Enter:
    ```
    sudo docker volume ls
    ```

    Notice that the **dvcontainer** volume is included in the results.

3. In the PuTTY session, to create a database, type the following command, and then press Enter:
    ```
    sudo docker exec -it demo3 /opt/mssql-tools/bin/sqlcmd -S localhost -U sa
    ```

4. In the PuTTY session, when prompted for a password, type **Pa55w.rd**, and then press Enter.

5. In the PuTTY session, type the following command, and then press Enter:
    ```
    CREATE DATABASE demodb2;
    GO
    ```

6. In the PuTTY session, type the following command, and then press Enter:
    ```
    SELECT name FROM sys.databases
    GO
    ```

    Observe that **demodb2** is returned in the list of databases.

7. In the PuTTY session, type **exit**, and then press Enter.

8. In the PuTTY session, to delete the **demo3** container, type the following command, and then press Enter:
    ```
    sudo docker stop demo3
    ```

9. In the PuTTY session, type the following command, and then press Enter:
    ```
    sudo docker rm demo3
    ```

10. In the PuTTY session, type the following command, and then press Enter:
    ```
    sudo docker volume ls
    ```

    Notice that the data volume is still present, even though the container has been removed.

11. In the PuTTY session, to start a new container that reuses the data volume, type the following command, and then press Enter:
    ```
    sudo docker run -e 'ACCEPT_EULA=Yes' -e 'MSSQL_SA_PASSWORD=Pa55w.rd' -v dvcontainer:/var/opt/mssql --name demo4 -d microsoft/mssql-server-linux:2017-GA
    ```

12. In the PuTTY session, to demonstrate that the data has been used by the new container, type the following command then press Enter:
    ```
    sudo docker exec demo4 /opt/mssql-tools/bin/sqlcmd -S localhost -U sa -P Pa55w.rd -Q "SELECT name FROM sys.databases"
    ```

    Notice that the **demodb2** database is listed.

13. In the PuTTY session, to delete the **demo4** container, type the following command then press Enter:
    ```
    sudo docker stop demo4
    ```

14. In the PuTTY session, type the following command, and then press Enter:
    ```
    sudo docker rm demo4
    ```

15. In the PuTTY session, type **exit**, and then press Enter to close your PuTTY session.

#### Delete the virtual machine

1. In Internet Explorer, on the left blade of the Azure portal, click **Resource groups**.

2. On the **Resource groups** blade, click **10999A**.

3. On the **10999A** blade, click **Delete resource group**.

4. On the **Are you sure you want to delete"10999A"?** blade, type **10999A**, and then click **Delete**. This deletes the virtual machines.

5. If there is a **NetworkWatcherRG** resource group present, repeat steps 2 to 4 to delete.

6. Close all open programs and windows.
