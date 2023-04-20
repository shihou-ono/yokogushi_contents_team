
# day7 loopディレクティブ

- loopディレクティブについて学習する

- 目次
  - 1.loopディレクティブとは？
  - 2.loopディレクティブの使用例
  - 3.loopディレクティブの実習(ハンズオン)
  - loopディレクティブのまとめ
  - 4.loopディレクティブの演習問題

<br>
<br>
<br>

---

## 1.loopディレクティブとは？

- 同一のタスクを複数回実行したい、繰り返したいときに使用する。
- loopディレクティブを使用する例
  - 複数のファイルを作成、削除する
  - 特定の結果に達するまで手順を繰り返す ...etc

- 補足
  - loopは、「with_list」で代用することも可能。

- loopのAnsible documentは[こちら](https://docs.ansible.com/ansible/latest/user_guide/playbooks_loops.html)

---

## 2.loop ディレクティブの説明

- 以下の2つの種類に分けてloopの説明を実施する
  - 標準loop
  - 複数のloop

### 標準loop

- 以下は、loopで「Apple」「Banana」「Peach」を定義し、debug moduleを使用してmsgを出力させているplaybookである。
- loopで繰り返すリストを定義することができる。
- loopで定義した内容は、変数「item」に格納されるようになっている。
```yaml
---
- name: sample
  hosts: localhost
  gather_facts: false

  tasks:
  - name: debug fruits 
    debug:
      msg: "{{ item }}"
    loop:
      - Apple
      - Banana
      - Peach
```

- このplaybookを実行すると、以下のような実行結果となる。
- loopで定義したリストが1つずつ代入され、msgが3回出力されている。
```yaml
$ ansible-playbook playbook.yaml

PLAY [sample] ******************************************************************

TASK [debug fruits] *************************************************************
ok: [localhost] => (item=Apple) => {
    "msg": "Apple"
}
ok: [localhost] => (item=Banana) => {
    "msg": "Banana"
}
ok: [localhost] => (item=Peach) => {
    "msg": "Peach"
}

PLAY RECAP *********************************************************************
localhost                  : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```


### 複数のloop

- 以下は、loopで「fruits: 'Apple', color: 'Red'」「fruits: 'Banana', color: 'Yellow'」「fruits: 'Peach', color: 'Pink'」を定義し、debug moduleを使用してmsgを出力させているplaybookである。
- loopをDict型で定義することができる。
- loopをDict型で定義したとき、変数「item.key」(fruits: 'xxxx')「item.value」(color: 'yyyy')に格納されるようになっている。
```yaml
---
- name: sample
  hosts: localhost
  gather_facts: false

  tasks:
  - name: debug fruits 
    debug:
      msg: "The {{ item.key }} is {{ item.value }}"
    loop:
      - { fruits: 'Apple', color: 'Red' }
      - { fruits: 'Banana', color: 'Yellow' }
      - { fruits: 'Peach', color: 'Pink' }
```

- このplaybookを実行すると、以下のような実行結果となる。
- loopで定義したリストが1つずつ代入され、msgが3回出力されている。
```yaml
PLAY [Sample] ******************************************************************

TASK [Debug fruits] *************************************************************
ok: [localhost] => (item={'fruits': 'Apple', 'color': 'Red'}) => {
    "msg": "The Apple is Red"
}
ok: [localhost] => (item={'fruits': 'Banana', 'color': 'Yellow'}) => {
    "msg": "The Banana is Yellow"
}
ok: [localhost] => (item={'fruits': 'Peach', 'color': 'Pink'}) => {
    "msg": "The Peach is Pink"
}

PLAY RECAP **********************************************************************
localhost                  : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```

<br>
<br>
<br>

---

## 3.loop ディレクティブの実習

### 目的
  - loopディレクティブを使用して、test1.txt、test2.txt、test3.txtを新規作成する

### 1.ディレクトリ移動
  - 使用するplaybook,inventoryファイルが存在するディレクトリに移動
```yaml
[ec2-user@ip-172-31-42-108]$ cd /home/ec2-user/yokogushi_contents_team/ansible_practice/07_loop
```

### 2.仮想環境(venv)に入る 
```yaml
[ec2-user@ip-172-31-42-108]$ source /home/ec2-user/venv/bin/activate
(venv)[ec2-user@ip-172-31-42-108]$
```

### 3.playbookの内容を確認
- varsで変数「file_names」に「test1.txt」「test2.txt」「test3.txt」を定義
- file_namesをloopさせ、file moduleのpathパラメータに代入
```yaml
---
- name: sample1
  hosts: localhost
  gather_facts: false

  vars:
    file_names:
      - test1.txt
      - test2.txt
      - test3.txt

  tasks:
    - name: Create files
      file:
        path: /home/ec2-user/yokogushi_contents_team/ansible_practice/07_loop/{{ item }}
        state: touch
      loop: "{{ file_names }}"
```

### 4.playbookを実行
```yaml
(venv) [ec2-user@ip-172-31-42-108 07_loop]$ ansible-playbook loop_sample_1.yml 
[WARNING]: provided hosts list is empty, only localhost is available. Note that the implicit localhost
does not match 'all'

PLAY [sample1] ********************************************************************************************

TASK [Create files] ***************************************************************************************
changed: [localhost] => (item=test1.txt)
changed: [localhost] => (item=test2.txt)
changed: [localhost] => (item=test3.txt)

PLAY RECAP ************************************************************************************************
localhost                  : ok=1    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

(venv) [ec2-user@ip-172-31-42-108 07_loop]$ 
```

### 5.事後確認
```yaml
(venv) [ec2-user@ip-172-31-42-108 07_loop]$ ls -l /home/ec2-user/yokogushi_contents_team/ansible_practice/07_loop/
total 20
-rw-rw-r-- 1 ec2-user ec2-user 218 Mar  3 10:50 loop_exam_1.yml
-rw-rw-r-- 1 ec2-user ec2-user 337 Mar  3 10:50 loop_exam_2.yml
-rw-rw-r-- 1 ec2-user ec2-user 302 Mar  3 10:50 loop_exam_3.yml
-rw-rw-r-- 1 ec2-user ec2-user 749 Mar  3 10:50 loop_exam_4.yml
-rw-rw-r-- 1 ec2-user ec2-user 328 Apr 20 04:20 loop_sample_1.yml
-rw-rw-r-- 1 ec2-user ec2-user   0 Apr 20 04:22 test1.txt
-rw-rw-r-- 1 ec2-user ec2-user   0 Apr 20 04:22 test2.txt
-rw-rw-r-- 1 ec2-user ec2-user   0 Apr 20 04:22 test3.txt
```

<br>
<br>
<br>

---

## loopディレクティブについてのまとめ



<br>
<br>
<br>

---

## 4.loopディレクティブの演習

---


### Q1 実行結果から、以下のplaybookの空欄に当てはまるものを考えてください。
- playbook
```yaml
---
- name: exam1
  hosts: localhost
  gather_facts: false

  vars:
    fruits:
      - Apple
      - Banana
      - Peach

  tasks:
    - name: debug fruits
      debug:
        msg: "{{ ■■■ }}"
      loop: "{{ ■■■ }}"
```

- 実行結果
```yaml
(venv) [ec2-user@ip-172-31-42-108 07_loop]$ ansible-playbook loop_exam_1.yml 
[WARNING]: provided hosts list is empty, only localhost is available. Note that the implicit localhost
does not match 'all'

PLAY [exam1] **********************************************************************************************

TASK [debug fruits] ***************************************************************************************
ok: [localhost] => (item=Apple) => {
    "msg": "Apple"
}
ok: [localhost] => (item=Banana) => {
    "msg": "Banana"
}
ok: [localhost] => (item=Peach) => {
    "msg": "Peach"
}

PLAY RECAP ************************************************************************************************
localhost                  : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

(venv) [ec2-user@ip-172-31-42-108 07_loop]$ 
```

<br>
<br>
<br>

---

### Q2 出力結果をloopの中から3つだけ出力させたいとき、■■■にはどんな条件式が入るでしょうか。
- playbook
```yaml
---
- name: exam2
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

<br>
<br>
<br>

---

### Q3 以下の条件のplaybookを作成して、実行してください
- 使用インベントリファイル：「/home/ec2-user/yokogushi_contents_team/ansible_practice/07_loop」配下のinventory.ini
- playbook作成先ディレクトリ：「/home/ec2-user/yokogushi_contents_team/ansible_practice/07_loop」配下
- playbook名：「loop_exam_3.yml」で作成
- 実行対象ノード：host01、host02
- 処理内容：
  - host01、host02の「/tmp」配下に、「loop_test1.txt」「loop_test2.txt」を作成する(パーミッション等は特に指定なし)

<br>
<br>
<br>

---

### Q4 以下の条件のplaybookを作成して、実行してください
- 使用インベントリファイル：「/home/ec2-user/yokogushi_contents_team/ansible_practice/07_loop」配下のinventory.ini
- playbook作成先ディレクトリ：「/home/ec2-user/yokogushi_contents_team/ansible_practice/07_loop」配下
- playbook名：「loop_exam_4.yml」で作成
- 実行対象ノード：vyos01、vyos02
- 処理内容：
  - loopディレクティブを使用して、vyos01、vyos02の eth1・eth2にそれぞれdescriptionを設定する
  - eth1→「loop_test1」eth2→「loop_test2」と設定する
  - 事前、事後で「show interfaces」を実施し、実行結果を出力させる（余力があれば）

<br>
<br>
<br>

---

### A1 正解：以下、解答例

- playbook
```yaml
---
- name: exam1
  hosts: localhost
  gather_facts: false

  vars:
    fruits:
      - Apple
      - Banana
      - Peach

  tasks:
    - name: debug fruits
      debug:
        msg: "{{ item }}" #解答
      loop: "{{ fruits }}" #解答
```

<br>
<br>
<br>

---

### A2 正解：以下、解答例

- **item > 26** などでもOK。そのほか解答あれば教えてください。 
```yaml
---
- name: exam2
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
      when: item < 26 #解答例
```

<br>
<br>
<br>

---

### A3 以下、解答例

- playbook
```yaml
---
- name: exam3
  hosts: centos7
  gather_facts: false

  tasks:
    - name: make file
      file:
        path: /tmp/{{ item }}
        state: touch
      loop:
        - loop_test1.txt
        - loop_test2.txt
```

- 実行結果
```yaml
(venv) [ec2-user@ip-172-31-42-108 07_loop]$ ansible-playbook -i inventory.ini loop_exam_3.yml 

PLAY [exam3] ****************************************************************************************

TASK [make file] ************************************************************************************
changed: [host01] => (item=loop_test1.txt)
changed: [host02] => (item=loop_test1.txt)
changed: [host02] => (item=loop_test2.txt)
changed: [host01] => (item=loop_test2.txt)

PLAY RECAP ******************************************************************************************
host01                     : ok=1    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
host02                     : ok=1    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

(venv) [ec2-user@ip-172-31-42-108 07_loop]$ 
```

- ファイルが作成されているか確認
```yaml
(venv) [ec2-user@ip-172-31-42-108 07_loop]$ docker exec -it host01 /bin/bash
[root@host01 /]# ls -l /tmp/
total 0
drwx------ 2 root root 37 Apr 13 04:54 ansible_yum_payload_Vc17Y3
-rw-r--r-- 1 root root  0 Apr 20 04:55 loop_test1.txt
-rw-r--r-- 1 root root  0 Apr 20 04:55 loop_test2.txt
-rw-r--r-- 1 root root  0 Apr 13 09:16 test_exam4.txt
[root@host01 /]# 
[root@host01 /]# exit
exit
(venv) [ec2-user@ip-172-31-42-108 07_loop]$ docker exec -it host02 /bin/bash
[root@host02 /]# ls -l /tmp/
total 0
drwx------ 2 root root 37 Apr 13 04:54 ansible_yum_payload_FWE23G
-rw-r--r-- 1 root root  0 Apr 20 04:55 loop_test1.txt
-rw-r--r-- 1 root root  0 Apr 20 04:55 loop_test2.txt
[root@host02 /]# 
[root@host02 /]# exit
exit
(venv) [ec2-user@ip-172-31-42-108 07_loop]$ 
```

<br>
<br>
<br>

---

### A4 以下、解答例

- playbook
```yaml
---
- name: exam4
  hosts: vyos
  gather_facts: false

  tasks:
    - name: check before interface description #任意の実施
      vyos_command:
        commands:
          - show interfaces
      register: result

    - name: check before interface description debug #任意の実施
      debug:
        var: result.stdout_lines

    - name: set descriptions
      vyos_config:
        lines:
          - set interfaces ethernet {{ item.ethernet }} description {{ item.description }}
      loop: 
        - { ethernet: 'eth1', description: 'loop_test1' }
        - { ethernet: 'eth2', description: 'loop_test2' }

    - name: check after interface description #任意の実施
      vyos_command:
        commands:
          - show interfaces
      register: result

    - name: check after interface description debug #任意の実施
      debug:
        var: result.stdout_lines
```

- 実行結果
```yaml
(venv) [ec2-user@ip-172-31-42-108 07_loop]$ ansible-playbook -i inventory.ini loop_exam_4.yml 

PLAY [exam4] ****************************************************************************************

TASK [check before interface description] ***********************************************************
[WARNING]: Platform linux on host vyos01 is using the discovered Python interpreter at
/usr/bin/python, but future installation of another Python interpreter could change this. See
https://docs.ansible.com/ansible/2.9/reference_appendices/interpreter_discovery.html for more
information.
ok: [vyos01]
[WARNING]: Platform linux on host vyos02 is using the discovered Python interpreter at
/usr/bin/python, but future installation of another Python interpreter could change this. See
https://docs.ansible.com/ansible/2.9/reference_appendices/interpreter_discovery.html for more
information.
ok: [vyos02]

TASK [check before interface description debug] *****************************************************
ok: [vyos01] => {
    "result.stdout_lines": [
        [
            "Codes: S - State, L - Link, u - Up, D - Down, A - Admin Down",
            "Interface        IP Address                        S/L  Description",
            "---------        ----------                        ---  -----------",
            "eth0             10.0.0.2/24                       u/u  ",
            "eth1             192.168.1.252/24                  u/u  vyos_config-test1",
            "                 192.168.1.254/24                       ",
            "eth2             192.168.2.252/24                  u/u  vyos_config-test2",
            "                 192.168.2.254/24                       ",
            "lo               127.0.0.1/8                       u/u"
        ]
    ]
}
ok: [vyos02] => {
    "result.stdout_lines": [
        [
            "Codes: S - State, L - Link, u - Up, D - Down, A - Admin Down",
            "Interface        IP Address                        S/L  Description",
            "---------        ----------                        ---  -----------",
            "eth0             10.0.0.3/24                       u/u  ",
            "eth1             192.168.1.253/24                  u/u  debug_exam",
            "eth2             192.168.2.253/24                  u/u  vyos_config-test2",
            "lo               127.0.0.1/8                       u/u"
        ]
    ]
}

TASK [set descriptions] *****************************************************************************
changed: [vyos01] => (item={'ethernet': 'eth1', 'description': 'loop_test1'})
changed: [vyos02] => (item={'ethernet': 'eth1', 'description': 'loop_test1'})
changed: [vyos01] => (item={'ethernet': 'eth2', 'description': 'loop_test2'})
changed: [vyos02] => (item={'ethernet': 'eth2', 'description': 'loop_test2'})

TASK [check after interface description] ************************************************************
ok: [vyos02]
ok: [vyos01]

TASK [check after interface description debug] ******************************************************
ok: [vyos01] => {
    "result.stdout_lines": [
        [
            "Codes: S - State, L - Link, u - Up, D - Down, A - Admin Down",
            "Interface        IP Address                        S/L  Description",
            "---------        ----------                        ---  -----------",
            "eth0             10.0.0.2/24                       u/u  ",
            "eth1             192.168.1.252/24                  u/u  loop_test1",
            "                 192.168.1.254/24                       ",
            "eth2             192.168.2.252/24                  u/u  loop_test2",
            "                 192.168.2.254/24                       ",
            "lo               127.0.0.1/8                       u/u"
        ]
    ]
}
ok: [vyos02] => {
    "result.stdout_lines": [
        [
            "Codes: S - State, L - Link, u - Up, D - Down, A - Admin Down",
            "Interface        IP Address                        S/L  Description",
            "---------        ----------                        ---  -----------",
            "eth0             10.0.0.3/24                       u/u  ",
            "eth1             192.168.1.253/24                  u/u  loop_test1",
            "eth2             192.168.2.253/24                  u/u  loop_test2",
            "lo               127.0.0.1/8                       u/u"
        ]
    ]
}

PLAY RECAP ******************************************************************************************
vyos01                     : ok=5    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
vyos02                     : ok=5    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

(venv) [ec2-user@ip-172-31-42-108 07_loop]$
```
