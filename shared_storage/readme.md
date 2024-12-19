### 241219
## 리눅스
### 리눅스같은 경우 NFS로 mount를 하면 되는데, 나는 현재 윈도우라서 윈로우로 진행하였고 나중에 설정할 일 있으면 따로 정리해야겠다.
### <br/><br/><br/>


## Windows 11 공유 드라이브 설정 방법
### 먼저 서버에서 설정해야 한다.
### 공유할 폴더 - 속성에 들어가서 공유 탭을 눌러 공유하기
### 아래 드라이브 경로를 복사해두자.
#### ![image](https://github.com/user-attachments/assets/b0b9057e-bab6-4b0a-980c-a8b175c361ab)
#### ![image](https://github.com/user-attachments/assets/4d8a2be5-1f0d-4564-aefa-8d98f26499a6)
### <br/>

### 내 PC - 우클릭 - 네트워크 드라이브 연결해서 테스트해보자.
#### ![image](https://github.com/user-attachments/assets/3e8439ef-2b0b-4a51-a587-2a3ecbe56842)
#### ![image](https://github.com/user-attachments/assets/2e92a3d1-f335-49ac-926b-ca84ebadf6a2)
### <br/>

### 연결이 이렇게 확인되면 준비 완료이다.
#### ![image](https://github.com/user-attachments/assets/aa7dcd8d-0c0d-4974-b72f-318810afccc2)
### <br/>

### 이제 client 쪽에서 vpn 접속하고, 네트워크 드라이브 연결하는데, 서버와 똑같이 입력하면 안 되고 아래와 같이 입력해야 한다.
```
\\[host ip]\[dir_name]
```


