# inventoryファイルとplaybookの基本的な構成と中身

## inventoryファイルの中身
```yaml
cat inventory.ini
[vyos]                                                   #グループ名vyos
vyos01 ansible_host=10.0.0.2                             #ホスト変数、ホスト変数名vyos01のIPは10.0.0.2 
vyos02 ansible_host=10.0.0.3                             #ホスト変数、ホスト変数名vyos02のIPは10.0.0.3

[host]
host01 ansible_host=10.0.0.4
host02 ansible_host=10.0.0.5

[vyos:vars]                                              #グループ名vyosのグループ変数
ansible_network_os=vyos                                  #AnsibleがどのネットワークOSを対象とするか認識するための指定
ansible_connection=network_cli                           #接続方式の指定。Ansible が内部でSSH でログインして CLI 操作するタイプ=network_cli
ansible_user=vyos                                        #vyosにログインするユーザー名　
ansible_password=vyos                                    #vyosにログインするユーザー名

[host:vars]
ansible_user=root
ansible_password=test_password

```
<br>
<br>
<br>
<br>
<br>


## playbookの中身
- playbookの中身はこのような構成が基本となります。詳細は、今後習っていきます。(以下は基本だけであり、以下以外にも様々な記述や機能があります)

```yaml
---                                           #YAMLファイルであることを宣言
- name: playbook beginner                     #play名を指定
  hosts: vyos                                 #必須項目。playbook実行対象ノードを指定
  become: true                                #管理者権限の指定。デフォルト=false
  gather_facts: false　　　　　　　　　　　　　 #実行対象ノードのシステム情報を取得。デフォルト=trueだが、基本的にfalse。

  vars:                                       #変数を指定
    vars_test: 1000                           #変数vars_testに1000を代入 (文字列や値なども可)

  tasks:                                      #実行させたい処理内容を記載
    - name: task name1                        #task名を指定
      vyos_command:                           #使用するモジュールを指定
        commands:                             #上記で使用するモジュールのパラメータを指定
          - show interfaces                   

    - name: task name2                                               
      vyos_config:                            
        lines:                                
          - set system XX                     
```

## playbookを実行するとき
- playbookを実行する際は仮想環境(venv)で実行する必要があるので、以下を実行しておく。
```yaml
[ec2-user@ip-172-31-42-108 03_vyos]$ source /home/ec2-user/venv/bin/activate
(venv)[ec2-user@ip-172-31-42-108 03_vyos]$
```

- playbookを実行する際は以下コマンドを実行する
```yaml
ansible-playbook -i <インベントリファイル> <playbook名>.yml

使用例
(venv)[ec2-user@ip-172-31-42-108 03_vyos]$ ansible-playbook -i inventory.ini test.yml
```
