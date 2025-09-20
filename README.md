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
<img width="80% height="80%" alt="ActiveDirectoryDeploy1" src="https://github.com/user-attachments/assets/461f3f09-79d1-408d-b115-f7b8a1d438c3" />

</p>

2. **Create a Virtual Network and Subnet**:
   - Set up a Virtual Network with a subnet to host your VMs.

<p>
<img width="80%" height="80%" alt="ActiveDirectoryDeploy2" src="https://github.com/user-attachments/assets/9f969d49-ab13-4384-97fb-6fb89cdf0300" />
</p>

3. **Create the Domain Controller VM (Windows Server 2022)**:
   - Name the VM: `DC-1`.
   - Ensure that the VM is on the Virtual Network created previously.

<p>
<img width="80%" height="80%" alt="ActiveDirectoryDeploy3" src="https://github.com/user-attachments/assets/f36e26a8-8e2b-4803-8b6e-158720a1e3ba" />
</p>

<p>
<img width="80%" height="80%" alt="ActiveDirectoryDeploy4" src="https://github.com/user-attachments/assets/90698e37-dbf5-43c6-916d-b68597092d16" />
</p>

<p>
<img width="80%" height="80%" alt="ActiveDirectoryDeploy5" src="https://github.com/user-attachments/assets/77345266-22f2-4cf3-90ca-a17b7a3fefd9" />
</p>

<p>
<img width="80%" height="80%" alt="ActiveDirectoryDeploy6" src="https://github.com/user-attachments/assets/d8176a49-8ac9-4838-9d32-0268bc5fca90" />
</p>

4. **Set Static Private IP for DC-1**:
   - After the VM is created, navigate to its Network Interface Card (NIC) settings and set the private IP to static.

<p>
<img width="80%" height="80%" alt="ActiveDirectoryDeploy7" src="https://github.com/user-attachments/assets/c6f3f62e-5f41-4147-a642-6b4e090c69f4" />

</p>

5. **Disable Windows Firewall**:
   - Log in to `DC-1` and disable the Windows Firewall for testing connectivity.

<p>
<img width="80%" height="80%" alt="ActiveDirectoryDeploy8" src="https://github.com/user-attachments/assets/618ee235-be66-4d80-8937-a9793982120e" />

</p>

#### **Setup Client-1 in Azure**
1. **Create the Client VM (Windows 10 22H2)**:
   - Name the VM: `Client-1`.

<p>
<img width="1940" height="1138" alt="ActiveDirectoryDeploy9" src="https://github.com/user-attachments/assets/5b1407de-955b-4451-b2a3-7e98a315f028" />

</p>

<p>
<img width="1874" height="674" alt="ActiveDirectoryDeploy10" src="https://github.com/user-attachments/assets/c6f00598-1df3-4721-abec-8025f9025874" />

</p>

<p>
<img width="1818" height="348" alt="ActiveDirectoryDeploy11" src="https://github.com/user-attachments/assets/ed2b973b-cb65-415d-91e5-20a9f40b2db4" />

</p>

2. **Attach Client-1 to the Same Region and Virtual Network**:
   - Ensure it is in the same Virtual Network and subnet as `DC-1`.

<p>
<img width="1836" height="1238" alt="ActiveDirectoryDeploy12" src="https://github.com/user-attachments/assets/3d2d5dbe-4da2-48ef-b942-4e409269c899" />

</p>

3. **Set DNS Settings**:
   - Update `Client-1`'s DNS settings to point to `DC-1`'s private IP address.

<p>
<img width="1788" height="1148" alt="ActiveDirectoryDeploy13" src="https://github.com/user-attachments/assets/d60f4d22-17c3-4afb-95e4-98f3461f8feb" />

</p>

4. **Test Connectivity**:
   - Restart `Client-1` from the Azure Portal.
   - Log into `Client-1` and use the `ping` command to test connectivity with `DC-1`.

<p>
<img width="1088" height="590" alt="ActiveDirectoryDeploy14" src="https://github.com/user-attachments/assets/081d17f1-30f5-42d2-8b93-3ade858725e7" />

</p>

5. **Verify DNS Settings**:
   - Run `ipconfig /all` in PowerShell on `Client-1` to ensure the DNS points to `DC-1`.

<p>
<img width="1278" height="856" alt="ActiveDirectoryDeploy15" src="https://github.com/user-attachments/assets/382cc6ca-4e42-4dcd-bcc2-f53dbdf57b9d" />

</p>

---

### Part 2: Deploying Active Directory

#### **Install Active Directory**
1. Log in to `DC-1`.
2. Install Active Directory Domain Services (AD DS).
3. Promote `DC-1` as a Domain Controller and set up a new forest (e.g., `mydomain.com`).
4. Restart `DC-1` and log in as `mydomain.com\labuser`.

<p>
<img width="2852" height="1272" alt="ActiveDirectoryDeploy16" src="https://github.com/user-attachments/assets/415adde0-fc5c-48ee-936e-90db7952542c" />

</p>

<p>
<img width="1496" height="1104" alt="ActiveDirectoryDeploy17" src="https://github.com/user-attachments/assets/5ceddb85-4633-474d-9d18-b031208686da" />

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
<img width="1496" height="1008" alt="ActiveDirectoryDeploy18" src="https://github.com/user-attachments/assets/453f4db9-1904-42ec-a677-c115d3ef9c4a" />

</p>

<p>
<img width="1336" height="898" alt="ActiveDirectoryDeploy19" src="https://github.com/user-attachments/assets/be7ec31f-d5cd-4da3-8a1b-0ca0132ed9ea" />

</p>

<p>
<img width="1324" height="1100" alt="ActiveDirectoryDeploy20" src="https://github.com/user-attachments/assets/426567d5-d769-4ee6-9acd-1ff2dd172ea4" />

</p>

#### **Join Client-1 to the Domain**
1. Log in as the local admin and join `Client-1` to the domain.
2. Create a new OU titled '_CLIENTS' & add `Client-1` in ADUC to `_CLIENTS`.

<p>
<img width="1798" height="1126" alt="ActiveDirectoryDeploy21" src="https://github.com/user-attachments/assets/e46738d4-ee5a-475f-891e-5878dcb2bf6a" />

</p>

<p>
<img width="1484" height="738" alt="ActiveDirectoryDeploy22" src="https://github.com/user-attachments/assets/f03e0811-4be5-427e-ac96-dba2b73263cd" />

</p>

---

### Part 3: Creating Users with PowerShell

#### **Setup Remote Desktop for Domain Users**
1. Log into `Client-1` as `mydomain.com\jane_admin`.
2. Open System Properties and enable Remote Desktop.
3. Allow "domain users" access to Remote Desktop.

<p>
<img width="1638" height="1170" alt="ActiveDirectoryDeploy23" src="https://github.com/user-attachments/assets/37444fd7-0b1e-4d86-ac1f-263f3451d1be" />

</p>

#### **Create Users with PowerShell**
1. Log in to `DC-1` as `jane_admin`.
2. Open PowerShell ISE as an administrator.
3. Create multiple new users by using this [script](https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1).
4. Verify users appear in the `_EMPLOYEES` OU in ADUC.
5. Attempt to log into `Client-1` with one of the created accounts.

<p>
<img width="1850" height="1362" alt="ActiveDirectoryDeploy24" src="https://github.com/user-attachments/assets/fbe0b528-efc3-4082-a36d-2345e9e9d489" />

</p>

<p>
<img width="1486" height="1046" alt="ActiveDirectoryDeploy25" src="https://github.com/user-attachments/assets/1023acbd-5af5-4fc7-9c03-01b967415ea2" />

</p>

<p>
<img width="1442" height="810" alt="ActiveDirectoryDeploy26" src="https://github.com/user-attachments/assets/ceaf8c74-3193-4b6d-876f-1caa27ebb18c" />

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
<img width="1708" height="1036" alt="ActiveDirectoryDeploy27" src="https://github.com/user-attachments/assets/40457622-f4dc-48a8-aaea-ebee66e5146b" />

</p>

<p>
<img width="1068" height="688" alt="ActiveDirectoryDeploy28" src="https://github.com/user-attachments/assets/ce0ba32e-81c6-4fba-a97d-338d4b5fde26" />

</p>

<p>
<img width="1502" height="1224" alt="ActiveDirectoryDeploy29" src="https://github.com/user-attachments/assets/a3367169-144a-4861-acca-d4661b239169" />

</p>

#### **Enable and Disable Accounts**
1. Disable a user account in ADUC.
2. Attempt to log in with the disabled account and observe the error message.
3. Re-enable the account and log in successfully.

<p>
<img width="1486" height="1026" alt="ActiveDirectoryDeploy30" src="https://github.com/user-attachments/assets/19c02359-5cd6-4177-80b1-eb62509e98c4" />

</p>

<p>
<img width="1080" height="600" alt="ActiveDirectoryDeploy31" src="https://github.com/user-attachments/assets/44e4c17a-19fd-4bd4-a109-467ff61d5d28" />

</p>

<p>
<img width="1546" height="904" alt="ActiveDirectoryDeploy32" src="https://github.com/user-attachments/assets/2bb1f3a8-1ca8-40af-ba44-43c8fd889fa3" />

</p>

#### **Observing Logs**
1. Review authentication and account-related logs in Event Viewer:
   - Log on `DC-1` for domain-level events (shown below).
   - Log on `Client-1` for local events.

<p>
<img width="1888" height="1106" alt="ActiveDirectoryDeploy33" src="https://github.com/user-attachments/assets/5518b255-4ea1-40b2-a5e2-6b3733b17bab" />

</p>

---

### Completion

Congratulations! You have successfully deployed and configured an on-premises Active Directory environment in Azure.
