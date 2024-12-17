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
#push "dhcp-option DNS 1.1.1.1"
#push "dhcp-option DNS 8.8.8.8"
push "dhcp-option DNS 10.8.0.1"
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
### C:\\Program Files\\OpenVPN\\easy-rsa\\EasyRSA-Start.bat를 실행한다.
