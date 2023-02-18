# docker-dev-doc
Document for Docker dev

## Requirement
- Windows 10

## Install

### Install Windows Terminal
- [Windows Terminal - Microsoft Store](https://aka.ms/terminal)からインストール

### Install WSL2 & Ubuntu 20.04 LTS
- [参考：公式手順](https://learn.microsoft.com/ja-jp/windows/wsl/install) 
- Powershell
  - `wsl --install -d Ubuntu-20.04`
- WSL(Ubuntu)
  - Linuxユーザー名とパスワードを設定

### Install Docker 
- [参考：公式手順](https://docs.docker.com/engine/install/ubuntu/#install-using-the-repository)
- `Set up the repository`の`1.`の手順については対応不要のため省略している
  - `1.`の手順内の`apt`パッケージは`Ubuntu-20.04 LTS`ではすべて初期状態でインストール済であるため

#### Dockerパッケージの復号化用公開鍵を追加
- WSL(Ubuntu)
  1. `sudo mkdir -m 0755 -p /etc/apt/keyrings`
      - `/etc/apt`配下に公開鍵の格納用フォルダを作成
  2. `curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg`
      - `curl`コマンドでDockerパッケージの復号化用公開鍵を取得
      - `gpg`コマンドでバイナリ化してから`docker.gpg`として保存

#### リポジトリの設定
- WSL(Ubuntu)
  1. `echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null`
      - Dockerパッケージ用のリポジトリの設定を`docker.list`として保存

#### Docker用`apt`パッケージの取得
- WSL(Ubuntu)
  1. `sudo apt-get update`
      - `apt`のパッケージリストを最新化する
  2. `sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin`
      - Docker用`apt`パッケージの取得

#### `sudo`なしで`docker`コマンドを使えるようにする
- WSL(Ubuntu)
  1. `sudo gpasswd -a (Ubuntuのユーザー名) docker`

#### Docker起動
- WSL(Ubuntu)
  1. `sudo service docker start`

#### インストール確認
- WSL(Ubuntu)
  1. `docker -v`
      - エラーが出なければOK
  2. `docker compose version`
      - エラーが出なければOK

#### 参考：やり直し方
- WSL(Ubuntu)が手に負えない状態になった場合は、以下の方法でやり直す
- Windows
  - スタート → 設定 → アプリ → Ubuntu 20.04
  - 詳細オプション → 「リセット」ボタン

### Install code
- Windows
  - エクスプローラーから`\\wsl$\Ubuntu-20.04\home\(Linuxユーザー名)`にソースコードを配置
    - `\\wsl$`配下以外の場所でコンテナを生成する場合、生成処理に大きく時間がかかる
      - WSLとWindows間でファイルの同期処理が走るため
- Powershell
  - git config --global --add safe.directory `%(prefix)///wsl.localhost/Ubuntu-20.04/home/(Linuxユーザー名)/(ソースフォルダ名)`
    - ↑はGit操作時に以下のエラーが出ることへの対処
      - `fatal: detected dubious ownership in repository at '//wsl.localhost/Ubuntu-20.04/home/(Linuxユーザー名)/(ソースフォルダ名)'`

## Usage

### Routine
- WindowsターミナルでWSL(Ubuntu)を起動
- WSL(Ubuntu)
  - `sudo service docker start`でDockerを起動
  - `docker compose up -d`でコンテナ生成
  - `docker compose down`でコンテナ破棄  

### Change Dockerfile
- Dockerfileを編集して保存
- WSL(Ubuntu)
  - `sudo service docker start`でDockerを起動
  - `docker compose up -d --build`で一からコンテナ生成
