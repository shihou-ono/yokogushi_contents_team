# 6. 条件分岐
---

# 6-1. 条件分岐をさせるためには？

- 条件分岐をさせたい時は、**Whenディレクティブ**を使用する。

  - 一般的に使用されるwhenディレクティブの使用例
    - 特定の条件に当てはまるタスクのみ実行したい時
    - 特定の条件に当てはまるタスクのみスキップしたい時

- whenディレクティブのAnsible documentは[こちら](https://docs.ansible.com/ansible/2.9_ja/user_guide/playbooks_conditionals.html)

---

# 6-2. whenディレクティブの説明

主に以下の条件式を用いて、whenディレクティブを使用する。
  - 演算子 


| 比較演算子 | trueになるとき|
| :-----: | :------------------------------------------------------------------------------------------------------------ |
| X == Y | X の値と Y の値が等しいとき |
| X != Y | X の値と Y の値が等しくないとき |
| X > Y | X の値が Y の値より大きいとき | 
| X >= Y | X の値が Y の値より大きいか等しいとき |

---

| 論理演算子 | trueになるとき|
| :-----: | :------------------------------------------------------------------------------------------------------------ |
| not 条件X | 条件X が Falseのとき |
| 条件X and 条件Y | 条件X と条件Y がともに True のとき |
| 条件X or 条件Y | 条件X か条件Y のどちらかが True のとき | 

| in 演算子 | trueになるとき|
| :-----: | :------------------------------------------------------------------------------------------------------------ |
| A in [X, Y, Z] | A と同じ値が X, Y, Z の中にあるとき |
| A not in [X, Y, Z] | A と同じ値が X, Y, Z の中にないとき |

---
- 比較演算子を使用して条件に一致した場合、タスクを実行する。
playbookは以下のようになる。
```yaml
tasks:
  - name: 
    yum: 
      name: httpd
      state: latest
    when: ansible_os_family == 'RedHat'
```
| 項目 | 説明 |
| :-----: | :---------------------------------------------------------------------- |
when: ansible_os_family == 'RedHat' | ターゲットノードのOSがRedHatの時タスクを実行する|

このplaybookは、ターゲットノードのOSがRedHatの時、
httpをインストールするという内容になっている。

---

- 論理演算子を使用して条件に一致した場合、タスクを実行する。
playbookは以下のようになる。
```yaml
tasks:
  - name: 
    yum: 
      name: httpd
      state: latest
    when: ansible_os_family == 'RedHat' or ansible_os_family == 'CentOS'
```
| 項目 | 説明 |
| :-----: | :---------------------------------------------------------------------- |
when: ansible_os_family == 'RedHat' or ansible_os_family == 'CentOS' | ターゲットノードのOSがRedHatかCentOSの時タスクを実行する |

このplaybookは、ターゲットノードのOSがRedHatかCentOSの時、
httpをインストールするという内容になっている。

---

- in演算子を使用して条件に一致した場合、タスクを実行する。
playbookは以下のようになる。
```yaml
tasks:
  - name: 
    yum: 
      name: httpd
      state: latest
    when: ansible_os_family in ['RedHat',CentOS]
```
| 項目 | 説明 |
| :-----: | :---------------------------------------------------------------------- |
ansible_os_family in ['RedHat',CentOS] | ターゲットノードのOSが、リスト(RedHat,CentOS)の中にある時タスクを実行する |

このplaybookは、ターゲットノードのOSがRedHatで、
リストにRedHatがあったとき、httpをインストールするという内容になっている。

---

# 6-3. whenディレクティブの実習(ハンズオン)
- 目的
vyos01だけに「show ip route」を実行し、実行結果を出力する。
### 1.inventory.iniの内容を確認
vyos01,vyos02が存在するので、
今回はvyos01だけに実行させるようにplaybookを作成する。

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
when: inventory_hostname == 'vyos01' で、vyos01のみタスクを実行するようにする。

```yaml
$ vi when_practice.yml

---
- name: when_sample_1
  hosts: vyos
  gather_facts: false

  tasks:
    - name: show commands
      vyos_command:
        commands: show ip route
      register: result
      when: inventory_hostname == 'vyos01'

    - name: debug result
      debug: 
        var: result.stdout_lines
```
---

### 3.playbookを実行
vyos01はOK=2、vyos02はOK=1,skipped=1であることを確認。
```yaml
(venv) [ec2-user@ip-172-31-44-135 ansible_practice]$ ansible-playbook -i inventory.ini when/when_sample_1.yml 

PLAY [when_sample_1] ****************************************************************************************************************************

TASK [show commands] **********************************************************************************************************************
skipping: [vyos02]
[WARNING]: Platform linux on host vyos01 is using the discovered Python interpreter at /usr/bin/python, but future installation of another
Python interpreter could change this. See https://docs.ansible.com/ansible/2.9/reference_appendices/interpreter_discovery.html for more
information.
ok: [vyos01]

TASK [debug result] ***********************************************************************************************************************
ok: [vyos01] => {
    "result.stdout_lines": [
        [
            "Codes: K - kernel route, C - connected, S - static, R - RIP,",
            "       O - OSPF, I - IS-IS, B - BGP, E - EIGRP, N - NHRP,",
            "       T - Table, v - VNC, V - VNC-Direct, A - Babel, D - SHARP,",
            "       F - PBR, f - OpenFabric,",
            "       > - selected route, * - FIB route, q - queued, r - rejected, b - backup",
            "",
            "K>* 0.0.0.0/0 [0/0] via 10.0.0.1, eth0, 00:25:43",
            "C>* 10.0.0.0/24 is directly connected, eth0, 00:25:43",
            "C>* 192.168.1.0/24 is directly connected, eth1, 00:25:43",
            "C>* 192.168.2.0/24 is directly connected, eth2, 00:25:43"
        ]
    ]
}
ok: [vyos02] => {
    "result.stdout_lines": "VARIABLE IS NOT DEFINED!"
}

PLAY RECAP ********************************************************************************************************************************
vyos01                     : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
vyos02                     : ok=1    changed=0    unreachable=0    failed=0    skipped=   rescued=0    ignored=0
```

---

# 6-4 whenディレクティブの演習
<br>

### Q1 以下のplaybookを実行した時、出力結果はどうなるでしょうか。実際に作成してみてください。
```yaml
---
- name: when_exam_1
  hosts: localhost
  gather_facts: false
  become: true

  tasks:
    - name: add user
      user: 
        name: jon
        state: present
      register: result
      ignore_errors: true
  
    - name: debug result OK
      debug:
        msg: add user OK!
      when: result is succeeded

    - name: debug result ERROR
      debug:
        msg: add user ERROR!
      when: result is failed
```
---

### Q2 出力結果をloopの中から3つだけ出力させたいとき、■■■にはどんな条件式が入るでしょうか。
```yaml
---
- name: command
  hosts: localhost
  gather_facts: false

  tasks:
    - name: when/loop
      debug: 
        var: item
      loop:
        - 3
        - 10
        - 25
        - 140
        - 233
        - 350
      when: ■■■
```

---

### Q3 以下の条件でplaybookを作成して下さい。
- whenディレクティブを使用して、
  vyos01,vyos02に対して「show ip route」を実行させること
  host01,host02に対してcommandモジュールを使用して「route」を実行させること
  (commandモジュールのAnsibleドキュメントは[こちら](https://docs.ansible.com/ansible/2.9/modules/command_module.html]))
- 実行結果を出力すること。
- 今回指定するinventory.iniは、以下を使用すること
  /home/ec2-user/yokogushi_contents_team/init_settings/inventory.ini

---

### Q4 以下の条件でplaybookを作成して下さい。
- コマンドが正常に実行できなかった時にタスクを実行させること。
- playbookを実行するときには敢えてコマンドを正常に実行できない状況を発生させること。

---

### A1.以下のような実行結果になります。
変数を登録し、変数の内容によって条件を指定することも出来ます。詳細は[こちら](https://docs.ansible.com/ansible/2.9_ja/user_guide/playbooks_conditionals.html)

```yaml
(venv) [ec2-user@ip-172-31-44-135 ansible_practice]$ ansible-playbook when/when_exam_1.yml 
[WARNING]: provided hosts list is empty, only localhost is available. Note that the implicit localhost does not match 'all'

PLAY [when_exam_1] ****************************************************************************************************************************

TASK [add user] ***************************************************************************************************************************
changed: [localhost]

TASK [debug result OK] ********************************************************************************************************************
ok: [localhost] => {
    "msg": "add user OK!"
}

TASK [debug result ERROR] *****************************************************************************************************************
skipping: [localhost]

PLAY RECAP ********************************************************************************************************************************
localhost                  : ok=2    changed=1    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0   
```
ユーザ「jon」がlocalhostに追加されたので、add user OK!と出力されました。
```yaml
(venv) [ec2-user@ip-172-31-44-135 ansible_practice]$ less /etc/passwd
・・・(省略)・・・
taro:x:1007:1009::/home/taro:/bin/bash
hanako:x:1008:1010::/home/hanako:/bin/bash
jon:x:1009:1011::/home/jon:/bin/bash
```

---

### A2.例は以下です。(これだけが正解ではありません)

```yaml
 ・item < 26
 ・item > 26 
```
whenとloopは併せて使用することがよくあります。
item < 26 を入れた場合、実行結果は以下です。3,10,25が出力されます。
```yaml
(venv) [ec2-user@ip-172-31-44-135 ansible_practice]$ ansible-playbook when/when_exam2.yml 
[WARNING]: provided hosts list is empty, only localhost is available. Note that the implicit localhost does not match 'all'

PLAY [command] ****************************************************************************************************************************

TASK [when/loop] **************************************************************************************************************************
ok: [localhost] => (item=3) => {
    "ansible_loop_var": "item",
    "item": 3
}
ok: [localhost] => (item=10) => {
    "ansible_loop_var": "item",
    "item": 10
}
ok: [localhost] => (item=25) => {
    "ansible_loop_var": "item",
    "item": 25
}
skipping: [localhost] => (item=140) 
skipping: [localhost] => (item=233) 
skipping: [localhost] => (item=350) 

PLAY RECAP ********************************************************************************************************************************
localhost                  : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0 
```

---

### A3.playbookの例は以下です。(これだけが正解ではありません)
```yaml
---
- name: when_exam_3
  hosts: all
  gather_facts: false

  tasks:
    - name: vyos show ip route
      vyos_command:
        commands: show ip route
      register: result
      when: inventory_hostname in groups['vyos']

    - name: vyos debug result
      debug:
        var: result.stdout_lines
      when: inventory_hostname in groups['vyos']

    - name: centos7 route
      command: route
      register: result
      when: inventory_hostname in groups['centos7']

    - name: centos7 debug result
      debug:
        var: result.stdout_lines
      when: inventory_hostname in groups['centos7']

```

---

<br>

上記playbookの実行結果は以下です。
```yaml
(venv) [ec2-user@ip-172-31-44-135 ansible_practice]$ ansible-playbook -i inventory.ini when/when_exam_3.yml 


PLAY [when_exam_3] *************************************************************************************************************************************

TASK [vyos show ip route] *****************************************************************************************************************************
skipping: [host01]
skipping: [host02]
[WARNING]: Platform linux on host vyos02 is using the discovered Python interpreter at /usr/bin/python, but future installation of another Python
interpreter could change this. See https://docs.ansible.com/ansible/2.9/reference_appendices/interpreter_discovery.html for more information.
ok: [vyos02]
[WARNING]: Platform linux on host vyos01 is using the discovered Python interpreter at /usr/bin/python, but future installation of another Python
interpreter could change this. See https://docs.ansible.com/ansible/2.9/reference_appendices/interpreter_discovery.html for more information.
ok: [vyos01]

TASK [vyos debug result] ******************************************************************************************************************************
skipping: [host01]
skipping: [host02]
ok: [vyos01] => {
    "result.stdout_lines": [
        [
            "Codes: K - kernel route, C - connected, S - static, R - RIP,",
            "       O - OSPF, I - IS-IS, B - BGP, E - EIGRP, N - NHRP,",
            "       T - Table, v - VNC, V - VNC-Direct, A - Babel, D - SHARP,",
            "       F - PBR, f - OpenFabric,",
            "       > - selected route, * - FIB route, q - queued, r - rejected, b - backup",
            "",
            "K>* 0.0.0.0/0 [0/0] via 10.0.0.1, eth0, 05:29:08",
            "C>* 10.0.0.0/24 is directly connected, eth0, 05:29:08",
            "C * 192.168.1.0/24 is directly connected, eth1, 05:28:44",
            "C>* 192.168.1.0/24 is directly connected, eth1, 05:29:08",
            "C * 192.168.2.0/24 is directly connected, eth2, 05:28:44",
            "C>* 192.168.2.0/24 is directly connected, eth2, 05:29:08"
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
            "K>* 0.0.0.0/0 [0/0] via 10.0.0.1, eth0, 05:29:08",
            "C>* 10.0.0.0/24 is directly connected, eth0, 05:29:08",
            "C>* 192.168.1.0/24 is directly connected, eth1, 05:29:08",
            "C>* 192.168.2.0/24 is directly connected, eth2, 05:29:08"
        ]
    ]
}
次ページに続く
```

---
<br>

```yaml
TASK [centos7 route] **********************************************************************************************************************************
skipping: [vyos01]
skipping: [vyos02]
changed: [host02]
changed: [host01]

TASK [centos7 debug result] ***************************************************************************************************************************
skipping: [vyos01]
skipping: [vyos02]
ok: [host02] => {
    "result.stdout_lines": [
        "Kernel IP routing table",
        "Destination     Gateway         Genmask         Flags Metric Ref    Use Iface",
        "default         ip-10-0-0-1.ap- 0.0.0.0         UG    0      0        0 eth0",
        "10.0.0.0        0.0.0.0         255.255.255.0   U     0      0        0 eth0",
        "192.168.2.0     0.0.0.0         255.255.255.0   U     0      0        0 eth1"
    ]
}
ok: [host01] => {
    "result.stdout_lines": [
        "Kernel IP routing table",
        "Destination     Gateway         Genmask         Flags Metric Ref    Use Iface",
        "default         ip-10-0-0-1.ap- 0.0.0.0         UG    0      0        0 eth0",
        "10.0.0.0        0.0.0.0         255.255.255.0   U     0      0        0 eth0",
        "192.168.1.0     0.0.0.0         255.255.255.0   U     0      0        0 eth1"
    ]
}

PLAY RECAP ********************************************************************************************************************************************
host01                     : ok=2    changed=1    unreachable=0    failed=0    skipped=2    rescued=0    ignored=0   
host02                     : ok=2    changed=1    unreachable=0    failed=0    skipped=2    rescued=0    ignored=0   
vyos01                     : ok=2    changed=0    unreachable=0    failed=0    skipped=2    rescued=0    ignored=0   
vyos02                     : ok=2    changed=0    unreachable=0    failed=0    skipped=2    rescued=0    ignored=0   

```

---

### A4.playbookの例は以下です。(これだけが正解ではありません)

vyos01だけに「show interfaces」を実行し、実行結果の内容を出力します。
このコマンドが正常に実行できた時は「OK!」
正常に実行できなかった時は「NG!」を出力します。
以下は、「show interfacess」となっているため、「NG!」が出力されます。

---

<br>

```yaml
---
- name: when_exam_4
  hosts: vyos
  gather_facts: false

  tasks:
    - name: show commands
      vyos_command:
        commands: show interfacess
      ignore_errors: true
      register: result
      when: inventory_hostname == 'vyos01'

    - name: debug result
      debug: 
        var: result.stdout_lines
      when: 
      - inventory_hostname == 'vyos01'
      - result is succeeded

    - name: debug result OK message
      debug: 
        msg: OK!
      when: 
      - inventory_hostname == 'vyos01'
      - result is succeeded

    - name: debug result NG message
      debug: 
        msg: NG!
      when: 
      - inventory_hostname == 'vyos01'
      - result is failed
```

---
上記playbookの実行結果は以下です。
```yaml
(venv) [ec2-user@ip-172-31-44-135 ansible_practice]$ ansible-playbook -i inventory.ini when/when_exam_4.yml 

PLAY [when_exam_4] *************************************************************************************************************************

TASK [show commands] **********************************************************************************************************************
skipping: [vyos02]
[WARNING]: Platform linux on host vyos01 is using the discovered Python interpreter at /usr/bin/python, but future installation of another
Python interpreter could change this. See https://docs.ansible.com/ansible/2.9/reference_appendices/interpreter_discovery.html for more
information.
fatal: [vyos01]: FAILED! => {"ansible_facts": {"discovered_interpreter_python": "/usr/bin/python"}, "changed": false, "msg": "show interfacess\r\n\r\n  Invalid command: show [interfacess]\r\n\r\nvyos@vyos01:~$ "}
...ignoring

TASK [debug result] ***********************************************************************************************************************
skipping: [vyos01]
skipping: [vyos02]

TASK [debug result OK message] ************************************************************************************************************
skipping: [vyos01]
skipping: [vyos02]

TASK [debug result NG message] ************************************************************************************************************
skipping: [vyos02]
ok: [vyos01] => {
    "msg": "NG!"
}

PLAY RECAP ********************************************************************************************************************************
vyos01                     : ok=2    changed=0    unreachable=0    failed=0    skipped=2    rescued=0    ignored=1   
vyos02                     : ok=0    changed=0    unreachable=0    failed=0    skipped=4    rescued=0    ignored=0   
```
