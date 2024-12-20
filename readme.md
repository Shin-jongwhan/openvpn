### 241217
## OpenVPN 설정 방법(서버)
### 나는 윈도우에 설치를 해볼 건데 먼저 openVPN을 다운로드 받는다.
#### https://openvpn.net/community-downloads/
#### ![image](https://github.com/user-attachments/assets/5b76f65d-98b5-4686-91a7-d380c671c2ad)
### <br/>

### 설치할 때 custom 설치로 들어가서 다음을 같이 설치한다.
#### ![image](https://github.com/user-attachments/assets/a371ff77-7712-49f5-bc0a-786544129a3d)
### <br/>

### 그리고 IP를 고정시킨다.
### cmd 창에 들어가 ipconfig /all을 입력 후 나온 ip 주소를 네트워크 관리로 가서 고정한다.
#### ![image](https://github.com/user-attachments/assets/85df214b-dcd2-4d39-8127-ab4c744c6c08)
### <br/>

### 공유 탭으로 들어가서 아래와 같이 설정
#### ![image](https://github.com/user-attachments/assets/fe6a87eb-186b-4c2a-9318-732f8fc55347)
### <br/>

### 이제 공유기 홈페이지로 가서 포트포워딩을 해야 한다.
### 각 공유기마다 접속 방법은 다르니 여기서는 자세한 접속 방법은 다른 블로그들을 참고하는 게 좋다.
### 포트포워딩하는 곳으로 가서 소스 포트, 소스 IP는 빈 칸으로 두고 원하는 포트를 설정한다.
### TCP, UDP 각각을 등록해도 되고, ALL이 있으면 이걸로 해도 된다.
#### ![image](https://github.com/user-attachments/assets/f634f8af-853a-411e-9a69-b834bb70042b)
#### ![image](https://github.com/user-attachments/assets/53875535-fac7-463d-915c-d6ea7479b486)
### <br/>

### C:\\Program Files\\OpenVPN\\config라는 폴더를 찾고(openvpn 설치 경로. 다른 곳에 있으면 그 위치) 그 안에 server 라는 폴더를 만들고, 그 안에 server.ovpn이라는 파일을 하나 생성한다.
### 그리고 다음과 같이 입력하는데, local에는 본인 IPv4 주소, port에는 포트포워딩한 port 번호를 입력한다.
### 서버에서 특정 route를 푸시해서 사용하려면 push "route 192.168.0.0 255.255.255.0 vpn_gateway"를 이용한다.
### 접속된 클라이언트 간에 통신하려면 client-to-client을 이용한다.
### DNS 주소는 서버 컴퓨터에서 DNS 설정한 주소랑 같아야 한다.
```
local 111.111.111.111
port 12345
proto udp
dev tun
ca pki/ca.crt
cert pki/issued/server.crt
key pki/private/server.key
dh pki/dh.pem
auth SHA512
tls-crypt tc.key
topology subnet
server 10.8.0.0 255.255.255.0
push "redirect-gateway def1 bypass-dhcp"
#ifconfig-pool-persist ipp.txt
push "dhcp-option DNS 1.1.1.1"
push "dhcp-option DNS 8.8.8.8"
#push "dhcp-option DNS 10.8.0.1"
#client-to-client
push "route 192.168.0.0 255.255.255.0 vpn_gateway"
keepalive 10 120
cipher AES-256-CBC
user nobody
group nogroup
persist-key
persist-tun
verb 3
#crl-verify crl.pem
explicit-exit-notify
```
### <br/>

### 이제 인증서 파일을 만들 준비를 한다.
1. 검색창에서 cmd를 관리자 권한으로 실행
2. cd C:\\Program Files\\OpenVPN\\easy-rsa\\
3. EasyRSA-Start.bat 입력
### 그 다음 아래 명령어들을 입력하고 모두 yes를 한다. 중간에 이름 설정하는 거 물어보는 게 있는데 본인이 원하는 걸로 하면 됨.
```
# easyrsa init-pki
# easyrsa build-ca nopass
# export EASYRSA_CERT_EXPIRE=3650
# easyrsa gen-req server nopass
# easyrsa sign-req server server
# easyrsa gen-dh
# easyrsa build-client-full client nopass
# openvpn --genkey secret tc.key
```
### 그럼 이렇게 pki라는 폴더가 하나 생성된다.
#### ![image](https://github.com/user-attachments/assets/d523d80c-5e4c-4228-9a27-f5c53bb5c475)
### <br/>

### 이제 클라이언트에서 접속할 수 있도록 인증서를 만들어야 한다.
### 파일 이름은 client.ovpn으로 한다. 
### 아래에서 remote \[WAN IP 주소\] \[port\]만 수정해서 사용한다.
### WAN 주소는 공유기 홈페이지에서 확인한다.
### port는 아까 포트포워딩한 주소를 입력한다.
### 그리고 pki 폴더에 있는 .crt와 각종 key 파일에 있는 암호화된 키를 파일명 써진 곳에 넣는다.
### C:\\Program Files\\OpenVPN\\easy-rsa\\ 안에는 tc.key 파일이 있다.
```
client
dev tun
proto udp
remote [WAN IP 주소] [port]
resolv-retry infinite
nobind
persist-key
persist-tun
remote-cert-tls server
auth SHA512
cipher AES-256-CBC
verb 3
<ca>
-----BEGIN CERTIFICATE-----
pki/ca.crt
-----END CERTIFICATE-----
</ca>
<cert>
-----BEGIN CERTIFICATE-----
pki/issued/client.crt
-----END CERTIFICATE-----
</cert>
<key>
-----BEGIN PRIVATE KEY-----
pki/private/client.key
-----END PRIVATE KEY-----
</key>
<tls-crypt>
-----BEGIN OpenVPN Static key V1-----
tc.key
-----END OpenVPN Static key V1-----
</tls-crypt>
```
### <br/>

### 이제 openvpn GUI를 열어서 server를 열고 client 컴퓨터에서 접속해보자. client.ovpn은 client 컴퓨터에 다운로드해서 openvpn/config 폴더 안에 넣어서 사용한다.
#### ![image](https://github.com/user-attachments/assets/f0ba9bf6-a923-47c5-91ca-f5dd8c26dead)
### <br/>

### 나는 openvpn 서버 컴퓨터에서 mysql을 실행해놨고, 여기에 client에서 접속이 잘 되는지 테스트해봤는데 잘 된다 !
### 이 때 접속할 때는 cmd 창에서 ipconfig를 입력해서 openvpn IPv4 주소를 입력해야 한다.
### <br/><br/><br/>

## 접속안 될 때 해결 사항들
### 방화벽으로 인해 접속이 안 될 수도 있다.
### 이 때는 UDP rule, openvpn 서버 포트를 inbound rule로 추가한다.
#### ![image](https://github.com/user-attachments/assets/7ef1007a-3caa-4b73-8120-61bf99a79019)
#### ![image](https://github.com/user-attachments/assets/be82f544-c337-445a-a2f8-8ac5588276d8)
### <br/>

---

### 다음으로는 NAT을 설정해야 한다.
### NAT (Network Address Translation)은 내부에서 라우팅하는 기술이다.
### openvpn은 내부에서 ip를 별도로 할당한다. 그리고 이 ip가 어떤 실제 서버 ip와 연결이 되는지 명시를 해줘야 한다.
### 왜냐면 서버 안에서는 그 ip와 인터넷이 연결이 되기 때문이다.
### 윈도우에서는 power shell을 관리자 권한으로 실행하고, internal ip address를 등록해줘야 한다.
### <br/>

### 내 net adapter를 확인하는 방법. 여기에서 Name이라고 써진 곳에 일단 OpenVPN TAP-Windows6이 등록되어 있어야 함
### 네트워크 연결 확인하는 곳에서도 확인 가능하다.
```
Get-NetAdapter
```
#### ![image](https://github.com/user-attachments/assets/124f9554-4ccc-466f-a4c0-e79440b10140)
#### ![image](https://github.com/user-attachments/assets/85c0403e-7cd7-4255-bfe8-6d38ca08c546)
### <br/>

### 그리고 ip 주소 확인 방법이다. ipconfig 같은 건데 좀 더 상세하게 알려준다.
### 여기서 OpenVPN TAP-Windows6를 찾는다.
```
Get-NetIPAddress
```
#### ![image](https://github.com/user-attachments/assets/56ed246a-0d89-4ba8-855b-f0cc51fe1471)
### <br/>

### NAT 등록 방법
```
New-NetNat -Name "OpenVPN_NAT" -InternalIPInterfaceAddressPrefix "[openvpn_IPv4_ip]/[subnet]"
```
### <br/>

### 등록된 NAT 확인 방법
```
Get-NetNat
```
#### ![image](https://github.com/user-attachments/assets/cb3c0c59-b8f8-4e68-b640-f170db12cc89)

