
# day4その2 copy/fileモジュール

- copy/fileモジュールを学習する。
- 本編ではサーバ寄りの内容になります。

- 目次
  - 1.copy/fileモジュールとは？
  - 2.copy/fileモジュールの使用例
  - 3.copy/fileモジュールの演習[ハンズオン]
  - copy/fileモジュールについてのまとめ
  - 4.copy/fileモジュールの演習

<br>
<br>
<br>

---

## 1. copy/fileモジュールとは？

### copyモジュールとは?

| モジュール名 | 説明 |
| :-----: | :------------------------------------------------------------------------------------------------------------ |
| copy | ファイル/ディレクトリをコピーする際に使用する | 

#### copyモジュールのパラメータ

| パラメータ(一部抜粋) | 説明 |
| :-: | :- |
| src  | コピー元を指定する。 |
| dest | コピー先を指定する。<br>コピー先にファイルが無ければ新規作成する。 |
| mode | パーミッションを指定する。 |
| content | コピー先ファイルに記述する内容を指定する。<br>変数の内容を書き込こむこともできる。 |
- copyモジュールのAnsible documentは[こちら](https://docs.ansible.com/ansible/2.9/modules/copy_module.html)

<br>
<br>
<br>

---

### fileモジュールとは?

| モジュール名 | 説明 |
| :-----: | :------------------------------------------------------------------------------------------------------------ |
| file | ファイル/ディレクトリを操作する際に使用する | 

#### fileモジュールのパラメータ

| パラメータ(一部抜粋) | 説明 |
| :-: | :- |
| path | 必須のパラメータ。操作するファイルを指定する。 |
| mode | ファイルのパーミッションを指定する。 |
| state | state:absent 既存のファイルを削除する。<br>state:touch pathに指定のファイルを作成する。<br>state:directory pathに指定のディレクトリを作成する。  |

- fileモジュールのAnsible documentは[こちら](https://docs.ansible.com/ansible/2.9/modules/file_module.html)

<br>
<br>
<br>

---

## 2.copy/fileモジュールの使用例

### copyモジュールの使用例

#### パラメータ「src/dest/mode」の使用例

- 以下は、localhostのsample.txtというファイルを、copy_sample.txtというファイルにコピーし、権限を644に設定している。
- コピー先にファイルが無ければ新規作成される。
- 同名のファイルが存在する場合は上書きされる。
```yaml
---
- name: sample_copy_1
  hosts: localhost
  gather_facts: false
  
  tasks:
  - name: copy file and change mode
    copy:
      src: /home/ec2-user/yokogushi_contents_team/ansible_practice/04-2_copy_file_modules/sample.txt
      dest: /home/ec2-user/yokogushi_contents_team/ansible_practice/04-2_copy_file_modules/copy_sample.txt
      mode: 0644
```

#### パラメータ「content」の使用例

- sample.txtにcontentで指定した内容を上書き記載する
```yaml
---
- name: sample_copy_1
  hosts: localhost
  gather_facts: false
  
  tasks:
  - name: write message
    copy:
      content: contentのテストです
      dest: /home/ec2-user/yokogushi_contents_team/ansible_practice/04-2_copy_file_modules/sample.txt
      mode: 0644
```

```yaml
[ec2-user@ip-172-31-42-108]$ cat sample.txt
contentのテストです
```

<br>
<br>
<br>

---

### fileモジュールの使用例

#### パラメータ「state」の使用例

- 以下は、localhostのsample.txtを削除する

```yaml
- name: sample_file_1
  hosts: localhost
  gather_facts: false

tasks:
  - name: delete file
    file:
      path: /home/ec2-user/yokogushi_contents_team/ansible_practice/04-2_copy_file_modules/sample.txt
      state: absent
```

<br>
<br>
<br>

---

## 3.copy/fileモジュールの演習[ハンズオン]

### copyモジュールの演習[ハンズオン]

#### 目的
  - localhostの「handson.txt」に変数「sample_handson」の内容を記述する。

#### 1.ディレクトリ移動
  - 使用するplaybook,inventoryファイルが存在するディレクトリに移動
```yaml
[ec2-user@ip-172-31-42-108]$ cd /home/ec2-user/yokogushi_contents_team/ansible_practice/04-2_copy_file
```


#### 2.仮想環境(venv)に入る 
```yaml
[ec2-user@ip-172-31-42-108]$ source /home/ec2-user/venv/bin/activate
(venv)[ec2-user@ip-172-31-42-108]$
```


#### 3.playbookの内容を確認

```yaml
---
- name: sample
  hosts: localhost
  gather_facts: false

  vars:
    sample_handson: テスト文です 

  tasks:
    - name: copy text file
      copy:
        content: "{{ sample_handson }}"
        dest: /home/ec2-user/yokogushi_contents_team/ansible_practice/04-2_copy_file/copy_directory/handson.txt
```


#### 4.playbookを実行

```yaml
(venv) [ec2-user@ip-172-31-42-108 04-2_copy_file]$ ansible-playbook copy_module_sample.yml 
[WARNING]: provided hosts list is empty, only localhost is available. Note that the implicit localhost does
not match 'all'

PLAY [sample] **********************************************************************************************

TASK [copy text file] **************************************************************************************
changed: [localhost]

PLAY RECAP *************************************************************************************************
localhost                  : ok=1    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

(venv) [ec2-user@ip-172-31-42-108 04-2_copy_file]$ 
```


#### 5.ファイル確認(事後確認)
```yaml
[ec2-user@ip-172-31-42-108 04-2_copy_file]$ ls -l handson.txt 
-rw-rw-r-- 1 ec2-user ec2-user 50 Mar 23 01:26 handson.txt

(venv) [ec2-user@ip-172-31-42-108 04-2_copy_file]$ ls -l copy_directory/
total 4
-rw-rw-r-- 1 ec2-user ec2-user 50 Mar 23 01:36 handson.txt
total 0
```
- handson.txtの内容
```yaml
テスト文です
```

<br>
<br>
<br>

---


### fileモジュールの演習[ハンズオン]

### 目的
  - localhostにディレクトリ「file_directory」を作成する


#### 1.ディレクトリ移動
  - 使用するplaybook,inventoryファイルが存在するディレクトリに移動
```yaml
[ec2-user@ip-172-31-42-108]$ cd /home/ec2-user/yokogushi_contents_team/ansible_practice/04-2_copy_file
```


#### 2.仮想環境(venv)に入る 
```yaml
[ec2-user@ip-172-31-42-108]$ source /home/ec2-user/venv/bin/activate
(venv)[ec2-user@ip-172-31-42-108]$
```


#### 3.ディレクトリ確認(事前確認)
```yaml



```


#### 4.playbookの内容を確認

```yaml
---
- name: sample
  hosts: localhost
  gather_facts: false

  tasks:
    - name: make directory
      file:
        path: /home/ec2-user/yokogushi_contents_team/ansible_practice/04-2_copy_file/file_directory
        state: directory
```


#### 5.playbookを実行

```yaml
(venv) [ec2-user@ip-172-31-42-108 04-2_copy_file]$ ansible-playbook file_module_sample.yml 
[WARNING]: provided hosts list is empty, only localhost is available. Note that the implicit localhost does
not match 'all'

PLAY [sample] **********************************************************************************************

TASK [make directory] **************************************************************************************
changed: [localhost]

PLAY RECAP *************************************************************************************************
localhost                  : ok=1    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```


#### 6.ディレクトリ確認(事後確認)

```yaml


```

<br>
<br>
<br>

---


## copy/fileモジュールについてのまとめ

- copyモジュールは、ファイルをコピーするときに使用
  - copyするときにパーミッションなどを変更してコピーすることが可能

- fileモジュールは、ファイルやディレクトリを操作するときに使用

<br>
<br>
<br>

---

## 4 copy/fileモジュールの演習

### Q1 以下のplaybookの空欄2つに当てはまるものは何でしょう。

```yaml
---
- name: exam1
  hosts: localhost
  gather_facts: false

  tasks:
    - name: content dest
      copy:
        content: exam1です
        dest: /home/ec2-user/test.txt
        ■■■■■■: 0644

    - name: file delete
      file:
        path: /home/ec2-user/test.txt
        state: ■■■■■■
```

1. absent
2. mode
3. permission
4. delete

<br>
<br>
<br>

---

### Q2 以下の条件のplaybookを作成して、実行してください
- playbook作成先ディレクトリ：「/home/ec2-user/yokogushi_contents_team/ansible_practice/04-2_copy_file_」配下
- playbook名：「vyos_module_exam_2.yml」で作成
- 実行対象ノード：localhost
- 処理内容：
  - playbook作成先ディレクトリ配下に「exam3.txt」を作成
  - 「exam.txt」を「copy_directory」配下にコピーする

<br>
<br>
<br>

---

### Q3 以下の条件のplaybookを作成して、実行してください
- 使用インベントリファイル：「/home/ec2-user/yokogushi_contents_team/ansible_practice/04-2_copy_file」配下のinventory.ini
- playbook作成先ディレクトリ：「/home/ec2-user/yokogushi_contents_team/ansible_practice/04-2_copy_file」配下
- playbook名：「vyos_module_exam_3.yml」で作成
- 実行対象ノード：vyos01
- 処理内容：
  - 「show interfaces」「show ip route」を実行
  - playbook作成先ディレクトリ配下に「vyos01_show_ip_route.log」を作成
  - 「show ip route」のみの実行結果を、「vyos01_show_ip_route.log」に記述する

<br>
<br>
<br>

---

### A1 正解：「2.mode」「1.absent」

- 以下、正しいplaybook
```yaml
---
- name: exam1
  hosts: localhost
  gather_facts: false

  tasks:
    - name: content dest
      copy:
        content: exam1です
        dest: /home/ec2-user/test.txt
        mode: 0644

    - name: file delete
      file:
        path: /home/ec2-user/test.txt
        state: absent
```

- 選択肢の解説
1. absent 指定したファイルを削除するので、正しい
2. mode パーミッションを設定するので、正しい
3. permission 存在しない
4. delete 存在しない

<br>
<br>
<br>

---

### A2.以下、解答例

- playbook
```yaml
---
- hosts: localhost
  gather_facts: false
  become: false

  tasks:
  - name: make file
    file:
      path: /home/ec2-user/yokogushi_contents_team/ansible_practice/04-2_copy_file/exam3.txt
      state: touch

  - name: copy file
    copy:
      src: /home/ec2-user/yokogushi_contents_team/ansible_practice/04-2_copy_file/exam3.txt
      dest: /home/ec2-user/yokogushi_contents_team/ansible_practice/04-2_copy_file/copy_directory
```

- playbookの実行結果
```yaml
(venv) [ec2-user@ip-172-31-42-108 04-2_copy_file]$ ansible-playbook copy_file_module_exam_2.yml 
[WARNING]: provided hosts list is empty, only localhost is available. Note that the implicit localhost does
not match 'all'

PLAY [localhost] **********************************************************************************************

TASK [make file] **********************************************************************************************
changed: [localhost]

TASK [copy file] **********************************************************************************************
changed: [localhost]

PLAY RECAP ****************************************************************************************************
localhost                  : ok=2    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```

<br>
<br>
<br>

---

### A3.以下、解答例

- playbook
```yaml
---
- name: exam3
  hosts: vyos01
  gather_facts: false

  tasks:
    - name: get show interfaces
      vyos_command:
        commands:
          - show interfaces
      register: vyos01_show_interfaces

    - name: vyos01_show_interfaces.log touch
      file:
        path: /home/ec2-user/yokogushi_contents_team/ansible_practice/04-2_copy_file/vyos01_show_interfaces.log
        state: touch

    - name: vyos01_show_interfaces.log writing
      copy:
        content: "{{ vyos01_show_interfaces.stdout[0] }}"
        dest: /home/ec2-user/yokogushi_contents_team/ansible_practice/04-2_copy_file/vyos01_show_interfaces.log
```

- playbookの実行結果
```yaml
(venv) [ec2-user@ip-172-31-42-108 04-2_copy_file]$ ansible-playbook -i inventory.ini copy_file_module_exam_3.yml 

PLAY [exam3] **************************************************************************************************

TASK [get show interfaces] ************************************************************************************
[WARNING]: Platform linux on host vyos01 is using the discovered Python interpreter at /usr/bin/python, but
future installation of another Python interpreter could change this. See
https://docs.ansible.com/ansible/2.9/reference_appendices/interpreter_discovery.html for more information.
ok: [vyos01]

TASK [vyos01_show_interfaces.log touch] ***********************************************************************
changed: [vyos01]

TASK [vyos01_show_interfaces.log writing] *********************************************************************
changed: [vyos01]

PLAY RECAP ****************************************************************************************************
vyos01                     : ok=3    changed=2    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

(venv) [ec2-user@ip-172-31-42-108 04-2_copy_file]$ 
```

- 作成した「vyos01_show_interfaces.log」の中身
```yaml
Codes: S - State, L - Link, u - Up, D - Down, A - Admin Down
Interface        IP Address                        S/L  Description
---------        ----------                        ---  -----------
eth0             10.0.0.2/24                       u/u  
eth1             192.168.1.252/24                  u/u  vyos_config-test1
                 192.168.1.254/24                       
eth2             192.168.2.252/24                  u/u  vyos_config-test2
                 192.168.2.254/24                       
lo               127.0.0.1/8                       u/u
```




