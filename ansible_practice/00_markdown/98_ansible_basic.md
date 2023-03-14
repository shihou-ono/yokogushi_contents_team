# inventoryファイルとplaybookの基本的な構成と中身
```yaml
[vyos]
vyos01 ansible_host=10.0.0.2
vyos02 ansible_host=10.0.0.3

[host]
host01 
host02 

[vyos:vars]
ansible_network_os=vyos
ansible_connection=network_cli
ansible_user=vyos
ansible_password=vyos

[host:vars]


```





# inventoryファイルの中身

# playbookの中身
- playbookの中身はこのような構成が基本となります。詳細は、今後習っていきます。(以下は基本だけであり、以下以外にも様々な記述や機能があります)

```yaml
---                                           #YAMLファイルであることを宣言
- name: playbook beginner                     
  hosts: vyos                                 #playbook実行対象ノードを指定、必ず必要な記載
  become: true                                #管理者権限の指定。デフォルト=false
  gather_facts: false　　　　　　　　　　　　　 #実行対象ノードのシステム情報を取得。デフォルト=trueだが、基本的にfalse。

  vars:                                       #変数を指定
    vars_test: 1000                           #変数vars_testに1000を代入 (文字列や値なども可)

  tasks:                                      #実行させたい処理内容を記載
    - name: task name1                        #タスクの名前を指定
      vyos_command:                           #使用するモジュールを指定
        commands:                             #上記で使用するモジュールのパラメータを指定
          - show interfaces                   

    - name: task name2                        #ここからまた違う内容のタスク                       
      vyos_config:                            #ここではvyos_configモジュールを指定
        lines:                                #ここではvyos_configモジュールのlinesパラメータを指定
          - set system XX                     
```
