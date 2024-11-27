# Lab #2,22110018, Nguyen Hai Duong, INSE330380E_24_1_03FIE
# Task 1: Firewall configuration

**Question 1**: Setup a set of vms/containers in a network configuration of 2 subnets (1,2) with a router forwarding traffic between them. Relevant services are also required:
- The router is initially can not route traffic between subnets
- PC0 on subnet 1 serves as a web server on subnet 1
- PC1,PC2 on subnet 2 acts as client workstations on subnet 2 
**Answer 1**:
## 1. The router is initially can not route traffic between subnets:
Setting up the router on VMWARE
<img width="1280" alt="{7EB0CC27-C7E5-4904-9B38-1206B4C8ACF2}" src="https://github.com/user-attachments/assets/74cba814-7002-44e9-8081-c9c2ff6e7781">
-Configure network setting for Router
-Assign static IP
```sh
network:
  version: 2
  ethernets:
    ens33:  # Card mạng kết nối Subnet 1
      addresses:
        - 192.168.1.1/24
      dhcp4: no
    ens34:  # Card mạng kết nối Subnet 2
      addresses:
        - 192.168.2.1/24
      dhcp4: no


```
then use sudo netplan apply
<img width="1280" alt="{1A22BCFA-7858-4169-951F-4268ABDC786D}" src="https://github.com/user-attachments/assets/a985b2df-0080-459b-ac03-7ed4b5a54d4f">





## 2. PC0 on subnet 1 serves as a web server on subnet 1Encrypt the file using AES-256 in ECB mode:
Setting up PC0 with VMWARE
- Configure network setting for PC0 
```sh
sudo nano /etc/netplan/00-installer-config.yaml
```
-Assign static IP
```sh
network:
  version: 2
  ethernets:
    ens33:
      addresses: [192.168.1.100/24]
      routes:
        - to: default
          via: 192.168.1.1
      dhcp4: no

```
<img width="916" alt="{D7C279DF-2A95-4837-95DE-E7C181DA6446}" src="https://github.com/user-attachments/assets/58778443-040e-432d-9b17-6f3b67c134ad">
then use sudo netplan apply
Check the network setting by  " ip addr"
- Setup Web Sever( Apache) on PC0

```sh

sudo apt install apache2 -y
```
<img width="1139" alt="{187451B3-403A-402A-A79C-9F2914D0F743}" src="https://github.com/user-attachments/assets/11b6218c-e0ca-46bf-a9d6-207a4b2be712">





## 3. PC1,PC2 on subnet 2 acts as client workstations on subnet 2 :

- Configure network setting for PC1:
<img width="1261" alt="{B37AA197-147C-439E-AA86-18AB5AC7D6F1}" src="https://github.com/user-attachments/assets/a01b84a8-a787-49aa-bc67-be412c953bb2">
-Assign static IP
```sh
network:
  version: 2
  ethernets:
    ens33:
      addresses:
        - 192.168.2.100/24
      routes:
        - to: 0.0.0.0/0
          via: 192.168.2.1
      dhcp4: no


```
then use sudo netplan apply
- Configure network setting for PC2:
  <img width="1280" alt="{37FEBDE5-0B62-44B4-8825-82E877F42AEA}" src="https://github.com/user-attachments/assets/40b1906e-94a5-4b44-9446-1cb0f2b065ad">

-Assign static IP
```sh
network:
  version: 2
  ethernets:
    ens33:
      addresses:
        - 192.168.2.101/24
      routes:
        - to: 0.0.0.0/0
          via: 192.168.2.1
      dhcp4: no

```
then use sudo netplan apply

  






**Question 2**: Enable packet forwarding on the router.
- Deface the webserver's home page with ssh connection on PC1
**Answer 2**:
  
## 1. Enable packet forwarding on the router:
 allow routing
 
  ```sh
sudo sysctl -w net.ipv4.ip_forward=1
```

<img width="752" alt="{8EFEEFA5-9950-49E0-87F6-C1924778DD7E}" src="https://github.com/user-attachments/assets/a45fb710-16dd-4f22-9a30-aeb170a39e7f">
Make sure routing is enabled permanently

  ```sh
sudo nano /etc/sysctl.conf
sudo sysctl -p

```
remove # in net.ipv4.ip_forward=1. line
<img width="1280" alt="{659E20CB-08EC-4B35-814B-A0F0C3E4BE85}" src="https://github.com/user-attachments/assets/49555bcb-0d95-45a9-a634-adaf89e80e0f">

## 1.Deface the webserver's home page with ssh connection on PC1:
connect SSH from PC1 to PC0:

  ```sh
ssh <duong>@192.168.1.100
```
Access the root file of the web server
  ```sh
sudo nano /var/www/html/index.html

```
change the content 
<img width="1246" alt="{FD13DB7A-A6DE-4A06-9525-879B859B2485}" src="https://github.com/user-attachments/assets/18a456c1-641c-4c00-96e2-d4fdc0ced4f8">

Access from PC1
  ```sh
curl http://192.168.1.100

```





<img width="221" alt="{A7FCBFAD-F427-4340-A5B5-CED36DD0819F}" src="https://github.com/user-attachments/assets/260cbebd-1d19-45b4-ba82-6919f24a7dba">






**Question 3**: Block SSH to PC0 from PC1
**Answer 3**:
  ```sh
sudo iptables -A FORWARD -p tcp --dport 22 -s 192.168.2.100 -d 192.168.1.100 -j DROP
```

Setup on Router to block SSH
<img width="1189" alt="{97D844E2-83B3-4F15-BB59-8DDE67C90382}" src="https://github.com/user-attachments/assets/2d6dc6a9-08ef-4b4b-87fb-7e98cbe36e9a">
After check SSH connection from PC1, SSH will be blocked: 
  ```sh
ssh <duong>@192.168.1.100

```
<img width="263" alt="{1F25113A-C068-4C17-A1CA-4C875B603BBE}" src="https://github.com/user-attachments/assets/47ad3b96-378d-4a7e-b363-65329e1fb144">



**Question 4**: Configure UDP Server and Block UDP from PC2
**Answer 4**:
- PC1 will be setup as UDP sever
  Install Netcat on PC1

  ``` sh
  sudo apt install netcat -y
  ```
  -Run UDP on PC1

  ``` sh
  nc -u -l 12345
  ```
  <img width="1218" alt="{E7F67000-0A1C-4AF2-99EC-20FB4B87906C}" src="https://github.com/user-attachments/assets/1ffdfeba-e8a5-41ff-bc39-d2b1bf90838f">

Check UDP
From PC0:
<img width="356" alt="{9598DA5A-2DA9-40F1-B503-3B4ACE0665C7}" src="https://github.com/user-attachments/assets/b110385b-1935-46d7-8de1-3e1dcffa42a8">
From PC2:
<img width="1280" alt="{FAD0FAB8-64C1-4435-AE91-A0D79775D67B}" src="https://github.com/user-attachments/assets/f4c7f038-9c6a-46cd-816b-aa616047c285">

Block UDP From PC2 on PC 1:
```sh
sudo iptables -A INPUT -p udp --dport 12345 -s 192.168.2.101 -j DROP
```
<img width="420" alt="{7FB42885-9485-4B8D-B5F0-92CBD3B6DF9A}" src="https://github.com/user-attachments/assets/525b8d4f-acf7-4026-a8c4-eaabdf0514e1">

Then I can see  the ping from PC0 still be accepted but the ping from PC2 will be blocked






# Task 2. Encrypting large message 
Use PC0 and PC2 for this lab 
Create a text file at least 56 bytes on PC2 this file will be sent encrypted to PC0
**Question 1**: Encrypt the file with aes-cipher in CTR and OFB modes. How do you evaluate both cipher in terms of error propagation and adjacent plaintext blocks are concerned. 
**Answer 1**:
## 1. Setup OpenSSL on PC0 and PC2.

<img width="1128" alt="{88763CDB-7A06-432F-9708-0D1883CC7BD4}" src="https://github.com/user-attachments/assets/49738f79-5d40-4a7f-bff5-7c495eca91ce">
<img width="554" alt="{981B0210-AE95-4AF2-812F-2AC8D386462A}" src="https://github.com/user-attachments/assets/a21f721d-7709-410d-bbbe-00c47977b97d">

```sh
sudo apt install -y openssl nano net-tools
```





## 2. Create a text file on PC2:

```sh
echo "test." > testfile.txt

```
<img width="304" alt="{4F2B4CA1-A0AE-475D-B4D3-B4EF18471B6E}" src="https://github.com/user-attachments/assets/5481e94f-9588-4243-903c-7dbe67009078">







## 3. Encrypt:

-Encrypt with CTR

```sh
openssl enc -aes-256-ctr -in testfile.txt -out encrypted_ctr.bin -K 0123456789abcdef0123456789abcdef -iv abcdefabcdefabcdefabcdefabcdef
```

<img width="1180" alt="{156D69A1-0CDF-4EC3-B365-79DB2B04EC38}" src="https://github.com/user-attachments/assets/e2b92dee-2eef-41c1-b0f8-22c58f5acff1">

- Encrypt with OFB

```sh
openssl enc -aes-256-ofb -in testfile.txt -out encrypted_ofb.bin -K 0123456789abcdef0123456789abcdef -iv abcdefabcdefabcdefabcdefabcdef
```
<img width="877" alt="{8E5F9ED5-9890-43C8-A79A-6B7EE7ABC96F}" src="https://github.com/user-attachments/assets/0143e45e-4640-4399-a181-1c49fe8fb076">



## 4. Transfer file from PC2 to PC0:

Send CTR: 

```sh
scp encrypted_ctr.bin user@192.168.1.100:/home/duong/
```
<img width="331" alt="{66529374-8378-4A7F-8A56-70016EB809E6}" src="https://github.com/user-attachments/assets/cfa08621-c05a-4ae0-8cb1-50813d89251c">


Send OFB: 
```sh
scp encrypted_ofb.bin user@192.168.1.100:/home/duong/

```
<img width="380" alt="{E27246D4-8577-4029-9AB0-4658488D03F8}" src="https://github.com/user-attachments/assets/e408b99e-5651-4d69-9315-dff8a6eb7b8e">


## 4. Verify the received file on PC0:
- Check CTR
```sh
cat encrypted_ctr.bin | hexdump -C
```
<img width="242" alt="{FF3BB346-9823-4BA2-9FC6-4AF36DE4CC65}" src="https://github.com/user-attachments/assets/529f2fa4-b8c6-42cb-8cd7-d6162ca03f9e">
- Check OFB

```sh
cat encrypted_ofb.bin | hexdump -C
```
<img width="225" alt="{5E56B73B-E9A5-4C42-8CC4-FBFE840554B9}" src="https://github.com/user-attachments/assets/d74a19e6-a4f6-454f-b871-eb14f399b220">

