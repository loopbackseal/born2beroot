# 선행 지식

### 가상머신과 사용목적

가상머신은 실제 사용하고 있는 컴퓨터 안에서 새로운 컴퓨터를 만들어 사용하는 것! 그에 따라 여러 운영체제를 한가지 운영체제에서 활용할 수 있기도 하다. ex) Mac OS 사용자의 Boot camp or Pararells

가상머신을 사용하는 목적은 여러가지가 있지만, 대표적으로 하나의 컴퓨터로 두개 이상의 운영체제를 실행하고자 할때, 상호 간섭이 없는 상황에서 두가지 환경을 운영하고자 할때 (Ex. 클라우드), 독립적인 환경을 운영하고자 할때 (Ex. 악성 코드 및 바이러스 분석을 할때, 물리 컴퓨터의 감염을 방지하고자)

### Debian과 CentOS의 차이

Debian은 dpkg와 apt를 통해 패키지의 설치 및 관리가 편하다고 들었기 때문에 Debian을 선택

또한 많이 사용되는 우분투가 데비안 계열이라고 알려져 있어서 선택.

### Aptitude와 apt의 차이

aptitude와 apt 모두 패키지 관리와 설치, 제거를 진행하는 소프트웨어이지만, apt는 GUI 없이 명령어를 사용한다는 특징이 있다. 하지만, aptitude는 GUI가 있으며, 비대화형 명령어를 사용하는 apt와 달리 대화형 명령어를 사용한다.

### Apparmor와 SELinux의 차이

CentOS에서는 SELinux를, 그 외 (Debian 포함) 운영체제는 주로 Apparmor를 사용한다.

Apparmor는 시스템 관리자가 프로그램 프로필 별로 프로그램의 역량을 제한할 수 있게 해주는 리눅스 커널 보안 모듈이다. (내가 이해한 바, 아이폰에서 앱(프로그램 프로필)마다 사용자의 기기에 대해 위치, 알림과 같은 요소(운영체제)에 접근할 수 있도록 개별 정책을 적용하는 것과 유사하다)

MAC(강제적 접근 통제, 정의된 정책을 활용해 사용자와 프로세스의 행동을 제어)을 제공함으로서 전통적인 유닉스 DAC(임의적 접근 통제 모델, 소유자가 사용자 또는 그룹이 자원에 접근하는 것을 조절)를 지원

정책 파일을 기반으로 하는 Apparmor와 달리, SELinux는 정책 파일과 올바른 파일 시스템 레이블이 필요함.

### LVM

Logical Volume Management의 약자로, 논리적 볼륨을 관리하는 커널의 일부이자 프로그램.

[[소개] LVM(Logical Volume Manager) - 개념](https://tech.cloud.nongshim.co.kr/2018/11/23/lvmlogical-volume-manager-1-%EA%B0%9C%EB%85%90/)

다섯가지 주요 용어는 `PV` `PE` `VG` `LV` `LE` 가 있다

`PV` 란 물리적 볼륨이며, born2beroot에서는 `/dev/sda1` 과 같이 실제로 물리적으로 저장공간을 차지하고 있는 곳을 의미한다.

`PE` 란 각각의 `PV` 를 구성하는 일정한 크기의 블록을 의미함. `LV` 와 `LE` 도 동일한 구조인데, 이중 `LE` 와 일대일 대응. 

`VG` 는 볼륨 그룹의 약자이고, `PV` 로 초기화된 그룹들의 집합으로 `VG` 를 만들 수 있다.

`/HOME` `/root` `/var` 와 같은 각각의 볼륨 그룹을 예로 들 수 있다.

LVM이 불가능한 경우에, 각각의 볼륨 그룹이 모두 찼는데 용량을 추가하고 싶은 경우, 추가 디스크(물리)를 장착하여, 새로운 마운트 포인트를 만들고, 추가한 디스크에 파티션을 생성하고 포멧하여 마운트하고, 기존 내용을 복사한 후에 기존 내용 언마운트 후 새 파티션을 기존의 파티션에 마운트 해야했지만, 

LVM을 사용하는 경우 추가된 디스크에 새로운 `PV` 를 생성하고, `VG` 에 추가 후, 기존의 `VG` 의 `LV` 사이즈만 키워주면 된다. 훨씬 간편해짐.

`LV` 는 논리 볼륨을 의미하고, 파일 시스템 및 어플리케이션, Database로 사용된다. `LE` 로 구성되어있으며, `LE` 는 `PE` 와 일대일 대응하며, 서로 맵핑하며 존재합니다.

`LV` 의 유형은 선형, 스트라이프, 미러 세가지입니다.

선형 `LV` 는 가장 직관적으로 일대일 대응을 하며 `PV` 를 `LV` 로 만들어 사용하는 방식입니다

미러 `LV` 는 선형으로 입력한다는 점에서 선형 `LV` 와 비슷하지만, 하나의 `PV` 에 데이터를 작성 할 때, 또 다른 `PV` 에 동일한 데이터를 저장하는 방식입니다. 따라서, 하나의 장치에 장애가 발생하는 경우, 다른 장치의 데이터를 통해 데이터를 보호할 수 있다는 장점이 있습니다.

스트라이프 `LV` 는 `PV` 에 데이터를 선형으로 기록하지 않고, Round-Robin 방식으로 미리 지정된 `PV` 들에 데이터를 병렬로 읽고/쓰는 방식으로, 대량의 읽기/쓰기 작업시에 효율이 좋은 방식입니다.

### UFW

Uncomplicated Firewall 의 줄임말

네트워크 보안 시스템. 내부와 외부 네트워크 간의 보안을 담당한다. 프레임워크를 관리 및 조작하는데 사용.

### SSH

보안 정보를 위한 일반 텍스트 통신 (Telnet)의 결함을 보완하기 위해 SSH를 만들었다 (Secure Shell)

SSH는 Secure Shell Protocol로, 네트워크 프로토콜 중 하나이며 컴퓨터와 컴퓨터가 인터넷과 같은 Public Network를 통해 서로 통신을 할 때 보안적으로 안전하게 통신을 하기 위해 사용하는 프로토콜이다

public key와 private key를 만들어 public key로 암호화, private key로 암호화 한다. 따라서 연결시 fingerprint 생성 여부를 묻는 것.


# 주요 명령어 정리

`hostnamectl` : 현재 hostname과 운영체제, 커널에 대한 정보를 보여줌, 옵션을 통해 host와 관련된 여러 기능 수행 가능

- `hostnamectl set-hostname yohwang42` : hostname을 `yohwang42`로 수정

`dpkg -l` : 이후에 오는 것이 설치되어있는지 확인

`su -` : 유저 변경, -뒤에 유저이름을 입력하여 해당 유저로 변경하고, 디폴트는 `root`

### Sudo

엄격한 규칙에 따라 sudo를 설치, 실행하기 위해 `visudo` 를 통해서 편집

- 각 명령어 앞에는 `Defaults`
    - sudo를 사용한 인증은 비밀번호가 틀릴 경우 3번으로 제한해야 합니다.
    `passwd_tries=3`
    - 오류로 인해 오류가 발생한 경우 선택한 사용자 지정 메시지가 표시되어야 합니다.
    비밀번호는 sudo를 사용할 때 발생합니다.
    `badpass_message`
    - sudo를 사용하는 각 작업은 입력과 출력 모두에서 보관되어야 합니다. 로그 파일
    /var/log/sudo/ 폴더에 저장해야 합니다.
    `log_input` `log_output`
    - 보안상의 이유로 TTY 모드를 활성화해야 합니다.
    `requiretty`
    - 보안상의 이유로 sudo가 사용할 수 있는 경로도 제한해야 합니다.
    예시: /usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin

`secure_path="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin"`

- `visudo` 를 사용하는 이유는 `vi` 와 달리 문법에 대한 체크도 진행하기 때문! (오류 방지)

### Gruop

`groupadd user42` 를 통해 `user42` 라는 그룹 추가.

`usermod -G sudo,user42 yohwang` : `yohwang` 을 `sudo` , `user42` 그룹에”만” 추가

`usermod -aG sudo,user42 yohwang` : `yohwang` 의 그룹목록에 `sudo` , `user42` 그룹을 추가

`usermod -g user42 yohwang` : `yohwang` 의 primary group을 `user42` 로 지정

### SSH

ssh 설정여부는 `systemctl status ssh` 를 통해 확인가능

`/etc/ssh/sshd_config` 의 내용을 수정하고, `systemctl restart ssh` 를 통해 변경내용 적용가능

연결은 로컬에서 `ssh yohwang@10.12.3.1 -p 4242` 와 해당 사용자의 비밀번호를 통해 가능
(dhclient 삭제, ufw 방화벽 설정 이후 가능!)

ssh 연결시 @뒤에 붙는 주소는 연결을 시도하는(호스트) 컴퓨터의 ip를 의미하며, `ipconfig` 혹은 맥 기준 시스템 환경설정의 네트워크 탭에서 확인할 수 있다

게스트 ip의 의미는 가상머신의 ip주소로, `hostname -I` 명령어를 통해 확인 가능

`포트 포워딩` 이 선행되어야 정상적인 연결이 가능하고, 포트 포워딩이 되어있음에도 불구하고 정상작동 하지 않으면, 리부트한 후에 다시 시도해볼 것.

`Port` 의 기본값은 22로, 4242로 수정해줄 것

`PermitRootLogin` 의 기본값은 no로, 따로 수정하지 않아도 root의 ssh 연결이 불가능하지만, no라고 명시적으로 작성

<img width="479" alt="%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202022-05-10%20%EC%98%A4%ED%9B%84%201 45 11" src="https://user-images.githubusercontent.com/25719161/167569732-c11b7512-4a6d-4ef3-9fca-1ebafe4c1c58.png">

### UFW

`apt install ufw` : ufw를 사용하기 위해 설치

`ufw enable` : ufw 사용, 재부팅시 적용

`ufw status verbose` : ufw의 activate 여부를 확인 (위 내용의 적용 여부를 확인)

`ufw allow 4242` : 4242 포트를 게스트 포트로 사용하도록 허용

`ufw status numbered` : ufw의 적용 규칙들을 넘버링하여 보여줌

`ufw delete` : 뒤에 지우고자 하는 해당 규칙의 번호를 입력하면 해당 번호 삭제

<img width="691" alt="스크린샷 2022-05-03 오후 3 06 33" src="https://user-images.githubusercontent.com/25719161/166446834-4dfc442d-916b-499c-96af-4c523d38910f.png">

이 설정이 다 끝난 후 reboot 한 후에 연결 시도시 fingerprint 생성 여부를 묻고, yes를 한다면 정상적으로 작성됨

<img width="845" alt="%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202022-05-10%20%EC%98%A4%ED%9B%84%202 05 07" src="https://user-images.githubusercontent.com/25719161/167569996-9367b9c1-2ccb-4f15-b897-c80e2e18eb96.png">
<img width="846" alt="%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202022-05-10%20%EC%98%A4%ED%9B%84%202 04 47" src="https://user-images.githubusercontent.com/25719161/167570042-b480aa86-9c05-465e-901b-02225521a1e6.png">

### Apparmor

활성화 여부는 `aa-enabled`를 통해 확인가능

`apt install apparmor-utils` 를 통해 패키지를 설치하고 프로필 생성 후, `aa-enforce` 를 실행할 것!

### User

새로운 유저의 추가는 `useradd -m hakim` 과 같이 할 수 있습니다. -m은 사용자 계정 추가와 동시에 홈 디렉토리를 만들어주는 기능

이후, `passwd hakim` 을 입력하여 해당 계정에 대한 암호를 설정할 수 있고, 이는 `/etc/pam.d/common-password` 의 내용중, `pam_pwquality.so` 를 준수해야함.

user의 제거는 `deluser hakim` 과 같이 할 수 있습니다. 해당 유저가 로그인 되어있다면 삭제되지 않고, 

### Passwd

강력한 암호 정책을 설정하려면 다음 요구 사항을 준수해야 합니다.
• 비밀번호는 30일마다 만료되어야 합니다. (chage)
• 비밀번호를 수정하기 전에 허용되는 최소 일수는
2로 설정합니다. (chage)
• 사용자는 암호가 만료되기 7일 전에 경고 메시지를 받아야 합니다. (chage)
앞으로 생성되는 유저에 대해서도 해당 옵션을 적용하고 싶다면, `/etc/login.defs` 를 편집
```
PASS_MAX_DAYS 30

PASS_MIN_DAYS 2

PASS_WARN_AGE 7
```
• 비밀번호는 10자 이상이어야 합니다. 대문자와 숫자를 포함해야 합니다. (common-password, `minlen` , `dcredit` , `ucredit`)
또한 동일한 내용이 연속 3개 이상 포함되어서는 안 됩니다. (common-password, `maxrepeat`, )

• 암호에 사용자의 이름이 그대로 혹은 뒤집어진 채로 들어가서는 안됩니다. (common-password, `reject_username`)

• 새로운 비밀번호는 직전 비밀번호와 7자 이상 겹치지 않아야 합니다. (common-password, `difok`)

• root 유저의 강력한 암호 정책은 직전 비밀번호 7자 규정을 제외한 모든 규정이 적용되어야 합니다.

(common-password, `enforce_for_root`)

`enforce_for_root`는 암호 정책을 root 계정에도 동일하게 적용된다는 내용인데도 불구하고

직전 비밀번호 7자 규정은 적용되지 않는 이유는 root 계정의 비밀번호 변경시에는 직전 비밀번호를 확인하지 않기 때문입니다.

유저의 비밀번호 변경은 `passwd -e yohwang` 으로 할 수 있습니다. 해당 명령어를 입력한 뒤에 다시 로그인 할 때, 해당 규정에 맞도록 새로운 비밀번호를 정해주면 됩니다.

자기 자신의 비밀번호 변경은 passwd 를 통해 할 수 있습니다. -e 옵션을 사용하는 경우, root의 경우에도 difok의 적용을 받지만, 해당 옵션없이 명령하는 경우, root는 difok의 적용을 받지 않습니다. 

<img width="946" alt="%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202022-05-10%20%EC%98%A4%ED%9B%84%202 25 26" src="https://user-images.githubusercontent.com/25719161/167570479-497feaf8-e11c-4433-af0b-44c786eef16e.png">

### monitoring.sh

Your script must always be able to display the following information:

• The architecture of your operating system and its kernel version.

• The number of physical processors.

• The number of virtual processors.

• The current available RAM on your server and its utilization rate as a percentage.

• The current available memory on your server and its utilization rate as a percentage.

• The current utilization rate of your processors as a percentage.

• The date and time of the last reboot.

• Whether LVM is active or not.

• The number of active connections.

• The number of users using the server.

• The IPv4 address of your server and its MAC (Media Access Control) address.

• The number of commands executed with the sudo program.


```bash
#!/bin/bash

# The architecture of your operating system and its kernel version.
#uname -a | awk ‘{print “#Architecture: “ $0}’
echo "#Architecture: $(uname -a)"

# The number of physical processors.
echo "#CPU Physical: $(nproc --all)" 

# vCPU
cat /proc/cpuinfo | grep processor | wc -l | awk ‘{print “#vCPU: “ $0}’

# Memory Usage
free —mega | grep Mem | awk ‘{printf(”#Memory Usage: %d/%dMB (%.2f%%)\n”, $3, $2, ($3/$2) * 100)}’

# Disk Usage
df -m | sed ‘1d’ | awk ‘{total += $2} {used += $3} END {printf(”#Disk Usage: %d/%dMB (%.2f%%)\n”, used, total, (used/total) * 100)}’

#CPU load
mpstat | awk 'NR==4 {printf ("#CPU load: %.2f%%\n", 100-$13)}'

# Last boot
who -b | awk '{printf ("#Last boot: %s %s\n", $3, $4)}'

#LVM
lsblk | grep lvm | wc -l | awk ‘$0 ≥ 1 {print “#LVM use: yes”} $0 == 0 {print “#LVM use: no”}’

# Connection TCP
echo "#Connection TCP: $(ss -t | grep -i estab | wc -l) ESTABLISHED"

# User login
echo “#User log: $(who | wc -l)”

# Network
echo “#Network: IP $(hostname -I)($(ip addr | grep link/ether | awk ‘{print $2}’))”

# The number of commands executed with the sudo program.
# echo "#Sudo: $(journalctl _COMM=sudo | wc -l) cmd"
echo "#Sudo: $(ls ~/var/log/sudo/00/00 | wc -l) cmd"
```
```
마지막 내용은 journalctl이라고 검색하면 주로 나오는데, 실제로 명령어를 한번 실행하면 1회 이상 숫자가 늘어난다.
따라서 `sudoers`에서 input과 output의 결과를 기록하는 `var/log/sudo/00/00`의 하위폴더의 숫자를 출력하도록 수정했는데,
1300번 이상의 경우에는 `var/log/sudo/00/01`에 기록이 된다고 한다.
보다 정확한 기록을 출력하기 위해서는 다른 부분을 고려해야할듯
```
### Crontab

[리눅스 크론탭(Linux Crontab) 사용법](https://jdm.kr/blog/2)

`crontab -e` 로 crontab에서 사용할 매크로를 적용할 수 있습니다.

<img width="833" alt="Screen Shot 2022-05-11 at 8 25 56 PM" src="https://user-images.githubusercontent.com/25719161/168412301-65a4fcc8-b794-4e27-aca0-34872fd528d5.png">

`*` 의 경우 모든을 의미하고, `*/10` 의 경우 각 10 (분, 시 등) 마다 적용되는 것을 의미한다.

위의 경우, 10분 마다 `/root/monitoring.sh` 를 실행한 결과를 모든 사용자에게 출력(`wall`)한다는 의미.

`systemctl stop cron` → cron의 작동을 멈출 수 있음

`systemctl start cron` → cron의 작동을 재시작 시킬 수 있음!

## Bonus

`lsblk` 를 통해 파티셔닝 확인

<img width="833" alt="Screen Shot 2022-05-11 at 8 25 56 PM" src="https://user-images.githubusercontent.com/25719161/168412361-88ade75a-b396-4aa1-b96c-7ddb92a36ebd.png">


mariadb, wordpress 설치 완료했으나 적용이 안됨

`ss -tunlp` 시 여러 포트가 열려있었음

`apt purge <패키지>` 를 통해 해결!
