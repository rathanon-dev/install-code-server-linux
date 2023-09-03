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

## add dominname 
```shell
  dominname.com {
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
ssh-keygen -q -t rsa -N '' -f ~/.ssh/id_rsa <<<y >/dev/null 2>&1
ssh-keyscan -t rsa remote.moe >> ~/.ssh/known_hosts
ssh -R 3000:localhost:3000 remote.moe
# คลิกลิงค์ที่ ถูกสร้างขึ้นมา
```
## อ้างอิงข้อมูลจาก
[code-server](https://coder.com/docs/code-server/latest)
[caddy](https://caddyserver.com/docs/install#debian-ubuntu-raspbian)
[node-js](https://github.com/nodesource/distributions#installation-instructions) <br>
Tunnels to Remote.moe, Provided by @.makuwu from discord.











