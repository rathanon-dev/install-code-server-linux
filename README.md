# install-code-server-linux
## การติดตัง code-server ใน linux
# Create VPN linux 
### เลือกผู้ให้บริการ VPS Server [OVHcloud: Cloud Computing & Web Hosting](https://ca.ovh.com/) or Cloud VPS etc.
### สร้าง VPN server เลือกเป็น [Debian](https://www.debian.org/) หรือ Linux os etc. 
# ssh to VPN server 
`$ ssh user@ip-vpn-server`
## Updata & Upgrade linux vpn server 
`$ sudo apt update`

#### อัพเดตแพ็กเกจที่มีการอัพเดตใหม่
`$ sudo apt upgrade -y`

#### ลบแพ็กเกจที่ไม่ใช้แล้ว
`$ sudo apt autoremove -y`

#### ลบแพ็กเกจที่ถูกเก็บไว้ในแคช
`$ sudo apt clean`
# Install Code-Server
`$ sudo -u debian curl -fsSL https://code-server.dev/install.sh | sh`
#### code-server เริ่มต้นทำงานทันทีหลังจาก Reboot
`$ sudo systemctl enable --now code-server@$USER`
### Password ของ code-server อยูใน ~/.config/code-server/config.yaml
`$ cat ~/.config/code-server/config.yaml`

### เปลี่ยน Password โดยแก้ Password: XXXXX ใน ~/.config/code-server/config.yaml
`$ sudo nano ~/.config/code-server/config.yaml` 
### restart code-server
`$ sudo systemctl restart code-server@$USER`

# Install Caddy
#### Add the Caddy repository's GPG key and repository information
```shell
sudo apt update
sudo apt install -y debian-keyring debian-archive-keyring apt-transport-https
sudo  curl -1sLf 'https://dl.cloudsmith.io/public/caddy/stable/gpg.key' | sudo gpg --dearmor -o /usr/share/keyrings/caddy-stable-archive-keyring.gpg
sudo curl -1sLf 'https://dl.cloudsmith.io/public/caddy/stable/debian.deb.txt' | sudo tee /etc/apt/sources.list.d/caddy-stable.list
```
#### Update package lists
`$ sudo apt update`
#### Install Caddy
`$ sudo apt install caddy`

## Replace /etc/caddy/Caddyfile using sudo so that the file looks like this: 
`$ sudo nano /etc/caddy/Caddyfile`

![image](https://github.com/ratanon-144/install-code-server-linux/assets/88425078/9e05c2fd-1104-4c6a-a77e-67670d8e2646)

## add domainname อ่านก่อน >> [caddy](https://coder.com/docs/code-server/latest/guide#using-lets-encrypt-with-caddy)
```shell
  domainname.com {
  # กำหนดประเทศไทย IP range หรือ IP ที่คุณต้องการ
  @internal {
     remote_ip 1.0.0.0/8 
  }
  # กำหนดเงื่อนไขให้เข้าถึงได้เฉพาะ IP ภายในที่ระบุ
  handle @internal {
      reverse_proxy 127.0.0.1:8080
  }
  # กำหนดการจัดการสำหรับผู้ใช้ที่ไม่ใช่ IP ภายใน IP range 
  respond "Forbidden" 403
} 
```
### แก้ไข แล้ว  reload caddy
`$ sudo systemctl reload caddy`
## ทำ SSL Nginx digitalocean >> [Nginx SSL](https://www.digitalocean.com/community/tutorials/how-to-secure-nginx-with-let-s-encrypt-on-debian-11)
# อนุญาตการเชื่อมต่อ SSH ผ่าน UFW ห้ามลืมเด็ดขาด!!!!!!! 
วิธีแก้ ssh ไม่ เพราะไม่เปิด firewall ssh port 22 (เฉพาะ VPS ค่าย OVH) 
![image](https://github.com/user-attachments/assets/c304fe7a-8743-4dd2-8a6a-059203c5cc81)
1. หา setting Reboot in rescue mode
 ![image](https://github.com/user-attachments/assets/bb42891a-649a-4cdc-a357-4a6ec129df29)
2. เข้าผ่าน KVM ดู use : password
3. เข้า ssh ตาม use : password  (rescue mode)
 ![image](https://github.com/user-attachments/assets/46196f49-0426-441e-8455-cd3caf06abad)
4. เมื่อคุณเข้าสู่ rescue mode ไดรฟ์ระบบของคุณอาจไม่ได้ถูก mount โดยอัตโนมัติ
   ค้นหาไดรฟ์ที่ใช้สำหรับ root filesystem โดยใช้คำสั่ง:
```shell
fdisk -l
```
![image](https://github.com/user-attachments/assets/810a0030-b5d5-4362-8c62-f96808147c35)
เมื่อรู้ว่า partition ไหนเป็น root ให้ mount ไดรฟ์ด้วยคำสั่ง:
```shell
mount /dev/sdb1 /mnt
mount --bind /dev /mnt/dev
mount --bind /proc /mnt/proc
mount --bind /sys /mnt/sys
```
chroot เพื่อให้สามารถทำงานในระบบหลักของคุณได้:
```shell
chroot /mnt
```
อนุญาตการเชื่อมต่อ SSH ผ่าน UFW:
```shell
ufw allow ssh
ufw status
```
ออกจาก chroot และ unmount:
```shell
exit
umount /mnt/dev
umount /mnt/proc
umount /mnt/sys
umount /mnt
```
Reboot VPS 
# install nodejs v 18.x
### add keyrings
```shell
sudo apt-get update
sudo apt-get install -y ca-certificates curl gnupg
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://deb.nodesource.com/gpgkey/nodesource-repo.gpg.key | sudo gpg --dearmor -o /etc/apt/keyrings/nodesource.gpg
```
### set key nodesource.list
```shell
echo "deb [signed-by=/etc/apt/keyrings/nodesource.gpg] https://deb.nodesource.com/node_18.x nodistro main" | sudo tee /etc/apt/sources.list.d/nodesource.list
```
### update and install
```shell
sudo apt-get update
sudo apt-get install nodejs -y
```
## เสริม install yarn
`$ sudo npm install --global yarn`

# แก้ปัญหา  npm run dev localhost:3000 ไม่ได้
### 1. npm run dev Terminal หลักก่อน 
`$ npm run dev`
### 2. เปิด หรือ Split Terminal อีกอัน 
##### สร้าง SSH reverse tunnel เพื่อเปิดพอร์ต 3000 บน Remote.moe และเชื่อมต่อไปยังพอร์ต 3000 ในเครื่องของคุณ.
```shell
# generate ssh key ของ remote.moe
ssh-keygen -q -t rsa -N '' -f ~/.ssh/id_rsa <<<y >/dev/null 2>&1
ssh-keyscan -t rsa remote.moe >> ~/.ssh/known_hosts
```
## คลิกลิงค์ที่ ถูกสร้างขึ้นมา (run คำสั่งนี้ ซ้ำเมื่อต้องการใช้ครั้งหน้า)
`$ ssh -R 3000:localhost:3000 remote.moe` 


## แก้ jupyter notebook (venv) not import
##### แก้ใชไฟล์ pyvenv.cfg ที่อยู่ในโฟลเดอร์ .venv หรือชื่อทีใช้สร้าง venv
![venv](https://github.com/ratanon-144/install-code-server-linux/assets/88425078/7af40789-45db-4b87-a70d-dba8a66f7144)
##### แก้ เป็น true แล้ว save
```shell 
include-system-site-packages = true  
```

![change](https://github.com/ratanon-144/install-code-server-linux/assets/88425078/7adc2e8d-f125-4044-b3a3-bf9aa099c7fb)

 
[stackoverflow](https://stackoverflow.com/questions/45666097/importerror-no-module-named-pandas-inside-virtualenv)

## Set Python 3.12 as default in Debian 12:
#### 1.) First, install Python3.12 as alternative for python via command:
```shell
sudo update-alternatives --install /usr/bin/python python /usr/local/bin/python3.12 1
```
#### Then, run the command below and type the number for Python 3.12:
```shell
sudo update-alternatives --config python
```
### 2.) For pip, it’s recommended to use python3.12 -m pip install command. If you insists, run commands below one by one to set it as default.
```shell
sudo update-alternatives --install /usr/bin/pip pip /usr/local/bin/pip3.12 1
```
```shell
sudo update-alternatives --config pip
```
[fostips](https://fostips.com/install-python-3-10-debian-11/)
## อ้างอิงข้อมูลจาก
[code-server](https://coder.com/docs/code-server/latest)
[caddy](https://caddyserver.com/docs/install#debian-ubuntu-raspbian)
[node-js](https://github.com/nodesource/distributions#installation-instructions) <br>
Tunnels to Remote.moe, Provided by @.makuwu from discord.











