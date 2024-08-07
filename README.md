# EXE.Docker-Process
aws의 인스턴스를 만들어서 진행하였고 Ubuntu 22.04버전을 사용하였습니다!!

## AWS 인스턴스 생성
인스턴스 시작을 누르고 설정을 해줍니다!

<img width="920" alt="image" src="https://github.com/user-attachments/assets/4129ec6a-3de9-49a5-b56b-3cd66bc3a509">

저는 프리티어로 사용중이기 때문에 Ubuntu 서버를 선택하는 곳에서 프리티어 사용 가능이라고 되어있는 22.04버전을 선택하였습니다.

<img width="920" alt="image" src="https://github.com/user-attachments/assets/9551bafd-ecb1-4a1c-9d86-e6fe4e075972">

인스턴스 유형 또한 프리티어로 사용 가능한 t2.micro로 선택하였고 키 페어는 생성하여 설정해주었습니다.

<img width="920" alt="image" src="https://github.com/user-attachments/assets/2b7a2b67-ccf3-4701-9c47-b1c5a89da16b">

그 외에는 기본적으로 설정된 것을 사용하였습니다!

### 인스턴스 연결
다음으로 인스턴스 창에서 인스턴스 연결을 눌러줍니다!

<img width="957" alt="image" src="https://github.com/user-attachments/assets/8051e9f9-a27d-479d-bc53-7a5951fe6471">

저는 EC2 Instance Connect를 사용하여 연결해줬습니다!
<img width="920" alt="image" src="https://github.com/user-attachments/assets/698f3247-8e65-4efc-a5b1-5be7c4c9cf53">

아래와 같은 쉘이 뜨면 끝입니다!!
<img width="957" alt="image" src="https://github.com/user-attachments/assets/18df4841-0c59-4309-ab2e-e5180d75b56a">

## Docker 설치 및 확인
- [docker docs installation methods](https://docs.docker.com/engine/install/ubuntu/#installation-methods)를 참고하였습니다!!

## 실습 진행
### 이전 버전을 지우고 도커 레포지토리 등록

#### 이전 버전 지우기
```
for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do sudo apt-get remove $pkg; done
```

<img width="922" alt="image" src="https://github.com/user-attachments/assets/74d04466-9fe5-4d41-a521-759140aa5f19">


#### 도커 레포지토리 등록
```
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```

<img width="922" alt="image" src="https://github.com/user-attachments/assets/71a44549-bad0-4a09-a227-723d24352149">


#### 설치
```
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

<img width="922" alt="image" src="https://github.com/user-attachments/assets/ebbbe214-b22c-4fe1-9051-932495d0b5a0">

#### Docker Container 실행
- Docker Container 실행해줍니다! sleep 명령어를 사용하여 1시간동안 유지되도록 합니다!
```
docker run -d --name test-container ubuntu sleep 3600
```
##### 트러블 슈팅

<img width="922" alt="image" src="https://github.com/user-attachments/assets/efe12859-41bb-4557-aec0-37cbdd742cdd">

- 어제 시험해본다고 똑같은 컨테이너를 만들어서 문제가 발생했습니다.
- 해당 컨테이너를 삭제하고 다시 진행하겠습니다!
```
docker rm test-container
```
<img width="922" alt="image" src="https://github.com/user-attachments/assets/00d8ed01-6a11-45a8-94c1-7c2a713a7692">
 
##### 성공!

<img width="922" alt="image" src="https://github.com/user-attachments/assets/50fa492e-607e-4dfd-9a86-3b887dd7ea65">

#### 실행중인 컨테이너 확인
```
docker ps
```

<img width="922" alt="image" src="https://github.com/user-attachments/assets/64d432b8-b014-4aac-b87f-129bfa1d7683">

컨테이너 ID, 이미지, 생성 커맨드, 생성 시간, 상태, 컨테이너 이름 등이 표시됩니다!

#### 컨테이너의 프로세스 ID 확인
컨테이너의 프로세스 ID (PID)를 확인해보겠습니다!
```
docker inspect --format '{{.State.Pid}}' test-container
```
<img width="922" alt="image" src="https://github.com/user-attachments/assets/390932f3-e700-4cc9-aaea-413823994906">

#### 프로세스 ID로 호스트에서 컨테이너 프로세스 확인
저는 PID가 29918이었기 떄문에 해당 PID로 진행했습니다!
```
ps -p <컨테이너 PID>
```
<img width="922" alt="image" src="https://github.com/user-attachments/assets/fcb29a40-f6fb-432d-bd0e-9a98bb42009b">
해당 프로세스가 sleep 명령어로 실행되고 있음을 확인할 수 있었습니다!!

## 테스트 컨테이너 삭제
실습을 다 했으니 테스트 컨테이너는 삭제하겠습니다!
```
docker stop test-container
docker rm test-container
```
<img width="922" alt="image" src="https://github.com/user-attachments/assets/0ce66df2-44d1-4f0a-9ae5-c074dc1d1e7e">
