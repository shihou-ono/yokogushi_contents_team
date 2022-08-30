# vyosのモジュール
---

# 1.  vyosのモジュールとは？
- 前章:vyosとは の後に読み進めていくことをお勧めする。
- vyosを操作するために使用する。vyosに関するモジュールは主に以下の2つがある

| モジュール名 | 説明 |
| :-----: | :------------------------------------------------------------------------------------------------------------ |
| vyos_config | vyosの設定変更などを行う時に使用。<br>コンフィギューレーションモード移行後の設定変更コマンドを実行 |
| vyos_command | vyosで主にshow系コマンドを実行する時に使用。| 

- vyos_configのAnsible documentは[こちら](https://docs.ansible.com/ansible/latest/collections/vyos/vyos/vyos_config_module.html)

- vyos_commandのAnsible documentは[こちら](https://docs.ansible.com/ansible/latest/collections/vyos/vyos/vyos_command_module.html)

---

# 2.vyosのモジュールの説明
- vyos_configの説明
  - 以下は、vyosのeth1を無効化しているplaybookである。
```yaml
  tasks:
    - name: set interfaces
      vyos_config:
        lines: 
        - set interfaces ethernet eth1 disable
```
- vyos_commandの説明
  - 以下は、vyosに対して「show interfaces」コマンドを実行しているplaybookである。
```yaml
  tasks:
    - name: show commands
      vyos_command:
        commands: show interfaces
```
---

# 3.vyosのモジュールの演習
- 目的
  - vyosにログインユーザー「test_vyos」パスワード「test123」で追加する。
  - 追加前と追加後に、showコマンドを実行してログインユーザーが追加されていることも確認する。

### 1.inventory.iniの内容を確認
vyos01,vyos02が存在する。今回は、vyos01,vyos02に対してplaybookを実行する。
```yaml
[vyos]
vyos01 ansible_host=10.0.0.2
vyos02 ansible_host=10.0.0.3

[vyos:vars]
ansible_network_os=vyos
ansible_connection=network_cli
ansible_user=vyos
ansible_password=vyos
```
---

### 2.playbookを作成
vyos_commandで現在のログインユーザーのリストを確認、
vyos_configでログインユーザーを追加する。
```yaml
---
- name: vyos_practice
  hosts: vyos
  gather_facts: false

  tasks:
    - name: show login user before
      vyos_command: 
        commands: show system login user 
      register: result

    - name: debug login user before
      debug: 
        var: result.stdout_lines

    - name: set login user
      vyos_config:
        lines: 
          - set system login user test_vyos 
          - set system login user test_vyos authentication plaintext-password test123
  
    - name: show login user after
      vyos_command: 
        commands: show system login user 
      register: result

    - name: debug login user after
      debug: 
        var: result.stdout_lines
```
---
### 3.playbookを実行
「show system login user」の実行結果を確認すると、ログインユーザー「test_vyos」が追加されていることが確認できる。
```yaml
---
(venv) [ec2-user@ip-172-31-44-135 ansible_practice]$ ansible-playbook -i inventory.ini vyos/vyos_practice1.yml 

PLAY [set login user] **********************************************************************************************************

TASK [show login user before] **************************************************************************************************
[WARNING]: Platform linux on host vyos02 is using the discovered Python interpreter at /usr/bin/python, but future installation
of another Python interpreter could change this. See
https://docs.ansible.com/ansible/2.9/reference_appendices/interpreter_discovery.html for more information.
ok: [vyos02]
[WARNING]: Platform linux on host vyos01 is using the discovered Python interpreter at /usr/bin/python, but future installation
of another Python interpreter could change this. See
https://docs.ansible.com/ansible/2.9/reference_appendices/interpreter_discovery.html for more information.
ok: [vyos01]

TASK [debug login user before] *************************************************************************************************
ok: [vyos01] => {
    "result.stdout_lines": [
        [
            "Username    Type    Locked    Tty    From      Last login",
            "----------  ------  --------  -----  --------  ------------------------",
            "vyos        vyos    False     pts/0  10.0.0.1  Fri Apr  8 07:55:22 2022"
        ]
    ]
}
ok: [vyos02] => {
    "result.stdout_lines": [
        [
            "Username    Type    Locked    Tty    From      Last login",
            "----------  ------  --------  -----  --------  ------------------------",
            "vyos        vyos    False     pts/0  10.0.0.1  Fri Apr  8 07:55:22 2022"
        ]
    ]
}

TASK [set login user] **********************************************************************************************************
changed: [vyos01]
changed: [vyos02]

TASK [show login user after] ***************************************************************************************************
ok: [vyos01]
ok: [vyos02]

TASK [debug login user after] **************************************************************************************************
ok: [vyos01] => {
    "result.stdout_lines": [
        [
            "Username    Type    Locked    Tty    From      Last login",
            "----------  ------  --------  -----  --------  ------------------------",
            "vyos        vyos    False     pts/0  10.0.0.1  Fri Apr  8 07:55:22 2022",
            "test_vyos   vyos    False                      never logged in"
        ]
    ]
}
ok: [vyos02] => {
    "result.stdout_lines": [
        [
            "Username    Type    Locked    Tty    From      Last login",
            "----------  ------  --------  -----  --------  ------------------------",
            "vyos        vyos    False     pts/0  10.0.0.1  Fri Apr  8 07:55:22 2022",
            "test_vyos   vyos    False                      never logged in"
        ]
    ]
}

PLAY RECAP *********************************************************************************************************************
vyos01                     : ok=5    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
vyos02                     : ok=5    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0 
```
---

# 4. vyosのモジュールの演習<br>

### Q1 以下のplaybookの空欄に当てはまるものは何でしょう。
```yaml
---
- name: vyos_exam1
  hosts: vyos
  gather_facts: false

  tasks:
    - name: show login user before
      ■■■■■■:
        commands: show interfaces

```
1. vyos_config
2. vyos_configure
3. vyos_commands
4. vyos_command

---

### Q2 以下のplaybookの空欄に当てはまるものは何でしょう。
```yaml
---
- name: vyos_exam2
  hosts: vyos
  gather_facts: false

  tasks:
    - name: set interfaces
      vyos_config:
        ■■■■■■: 
        - delete interfaces ethernet eth0 disable
```
1. commands
2. interfaces
3. lines
4. config
---

### Q3 以下の条件のplaybookを作成して下さい。
- vyos01,vyos02に対して実行。
- 「show ip route」を実行。
- 実行結果を出力させること。
---

### Q4 以下の条件のplaybookを作成して下さい。
- vyos01,vyos02に対し実行。
- discriptionを以下に設定する。
  - eth1 ==> service_nw001
  - eth2 ==> service_nw002
- 事前と事後のdiscriptionを出力させて、discriptionが変更されていることを確認する。
---

### A1.正解は「4.vyos_command」です。
以下が正しいplaybookになります。
```yaml
---
- name: vyos_exam1
  hosts: vyos
  gather_facts: false

  tasks:
    - name: show login user before
      vyos_command: 
        commands: show interfaces 
```
選択肢の解説は以下です。
1. vyos_config　vyosの設定変更などを行う時に使用するモジュールです。
2. vyos_configure　存在しないモジュールです。
3. vyos_commands　2と同じく、存在しないモジュールです。
4. vyos_command　vyosにshowコマンドを実行する時使用するモジュールなので、正しいです。

---

### A2.正解は「3. lines」です。
以下が正しいplaybookになります。
```yaml
---
- name: vyos_exam2
  hosts: vyos
  gather_facts: false

  tasks:
    - name: set interfaces
      vyos_config:
        lines: 
        - delete interfaces ethernet eth0 disable
```
他の選択肢の解説は以下です。
1. commands vyos_commandのパラメーターです。
2. interfaces 存在しないパラメーターです。
3. config 2と同じく、存在しないパラメーターです。
---
### A3.playbookの例は以下です。(これだけが正解ではありません)
```yaml
---
- name: vyos_exam3
  hosts: vyos
  gather_facts: false

  tasks:
    - name: show interface
      vyos_command:
        commands: show ip route
      register: result

    - name: debug ip route
      debug: 
        var: result.stdout_lines
```
---
上記playbookの実行結果は以下です。
```yaml
(venv) [ec2-user@ip-172-31-44-135 ansible_practice]$ ansible-playbook -i inventory.ini vyos/vyos_exam3.yml 

PLAY [vyos_exam3] *************************************************************************************************************************

TASK [show interface] *********************************************************************************************************************
[WARNING]: Platform linux on host vyos02 is using the discovered Python interpreter at /usr/bin/python, but future installation of another
Python interpreter could change this. See https://docs.ansible.com/ansible/2.9/reference_appendices/interpreter_discovery.html for more
information.
ok: [vyos02]
[WARNING]: Platform linux on host vyos01 is using the discovered Python interpreter at /usr/bin/python, but future installation of another
Python interpreter could change this. See https://docs.ansible.com/ansible/2.9/reference_appendices/interpreter_discovery.html for more
information.
ok: [vyos01]

TASK [debug ip route] *********************************************************************************************************************
ok: [vyos01] => {
    "result.stdout_lines": [
        [
            "Codes: K - kernel route, C - connected, S - static, R - RIP,",
            "       O - OSPF, I - IS-IS, B - BGP, E - EIGRP, N - NHRP,",
            "       T - Table, v - VNC, V - VNC-Direct, A - Babel, D - SHARP,",
            "       F - PBR, f - OpenFabric,",
            "       > - selected route, * - FIB route, q - queued, r - rejected, b - backup",
            "",
            "K>* 0.0.0.0/0 [0/0] via 10.0.0.1, eth0, 01:51:59",
            "C>* 10.0.0.0/24 is directly connected, eth0, 01:51:59",
            "C>* 192.168.1.0/24 is directly connected, eth1, 01:02:01",
            "C>* 192.168.2.0/24 is directly connected, eth2, 01:51:59"
        ]
    ]
}
ok: [vyos02] => {
    "result.stdout_lines": [
        [
            "Codes: K - kernel route, C - connected, S - static, R - RIP,",
            "       O - OSPF, I - IS-IS, B - BGP, E - EIGRP, N - NHRP,",
            "       T - Table, v - VNC, V - VNC-Direct, A - Babel, D - SHARP,",
            "       F - PBR, f - OpenFabric,",
            "       > - selected route, * - FIB route, q - queued, r - rejected, b - backup",
            "",
            "K>* 0.0.0.0/0 [0/0] via 10.0.0.1, eth0, 01:51:59",
            "C>* 10.0.0.0/24 is directly connected, eth0, 01:51:59",
            "C * 192.168.1.0/24 is directly connected, eth1, 01:01:58",
            "C>* 192.168.1.0/24 is directly connected, eth1, 01:02:01",
            "C * 192.168.2.0/24 is directly connected, eth2, 01:01:58",
            "C>* 192.168.2.0/24 is directly connected, eth2, 01:51:59"
        ]
    ]
}

PLAY RECAP ********************************************************************************************************************************
vyos01                     : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
vyos02                     : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0 
```
---
### A4.playbookの例は以下です。(これだけが正解ではありません)
```yaml
---
- name: vyos_exam4
  hosts: vyos
  gather_facts: false

  tasks:
  - name: show interface
    vyos_command:
      commands: show interfaces
    register: result

  - name: debug interface before
    debug: 
      var: result.stdout_lines

  - name: change discription
    vyos_config: 
      lines: 
      - set interfaces ethernet eth1 description to_service_nw001
      - set interfaces ethernet eth2 description to_service_nw002

  - name: show interface after
    vyos_command:
      commands: show interfaces
    register: result

  - name: debug interface after
    debug: 
      var: result.stdout_lines
```
---
上記playbookの実行結果は以下です。
```yaml
(venv) [ec2-user@ip-172-31-44-135 ansible_practice]$ ansible-playbook -i inventory.ini vyos/vyos_exam4.yml 

PLAY [vyos_exam4] *************************************************************************************************************************

TASK [show interface] *********************************************************************************************************************
[WARNING]: Platform linux on host vyos01 is using the discovered Python interpreter at /usr/bin/python, but future installation of another
Python interpreter could change this. See https://docs.ansible.com/ansible/2.9/reference_appendices/interpreter_discovery.html for more
information.
ok: [vyos01]
[WARNING]: Platform linux on host vyos02 is using the discovered Python interpreter at /usr/bin/python, but future installation of another
Python interpreter could change this. See https://docs.ansible.com/ansible/2.9/reference_appendices/interpreter_discovery.html for more
information.
ok: [vyos02]

TASK [debug interface before] *************************************************************************************************************
ok: [vyos02] => {
    "result.stdout_lines": [
        [
            "Codes: S - State, L - Link, u - Up, D - Down, A - Admin Down",
            "Interface        IP Address                        S/L  Description",
            "---------        ----------                        ---  -----------",
            "eth0             10.0.0.3/24                       u/u  ",
            "eth1             192.168.1.253/24                  u/u  ",
            "eth2             192.168.2.253/24                  u/u  ",
            "lo               127.0.0.1/8                       u/u"
        ]
    ]
}
ok: [vyos01] => {
    "result.stdout_lines": [
        [
            "Codes: S - State, L - Link, u - Up, D - Down, A - Admin Down",
            "Interface        IP Address                        S/L  Description",
            "---------        ----------                        ---  -----------",
            "eth0             10.0.0.2/24                       u/u  ",
            "eth1             192.168.1.252/24                  u/u  ",
            "                 192.168.1.254/24                       ",
            "eth2             192.168.2.252/24                  u/u  ",
            "                 192.168.2.254/24                       ",
            "lo               127.0.0.1/8                       u/u"
        ]
    ]
}

TASK [change discription] *****************************************************************************************************************
changed: [vyos02]
changed: [vyos01]

TASK [show interface after] ***************************************************************************************************************
ok: [vyos02]
ok: [vyos01]

TASK [debug vrrp after] *******************************************************************************************************************
ok: [vyos02] => {
    "result.stdout_lines": [
        [
            "Codes: S - State, L - Link, u - Up, D - Down, A - Admin Down",
            "Interface        IP Address                        S/L  Description",
            "---------        ----------                        ---  -----------",
            "eth0             10.0.0.3/24                       u/u  ",
            "eth1             192.168.1.253/24                  u/u  to_service_nw001",
            "eth2             192.168.2.253/24                  u/u  to_service_nw002",
            "lo               127.0.0.1/8                       u/u"
        ]
    ]
}
ok: [vyos01] => {
    "result.stdout_lines": [
        [
            "Codes: S - State, L - Link, u - Up, D - Down, A - Admin Down",
            "Interface        IP Address                        S/L  Description",
            "---------        ----------                        ---  -----------",
            "eth0             10.0.0.2/24                       u/u  ",
            "eth1             192.168.1.252/24                  u/u  to_service_nw001",
            "                 192.168.1.254/24                       ",
            "eth2             192.168.2.252/24                  u/u  to_service_nw002",
            "                 192.168.2.254/24                       ",
            "lo               127.0.0.1/8                       u/u"
        ]
    ]
}

PLAY RECAP ********************************************************************************************************************************
vyos01                     : ok=5    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
vyos02                     : ok=5    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0  
