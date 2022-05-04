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

ssh 연결시 @뒤에 붙는 주소는 연결을 시도하는(호스트) 컴퓨터의 ip를 의미하며, `ipconfig` 혹은 맥 기준 시스템 환경설정의 네트워크 탭에서 확인할 수 있다

게스트 ip의 의미는 가상머신의 ip주소로, `hostname -I` 명령어를 통해 확인 가능

`포트 포워딩` 이 선행되어야 정상적인 연결이 가능하고, 포트 포워딩이 되어있음에도 불구하고 정상작동 하지 않으면, 리부트한 후에 다시 시도해볼 것.

`Port` 의 기본값은 22로, 4242로 수정해줄 것

`PermitRootLogin` 의 기본값은 no로, 따로 수정하지 않아도 root의 ssh 연결이 불가능하지만, no라고 명시적으로 작성

### UFW

`apt install ufw` : ufw를 사용하기 위해 설치

`ufw enable` : ufw 사용, 재부팅시 적용

`ufw status verbose` : ufw의 activate 여부를 확인 (위 내용의 적용 여부를 확인)

`ufw allow 4242` : 4242 포트를 게스트 포트로 사용하도록 허용

`ufw status numbered` : ufw의 적용 규칙들을 넘버링하여 보여줌

`ufw delete` : 뒤에 지우고자 하는 해당 규칙의 번호를 입력하면 해당 번호 삭제

<img width="691" alt="스크린샷 2022-05-03 오후 3 06 33" src="https://user-images.githubusercontent.com/25719161/166446834-4dfc442d-916b-499c-96af-4c523d38910f.png">


### User

새로운 유저의 추가는 `useradd -m hakim` 과 같이 할 수 있습니다. -m은 사용자 계정 추가와 동시에 홈 디렉토리를 만들어주는 기능

이후, `passwd hakim` 을 입력하여 해당 계정에 대한 암호를 설정할 수 있고, 이는 `/etc/pam.d/common-password` 의 내용중, `pam_pwquality.so` 를 준수해야함.

user의 제거는 `deluser hakim` 과 같이 할 수 있습니다. 해당 유저가 로그인 되어있다면 삭제되지 않고, 

강력한 암호 정책을 설정하려면 다음 요구 사항을 준수해야 합니다.
• 비밀번호는 30일마다 만료되어야 합니다. (chage)
• 비밀번호를 수정하기 전에 허용되는 최소 일수는
2로 설정합니다. (chage)
• 사용자는 암호가 만료되기 7일 전에 경고 메시지를 받아야 합니다. (chage)
• 비밀번호는 10자 이상이어야 합니다. 대문자와 숫자를 포함해야 합니다. (common-password, `minlen` , `dcredit` , `ucredit`)
또한 동일한 내용이 연속 3개 이상 포함되어서는 안 됩니다. (common-password, `maxrepeat`, )

• 암호에 사용자의 이름이 그대로 혹은 뒤집어진 채로 들어가서는 안됩니다. (common-password, `reject_username`)

• 새로운 비밀번호는 직전 비밀번호와 7자 이상 겹치지 않아야 합니다. (common-password, `difok`)

• root 유저의 강력한 암호 정책은 직전 비밀번호 7자 규정을 제외한 모든 규정이 적용되어야 합니다.

(common-password, `enforce_for_root`)

`enforce_for_root`는 암호 정책을 root 계정에도 동일하게 적용된다는 내용인데도 불구하고

직전 비밀번호 7자 규정은 적용되지 않는 이유는 root 계정의 비밀번호 변경시에는 직전 비밀번호를 확인하지 않기 때문입니다.

유저의 비밀번호 변경은 `passwd -e yohwang` 으로 할 수 있습니다. 해당 명령어를 입력한 뒤에 다시 로그인 할 때, 해당 규정에 맞도록 새로운 비밀번호를 정해주면 됩니다.
