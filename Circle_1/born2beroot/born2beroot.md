공부 해야할 것
1. 가상머신
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
1. 그룹 관련 명령어
	1. 그룹
		1. 그룹 조회 : `cat /etc/group`
		2. 그룹 추가 : `groupadd [그룹명]`
		3. 그룹 삭제 : `groupdel [그룹명]`
	2. `gpasswd` 옵션
		1. 그룹 패스워드 변경 : `gpasswd [그룹명]`
		2. 그룹에 사용자 추가 : `gpasswd -a [사용자] [그룹명]` /  `usermod -aG [그룹명] [사용자]`
		3. 그룹의 사용자 삭제 : `gpasswd -d [사용자]`
		4. 그룹 패스워드 삭제 : `gpasswd -r [그룹명]`
	4.  사용자
		1. 사용자 추가 : `useradd [사용자명]`
		2. 사용자 삭제 : `userdel [사용자명]`
		3. 사용자 그룹 확인 : `id [사용자명]`
		4. 사용자 패스워드 변경 : `passwd [사용자명]`
2. 패스워드 정책 설정, 패스워드 변경
	1. `vi /etc/login.defs`
		- `PASS_MAX_DAYS`, `PASS_MIN_DAYS`, `PASS_WARN_AGE`
	2. `apt install libpam-pwquality`
	3. `vi /etc/pam.d/common-password`
		- `pam_pwquality.so retry=3 minlen=10 ucredit=1 lcredit=-1 dcredit=-1 maxrepeat=3 reject_username enforce_for_root difok=7`
3. hostname 수정 : `hostname [변경명]`
4. sudo
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
	1. visudo, etc/sudoer
5. UFW
6. LVM
7. ssh
	1. ssh란?
	2. 포트포워딩
8. monitoring.sh
	1. cron
9. 웹 서버 기본 구조