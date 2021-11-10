# kubernetes
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
## Virtual Box를 이용하여 여러개의 VM 설치
- 참조: [나만의 k8s 클러스터 구축하기](https://coffeewhale.com/kubernetes/cluster/virtualbox/2020/08/31/k8s-virtualbox/)
- ``VirtualBox 6.1.28 platform packages`` 설치
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





