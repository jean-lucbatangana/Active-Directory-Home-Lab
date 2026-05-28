# 🖥️ Active Directory + User Management Home Lab

## 📋 Project Overview

This project involves the design, deployment and full configuration of an **Active Directory (AD) environment** from scratch using **Windows Server 2022** running inside **Oracle VirtualBox**. The goal was to simulate a real enterprise IT environment — building a domain controller, structuring a company with departments and store branches, managing users and groups, and enforcing security policies using Group Policy.

This is a practical home lab project aimed at developing real-world skills used daily by IT Administrators, Help Desk Technicians and System Administrators.

---

## 🛠️ Technologies Used

| Technology | Purpose |
|---|---|
| Oracle VirtualBox | Virtualisation platform to host the VM |
| Windows Server 2022 | Server operating system |
| Active Directory Domain Services (AD DS) | Core directory service |
| Group Policy Management | Enforcing security and configuration policies |
| DNS Server | Domain name resolution within the network |
| Active Directory Users and Computers (ADUC) | Managing users, groups and OUs |

---

## 🧰 Prerequisites & Setup

### What You Need
- A PC or laptop with at least **8GB RAM**
- **Oracle VirtualBox** installed — [Download here](https://www.virtualbox.org/)
- **Windows Server 2022 ISO** — [Download free from Microsoft Evaluation Center](https://www.microsoft.com/en-us/evalcenter/evaluate-windows-server-2022)
  - ⚠️ Important: Download the **ISO** format, NOT the OEM package

### Virtual Machine Specifications Used
| Setting | Value |
|---|---|
| RAM | 2048 MB |
| CPU | 1 Core |
| Storage | 50 GB (Dynamically Allocated) |
| OS | Windows Server 2022 (64-bit) |
| Network | NAT (default) |

---

## 📁 Project Structure

```
mydomain.local (Domain)
├── HR
│   ├── Users (3 staff members)
│   └── HR Security Group
├── IT
│   ├── Users (3 staff members)
│   └── IT Security Group
├── Finance
│   ├── Users (3 staff members)
│   └── Finance Security Group
├── CEO & CFO
│   ├── Users
│   └── Executive Security Group
└── Store Teams
    ├── Cape Town Store
    │   ├── Users (2-3 staff members)
    │   └── Cape Town Store Staff (Security Group)
    └── Johannesburg Store
        ├── Users (2-3 staff members)
        └── Johannesburg Store Staff (Security Group)
```

---

## 🚀 Step-by-Step Implementation

### Step 1 — Installing Windows Server 2022

1. Created a new Virtual Machine in VirtualBox with the recommended specs
2. Attached the Windows Server 2022 ISO to the VM via IDE Controller
   > ⚠️ The ISO must be attached to an **IDE Controller** not SATA — otherwise VirtualBox cannot boot from it
3. Booted the VM and selected **"Windows Server 2022 Standard Evaluation (Desktop Experience)"** during installation
   > Desktop Experience gives you the full GUI — without it you only get a command line
4. Selected **Custom Installation** and installed to the unallocated virtual disk
5. Set a strong **Administrator password** meeting complexity requirements
6. Logged in and declined network discovery for security

**What I learned:** How to properly configure a VM, the difference between ISO and OEM formats, and the importance of selecting Desktop Experience for a usable server interface.

---

### Step 2 — Installing Active Directory Domain Services

1. Opened **Server Manager → Add Roles and Features**
2. Selected **Role-based or feature-based installation**
3. Checked **Active Directory Domain Services**
4. Added all required features when prompted
5. Completed the installation wizard

> **Why this step matters:** Installing AD DS adds the software and tools to the server but does not make it a domain controller yet. The server is still just a regular Windows Server at this point.

**What I learned:** The difference between installing a role and actually configuring it — two separate steps that beginners often confuse.

---

### Step 3 — Promoting the Server to a Domain Controller

1. Clicked **"Promote this server to a domain controller"** in Server Manager
2. Selected **"Add a new forest"** since this was a brand new domain
3. Set the **Root Domain Name** to `mydomain.local`
4. Set the **Forest and Domain Functional Level** to Windows Server 2016
5. Confirmed **DNS Server** and **Global Catalog** were enabled
6. Set the **DSRM (Directory Services Restore Mode) password** — an emergency recovery password
7. Allowed the wizard to complete and the server restarted automatically

> **Why "Add a new forest":** A forest is the top-level container of an entire Active Directory structure. Since there was no existing domain, a new forest was required. Adding to an existing forest would only be done when expanding an already-established domain.

> **Why DNS is critical:** DNS acts as the phonebook of the network. Without DNS, computers cannot locate the domain controller and nothing on the domain can communicate properly.

> **What the DSRM password is for:** This is a special emergency password used only if Active Directory breaks and needs to be repaired in recovery mode. It should be stored securely.

**What I learned:** The process of promoting a server, the role of DNS in Active Directory, what a forest and domain mean, and the purpose of DSRM.

---

### Step 4 — Creating Organizational Units (OUs)

Opened **Active Directory Users and Computers (ADUC)** via Tools in Server Manager and created the following OU structure:

| OU Name | Purpose |
|---|---|
| HR | Human Resources department staff |
| IT | Information Technology staff |
| Finance | Finance department staff |
| CEO & CFO | Executive leadership |
| Store Teams | Parent OU for all retail store locations |
| Store Teams → Cape Town Store | Staff at the Cape Town branch |
| Store Teams → Johannesburg Store | Staff at the Johannesburg branch |

**How to create an OU:**
> Right-click on the domain → New → Organizational Unit → Enter name → OK

**Why OUs matter:** In real companies users are never thrown into one flat list. OUs allow IT admins to:
- Organise users logically by department or location
- Apply different **Group Policies** to different departments
- Delegate administrative control per department
- Make it easier to manage large numbers of users

**What I learned:** How to design an OU hierarchy that mirrors a real company structure, including how to create nested sub-OUs for multi-branch organisations.

---

### Step 5 — Creating User Accounts

Created multiple user accounts inside each OU representing real employees across the company.

**How to create a user:**
> Right-click OU → New → User → Fill in First name, Last name, User logon name → Set password → Finish

**Password settings applied:**
- Password meets complexity requirements ✅
- User must change password at next logon ✅ *(industry standard — admin should never know the user's final password)*

**What I learned:** How to create user accounts the same way a real IT admin does when onboarding new employees, and why forcing a password change at first login is a security best practice.

---

### Step 6 — Creating Security Groups and Adding Members

Created a **Security Group** inside each OU and added all users from that OU as members.

**How to create a group:**
> Right-click OU → New → Group → Set name → Group scope: Global → Group type: Security → OK

**How to add members:**
> Double-click the group → Members tab → Add → Search for user → Check Names → OK

| Group Setting | Value Used | Why |
|---|---|---|
| Group Scope | Global | Can be used anywhere in the domain |
| Group Type | Security | Controls access and permissions (vs Distribution which is email only) |

**Why Security Groups matter:** Instead of assigning permissions to each user one by one, you assign permissions to the group. Every user inside the group automatically inherits those permissions. When someone changes departments you simply move them to a new group — one action instead of dozens.

**What I learned:** The difference between Security Groups and Distribution Groups, how group scope works, and why group-based permission management is essential in enterprise environments.

---

### Step 7 — Daily IT Admin Tasks

Practised the three most common tasks performed by IT administrators and help desk staff:

#### 🔑 Password Reset
> Right-click user → Reset Password → Enter new password → Check "User must change password at next logon" → OK

**Why this happens constantly:** Users regularly forget passwords, get locked out, or need their password reset after returning from leave.

#### 🔒 Disabling an Account
> Right-click user → Disable Account

**Why disable instead of delete:** When an employee leaves, their account is disabled immediately — not deleted. The data, emails and files remain intact for handover or legal purposes. Accounts are typically deleted after 30-90 days once confirmed no longer needed.

#### 🔓 Re-enabling an Account
> Right-click user → Enable Account

**What I learned:** Why account disabling is a critical security task (a former employee with active credentials is a serious security risk), and why companies preserve data before permanently removing accounts.

---

### Step 8 — Configuring Group Policy

Opened **Group Policy Management** via Tools in Server Manager and created two GPOs linked to the domain.

#### GPO 1 — Password Policy

> Group Policy Management → Right-click mydomain.local → Create a GPO → Name: "Password Policy" → Right-click → Edit → Computer Configuration → Policies → Windows Settings → Security Settings → Account Policies → Password Policy

| Setting | Value Set | Why |
|---|---|---|
| Enforce password history | 5 passwords | Prevents users reusing old passwords |
| Maximum password age | 90 days | Forces regular password changes |
| Minimum password age | 1 day | Prevents immediate reuse after change |
| Minimum password length | 8 characters | Short passwords are easily guessed |
| Password must meet complexity | Enabled | Forces uppercase, numbers and symbols |

#### GPO 2 — Account Lockout Policy

> Same path → Account Policies → Account Lockout Policy

| Setting | Value Set | Why |
|---|---|---|
| Account lockout threshold | 5 attempts | Locks after 5 wrong passwords |
| Account lockout duration | 30 minutes | How long account stays locked |
| Reset lockout counter after | 30 minutes | Resets failed attempt count |

**Why Account Lockout is critical:** Without this, attackers can try thousands of password combinations in a brute force attack. Locking the account after 5 attempts stops this completely and is a fundamental security control in every enterprise environment.

**What I learned:** How Group Policy works, how to navigate the Group Policy Management Editor, and how password and lockout policies protect an organisation from both internal and external threats.

---

## ✅ Skills Demonstrated

- Virtual machine setup and configuration
- Windows Server 2022 installation and administration
- Active Directory Domain Services installation and promotion
- Domain Controller configuration
- DNS Server role understanding
- Organisational Unit (OU) design and hierarchy
- User account creation and management
- Security Group creation and membership management
- Password resets and account management
- Group Policy Object (GPO) creation and configuration
- Password Policy enforcement
- Account Lockout Policy configuration
- Understanding of enterprise IT security practices

---

## 💡 Key Lessons Learned

1. **ISO vs OEM** — VirtualBox requires an ISO format to boot. OEM packages are for manufacturer pre-installations and cannot be used as bootable media.
2. **IDE vs SATA for boot** — ISO files must be attached to an IDE Controller in VirtualBox for the VM to boot from them correctly.
3. **Installing a role vs configuring it** — Installing AD DS and promoting to Domain Controller are two separate steps. Many beginners miss this distinction.
4. **Disable before delete** — Never immediately delete a user account when someone leaves. Always disable first to preserve data.
5. **Group-based permissions** — Always assign permissions to groups not individuals. It scales infinitely better and is far easier to manage.
6. **Group Policy scope** — Policies linked at the domain level apply to everyone. Policies linked to specific OUs apply only to users and computers in that OU.

---

## 🔮 Future Improvements

- [ ] Add a Windows 10/11 client VM and join it to the domain
- [ ] Set up shared network folders with group-based access permissions
- [ ] Configure Remote Desktop access for domain users
- [ ] Implement Fine-Grained Password Policies per OU
- [ ] Set up a second domain controller for redundancy
- [ ] Explore Active Directory with PowerShell automation

---

## 👤 Author

**JeanI**
Aspiring IT Professional | Home Lab Enthusiast
[LinkedIn Profile](#) *(add your LinkedIn URL here)*

---

## 📄 License

This project is for educational purposes. Windows Server 2022 Evaluation is provided free by Microsoft for 180 days for testing and learning.
