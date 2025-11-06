`nmcli`（NetworkManager command-line interface）は、Linuxシステムでネットワーク接続を管理する「NetworkManager」サービスを、コマンドラインから操作するための標準的なツールです。

サーバー環境（CUI）でのネットワーク設定や、スクリプトによる自動化に不可欠です。

-----

## 基本的な構文

`nmcli`のコマンドは、操作対象の「オブジェクト」と、実行する「アクション」を組み合わせて使用するのが基本です。

```bash
nmcli [オプション] {オブジェクト} {アクション} [引数...]
```

主なオブジェクトには以下の4つがあります。

  - **`general`**: NetworkManager全体の動作状態や権限
  - **`device`**: ネットワークインターフェース（物理的なNICやWi-Fiアダプタ）
  - **`connection`**: ネットワーク設定のプロファイル（IPアドレス、DNS、ゲートウェイ設定の集まり）
  - **`radio`**: Wi-FiやWWAN（携帯電話網）のオン/オフスイッチ

-----

## `nmcli` コマンド一覧（主要なもの）

以下に、オブジェクトごとに主要なコマンドと使用例を示します。

### 1\. `general` (全体の状態管理)

NetworkManagerサービス自体の状態を確認します。

  - **`nmcli general status`**

      - **説明:** 現在のネットワークの全体的な状態（接続済みか、Wi-Fiは有効かなど）を表示します。
      - **使用例:**
        ```bash
        $ nmcli general status
        STATE      CONNECTIVITY  WIFI-HW  WIFI     WWAN-HW  WWAN
        connected  full          enabled  enabled  enabled  enabled
        ```

  - **`nmcli general hostname`**

      - **説明:** システムのホスト名を表示または変更します。
      - **使用例:**
        ```bash
        # ホスト名を 'my-server' に変更する
        sudo nmcli general hostname my-server
        ```

### 2\. `device` (デバイスの管理)

物理的または仮想的なネットワークインターフェース（NIC）を直接操作します。

  - **`nmcli device status`**

      - **説明:** システムが認識しているすべてのネットワークデバイス（`enp0s3`, `wlan0`, `lo`など）の状態一覧を表示します。
      - **使用例:**
        ```bash
        $ nmcli device status
        DEVICE  TYPE      STATE      CONNECTION
        enp0s3  ethernet  connected  Wired connection 1
        enp0s8  ethernet  connected  enp0s8
        lo      loopback  unmanaged  --
        ```

  - **`nmcli device show [デバイス名]`**

      - **説明:** 特定のデバイス（例: `enp0s3`）の詳細情報（IPアドレス、MACアドレス、DNSなど）を表示します。
      - **使用例:**
        ```bash
        $ nmcli device show enp0s3
        ```

  - **`nmcli device connect <デバイス名>`**

      - **説明:** 指定したデバイスを使用してネットワークに接続します。（DHCPによるIP取得など）
      - **使用例:**
        ```bash
        sudo nmcli device connect enp0s8
        ```

  - **`nmcli device disconnect <デバイス名>`**

      - **説明:** 指定したデバイスをネットワークから切断します。
      - **使用例:**
        ```bash
        sudo nmcli device disconnect enp0s8
        ```

  - **`nmcli device wifi list`**

      - **説明:** (Wi-Fiが有効な場合) 周囲の利用可能なWi-Fiアクセスポイントをスキャンして一覧表示します。

### 3\. `connection` (接続プロファイルの管理)

「接続プロファイル」（設定の集まり）を管理します。デバイス（`device`）は物理的な「差し込み口」、接続（`connection`）は「その差し込み口に適用するIPアドレスやDNSの設定書」に例えられます。

  - **`nmcli connection show`** (または `nmcli con show`)

      - **説明:** 保存されているすべての接続プロファイルの一覧を表示します。
      - **使用例:**
        ```bash
        $ nmcli con show
        NAME                UUID                                  TYPE      DEVICE
        Wired connection 1  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  ethernet  enp0s3
        enp0s8              xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  ethernet  enp0s8
        ```

  - **`nmcli connection up <プロファイル名>`**

      - **説明:** 指定した接続プロファイルを有効化（アクティベート）します。
      - **使用例:**
        ```bash
        sudo nmcli con up "Wired connection 1"
        ```

  - **`nmcli connection down <プロファイル名>`**

      - **説明:** 指定した接続プロファイルを無効化（ディアクティベート）します。
      - **使用例:**
        ```bash
        sudo nmcli con down enp0s8
        ```

  - **`nmcli connection add ...`**

      - **説明:** 新しい接続プロファイルを作成します。（固定IPの設定など）
      - **使用例 (固定IP `192.168.1.50` を設定):**
        ```bash
        sudo nmcli connection add con-name "static-ip" ifname enp0s3 type ethernet \
          ip4 192.168.1.50/24 gw4 192.168.1.1

        # DNSサーバーを設定
        sudo nmcli connection modify "static-ip" ipv4.dns "8.8.8.8,1.1.1.1"

        # 設定を反映
        sudo nmcli connection up "static-ip"
        ```

  - **`nmcli connection modify <プロファイル名> ...`**

      - **説明:** 既存の接続プロファイルの設定を変更します。（例: DHCPに戻す）
      - **使用例 (DHCP自動取得に戻す):**
        ```bash
        sudo nmcli connection modify "static-ip" ipv4.method auto
        ```

  - **`nmcli connection delete <プロファイル名>`**

      - **説明:** 不要になった接続プロファイルを削除します。
      - **使用例:**
        ```bash
        sudo nmcli connection delete "static-ip"
        ```

  - **`nmcli connection reload`**

      - **説明:** ディスク上の設定ファイル（`/etc/sysconfig/network-scripts/`など）を直接編集した後、NetworkManagerに設定を再読み込みさせます。

### 4\. `radio` (無線スイッチの管理)

  - **`nmcli radio all`**

      - **説明:** すべての無線デバイス（Wi-Fi, WWAN）のハードウェアスイッチの状態を表示します。

  - **`nmcli radio wifi off`**

      - **説明:** Wi-Fiのソフトウェアスイッチをオフにします。（機内モードのような状態）
      - **使用例:**
        ```bash
        sudo nmcli radio wifi off
        ```

  - **`nmcli radio wifi on`**

      - **説明:** Wi-Fiのソフトウェアスイッチをオンにします。