# Active-Directory-Home-Lab-With-Bulk-User-Creation

![Screenshot (94)](https://github.com/user-attachments/assets/df3aef43-86d2-413e-bc95-3ed032b1e45f)

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

