# docker-dev-doc
Document for Docker dev

## Requirement
- Windows 10

## Install

### Install Windows Terminal
![](https://img.shields.io/badge/-Windows-blue)

1. [Windows Terminal - Microsoft Store](https://aka.ms/terminal)からWindowsターミナルをインストール

### Install WSL2 & Ubuntu 20.04 LTS
- [参考：公式手順](https://learn.microsoft.com/ja-jp/windows/wsl/install) 

#### WSLとUbuntuをインストール

##### WSLを未インストールの場合

![](https://img.shields.io/badge/-Powershell-blue)

1. WSLとUbuntuをインストール

```PowerShell
wsl --install -d Ubuntu-20.04
```

##### WSLをインストール済の場合

![](https://img.shields.io/badge/-Windows-blue)

1. [Ubuntu 20.04 - Microsoft Store](https://apps.microsoft.com/store/detail/ubuntu-2004/9N6SVWS3RX71?hl=ja-jp&gl=jp)からUbuntuをインストール

#### Ubuntu初期設定
![](https://img.shields.io/badge/-Windows-blue)

1. WindowsターミナルからUbuntuを起動

![](https://img.shields.io/badge/-WSL(Ubuntu)-orange)

2. Linuxユーザー名とパスワードを設定

### Install Docker 
- [参考：公式手順](https://docs.docker.com/engine/install/ubuntu/#install-using-the-repository)
- `Set up the repository`の`1.`の手順については対応不要のため省略している
  - `1.`の手順内の`apt`パッケージは`Ubuntu-20.04 LTS`ではすべて初期状態でインストール済であるため

#### Dockerパッケージの復号化用公開鍵を追加
![](https://img.shields.io/badge/-WSL(Ubuntu)-orange)

1. `/etc/apt`配下に公開鍵の格納用フォルダを作成

```bash
sudo mkdir -m 0755 -p /etc/apt/keyrings
```

2. Dockerパッケージの復号化用公開鍵を取得し、バイナリ化してから`docker.gpg`として保存

```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```

#### リポジトリの設定
![](https://img.shields.io/badge/-WSL(Ubuntu)-orange)

1. Dockerパッケージ用のリポジトリの設定を`docker.list`として保存

```bash
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

#### Docker用`apt`パッケージの取得
![](https://img.shields.io/badge/-WSL(Ubuntu)-orange)

1. `apt`のパッケージリストを最新化する

```bash
sudo apt-get update
```

2. Docker用`apt`パッケージの取得

```bash
sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

#### `sudo`なしで`docker`コマンドを使えるようにする
![](https://img.shields.io/badge/-WSL(Ubuntu)-orange)

1. Ubuntuのログインユーザーをユーザーグループ`docker`に追加

```bash
sudo gpasswd -a (Ubuntuのユーザー名) docker
```

#### Docker起動
![](https://img.shields.io/badge/-WSL(Ubuntu)-orange)

1. Dockerデーモンを起動

```bash
sudo service docker start
```

#### インストール確認
![](https://img.shields.io/badge/-WSL(Ubuntu)-orange)

1. `docker`コマンド実行確認
    - エラーが出なければOK

```bash
docker -v
```

2. `docker compose`コマンド実行確認
    - エラーが出なければOK

```bash
docker compose version
```

#### 参考：WSL(Ubuntu)が手に負えない状態になったときのやり直し方
![](https://img.shields.io/badge/-Windows-blue)

1. スタート → 設定 → アプリ → Ubuntu 20.04
2. 詳細オプション → 「リセット」ボタン

##### 補足
- 上記手順後、Ubuntuの初回起動画面で以下のエラーになることがある
  - `Error code: Wsl/Service/CreateInstance/MountVhd/ERROR_FILE_NOT_FOUND`
- その際は、以下を実施してからUbuntuを再起動する

![](https://img.shields.io/badge/-Powershell-blue)

1. WSLからUbuntuの登録を解除
    - WSLに登録中のLinuxディストリビューションは、`wsl -l`で確認できる

```bash
wsl --unregister Ubuntu-20.04
```

### Install code

#### ソースフォルダ配置
![](https://img.shields.io/badge/-Windows-blue)

1. エクスプローラーから`\\wsl$\Ubuntu-20.04\home\(Linuxユーザー名)`にソースコードを配置
    - `\\wsl$`配下以外の場所でコンテナを生成する場合、生成処理に大きく時間がかかる
      - WSLとWindows間でファイルの同期処理が走るため

#### Git Config変更
![](https://img.shields.io/badge/-Powershell-blue)

1. 配置したソースフォルダのパスをGit Configのセーフディレクトリとして追加
    - Git操作時に以下のエラーが出ることへの対処
      - `fatal: detected dubious ownership in repository at '//wsl.localhost/Ubuntu-20.04/home/(Linuxユーザー名)/(ソースフォルダ名)'`

```Powershell
git config --global --add safe.directory `%(prefix)///wsl.localhost/Ubuntu-20.04/home/(Linuxユーザー名)/(ソースフォルダ名)`
```

## Usage

### Routine
![](https://img.shields.io/badge/-Windows-blue)

1. WindowsターミナルでWSL(Ubuntu)を起動

![](https://img.shields.io/badge/-WSL(Ubuntu)-orange)

2. Dockerデーモンを起動

```bash
sudo service docker start
```

3. Dockerコンテナ作成

```bash
docker compose up -d
```

4. Dockerコンテナで作業実施

5. Dockerコンテナ破棄

```bash
docker compose down
```

### Change Dockerfile
![](https://img.shields.io/badge/-Windows-blue)

1. Dockerfileを編集して保存

![](https://img.shields.io/badge/-WSL(Ubuntu)-orange)

2. Dockerコンテナ破棄

```bash
docker compose down
```

3. 一からDockerコンテナ再生成

```bash
docker compose up -d --build
```
