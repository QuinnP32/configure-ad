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

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (21H2)
## High-Level Deployment Steps ️

These steps provide a roadmap for deploying Active Directory within Azure Virtual Machines.

1.  <a href="#section1">**Install Active Directory Domain Services:**</a><a href="#section1"></a>
2.  <a href="#section2">**Promote Server to Domain Controller**</a>
3.  <a href="#section3">**Create Domain Admin User and OUs**</a>
4.  <a href="#section4">**Join Client Machine to Domain, Configure Remote Desktop**</a>
5. ‍‍ <a href="#section5">(Optional) **Create Additional Users with Script**</a>  

<h2>Detailed Deployment and Configuration Steps</h2>

Here's a breakdown of each step involved in the deployment process:

<section id="section1">
<h3>Step 1: Install Active Directory Domain Services</h3>

1. Access your Azure VM (DC-1) using Remote Desktop.
2. Open Server Manager and install the **Active Directory Domain Services** role.
</section>
<section id="section2">
<h3>Step 2: Promote Server to Domain Controller</h3>


1. Within Server Manager, select **Promote this server to domain controller**.
2. Choose **Add a new forest** and enter your desired domain name (e.g., `mydomain.com`).
3. Set a strong password for the Directory Services Restore Mode (DSRM) account.
4. Review the configuration summary and click **Next** to proceed.
5. The server will restart. Log back in using the newly created domain name (e.g., `mydomain.com\labuser`).
</section>
<section id="section3">
<h3>Step 3: Create Domain Admin User and OUs</h3>


1. Launch **Active Directory Users and Computers (ADUC)**.
2. Create two Organizational Units (OUs):
    * `_EMPLOYEES` for user accounts.
    * `_ADMINS` for administrative accounts.
3. Create a new user named "Jane Doe" with the username `jane_admin` and a strong password (replace `Cyberlab123!` with a strong password).
4. Add "jane_admin" to the **Domain Admins** security group.
5. Log out and log back in as `mydomain.com\jane_admin`.
</section
<section id="section4">
<h3>Step 4: Join Client Machine to Domain, Configure Remote Desktop</h3>

1. Log in to Client-1 with the local administrator account (e.g., `labuser`).
2. Join Client-1 to the domain named `mydomain.com` (replace with your chosen name).
3. Verify Client-1 shows up in ADUC on the Domain Controller.
4. Create a new OU named `_CLIENTS` and move Client-1 into it.
5. Log in to the Domain Controller as `mydomain.com\jane_admin`.
6. Open System Properties on the Domain Controller.
7. Go to the **Remote Desktop** tab and allow **Domain Users** access.
</section>
<section id="section5">
### Step 5: Create Additional Users with Script (Optional) ‍‍
</section>
1. On the Domain Controller, open PowerShell ISE as an administrator.
2. Create a new file and paste the following script content (replace placeholders with your desired values):

```powershell
# ... (Paste your PowerShell script here)

<b>PowerShell</b>

$number_of_users = 10  # Adjust the number of users to create
$ou_name = "_EMPLOYEES"  # Change OU name if needed
$base_username = "user"
$domain_name = "mydomain.com"  # Replace with your domain name
$starting_number = 1

for ($i = 0; $i -lt $number_of_users; $i++) {
  $username = "$base_username" + ($starting_number + $i)
  $password = ConvertTo-SecureString "Cyberlab123!" -AsPlainText -Force
  $new_user = New-ADUser -Name $username -SamAccountName $username -Password $password -Path "OU=$ou_name,$domain_name"
  if ($new_user) {
    Write-Host "User '$username' created successfully."
  } else {
    Write-Error "Failed to create user '$username'."
  }
}

Use code with caution.

    Run the script and observe the accounts being created.
    Open ADUC and verify the accounts are present in the "_EMPLOYEES" OU.
    Attempt to log into Client-1 with one of the user accounts (using the password "Cyberlab123!" or the password defined in the script).
