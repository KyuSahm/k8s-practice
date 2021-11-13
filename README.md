# kubernetes
- 유튜브 이성미 강사님의 [따라하면서 배우는 쿠버네티스](https://www.youtube.com/watch?v=6n5obRKsCRQ&list=PLApuRlvrZKohaBHvXAOhUD-RxD0uQ3z0c)를 주로 정리
- ``쿠버네티스 입문(90가지 예제로 배우는 컨테이너 관리 자동화 표준)``을 정리

## WSL
- Windows Subsystem for Linux
- Hypervisor 위에 윈도우 NT 커널과 리눅스 커널을 각각 올리는 방식

![WSL2](./images/WSL.png)
## 윈도우에 WSL2을 여러 개 설치하는 방법 (실패)
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
## WSL의 명령 참고 자료
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
## Virtual Box를 이용하여 여러개의 VM 설치 (성공)
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
  - bcdedit /set hypervisorlaunchtype auto
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
