<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

# On-premises Active Directory Deployed in the Cloud (Azure)
Active Directory centrally manages thousands of user accounts in a single place (accounts, passwords and permissions) as well as manage devices on a large scale.
This tutorial outlines the implementation of on-premises Active Directory within Azure Virtual Machines.<br />

## Environments and Technologies Used

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

## Operating Systems Used

- Windows Server 2022
- Windows 10 (21H2)

## High-Level Deployment and Configuration Steps

- Domain Controller VM (Windows Server 2022) named “DC-1”
- Domain Controller’s NIC Private IP address to be static
- ICMPv4 (ping) was allowed on the Domain Controller
- Create an Admin and Normal User Account in Active Directory
- Join Client to domain
- Attempt to login Client-1 with one of the users

| Terms | Descriptions|
|-------| ------------|
| Resource Group | A Resource Group is a logical container in Microsoft Azure that helps you organize and manage related Azure resources. It allows you to group together resources that belong to a specific solution, project, or application. By associating resources with a resource group, you can easily manage, monitor, and apply consistent settings and permissions to those resources collectively.
| Virtual Machine | A Virtual Machine (VM) is a software emulation of a physical computer, running an operating system (OS) and applications. In the context of Azure, a virtual machine refers to a computing resource that you can provision in the Azure cloud. It provides the flexibility of a virtualized environment, allowing you to create and manage virtual machines with different configurations, such as the choice of OS, processing power, memory, storage, and networking.
| Remote Desktop | Remote Desktop is a technology that enables you to access and control a computer or virtual machine from a remote location. It allows you to establish a remote connection to the desktop environment of another computer over a network connection. With Remote Desktop, you can interact with the remote computer as if you were physically present at its location, accessing files, running applications, and performing administrative tasks.
| Active Directory Domain Services | Active Directory Domain Services (AD DS) is a Windows Server-based directory service that stores and manages information about network resources. It provides authentication and authorization services within a domain-based network infrastructure. AD DS enables centralized management of users, groups, computers, and other network objects, facilitating secure access and resource management across an organization's network.
| Powershell |  is a cross-platform task automation solution made up of a command-line shell, a scripting language, and a configuration management framework. PowerShell runs on Windows, Linux, and macOS.
| Domain Controller | A Domain Controller (DC) is a server in a Windows Server-based network that authenticates users and enforces security policies within an Active Directory domain. It hosts a copy of the Active Directory database and provides services such as user authentication, authorization, and the replication of directory data. Domain Controllers are responsible for managing domain-specific operations and enabling communication between client devices and the Active Directory infrastructure.
|Join Client to Domain | Joining a client computer to a domain refers to the process of establishing a trust relationship between the client device and an Active Directory domain. When a client joins a domain, it becomes a member of that domain and can leverage domain-based authentication, security policies, and access to shared network resources. By joining a client to a domain, users can log in with their domain credentials and access resources within the domain, subject to the permissions and policies defined by the domain administrator. |

## Deployment and Configuration Steps

Firstly, we will need to establish the resource group so that you can add your virtual machines for the Domain Controller (DC-1) and the Client Virtual Machine (Client-1). The Domain Controller VM will use Windows Server 2022 system image (a serialized copy of the entire state of a computer system stored in some non-volatile form such as a file). 

<p align="center">
<img src="https://i.imgur.com/lKmRcIy.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
The Client VM (Windows 10) named “Client-1” was created with the same Resource Group and Vnet that was created in DC-1. 
<p align="center">
<img src="https://i.imgur.com/w34Z93S.png" height="80%" width="80%" alt="client 1 vm settings"/>
</p>
Private IP address set to static (static IP addresses are necessary for devices that need constant access.)
<p align="center">
<img src="https://i.imgur.com/n3KceWF.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<br />

Second, check for connection between the client device and domain controller by logging into Client-1 with Remote Desktop Connection (RDP) and pinging DC-1’s private IP address using ping -t (perpetual ping). ICMPv4 (ping) was allowed on the Domain Controller's (DC-1) Firewall in Windows Firewall (Core Networking Diagnostics - ICMP Echo Request (ICMPv4-In)). After logging back into Client-1 check to make sure the ping is successful.

<p align="center">
<img src="https://i.imgur.com/FWLTP8X.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>

Pictured below displays that the icmp rule has been allowed on the windows firewall for inbound traffic:
<p align="center">
<img src="https://i.imgur.com/f4i0pdh.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>  
</p>
<br />  
While in DC-1, we've selected to 'add roles and features' to enable Active Directory Domain Services. Promoted as a Domain Controller (DC): a new forest as mydomain.com setup. Remote Desktop was Restarted and logged back into DC-1 as user: mydomain.com\labuser.

<p align="center">
<img src="https://i.imgur.com/ipCHp9t.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>

<p align="center">
  <img src="https://i.imgur.com/Ccbt7ak.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<br />

Next, we configure the organizational units for the admins and employees in Active Directory (AD) while continuing to be in DC-1 (Remote Desktop Connection).  The accounts can now be viewed in Active Directory in the appropriate organizational unit. In the Active Directory, right click on your <b>domain name</b> and move your mouse to hover <b>new</b>--><b>Organizational Unit</b> and left click to create folders for your AD. We will create employees, admins, and security groups.

<p align="center">
<img src="https://i.imgur.com/D59IbY9.png" height="80%" width="80%" alt="Active Directory OU"/>
  </p>
</br>
Create a new OU named '_ADMINS' --> Create a new employee named <b>Karen What</b> (same password) with the username of <b>'karen_admin'</b>. Once the admin is created, add "karen_admin" to the "domain admins" security group.

<p align="center">
<img src="https://i.imgur.com/Qzlpsyk.png" height="80%" width="80%" alt="Add user to domain admins"/>
  </p>
  </br>
Log out and close the connection to dc-1 for current user(mydomain.com\labuser) and log back in as "mydomain.com\karen_admin".
<p align="center">
<img src="https://i.imgur.com/WiQI5sG.png" height="80%" width="80%" alt="cmd displays new loggin user"/>
 </p>
  </br>
  
Next, we'll join Client-1 to the domain< (mydomain.com); however, we must change the DNS on Client-1 to the private IP address of DC-1 so that we can properly add client-1 to the domain. Here we will select the NIC on client-1 to change the dns to the private IP address of DC-1
 
  <p align="center">
    <img src="https://i.imgur.com/f2Vka1D.png" height="80%" width="80%" alt="select network inferface client 1"/> </p>
    </br>
    Select 'DNS Servers'
      <p align="center">
    <img src="https://i.imgur.com/BlO0rjn.png" height="80%" width="80%" alt="select network inferface client 1"/> </p>
    </br>
    Select 'Custom' radio button for DNS server so that you can now enter the DC-1 private IP address.
  <p align="center">
  <img src="https://i.imgur.com/Rcjik7d.png" height="80%" width="80%" alt="select customer dns"/></p>
  </br>
  Now that we have successfully changed the DNS server to the private IP address of DC-1, we can add client-1 to the domain without error. You will receive a message letting you know that the client has been successfully added to the domain. This can be done by going to System > Rename This PC > enter domain name > select OK > select Apply. The update this then require a system restart.  
  <p align="center">
    <img src="https://i.imgur.com/v2B6jza.png" height="80%" width="80%" alt="add to domain"/></p>
    </br>
    Message displays that client has been successfully add to the domain
    <p align="center">
  <img src="https://i.imgur.com/awUUK52.png" height="80%" width="80%" alt="message displays client added to domain"/>
  </p>
  Now, we can create our users that will be loaded into our <b>_EMPLOYEES OU</b> in the domain controller (DC-1). To create these employees we will run <b>PowerShell_ISE</b> as an <b>administrator</b>. A new File will be created then we can enter the pre-configured script into the file. When the script is run, the random employees will be created.
  
<details>  
  <summary> <h6>pre-configured Powershell Script</h6> </summary>
  
  
```powershell
# ----- Edit these Variables for your own Use Case ----- #
$PASSWORD_FOR_USERS   = "Password1"
$NUMBER_OF_ACCOUNTS_TO_CREATE = 10000
# ------------------------------------------------------ #

Function generate-random-name() {
    $consonants = @('b','c','d','f','g','h','j','k','l','m','n','p','q','r','s','t','v','w','x','z')
    $vowels = @('a','e','i','o','u','y')
    $nameLength = Get-Random -Minimum 3 -Maximum 7
    $count = 0
    $name = ""

    while ($count -lt $nameLength) {
        if ($($count % 2) -eq 0) {
            $name += $consonants[$(Get-Random -Minimum 0 -Maximum $($consonants.Count - 1))]
        }
        else {
            $name += $vowels[$(Get-Random -Minimum 0 -Maximum $($vowels.Count - 1))]
        }
        $count++
    }

    return $name

}

$count = 1
while ($count -lt $NUMBER_OF_ACCOUNTS_TO_CREATE) {
    $fisrtName = generate-random-name
    $lastName = generate-random-name
    $username = $fisrtName + '.' + $lastName
    $password = ConvertTo-SecureString $PASSWORD_FOR_USERS -AsPlainText -Force

    Write-Host "Creating user: $($username)" -BackgroundColor Black -ForegroundColor Cyan
    
    New-AdUser -AccountPassword $password `
               -GivenName $firstName `
               -Surname $lastName `
               -DisplayName $username `
               -Name $username `
               -EmployeeID $username `
               -PasswordNeverExpires $true `
               -Path "ou=_EMPLOYEES,$(([ADSI]`"").distinguishedName)" `
               -Enabled $true
    $count++
}
```
  
</details>

  Here is the script loaded into powershell prior to running the script to create 1000 random users
  <p align="center">
  <img src="https://i.imgur.com/ez4THWm.png" height="80%" width="80%" alt="powershell with script loaded"/>
  </p>
  </br>
  Random users are created now after choosing to execute the code. Here we can now see the script loading the 1000 users:
<p align="center">    
  <img src="https://i.imgur.com/f2vKx8Y.png" height="80%" width="80%" alt="powershell execute code"/> </p>
  Those random Users are now reflected in Active Directory on the Domain Controller
  <p align="center">
  <img src="https://i.imgur.com/lHBM2nh.png" height="80%" width="80%" alt="active directory shows created users"/>
  </p>
  Attempt to login on Client-1 with a random user that has been created
  <p align="center">
  <img src="https://i.imgur.com/HFguOhB.png" height="80%" width="80%" alt="windows start menu shows login user"/>
  </p>
  
