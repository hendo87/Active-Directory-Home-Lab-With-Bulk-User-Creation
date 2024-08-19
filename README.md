# Active-Directory-Home-Lab-With-Bulk-User-Creation


![Screenshot (112)](https://github.com/user-attachments/assets/e2b87dfc-7819-4d9c-8844-d770b61cd937)

<br>

For the Virtual Machine that will be hosting my Domain Controller, I need two network adapters. I need the NAT that will use my host IP address from my home router and an Internal Network Adapter so that my DC can communicate with other Virtual Machines. For the Internal Network I will be using VMnet0. Refer to the diagram at the beginning


![Screenshot (95)](https://github.com/user-attachments/assets/e51d0526-6143-4790-bb80-1bf44d7f9a9e)

<br>

![Screenshot (96)](https://github.com/user-attachments/assets/b95a1010-0ca0-4d2a-99a4-e289c05e7da1)

<br>

After downloading Windows Server 2019 on the Virtual Machine the first thing I have to do is configure the two network Adapters I have. One is the external NIC and one is the Internal NIC

<br>

![Screenshot (97)](https://github.com/user-attachments/assets/9fd7acb2-c5e0-4b20-9e99-b792dcc047cc)

<br>
Now I have to figure out which NIC is our NAT. It is Ethernet0 because its DNS is localdomain

![Screenshot (99)](https://github.com/user-attachments/assets/beaad5e0-59ac-46d0-ae85-228cddc64373)

Now I configure the Internal network adapter and assign it an IP address based on the diagram above (172.16.0.1) and I do not need to give it a default gateway because the Domain Controller is the gateway. As for the DNS server I assign it an IP based on the diagram because when we install Active Directory it will install DNS. I set it as a loopback address so it pings itself

![Screenshot (101)](https://github.com/user-attachments/assets/0b16dc10-4a83-4a3d-a425-222746aeca33)

Now that I know which network adapter is our external and internal, I go ahead and rename the PC from the long complicated name is has now to just DC (Domain Controller). This forces a restart, which is fine

![Screenshot (102)](https://github.com/user-attachments/assets/36bcc48a-17a2-468a-aca8-f808c15f0f1c)

After the reboot we install Active Directory Domain Services on the DC and create a full qualified domain name (FQDN)

![Screenshot (103)](https://github.com/user-attachments/assets/36ee3f53-1287-4549-b9ae-61a5d7dd6fe9)



![Screenshot (104)](https://github.com/user-attachments/assets/3772744d-5126-40e0-bceb-dc694b8ab77f)


<br>

We’ll have to promote this server to a domain controller. Choose “Add a new forest” and we can name it “mydomain.com” for simplicity sake. Go through the rest of the configuration wizard leaving the settings as is and click install. Your VM will restart automatically.

![Screenshot (105)](https://github.com/user-attachments/assets/6565f0ef-29a6-4d60-b08e-fef515d1798a)

![Screenshot (106)](https://github.com/user-attachments/assets/984c50ec-9058-42e2-8d53-99c061e6db08)


We’ll create a dedicated organizational unit (OU) for our administrators and a new admin user within that OU. We’ll need to give the new user admin access by right-clicking and selecting properties. From here we can select add and type in “Domain Admins”. Click OK and apply the changes.

![Screenshot (107)](https://github.com/user-attachments/assets/c892b247-5fdf-4a80-b618-6079608753d8)

![Screenshot (108)](https://github.com/user-attachments/assets/e6aa2378-83ae-4510-9417-48135429c2b6)

Next, we’re going to setup RAS/NAT which stands for Remote Access Server and Network Address Translation. RAS will be installed the same way we installed AD AS. Once that is complete we will install NAT. We will go to the “Tools” bar in our Server Manager and select “Routing and Remote Access”. Right-click our DC and select “Network address translation”. You’ll want to see the top option selected with our NICs visible and we will choose “_INTERNET”. If you have trouble close those tabs out and try the steps again.

![Screenshot (109)](https://github.com/user-attachments/assets/a90ed446-2986-4776-93b6-8af4e1b947bf)

![Screenshot (110)](https://github.com/user-attachments/assets/cfd630bb-6c79-4190-99c4-4ad532aa5ef5)

Now to configure the DHCP and setup a scope. The whole purpose of DHCP is to allows computers on the network to automatically be assigned an IP address. The scope I will be creating will give assign IP addresses in a range, the range being 172.16.0.100-200 so the DHCP will effectively be able to give out 100 IP addresses. I also set the amount of time the IP addresses can be leased out to 20 days. The reason for the lease is when an IP address is assigned, it can't be used by other devices. So if I only have 100 IP addresses and 100 are used, new devices can't be assigned an IP address on the network meaning they can't connect to the internet. A lease is just an amount of time an IP address can be owned (leased) by a device before being recycled. If this was for example a Café that offers wifi and the average time a person spent inside said Café was 2 hours, it would make no sense to lease an IP address to them for 20 days. That would effectively lock up that IP address for that amount of time and no one else could use it. If this were a Café I would recommend setting the lease duration to under 4 hours and give a bigger range. Since this is only VM, the lease duration doesn't matter.


![Screenshot (111)](https://github.com/user-attachments/assets/5e417d5f-b0bc-46ee-94a9-c11d2318705b)

Now that Active Directory is configured and my Domain Controller is configured as well, I use a Powershell script to create over 1000 user accounts

![Screenshot (114)](https://github.com/user-attachments/assets/017fcec5-ef0e-41ee-93fb-4b893ac9a9d1)


<br>
<li>$PASSWORD_FOR_USERS = "Password1" ( We are creating a password variable. All of our users will use "Password1" as the password)</li>
<li>$USER_FIRST_LAST_LIST = Get-Content .\names.txt ( We are taking names from the " names.txt " file and storing them in the variable )</li>
<li>$password = ConvertTo-SecureString $PASSWORD_FOR_USERS -AsPlainText - Force ( This takes the plain text password and creates it into a object )</li>
<li>New-ADOrganization -Name _USERS -ProtectedFromAccidentalDeletion $false ( This creates a new Organizational Unit called " Users " )</li>
<li>foreach ($n in $USER_FIRST_Last_List) -- ( creating a loop , $n is a object and will iterate through our list)</li>
<li>$first = $n.Split(" ")[0].ToLower() -- (This splits the names. Element 0 becomes the first name. Also converts the text to all lower case )</li>
<li>$last = $n.Split( " " )[1].ToLower() -- (Element 1 becomes the last name. Also converts text to lower case)</li>
<li>$username = "$($first.Substring(0,1))$($last)".ToLower() -- (We are taking the first character from the first name and appending it to the last name to create the username. Example RHenderson)</li>
<li>Write-Host "Creating user: $($username)" - BackgroundColor Black -ForegroundColor Cyan ( This is creating output on the screen every time a user is created)</li><br>
<li>New-ADUser -AccountPassword $password ( The rest of the code is inputting the information needed to create the user in Active Directory)</li> <br>
<p>-GivenName $first</p>
<p>-Surename $last</p>
<p>-DisplayName $username</p>
<p>-Name $username</p>
<p>-EmployeeID $username</p>
<p>-PasswordNeverExpires $true</p>
<p>-Path "ou=_USERS,$(([ADSI]' "").distinguishedName)"</p>
<p>-Enabled $true</p>


![Screenshot (115)](https://github.com/user-attachments/assets/6e5c8462-839d-461a-9f21-17f55450ada1)

<br>

It is now time to create a new Virtual Machine that will act as a user in the domain. I name this machine CLIENT1.I configure the network adapter so that it is not NAT and can't connect to the internet on my local network. The only way this Virtual Machine should be able to connect to the internet is by being assigned an IP from the DC on the Server VM. Refer to the Diagram at the beginning. I have to change the network adapter to be on the same internal network as the Domain Controller, in this case VMnet0

![Screenshot (116)](https://github.com/user-attachments/assets/17f88bd9-50d7-4ec9-aef8-4defa9eb1a8b)

