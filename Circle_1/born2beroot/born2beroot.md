공부 해야할 것
1. 가상머신
	1. 가상머신 작동방식
		- 하드웨어를 소프트웨어로 에뮬레이트하여 여러 개의 독립적인 가상 환경을 만들어내는 기술
		- 주로 하나의 물리적인 컴퓨터에서 여러 운영 체제를 실해하거나, 개발 및 테스트 목적으로 사용
	1. centOS, Debian 차이점
		- centOS 
			1. RPM(Red Hat Package Manager) 패키지 시스템 사용
			2. 
	1. apt, aptitude 차이점
	2. AppArmor
2. 그룹 관련 명령어
	1. 그룹
		1. 그룹 조회 : `cat /etc/group`
		2. 그룹 추가 : `groupadd [그룹명]`
		3. 그룹 삭제 : `groupdel [그룹명]`
	2. `gpasswd` 옵션
		1. 그룹 패스워드 변경 : `gpasswd [그룹명]`
		2. 그룹에 사용자 추가 : `gpasswd -a [사용자] [그룹명]`
		3. 그룹의 사용자 삭제 : `gpasswd -d [사용자]`
		4. 그룹 패스워드 삭제 : `gpasswd -r [그룹명]`
	4.  사용자
		1. 사용자 추가 : `useradd [사용자명]`
		2. 사용자 삭제 : `userdel [사용자명]`
		3. 사용자 그룹 확인 : `id [사용자명]`
3. 패스워드 정책 설정, 패스워드 변경
4. hostname 수정, 파시션 확인
5. sudo
	1. sudo 정책 적용
	2. tty
	3. visudo, etc/sudoer
6. UFW
7. LVM
8. ssh
	1. ssh란?
	2. 포트포워딩
9. monitoring.sh
	1. cron
10. 웹 서버 기본 구조