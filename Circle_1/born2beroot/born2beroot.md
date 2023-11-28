# Born2BeRoot

본 과제는 가상 머신을 이용해, debian 기반의 가상 환경으로 운영체제에 대한 기 지식, 여러 리눅스 명령어, 방화벽, 원격접속, 웹 서버 등 네트워크 관련 지식을 습득하는 과제이다. <br>
### 1. 가상머신
1. 가상머신 작동방식
	- 하드웨어를 소프트웨어로 에뮬레이트하여 여러 개의 독립적인 가상 환경을 만들어내는 기술
	- 주로 하나의 물리적인 컴퓨터에서 여러 운영 체제를 실해하거나, 개발 및 테스트 목적으로 사용
2. centOS, Debian 차이점
	- centOS 
		1. RPM(Red Hat Package Manager) 기반, 'yum'이나 'dnf'를 통해 패키지 관리
		2. 신뢰성 및 안정성이 높아, 기업 환경 / 특히 서버용에서 많이 사용
	- Debian
		1. dpkg 기반, 'apt'를 통해 패키지 관리
		2. 커뮤니티 중심 개발 및 지원, 무료 및 오픈 소스 소프트웨어 프로젝트
3. apt, aptitude 차이점
4. AppArmor
	- 사용자와 프로세스의 보안을 강화하기 위한 LSM(Linux Security Module) 중 하나
	- LSM은 리눅스 커널의 보안 기능을 확장하고 강화하는 모듈
	- 어플리케이션의 행위를 제한하고 감시하는 데 중점을 둔 보안 도구
	- 프로파일 기반 보안, 실행 시간 감시 및 제한, 경로 기반 제어, 네트워크 제어, 프로세스 간 통신 보호

---

### 2. USER / GROUP
1. 그룹
	1. 그룹 조회 : `cat /etc/group`
	2. 그룹 추가 : `groupadd [그룹명]`
	3. 그룹 삭제 : `groupdel [그룹명]`
2. `gpasswd` 옵션
	1. 그룹 패스워드 변경 : `gpasswd [그룹명]`
	2. 그룹에 사용자 추가 : `gpasswd -a [사용자] [그룹명]`
	3. 그룹의 사용자 삭제 : `gpasswd -d [사용자]`
	4. 그룹 패스워드 삭제 : `gpasswd -r [그룹명]`
3.  사용자
	1. 사용자 추가 : `useradd [사용자명]`
	2. 사용자 삭제 : `userdel [사용자명]`
	3. 사용자 그룹 확인 : `id [사용자명]`
	4. 사용자 패스워드 변경 : `passwd [사용자명]`
4. `usermod` 옵션
	1. 그룹에 사용자 추가 : `usermod -aG [그룹명] [사용자명]`
	2. 사용자의 그룹 변경 : `usermode -g [] [이`

---

### 3. PASSWORD
- 주로 PAM(Pluggable Authentication Modules)를 사용하여 시스템의 패스워드 정책을 제어
- 패스워드 정책 설정
	1. `vi /etc/login.defs`
		- `PASS_MAX_DAYS`, `PASS_MIN_DAYS`, `PASS_WARN_AGE`
	2. `apt install libpam-pwquality`
	3. `vi /etc/pam.d/common-password`
		- `pam_pwquality.so retry=3 minlen=10 ucredit=1 lcredit=-1 dcredit=-1 maxrepeat=3 reject_username enforce_for_root difok=7`
- 장점 및 단점
	- 장점 : 보안 강화, 안전한 계정 관리
	- 단점 : 사용자 편의성 저하, 관리 부주의 증가, 정책에 따른 불편함 증가로 약한 패스워드 사용

---

### 4. hostname
1. 시스템 이름 확인 : `hostname`
2. 시스템 이름 변경 : `hostname [변경명]`
3. 호스트의 IP 주소 출력 : `hostname -I`

---

### 5. sudo
- sudo란?
	- 사용자가 특정 명령을 관리자 권한으로 실행할 수 있게 해주는 도구
	- 이를 통해, 시스템 관리자가 일반 사용자들에게 일부 관리자 권한을 부여하고, 시스템 보안을 강화
	- 사용자에게 필요한 작업을 수행할 권한을 부여하면서도 불필요한 권한 부여는 피하고, 보안 문제를 최소화 할 수 있도록 도와줌
- 사용 이유
	1. 보안 강화 - 필요한 권한만을 부여하여 시스템 보안을 강화시킴, 또한 비밀번호 인증을 통해 권한 상승에 대한 추가적인 보안 계층을 제공
	2. 작업 기록 - sudo를 통해 실행된 명령어와 그에 따른 로그를 기록할 수 있다. 이는 시스템의 관리 작업을 추적하고, 문제가 발생할 경우 해결에 도움을 줌
	3. 개별 사용자 식별 - 각 사용자는 자신의 비밀번호를 사용하여 sudo 명령어를 실행하므로, 관리자는 누가 어떤 명령을 실행했는지 식별 가능
-  sudo 정책 적용
	1. `Default authfail_message="권한 획득 실패 메세지"`
	2. `badpass_message="비밀번호 틀릴 때 메세지"`
	3. `iolog_dir="/var/log/sudo"`
	4. `log_input`, `log_output`
	5. `requiretty`
	6. `passwd_tries=3`
	7. secure_path : `/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin`
-  tty (teletypewriter)
	- 리눅스, 유닉스 기반 시스템에서 현재 사용자가 로그인한 터미널 디바이스를 나타냄
	- tty 명령어를 사용하면 현재 터미널 디바이스를 확인 가능
-  visudo
	- visudo를 이용하여 '/ect/sudoers' 파일 수정하는 것을 권장
	- visudo를 사용하면 텍스트 에디터를 열기 전에 파일의 유효성 검사를 하므로, 수정 사항이 구문적으로 올바른 지 확인 가능

---

### 6. UFW
- UFW란?
	- UFW(Uncomplicated Firewall), 리눅스에서 사용되는 간단하고 사용하기 쉬운 방화벽 설정 도구
	- 주로 서버 환경에서 네트워크 보안을 강화하고 외부에서의 액세스를 통제하기 위해 사용
	- 응용 프로그램 및 서비스에 대한 규칙을 정의하고, 특정 포트의 열고 닫음, 특정 IP주소 또는 범위의 IP주소에서의 연결 허용 또는 차단 등을 구성할 수 있음
- UFW 설정
	1. `apt-get install ufw`
	2. `ufw status verbose`
	3. `ufw enable` 
	4. `ufw default deny`
	5. `ufw allow 4242`
	6. `ufw status verbose`

---

### 7. LVM
- LVM이란?
	- LVM(Logical Volume Manager), 리눅스에서 디스크 관리를 용이하게 하는 유틸리티
	- LVM을 사용하면, 물리적인 디스크와 볼륨을 논리적인 단위로 나누고, 이를 필요에 따라 동적으로 조절할 수 있다.
- 구성 요소 및 작동 방식
	- PV(Physical Volume), VG(Volume Group), LV(Logical Volume)으로 구성
- Bonus (8GB)
	- root, 2600MB
	- swap, 600MB
	- home, 1400MB
	- var, 800MB
	- srv, 800MB
	- tmp, 800MB
	- var-log, 1000MB

---

### 8. SSH
- SSH란?
	- SSH(Secure Shell), 네트워크 상에서 안전하게 통신하기 위한 프로토콜
	- 주로 원격으로 다른 컴퓨터에 접속하거나 파일을 전송할 때 사용
	- 텍스트 기반의 원격 접속으로 보안적으로 강력하며, 그래픽 기반의 원격 접속보다 더 안전함
- 핵심 기능
	1. 보안 접속
		- SSH는 텍스트 기반의 원격 접속을 안전하게 제공. 데이터의 전송이 암호화되기 때문에 중간에서 정보를 가로채는 것이 어려움
	2. 원격제어
		- SSH를 사용하면 원격 서버 또는 다른 컴퓨터에 접속하여 명령 실행 가능.
	3. 파일전송
		- SCP(Secure Copy Protocol) 또는 SFTP(SSH File Transfer Protocol)을 사용하여 파일을 안전하게 전송 가능
	4. 포트포워딩
		- 로컬 컴퓨터와 원격 서버 간의 포트 포워딩을 설정하여 로컬 서버의 서비스에 접근
- 포트 포워딩
	- 네트워크에서 특정한 포트를 통해 들어오는 연결을 다른 포트나 다른 IP 주소로 전달하는 프로세스
	- 주로 공유기나 방화벽과 같은 네트워크 장비를 통해 내부 네트워크의 서비스에 외부에서 접근할 수 있도록 하는데 사용
	- 특정한 포트와 IP 주소를 기반으로 동작하며, 공용 IP 주소와 여러 내부 장치 사이의 통신을 원할하게 만듬.
	- '원격 접속', '로컬 서버에 대한 외부 접속' 등에 상황에서 사용됨
- SSH 설정
	1. apt-get -y install openssh-server
	2. vim /etc/ssh/sshd_config
	3. '#Port 22' -> 'Port 4242' 변경
	4. '#PermitRootLogin prohibit-password' -> 'PermitRootLogin no' 변경
	5. systemctl restart ssh
	6. systemctl status ssh
	7. 포트포워딩

---

### 9. Cron
- 일정한 간격으로 프로그램이나 스크립트를 자동으로 실행하기 위한 시간 기반 작업 스케줄러
-  작업은 주기적으로 실행되며, 특정 시간, 날짜 또는 주기에 따라 정의된 작업을 실행함
- Cron 작업은 crontab 파일을 사용하여 정의됨. crontab 파일에는 실행할 명령 라인과 실행 주기가 정의되어 있음.
- monitoring.sh 설정
	1. apt-get -y install sysstat
	2. vim /root/monitoring.sh 후 아래 내용 입력
	```c
	printf "#Architecture: "
	uname -a
	
	printf "#CPU physical : "
	cat /proc/cpuinfo | grep physical id | wc -l
	
	printf "#vCPU : "
	cat /proc/cpuinfo | grep processor | wc -l
	
	printf "#Memory Usage: "
	free -m | grep Mem | awk '{printf "%d/%dMB (%.2f%%)\n", $3, $2, $3/$2 * 100}'
	
	printf "#Disk Usage: "
	df -BM | grep /dev/map | awk '{sum+=$3}END{print sum}' | tr -d '\n'
	printf "/"
	df -BG | grep /dev/map | awk '{sum+=$4}END{print sum}' | tr -d '\n'
	printf "Gb ("
	df -BM | grep /dev/map | awk '{sum1+=$3; sum2+=$4}END{printf "%d", sum1 / sum2 * 100}' | tr -d '\n'
	printf "%%)\n"
	
	printf "#CPU load: "
	mpstat | grep all | awk '{printf "%.2f%%\n", 100-$13}'
	
	printf "#Last boot: "
	who -b | awk '{printf $3" "$4"\n"}'
	
	printf "#LVM use: "
	lsblk | grep LVM | wc -l | awk '{if ($1>0) print "yes"; else print "no"}'
	
	printf "#Connections TCP : "
	ss -t | grep ESTAB | wc -l | tr -d '\n'
	printf " ESTABLISHED\n"
	
	printf "#User log: "
	who | wc -l
	
	printf "#Network: IP "
	hostname -I | tr -d '\n'
	
	printf "("
	ip link | grep link/ether | awk '{printf $2}'
	printf ")\n"
	
	printf "#Sudo : "
	journalctl _COMM=sudo | wc -l | tr -d '\n'
	printf " cmd\n"
	```

---

### 10. 네트워크 인터페이스
- 컴퓨터나 네트워크 장비가 네트워크에 연결되고 통신하기 위한 하트웨어 또는 소프트웨어의 구성 요소
- 각 네트워크 인터페이스는 고유한 식별자를 가지며, 이는 맥 주소(MAC Address)라고 불림
- 데이터를 주고 받을 때 사용되는 연결 지점
1. 이더넷 인터페이스 (Ethernet Interface)
	- 유선 네트워크를 연결할 때 사용되는 인터페이스
	- 주로 네트워크 케이블을 통해 라우터, 스위치 또는 다른 컴퓨터와 연결
2. 무선 인터페이스 (Wireless Interface)
	- Wi-Fi 또는 무선 네트워크에 연결할 때 사용되는 인터페이스
	- 무선 라이터 또는 액세스 포인트에 연결됨
3. 루프백 인터페이스 (Loopback Interface)
	- 로컬 호스트에서 자체적으로 통신할 때 사용되는 가상의 인터페이스
	- 주로 '127.0.0.1'과 같은 로컬 루프백 주소가 할당
4. 가상 인터페이스 (Virtual Interface)
	- 소프트웨어적으로 생성되어 논리적으로 동작하는 인터페이스
	- 가상 머신이나 VPN 등에서 사용됨
