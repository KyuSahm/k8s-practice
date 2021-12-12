# kubernetes
- 유튜브 이성미 강사님의 [따라하면서 배우는 쿠버네티스](https://www.youtube.com/watch?v=6n5obRKsCRQ&list=PLApuRlvrZKohaBHvXAOhUD-RxD0uQ3z0c)를 주로 정리
- [이성미 강사님의 GitHub 페이지](https://github.com/237summit)
- ``쿠버네티스 입문(90가지 예제로 배우는 컨테이너 관리 자동화 표준)``을 정리

## 설치없이 쿠버네티스 사용하기
- 카타코다 쿠버네티스 플레이그라운드
  - https://www.katacoda.com/courses/kubernetes/playground
  - Master, node1이 구성되어 있어 바로 사용가능
- Play with Kubernetes
  - docker에서 제공. docker hub 계정으로 로그인
  - https://labs.play-with-k8s.com/
  - 4시간 사용 가능.
  - Master, Worker Noder를 직접 구성한 후 사용가능

## 쿠버네티스 설치해서 사용하기
### 윈도우에 WSL2을 여러 개 설치하는 방법 (실패)
- WSL이란?
  - Windows Subsystem for Linux
  - Hypervisor 위에 윈도우 NT 커널과 리눅스 커널을 각각 올리는 방식
![WSL2](./images/WSL.png)  
- 상세 절차는 [MS Document를 참조](https://docs.microsoft.com/en-us/windows/wsl/install-manual)
#### Step 1: Enable the Windows Subsystem for Linux
- 윈도우에 Linux를 깔기 전에 "Windows Subsystem for Linux" optional feature를 Enable함
- Windows PowerShell에서 아래의 명령 입력
```powerShell
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
```
#### Step 2: Check requirements for running WSL2
- Windows 10에서 운영 중이어야 함
- For x64 systems: Version 1903 or higher, with Build 18362 or higher.
- For ARM64 systems: Version 2004 or higher, with Build 19041 or higher.
- Builds lower than 18362 do not support WSL 2. Use the Windows Update Assistant to update your version of Windows.

#### Step 3: Enable Virtual Machine feature
- ``Virtual Machine Platform" optional feature를 enable함
```powerShell
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
```
#### Step 4: Download the Linux kernel update package
- 최신 패키지를 다운로드
  - [WSL2 Linux kernel update package for x64 machines](https://wslstorestorage.blob.core.windows.net/wslblob/wsl_update_x64.msi)
- 해당 패키지를  설치
#### Step 5: Set WSL 2 as your default version
- 새로운 리눅스를 깔 때, 기본 버전으로서 WSL2를 지정함
```powerShell
wsl --set-default-version 2
```
#### Step 6: Install a lot of WSL on Windows 10
- Ubuntu-20.04[https://aka.ms/wslubuntu2004]를 다운로드
- 7zip를 이용하여 압축을 푼다
  - 만약, appx 확장자를 가진 다운로드 파일을 클릭하면 설치가 됨.
  - 설치를 하게 되면 하나의 Linux가 설치됨
###### Step 6-1: WSL 명령어를 이용하여 설치하기
```powerShell
wsl --import Ubuntu-20.04-Master D:\WSL\Data\Ubuntu-20.04-Master D:\Tools\Ubuntu\Ubuntu_2004.2020.424.0_x64\install.tar.gz
wsl --list
wsl --distribution Ubuntu-20.04-Master
```
- 출처: https://godsman.tistory.com/entry/윈도우즈에-WSL-여러-개-설치하기 [선수로 산다, 때론 좋은 코치로]     
###### Step 6-2: 사용자 추가
```powerShell
useradd gusami
passwd gusami
usermod -aG sudo gusami
```
###### Step 6-3: 추가된 사용자로 wsl 실행 하기
```powerShell
wsl --distribution Ubuntu-20.04-Master --user gusami
```
### WSL의 명령 참고 자료
- [Microsoft Document](https://docs.microsoft.com/ko-kr/windows/wsl/basic-commands)
#### Linux 배포판 생성
```powerShell
wsl --import <Distribution Name> <InstallLocation> <FileName>
wsl --import k8s-master D:\WSL\Data\k8s-master D:\Tools\Ubuntu\Ubuntu_2004.2020.424.0_x64\install.tar.gz
```
#### 특정 Linux 배포판 실행
```powerShell
wsl --distribution <Distribution Name> --user <User Name>
wsl --distribution Ubuntu-20.04-Master 
```
#### 특정 Linux 배포판 종료
```powerShell
wsl --terminate <Distribution Name>
wsl --terminate Ubuntu-20.04-Master 
```
#### 모든 Linux 배포판과 유틸리티 가상 머신을 종료
```powerShell
wsl --shutdown
```
#### 설치된 Linux 배포판 나열
```powerShell
wsl --list --verbose
```
#### 사용 가능한 Linux 배포판 나열
- 온라인 스토어를 통해 받을 수 있는 Linux 배포판 목록을 나열함
```powerShell
wsl --list --online
```
#### Linux 배포판 등록 취소 또는 제거
```powerShell
wsl --unregister <Distribution Name>
wsl --unregister Ubuntu-20.04-Master
```
#### 디스크 또는 디바이스 탑재
```powerShell
wsl --mount <DiskPath>
```
- ``<DiskPath>``를 디스크가 있는 디렉터리\파일 경로로 바꿔서 모든 WSL2 배포판에 물리적 디스크를 연결하고 탑재합니다. WSL 2에 Linux 디스크 탑재를 참조하세요. 다음 옵션을 사용할 수 있습니다.
 - wsl --mount --bare: WSL2에 디스크를 연결하지만 탑재하지는 않습니다.
 - wsl --mount --type ``<Filesystem>``: 디스크를 탑재할 때 사용되는 파일 시스템 유형입니다. 지정하지 않으면 기본값은 ext4입니다. 이 명령은 wsl --mount -t   ``<Filesystem>``으로 입력할 수도 있습니다. blkid ``<BlockDevice>`` 명령을 사용하여 파일 시스템 형식을 검색할 수 있습니다(예: blkid ``<dev/sdb1>``).
 - wsl --mount --partition ``<Partition Number>``: 탑재할 파티션의 인덱스 번호입니다. 지정하지 않으면 전체 디스크가 기본값입니다.
 - wsl --mount --options ``<MountOptions>``: 디스크를 탑재할 때 포함할 수 있는 몇 가지 파일 시스템 관련 옵션이 있습니다. wsl --mount -o "data-ordered" 또는 wsl --mount -o "data=writeback 같은 ext4 탑재 옵션을 예로 들 수 있습니다. 그러나 현재는 파일 시스템 관련 옵션만 지원됩니다. ro, rw 또는 noatime과 같은 일반 옵션은 지원되지 않습니다.
 - wsl --unmount ``<DiskPath>``: 모든 WSL 2 배포판에서 디스크를 탑재 해제하고 분리합니다. ``<DiskPath>``가 포함되지 않으면 이 명령은 탑재된 모든 디스크를 탑재 해제하고 분리합니다.

### Virtual Box를 이용하여 여러개의 VM 설치 (성공)
- 참조: [나만의 k8s 클러스터 구축하기](https://coffeewhale.com/kubernetes/cluster/virtualbox/2020/08/31/k8s-virtualbox/)
- ``VirtualBox 6.1.28 platform packages`` 설치
- 아래 그림의 형태로 노드들을 구성
![Node Configuration](./images/Node_Configuration.png)
#### virtual Box 설치
- [https://www.virtualbox.org/wiki/Downloads](https://www.virtualbox.org/wiki/Downloads)
  - ``VirtualBox platform packages > Windows hosts`` 선택해서 다운로드
#### Ubuntu 20.04 LTS 설치
- [https://releases.ubuntu.com/20.04/](https://releases.ubuntu.com/20.04/)
  - ``64-bit PC (AMD64) desktop image`` 선택해서 다운로드
#### 네트워크 및 노드 설정
###### NAT 네트워크 구성
- Virtual Box 실행 > 파일 > 환경설정 > 네트워크
- 새로운 NAT 네트워크를 추가
- 추가된 ``NatNetwork`` 더블 클릭
- 다음과 같이 설정
  - 네트워크 이름: k8s-network
  - 네트워크 CIDR: 10.0.1.0/24
  - 네트워크 옵션: DHCP 지원 (체크)
#### Master Node 설치
- Virtual Box 실행 > 머신 > 새로만들기
- 다음과 같이 설정
  - 이름: master
  - 머신 폴더: 디스크 용량이 넉넉한 드라이버를 선택
  - 종류: Linux
  - 버전: Ubuntu (64-bit)
- 메모리 크기: 3GB (k3s 스펙상 512MB도 가능하나 원활한 테스트 진행을 위해)
- 새 가상 하드 디스크 만들기
- VDI(VirtualBox 디스크 이미지)
- 하드디스크: 고정 크기
- 하드디스크 크기: 20GB
- 만들기
###### VM 설정
- Virtual Box 실행 > 머신 > 설정
  - 일반 > 고급 > 클립 보드 공유: 양방향
  - 네트워크 > 어댑터 1
    - 네트워크 어댑터 사용하기: 체크
    - 다음에 연결됨: ``NAT 네트워크``(NAT라고만 적혀 있는 것은 다른 네트워크)
    - 네트워크 이름: k8s-network
###### VM 시작 및 우분투 설치
- master VM을 더블클릭하여 서버를 구동
  - 시동 디스크 선택: 다운로드 받은 우분투 20.04 이미지를 선택
  - 시작
  - English > Install Ubuntu (사용자의 취향에 맞게 설정합니다.)
  - Keyboard layout: English > English(US) (사용자의 취향에 맞게 설정합니다.) > Continue
  - Minimal installation > Download updates (체크 해제) > Continue
  - Erase disk and install Ubuntu (사용자의 취향에 맞게 설정합니다.) > Install Now
  - Write the changes to disk? > Continue
  - Where are you? (Seoul) > Continue
  - Who are you?
    - Your name: gusami
    - Your computer’s name: master
    - Pick a username: gusami
    - Password: e*****3
    - Installation Complete > Restart Now
    - Please remove the installation medium, then press ENTER > ENTER

###### Master Node VM 시작 시 ``가상머신 master의 세션을 열 수 없습니다`` 해결 방법
- 최신의 ``Oracle VM VirtualBox Extension Pack`` 설치
  - 도움말에서 Virtual Box의 버전 확인
  - Virtual box 다운로드 사이트에서 최신의 Extension Pack을 다운로드 받음
  - Virtual Box > 메뉴 > 파일 > 환경설정 > 확장
    - 기존 패키지를 삭제 후, 새 패키지를 설치
    - 상위 버전의 패키지는 하위 버전과의 호환성을 보장
- 명령어를 이용해서 Hyper-v 기능 off
  - open a cmd with admin privilige
  - turn on
    - bcdedit /set hypervisorlaunchtype auto
  - turn off
    - bcdedit /set hypervisorlaunchtype off
  - reboot

###### 네트워크 설정
![WSL2](./images/Master_Ubuntu_Network.png)
- Master Node의 Ubuntu 서버를 접속하여 네트워크를 설정
  - 우측 상단, 네트워크 아이콘 클륵
  - Settings 클릭
  - 톱니바퀴 아이콘 클릭
  - 이미 10.0.1.4로 IP가 자동으로 잡혀져있는 것을 확인할 수 있지만 IP를 명시적으로 고정시키기 위해 IPv4 수동 설정
  - IPv4 탭 클릭 > Manual 선택
    - Address: 10.0.1.4
    - Netmask: 255.255.255.0
    - Gateway: 10.0.1.1
    - DNS: 8.8.8.8
  - Apply 버튼 클릭
  - 네트워크 반영을 위해 토글 버튼을 눌러 잠깐 껐다가 다시 켜줌
- ``CTRL + ALT + T``를 눌러 터미널을 열고, 네트워크 설정이 정상적으로 동작하는지 확인 위해 아래의 명령을 수행  
```bash
sudo apt update
```
- 인터넷이 정상적으로 작동하면 VM 복제를 위해 종료
```bash
sudo shutdown now
```
#### Worker Node 설치
###### worker 복제
- 이미 생성한 master 노드를 복제
- 종료된 master 노드를 우클릭하여 복제 메뉴를 클릭
  - 이름: worker-1
  - 경로: master VM을 저장한 위치에 저장
  - MAC 주소 정책: 모든 네트워크 어댑터의 새 MAC 주소 생성
  - 나머지 전부 체크 해제 > 다음
  - 복제 방식: 완전한 복제 > 복제
- 복제가 완료되면 master, worker 노드 둘다 시작
###### Host명 변경 및 네트워크 설정
- worker 노드로 접속하여 Host명 변경 및 네트워크 설정
- master 노드를 복제했기 때문에 Host명이 master로 설정되어 있음
- CTRL + ALT + T를 눌러 터미널을 열어 다음과 같은 명령을 수행
```bash
sudo hostname worker-1
sudo sh -c 'echo worker-1 > /etc/hostname' // 실패 시 직접 파일을 열어서 수정
sudo sed -i 's/master/worker-1/g' /etc/hosts
# 터미널을 종료합니다.
exit
```
- Host명 변경 후 네트워크 세팅으로 들어가 다음과 같이 설정
  - IPv4 탭 클릭 > Manual 선택
  - Address: 10.0.1.5
  - Netmask: 255.255.255.0
  - Gateway: 10.0.1.1
  - DNS: 8.8.8.8
  - Apply 버튼 클릭
  - 네트워크 반영을 위해 토글 버튼을 눌러 잠깐 껐다가 다시 켜줌
###### DHCP
- DHCP는 Dynamic Host Configuration Protocol의 약자
- 호스트의 IP주소와 각종 TCP/IP 프로토콜의 기본 설정을 클라이언트에게 자동적으로 제공
- 네트워크에 사용되는 IP주소를 DHCP서버가 중앙집중식으로 관리하는 클라이언트/서버 모델
- DHCP지원 클라이언트는 네트워크 부팅과정에서 DHCP서버에 IP주소를 요청해서 얻어 감
- 네트워크 안의 컴퓨터에 자동으로 네임서버 주소, IP 주소, 게이트웨이 주소를 할당
- 클라이언트에게 일정 기간 임대를 하는 동적 주소 할당 프로토콜
- 장점
  - PC의 수가 많거나 PC 자체 변동사항이 많은 경우, IP 설정이 자동으로 되기 때문에 효율적으로 사용 가능
  - IP를 자동으로 할당해주기 때문에 IP 충돌을 막을 수 있음
- 단점
  - DHCP 서버에 의존되기 때문에 서버가 다운되면 IP 할당
- 출처: [개발자를 꿈꾸는 프로그래머](https://jwprogramming.tistory.com/35)

## kubeadm을 이용한 쿠버네티스 설치 - 온프레미스
 1. Docker Install
 2. Kubernetes Install
   1. 설치 전 환경설정
   2. kubeadm, kubectl, kubelet 설치
   3. control-plane 구성
   4. worker node 구성
   5. 설치 확인
#### Docker Install
- k8s-master, k8s-node1, k8s-node2 리눅스 시스템들에 모두  Docker를 Install해야 함
- https://docs.docker.com/ >> Download and install >> Docker for Linux >> 왼쪽 창의 ``Installation per distro`` >> ``Install on Ubuntu`` 에 접속
- 해당 페이지의 ``Install using the repository`` 절차를 수행
1. Setup the Repository
```bash
# 1. Update the apt package index and install packages to allow apt to use a repository over HTTPS:
$sudo apt-get update
$sudo apt-get install -y ca-certificates curl gnupg lsb-release
# 2. Add Docker’s official GPG key:  
$curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
# 3. Use the following command to set up the stable repository. To add the nightly or test repository, add the word nightly or test (or both) after the word stable in the commands below. Learn about nightly and test channels.
$echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```
2. Install Docker Engine
```bash
#1. Update the apt package index, and install the latest version of Docker Engine and contained, or go to the next step to install a specific version:  
$sudo apt-get update
$sudo apt-get install -y docker-ce docker-ce-cli containerd.io
#3. Verify that Docker Engine is installed correctly by running the hello-world image.
$sudo systemctl enable docker  // on WSL2 => $sudo systemctl enable docker
$sudo systemctl start docker   // on WSL2 => $sudo service docker start
$sudo docker version
```
2-1. Install Docker Engine with Specific version(번외)
```bash
#1. Update the apt package index, and install the latest version of Docker Engine and contained, or go to the next step to install a specific version:  
$sudo apt-get update
$sudo apt-get install -y docker-ce docker-ce-cli containerd.io
#2. To install a specific version of Docker Engine, list the available versions in the repo, then select and install:
  #a. List the versions available in your repo:
$apt-cache madison docker-ce
  #b. Install a specific version using the version string from the second column, for example, 5:18.09.1~3-0~ubuntu-xenial.
$sudo apt-get install docker-ce=<VERSION_STRING> docker-ce-cli=<VERSION_STRING> containerd.io  
#3. Verify that Docker Engine is installed correctly by running the hello-world image.
$sudo docker run hello-world
3. Upgrade Docker Engine(번외)
- To upgrade Docker Engine, first run ``sudo apt-get update``, then follow the installation instructions, choosing the new version you want to install.

4. Docker 컨테이너의 cgroup 관리에 systemd를 사용하도록 도커 데몬을 구성
```bash
sudo mkdir /etc/docker
cat <<EOF | sudo tee /etc/docker/daemon.json
{
  "exec-opts": ["native.cgroupdriver=systemd"],
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "100m"
  },
  "storage-driver": "overlay2"
}
EOF
```
#### Kubernetes Install
- kubernetes.io 사이트 접속 > Documentation 선택 > Install the kubeadm setup tool 선택
###### 설치전 환경설정
1. root 계정 활성화
- 기본적으로 Ubuntu는 root계정이 locked 상태임
- 아래와 같은 명령어들을 사용해서 unlock 시킴
- 이제부터는 root 계정으로 명령어들을 수행
```bash
$sudo passwd root
$su -
```
2. Swap disabled. You MUST disable swap in order for the kubelet to work properly.
```bash
$swapoff -a && sed -i '/swap/s/^/#/' /etc/fstab
```
2. 브릿지 네트워크을 Listen할 수 있도록 지원하기 위한 설정
- root 계정으로 진행
```bash
cat <<EOF | tee /etc/modules-load.d/k8s.conf
br_netfilter
EOF

cat <<EOF | tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
sysctl --system
```
3. 방화벽 해제
- 방화벽은 일반적으로 네트워크에서 k8s앞단에 위치한 서버에 존재하기 때문에 필요없음
```bash
$systemctl stop firewalld 
$systemctl disable firewalld
```
###### kubeadm, kubectl, kubelet 역할
- kubeadm
  - the command to bootstrap the cluster
  - k8s 전체를 운영하고 관리해주는 역할을 하는 명령어
- kubelet
  - the component that runs on all of the machines in your cluster and does things like starting pods and containers
  - k8s의 컨테이너를 조작하고, master와 통신에 사용하는 데몬
- kubectl
  - the command line util to talk to your cluster
  - k8s를 제어하는 명령어를 수행

###### kubeadm, kubectl, kubelet 설치 절차
1. Update the apt package index and install packages needed to use the Kubernetes apt repository
```bash
apt-get update
apt-get install -y apt-transport-https ca-certificates curl
```
2. Download the Google Cloud public signing key
```bash
curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg
```
3. Add the Kubernetes apt repository:
```bash
echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | tee /etc/apt/sources.list.d/kubernetes.list
```
4. Update apt package index, install kubelet, kubeadm and kubectl, and pin their version
```bash
apt-get update
apt-get install -y kubelet kubeadm kubectl
apt-mark hold kubelet kubeadm kubectl
```

5. restarting the kubelet
```bash
systemctl start kubelet
systemctl enable kubelet
```
###### Cluster 생성하기(single master and multi worker nodes)
- 참조 - [Creating a cluster with kubeadm](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/)
- VirtualBox에서 모든 노드에 대해서 CPU를 두 개 이상 할당해 줌
- Control-plane 구성하기 
  - master node에서만 아래의 명령 수행(중요!!!)
  - 이 명령어를 통해서 master node의 ``API``, ``scheduler``, ``controller``, ``CoreDNS`` 컴포넌트들이 구성
```bash
$sudo kubeadm init
```  
![k8s_cluster](./images/K8s_cluster.png)

- 일반 유저로 클러스터를 Start하기 위해서는 master node에서 아래의 명령어를 수행
```bash  
#To start using your cluster, you need to run the following as a regular user:
$mkdir -p $HOME/.kube
$sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
$sudo chown $(id -u):$(id -g) $HOME/.kube/config
```
- 일반 유저가 아닌 root user로 클러스터를 Start하기 위해서는 master node에서 아래의 명령어를 .bashrc에 추가
```bash
#Alternatively, if you are the root user, you can run:
export KUBECONFIG=/etc/kubernetes/admin.conf
```
- ``kubectl``로 노드 정보를 아래와 같이 가져오는지 확인
  - 만약, port 관련 에러가 난다면 시스템 리부팅
```bash
gusami@master:~$ kubectl get nodes
# or
root@master:~$ kubectl get nodes
```
###### Pod Network 설치
- AddOn으로 여러 제품들 중 하나를 설치
- master 노드에 Weave Net works를 설치
```bash
gusami@master:~$ kubectl apply -f "https://cloud.weave.works/k8s/net?k8s-version=$(kubectl version | base64 | tr -d '\n')"
$ kubectl get nodes
```
###### Cluster에 조인하기
- Worker node들에서 조인 명령 수행(중요!!!)
  - 해당 인증서 해쉬 값은 ``kubeadm init`` 수행 할 때, 주어지는 값
```bash  
#Then you can join any number of worker nodes by running the following on each as root:
gusami@worker-2:~$sudo kubeadm join 10.0.1.4:6443 --token u9mrhu.g7n13qbgz67wg0kj \
	--discovery-token-ca-cert-hash sha256:2fddaed3f956819bff808814053fe95c64b0b55612b430b65622a38b038dd3bd 
```
- 마지막으로, master 노드에서 정상적으로 조인 되었는지 확인
```bash
gusami@master:~$ kubectl get nodes -o wide
NAME       STATUS   ROLES                  AGE    VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION      CONTAINER-RUNTIME
master     Ready    control-plane,master   49m    v1.22.3   10.0.1.4      <none>        Ubuntu 20.04.3 LTS   5.11.0-40-generic   docker://20.10.10
worker-1   Ready    <none>                 103s   v1.22.3   10.0.1.5      <none>        Ubuntu 20.04.3 LTS   5.11.0-40-generic   docker://20.10.10
worker-2   Ready    <none>                 32s    v1.22.3   10.0.1.6      <none>        Ubuntu 20.04.3 LTS   5.11.0-40-generic   docker://20.10.10
gusami@master:~$ kubectl get pods --all-namespaces
NAMESPACE     NAME                             READY   STATUS    RESTARTS        AGE
kube-system   coredns-78fcd69978-nz4fr         1/1     Running   0               52m
kube-system   coredns-78fcd69978-vsnzh         1/1     Running   0               52m
kube-system   etcd-master                      1/1     Running   1 (23m ago)     52m
kube-system   kube-apiserver-master            1/1     Running   1 (23m ago)     52m
kube-system   kube-controller-manager-master   1/1     Running   1 (23m ago)     52m
kube-system   kube-proxy-s9cp2                 1/1     Running   0               4m36s
kube-system   kube-proxy-vscnf                 1/1     Running   1 (23m ago)     52m
kube-system   kube-proxy-wsc4f                 1/1     Running   0               3m25s
kube-system   kube-scheduler-master            1/1     Running   1 (23m ago)     52m
kube-system   weave-net-kzxnd                  2/2     Running   1 (2m41s ago)   3m25s
kube-system   weave-net-tbcxg                  2/2     Running   1 (9m24s ago)   9m35s
kube-system   weave-net-zvqg4                  2/2     Running   0               4m36s
```
#### k8s 명령어 자동완성 on bash shell
- [k8s documentation 사이트](https://kubernetes.io/docs/home/)에서 ``bash completion cheat sheet``으로 검색
- 아래 명령어를 수행
  - 일반 유저와 root user 모두 수행
  - master node와 worker node들에 모두 수행
```bash
# setup autocomplete in bash into the current shell, bash-completion package should be installed first.
$source <(kubectl completion bash)
# add autocomplete permanently to your bash shellash 
$echo "source <(kubectl completion bash)" >> ~/.bashrc
$source <(kubeadm completion bash)
$echo "source <(kubeadm completion bash)" >> ~/.bashrc
```
#### SSH를 이용한 Virtual Box내부의 노드에 접근
1. master, worker-1, worker-2 노드에 ssh 설치
```bash
gusami@master:~$ sudo apt-get install ssh
```
2. SSH daemon이 시작되었는지 확인
```bash
gusami@master:~$ sudo lsof -i -P -n | grep LISTEN
sshd      33448            root    3u  IPv4 192286      0t0  TCP *:22 (LISTEN)
sshd      33448            root    4u  IPv6 192288      0t0  TCP *:22 (LISTEN)
```
3. VirtualBox에서 포트 포워딩 규칙 추가
  - host IP: 127.0.0.1
  - host port: 104
  - guest IP: 10.0.1.4
  - guest Port: 22

4. XShell에서 로긴 정보 추가
  - 연결 > 호스트: 127.0.0.1
  - 연결 > 포트번호: 104
  - 사용자 인증 > 사용자 이름: gusami
  - 연결 끊는법: exit 명령어를 사용
```bash
gusami@master:~$ exit
logout

Connection closed.
```  

## 쿠버네티스 클러스터
#### 쿠버네티스 클러스터를 직접 구성하는 도구
- kubeadm
  - 쿠버네티스에서 공식 제공하는 클러스터 생성/관리 도구
- kubespray
  - 쿠버네티스 클러스터를 배포하는 오픈 소스 프로젝트
  - multi master를 구성하기에 적합
  - 다양한 형식으로 쿠버네티스 클러스터 구성가능
  - 온프레미스에서 상용 서비스 클러스터 운용시 유용
  - 다양한 CNI 제공 - p60, p61  
#### CNI(Container Network Interface)
- Container간 통신을 지원하는 VxLAN. Pod Network이라고도 부름
- 다양한 종류의 플러그인이 존재
  - 플라넬(flannel), 칼리코(calico), 위브넷(weavenet)등이 존재
- 물리적인 노드의 네트워크와 Container내부의 네트워크간의 중간 매개자 역할 수행
  - 예를 들면, 아래 그림에서 node1의 UI Container가 CNI를 겨쳐 물리 네트워크를 통해서 전달
  - 물리 네트워크에서 받은 데이터를 CNI를 거쳐 node2의 Login 컨테이너로 전달

![cni](./images/CNI.png)
#### 쿠버네티스 클러스터 구성
- control plane(master node)
  - 워커 노드들의 상태를 관리하고 제어
  - single master
  - multi master(3, 5개의 master nodes)
- worker node
  - 도커 플랫폼을 통해 컨테이너를 동작하며 실제 서비스 제공

## 쿠버네티스로 컨테이너 실행하기
### kubectl이란?
- "쿠버네티스야 웹서버 3개 실행해줘"라고 하면, worker node들에 알아서 분배해서 실행해 줌
- 실제 실행할 컨테이너와 이미지와 옵션들과 개수들을 지정할 수 있음
![kubectl_concept](./images/kubectl_concept.png)  
#### kubectl 명령어 기본 구조
- command: 자원(object)에 실행 할 명령
  - ``create, get, delete, edit.....``
- TYPE: 자원의 타입
  - ``node, pod, service....``
- NAME: 자원의 이름(내가 지어준 이름)
  - 예: ``webserver``
- flags: 부가적으로 설정할 옵션
  - ``--help, -o options``
```bash
kubectl [command] [TYPE] [NAME] [flags]
```
```bash
kubectl get pod webserver -o wide
```
#### kubectl commands
- linux 명령어 옵션 
  - 옵션 중에 `-`가 인 경우, system5 계열의 명령을 사용할 경우
  - 옵션 중에 `--`가 인 경우, BSD 계열의 명령을 사용할 경우
  - linux는 system5 계열과 BSD 계열 모두를 지원
  - k8s의 명령어의 경우, 명령어에 따라서 ``-``인 경우도 있고, ``--``인 경우도 존재
  - k8s에서는 full name 옵션은 ``--``이고, 단축 옵션은 ``-``인 경우가 많음
```bash
$kubectl --help
$kubectl command --help

$kubectl run <자원이름> <옵션>
$kubectl create -f obj.yaml
$kubectl apply -f obj.yaml

$kubectl get <자원타입> <자원이름>
$kubectl edit <자원타입> <자원이름>
$kubectl describe <자원타입> <자원이름>

$kubectl delete pod main
```
- kubectl resource에 대한 약어 모음 보기
  - k8s 자동완성 기능을 설정한 후, ``<tab>`` 키를 사용하는 것도 좋은 방법
```bash
gusami@master:~$ kubectl api-resources 
NAME                              SHORTNAMES   APIVERSION                             NAMESPACED   KIND
bindings                                       v1                                     true         Binding
componentstatuses                 cs           v1                                     false        ComponentStatus
configmaps                        cm           v1                                     true         ConfigMap
endpoints                         ep           v1                                     true         Endpoints
events                            ev           v1                                     true         Event
limitranges                       limits       v1                                     true         LimitRange
namespaces                        ns           v1                                     false        Namespace
nodes                             no           v1                                     false        Node
persistentvolumeclaims            pvc          v1                                     true         PersistentVolumeClaim
persistentvolumes                 pv           v1                                     false        PersistentVolume
pods                              po           v1                                     true         Pod
podtemplates                                   v1                                     true         PodTemplate
replicationcontrollers            rc           v1                                     true         ReplicationController
resourcequotas                    quota        v1                                     true         ResourceQuota
secrets                                        v1                                     true         Secret
serviceaccounts                   sa           v1                                     true         ServiceAccount
services                          svc          v1                                     true         Service
mutatingwebhookconfigurations                  admissionregistration.k8s.io/v1        false        MutatingWebhookConfiguration
validatingwebhookconfigurations                admissionregistration.k8s.io/v1        false        ValidatingWebhookConfiguration
customresourcedefinitions         crd,crds     apiextensions.k8s.io/v1                false        CustomResourceDefinition
apiservices                                    apiregistration.k8s.io/v1              false        APIService
controllerrevisions                            apps/v1                                true         ControllerRevision
daemonsets                        ds           apps/v1                                true         DaemonSet
deployments                       deploy       apps/v1                                true         Deployment
replicasets                       rs           apps/v1                                true         ReplicaSet
statefulsets                      sts          apps/v1                                true         StatefulSet
tokenreviews                                   authentication.k8s.io/v1               false        TokenReview
localsubjectaccessreviews                      authorization.k8s.io/v1                true         LocalSubjectAccessReview
selfsubjectaccessreviews                       authorization.k8s.io/v1                false        SelfSubjectAccessReview
selfsubjectrulesreviews                        authorization.k8s.io/v1                false        SelfSubjectRulesReview
subjectaccessreviews                           authorization.k8s.io/v1                false        SubjectAccessReview
horizontalpodautoscalers          hpa          autoscaling/v1                         true         HorizontalPodAutoscaler
cronjobs                          cj           batch/v1                               true         CronJob
jobs                                           batch/v1                               true         Job
certificatesigningrequests        csr          certificates.k8s.io/v1                 false        CertificateSigningRequest
leases                                         coordination.k8s.io/v1                 true         Lease
endpointslices                                 discovery.k8s.io/v1                    true         EndpointSlice
events                            ev           events.k8s.io/v1                       true         Event
flowschemas                                    flowcontrol.apiserver.k8s.io/v1beta1   false        FlowSchema
prioritylevelconfigurations                    flowcontrol.apiserver.k8s.io/v1beta1   false        PriorityLevelConfiguration
ingressclasses                                 networking.k8s.io/v1                   false        IngressClass
ingresses                         ing          networking.k8s.io/v1                   true         Ingress
networkpolicies                   netpol       networking.k8s.io/v1                   true         NetworkPolicy
runtimeclasses                                 node.k8s.io/v1                         false        RuntimeClass
poddisruptionbudgets              pdb          policy/v1                              true         PodDisruptionBudget
podsecuritypolicies               psp          policy/v1beta1                         false        PodSecurityPolicy
clusterrolebindings                            rbac.authorization.k8s.io/v1           false        ClusterRoleBinding
clusterroles                                   rbac.authorization.k8s.io/v1           false        ClusterRole
rolebindings                                   rbac.authorization.k8s.io/v1           true         RoleBinding
roles                                          rbac.authorization.k8s.io/v1           true         Role
priorityclasses                   pc           scheduling.k8s.io/v1                   false        PriorityClass
csidrivers                                     storage.k8s.io/v1                      false        CSIDriver
csinodes                                       storage.k8s.io/v1                      false        CSINode
csistoragecapacities                           storage.k8s.io/v1beta1                 true         CSIStorageCapacity
storageclasses                    sc           storage.k8s.io/v1                      false        StorageClass
volumeattachments                              storage.k8s.io/v1                      false        VolumeAttachment
```
- kubectl 도움말 보기
  - ``kubectl --help``
```bash
gusami@master:~$ kubectl --help
kubectl controls the Kubernetes cluster manager.

 Find more information at: https://kubernetes.io/docs/reference/kubectl/overview/

Basic Commands (Beginner):
  create        Create a resource from a file or from stdin
  expose        Take a replication controller, service, deployment or pod and expose it as a new Kubernetes service
  run           Run a particular image on the cluster
  set           Set specific features on objects

Basic Commands (Intermediate):
  explain       Get documentation for a resource
  get           Display one or many resources
  edit          Edit a resource on the server
  delete        Delete resources by file names, stdin, resources and names, or by resources and label selector

Deploy Commands:
  rollout       Manage the rollout of a resource
  scale         Set a new size for a deployment, replica set, or replication controller
  autoscale     Auto-scale a deployment, replica set, stateful set, or replication controller

Cluster Management Commands:
  certificate   Modify certificate resources.
  cluster-info  Display cluster information
  top           Display resource (CPU/memory) usage
  cordon        Mark node as unschedulable
  uncordon      Mark node as schedulable
  drain         Drain node in preparation for maintenance
  taint         Update the taints on one or more nodes

Troubleshooting and Debugging Commands:
  describe      Show details of a specific resource or group of resources
  logs          Print the logs for a container in a pod
  attach        Attach to a running container
  exec          Execute a command in a container
  port-forward  Forward one or more local ports to a pod
  proxy         Run a proxy to the Kubernetes API server
  cp            Copy files and directories to and from containers
  auth          Inspect authorization
  debug         Create debugging sessions for troubleshooting workloads and nodes

Advanced Commands:
  diff          Diff the live version against a would-be applied version
  apply         Apply a configuration to a resource by file name or stdin
  patch         Update fields of a resource
  replace       Replace a resource by file name or stdin
  wait          Experimental: Wait for a specific condition on one or many resources
  kustomize     Build a kustomization target from a directory or URL.

Settings Commands:
  label         Update the labels on a resource
  annotate      Update the annotations on a resource
  completion    Output shell completion code for the specified shell (bash or zsh)

Other Commands:
  api-resources Print the supported API resources on the server
  api-versions  Print the supported API versions on the server, in the form of "group/version"
  config        Modify kubeconfig files
  plugin        Provides utilities for interacting with plugins
  version       Print the client and server version information

Usage:
  kubectl [flags] [options]

Use "kubectl <command> --help" for more information about a given command.
Use "kubectl options" for a list of global command-line options (applies to all commands).
```
- kubectl 특정 명령어의 도움말 보기
  - ``kubectl <command> --help``
```bash
gusami@master:~$ kubectl logs --help
Print the logs for a container in a pod or specified resource. If the pod has only one container, the container name is
optional.

Examples:
  # Return snapshot logs from pod nginx with only one container
  kubectl logs nginx
  
  # Return snapshot logs from pod nginx with multi containers
  kubectl logs nginx --all-containers=true
  
  # Return snapshot logs from all containers in pods defined by label app=nginx
  kubectl logs -l app=nginx --all-containers=true
  
  # Return snapshot of previous terminated ruby container logs from pod web-1
  kubectl logs -p -c ruby web-1
  
  # Begin streaming the logs of the ruby container in pod web-1
  kubectl logs -f -c ruby web-1
  
  # Begin streaming the logs from all containers in pods defined by label app=nginx
  kubectl logs -f -l app=nginx --all-containers=true
  
  # Display only the most recent 20 lines of output in pod nginx
  kubectl logs --tail=20 nginx
  
  # Show all logs from pod nginx written in the last hour
  kubectl logs --since=1h nginx
  
  # Show logs from a kubelet with an expired serving certificate
  kubectl logs --insecure-skip-tls-verify-backend nginx
  
  # Return snapshot logs from first container of a job named hello
  kubectl logs job/hello
  
  # Return snapshot logs from container nginx-1 of a deployment named nginx
  kubectl logs deployment/nginx -c nginx-1

Options:
      --all-containers=false: Get all containers' logs in the pod(s).
  -c, --container='': Print the logs of this container
  -f, --follow=false: Specify if the logs should be streamed.
      --ignore-errors=false: If watching / following pod logs, allow for any errors that occur to be non-fatal
      --insecure-skip-tls-verify-backend=false: Skip verifying the identity of the kubelet that logs are requested from.
In theory, an attacker could provide invalid log content back. You might want to use this if your kubelet serving
certificates have expired.
      --limit-bytes=0: Maximum bytes of logs to return. Defaults to no limit.
      --max-log-requests=5: Specify maximum number of concurrent logs to follow when using by a selector. Defaults to 5.
      --pod-running-timeout=20s: The length of time (like 5s, 2m, or 3h, higher than zero) to wait until at least one
pod is running
      --prefix=false: Prefix each log line with the log source (pod name and container name)
  -p, --previous=false: If true, print the logs for the previous instance of the container in a pod if it exists.
  -l, --selector='': Selector (label query) to filter on.
      --since=0s: Only return logs newer than a relative duration like 5s, 2m, or 3h. Defaults to all logs. Only one of
since-time / since may be used.
      --since-time='': Only return logs after a specific date (RFC3339). Defaults to all logs. Only one of since-time /
since may be used.
      --tail=-1: Lines of recent log file to display. Defaults to -1 with no selector, showing all log lines otherwise
10, if a selector is provided.
      --timestamps=false: Include timestamps on each line in the log output

Usage:
  kubectl logs [-f] [-p] (POD | TYPE/NAME) [-c CONTAINER] [options]

Use "kubectl options" for a list of global command-line options (applies to all commands).
```
- kubectl 전체 node 정보 보기
  - ``kubectl get nodes``
```bash
gusami@master:~$ kubectl get nodes -o wide
NAME       STATUS   ROLES                  AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION      CONTAINER-RUNTIME
master     Ready    control-plane,master   26h   v1.22.3   10.0.1.4      <none>        Ubuntu 20.04.3 LTS   5.11.0-40-generic   docker://20.10.10
worker-1   Ready    <none>                 26h   v1.22.3   10.0.1.5      <none>        Ubuntu 20.04.3 LTS   5.11.0-40-generic   docker://20.10.10
worker-2   Ready    <none>                 26h   v1.22.3   10.0.1.6      <none>        Ubuntu 20.04.3 LTS   5.11.0-40-generic   docker://20.10.1
```    
- kubectl 특정 노드의 상세 정보 보기
  - ``kubectl describe node <node명>``
```bash
gusami@master:~$ kubectl describe node master
Name:               master
Roles:              control-plane,master
Labels:             beta.kubernetes.io/arch=amd64
                    beta.kubernetes.io/os=linux
                    kubernetes.io/arch=amd64
                    kubernetes.io/hostname=master
                    kubernetes.io/os=linux
                    node-role.kubernetes.io/control-plane=
                    node-role.kubernetes.io/master=
                    node.kubernetes.io/exclude-from-external-load-balancers=
Annotations:        kubeadm.alpha.kubernetes.io/cri-socket: /var/run/dockershim.sock
                    node.alpha.kubernetes.io/ttl: 0
                    volumes.kubernetes.io/controller-managed-attach-detach: true
CreationTimestamp:  Sat, 13 Nov 2021 10:01:27 +0900
Taints:             node-role.kubernetes.io/master:NoSchedule
Unschedulable:      false
Lease:
  HolderIdentity:  master
  AcquireTime:     <unset>
  RenewTime:       Sun, 14 Nov 2021 12:52:57 +0900
Conditions:
  Type                 Status  LastHeartbeatTime                 LastTransitionTime                Reason                       Message
  ----                 ------  -----------------                 ------------------                ------                       -------
  NetworkUnavailable   False   Sun, 14 Nov 2021 12:40:49 +0900   Sun, 14 Nov 2021 12:40:49 +0900   WeaveIsUp                    Weave pod has set this
  MemoryPressure       False   Sun, 14 Nov 2021 12:50:44 +0900   Sat, 13 Nov 2021 10:01:24 +0900   KubeletHasSufficientMemory   kubelet has sufficient memory available
  DiskPressure         False   Sun, 14 Nov 2021 12:50:44 +0900   Sat, 13 Nov 2021 10:01:24 +0900   KubeletHasNoDiskPressure     kubelet has no disk pressure
  PIDPressure          False   Sun, 14 Nov 2021 12:50:44 +0900   Sat, 13 Nov 2021 10:01:24 +0900   KubeletHasSufficientPID      kubelet has sufficient PID available
  Ready                True    Sun, 14 Nov 2021 12:50:44 +0900   Sat, 13 Nov 2021 10:44:35 +0900   KubeletReady                 kubelet is posting ready status. AppArmor enabled
Addresses:
  InternalIP:  10.0.1.4
  Hostname:    master
Capacity:
  cpu:                2
  ephemeral-storage:  14897128Ki
  hugepages-2Mi:      0
  memory:             2027836Ki
  pods:               110
Allocatable:
  cpu:                2
  ephemeral-storage:  13729193143
  hugepages-2Mi:      0
  memory:             1925436Ki
  pods:               110
System Info:
  Machine ID:                 7bf4f9e7c32f4fdea81b4d8afb9f52c5
  System UUID:                f6adb473-6d29-6947-8eba-ef2d794d2af2
  Boot ID:                    438f5e8d-7869-49ae-9acd-ebca9c00f493
  Kernel Version:             5.11.0-40-generic
  OS Image:                   Ubuntu 20.04.3 LTS
  Operating System:           linux
  Architecture:               amd64
  Container Runtime Version:  docker://20.10.10
  Kubelet Version:            v1.22.3
  Kube-Proxy Version:         v1.22.3
Non-terminated Pods:          (8 in total)
  Namespace                   Name                              CPU Requests  CPU Limits  Memory Requests  Memory Limits  Age
  ---------                   ----                              ------------  ----------  ---------------  -------------  ---
  kube-system                 coredns-78fcd69978-nz4fr          100m (5%)     0 (0%)      70Mi (3%)        170Mi (9%)     26h
  kube-system                 coredns-78fcd69978-vsnzh          100m (5%)     0 (0%)      70Mi (3%)        170Mi (9%)     26h
  kube-system                 etcd-master                       100m (5%)     0 (0%)      100Mi (5%)       0 (0%)         26h
  kube-system                 kube-apiserver-master             250m (12%)    0 (0%)      0 (0%)           0 (0%)         26h
  kube-system                 kube-controller-manager-master    200m (10%)    0 (0%)      0 (0%)           0 (0%)         26h
  kube-system                 kube-proxy-vscnf                  0 (0%)        0 (0%)      0 (0%)           0 (0%)         26h
  kube-system                 kube-scheduler-master             100m (5%)     0 (0%)      0 (0%)           0 (0%)         26h
  kube-system                 weave-net-tbcxg                   100m (5%)     0 (0%)      200Mi (10%)      0 (0%)         26h
Allocated resources:
  (Total limits may be over 100 percent, i.e., overcommitted.)
  Resource           Requests     Limits
  --------           --------     ------
  cpu                950m (47%)   0 (0%)
  memory             440Mi (23%)  340Mi (18%)
  ephemeral-storage  0 (0%)       0 (0%)
  hugepages-2Mi      0 (0%)       0 (0%)
Events:
  Type    Reason                   Age                From        Message
  ----    ------                   ----               ----        -------
  Normal  Starting                 24h                kube-proxy  
  Normal  Starting                 20h                kube-proxy  
  Normal  Starting                 12m                kube-proxy  
  Normal  Starting                 24h                kubelet     Starting kubelet.
  Normal  NodeHasNoDiskPressure    24h (x8 over 24h)  kubelet     Node master status is now: NodeHasNoDiskPressure
  Normal  NodeHasSufficientPID     24h (x7 over 24h)  kubelet     Node master status is now: NodeHasSufficientPID
  Normal  NodeHasSufficientMemory  24h (x8 over 24h)  kubelet     Node master status is now: NodeHasSufficientMemory
  Normal  NodeAllocatableEnforced  24h                kubelet     Updated Node Allocatable limit across pods
  Normal  Starting                 20h                kubelet     Starting kubelet.
  Normal  NodeHasNoDiskPressure    20h (x8 over 20h)  kubelet     Node master status is now: NodeHasNoDiskPressure
  Normal  NodeHasSufficientMemory  20h (x8 over 20h)  kubelet     Node master status is now: NodeHasSufficientMemory
  Normal  NodeAllocatableEnforced  20h                kubelet     Updated Node Allocatable limit across pods
  Normal  NodeHasSufficientPID     20h (x7 over 20h)  kubelet     Node master status is now: NodeHasSufficientPID
  Normal  Starting                 12m                kubelet     Starting kubelet.
  Normal  NodeAllocatableEnforced  12m                kubelet     Updated Node Allocatable limit across pods
  Normal  NodeHasSufficientMemory  12m (x8 over 12m)  kubelet     Node master status is now: NodeHasSufficientMemory
  Normal  NodeHasNoDiskPressure    12m (x8 over 12m)  kubelet     Node master status is now: NodeHasNoDiskPressure
  Normal  NodeHasSufficientPID     12m (x7 over 12m)  kubelet     Node master status is now: NodeHasSufficientPID
```
#### command 사용법 실전 예제
1. Worker node중 하나에서 docker hub에서 이미지를 다운받아 container 생성한 후, 실행
```bash
# create nginx 1.14 version and open 80 port
gusami@master:~$ kubectl run webserver --image=nginx:1.14 --port 80
pod/webserver created
# worker-1 node에서 실행 중임, pod의 IP Address도 확인 가능
gusami@master:~$ kubectl get pods -o wide
NAME        READY   STATUS    RESTARTS   AGE     IP          NODE       NOMINATED NODE   READINESS GATES
webserver   1/1     Running   0          4m11s   10.44.0.1   worker-1   <none>           <none>
# webserver pod의 상세 정보
gusami@master:~$ kubectl describe pod webserver
Name:         webserver
Namespace:    default
Priority:     0
Node:         worker-1/10.0.1.5
Start Time:   Sun, 14 Nov 2021 13:05:12 +0900
Labels:       run=webserver
Annotations:  <none>
Status:       Running
IP:           10.44.0.1
IPs:
  IP:  10.44.0.1
Containers:
  webserver:
    Container ID:   docker://434ce103d5103ba7c695be13ac3a276cff437dbe4b480decab36c1fe64f58475
    Image:          nginx:1.14
    Image ID:       docker-pullable://nginx@sha256:f7988fb6c02e0ce69257d9bd9cf37ae20a60f1df7563c3a2a6abe24160306b8d
    Port:           80/TCP
    Host Port:      0/TCP
    State:          Running
      Started:      Sun, 14 Nov 2021 13:05:22 +0900
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-ljt56 (ro)
Conditions:
  Type              Status
  Initialized       True 
  Ready             True 
  ContainersReady   True 
  PodScheduled      True 
Volumes:
  kube-api-access-ljt56:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason     Age    From               Message
  ----    ------     ----   ----               -------
  Normal  Scheduled  5m5s   default-scheduler  Successfully assigned default/webserver to worker-1
  Normal  Pulling    5m3s   kubelet            Pulling image "nginx:1.14"
  Normal  Pulled     4m55s  kubelet            Successfully pulled image "nginx:1.14" in 8.005241046s
  Normal  Created    4m55s  kubelet            Created container webserver
  Normal  Started    4m55s  kubelet            Started container webserver
```
- Events 정보를 보면, docker hub에서 image를 다운로드 받아서 container을 생성하고, 시작한 것을 알수 있음
2. 명령어 웹브라우저인 ``curl``을 이용해서 webserver에 접속해 보자
- ``curl 10.44.0.1``
```bash
gusami@master:~$ curl 10.44.0.1
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```
- ``elinks``를 설치해서 확인해 보자
  - terminal에서 GUI 형태로 html tag를 적용해서 보여줌
  - 나올 때는 ``esc`` key를 누른 후, ``File menu``에서 ``exit`` 선택하면 됨
```bash
gusami@master:~$ sudo apt-get install elinks
gusami@master:~$ elinks 10.44.0.1
```
- 특정 pod의 정보만 보고 싶을 때
  - ``kubectl get pods <pod name>``
```bash
gusami@master:~$ kubectl get pods webserver -o wide
NAME        READY   STATUS    RESTARTS   AGE   IP          NODE       NOMINATED NODE   READINESS GATES
webserver   1/1     Running   0          17m   10.44.0.1   worker-1   <none>           <none>
```
3. 동일한 image의 pod를 여러 개를 한번에 생성 및 실행해 보자
- image에서 버전을 생략하면 기본적으로 ``latest`` 사용
- ``mainui``라는 이름을 가진 apache webserver httpd를 3개 생성해 보자
  - ``kubectl create deployment mainui --image=httpd --replicas=3``
```bash
gusami@master:~$ kubectl create deployment mainui --image=httpd --replicas=3
deployment.apps/mainui created

gusami@master:~$ kubectl get deployments.apps 
NAME     READY   UP-TO-DATE   AVAILABLE   AGE
mainui   3/3     3            3           75s

gusami@master:~$ kubectl get pods -o wide
NAME                     READY   STATUS    RESTARTS   AGE   IP          NODE       NOMINATED NODE   READINESS GATES
mainui-d77bf4d8f-ckrn6   1/1     Running   0          93s   10.44.0.2   worker-1   <none>           <none>
mainui-d77bf4d8f-jgks8   1/1     Running   0          93s   10.36.0.1   worker-2   <none>           <none>
mainui-d77bf4d8f-snb86   1/1     Running   0          93s   10.36.0.2   worker-2   <none>           <none>
webserver                1/1     Running   0          25m   10.44.0.1   worker-1   <none>           <none>

gusami@master:~$ kubectl describe deployments.apps mainui
Name:                   mainui
Namespace:              default
CreationTimestamp:      Sun, 14 Nov 2021 13:29:20 +0900
Labels:                 app=mainui
Annotations:            deployment.kubernetes.io/revision: 1
Selector:               app=mainui
Replicas:               3 desired | 3 updated | 3 total | 3 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  app=mainui
  Containers:
   httpd:
    Image:        httpd
    Port:         <none>
    Host Port:    <none>
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Available      True    MinimumReplicasAvailable
  Progressing    True    NewReplicaSetAvailable
OldReplicaSets:  <none>
NewReplicaSet:   mainui-d77bf4d8f (3/3 replicas created)
Events:
  Type    Reason             Age    From                   Message
  ----    ------             ----   ----                   -------
  Normal  ScalingReplicaSet  2m36s  deployment-controller  Scaled up replica set mainui-d77bf4d8f to 3  
```
- mainui pod 개수를 4개로 늘려보자
  - ``gusami@master:~$ kubectl edit deployments.apps mainui``
  - deployment.apps 구성 edit창에서 spec >> replicas의 개수를 수정한 후, 저장
  - 저장과 동시에 pod개수가 4개로 늘어남
```bash
# Please edit the object below. Lines beginning with a '#' will be ignored,
# and an empty file will abort the edit. If an error occurs while saving this file will be
# reopened with the relevant failures.
#
apiVersion: apps/v1
kind: Deployment
metadata:
  annotations:
    deployment.kubernetes.io/revision: "1"
  creationTimestamp: "2021-11-14T04:29:20Z"
  generation: 1
  labels:
    app: mainui
  name: mainui
  namespace: default
  resourceVersion: "20172"
  uid: 26d053db-551b-420f-8e32-a1cbd4fa5449
spec:
  progressDeadlineSeconds: 600
  replicas: 4
  revisionHistoryLimit: 10
  selector:
    matchLabels:
      app: mainui
  strategy:
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
    type: RollingUpdate
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: mainui
    spec:
      containers:
      - image: httpd
        imagePullPolicy: Always
        name: httpd
        resources: {}
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
      dnsPolicy: ClusterFirst
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      terminationGracePeriodSeconds: 30
status:
  availableReplicas: 3
  conditions:
  - lastTransitionTime: "2021-11-14T04:29:34Z"
    lastUpdateTime: "2021-11-14T04:29:34Z"
    message: Deployment has minimum availability.
    reason: MinimumReplicasAvailable
    status: "True"
    type: Available
  - lastTransitionTime: "2021-11-14T04:29:20Z"
    lastUpdateTime: "2021-11-14T04:29:34Z"
    message: ReplicaSet "mainui-d77bf4d8f" has successfully progressed.
    reason: NewReplicaSetAvailable
    status: "True"
    type: Progressing
  observedGeneration: 1
  readyReplicas: 3
  replicas: 3
  updatedReplicas: 3
```
```bash
gusami@master:~$ kubectl get pods
NAME                     READY   STATUS    RESTARTS   AGE
mainui-d77bf4d8f-ckrn6   1/1     Running   0          58m
mainui-d77bf4d8f-jgks8   1/1     Running   0          58m
mainui-d77bf4d8f-snb86   1/1     Running   0          58m
mainui-d77bf4d8f-wjgdb   1/1     Running   0          63s
webserver                1/1     Running   0          83m
```
- apache webserver 실행 확인
```bash
gusami@master:~$ curl 10.44.0.2
<html><body><h1>It works!</h1></body></html>
```
- 특정 pod를 yaml 또는 json 형태로 자세히 확인
  - ``kubectl get pods <pod name> -o yaml``
  - ``kubectl get pods <pod name> -o json``
```bash
gusami@master:~$ kubectl get pods mainui-d77bf4d8f-ckrn6 -o yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: "2021-11-14T04:29:20Z"
  generateName: mainui-d77bf4d8f-
  labels:
    app: mainui
    pod-template-hash: d77bf4d8f
  name: mainui-d77bf4d8f-ckrn6
  namespace: default
  ownerReferences:
  - apiVersion: apps/v1
    blockOwnerDeletion: true
    controller: true
    kind: ReplicaSet
    name: mainui-d77bf4d8f
    uid: c93ba43c-b299-4013-8d37-5073ca3a9877
  resourceVersion: "20156"
  uid: 9073a33a-58c4-47cc-9ecf-f5d967fc1e8b
spec:
  containers:
  - image: httpd
    imagePullPolicy: Always
    name: httpd
    resources: {}
    terminationMessagePath: /dev/termination-log
    terminationMessagePolicy: File
    volumeMounts:
    - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
      name: kube-api-access-9mqlg
      readOnly: true
  dnsPolicy: ClusterFirst
  enableServiceLinks: true
  nodeName: worker-1
  preemptionPolicy: PreemptLowerPriority
  priority: 0
  restartPolicy: Always
  schedulerName: default-scheduler
  securityContext: {}
  serviceAccount: default
  serviceAccountName: default
  terminationGracePeriodSeconds: 30
  tolerations:
  - effect: NoExecute
    key: node.kubernetes.io/not-ready
    operator: Exists
    tolerationSeconds: 300
  - effect: NoExecute
    key: node.kubernetes.io/unreachable
    operator: Exists
    tolerationSeconds: 300
  volumes:
  - name: kube-api-access-9mqlg
    projected:
      defaultMode: 420
      sources:
      - serviceAccountToken:
          expirationSeconds: 3607
          path: token
      - configMap:
          items:
          - key: ca.crt
            path: ca.crt
          name: kube-root-ca.crt
      - downwardAPI:
          items:
          - fieldRef:
              apiVersion: v1
              fieldPath: metadata.namespace
            path: namespace
status:
  conditions:
  - lastProbeTime: null
    lastTransitionTime: "2021-11-14T04:29:20Z"
    status: "True"
    type: Initialized
  - lastProbeTime: null
    lastTransitionTime: "2021-11-14T04:29:31Z"
    status: "True"
    type: Ready
  - lastProbeTime: null
    lastTransitionTime: "2021-11-14T04:29:31Z"
    status: "True"
    type: ContainersReady
  - lastProbeTime: null
    lastTransitionTime: "2021-11-14T04:29:20Z"
    status: "True"
    type: PodScheduled
  containerStatuses:
  - containerID: docker://16e4cfa30ea5f2b2a4c27ce136ca5d8a675979c4638acf77368acc92caddf58f
    image: httpd:latest
    imageID: docker-pullable://httpd@sha256:f70876d78442771406d7245b8d3425e8b0a86891c79811af94fb2e12af0fadeb
    lastState: {}
    name: httpd
    ready: true
    restartCount: 0
    started: true
    state:
      running:
        startedAt: "2021-11-14T04:29:30Z"
  hostIP: 10.0.1.5
  phase: Running
  podIP: 10.44.0.2
  podIPs:
  - ip: 10.44.0.2
  qosClass: BestEffort
  startTime: "2021-11-14T04:29:20Z"
```
4. 특정 Pod내의 container에 접속해서 웹페이지를 바꿔 보자
- ``kubectl exec <pod name> -it -- <첫 실행할 명령>``
- ``exec``는 pod 또는 container 내부의 명령어를 수행
```bash
# pod에 접속
gusami@master:~$ kubectl exec webserver -it -- /bin/bash
# index.html 수정
root@webserver:/#
root@webserver:/# cd /usr/share/nginx/html/
root@webserver:/usr/share/nginx/html# echo "Welcome gusami world" > index.html
root@webserver:/usr/share/nginx/html# cat index.html 
Welcome gusami world
root@webserver:/# exit
exit

gusami@master:~$ curl 10.44.0.1
Welcome gusami world
```
```bash
gusami@master:~$ kubectl exec --help
Execute a command in a container.

Examples:
  # Get output from running the 'date' command from pod mypod, using the first container by default
  kubectl exec mypod -- date
  
  # Get output from running the 'date' command in ruby-container from pod mypod
  kubectl exec mypod -c ruby-container -- date
  
  # Switch to raw terminal mode; sends stdin to 'bash' in ruby-container from pod mypod
  # and sends stdout/stderr from 'bash' back to the client
  kubectl exec mypod -c ruby-container -i -t -- bash -il
  
  # List contents of /usr from the first container of pod mypod and sort by modification time
  # If the command you want to execute in the pod has any flags in common (e.g. -i),
  # you must use two dashes (--) to separate your command's flags/arguments
  # Also note, do not surround your command and its flags/arguments with quotes
  # unless that is how you would execute it normally (i.e., do ls -t /usr, not "ls -t /usr")
  kubectl exec mypod -i -t -- ls -t /usr
  
  # Get output from running 'date' command from the first pod of the deployment mydeployment, using the first container
by default
  kubectl exec deploy/mydeployment -- date
  
  # Get output from running 'date' command from the first pod of the service myservice, using the first container by
default
  kubectl exec svc/myservice -- date

Options:
  -c, --container='': Container name. If omitted, use the kubectl.kubernetes.io/default-container annotation for
selecting the container to be attached or the first container in the pod will be chosen
  -f, --filename=[]: to use to exec into the resource
      --pod-running-timeout=1m0s: The length of time (like 5s, 2m, or 3h, higher than zero) to wait until at least one
pod is running
  -q, --quiet=false: Only print output from the remote session
  -i, --stdin=false: Pass stdin to the container
  -t, --tty=false: Stdin is a TTY

Usage:
  kubectl exec (POD | TYPE/NAME) [-c CONTAINER] [flags] -- COMMAND [args...] [options]

Use "kubectl options" for a list of global command-line options (applies to all commands).
```
- 특정 pod의 log 확인
  - ``kubectl logs <pod name>``  
```bash
gusami@master:~$ kubectl logs webserver
10.32.0.1 - - [14/Nov/2021:04:16:28 +0000] "GET / HTTP/1.1" 200 612 "-" "curl/7.68.0" "-"
10.32.0.1 - - [14/Nov/2021:04:19:53 +0000] "GET / HTTP/1.1" 200 612 "-" "ELinks/0.13.1 (textmode; Linux 5.11.0-40-generic x86_64; 209x52-2)" "-"
10.32.0.1 - - [14/Nov/2021:04:48:06 +0000] "GET / HTTP/1.1" 200 21 "-" "curl/7.68.0" "-"
```
5. 외부 사용자가 접속할 수 있도록 바꿔 보자
- 현재는 k8s 내부의 master와 worker node들에서만 접속 가능
```bash
gusami@worker-2:~$ curl 10.44.0.1
Welcome gusami world
```
- Port forwarding 기법을 이용해서 외부에서도 접속 가능하도록 설정
  - ``kubectl port-forward <pod name> <host port>:<guest port>``
```bash
# master node의 8080 port를 webserver의 80포트로 port forward함
gusami@master:~$ kubectl port-forward webserver 8080:80
Forwarding from 127.0.0.1:8080 -> 80
Forwarding from [::1]:8080 -> 80
```
```bash
# XShell에서 세션 복제 후, 접속
gusami@master:~$ curl localhost:8080
Welcome gusami world
```
###### dry-run 기능과 yaml 파일 생성하기
- ``dry-run``옵션을 통해 실제 생성하지 않고, Pod의 생성가능 여부를 확인
```bash
# 실제 생성을 하면 기존의 것과 충돌나서 실패
gusami@master:~$ kubectl run webserver --image=nginx:1.14 --port 80
Error from server (AlreadyExists): pods "webserver" already exists
# dry-run을 통해서 생성 가능 여부를 체크 가능
gusami@master:~$ kubectl run webserver --image=nginx:1.14 --port 80 --dry-run=client
pod/webserver created (dry run)
```
- yaml 파일 생성 방법
  - ``kubectl run <pod name> --image=<name:version> --dry-run=client -o yaml > **.yaml``
  - 생성 후, 편집에서 ``creationTimestamp``, ``resources``, ``dnsPolicy``, ``restartPolicy``, ``status`` 삭제
```bash
gusami@master:~$ kubectl run webserver --image=nginx:1.14 --port 80 --dry-run=client -o yaml > webserver.yaml
# 편집을 위해 VI로 Open
gusami@master:~$ vi webserver.yaml
```
- 실행 예제
  - yaml 파일 생성 후, 기존의 모든 pod들을 삭제하고 yaml파일을 이용해서 webserver 재생성
```bash
# webserver pod 삭제(내부적인 것들은 모두 삭제됨)
gusami@master:~$ kubectl delete pod webserver
pod "webserver" deleted
# mainui deployments.apps를 삭제. 관련 pods들이 모두 삭제됨
gusami@master:~$ kubectl delete deployments.apps mainui
deployment.apps "mainui" deleted
# 삭제 확인
gusami@master:~$ kubectl get pods
No resources found in default namespace.
# yaml 파일을 이용해서 pod 생성
gusami@master:~$ kubectl create -f webserver.yaml 
pod/webserver created
# 확인
gusami@master:~$ kubectl get pods -o wide
NAME        READY   STATUS    RESTARTS   AGE   IP          NODE       NOMINATED NODE   READINESS GATES
webserver   1/1     Running   0          13s   10.44.0.1   worker-1   <none>           <none
```
- pod 생성 방법
  - ``kubectl run`` 명령어
  - ``kubectl create -f <yaml 파일>`` 명령어
  - ``kubectl create deployment ...`` 명령어
## 쿠버네티스 Architecture
### 쿠버네티스 동작 원리
- 쿠버네티스에서 컨테이너 동작 Flow

![kubectl_deploy_flow](./images/kubectl_deploy_flow.png)
#### 쿠버네티스 컴포넌트
- Master Component
  - etcd
    - key-value 타입의 저장소
    - worker node들에 대한 상태 정보
      - H/W Resource 정보, worker node내의 docker container 상태, 다운로드한 image 상태를 저장
      - worker node들에 존재하는 kubelet의 도움을 받음 
      - kubelet에는 cAdvisor이라는 Container Monitor Tool이 존재
  - kube-apiserver
    - k8s API를 사용하도록 요청을 받고, 요청이 유효한지 검사
    - ``kubectl create ui ngnix``와 같은 명령어를 받음
  - kube-scheduler
    - Pod를 실행할 노드 선택
    - etcd의 정보를 받아서 사용
      - 특정 노드가 선택되면, kube-apiserver를 거쳐 worker node의 kubelet에 이미지의 생성과 실행을 요청
      - kubelet은 docker에게 특정 이미지의 실행을 요청
      - docker는 docker-hub으로부터 요청 받은 이미지를 다운 받아 container를 생성
  - kube-controller-manager
    - Pod를 관찰하며 개수를 보장
    - 만약, 특정 Worker node가 down된다면, 부족한 개수만큼의 container들을 생성하도록 kube-apiserver에 요청
- Worker node component
  - kubelet
    - 모든 노드에서 실행되는 k8s agent
    - 데몬 형태로 동작
  - kube-proxy
    - k8s의 network 동작을 관리
    - iptables rule를 구성
  - Container runtime
    - 컨테이너를 실행하는 엔진
    - docker, containerd, runc등의 컨테이너 엔진이 존재    
- Add On Component
  - 네트워크 AddOn
    - CNI - weave, calico, flaneId, kube-route
      - Container간의 통신을 지원
  - dns AddOn
    - coreDNS
  - 대시보드 AddOn
  - Container 자원 모니터링
    - cAdvisor
  - Clustor Logging
    - Container 로그, k8s 운영 로그들을 수집해서 중앙화
    - ELK(ElasticSearch, LogStash, Kibana), EFK(ElasticSearch, Fluentd Kibana), DataLog    

![k8s architecture](./images/K8S_architecture.png)
- 웹 UI Dashboard (별도 설치)

![k8s dashboard](./images/k8s_dashboard.png)
### namespace
- 클러스터 하나를 여러 개의 논리적인 단위로 나눠서 사용
 - 쿠버네티스 오브젝트를 묶는 하나의 가상공간 또는 그룹
 - 논리적으로 분리하는 것이지, 물리적으로 분리하는 것이 아님
 - 다른 namespace의 pod이더라도, 서로 통신이 가능
 - 클러스터의 장애가 발생한 경우, 모든 namespace가 타격을 입음
 - 쿠버네티스 클러스터 하나를 여러 팀이나 사용자가 함께 공유 가능
 - 용도에 다라 실행해야 하는 앱을 구분할 때 사용 
 - ``namespace = k8s API`` 종류 중 하나

![k8s_namespace](./images/k8s_namespaces.png)
![k8s_namespace](./images/k8s_namespaces_1.png)
#### namespace 생성
- CLI
  - command를 통해서 생성
  - yaml 파일을 통해서 생성
```bash
# command를 통한 생성
controlplane $kubectl create namespace blue
namespace/blue created
$kubectl get namespaces
```
- yaml
```bash
# yaml 파일을 통한 생성 (dry-run 옵션 사용)
controlplane $kubectl create namespace orange --dry-run=client -o yaml > orange-ns.yaml
# 필요없는 속성 제거
$vim orange.yaml
apiVersion: v1
kind: Namespace
metadata:
  name: orange
# yaml 파일을 통한 namespace 생성
controlplane $kubectl create -f orange-ns.yaml
namespace/orange created
# 생성된 namespace를 확인
controlplane $kubectl get namespace
NAME              STATUS   AGE
blue              Active   6m24s
default           Active   31m
kube-node-lease   Active   31m
kube-public       Active   31m
kube-system       Active   31m
orange            Active   29s
```
#### namespace 관리
- 기본적으로 4개의 namespace가 존재
  - default, kube-node-lease, kube-public, kube-system
##### namespace 관련 테스트   
```bash
# katacoda playground를 이용
# 현재의 namespace 정보 확인 (기본적으로 4개의 namespace가 존재)
controlplane $kubectl get namespace
NAME              STATUS   AGE
default           Active   3m36s
kube-node-lease   Active   3m38s
kube-public       Active   3m38s
kube-system       Active   3m38s
# 특정 namespace내의 pod를 확인
controlplane $kubectl get pod --namespace default
No resources found in default namespace
# yaml 파일 생성
controlplane $vi nginx.yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
    - image: nginx:1.14
      name: nginx
      ports:
      - containerPort: 80
      - containerPort: 443
# default namespace에서 pod 생성      
controlplane $kubectl create -f nginx.yaml
pod/mypod created
# default namespace의 pod 확인
controlplane $kubectl get pods -n default
NAME    READY   STATUS    RESTARTS   AGE
mypod   1/1     Running   0          3m1s
# kube-system namespace의 pod 확인
controlplane $kubectl get pods -n kube-system
NAME                                       READY   STATUS             RESTARTS   AGE
coredns-66bff467f8-2jmrx                   1/1     Running            0          18m
coredns-66bff467f8-58z5z                   1/1     Running            0          18m
etcd-controlplane                          1/1     Running            0          18m
katacoda-cloud-provider-5779df96d7-nwl8w   0/1     CrashLoopBackOff   7          18m
kube-apiserver-controlplane                1/1     Running            0          18m
kube-controller-manager-controlplane       1/1     Running            0          18m
kube-flannel-ds-amd64-4gfrh                1/1     Running            1          18m
kube-flannel-ds-amd64-d2jhh                1/1     Running            0          18m
kube-keepalived-vip-486lv                  1/1     Running            0          17m
kube-proxy-ttzmh                           1/1     Running            0          18m
kube-proxy-xpmf4                           1/1     Running            0          18m
kube-scheduler-controlplane                1/1     Running            0          18m
# 모든 namespace에서 작동 중인 pod를 확인
controlplane $ kubectl get pods --all-namespaces
NAMESPACE     NAME                                       READY   STATUS    RESTARTS   AGE
default       mypod                                      1/1     Running   0          9m1s
kube-system   coredns-66bff467f8-2jmrx                   1/1     Running   0          22m
kube-system   coredns-66bff467f8-58z5z                   1/1     Running   0          22m
kube-system   etcd-controlplane                          1/1     Running   0          23m
kube-system   katacoda-cloud-provider-5779df96d7-nwl8w   1/1     Running   9          22m
kube-system   kube-apiserver-controlplane                1/1     Running   0          23m
kube-system   kube-controller-manager-controlplane       1/1     Running   0          23m
kube-system   kube-flannel-ds-amd64-4gfrh                1/1     Running   1          22m
kube-system   kube-flannel-ds-amd64-d2jhh                1/1     Running   0          22m
kube-system   kube-keepalived-vip-486lv                  1/1     Running   0          22m
kube-system   kube-proxy-ttzmh                           1/1     Running   0          22m
kube-system   kube-proxy-xpmf4                           1/1     Running   0          22m
kube-system   kube-scheduler-controlplane                1/1     Running   0          23m
# blue namespace에서 pod 생성
controlplane $kubectl create -f nginx.yaml -n blue
pod/mypod created
# orange namespace에서 pod 생성
controlplane $kubectl create -f nginx.yaml -n orange
pod/mypod created
# orange namespace에서 pod 삭제
controlplane $kubectl delete pod mypod -n orange
# pod 생성 파일에 namespace를 명시할 수도 있음
controlplane $vi nginx.yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
  namespace: orange
spec:
  containers:
    - image: nginx:1.14
      name: nginx
      ports:
      - containerPort: 80
      - containerPort: 443
# orange namespace에서 pod 생성      
controlplane $kubectl create -f nginx.yaml
pod/mypod created
# 생성된 pod 확인
controlplane $ kubectl get pods -n orange
NAME    READY   STATUS    RESTARTS   AGE
mypod   1/1     Running   0          16s
# blue namespace 삭제 (내부적으로 존재하는 pod들도 모두 삭제됨)
controlplane $kubectl delete namespace blue
namespace "blue" deleted
```
##### namespace switch
- 기본으로 사용하는 namespace를 default가 아닌 다른 이름의 namespace로 switch
- 먼저 선행 조건으로 k8s의 config에 namespace들을 등록을 해줘야 함
  - namespace가 등록된 공간을 config의 context라고 부름
```bash
# 현재의 config의 context 정보 보기
controlplane $kubectl config get-contexts
CURRENT   NAME                          CLUSTER      AUTHINFO           NAMESPACE
*         kubernetes-admin@kubernetes   kubernetes   kubernetes-admin   
# 현재의 config의 전체 정보 보기
controlplane $kubectl config view
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://172.17.0.62:6443
  name: kubernetes
contexts:
- context:
    cluster: kubernetes
    user: kubernetes-admin
  name: kubernetes-admin@kubernetes
current-context: kubernetes-admin@kubernetes
kind: Config
preferences: {}
users:
- name: kubernetes-admin
  user:
    client-certificate-data: REDACTED
    client-key-data: REDACTED
# blue namespace를 blue@kubernetes라는 context에 등록해 줌
controlplane $kubectl config set-context blue@kubernetes --cluster=kubernetes --user=kubernetes-admin --namespace=blue
Context "blue@kuberentes" created
# 현재의 config 정보 전체 보기 (새로운 context가 생성됨)
controlplane $ kubectl config view
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://172.17.0.41:6443
  name: kubernetes
contexts:
- context:
    cluster: kubernetes
    namespace: blue
    user: kubernetes-admin
  name: blue@kubernetes
- context:
    cluster: kubernetes
    user: kubernetes-admin
  name: kubernetes-admin@kubernetes
current-context: kubernetes-admin@kubernetes
kind: Config
preferences: {}
users:
- name: kubernetes-admin
  user:
    client-certificate-data: REDACTED
    client-key-data: REDACTED
       
# 생성된 context와 등록된 namespace 정보 확인
controlplane $ kubectl config get-contexts
CURRENT   NAME                          CLUSTER      AUTHINFO           NAMESPACE
          blue@kubernetes               kubernetes   kubernetes-admin   blue
*         kubernetes-admin@kubernetes   kubernetes   kubernetes-admin      
# 현재의 context를 확인
controlplane $kubectl config current-context
kubernetes-admin@kubernetes
# context와 namespace를 스위치 (기본 namespace가 바뀜)
controlplane $kubectl config use-context blue@kubernetes
Switched to context "blue@kubernetes".
# 현재의 context를 확인
controlplane $kubectl config current-context
blue@kubernetes
# default context를 확인
controlplane $ kubectl config get-contexts
CURRENT   NAME                          CLUSTER      AUTHINFO           NAMESPACE
*         blue@kubernetes               kubernetes   kubernetes-admin   blue
          kubernetes-admin@kubernetes   kubernetes   kubernetes-admin
# 현재 context의 pod 확인             
controlplane $ kubectl get pods
NAME    READY   STATUS    RESTARTS   AGE
mypod   1/1     Running   0          46s

# context의 namespace이름 지정
controlplane $kubectl config set-context kubernetes-admin@kubernetes --namespace=default
Context "kubernetes-admin@kubernetes" modified.
# 확인
controlplane $ kubectl config get-contexts
CURRENT   NAME                          CLUSTER      AUTHINFO           NAMESPACE
*         blue@kubernetes               kubernetes   kubernetes-admin   blue
          kubernetes-admin@kubernetes   kubernetes   kubernetes-admin   default
```
### yaml 템플릿과 API
#### yaml 템플릿
- 사람이 쉽게 읽을 수 있는 데이터 직렬화 양식
- 기본 문법
  - 구조화된 데이터를 표현하기 위한 데이터 포맷
  - Python처럼 들여쓰기로 데이터 계층을 표기
  - 들여쓰기를 할 때에는 Tab이 아닌 **Space Bar**를 사용
  - 가독성이 좋아 설정 파일에 적합한 형식
  - Scalar 문법: **`:`을 기준으로 key:value를 설정
  - 배열 문법: `-`문자로 여러 개를 나열
  - 공식 사이트: http://yaml.org/
- kubernetes yaml example
```bash
$cat nginx-pod.yaml
apiVersion: v1
kind: Pod
parent:
  child1: first child
  key2:
    grandchild1: kim
  key3:
    - grandchild2:
      name: kim
    - grandchild3:
      name: lee # comment
# comment line        
```
#### API version
- alpha -> beta -> stable
- kubernetes Object 정의 시 apiVersion이 필요
- kubernetes가 update하는 API가 있으면 새로운 API가 생성됨
- API Object의 종류 및 버전
  - Deployment: apps/v1
  - Pod: v1
  - ReplicaSet: apps/v1
  - ReplicationController: v1
  - Service: v1
  - PersistentVolume: v1
- `kubectl explain [오브젝트타입명]`: 리소스의 정보 출력, **api version도 확인 가능**
```bash
controlplane $ kubectl explain pod
KIND:     Pod
VERSION:  v1

DESCRIPTION:
     Pod is a collection of containers that can run on a host. This resource is
     created by clients and scheduled onto hosts.

FIELDS:
   apiVersion   <string>
     APIVersion defines the versioned schema of this representation of an
     object. Servers should convert recognized schemas to the latest internal
     value, and may reject unrecognized values. More info:
     https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#resources

   kind <string>
     Kind is a string value representing the REST resource this object
     represents. Servers may infer this from the endpoint the client submits
     requests to. Cannot be updated. In CamelCase. More info:
     https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#types-kinds

   metadata     <Object>
     Standard object's metadata. More info:
     https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#metadata

   spec <Object>
     Specification of the desired behavior of the pod. More info:
     https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#spec-and-status

   status       <Object>
     Most recently observed status of the pod. This data may not be up to date.
     Populated by the system. Read-only. More info:
     https://git.k8s.io/community/contributors/devel/sig-architecture/api-conventions.md#spec-and-status
```
## Pod
### Pod 개념 및 사용하기
#### Container 정리
- docker container는 하나의 application과 동일
```bash
# source file 생성
$cat > app.js
const http = require('http');
const os = require('os');
console.log("Test server starting...");
var handler = function(req, res) {
  res.writeHead(200);
  res.end("Container Hostname: " + os.hostname() + "\n");  
};
var www = http.createServer(handler);
www.listen(8080);
# Docker file 생성
$cat > Dockerfile
FROM node:12
COPY app.js /app.js
# container 실행 시 "node app.js" 실행
ENTRYPOINT ["node", "app.js"] 
<Ctrl><d>
# Container build (Docker 이미지 파일 생성)
$docker build -t smlinux/appjs .
# Docker hub에 올림. 아래 명령은 docker hub에 smlinux계정내에 appjs로 올림
$docker push smlinux/appjs
```
![k8s_docker_image_generation](./images/k8s_docker_image_generation.png)
#### Pod란?
  - Container를 표현하는 k8s API의 최소 단위
  - Pod에는 하나 또는 여러 개의 컨테이너가 포함될 수 있음

![k8s pod](./images/k8s_pod.png)
#### Pod 생성하기
- kubectl run 명령으로 생성(CLI)
```bash
$kubectl run webserver --image=nginx:1.14
# port를 지정해도 되고 안해도 됨
$kubectl run web-1 --image=nginx:1.14 --port=80
```
- pod yaml을 이용해 생성
  - dry-run을 통해서 생성 가능
  - ``$kubectl get pods <pod name> -o yaml``을 통해서 생성 후 필요한 정보만 추출해서 생성 가능
```bash
# yaml 파일 생성
$cat pod-nginx.yaml
apiVersion: v1
kind: Pod
metadata:
  name: webserver-2
spec:
  containers:
  - name: nginx-container
    image: nginx:1.14
    imagePullPolicy: Always
    ports:
    - containerPort: 80
      protocol: TCP
# Pod 생성 및 실행
$kubectl create -f pod-nginx.yaml
# 현재 동작중인 Pod 확인
$kubectl get pods
$kubectl get pods -o wide
$kubectl get pods -o yaml
$kubectl get pods -o json
$kubectl get pods webserver -o yaml
# 2초마다 한번씩 지속적으로 명령 실행(linux watch 명령 이용)
$watch kubectl get pods -o wide
NAME        READY   STATUS    RESTARTS      AGE     IP          NODE       NOMINATED NODE   READINESS GATES
web-1       1/1     Running   0             12m     10.36.0.1   worker-2   <none>           <none>
web2        1/1     Running   0             8m51s   10.44.0.2   worker-1   <none>           <none>
webserver   1/1     Running   1 (17m ago)   5d21h   10.44.0.1   worker-1   <none>           <none>
# Pod에 접속해서 결과보기
$curl <pod IP address>
# grep을 이용하여 특정 정보만 추출
$ kubectl get pods webserver -o json | grep -i podip
        "podIP": "10.44.0.1",
        "podIPs": [
```
#### multi-container Pod 생성하기
- 예시: ``web-server container``와 ``wblog-agent container``를 하나의 Pod에 생성
  - 웹서비스와 관련된 로그를 생성하는 컨테이너를 하나의 Pod에 둠
  - volume을 두 개의 컨테이너 간에 공유하고, web server가 생성한 로그를 wblog가 수집 및 분석
  - **Pod 단위로 IP가 할당되므로, 두 개의 Container가 동일한 IP 사용**
  - Container들간의 유기적인 관계가 필요한 경우 
```bash
# multi-container pod를 위한 yaml파일 정의
# centos는 container 실행 시, sleep "10000"를 실행
$cat multi-container-pod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: multi-container-pod
spec:
  containers:
  - name: nginx-container
    image: nginx:1.14
    ports:
    - containerPort: 80
  - name: centos-container
    image: centos:7
    command:
    - sleep
    - "10000"
# multi-container pod 생성    
$kubectl create -f multi-container-pod.yaml
$kubectl get pods

# READY 정보를 이용해서 pod내부의 container 개수를 확인 가능
$kubectl get pods -o wide
NAME                  READY   STATUS    RESTARTS      AGE     IP          NODE       NOMINATED NODE   READINESS GATES
multi-container-pod   2/2     Running   0             11m     10.36.0.2   worker-2   <none>           <none>
web-1                 1/1     Running   0             51m     10.36.0.1   worker-2   <none>           <none>
web2                  1/1     Running   0             47m     10.44.0.2   worker-1   <none>           <none>
webserver             1/1     Running   1 (55m ago)   5d22h   10.44.0.1   worker-1   <none>           <none>

# resource의 정확한 상세 정보를 출력하는 명령어
$kubectl describe --help
Show details of a specific resource or group of resources.
# multi-container-pod의 상세 정보 출력
# 만약, pod 생성이 안되거나 했을 때 troubleshooting 용도로 사용 가능
$kubectl describe pod multi-container-pod 
Name:         multi-container-pod
Namespace:    default
Priority:     0
Node:         worker-2/10.0.1.6
Start Time:   Sat, 20 Nov 2021 13:08:10 +0900
Labels:       <none>
Annotations:  <none>
Status:       Running
IP:           10.36.0.2
IPs:
  IP:  10.36.0.2
Containers:
  nginx-container:
    Container ID:   docker://07b2ab909b623b5102b35fe92aa18c4e3bf3ece5c6814633026d5bf3a5adf939
    Image:          nginx:1.14
    Image ID:       docker-pullable://nginx@sha256:f7988fb6c02e0ce69257d9bd9cf37ae20a60f1df7563c3a2a6abe24160306b8d
    Port:           80/TCP
    Host Port:      0/TCP
    State:          Running
      Started:      Sat, 20 Nov 2021 13:08:11 +0900
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-jrjkm (ro)
  centos-container:
    Container ID:  docker://ebb07f3f5e496f595dca119171d6f910b6135cda8b400c92f655273f8920bc81
    Image:         centos:7
    Image ID:      docker-pullable://centos@sha256:9d4bcbbb213dfd745b58be38b13b996ebb5ac315fe75711bd618426a630e0987
    Port:          <none>
    Host Port:     <none>
    Command:
      sleep
      10000
    State:          Running
      Started:      Sat, 20 Nov 2021 13:08:11 +0900
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-jrjkm (ro)
Conditions:
  Type              Status
  Initialized       True 
  Ready             True 
  ContainersReady   True 
  PodScheduled      True 
Volumes:
  kube-api-access-jrjkm:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason     Age    From               Message
  ----    ------     ----   ----               -------
  Normal  Scheduled  6m47s  default-scheduler  Successfully assigned default/multi-container-pod to worker-2
  Normal  Pulled     6m46s  kubelet            Container image "nginx:1.14" already present on machine
  Normal  Created    6m46s  kubelet            Created container nginx-container
  Normal  Started    6m46s  kubelet            Started container nginx-container
  Normal  Pulled     6m46s  kubelet            Container image "centos:7" already present on machine
  Normal  Created    6m46s  kubelet            Created container centos-container
  Normal  Started    6m46s  kubelet            Started container centos-container
# nginx-container에 접속해 보기
$kubectl exec multi-container-pod -c nginx-container -it -- /bin/bash
root@multi-container-pod:/#cd /usr/share/nginx/html/
root@multi-container-pod:/usr/share/nginx/html# ls -al
total 16
drwxr-xr-x 2 root root 4096 Mar 26  2019 .
drwxr-xr-x 3 root root 4096 Mar 26  2019 ..
-rw-r--r-- 1 root root  537 Dec  4  2018 50x.html
-rw-r--r-- 1 root root  612 Dec  4  2018 index.html

# cento os container에 접속하여 nginx web server에 curl로 접속해 보기
# centos-container의 /bin/bash 명령어를 수행(standard input을 terminal로 지정)
$kubectl exec multi-container-pod -c centos-container -it -- /bin/bash
[root@multi-container-pod /]# curl localhost:80
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
# multi container pod인 경우, 로그 정보는 특정 container 단위로 볼 수 있음
$kubectl logs multi-container-pod
error: a container name must be specified for pod multi-container-pod, choose one of: [nginx-container centos-container]
gusami@master:~$kubectl logs multi-container-pod -c nginx-container
127.0.0.1 - - [20/Nov/2021:04:24:53 +0000] "GET / HTTP/1.1" 200 612 "-" "curl/7.29.0" "-"
10.32.0.1 - - [20/Nov/2021:04:32:19 +0000] "GET / HTTP/1.1" 200 9 "-" "curl/7.68.0" "-"
127.0.0.1 - - [20/Nov/2021:04:32:34 +0000] "GET / HTTP/1.1" 200 9 "-" "curl/7.29.0" "-"
gusami@master:~$kubectl logs multi-container-pod -c centos-container
# single container pod인 경우, 로그 정보는 pod 단위로 볼 수 있음
gusami@master:~$kubectl logs web-1
10.32.0.1 - - [20/Nov/2021:03:29:50 +0000] "GET / HTTP/1.1" 200 612 "-" "curl/7.68.0" "-"
```
#### 쿠버네티스 Pod 동작 Flow
- Pending 상태
  - The Pod has been accepted by the Kubernetes cluster, but one or more of the containers has not been set up and made ready to run. This includes time a Pod spends waiting to be scheduled as well as the time spent downloading container images over the network.
- Running	상태
  - The Pod has been bound to a node, and all of the containers have been created. At least one container is still running, or is in the process of starting or restarting.
- Succeeded 상태
  - All containers in the Pod have terminated in success, and will not be restarted.
- Failed 상태
  - All containers in the Pod have terminated, and at least one container has terminated in failure. That is, the container either exited with non-zero status or was terminated by the system.
- Unknown 상태
  -	For some reason the state of the Pod could not be obtained. This phase typically occurs due to an error in communicating with the node where the Pod should be running.
##### 실습
- 초기 셋팅
  - 기존의 모든 Pod 삭제 후, 새로운 Pod 생성
```bash
gusami@master:~$kubectl delete pod --all
```
- 새로운 Pod 생성 및 삭제
```bash
gusami@master:~$kubectl create -f pod-nginx.yaml
pod/web2 created
gusami@master:~$kubectl delete pod web2
```
- ``--watch`` option을 이용한 상태 정보 추적
```bash
gusami@master:~$kubectl get pods -o wide --watch
web2                  0/1     Pending       0             0s    <none>      <none>     <none>           <none>
web2                  0/1     Pending       0             0s    <none>      worker-2   <none>           <none>
web2                  0/1     ContainerCreating   0             0s    <none>      worker-2   <none>           <none>
web2                  1/1     Running             0             5s    10.36.0.1   worker-2   <none>           <none>
web2                  1/1     Terminating         0             61s   10.36.0.1   worker-2   <none>           <none>
web2                  0/1     Terminating         0             62s   10.36.0.1   worker-2   <none>           <none>
web2                  0/1     Terminating         0             62s   10.36.0.1   worker-2   <none>           <none>
web2                  0/1     Terminating         0             62s   10.36.0.1   worker-2   <none>           <none>
```
##### Pod 관리하기
- 동작 중인 Pod 정보 보기
```bash
# 현재 namespace에서의 Pod 보기
gusami@master:~$kubectl get pods
No resources found in default namespace.
# 전체 namespace에서의 Pod 보기
gusami@master:~$kubectl get pods --all-namespaces
NAMESPACE     NAME                             READY   STATUS    RESTARTS       AGE
kube-system   coredns-78fcd69978-nz4fr         1/1     Running   5 (12d ago)    19d
kube-system   coredns-78fcd69978-vsnzh         1/1     Running   5 (12d ago)    19d
kube-system   etcd-master                      1/1     Running   6 (12d ago)    19d
kube-system   kube-apiserver-master            1/1     Running   6 (12d ago)    19d
kube-system   kube-controller-manager-master   1/1     Running   6 (12d ago)    19d
kube-system   kube-proxy-s9cp2                 1/1     Running   4 (12d ago)    19d
kube-system   kube-proxy-vscnf                 1/1     Running   6 (12d ago)    19d
kube-system   kube-proxy-wsc4f                 1/1     Running   4 (12d ago)    19d
kube-system   kube-scheduler-master            1/1     Running   6 (12d ago)    19d
kube-system   weave-net-kzxnd                  2/2     Running   9 (12d ago)    19d
kube-system   weave-net-tbcxg                  2/2     Running   11 (12d ago)   19d
kube-system   weave-net-zvqg4                  2/2     Running   8 (12d ago)    19d
$kubectl get pods -o wide
$kubectl describe pod webserver
```
- 동작 중인 Pod 수정
```bash
$kubectl edit pod webserver
```
- 동작 중인 Pod 삭제
```bash
$kubectl delete pod webserver
# 모든 Pod 삭제
$kubectl delete pod --all
```
##### Question & Answer
- 현재 namespace에서 동작 중인 Pod는 몇 개인가?
```bash
$kubectl get pods
```
- 현재 시스템에서 동작 중인 Pod 수는?
```bash
$kubectl get pods --all-namespaces
```
- 컨테이너 nginx를 실행하는 nginx-pod라는 이름의 Pod를 생성하시오
```bash
$kubectl run nginx-pod --image=nginx:1.14 --port 80
```
- 앞에서 생성한 Pod의 image정보를 확인하는 명령은 무엇인가?
```bash
$kubectl describe pod nginx-pod
```
- 앞에서 생성한 nginx-pod는 어느 node에 배치되었나?
```bash
$ kubectl get pods -o wide
NAME   READY   STATUS    RESTARTS   AGE   IP          NODE       NOMINATED NODE   READINESS GATES
web2   1/1     Running   0          9s    10.44.0.1   worker-1   <none>           <none>
```
- 앞에서 생성한 Pod에는 몇 개의 컨테이너가 포함되어 있나?
  - ``kubectl get pods`` 명령: READY 칼럼 정보를 확인
  - ``kubectl describe pod <Pod name>``: yaml 형식의 containers항목을 살펴봄
- 앞에서 생성한 Pod는 현재 상태는 어떠한가?
  - ``kubectl describe pod <Pod name>``
  - ``kubectl get pods`` 명령어에서 STATUS 칼럼 확인
- 새 Pod내의 컨테이너 상태는 어떻습니까?
  - ``kubectl describe pod <Pod name>``를 이용
- ``kubectl get pods``명령의 출력에서 READY 열은 무엇을 의미하나?
  - 현재 READY중인 컨테이너 수 / 전체 컨테이너 수
- 생성한 Pod를 삭제하시오.
```bash
$kubectl delete pod webserver
```
- 컨테이너 image `redis123`을 실행하는 pod `redis`를 ``redis.yaml``을 이용해 생성하시오
```bash
gusami@master:~$kubectl run redis --image=redis123 --dry-run=client -o yaml > redis.yaml
gusami@master:~$cat redis.yaml
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: redis
  name: redis
spec:
  containers:
  - image: redis123
    name: redis
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Always
status: {}
# 불필요한 항목 제거
gusami@master:~$vi redis.yaml
apiVersion: v1
kind: Pod
metadata:
  name: redis
spec:
  containers:
  - image: redis123
    name: redis
# 상태 점검. 이미지 이름이 잘못 되어서 Pull에서 실패    
gusami@master:~$kubectl get pods
NAME    READY   STATUS         RESTARTS   AGE
redis   0/1     ErrImagePull   0          8s
# describe pod 명령을 이용한 정확한 원인 분석 (Events항목을 분석)
gusami@master:~$kubectl describe pod redis
Name:         redis
Namespace:    default
Priority:     0
Node:         worker-2/10.0.1.6
Start Time:   Thu, 02 Dec 2021 23:29:35 +0900
Labels:       <none>
Annotations:  <none>
Status:       Pending
IP:           10.36.0.1
IPs:
  IP:  10.36.0.1
Containers:
  redis:
    Container ID:   
    Image:          redis123
    Image ID:       
    Port:           <none>
    Host Port:      <none>
    State:          Waiting
      Reason:       ErrImagePull
    Ready:          False
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-n2tjz (ro)
Conditions:
  Type              Status
  Initialized       True 
  Ready             False 
  ContainersReady   False 
  PodScheduled      True 
Volumes:
  kube-api-access-n2tjz:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type     Reason     Age   From               Message
  ----     ------     ----  ----               -------
  Normal   Scheduled  12s   default-scheduler  Successfully assigned default/redis to worker-2
  Normal   Pulling    12s   kubelet            Pulling image "redis123"
  Warning  Failed     8s    kubelet            Failed to pull image "redis123": rpc error: code = Unknown desc = Error response from daemon: pull access denied for redis123, repository does not exist or may require 'docker login': denied: requested access to the resource is denied
  Warning  Failed     8s    kubelet            Error: ErrImagePull
  Normal   BackOff    8s    kubelet            Back-off pulling image "redis123"
  Warning  Failed     8s    kubelet            Error: ImagePullBackOff
```
- 앞서 만든 redis pod의 image를 redis로 수정하여 동작시키시오.
```bash
$kubectl edit pod redis
# image 이름을 redis123에서 redis로 수정 후, 저장
# 상태가 Running로 자동 변경됨
gusami@master:~$kubectl get pods
NAME    READY   STATUS    RESTARTS   AGE
redis   1/1     Running   0          5m5s
```
### livenessPorbe를 사용한 self-healing Pod
- kubelet으로 컨테이너 진단하기
- 사용자 헬스체크 루틴에 응답하지 않는 컨테이너를 죽이고 재시작 함
##### Liveness Probe 개념
- Pod가 계속 실행할 수 있음을 보장
- Pod의 spec에 정의
- livenessProbe definition을 추가한 Pod yaml 형식 정의
  - 웹 기반의 서비스이므로, 80 포트에 대해서 http로 주기적으로 접속해 봄
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
spec:
  containers:
    - image: nginx:1.14
      name: nginx-container
      livenessProbe:
        httpGet:
          path: /
          port: 80
```
##### livenessProbe 메커니즘
- 중요한 점
  - 오류가 연속 세번 발생하면, Pod는 그대로 두고 새로운 Container 이미지를 다운로드 받아 다시 시작
  - **만약, Pod는 그대로이고, Container만 재시작하므로 Pod에 맵핑된 IP는 그대로 임**

- 방법 1: httpGet Probe
  - 웹 기반의 서비스에 적용가능한 방법
  - 지정한 IP주소, Port, Path에 Http GET 요청을 주기적으로 보내, 해당 컨테이너가 응답하는지를 확인
  - 반환 코드가 200이 아닌 값이 연속으로 3번 나오면 오류발생
    - docker hub에서 새로운 이미지를 다운로드 받아 컨테이너를 다시 시작
```yaml
livenessProbe:
  httpGet:
    path: /
    port: 80
```
- 방법 2: tcpSocket Probe
  - 지정한 포트에 TCP 연결을 주기적으로 시도
    - 연속 3번 연결되지 않으면 docker hub에서 새로운 이미지를 다운로드 받아 컨테이너를 다시 시작
    - 사용예: ssh가 제대로 동작하는지 체크하는 예
```yaml
livenessProbe:
  tcpSocket:
    port: 22
```
- 방법 3: exec Probe
  - exec 명령을 주기적으로 전달한 후
    - 연속 3번 명령의 종료코드가 0이 아니면 docker hub에서 새로운 이미지를 다운로드 받아 컨테이너를 다시 시작
    - ``ls command, ps command etc`` 다양한 명령어를 사용 가능
```yaml
livenessProbe:
  exec:
    command:
    - ls
    - /data/file
```
##### livenessProbe 적용
- liveness probe 매개 변수
  - 명시하지 않으면, Default 값이 존재
  - periodSeconds: health check 반복 실행 시간(초) (Defaults to 10 seconds)
  - initialDelaySeconds: Pod 실행 후, delay할 시간(초) (Default to 0 seconds)
  - timeoutSeconds: health check 후, 응답을 기다리는 시간(초) (Default to 1 seconds)
  - successThreshold: 성공으로 생각하는 연속적인 정상 응답 횟수 (Defaults to 1)
  - failureThreshold: 실패로 생각하는 연속적인 실패 응답 횟수 (Defaults to 3)  
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
spec:
  containers:
    - image: nginx:1.14
      name: nginx-container
      livenessProbe:
        httpGet:
          path: /
          port: 80
        initialDelaySeconds: 15
        periodSeconds: 20
        timeoutSeconds: 1
        successThreshold: 1
        failureThreshold: 3  
```
- liveness probe 매개 변수를 명시하지 않은 경우의 Default 값 확인
```bash
# liveness probe 매개 변수를 지정하지 않은 yaml 파일
gusami@master:~$cat pod-nginx-liveness.yaml 
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod-liveness
spec:
  containers:
  - name: nginx-container
    image: nginx:1.14
    ports:
    - containerPort: 80
      protocol: TCP
    livenessProbe:
      httpGet:
        path: /
        port: 80
# yaml 파일을 이용한 pod 생성
gusami@master:~$kubectl create -f pod-nginx-liveness.yaml
pod/nginx-pod-liveness created
# liveness porbe 매개 변수 확인
# Liveness 항목에서 확인 가능
# Liveness:       http-get http://:80/ delay=0s timeout=1s period=10s #success=1 #failure=3
# initialDelaySeconds: 0, periodSeconds: 10, timeoutSeconds: 1에 해당
gusami@master:~$kubectl describe pod nginx-pod-liveness 
Name:         nginx-pod-liveness
Namespace:    default
Priority:     0
Node:         worker-1/10.0.1.5
Start Time:   Sat, 04 Dec 2021 12:35:44 +0900
Labels:       <none>
Annotations:  <none>
Status:       Running
IP:           10.44.0.1
IPs:
  IP:  10.44.0.1
Containers:
  nginx-container:
    Container ID:   docker://6b240d5331451c52f5da31b28037f2c5f9a2e847062f783652c51e240f29e007
    Image:          nginx:1.14
    Image ID:       docker-pullable://nginx@sha256:f7988fb6c02e0ce69257d9bd9cf37ae20a60f1df7563c3a2a6abe24160306b8d
    Port:           80/TCP
    Host Port:      0/TCP
    State:          Running
      Started:      Sat, 04 Dec 2021 12:35:45 +0900
    Ready:          True
    Restart Count:  0
    Liveness:       http-get http://:80/ delay=0s timeout=1s period=10s #success=1 #failure=3
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-ztz29 (ro)
Conditions:
  Type              Status
  Initialized       True 
  Ready             True 
  ContainersReady   True 
  PodScheduled      True 
Volumes:
  kube-api-access-ztz29:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason     Age   From               Message
  ----    ------     ----  ----               -------
  Normal  Scheduled  49s   default-scheduler  Successfully assigned default/nginx-pod-liveness to worker-1
  Normal  Pulled     48s   kubelet            Container image "nginx:1.14" already present on machine
  Normal  Created    48s   kubelet            Created container nginx-container
  Normal  Started    48s   kubelet            Started container nginx-container        
```
##### livenessProbe example (1)
- liveness Probe는 Pod의 spec에 정의
- 아래 example의 smlinux/unhealthy 컨테이너는 HTTP 연결에 대해서
  - 초기의 5번은 200번 상태코드를 응답
  - 6번째부터는 내부 서버 오류로 HTTP 500 ERROR를 발생시킴
```bash
# pod 정의
$cat pod-liveness.yaml
apiVersion: v1
kind: Pod
metadata:
  name: liveness-pod
spec:
  containers:
  - image: smlinux/unhealthy
    name: unhealthy-container
    ports:
    - containerPort: 8080
      protocol: TCP
    livenessProbe:
      httpGet:
        path: /
        port: 8080
# pod 생성
gusami@master:~$kubectl create -f pod-liveness.yaml 
pod/liveness-pod created
# test 8080 port
gusami@master:~$curl 10.36.0.1:8080
I am not well. Please restart me!
gusami@master:~$curl 10.36.0.1:8080
curl: (7) Failed to connect to 10.36.0.1 port 8080: Connection refused
# monitor pods
gusami@master:~$watch kubectl get pods -o wide
NAME                 READY   STATUS    RESTARTS      AGE     IP          NODE       NOMINATED NODE   READINESS GATES
liveness-pod         1/1     Running   2 (71s ago)   5m11s   10.36.0.1   worker-2   <none>           <none>
nginx-pod-liveness   1/1     Running   0             11m     10.44.0.1   worker-1   <none>           <none>
```        
##### livenessProbe example (2)
- 아래의 liveness-exam.yaml 파일에 self-healing 기능을 추가하시오
  - 동작되는 Pod내의 컨테이너에 /tmp/healthy 파일이 있는지 5초마다 확인
  - Pod 실행 후, 10초 후부터 검사
  - 성공횟수는 1번, 실패횟수는 연속 2회로 구성
```bash  
# pod 정의
$cat > pod-liveness-exam.yaml
apiVersion: v1
kind: Pod
metadata:
  name: liveness-exam
spec:
  containers:
  - image: busybox
    name: busybox-container
    args:
    - /bin/sh
    - -c
    - touch /tmp/healthy; sleep 30; rm -rf /tmp/healthy; sleep 600
    livenessProbe:
      exec:
        command:
        - ls
        - /tmp/healthy
      initialDelaySeconds: 10
      periodSeconds: 5
      successThreshold: 1
      failureThreshold: 2
# Pod 생성
gusami@master:~$kubectl create -f pod-liveness-busybox.yaml 
pod/liveness-exam created
# Watch kubectl get pods 
gusami@master:~$watch kubectl get pods -o wide
NAME                 READY   STATUS    RESTARTS     AGE     IP          NODE       NOMINATED NODE   READINESS GATES
liveness-exam        1/1     Running   3 (6s ago)   3m41s   10.36.0.1   worker-2   <none>           <none>
```
### Kubernetes Pod - init container & infra container
#### init container 
- 앱 컨테이너 실행 전에 미리 동작시킬 컨테이너
- 본 컨테이너(Main Container)가 실행되기 전에 사전 작업이 필요할 경우 사용
- 초기화 컨테이너가 모두 실행된 후에 앱 컨테이너를 실행
- https://kubernetes.io/docs/concepts/workloads/pods/init-containers/
- https://github.com/arisu1000/kubernetes-book-sample/blob/master/pod/pod-init.yaml
- init container를 적용한 Pod
  - init container의 역할
    - main container의 실행을 위한 환경 셋팅 및 초기화 구성
- 적용 예시
  - node.js로 만들어진 login application container이 있다고 가정
  - db에 접속해서 사용자 정보를 가져오는 container가 있다고 가정
  - 두 개의 container을 하나의 Pod로 묶으면? (종속 관계)
    - db관련 container는 init container 역할
    - login application container는 main container 역할
    - db관련 container가 성공해야 login application container가 정상 동작 가능

![init_container_concept.png](./images/init_container_concept.png)    
- 예제
  - ``until``: ``nslookup`` 명령어 실패 시, ``do``와 ``done`` 사이의 명령어 수행을 반복
  - init container들은 ``nslookup`` 이용해서 ``myservice.XXX``와 ``mydb.XXX``가 실행될 때까지 대기
    - init container들은 ``command`` 항목이 실행이 끝나야 완료되는 것임
  - main container는 init container들의 ``command`` 항목이 완료된 후에, 실행을 시작
    - init container들이 완료되지 않으면, main container에서 ``The app is running!``를 볼 수 없음
```bash
# delete all pods using currently
$kubectl delete pods --all
# define pod with init containers from k8s site example
$cat > init-container-exam.yaml
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
  labels:
    app: myapp
spec:
  containers:
  - name: myapp-container
    image: busybox:1.28
    command: ['sh', '-c', 'echo The app is running! && sleep 3600']
  initContainers:
  - name: init-myservice
    image: busybox:1.28
    command: ['sh', '-c', "until nslookup myservice.$(cat /var/run/secrets/kubernetes.io/serviceaccount/namespace).svc.cluster.local; do echo waiting for myservice; sleep 2; done"]
  - name: init-mydb
    image: busybox:1.28
    command: ['sh', '-c', "until nslookup mydb.$(cat /var/run/secrets/kubernetes.io/serviceaccount/namespace).svc.cluster.local; do echo waiting for mydb; sleep 2; done"]
# Pod 생성
$kubectl create -f init-container-exam.yaml 
pod/myapp-pod created
# Pod 상태 관찰 - init container 생성 중
$kubectl get pods -o wide
NAME        READY   STATUS     RESTARTS   AGE   IP          NODE       NOMINATED NODE   READINESS GATES
myapp-pod   0/1     Init:0/2   0          26s   10.36.0.1   worker-2   <none>           <none>
# myservice 파일 생성
$cat > init-container-exam-svc.yaml
apiVersion: v1
kind: Service
metadata:
  name: myservice
spec:
  ports:
  - protocol: TCP
    port: 80
    targetPort: 9376
# myservice 생성
$kubectl create -f init-container-exam-svc.yaml 
service/myservice created
# Pod 상태 관찰 - init container 한 개 생성 완료
$kubectl get pods -o wide
NAME        READY   STATUS     RESTARTS   AGE     IP          NODE       NOMINATED NODE   READINESS GATES
myapp-pod   0/1     Init:1/2   0          7m20s   10.36.0.1   worker-2   <none>           <none>
# mydb 파일 생성
$cat > init-container-exam-db.yaml
apiVersion: v1
kind: Service
metadata:
  name: mydb
spec:
  ports:
  - protocol: TCP
    port: 80
    targetPort: 9377
# mydb 서비스 생성    
$kubectl create -f init-container-exam-db.yaml 
service/mydb created
# Pod 상태 관찰 - init container 두개 생성 완료 및 Pod가 running 상태로 변함
$kubectl get pods -o wide
NAME        READY   STATUS    RESTARTS   AGE   IP          NODE       NOMINATED NODE   READINESS GATES
myapp-pod   1/1     Running   0          13m   10.36.0.1   worker-2   <none>           <none>
# 서비스 목록 보기
$kubectl get services -o wide
NAME         TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE     SELECTOR
kubernetes   ClusterIP   10.96.0.1      <none>        443/TCP   28d     <none>
mydb         ClusterIP   10.98.227.39   <none>        80/TCP    2m55s   <none>
myservice    ClusterIP   10.106.2.106   <none>        80/TCP    9m39s   <none>
```
#### infra container(pause) 이해
- Pod 생성 시, 기본적으로 사용자가 만든 container외에도 infra container가 생성
- Pod의 infra(IP, hostname etc) 및 환경을 생성하고 관리하는 역할

![infra_container.png](./images/infra_container.png)
```bash
# Pod 생성
$kubectl run webserver --image=nginx:1.14 --port=80
pod/webserver created
# worker-1 node에 pod 생성됨
$kubectl get pods -o wide
NAME        READY   STATUS    RESTARTS   AGE     IP          NODE       NOMINATED NODE   READINESS GATES
webserver   1/1     Running   0          4m37s   10.44.0.1   worker-1   <none>           <none>
# worker-1과 worker-2에 대한 ip 정보 입력
$vi /etc/hosts
127.0.0.1	localhost
127.0.1.1	master
10.0.1.5 worker-1
10.0.1.6 worker-2
...
# worker node 접속
gusami@master:~$ssh gusami@worker-1
# infra container 생성 확인(pause)
gusami@worker-1:~$sudo docker ps
[sudo] password for gusami: 
CONTAINER ID   IMAGE                  COMMAND                  CREATED             STATUS             PORTS     NAMES
1a897a2a5c45   295c7be07902           "nginx -g 'daemon of…"   7 minutes ago       Up 7 minutes                 k8s_webserver_webserver_default_a80d0fa6-dc99-4720-a6d0-480130fc735f_0
a914dae8873e   k8s.gcr.io/pause:3.5   "/pause"                 7 minutes ago       Up 7 minutes                 k8s_POD_webserver_default_a80d0fa6-dc99-4720-a6d0-480130fc735f_0
c5ee827d7a1c   7f92d556d4ff           "/usr/bin/launch.sh"     About an hour ago   Up About an hour             k8s_weave-npc_weave-net-zvqg4_kube-system_e4417f2c-f194-4dcb-b38a-266b742f9fe0_9
9a04c85e1f59   df29c0a4002c           "/home/weave/launch.…"   About an hour ago   Up About an hour             k8s_weave_weave-net-zvqg4_kube-system_e4417f2c-f194-4dcb-b38a-266b742f9fe0_9
72616719126f   6120bd723dce           "/usr/local/bin/kube…"   About an hour ago   Up About an hour             k8s_kube-proxy_kube-proxy-s9cp2_kube-system_e8bc827e-645d-4e3e-85b2-6468b6e7f64a_9
f3bff8222eae   k8s.gcr.io/pause:3.5   "/pause"                 About an hour ago   Up About an hour             k8s_POD_weave-net-zvqg4_kube-system_e4417f2c-f194-4dcb-b38a-266b742f9fe0_9
277e0433e3fb   k8s.gcr.io/pause:3.5   "/pause"                 About an hour ago   Up About an hour             k8s_POD_kube-proxy-s9cp2_kube-system_e8bc827e-645d-4e3e-85b2-6468b6e7f64a_9
# webserver pod 삭제
gusami@master:~$kubectl delete pods webserver
pod "webserver" deleted
# infra container도 함께 삭제됨
gusami@worker-1:~$sudo docker ps
CONTAINER ID   IMAGE                  COMMAND                  CREATED             STATUS             PORTS     NAMES
c5ee827d7a1c   7f92d556d4ff           "/usr/bin/launch.sh"     About an hour ago   Up About an hour             k8s_weave-npc_weave-net-zvqg4_kube-system_e4417f2c-f194-4dcb-b38a-266b742f9fe0_9
9a04c85e1f59   df29c0a4002c           "/home/weave/launch.…"   About an hour ago   Up About an hour             k8s_weave_weave-net-zvqg4_kube-system_e4417f2c-f194-4dcb-b38a-266b742f9fe0_9
72616719126f   6120bd723dce           "/usr/local/bin/kube…"   About an hour ago   Up About an hour             k8s_kube-proxy_kube-proxy-s9cp2_kube-system_e8bc827e-645d-4e3e-85b2-6468b6e7f64a_9
f3bff8222eae   k8s.gcr.io/pause:3.5   "/pause"                 About an hour ago   Up About an hour             k8s_POD_weave-net-zvqg4_kube-system_e4417f2c-f194-4dcb-b38a-266b742f9fe0_9
277e0433e3fb   k8s.gcr.io/pause:3.5   "/pause"                 About an hour ago   Up About an hour             k8s_POD_kube-proxy-s9cp2_kube-system_e8bc827e-645d-4e3e-85b2-6468b6e7f64a_9
```
### static Pod 만들기
- static Pod는 Master node(control-plane)의 API 서버를 통해 요청을 보내지 않음
- worker node내에 존재하는 ``kubelet`` 데몬에 의해 직접 관리
  - ``kubelet` 데몬이 관리하는 디렉토리가 존재
  - 해당 디렉토리에 yaml 파일을 생성하면, 자동으로 pod가 실행됨
  - 해당 디렉토리에 yaml 파일을 지우면, 자동으로 pod가 삭제됨

![static_pod](./images/static_pod.png)
#### static Pod
- API 서버없이 특정 노드에 있는 kubelet 데몬에 의해 직접 관리  
- /etc/kubernetes/manifests/ 디렉토리에 k8s yaml 파일을 저장 시 적용됨
  - master node는 해당 디렉토리에 etcd, apiserver, scheduler pod에 대한 yaml 정의
    - master node에서 사용자 정의 yaml을 추가 시, worker 노드들 중 하나에 배치 (주의)
- static pod 디렉토리 구성
  - config yaml 파일에 경로가 존재  
  - CKA 자격증 문제로 자주 출제: staticPodPath 변경
    - 설정 파일에서 경로 변경
    - 새로운 경로의 디렉토리 생성
    - ``kubelet`` daemon 재시작
    - 해당 경로에 yaml 파일을 생성
```bash
# 디렉토리 경로 확인
$vi /var/lib/kubelet/config.yaml
...
staticPodPath: /etc/kubernetes/manifests
....
# 디렉토리 경로 수정 시, kubelet 데몬 재실행
$systemctl restart kubelet
```
- 실제 예시
```bash
root@worker-2:~#cat /var/lib/kubelet/config.yaml
apiVersion: kubelet.config.k8s.io/v1beta1
authentication:
  anonymous:
    enabled: false
  webhook:
    cacheTTL: 0s
    enabled: true
  x509:
    clientCAFile: /etc/kubernetes/pki/ca.crt
authorization:
  mode: Webhook
  webhook:
    cacheAuthorizedTTL: 0s
    cacheUnauthorizedTTL: 0s
cgroupDriver: systemd
clusterDNS:
- 10.96.0.10
clusterDomain: cluster.local
cpuManagerReconcilePeriod: 0s
evictionPressureTransitionPeriod: 0s
fileCheckFrequency: 0s
healthzBindAddress: 127.0.0.1
healthzPort: 10248
httpCheckFrequency: 0s
imageMinimumGCAge: 0s
kind: KubeletConfiguration
logging: {}
memorySwap: {}
nodeStatusReportFrequency: 0s
nodeStatusUpdateFrequency: 0s
resolvConf: /run/systemd/resolve/resolv.conf
rotateCertificates: true
runtimeRequestTimeout: 0s
shutdownGracePeriod: 0s
shutdownGracePeriodCriticalPods: 0s
staticPodPath: /etc/kubernetes/manifests
streamingConnectionIdleTimeout: 0s
syncFrequency: 0s
volumeStatsAggPeriod: 0s
# 디렉토리 이동
root@worker-2:~#cd /etc/kubernetes/manifests
# 파일 생성
root@worker-2:/etc/kubernetes/manifests#vi pod-nginx.yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
spec:
  containers:
  - name: nginx-container
    image: nginx:1.14
    imagePullPolicy: Always
    ports:
    - containerPort: 80
      protocol: TCP
# 파드 상태 확인. 파일이 생성되면 자동 시작
gusami@master:~$kubectl get pods -o wide
NAME                 READY   STATUS    RESTARTS   AGE   IP          NODE       NOMINATED NODE   READINESS GATES
nginx-pod-worker-2   1/1     Running   0          26s   10.36.0.1   worker-2   <none>           <none>
# 파드 삭제 명으로 삭제
gusami@master:~$ kubectl delete pods --all
pod "nginx-pod-worker-2" deleted
# 파드 삭제 후, 자동으로 재생성 됨
gusami@master:~$kubectl get pods -o wide
NAME                 READY   STATUS    RESTARTS   AGE   IP       NODE       NOMINATED NODE   READINESS GATES
nginx-pod-worker-2   0/1     Pending   0          3s    <none>   worker-2   <none>           <none>
# worker node에서 yaml 파일 삭제
root@worker-2:/etc/kubernetes/manifests#rm -rf pod-nginx.yaml
# Pod도 함께 삭제됨
gusami@master:~$kubectl get pods -o wide
No resources found in default namespace.
# master node에는 etcd, apiserver, controller-manager, scheduler이 static pod 형태로 존재
gusami@master:~$ls /etc/kubernetes/manifests
etcd.yaml  kube-apiserver.yaml  kube-controller-manager.yaml  kube-scheduler.yaml
```
### Pod에 리소스(cpu, memory) 할당하기
- Pod에 리소스를 명시적으로 제한하지 않으면?
  - 물리적인 노드의 리소스를 특정 Pod가 다 사용할 수도 있음
  - 만약, 특정 Pod가 보안에 취약해서 리소스 공격을 받으면, 모든 리소스를 다 사용하는 경우가 발생
    - 다른 Pod가 실행되지 못하는 경우가 발생

![Pod_Resource_Assign](./images/Pod_Resource_Assign.png)
- 새로운 Pod가 생성될 때 어디에 배치될까?
  - 물리적인 Worker node들에서 실행 중인 Pod들의 개수와 리소스 사용량을 가지고, Scheduler가 판단
    - 만약, Pod들의 개수를 이용해서 판단하면, 리소스가 남지 않은 Worker node에 할당되는 경우가 발생 가능
  - 새로운 Pod에 대한 cpu와 memory 값을 Request에 명시하면, 해당 조건을 만족하는 Worker node에 할당 가능
    - scheduler는 worker node들에 대한 cpu와 메모리 사용량 정보를 얻음 
    - scheduler는 새로운 Pod를 사용자가 명시한 값보다 여유가 있는 worker node에 할당

- 리소스 할당값 명시하기 (Request, Limit)
  - 새로운 Pod를 생성할 때, 배치를 위해서 Request에 명시 가능
  - 실제 Worker node에서 운영중에 사용가능한 Limit 값을 명시 가능

![Pod_Resource_Assign_1](./images/Pod_Resource_Assign_1.png)  
### Pod Resource 요청 및 제한
- 실제적으로는 Pod내의 container별로 Request 및 limit를 명시
  - Pod는 구성하는 container 값들의 합임
- Resource Requests
  - Container를 실행하기 위한 최소 리소스 양을 요청
- Resource Limit
  - Container가 사용할 수 있는 최대 리소스 양을 제한
  - Memory limit를 초과해서 사용되는 Pod는 종료(OOM Kill)되며 다시 스케줄링 됨(Pod Restart)
- 참고 사이트: https://kubernetes.io/docs/tasks/configure-pod-container/assign-cpu-resource/ 
- 메모리 단위
  - 1MB와 1024KB가 아니라 1000KB임
  - 1MiB는 1024KiB임
- CPU 단위
  - 물리적인 Core수로 판단(Chipset 수가 아님)
  - 1 core = 1000m core임. 200m core는 1 core의 1/5임   
- 사용 예제
  - 사용자가 Limit만 명시하면, Request 값도 Limit와 동일한 값 사용
  - 사용자가 Request만 명시하면, Limit값은 설정 안됨
```bash
# define pod yaml with container's resource request and limit
gusami@master:~$cat > pod-nginx-resources.yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod-env
spec:
  containers:
  - name: nginx-container
    image: nginx:1.14
    ports:
    - containerPort: 80
      protocol: TCP
    env:
    - name: MYVAR
      value: "testvalue"
    resources:
      requests:
        memory: 500Mi
        cpu: 200m
      limits:
        memory: 1Gi
        cpu: 1
# Pod 생성        
gusami@master:~$kubectl create -f pod-nginx-resources.yaml 
pod/nginx-pod-env created
# Pod 상태 확인
gusami@master:~$ kubectl get pods -o wide
NAME            READY   STATUS    RESTARTS   AGE   IP          NODE       NOMINATED NODE   READINESS GATES
nginx-pod-env   1/1     Running   0          26s   10.36.0.1   worker-2   <none>           <none>
# Pod 상세 정보 확인
gusami@master:~$kubectl describe pod nginx-pod-env
Name:         nginx-pod-env
Namespace:    default
Priority:     0
Node:         worker-2/10.0.1.6
Start Time:   Sun, 12 Dec 2021 15:03:33 +0900
Labels:       <none>
Annotations:  <none>
Status:       Running
IP:           10.36.0.1
IPs:
  IP:  10.36.0.1
Containers:
  nginx-container:
    Container ID:   docker://8cec8bcdb1b9c20dee824477383754aca2f067767e87a1c2bdaf577dea4520a8
    Image:          nginx:1.14
    Image ID:       docker-pullable://nginx@sha256:f7988fb6c02e0ce69257d9bd9cf37ae20a60f1df7563c3a2a6abe24160306b8d
    Port:           80/TCP
    Host Port:      0/TCP
    State:          Running
      Started:      Sun, 12 Dec 2021 15:03:34 +0900
    Ready:          True
    Restart Count:  0
    Limits:
      cpu:     1
      memory:  1Gi
    Requests:
      cpu:     200m
      memory:  500Mi
    Environment:
      MYVAR:  testvalue
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-w4ln7 (ro)
Conditions:
  Type              Status
  Initialized       True 
  Ready             True 
  ContainersReady   True 
  PodScheduled      True 
Volumes:
  kube-api-access-w4ln7:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
QoS Class:                   Burstable
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason     Age   From               Message
  ----    ------     ----  ----               -------
  Normal  Scheduled  101s  default-scheduler  Successfully assigned default/nginx-pod-env to worker-2
  Normal  Pulled     101s  kubelet            Container image "nginx:1.14" already present on machine
  Normal  Created    101s  kubelet            Created container nginx-container
  Normal  Started    101s  kubelet            Started container nginx-container
# 특정 Pod의 CPU와 메모리 사용량 확인하기
gusami@master:~$ kubectl top pod nginx-pod-env --namespace=default
error: Metrics API not available
# 기존의 모든 Pod 삭제
gusami@master:~$kubectl delete pods --all
pod "nginx-pod-env" deleted
# 만약, 가상머신에 할당된 2GB 전체를 할당해 보면? 
gusami@master:~$vi pod-nginx-resources.yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod-env
spec:
  containers:
  - name: nginx-container
    image: nginx:1.14
    ports:
    - containerPort: 80
      protocol: TCP
    env:
    - name: MYVAR
      value: "testvalue"
    resources:
      requests:
        memory: 500Mi
        cpu: 2
      limits:
        memory: 1Gi
        cpu: 2
# Pod 생성        
gusami@master:~$kubectl create -f pod-nginx-resources.yaml 
pod/nginx-pod-env created
# Pod 상태 확인.
# 기존의 물리적인 노드에서 사용하는 기본 양이 있으므로,
# 리소스 부족으로 Pending 상태에 머무르고, Scheduler에 deploy가 안됨
gusami@master:~$ kubectl get pods -o wide
NAME            READY   STATUS    RESTARTS   AGE     IP       NODE     NOMINATED NODE   READINESS GATES
nginx-pod-env   0/1     Pending   0          2m14s   <none>   <none>   <none>           <none>          
```



