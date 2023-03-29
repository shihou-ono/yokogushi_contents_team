
# day5 variable

- variable(変数)を学習する

- 目次
  - 1.variable(変数)とは？
  - 2.variable(変数)の説明
  - 3.
  - まとめ
  - 4.

<br>
<br>
<br>

---

## 1. variable(変数)とは？

- 特定の文字列に対して様々な値を入れることができる箱のようなもの。<br>
→このように変数に対して文字列や数字などの値を入れることを**変数定義**という。

- 数学でよく見る以下の式も変数の一種である。
```yaml
x = 2      <-「x」という変数は数字の「2」として定義されている

y = 3      <-「y」という変数は数字の「3」として定義されている

x + y = 5  <-「x=2」+「y=3」のため右辺は「5」となる
```


- ただし、プログラミング上での変数定義では必ずしも左辺と右辺がイコールになるわけではない。
- 以下に、プログラミングならではの変数定義例を記載する。
```python
$ python3

>>> x = 2       <-A. 「x」という変数は数字の「2」として定義されている
>>> x = 3 + x   <-B. 「x」という変数は新たに「3 + x」として定義される
>>> print(x)　　<-C. 　BではA時点で定義された「x=2」が右辺の「x」に代入されるため、Cの「x」の出力結果は「3 + 2 = 5」となる
5
>>>
```

<br>
<br>
<br>

---

## 2.variable(変数)の説明

- 変数は任意の値を定義することが多いが、デフォルトで定義されているものも存在する。
- playbookにおいて任意の値を定義する方法は以下の2つである。

### 変数「vars」の使用例
- varは「variable(変数)」の略で、`vars`とは変数を集めておく場所のようなもの。
- 以下にplaybook内のVarsセクションにてよく使用される`vars`について紹介する。 - 下記のパラメータにより定義された変数はTasksセクションで使用することができる。

<br>

| パラメータ | 説明 |
| :----- | :---------------------- | 
| `vars` | playbook内で変数定義する際に使用するパラメータ。 |
| `vars_files` | playbook外から変数定義されたyamlファイルを読み込む際に使用するパラメータ。複数ファイルを読み込むことも可能。 |
| `vars_prompt` | playbook実行時に定義した変数の値を対話的にユーザへ求めるようにするパラメータ。記録として残したくない情報(パスワードなど)などを使用したい時に使用することが多い。 |


- その他にもplaybook外でディレクトリを作成し、グループ/ホスト共通で使用する変数ファイルを作成することもできる。

| ディレクトリ名 | 説明 |
| :----- | :---------------------- | 
| `group_vars` | 対象のグループが共通で使用することのできる変数ファイルを格納するディレクトリ。 |
| `host_vars` | 対象のホストが共通で使用することのできる変数ファイルを格納するディレクトリ。 |


- `group_vars`について横地さんが記事にされているため紹介する。ブログの内容は[こちら](https://tekunabe.hatenablog.jp/entry/2018/12/15/ansible_group_vars_dir)をクリック。


### 変数「set_fact」の使用例

- Tasksセクションで変数定義をしたい時に使用するパラメータ。
- 使用方法は先程紹介したVarsセクションで使用するパラメータの中の`vars`と似ている。

- `set_fact`の使用例を以下に記載。
```yaml
---
- name: variable_sample
  hosts: vyos01
  gather_facts: no
  
  tasks:
    - name: test
      set_fact:                   
        test: "Hello Ansible!"  # <-「set_fact:」配下で「<変数名>: <値>」で変数定義を行う  
```

- 次にデフォルトで定義されているものとして、以下にAnsibleが用意している変数を紹介する。

| 変数の種類 | 説明 |
| :----- | :---------------------- | 
| マジック変数  | ユーザが直接設定することのできない変数。<br>Ansibleがシステム内の状態を反映してこの変数を常に上書きしている。 |
| ファクト変数  | 現在実行中のホストに関連する情報(inventory_hostname)を含む変数。<br>playbook内で`gather_facts: no`を指定した場合は使用不可。 |
| 接続変数 | ターゲットホストへのアクション実行方法を具体的に設定する時に使用。 |

<br>

変数についてのAnsibleの公式ドキュメントは[こちら](https://docs.ansible.com/ansible/2.9_ja/reference_appendices/special_variables.html)

### マジック変数
- 以下にマジック変数の中でも特に使用頻度が高いものを紹介する。

| 変数名 | 説明 |
| :----- | :---------------------- | 
| `group_names` | 現在実行中のホストが所属するグループの一覧。 |
| `groups` | インベントリー内の全グループを含むディクショナリーマップ。 |
| `inventory_hostname` | 現在実行中のホストのイベントリー名。 |
| `ansible_play_name` | 現在実行されているplaybookの名前。 |
| `playbook_dir` | 現在実行されているplaybookのディレクトリパス。 |
| `inventory_dir` | インベントリファイルのディレクトリパス。 |

### ファクトのためのマジック変数

- ファクトとは、ホストに関するあらゆる情報を保存した変数のことを指す。
- `hostvars`というマジック変数を使用すると、現在実行中のホストを含むターゲットホストのファクトにアクセスすることが可能になる。

| 変数名 | 説明 |
| :----- | :---------------------- | 
| `hostvars` | ターゲットホストのファクトを集約して格納した変数。 |

### ファクト変数
- 以下にファクト変数の一部を紹介する。

| 変数名 | 説明 |
| :----- | :---------------------- | 
| `ansible_facts` | `gather_facts: no` の場合はターゲットホストのファクト情報が格納されない。`gather_facts: no` であっても、未定義ではなく、値は空になる。 |

### 接続変数
- 以下に接続変数の中でも特に使用頻度が高いものを紹介する。

<br>

| 変数名 | 説明 |
| :----- | :---------------------- | 
| `ansible_network_os` | ターゲットホストのプラットフォーム情報。 |
| `ansible_connection` | ターゲットホストへの接続方式。 |
| `ansible_user` | SSH接続するときのユーザ情報。 |
| `ansible_password` | SSH接続するときのパスワード情報。 |
| `become` | root権限昇格の有無。`false`を指定することで昇格する。 |

### 接続変数
- 接続変数は以下のインベントリファイルのように`vars`として設定することができる。
- ※接続変数でなくても変数定義は可能だが、インベントリファイル内では接続変数を記載することが多い。

```yaml
[vyos]
vyos01 ansible_host=10.0.0.2
vyos02 ansible_host=10.0.0.3

[vyos:vars]                       # <-vyosグループで指定されたホストへの接続で使用するvarsを定義
ansible_network_os=vyos
ansible_connection=network_cli
ansible_user=vyos
ansible_password=vyos               
```

---


### register
- 実行タスクの様々な結果を格納する`register`というものがある。
- 主な使用方法は、実行結果を`register`で変数に詰めて、その変数の内容から処理を変えたりエラーメッセージを出力するなど多岐にわたる。
- 以下はplaybook作成例である。

```yaml
---
- name: variable_sample
  hosts: vyos01
  gather_facts: false
  
  tasks:
    - name: get show version
      vyos_command:
        commands: 'show version'
      register: result              # <-vyos_commandで取得したshowコマンドをresultという変数へ格納

    - name: debug result
      debug:
        msg: "{{ result.stdout_lines[0] }}"            
```

- 前ページのplaybook実行例を以下に記載。

```yaml 
PLAY [variable_sample] ************************************************************************************************************

TASK [get show version] ************************************************************************************************************
ok: [vyos01]

TASK [debug result] ****************************************************************************************************************
ok: [vyos01] => {
    "msg": [
        "Version:          VyOS 1.4-rolling-202108071508",      # <-取得したshowコマンドの結果が表示されている
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
        "Hardware S/N:     ec248bf0-f7a1-3f1a-e76d-4dc0168b8dc4",
        "Hardware UUID:    ec248bf0-f7a1-3f1a-e76d-4dc0168b8dc4",
        "",
        "Copyright:        VyOS maintainers and contributors"
    ]
}

PLAY RECAP *************************************************************************************************************************
vyos01                     : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

<br>
<br>
<br>

---

## 3. variable(変数)の実習

### 目的
  - 1. Varsセクションにて`vars`を使用して変数定義を行い、変数の中身をdebugで出力する
  - 2. `set_fact`を使用して変数定義を行い、変数の中身をdebugで出力する
  - 3. マジック変数の中身をdebugで出力する
  - 4. ファクト変数の中身の一部をdebugで出力する

### 1.ディレクトリ移動
  - 使用するplaybook,inventoryファイルが存在するディレクトリに移動
```yaml
[ec2-user@ip-172-31-42-108]$ cd /home/ec2-user/yokogushi_contents_team/ansible_practice/05_variable
```

### 2.仮想環境(venv)に入る 
```yaml
[ec2-user@ip-172-31-42-108]$ source /home/ec2-user/venv/bin/activate
(venv)[ec2-user@ip-172-31-42-108]$
```

### 3.インベントリファイルの内容を確認
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

### 目的1 Varsセクションにて`vars`を使用して変数定義を行い、変数の中身をdebugで出力する

### 4.playbookの内容を確認
- 「test1」という変数に「Hello Ansible!」という文字列を定義
- Varsセクションで定義した「test」の変数の中身を出力
```yaml
---
- name: sample1
  hosts: localhost
  gather_facts: false

  vars:
    test: "Hello Ansible!"

  tasks:
    - name: sample1 debug vars
      debug:
        var: test
```

### 5.playbookを実行
```yaml
(venv) [ec2-user@ip-172-31-42-108 05_variable]$ ansible-playbook variable_sample_1.yml 
[WARNING]: provided hosts list is empty, only localhost is available. Note that the implicit localhost
does not match 'all'

PLAY [sample1] ********************************************************************************************

TASK [sample1 debug vars] *********************************************************************************
ok: [localhost] => {
    "test": "Hello Ansible!"
}

PLAY RECAP ************************************************************************************************
localhost                  : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

(venv) [ec2-user@ip-172-31-42-108 05_variable]$ 
```

### 目的2 set_factを使用して変数定義を行い、変数の中身をdebugで出力する

### 4.playbookの内容を確認
- 「set_fact」を使用して「test」という変数に「Hello Ansible!」という文字列を定義
- 「set_fact」で定義した「test」の変数の中身を出力
```yaml
---
- name: sample2
  hosts: localhost
  gather_facts: false

  tasks:
    - name: sample2 set_fact
      set_fact:
        test: "Hello Ansible!"

    - name: sample2 debug set_fact 
      debug:
        var: test
```

### 5.playbookを実行
```yaml
(venv) [ec2-user@ip-172-31-42-108 05_variable]$ ansible-playbook variable_sample_2.yml 
[WARNING]: provided hosts list is empty, only localhost is available. Note that the implicit localhost
does not match 'all'

PLAY [sample2] ********************************************************************************************

TASK [sample2 set_fact] ***********************************************************************************
ok: [localhost]

TASK [sample2 debug set_fact] *****************************************************************************
ok: [localhost] => {
    "test": "Hello Ansible!"
}

PLAY RECAP ************************************************************************************************
localhost                  : ok=2    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

(venv) [ec2-user@ip-172-31-42-108 05_variable]$ 
```

### 目的3 マジック変数の中身をdebugで出力する

### 4.playbookの内容を確認
- 「ansible_play_name」はマジック変数のため、変数定義の必要なし
```yaml
---
- name: sample3
  hosts: localhost
  gather_facts: false

  tasks:
    - name: sample3 magic debug
      debug:
        var: ansible_play_name
```

### 5.playbookを実行
- - 「ansible_play_name」に格納されたplaybookの名前(nameで定義した内容)が出力されていることを確認
```yaml
(venv) [ec2-user@ip-172-31-42-108 05_variable]$ ansible-playbook variable_sample_3.yml 
[WARNING]: provided hosts list is empty, only localhost is available. Note that the implicit localhost
does not match 'all'

PLAY [sample3] ********************************************************************************************

TASK [sample3 magic debug] ********************************************************************************
ok: [localhost] => {
    "ansible_play_name": "sample3"
}

PLAY RECAP ************************************************************************************************
localhost                  : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

(venv) [ec2-user@ip-172-31-42-108 05_variable]$ 
```

### 目的4 ファクト変数の中身の一部をdebugで出力する

### 4.playbookの内容を確認
- 「gather_facts: no」を省略した場合は「ansible_facts」にファクト情報が格納される。
```yaml
---
- name: sample4
  hosts: vyos01

  tasks:
    - name: debug ansible_facts
      debug:
        var: ansible_facts

    - name: debug ansible_facts.net_hostname
      debug:
        var: ansible_facts.net_hostname
```

### 5.playbookを実行
- 「ansible_facts」の中身全てを出力すると量が多すぎる
- 「ansible_facts」に格納されているディクショナリの中の値(value)を取り出すことができる
```yaml
(venv) [ec2-user@ip-172-31-42-108 05_variable]$ ansible-playbook -i inventory.ini variable_sample_4.yml 

PLAY [sample4] ********************************************************************************************

TASK [Gathering Facts] ************************************************************************************
[WARNING]: Ignoring timeout(10) for vyos_facts
[WARNING]: default value for `gather_subset` will be changed to `min` from `!config` v2.11 onwards
[WARNING]: Platform linux on host vyos01 is using the discovered Python interpreter at /usr/bin/python,
but future installation of another Python interpreter could change this. See
https://docs.ansible.com/ansible/2.9/reference_appendices/interpreter_discovery.html for more information.
ok: [vyos01]

TASK [debug ansible_facts] **********************************************************************************************
ok: [vyos01] => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python",
        "net_api": "cliconf",
        "net_commits": [
            {
                "by": "root",
                "comment": null,
                "datetime": "2023-03-16 09:55:22 ",
                "revision": "0",
                "via": "vyos-boot-config-loader"
            },
            {
                "by": "vyos",
                "comment": "configured by vyos_config",
                "datetime": "2023-03-15 08:55:14 ",
                "revision": "1",
                "via": "cli"
            },
            {
                "by": "vyos",
                "comment": "configured by vyos_config",
                "datetime": "2023-03-15 07:03:46 ",
                "revision": "2",
                "via": "cli"
            },
            {
                "by": "vyos",
                "comment": "configured by vyos_config",
                "datetime": "2023-03-15 06:34:27 ",
                "revision": "3",
                "via": "cli"
            },
            {
                "by": "vyos",
                "comment": "configured by vyos_config",
                "datetime": "2023-03-14 08:40:23 ",
                "revision": "4",
                "via": "cli"
            },
            {
                "by": "vyos",
                "comment": "configured by vyos_config",
                "datetime": "2023-03-14 08:27:32 ",
                "revision": "5",
                "via": "cli"
            },
            {
                "by": "vyos",
                "comment": null,
                "datetime": "2023-03-14 08:21:09 ",
                "revision": "6",
                "via": "cli"
            },
            {
                "by": "root",
                "comment": null,
                "datetime": "2023-03-14 08:20:14 ",
                "revision": "7",
                "via": "vyos-boot-config-loader"
            },
            {
                "by": "vyos",
                "comment": null,
                "datetime": "2023-03-14 08:18:18 ",
                "revision": "8",
                "via": "cli"
            },
            {
                "by": "vyos",
                "comment": null,
                "datetime": "2023-03-14 08:16:12 ",
                "revision": "9",
                "via": "cli"
            },
            {
                "by": "vyos",
                "comment": null,
                "datetime": "2023-03-14 08:03:19 ",
                "revision": "10",
                "via": "cli"
            },
            {
                "by": "vyos",
                "comment": null,
                "datetime": "2023-03-14 02:09:46 ",
                "revision": "11",
                "via": "cli"
            },
            {
                "by": "root",
                "comment": null,
                "datetime": "2023-03-14 02:07:39 ",
                "revision": "12",
                "via": "vyos-boot-config-loader"
            },
            {
                "by": "vyos",
                "comment": null,
                "datetime": "2023-03-14 02:06:03 ",
                "revision": "13",
                "via": "cli"
            },
            {
                "by": "vyos",
                "comment": null,
                "datetime": "2023-03-09 11:20:43 ",
                "revision": "14",
                "via": "cli"
            },
            {
                "by": "vyos",
                "comment": null,
                "datetime": "2023-03-09 02:55:06 ",
                "revision": "15",
                "via": "cli"
            },
            {
                "by": "vyos",
                "comment": "configured by vyos_config",
                "datetime": "2023-03-07 11:05:26 ",
                "revision": "16",
                "via": "cli"
            },
            {
                "by": "root",
                "comment": null,
                "datetime": "2023-03-07 11:01:16 ",
                "revision": "17",
                "via": "vyos-boot-config-loader"
            }
        ],
        "net_config": [
            "set high-availability vrrp group service_nw01 interface 'eth1'\nset high-availability vrrp group service_nw01 priority '150'\nset high-availability vrrp group service_nw01 virtual-address 192.168.1.254/24\nset high-availability vrrp group service_nw01 vrid '10'\nset high-availability vrrp group service_nw02 interface 'eth2'\nset high-availability vrrp group service_nw02 priority '150'\nset high-availability vrrp group service_nw02 virtual-address 192.168.2.254/24\nset high-availability vrrp group service_nw02 vrid '20'\nset high-availability vrrp sync-group MAIN member 'service_nw01'\nset high-availability vrrp sync-group MAIN member 'service_nw02'\nset interfaces ethernet eth1 address '192.168.1.252/24'\nset interfaces ethernet eth1 description 'vyos_config-test1'\nset interfaces ethernet eth1 ipv6 address no-default-link-local\nset interfaces ethernet eth2 address '192.168.2.252/24'\nset interfaces ethernet eth2 description 'vyos_config-test2'\nset interfaces ethernet eth2 ipv6 address no-default-link-local\nset service ssh\nset system config-management commit-revisions '100'\nset system conntrack modules ftp\nset system conntrack modules h323\nset system conntrack modules nfs\nset system conntrack modules pptp\nset system conntrack modules sip\nset system conntrack modules sqlnet\nset system conntrack modules tftp\nset system console device ttyS0 speed '115200'\nset system host-name 'vyos01'\nset system login user vyos authentication encrypted-password '$6$QxPS.uk6mfo$9QBSo8u1FkH16gMyAVhus6fU3LOzvLR9Z9.82m3tiHFAxTtIkhaZSWssSgzt4v4dGAL8rhVQxTg0oAG9/q11h/'\nset system login user vyos authentication plaintext-password ''\nset system ntp server time1.vyos.net\nset system ntp server time2.vyos.net\nset system ntp server time3.vyos.net\nset system syslog global facility all level 'info'\nset system syslog global facility protocols level 'debug'",
            "0   2023-03-16 09:55:22 by root via vyos-boot-config-loader\n1   2023-03-15 08:55:14 by vyos via cli\n    configured by vyos_config\n2   2023-03-15 07:03:46 by vyos via cli\n    configured by vyos_config\n3   2023-03-15 06:34:27 by vyos via cli\n    configured by vyos_config\n4   2023-03-14 08:40:23 by vyos via cli\n    configured by vyos_config\n5   2023-03-14 08:27:32 by vyos via cli\n    configured by vyos_config\n6   2023-03-14 08:21:09 by vyos via cli\n7   2023-03-14 08:20:14 by root via vyos-boot-config-loader\n8   2023-03-14 08:18:18 by vyos via cli\n9   2023-03-14 08:16:12 by vyos via cli\n10  2023-03-14 08:03:19 by vyos via cli\n11  2023-03-14 02:09:46 by vyos via cli\n12  2023-03-14 02:07:39 by root via vyos-boot-config-loader\n13  2023-03-14 02:06:03 by vyos via cli\n14  2023-03-09 11:20:43 by vyos via cli\n15  2023-03-09 02:55:06 by vyos via cli\n16  2023-03-07 11:05:26 by vyos via cli\n    configured by vyos_config\n17  2023-03-07 11:01:16 by root via vyos-boot-config-loader\n18  2023-03-07 11:00:47 by root via init"
        ],
        "net_gather_network_resources": [],
        "net_gather_subset": [
            "neighbors",
            "default",
            "config"
        ],
        "net_hostname": "vyos01",
        "net_neighbors": {},
        "net_python_version": "2.7.18",
        "net_serialnum": null,
        "net_system": "vyos",
        "net_version": "VyOS 1.3-rolling-202303060135",
        "network_resources": {}
    }
}

TASK [debug ansible_facts.net_hostname] **********************************************************************************************
ok: [vyos01] => {
    "ansible_facts.net_hostname": "vyos01"
}

PLAY RECAP ************************************************************************************************
vyos01                     : ok=3    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   

(venv) [ec2-user@ip-172-31-42-108 05_variable]$ 
```

<br>
<br>
<br>

---

## variable(変数)についてのまとめ

- playbookにおいて任意の値を変数定義する方法は主に2通りある
  - `vars`で定義
  - `set_fact`で定義

- playbookにおいてデフォルトで定義されているものとして以下が存在する
  - マジック変数
  - ファクト変数
  - 接続変数


<br>
<br>
<br>

---


## 4.variable(変数)の演習

---


### Q1 














## 1. Varsセクションにて`vars`を使用して変数定義を行い、変数の中身をdebugで出力する

以下のplaybookを作成する。

```yaml
$ vi variable_sample_1.yml
---
- name: variable_sample_1
  hosts: localhost
  gather_facts: false
  
  vars:
    test: "Hello Ansible!"  # <-「test1」という変数に「Hello Ansible!」という文字列を定義

  tasks:
    - name: debug
      debug:
        var: test  # <-Varsセクションで定義した「test1」の変数の中身を出力
```

---

# 5-3. 変数の実習

以下は作成したplaybookを実行/出力例である。

<br>

```yaml
$ ansible-playbook -i inventory.ini variable_sample_1.yml 

PLAY [variable_sample_1] ****************************************************************************

TASK [debug] ***************************************************************************************
ok: [localhost] => {
    "test": "Hello Ansible!"  # <-想定通りの出力を確認
}

PLAY RECAP *****************************************************************************************
localhost : ok=1  changed=0  unreachable=0  failed=0  skipped=0  rescued=0  ignored=0
```

---

# 5-3. 変数の実習

## 2. `set_fact`を使用して変数定義を行い、変数の中身をdebugで出力する

以下のplaybookを作成する。

```yaml
$ vi variable_sample_2.yml
---
- name: variable_sample_2
  hosts: localhost
  gather_facts: false
  
  tasks:
    - name: variable definition
      set_fact:                   
        test: "Hello Ansible!"  # <-「set_fact」を使用して「test2」という変数に「Hello Ansible!」という文字列を定義

    - name: debug
      debug:
        var: test  # <-「set_fact」で定義した「test2」の変数の中身を出力
```

---

# 5-3. 変数の実習

以下は作成したplaybookを実行/出力例である。

<br>

```yaml
$ ansible-playbook -i ../inventory.ini variable_sample_2.yml 

PLAY [variable_sample_2] ********************************************************************************************************

TASK [variable definition] ********************************************************************************************
ok: [localhost]

TASK [debug] *******************************************************************************************
ok: [localhost] => {
    "test": "Hello Ansible!"  # <-想定通りの出力を確認
}

PLAY RECAP *********************************************************************************************
localhost : ok=2  changed=0  unreachable=0  failed=0  skipped=0  rescued=0  ignored=0
```

---

# 5-3. 変数の実習

## 3. マジック変数の中身をdebugで出力する

以下のplaybookを作成する。

```yaml
$ vi variable_sample_3.yml
---
- name: variable_sample_3
  hosts: localhost
  gather_facts: false
  
  tasks:
    - name: debug
      debug:
        var: ansible_play_name  # <-「ansible_play_name」はマジック変数のため、変数定義の必要なし
```

---

# 5-3. 変数の実習

以下は作成したplaybookを実行/出力例である。

<br>

```yaml
$ ansible-playbook -i ../inventory.ini variable_sample_3.yml 

PLAY [variable_sample_3] **************************************************************************************************

TASK [debug] *************************************************************************************************************
ok: [localhost] => {
    "ansible_play_name": "variable_sample_3"  # <-「ansible_play_name」に格納されたplaybookの名前が出力されていることを確認
}

PLAY RECAP ***************************************************************************************************************
localhost : ok=1  changed=0  unreachable=0  failed=0  skipped=0  rescued=0  ignored=0
```

---

# 5-3. 変数の実習

## 4. ファクト変数の中身の一部をdebugで出力する

以下のplaybookを作成する。

```yaml
---
- name: variable_sample_4-1
  hosts: vyos01 
                            # <-「gather_facts: no」を省略した場合は「ansible_facts」にファクト情報が格納される。
  tasks:
    - name: debug
      debug:
        var: ansible_facts  # <-「ansible_facts」の中身を出力
```

---

# 5-3. 変数の実習

以下は作成したplaybookを実行/出力例である。

```yaml
PLAY [variable_sample_4-1] ********************************************************************************************

TASK [Gathering Facts] *********************************************************************************************
[WARNING]: Ignoring timeout(10) for vyos_facts
[WARNING]: default value for `gather_subset` will be changed to `min` from `!config` v2.11 onwards
[WARNING]: Platform linux on host vyos01 is using the discovered Python interpreter at /usr/bin/python, but future installation of another Python interpreter could change this.
See https://docs.ansible.com/ansible/2.9/reference_appendices/interpreter_discovery.html for more information.
ok: [vyos01]

TASK [debug] *******************************************************************************************************
ok: [vyos01] => {
    "ansible_facts": {       # <-「ansible_facts」の中身全てを出力すると量が多すぎる
        "discovered_interpreter_python": "/usr/bin/python",
        "net_api": "cliconf",
        "net_commits": [

---------------------------------(snip)---------------------------------

        "net_hostname": "vyos01",
        "net_neighbors": {},
        "net_python_version": "2.7.18",
        "net_serialnum": null,
        "net_system": "vyos",
        "net_version": "VyOS 1.4-rolling-202108071508",
        "network_resources": {}
    }
}

PLAY RECAP **********************************************************************************************************
vyos01 : ok=2  changed=0  unreachable=0  failed=0  skipped=0  rescued=0  ignored=0
```

---

# 5-3. 変数の実習

`ansible_facts`の中身全てを出力すると膨大な量があったため、今回は`ansible_facts`に格納されているディクショナリの中の`net_hostname`の値(value)を取り出してみる。以下はそのplaybookである。

```yaml
---
- name: variable_sample_4-2
  hosts: vyos01 
                            # <-「gather_facts: no」を省略した場合は「ansible_facts」にファクト情報が格納される。
  tasks:
    - name: debug
      debug:
        var: ansible_facts.net_hostname  # <-「ansible_facts」の中身を出力
```

---

# 5-3. 変数の実習

以下は作成したplaybookを実行/出力例である。

<br>

```yaml
PLAY [variable_sample_4-2] *****************************************************************************************

TASK [Gathering Facts] ******************************************************************************************
[WARNING]: Ignoring timeout(10) for vyos_facts
[WARNING]: default value for `gather_subset` will be changed to `min` from `!config` v2.11 onwards
[WARNING]: Platform linux on host vyos01 is using the discovered Python interpreter at /usr/bin/python, but future
installation of another Python interpreter could change this.
See https://docs.ansible.com/ansible/2.9/reference_appendices/interpreter_discovery.html for more information.
ok: [vyos01]

TASK [debug] ****************************************************************************************************
ok: [vyos01] => {
    "ansible_facts.net_hostname": "vyos01"  # <-想定通りの出力になっていることを確認
}

PLAY RECAP ******************************************************************************************************
vyos01 : ok=2  changed=0  unreachable=0  failed=0  skipped=0  rescued=0  ignored=0 
```

---

# 5-4.  演習

**Q1. 以下のplaybookを実行した場合に、`TASK [debug]`にて「"Hello":【 】」の【 】として表示される文字列を
　　1つ選択してください。**

```yaml
---
- name: variable_exam_1
  hosts: localhost
  gather_facts: false
  
  tasks:
    - set_fact:
        HelIo: "Hello Ansible!"

    - debug:
        var: Hello
```

1. “VARIABLE IS NOT DEFINED!”
1. "Hello"
1. "Hello Ansible!"
1. "error"

---

# 5-4.  演習

**Q2. 以下のplaybookを実行した場合に、`TASK [debug]`にて「"ansible_play_name":【 】」の【 】として
　　表示される文字列を1つ選択してください。**

```yaml
---
- name: variable_exam_2
  hosts: localhost
  gather_facts: false
  
  tasks:
    - name: set_fact
      set_fact:
        ansible_play_name: "Hello Ansible!"

    - name: debug
      debug:
        var: ansible_play_name
```

1. Hello Ansible!
1. "Hello Ansible!"
1. "error"
1. "variable_exercise2"

---

# 5-4.  演習

**Q3. debugモジュールの`var`パラメータを使用し、playbook実行時に`TASK [debug]`にて「vyos01」と
　　表示させるplaybookを作成してください。**


---

# 5-4.  演習

**Q4. vyos01から`show vrrp`の結果を取得し、registerモジュールを使用して取得結果を出力させる
　　playbookを作成してください。**


---

# 5-4.  演習

**A1. 以下のplaybookを実行した場合に、`TASK [debug]`にて「"Hello":【 】」の【 】として表示される文字列を
　　1つ選択してください**

```yaml
- name: variable_exam_1
  hosts: localhost
  gather_facts: false
  
  tasks:
    - name: set_fact
    set_fact:
        HelIo: "Hello Ansible!"

    - name: debug
      debug:
        var: Hello
```

1. **“VARIABLE IS NOT DEFINED!”**
1. ~~"Hello"~~
1. ~~"Hello Ansible!"~~
1. ~~"error"~~

---

# 5-4.  演習

実際の出力結果を以下に記載。

<br>

```yaml
TASK [set_fact] *******************************************************************************
ok: [localhost]

TASK [debug] **********************************************************************************
ok: [localhost] => {
    "Hello": "VARIABLE IS NOT DEFINED!"
}

PLAY RECAP ************************************************************************************
localhost : ok=2  changed=0  unreachable=0  failed=0  skipped=0  rescued=0  ignored=0
```

---

# 5-4.  演習

よく変数部分をみてみると`set_fact`で定義している変数名が「Hello」ではなく、「HelIo」になっていることが分かる。変数名が間違えていたため、「Hello」は定義されていません(VARIABLE IS NOT DEFINED!)と表示されている。

<br>

```yaml
- name: variable_exam_1
  hosts: localhost
  gather_facts: false
  
  tasks:
    - set_fact:
        HelIo: "Hello Ansible!"  # <-「Hello」ではなく、「HelIo」になっている。

    - debug:
        var: Hello
```

---

# 5-4.  演習

**A2. 以下のplaybookを実行した場合に、`TASK [debug]`にて「"ansible_play_name":【 】」の【 】として
　　表示される文字列を1つ選択してください。**

```yaml
---
- name: variable_exam_2
  hosts: localhost
  gather_facts: no
  
  tasks:
    - name: set_fact
      set_fact:
        ansible_play_name: "Hello Ansible!"

    - name: debug
      debug:
        var: ansible_play_name
```

1. ~~Hello Ansible!~~
1. ~~"Hello Ansible!"~~
1. ~~"error"~~
1. **"variable_exercise2"**

---

# 5-4.  演習

実際の出力結果を以下に記載。

<br>

```yaml
PLAY [variable_exam_2] ******************************************************************************

TASK [set_fact] ****************************************************************************************
ok: [localhost]

TASK [debug] *******************************************************************************************
ok: [localhost] => {
    "ansible_play_name": "variable_exercise2"
}

PLAY RECAP *********************************************************************************************
localhost : ok=2  changed=0  unreachable=0  failed=0  skipped=0  rescued=0  ignored=0 
```

---

# 5-4.  演習

`set_fact`で定義した`ansible_play_name`という変数はマジック変数であり、現在実行されているplaybookの名前を変数の中に格納する。今回`set_fact`で「Hello Ansible!」という文字列を定義したが、それよりもマジック変数が優先されるため、出力結果は`variable_exercise2`となる。

<br>

```yaml
---
- name: variable_exam_2  # <-ここに記載されているplaybook名が「ansible_play_name」というマジック変数に格納される
  hosts: localhost
  gather_facts: false
  
  tasks:
    - set_fact:
        ansible_play_name: "Hello Ansible!"

    - debug:
        var: ansible_play_name
```

---

# 5-4.  演習

**A3. debugモジュールの`var`パラメータを使用し、playbook実行時に`TASK [debug]`にて「vyos01」と
　　表示させるplaybookを作成してください。**

以下に例として要件を満たすplaybookを記載。

```yaml
---
- name: variable_exam_3
  hosts: localhost
  gather_facts: false
  
  tasks:
    - name: set_fact
      set_fact:
        test_hostname: "vyos01"  # <-「set_fact」を使用して「test」という変数に「vyos01」という文字列を定義

    - name: debug
      debug:
        var: test_hostname  # <-「set_fact」で定義した「test」の変数の中身を出力
```

---

# 5-4.  演習

実際の出力結果を以下に記載。

<br>

```yaml
PLAY [variable_exam_3] ****************************************************************************************************

TASK [set_fact] ************************************************************************************
ok: [localhost]

TASK [debug] ***************************************************************************************
ok: [localhost] => {
    "test_hostname": "vyos01"
}

PLAY RECAP *****************************************************************************************
localhost : ok=2  changed=0  unreachable=0  failed=0  skipped=0  rescued=0  ignored=0 
```

---

# 5-4.  演習

**A4. vyos01から`show vrrp`の結果を取得し、registerモジュールを使用して取得結果を出力させる
　　playbookを作成してください。**

以下に例として要件を満たすplaybookを記載。

```yaml
---
- name: variable_exam_4
  hosts: vyos01
  gather_facts: false
  
  tasks:
    - name: get show vrrp
      vyos_command:
        commands: 'show vrrp'
      register: result

    - name: debug result
      debug:
        msg: "{{ result.stdout_lines[0] }}"
```

---

# 5-4.  演習

実際の出力結果を以下に記載。

```yaml

PLAY [variable_exam_4] **************************************************************************

TASK [get show vrrp] *******************************************************************************
ok: [vyos01]

TASK [debug result] ********************************************************************************
ok: [vyos01] => {
    "msg": [
        "Name          Interface      VRID  State      Priority  Last Transition",
        "------------  -----------  ------  -------  ----------  -----------------",
        "service_nw01  eth1             10  MASTER          150  1h20m44s",
        "service_nw02  eth2             20  MASTER          150  1h20m44s"
    ]
}

PLAY RECAP *****************************************************************************************
vyos01  : ok=2  changed=0  unreachable=0  failed=0  skipped=0  rescued=0  ignored=0   

```

---
