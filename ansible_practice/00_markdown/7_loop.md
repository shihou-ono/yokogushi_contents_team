
# 7. loop ディレクティブ
---
# 7-1. loop ディレクティブとは？

■同一のタスクを複数回実行したい、繰り返したいときに使用する。
- 一般的に使用されるループの例
  - ファイルモジュールを使用して複数のファイルやディレクトリの
  所有権を変更する
  - ユーザーモジュールを使用して複数のユーザーを作成する
  - 特定の結果に達するまでポーリング手順を繰り返す

■メモ
loopは、「with_list」で代用することも可能。

loopのAnsible documentは[こちら](https://docs.ansible.com/ansible/latest/user_guide/playbooks_loops.html)

---

# 7-2. loop ディレクティブの説明
## 標準ループ
以下は、繰り返しのタスクを行う場合のplaybookである。

```yaml
tasks:
  - name: add users
    user:
      name: "{{ item }}"
      state: present
      groups: "testgroup"
    loop:
      - testuser1
      - testuser2
```

---

    
| 項目 | 説明                                                                                                          |
| :-----: | :------------------------------------------------------------------------------------------------------------ |
| loop  | 繰り返すリストを定義する。ここでは、testuser1,testuser2が渡されている。 |
| "{{ item }}"  | loopで定義したリストが1つずつ代入される。ここでは、testuser1,testuser2がitemに代入される。|

以上のルールに則り、このplaybookは、userモジュールを使用して、<br>ユーザー「testuser1」,「testuser2」を、
グループ「testgroup」に追加するという内容になっている。<br>

---

## 複数のループ
以下は、複数の変数で繰り返しのタスクを行う場合のplaybookである。
loopをDict型で定義することができる。
```yaml
tasks:
  - name: add users2
    user:
      name: "{{ item.name }}"
      state: present
      groups: "{{ item.groups }}"
    loop:
      - { name: 'testuser1', groups: 'testgroup1' }
      - { name: 'testuser2', groups: 'testgroup2' }
```

---

| 項目 | 説明                                                                                                          |
| :-----: | :------------------------------------------------------------------------------------------------------------ |
| {{ item.name }}  | loopのname:のみが渡される。<br>ここでは、testuser1,testuser2が渡される。　|
| {{ item.groups }}  | loopのgroups:のみが渡される。<br>ここでは、testgroup1,testgroup2が渡される。　| 

以上のルールに則り、このplaybookは、
ユーザー「testuser1」をグループ「testgroup1」に追加、
ユーザー「testuser2」をグループ「testgroup2」に追加する
という内容になっている。

---

# 7-3. loop ディレクティブの実習
## 目的
text1.txt、text2.txtのパーミッションを変更する。
### 1.事前確認
test1.txt、test2.txtのパーミッションを確認する。
2つのテキストファイルのパーミッションは現在、test1.txtは644、test2.txtは755。

```yaml
(venv) [ec2-user@ip-172-31-44-135 ~]$ cd /home/ec2-user/yokogushi_contents_team/ansible_practice/loop/
(venv) [ec2-user@ip-172-31-44-135 loop]$ ls -l
total 4
-rw-rw-r-- 1 ec2-user ec2-user 198 Feb 15 07:41 loop_1.yml
-rw-r--r-- 1 ec2-user ec2-user   0 Feb 15 07:29 test1.txt
-rwxr-xr-x 1 ec2-user ec2-user   0 Feb 15 07:29 test2.txt
-rw-rw-r-- 1 ec2-user ec2-user   0 Feb 15 07:29 test3.txt
```

---

### 2.playbookを作成
test1.txt、test2.txtのパーミッションを777に変更する。

```yaml
$ vi loop_sample_1.yml

---
- name: loop_sample_1
  hosts: localhost
  gather_facts: false

  tasks:
    - name: chmod text
      file: path={{ playbook_dir }}/{{ item }}.txt mode=777
      loop: 
        - test1
        - test2
```
---

### 3.playbookを実行
ok=1 change=1 であることを確認。(正常に実行され、問題なく変更できたこと)
```yaml
(venv) [ec2-user@ip-172-31-44-135 loop]$ ansible-playbook loop_sample_1.yml
[WARNING]: provided hosts list is empty, only localhost is available. Note that
the implicit localhost does not match 'all'

PLAY [loop_sample_1] **************************************************************

TASK [chmod text] **************************************************************
changed: [localhost] => (item=test1)
changed: [localhost] => (item=test2)

PLAY RECAP *********************************************************************
localhost                  : ok=1    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

---

### 4.事後確認
test1.txt、test2.txtのパーミッションを確認する。
test1.txt、test2.txtどちらもパーミッションは777であることを確認。

```yaml
(venv) [ec2-user@ip-172-31-44-135 loop]$ ls -l
total 4
-rw-rw-r-- 1 ec2-user ec2-user 198 Feb 15 07:48 loop_1.yml
-rwxrwxrwx 1 ec2-user ec2-user   0 Feb 15 07:29 test1.txt
-rwxrwxrwx 1 ec2-user ec2-user   0 Feb 15 07:29 test2.txt
-rw-rw-r-- 1 ec2-user ec2-user   0 Feb 15 07:29 test3.txt
```

---

# 7-4 loopディレクティブの演習
### Q1 以下のplaybookを実行した時、出力結果はどうなるでしょうか。実際に作成してみてください。
```yaml
---
- name: loop_exam_1
  hosts: localhost
  gather_facts: false
  become: true

tasks:
  - name: add users
    user:
      name: "{{ item }}"
      state: present
    loop:
      - taro
      - hanako
```

---

### Q2 以下のplaybookを実行したとき、実行結果はどうなるでしょうか。<br>実際に作成してみてください。
```yaml
---
- name: loop_exam_2
  hosts: vyos01
  gather_facts: false

  tasks:
    - name: show commands
      vyos_command:
        commands: "{{ item }}"
      loop: 
        - show version
        - show interfaces
      register: result

    - name: debug result
      debug:
        var: result.results|map(attribute='stdout_lines') | list
```

---

### Q3 localhostに、loopを使用してファイルを作成して下さい
作成するファイル: data1.txt,data2.txt,data3.txt
作成先ディレクトリ：/home/ec2-user/home/ec2-user/yokogushi_contents_team/ansible_practice/loop

---

### Q4 vyos01にユーザー名「guest1」「guest2」「guest3」を追加し、<br>それぞれパスワードを設定して、ユーザーが追加されていることが<br>分かることを確認できるplaybookを、loopを使用して作成して下さい。

---

### A1.以下のような実行結果になります。
```yaml
(venv) [ec2-user@ip-172-31-44-135 loop]$ ansible-playbook loop_exam_1.yml 
[WARNING]: provided hosts list is empty, only localhost is available. Note that the implicit localhost does not match 'all'

PLAY [loop_exam_1] ********************************************************************************************************************

TASK [add users] *******************************************************************************************************************
changed: [localhost] => (item=taro)
changed: [localhost] => (item=hanako)

PLAY RECAP *************************************************************************************************************************
localhost                  : ok=1    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```
ユーザ「taro」「hanako」がlocalhostに追加されました。
```yaml
(venv) [ec2-user@ip-172-31-44-135 loop]$ cat /etc/passwd
…(省略)…
taro:x:1007:1009::/home/taro:/bin/bash
hanako:x:1008:1010::/home/hanako:/bin/bash
```

---

### A2.以下のような実行結果になります。
show version,show interfaceの実行結果が表示されます。
```yaml
(venv) [ec2-user@ip-172-31-44-135 loop]$ ansible-playbook -i /home/ec2-user/yokogushi_contents_team/ansible_practice/inventory.ini loop_exam_2.yml 

PLAY [loop_exam_2] ****************************************************************************************

TASK [show commands] **********************************************************************************
ok: [vyos01] => (item=show version)
ok: [vyos01] => (item=show interfaces)
[WARNING]: Platform linux on host vyos01 is using the discovered Python interpreter at
/usr/bin/python, but future installation of another Python interpreter could change this. See
https://docs.ansible.com/ansible/2.9/reference_appendices/interpreter_discovery.html for more
information.

TASK [debug result] ***********************************************************************************
ok: [vyos01] => {
    "result.results|map(attribute='stdout_lines')|list": [
        [
            [
                "Version:          VyOS 1.4-rolling-202108071508",
                "Release Train:    sagitta",
                "",
                "Built by:         vyos_bld@ae1b2315b0ae",
                "Built on:         Sat 07 Aug 2021 15:08 UTC",
                "Build UUID:       e7077035-649b-47d4-8eb2-cd6f1fc399cc",
                "Build Commit ID:  4f6c9346247bd6",
                "",
                "Architecture:     x86_64",
                "Boot via:         installed image",
                "System type:      Xen HVM guest",
                "",
                "Hardware vendor:  Xen",
                "Hardware model:   HVM domU",
                "Hardware S/N:     ec2b85f7-3bc5-3377-1fa9-ae989708ede7",
                "Hardware UUID:    ec2b85f7-3bc5-3377-1fa9-ae989708ede7",
                "",
                "Copyright:        VyOS maintainers and contributors"
            ]
        ],
        [
            [
                "Codes: S - State, L - Link, u - Up, D - Down, A - Admin Down",
                "Interface        IP Address                        S/L  Description",
                "---------        ----------                        ---  -----------",
                "eth0             10.0.0.2/24                       u/u  ",
                "eth1             192.168.1.252/24                  u/u  ",
                "eth2             192.168.2.252/24                  u/u  ",
                "lo               127.0.0.1/8                       u/u"
            ]
        ]
    ]
}

PLAY RECAP ********************************************************************************************
vyos01                     : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

---

### A3.playbookの例は以下です。(これだけが正解ではありません)
```yaml
---
- name: loop_exam_3
  hosts: localhost
  gather_facts: false

  tasks:
    - name: create text
      file: 
        path: /home/ec2-user/yokogushi_contents_team/ansible_practice/loop/{{ item }}.txt
        state: touch
        mode: '644'
      loop: 
        - data1
        - data2
        - data3
```

---
以下ような実行結果になります。
```yaml
(venv) [ec2-user@ip-172-31-44-135 loop]$ ansible-playbook loop_exam_3.yml 
[WARNING]: provided hosts list is empty, only localhost is available. Note that
the implicit localhost does not match 'all'

PLAY [loop_exam_3] *************************************************************

TASK [create text] *************************************************************
changed: [localhost] => (item=data1)
changed: [localhost] => (item=data2)
changed: [localhost] => (item=data3)

PLAY RECAP *********************************************************************
localhost                  : ok=1    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0 
```
data1.txt,data2.txt,data3.txtが作成されました。
```yaml
(venv) [ec2-user@ip-172-31-44-135 loop]$ ls -l /home/ec2-user/yokogushi_contents_team/ansible_practice/loop/
total 28
-rw-rw-r-- 1 ec2-user ec2-user   0 Mar  8 10:38 data1.txt
-rw-rw-r-- 1 ec2-user ec2-user   0 Mar  8 10:38 data2.txt
-rw-rw-r-- 1 ec2-user ec2-user   0 Mar  8 10:38 data3.txt
```

---

### A4.playbookの例は以下です。(これだけが正解ではありません)
```yaml
---
- name: loop_exam_4
  hosts: vyos01
  gather_facts: false
  become: true
    
  tasks:
    - name: user check
      vyos_command:
        commands: show system login users
      register: result

    - name: before users
      debug:
        var: result.stdout_lines

    - name: add users
      vyos_user:
        name: "{{ item.name }}"
        configured_password: "{{ item.password }}"
        state: present
      loop:
        - { name: 'guest1', password: 'pass1' }
        - { name: 'guest2', password: 'pass2' }
        - { name: 'guest3', password: 'pass3' }

    - name: user check
      vyos_command:
        commands: show system login users
      register: result

    - name: after users
      debug:
        var: result.stdout_lines
```

---
以下ような実行結果になります。
```yaml
(venv) [ec2-user@ip-172-31-44-135 loop]$ ansible-playbook -i/home/ec2-user/yokogushi_contents_team/ansible_practice/inventory.ini loop_exam_4.yml 

PLAY [loop_exam_4] ****************************************************************

TASK [user check] **************************************************************
[WARNING]: Platform linux on host vyos01 is using the discovered Python
interpreter at /usr/bin/python, but future installation of another Python
interpreter could change this. See https://docs.ansible.com/ansible/2.9/referen
ce_appendices/interpreter_discovery.html for more information.
ok: [vyos01]

TASK [before users] ************************************************************
ok: [vyos01] => {
    "result.stdout_lines": [
        [
            "Username    Type    Locked    Tty    From      Last login",
            "----------  ------  --------  -----  --------  ------------------------",
            "vyos        vyos    False     pts/1  10.0.0.1  Tue Mar  8 09:20:44 2022"
        ]
    ]
}

TASK [add users] ***************************************************************
changed: [vyos01] => (item={'name': 'guest1', 'password': 'pass1'})
changed: [vyos01] => (item={'name': 'guest2', 'password': 'pass2'})
changed: [vyos01] => (item={'name': 'guest3', 'password': 'pass3'})
[WARNING]: Module did not set no_log for update_password

TASK [user check] **************************************************************
ok: [vyos01]

TASK [after users] *************************************************************
ok: [vyos01] => {
    "result.stdout_lines": [
        [
            "Username    Type    Locked    Tty    From      Last login",
            "----------  ------  --------  -----  --------  ------------------------",
            "vyos        vyos    False     pts/1  10.0.0.1  Tue Mar  8 09:20:44 2022",
            "guest1      vyos    False                      never logged in",
            "guest2      vyos    False                      never logged in",
            "guest3      vyos    False                      never logged in"
        ]
    ]
}

PLAY RECAP *********************************************************************
vyos01                     : ok=5    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0  
```

