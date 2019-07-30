# Module 2: Deploying SQL Server on Linux

- [Module 2: Deploying SQL Server on Linux](#module-2-deploying-sql-server-on-linux)
  - [Lesson 1: Deploying SQL Server on Ubuntu](#lesson-1-deploying-sql-server-on-ubuntu)
    - [Demo 1: Installing SQL Server on Ubuntu](#demo-1-installing-sql-server-on-ubuntu)
      - [Create an Ubuntu 16.04 LTS VM in Azure](#create-an-ubuntu-1604-lts-vm-in-azure)
      - [Connect to the Linux VM](#connect-to-the-linux-vm)
      - [Install the SQL Server package](#install-the-sql-server-package)
      - [Configure the SQL Server package](#configure-the-sql-server-package)
      - [Install the command-line tools](#install-the-command-line-tools)
      - [Create a database](#create-a-database)
      - [Delete the virtual machine](#delete-the-virtual-machine)
  - [Lesson 4: Deploying SQL Server containers on Docker](#lesson-4-deploying-sql-server-containers-on-docker)
    - [Demo 1: Pulling and running a SQL Server 2017 Docker image](#demo-1-pulling-and-running-a-sql-server-2017-docker-image)
      - [Create a Docker on Ubuntu Server in Azure](#create-a-docker-on-ubuntu-server-in-azure)
      - [Connect to the new Ubuntu server by using PuTTY](#connect-to-the-new-ubuntu-server-by-using-putty)
      - [Pull the SQL Server Docker image](#pull-the-sql-server-docker-image)
      - [Run a Docker container](#run-a-docker-container)
      - [Create a database](#create-a-database-1)
      - [Delete the virtual machine](#delete-the-virtual-machine-1)

## Lesson 1: Deploying SQL Server on Ubuntu

### Demo 1: Installing SQL Server on Ubuntu

#### Create an Ubuntu 16.04 LTS VM in Azure

1. Ensure that the **MT17B-WS2016-NAT**, **10999A-MIA-DC** and **10999A-MIA-SQL** virtual machines are running, and then log on to **10999A-MIA-SQL** as **ADVENTUREWORKS\\Student** with the password **Pa55w.rd**.

2. Using Microsoft Internet Explorer®, type **https://portal.azure.com** to display the Azure portal. Enter your Microsoft Azure Pass credentials to log on.

3. In Microsoft Azure, in the left menu, click **+ Create a resource**, in the **Search** box, type **Ubuntu**, and then press Enter.

4. On the **Showing All Results** page, click **Ubuntu Server by Canonical**.

5. On the **Ubuntu Server** blade, in the **Select a software plan** list, click **Ubuntu Server 16.04 LTS**, and then click **Create**.

6. On the **Create a virtual machine** blade, enter the following information, and then click **Next : Disks \>**:

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

    -   **Image**: Ubuntu Server 16.04 LTS

    -   **Size**: Standard A2 v2

    -   **Authentication type**: Password

    -   **User name**: student

    -   **Password**: Pa55w.rdDemo

    -   **Confirm password**: Pa55w.rdDemo

1. On the **Disks** page, in the **OS disk type** list, click **Standard HDD**, and then click **Next : Networking \>**.

2. On the **Networking** page, next to **Public inbound ports**, click **Allow selected ports**.

3. In the **Select inbound ports** list, select the **SSH** check box, and then click **Next : Management \>**.

4. On the **Management** page, change the value of the following settings, and then click **Review + create**:

    -   **Boot diagnostics**: Off

    -   **Enable auto-shutdown**: Off

11. On the **Review + create** page, review the summary, and then click **Create**. Deployment might take a few minutes to complete.

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

#### Install the SQL Server package

1. In the PuTTY session, to register the Microsoft package repository GPG key, type the following command, and then press Enter:
    ```
    curl https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add -
    ```

2. If you are prompted for the **sudo** password, type **Pa55w.rdDemo**, and then press Enter.

3. In the PuTTY session, to register the Microsoft package repository, type the following command, and then press Enter:
    ```
    sudo add-apt-repository "$(curl https://packages.microsoft.com/config/ubuntu/16.04/mssql-server-2017.list)"
    ```

4. In the PuTTY session, to install the **mssql-server** package, type the following command, and then press Enter:
    ```
    sudo apt-get update
    ```

5. In the PuTTY session, type the following command, and then press Enter:
    ```
    sudo apt-get install mssql-server
    ```

6. In the PuTTY session, type **y**, and then press Enter.

    The installation might take a few minutes to complete.

#### Configure the SQL Server package

1. In the PuTTY session, to begin configuring the **mssql-server** package, type the following command, and then press Enter:
    ```
    sudo /opt/mssql/bin/mssql-conf setup
    ```

2. At the prompt to select an edition, type **1**, and then press Enter. This selects the Evaluation edition.

3. At the prompt to accept the license terms, type **Yes**, and then press Enter.

4. At the prompt to enter the SQL Server system administrator password, type **Pa55w.rd**, and then press Enter.

5. At the prompt to confirm the password, type **Pa55w.rd**, and then press Enter.

    After this step, configuration is complete. You should see a message in the terminal that setup has completed and SQL Server is starting.

6. In the PuTTY session, to verify the state of the SQL Server service, type the following command, and then press Enter:
    ```
    systemctl status mssql-server
    ```

    If SQL Server is running, the output will include a line that begins:
    ```
    Active: active (running)
    ```

7. Leave your PuTTY session running for the next task.

#### Install the command-line tools

1. In the PuTTY session, to register a new repository for the Microsoft SQL Server tools package, type the following command, and then press Enter:
    ```
    sudo add-apt-repository "$(curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list)"
    ```

2. In the PuTTY session, to update to package lists, type the following command, and then press Enter:
    ```
    sudo apt-get update
    ```

3. In the PuTTY session, to install the command-line tools, type the following command, and then press Enter:
    ```
    sudo apt-get install mssql-tools unixodbc-dev
    ```

4. In the PuTTY session, type **y**, and then press Enter.

5. In the **Configuring mssql-tools** window, press the left arrow, and then press Enter.

6. In the **Configuring msodbcsql** window, press the left arrow, and then press Enter.

7. Wait until the installation is complete.

8. In the PuTTY session, to add the tools to the **PATH** environment variable, type the following commands, and then press Enter:
    ```
    echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bash_profile
    echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc
    source ~/.bashrc
    ```

9. In the PuTTY session, type the following command, and then press Enter:
    ```
    bcp /?
    ```

    If help information for the **bcp** tool is displayed, the command-line
    tools have been successfully installed. Do not exit PuTTY.

#### Create a database

1. In the PuTTY session, to connect to the SQL Server and authenticate, type the following command, and then press Enter:
    ```
    sqlcmd -S localhost -U SA -P 'Pa55w.rd'
    ```

2. In the PuTTY session, to create a new database, type the following commands, and then press Enter:
    ```
    CREATE DATABASE DemoDB1
    GO
    ```

3. In the PuTTY session, to check the available databases, type the following Transact-SQL commands, and then press Enter:
    ```
    SELECT Name FROM sys.Databases
    GO
    ```

4. In the PuTTY session, to create a new table in the DemoDB1 database, type the following Transact-SQL commands, and then press Enter:
    ```
    USE DemoDB1
    CREATE TABLE Products (id INT, Name NVARCHAR(50), Price DECIMAL(10, 2))
    GO
    ```

5. In the PuTTY session, to add a record to the Products table, type the following Transact-SQL commands, and then press Enter:
    ```
    INSERT INTO Products VALUES(1, 'Gizmo', 500.00);
    GO
    ```

6. In the PuTTY session, to query the new database, type the following commands, and then press Enter:
    ```
    SELECT * FROM Products
    GO
    ```

7. In the PuTTY session, type **quit**, and then press Enter.

8. In the PuTTY session, type **exit**, and then press Enter.

#### Delete the virtual machine

1. In Internet Explorer, on the left blade of the Azure portal, click **Resource groups**.

2. On the **Resource groups** blade, click **10999A**.

3. On the **10999A blade,** click **Delete resource group**.

4. On the **Are you sure you want to delete"10999A"?** blade, type **10999A**, and then click **Delete**. This deletes the virtual machines.

5. If there is a **NetworkWatcherRG** resource group present, repeat steps 2 to 4 to delete.

6. Close all open programs and windows.

---

## Lesson 4: Deploying SQL Server containers on Docker

### Demo 1: Pulling and running a SQL Server 2017 Docker image

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
    sudo docker pull microsoft/mssql-server-linux:latest
    ```

2. Wait until the download is complete.

3. In the PuTTY session, to check that the image exists, type the following command, and then press Enter:
    ```
    sudo docker images
    ```

#### Run a Docker container

1. In the PuTTY session, type the following command, and then press Enter:
    ```
    sudo docker run -e 'ACCEPT_EULA=Y' -e 'MSSQL_SA_PASSWORD=Pa55w.rd' --name mssql-latest -h mssql-latest -p 14333:1433 -d microsoft/mssql-server-linux:latest
    ```

2. In the PuTTY session , to check that the container is running, type the following command, and then press Enter:
    ```
    sudo docker ps -a
    ```

#### Create a database

1. In the PuTTY session, to start an interactive bash session inside the SQL Server 2017 container, type the following command, and then press Enter:
    ```
    sudo docker exec -it mssql-latest bash
    ```

2. In the PuTTY session, to start the **sqlcmd** tool, type the following command, and then press Enter:
    ```
    /opt/mssql-tools/bin/sqlcmd -S localhost -U sa
    ```

3. When you are prompted for a password, type **Pa55w.rd**, and then press Enter.

4. In the PuTTY session, to check the available databases, type the following Transact-SQL commands, and then press Enter:
    ```
    SELECT Name FROM sys.Databases
    GO
    ```

5. In the PuTTY session, to create a new database called **AdWorksDemo**, type the following commands, and then press Enter:
    ```
    CREATE DATABASE AdWorksDemo
    GO
    ```

6. In the PuTTY session, to check the available databases again, type the following commands, and then press Enter:
    ```
    SELECT Name FROM sys.Databases
    GO
    ```

    The **AdWorksDemo** database is shown in the list.

7. In the PuTTY session, type **quit**, and then press Enter.

8. In the PuTTY session, type **exit**, and then press Enter.

9. In the PuTTY session, type **exit**, and then press Enter.

#### Delete the virtual machine

1. In Internet Explorer, on the left blade of the Azure portal, click **Resource groups**.

2. On the **Resource groups** blade, click **10999A**.

3. On the **10999A blade,** click **Delete resource group**.

4. On the **Are you sure you want to delete"10999A"?** blade, type **10999A**, and then click **Delete**. This deletes the virtual machines.

5. If there is a **NetworkWatcherRG** resource group present, repeat steps 2 to 4 to delete.

6. Close all open programs and windows.
