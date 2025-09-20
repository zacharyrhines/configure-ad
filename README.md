<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>On-premises Active Directory Deployed in the Cloud (Azure)</h1>
This tutorial outlines the implementation of on-premises Active Directory within Azure Virtual Machines.<br />

<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

<h2>Operating Systems Used</h2>

- Windows Server 2022
- Windows 10 (22H2)

<h2>High-Level Deployment and Configuration Steps</h2>

- Preparing the AD Infrastructure in Azure
- Deploying Active Directory
- Creating Users with PowerShell
- Group Policy and Managing Accounts

---

<h2>Deployment and Configuration Steps</h2>

### Part 1: Preparing the AD Infrastructure in Azure

#### **Setup Domain Controller in Azure**
1. **Create a Resource Group**:
   - Navigate to the Azure Portal and create a new Resource Group for the lab environment.

<p>
<img src="https://imgur.com/6xuYUly.png" height="80%" width="80%" alt="Lab 5"/>
</p>

2. **Create a Virtual Network and Subnet**:
   - Set up a Virtual Network with a subnet to host your VMs.

<p>
<img src="https://imgur.com/QYzlDYu.png" height="80%" width="80%" alt="Lab 5"/>
</p>

3. **Create the Domain Controller VM (Windows Server 2022)**:
   - Name the VM: `DC-1`.
   - Ensure that the VM is on the Virtual Network created previously.

<p>
<img src="https://imgur.com/hsOZVMG.png" height="80%" width="80%" alt="Lab 5"/>
</p>

<p>
<img src="https://imgur.com/eNzhq07.png" height="80%" width="80%" alt="Lab 5"/>
</p>

<p>
<img src="https://imgur.com/Lh5JOHT.png" height="80%" width="80%" alt="Lab 5"/>
</p>

<p>
<img src="https://imgur.com/XFSp1Gs.png" height="80%" width="80%" alt="Lab 5"/>
</p>

4. **Set Static Private IP for DC-1**:
   - After the VM is created, navigate to its Network Interface Card (NIC) settings and set the private IP to static.

<p>
<img src="https://imgur.com/dh1s1m1.png" height="80%" width="80%" alt="Lab 5"/>
</p>

5. **Disable Windows Firewall**:
   - Log in to `DC-1` and disable the Windows Firewall for testing connectivity.

<p>
<img src="https://imgur.com/AVmEK6s.png" height="80%" width="80%" alt="Lab 5"/>
</p>

#### **Setup Client-1 in Azure**
1. **Create the Client VM (Windows 10 22H2)**:
   - Name the VM: `Client-1`.

<p>
<img src="https://imgur.com/CESOqTD.png" height="80%" width="80%" alt="Lab 5"/>
</p>

<p>
<img src="https://imgur.com/Hi9URhe.png" height="80%" width="80%" alt="Lab 5"/>
</p>

<p>
<img src="https://imgur.com/t0IoRUj.png" height="80%" width="80%" alt="Lab 5"/>
</p>

2. **Attach Client-1 to the Same Region and Virtual Network**:
   - Ensure it is in the same Virtual Network and subnet as `DC-1`.

<p>
<img src="https://imgur.com/XEzfBI4.png" height="80%" width="80%" alt="Lab 5"/>
</p>

3. **Set DNS Settings**:
   - Update `Client-1`'s DNS settings to point to `DC-1`'s private IP address.

<p>
<img src="https://imgur.com/KzlkBSO.png" height="80%" width="80%" alt="Lab 5"/>
</p>

4. **Test Connectivity**:
   - Restart `Client-1` from the Azure Portal.
   - Log into `Client-1` and use the `ping` command to test connectivity with `DC-1`.

<p>
<img src="https://imgur.com/xqj9ERd.png" height="80%" width="80%" alt="Lab 5"/>
</p>

5. **Verify DNS Settings**:
   - Run `ipconfig /all` in PowerShell on `Client-1` to ensure the DNS points to `DC-1`.

<p>
<img src="https://imgur.com/vYVLEne.png" height="80%" width="80%" alt="Lab 5"/>
</p>

---

### Part 2: Deploying Active Directory

#### **Install Active Directory**
1. Log in to `DC-1`.
2. Install Active Directory Domain Services (AD DS).
3. Promote `DC-1` as a Domain Controller and set up a new forest (e.g., `mydomain.com`).
4. Restart `DC-1` and log in as `mydomain.com\labuser`.

<p>
<img src="https://imgur.com/AZSsDnK.png" height="80%" width="80%" alt="Lab 5"/>
</p>

<p>
<img src="https://imgur.com/t4epPKK.png" height="80%" width="80%" alt="Lab 5"/>
</p>

#### **Create a Domain Admin User**
1. Open Active Directory Users and Computers (ADUC).
2. Create an Organizational Unit (OU) named `_EMPLOYEES`.
3. Create another OU named `_ADMINS`.
4. Add a new user:
   - Name: `Jane Doe`
   - Username: `jane_admin`
5. Add `jane_admin` to the `Domain Admins` security group.
6. Log out and log back in as `mydomain.com\jane_admin`.

<p>
<img src="https://imgur.com/Hmh13Iu.png" height="80%" width="80%" alt="Lab 5"/>
</p>

<p>
<img src="https://imgur.com/4WijGrh.png" height="80%" width="80%" alt="Lab 5"/>
</p>

<p>
<img src="https://imgur.com/pTUrCWZ.png" height="80%" width="80%" alt="Lab 5"/>
</p>

#### **Join Client-1 to the Domain**
1. Log in as the local admin and join `Client-1` to the domain.
2. Create a new OU titled '_CLIENTS' & add `Client-1` in ADUC to `_CLIENTS`.

<p>
<img src="https://imgur.com/YJ5n2WA.png" height="80%" width="80%" alt="Lab 5"/>
</p>

<p>
<img src="https://imgur.com/9FmZOUF.png" height="80%" width="80%" alt="Lab 5"/>
</p>

---

### Part 3: Creating Users with PowerShell

#### **Setup Remote Desktop for Domain Users**
1. Log into `Client-1` as `mydomain.com\jane_admin`.
2. Open System Properties and enable Remote Desktop.
3. Allow "domain users" access to Remote Desktop.

<p>
<img src="https://imgur.com/dNaJHfs.png" height="80%" width="80%" alt="Lab 5"/>
</p>

#### **Create Users with PowerShell**
1. Log in to `DC-1` as `jane_admin`.
2. Open PowerShell ISE as an administrator.
3. Create multiple new users by using this [script](https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1).
4. Verify users appear in the `_EMPLOYEES` OU in ADUC.
5. Attempt to log into `Client-1` with one of the created accounts.

<p>
<img src="https://imgur.com/m7djerT.png" height="80%" width="80%" alt="Lab 5"/>
</p>

<p>
<img src="https://imgur.com/QrlPn5I.png" height="80%" width="80%" alt="Lab 5"/>
</p>

<p>
<img src="https://imgur.com/1HzXcl2.png" height="80%" width="80%" alt="Lab 5"/>
</p>

---

### Part 4: Group Policy and Managing Accounts

#### **Account Lockout Configuration**
1. Log in to `DC-1`.
2. Open Group Policy Management.
3. Edit the Default Domain Policy:
   - Set account lockout threshold to 5 invalid attempts.
4. Attempt to log in with a user account using incorrect passwords. Observe the account lockout behavior.
5. Unlock the account in ADUC and reset the password.

<p>
<img src="https://imgur.com/3kkDVkm.png" height="80%" width="80%" alt="Lab 5"/>
</p>

<p>
<img src="https://imgur.com/qgCHNt7.png" height="80%" width="80%" alt="Lab 5"/>
</p>

<p>
<img src="https://imgur.com/szfMfXp.png" height="80%" width="80%" alt="Lab 5"/>
</p>

#### **Enable and Disable Accounts**
1. Disable a user account in ADUC.
2. Attempt to log in with the disabled account and observe the error message.
3. Re-enable the account and log in successfully.

<p>
<img src="https://imgur.com/eeSuxFO.png" height="80%" width="80%" alt="Lab 5"/>
</p>

<p>
<img src="https://imgur.com/rlpBjP9.png" height="80%" width="80%" alt="Lab 5"/>
</p>

<p>
<img src="https://imgur.com/dKHctiJ.png" height="80%" width="80%" alt="Lab 5"/>
</p>

#### **Observing Logs**
1. Review authentication and account-related logs in Event Viewer:
   - Log on `DC-1` for domain-level events (shown below).
   - Log on `Client-1` for local events.

<p>
<img src="https://imgur.com/xgdjW70.png" height="80%" width="80%" alt="Lab 5"/>
</p>

---

### Completion

Congratulations! You have successfully deployed and configured an on-premises Active Directory environment in Azure.
