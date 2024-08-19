# azure-honeypot-project
Welcome to the Azure Honeypot Project!
Honeypot is a fake network or computer system that appears legitimate but isn't really being used for any critical tasks. It’s designed to attract hackers/attackers.
Hackers are drawn to these honeypots because they appear like simple targets, just like a bee is drawn to honey. Once they try to break in, cybersecurity experts can watch what the hackers are doing.
This project will guide you through one of the most fascinating honeypots I've ever deployed called <b>T-Pot</b>.
# Use-Cases
* Identify attack source, location, and ISP.
* Discover frequently used passwords for brute-force attacks.
* Analyze intruder actions and commands executed.
* Track malware download attempts.
* Detect and analyze malware dropped by intruders.

Let's start the project.

The first thing to do is to sign up an [microsoft azure portal.](https://azure.microsoft.com/en-in/get-started/azure-portal)

_NOTE_ -: _The whole project can be done with the free trial account of Microsoft Azure. Azure provides $200 free credit, and this is more than sufficient for this project. It is completely free of charge. So no need to worry :)_

After the creation of account. At the top, search for virtual machine,then click on create VM. 

After that create a new resource group and name it "tpot-rg". ↓

![WhatsApp Image 2024-08-19 at 12 30 04 PM](https://github.com/user-attachments/assets/d0ea6b8f-527c-488f-8f5f-73929731a2a5)

If you ever wanted to delete the entire project, just delete this resource group and everything will be erased completely.

Then name the virtual machine as your choice. Set the region *(US) EAST US 2*. ↓

![WhatsApp Image 2024-08-19 at 12 40 21 PM](https://github.com/user-attachments/assets/33baaa17-f436-4194-9b40-8538f6d61528)

After that select the Availability zone as "Zone 3". So that we can avail our Almalinux os image and our storage size. Then set the security type to _standard._  ↓

![WhatsApp Image 2024-08-19 at 12 51 17 PM](https://github.com/user-attachments/assets/05062d89-a574-471e-a7c6-8d83bbf1220c)

Then select the OS image *AlmaLinux OS 9 -x64 Gen2* which is completely free. ↓

![WhatsApp Image 2024-08-19 at 12 56 24 PM](https://github.com/user-attachments/assets/6396fa42-abb0-481c-96ab-91281db33905)

Next thing to do is choose size , here choose _Standard_B4ms-4vcpus, 16 GiB memory (10.081.53/month)_.<b>No need to panic with the money it will be deducted from your free $200 credit:)</b> ↓

![WhatsApp Image 2024-08-19 at 4 08 00 PM](https://github.com/user-attachments/assets/bb6fae48-7b50-4101-89a8-0eb5bd78c5df)

Now select the authentication type as "Password." Then enter username & password as your choice. After that click on <b>Next : Disks ></b> ↓

![WhatsApp Image 2024-08-19 at 4 16 40 PM](https://github.com/user-attachments/assets/fd4b11d0-431f-4103-8ad4-9f878b99b692)

Then change the disk size to 256 GiB (P15), and then select <b></b>Next : Networking > </b> ↓

![WhatsApp Image 2024-08-19 at 4 31 42 PM](https://github.com/user-attachments/assets/1efb3f99-86e4-4e5e-b507-64ff6983fc78)

Here, check the box to delete public IP and NIC when VM is deleted.(Most important). Then click on <b>Review + create </b> and wait a few seconds for validation test.

*NOTE -: I didn't use a load balancer here because it is a small-scale project, so I am not expecting heavy traffic. If you want to use it. It's completely up to your choices and needs.* ↓

![WhatsApp Image 2024-08-19 at 4 42 55 PM](https://github.com/user-attachments/assets/5cfdd8af-7656-4ee8-a6e9-6988cc709128)

After passing the validation click on <B>Create</b> and wait 2-3 minutes for deployment process. ↓

![WhatsApp Image 2024-08-19 at 4 57 23 PM](https://github.com/user-attachments/assets/9b2eb656-662d-4c00-8f3d-785bb62a3ab2)

![WhatsApp Image 2024-08-19 at 12 22 08 PM](https://github.com/user-attachments/assets/ead81ba9-b7c5-4954-a554-ecbc7cbdaa73)

There is no need to panic by watching the pricing. This will be deducted from the free credit. So Keep Calm and take a deep breathe ;)

![WhatsApp Image 2024-08-19 at 12 22 08 PM (1)](https://github.com/user-attachments/assets/c8fca700-fc36-48eb-a1e5-5c36f9c1033e)

# Open traffic flow

Now we need to open up the gates and create a rule to allow all communication into the honeypot. This will allow the attackers to be able to attack the honeypot, so we can collect the data. 
So let's start ⏩


At the search bar type "*tpot-vm-nsg*" make sure you replace tpot-vm with your own vm name. and select the NSG(Network security group resource).

Now select <b>Inbound security rules</b> from left panel under settings and then click on <b>Add.</b> ↓

![WhatsApp Image 2024-08-19 at 5 39 44 PM](https://github.com/user-attachments/assets/ee6ad8c6-4d49-4591-ad34-36ef9578c14f)

Now change *Destination port ranges* to "*" which means allow any port. ↓

![WhatsApp Image 2024-08-19 at 5 49 00 PM](https://github.com/user-attachments/assets/ea73e69e-3872-474d-a132-de80b8072fb0)

Now scroll down and change priority to <b>100</b>. Because rules are processed in priority order. Lower the number higher the priority.
Change name to "Danger_allow_all". This is customizable you can write your own warning message whatever you want.
Then click on "Add" ↓

![WhatsApp Image 2024-08-19 at 5 57 35 PM](https://github.com/user-attachments/assets/23aa6f55-8c2c-4b72-aefa-b3e3498817a3)


This set of rules on the NSG applies to all resources in the NSG and *allows all traffics* on *all ports* inside.
This is not recommended anywhere except this project. This is not a good practice at all.

And all set. The cloud configuration part is over. Now it's high time to move to the configuration of honeypot ;)

Grab your coffee guys ☕

# Configuring the honeypot

First of all copy your public IP address from your azure virtual machine. After that open your Kali linux or parrot machine or whatever you are using and login to the azure VM through ssh.

<b>Syntax for ssh login-: ssh yourazurevmusername@publicipthatyoucopied </b>

Now after login setup a non-root user with sudo permission.

# Setting up a non-root user
Syntax for creating non-root user-: sudo useradd -m -d /home/newuser -s /bin/bash newuser ↓

![WhatsApp Image 2024-08-19 at 12 22 08 PM (3)](https://github.com/user-attachments/assets/056b190b-7670-43b3-a8de-e51174864207)

Now set password for that non-root user.

Syntax to setup password for non-root user :- sudo passwd newuser ↓

![WhatsApp Image 2024-08-19 at 12 22 08 PM (4)](https://github.com/user-attachments/assets/c97d08de-e45a-41db-b58e-8acc48b25708)

Now give that non-root user sudo privilege. Syntax-: sudo usermod -aG wheel newuser ↓

![WhatsApp Image 2024-08-19 at 12 22 08 PM (5)](https://github.com/user-attachments/assets/25fc13f1-82c6-4240-959c-26e0efa16ae2)

_NOTE-: In AlmaLinux the non-root users can be granted sudo permission by adding them to the <b>wheel</b> group._

Now update the server and OS and install some packages 

Follow the below commands ↓

sudo dnf update -y

sudo dnf upgrade -y

sudo dnf install nmap lynx screen grc

![WhatsApp Image 2024-08-19 at 12 22 08 PM (6)](https://github.com/user-attachments/assets/15092a74-7c34-4d4c-a70d-a0243fb3734f)

It's okay if you can't install grc,screen and lynx package but make sure you have installed nmap.

Now log in as the non-root user:-

Syntax to login-: su newuser 

Enter your password. ↓

![WhatsApp Image 2024-08-19 at 12 22 08 PM (7)](https://github.com/user-attachments/assets/72bf8c2e-ddcf-430a-bf0b-a0f5dfea7070)

# Pre-install checks

Let’s check no other ports are open other than SSH which we need for remote management.

Syntax for this:- nmap -p- localhost ↓

![WhatsApp Image 2024-08-19 at 12 22 08 PM (8)](https://github.com/user-attachments/assets/23a6274b-a9bd-4dbd-b878-97a4b98a126c)

List all currently running services on your AlmaLinux system.

Syntax for this :- sudo systemctl list-units --type=service --state=running  ↓

![WhatsApp Image 2024-08-19 at 12 22 08 PM (9)](https://github.com/user-attachments/assets/cc7ee678-cbec-41d2-9926-1495a250ee29)

Install netstat tool to check all listening ports on your system installing T-Pot.

Syntax for this :- sudo dnf install net-tools  ↓

![WhatsApp Image 2024-08-19 at 12 22 08 PM (10)](https://github.com/user-attachments/assets/05b82127-12aa-402e-8cd2-a6ffa96913b4)

Now check all listening ports on your system installing T-Pot. 

Syntax for this :- sudo netstat -tulpen ↓

![WhatsApp Image 2024-08-19 at 12 22 08 PM (11)](https://github.com/user-attachments/assets/dcc34483-05ea-471c-a9ff-a0815f9c53ff)

Now the show begins :)

# Installation of T-Pot

Install the prerequisite packages.

Follow these commands  ↓

sudo yum update

sudo yum install python3 git

![WhatsApp Image 2024-08-19 at 12 22 08 PM (12)](https://github.com/user-attachments/assets/8d01d908-0200-4526-a74d-9bced70aef40)

Clone the GitHub repository :-  git clone https://github.com/telekom-security/tpotce.git   ↓

![WhatsApp Image 2024-08-19 at 12 22 08 PM (13)](https://github.com/user-attachments/assets/b690c12e-b3b1-4c29-bf4e-e031361b35ec)

Now browse to the tpotce folder. Syntax :- cd tpotce 

Then run ./install.sh  ↓

![WhatsApp Image 2024-08-19 at 12 22 08 PM (14)](https://github.com/user-attachments/assets/8efbd764-0fbe-4d46-83df-b01530b53d1c)

Choose h as installation type for T-Pot Standard / HIVE.   ↓

![WhatsApp Image 2024-08-19 at 12 22 08 PM (15)](https://github.com/user-attachments/assets/46a44166-afb4-4658-aa14-453b77047756)

Create a web username and password for your T-Pot web portal. ↓

![WhatsApp Image 2024-08-19 at 12 22 08 PM (16)](https://github.com/user-attachments/assets/cb79fb4b-17eb-4c07-8685-8d31758753de)

Once the installer has completed the setup successfully, it’s now time to reboot the server. ↓

![WhatsApp Image 2024-08-19 at 12 22 08 PM (17)](https://github.com/user-attachments/assets/9f01766c-8283-4017-a02b-b6487a1554c4)

And all set. Now You are ready.

# T-Pot Landing Page
The T-Pot web portal runs on port 64297, head over to https://<yourpublic_ip>:64297/ and login with your newly created T-Pot web username and password.  ↓

![Screenshot (17)](https://github.com/user-attachments/assets/90b91fcf-4f5a-4c8c-ad37-06ee67204a51)

In the end explore a little t-pot feature :- 

That is Kibana in T-Pot is a powerful visualization tool for exploring and analyzing Elasticsearch data, offering a variety of dashboards and visualizations tailored to T-Pot-supported honeypots.


![Screenshot (18)](https://github.com/user-attachments/assets/902cf21d-361d-4355-99f7-bc34a59e176c)



![Screenshot (19)](https://github.com/user-attachments/assets/0be7169e-c631-4cc9-8747-c75de3d60df2)


# Conclusion
T-Pot stands out as a robust, all-in-one open-source platform, bundling multiple honeypots into a single, streamlined package.

It’s user-friendly and comes loaded with impressive dashboards, visualizations, and analytics, making it an excellent choice for anyone interested in threat intelligence and network security monitoring.

While T-Pot demands decent system resources, the ease of use and comprehensive features make it worth it. The visualizations and analytics, powered by Kibana, offer deep insights and can keep you engaged for hours.

Improving your OSINT and threat analysis capabilities is a key benefit of working with T-Pot.

That's all, hope you liked it.

Happy Hacking 🤜🤛


























