# Jupyter NotebookをリモートサーバにインストールするためのAnsible Playbook

## Jupyter Notebookとは
* Pythonコードをインタラクティブに実行できるツールです
* データの可視化も何かと便利です

## Ansibleとは
* 構成管理ツールです．自動で環境を構築できます．
* SSH接続を行って,リモートマシンも含めた環境構築ができます.

## インストール方法
### 0. リモートへのSSH接続の設定
.ssh/config, 公開鍵認証の設定を行っておきましょう．
### 1. **ローカル**へのAnsibleをインストール
  * （例）Ubuntuの場合
    ```
    sudo apt-get install software-properties-common
    sudo apt-add-repository ppa:ansible/ansible
    sudo apt-get update
    sudo apt-get install ansible
    ```
### 2. Ansible Playbookのダウンロード
  ```
  git clone https://github.com/elecho1/jupyter_notebook_ansible.git
  ```

### 3. Ansible Playbookの実行  
  ```
  $ cd jupyter_notebook_ansible
  $ ansible-playbook -i <インストールしたいホスト名 or ip>, jupyter_ansible.yml
  your remote account name: ＜サーバーのアカウント名＞
  Remote port for Jupyter:  ＜Jupyter用に開けるリモートのポート番号（適当に決めてください）＞
  Password for Jupyter:     ＜Jupyterのパスワード＞
  Confirm Password for Jupyter: ＜Jupyterのパスワードの再入力＞ 
  ```

## リモートのJupyter Notebookへの接続方法
### 1. **リモートサーバ**でJupyter Notebookを起動
```
cd ＜コードを動かしたいディレクトリ＞
jupyter notebook
```
### 2. **ローカル**のブラウザでJupyter Notebookにアクセス
#### ローカルネットワーク内の場合
  ブラウザで`http://（サーバのIP）:（Jupyter用に開けたポート番号）/tree`にアクセス
#### ローカルネットワーク外の場合
#####  1. SSHポートフォワーディングの開始  
  ```
  ssh -L （ローカルで開けるポート番号）:（リモートホスト名 or IP）:（リモートでJupyter用に開けたポート番号） （リモートホスト名 or IP）
  ```

#####  2. ブラウザでアクセス
  ```
  http://localhost:（ローカルで開けたポート番号）/tree
  ```


## Ansible Playbook内で行っている操作
作業は全て**リモート**で行います.
### 1. Jupyter Notebookのインストール
```
pip3 install --user jupyter
```
### 2. jupyter-contrib-nbextensions（ちょっと便利なツール群）のインストール
```
pip3 install --user jupyter-contrib-nbextensions
```
### 3. jupyter-contrib-nbextensionsの有効化
```
jupyter contrib nbextension install --user
```
### 4. jupyter_nbextensions_configuratorのインストール
```
pip3 install --user jupyter_nbextensions_configurator
```
### 5. jupyter_nbextensions_configuratorの有効化
```
jupyter nbextensions_configurator enable --user
```
### 6. IPythonのインストール
```
pip3 install --user IPython
```
### 7. Jupyterのデフォルト設定ファイル(~/.jupyter/jupyter_notebook_config.py)の生成
```
jupyter notebook --generate-config
```
### 8. Jupyter用パスワードのSHA1鍵の生成
```
python3 -c "from IPython.lib import passwd
print(passwd('＜設定したいパスワード＞'))" 
```
（改行をそのまま残してターミナルで実行してください）
### 9. Jupyterの設定の変更
2つ上の手順で生成したJupyter設定ファイル(~/.jupyter/jupyter_notebook_config.py)を編集します．

```
# 該当する箇所のコメントアウトを外して編集
c.NotebookApp.ip = '*'
c.NotebookApp.open_browser = False
c.NotebookApp.port = （Jupyter用に開けたいリモートのポート番号）
c.NotebookApp.password = u'（上の手順で生成されたSHA1鍵）'
```
