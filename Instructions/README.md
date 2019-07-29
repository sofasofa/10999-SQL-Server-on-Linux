# 10999A: SQL Server on Linux

## Virtual Machine Setup

All students and the instructor must perform the following tasks prior to commencing the modules.

A Microsoft® Azure® account is required to run the demos and labs in this module.

Demos and labs in this module use Linux virtual machines in Azure. You connect to Linux machines using a remote SSH session. Linux and Mac OS X computers have a built-in SSH client interface but, if you plan to use a Windows client computer, you must install an SSH client such as PuTTY.

### Start the VMs

1. In Hyper-V Manager, under Virtual Machines, right-click **MT17B-WS2016-NAT**, and then click **Start**.
2. In Hyper-V Manager, under Virtual Machines, right-click **10999A-MIA-DC**, and then click **Start**.
3. In Hyper-V Manager, under Virtual Machines, right-click **10999A-MIA-SQL**, and then click **Start**.
4. Right-click **10999A-MIA-SQL**, and then click **Connect**.
5. Log in as **ADEVENTUREWORKS\Student** with the password **Pa55w.rd**.

### Install PuTTY on the LON-DEV VM

1. Start a web browser, go to **http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html**.
2. Under **Alternative binary files**, download **putty.exe**, and **pscp.exe**; saving it to a suitable folder in the **10999A-MIA-SQL** file system (for example, **C:\putty**).
3. Create a shortcut to **putty.exe** on your desktop.
4. Close Internet Explorer.
