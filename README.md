# Jupyter notebookをリモートサーバにインストールするためのansible playbook
## インストール方法
1. ansibleをインストール
2. 実行  
  ```
  ansible-playbook -i <host1>,<host2>,(...), jupyter_ansible.yml
  ```
3. アカウントユーザー名，ポート番号，jupyterパスワードを入力する．
