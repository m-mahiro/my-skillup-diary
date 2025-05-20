## WpaSupplicant と Netplan違い
**1. `wpa_supplicant`の設定ファイルを使用する方法**

`wpa_supplicant`は、Wi-Fi接続を管理するための低レベルのツールであり、直接Wi-Fiネットワークへの接続を制御します。`/etc/wpa_supplicant/wpa_supplicant.conf`ファイルに接続情報を記述し、`wpa_supplicant`を手動で起動してWi-Fi接続を確立します。この方法は、詳細なWi-Fi設定や複数のWi-Fiネットワークの管理が可能で、特定の接続要件がある場合に適しています。

**2. Netplanの設定ファイルを使用する方法**

Netplanは、Ubuntu 17.10以降で導入されたネットワーク設定の統一的なフレームワークで、YAML形式の設定ファイルを使用してネットワーク構成を記述します。`/etc/netplan/99-custom-config.yaml`などのファイルにWi-Fi接続の情報を記述し、`netplan apply`コマンドで設定を適用します。Netplanは、`wpa_supplicant`や`networkd`などのバックエンドを使用して実際の接続を管理しますが、ユーザーはNetplanの設定ファイルを編集するだけでネットワーク設定を行うことができます。この方法は、システム全体のネットワーク設定を一元管理しやすく、保守性が高いのが特徴です。

**まとめ**

`wpa_supplicant`の設定ファイルを直接使用する方法は、詳細なWi-Fi設定や特定の要件がある場合に適しています。一方、Netplanの設定ファイルを使用する方法は、システム全体のネットワーク設定を統一的に管理でき、保守性が高いという利点があります。Ubuntuのバージョンやシステムの要件に応じて、適切な方法を選択することが重要です。 


Ubuntu ServerでProtected Extensible Authentication Protocol（PEAP）認証を使用してWi-Fiに接続する際、`/etc/netplan/50-cloud-init.yaml`ファイルを直接編集することは推奨されていません。これは、`cloud-init`が再実行された場合、このファイルが上書きされ、設定が失われる可能性があるためです。 citeturn0search1

**`/etc/netplan/50-cloud-init.yaml`ファイルについて**

`/etc/netplan/50-cloud-init.yaml`は、`cloud-init`によって自動生成されるNetplanの設定ファイルです。`cloud-init`は、クラウド環境向けの初期設定フレームワークで、ネットワーク設定やホスト名の設定などを自動的に行います。このファイルは、システムの初回起動時に生成され、再度`cloud-init`が実行されると上書きされる可能性があります。 citeturn0search1

**推奨される設定方法**

Netplanの設定を行う際は、`/etc/netplan/`ディレクトリ内に新しいYAMLファイルを作成し、既存の設定ファイルよりもアルファベット順で後に読み込まれるようにします。例えば、`99-custom-config.yaml`という名前でファイルを作成します。 citeturn0search1

**Wi-Fi接続の設定手順**

1. **必要なパッケージのインストール**

   `wpa_supplicant`（Wi-Fi Protected Access Supplicant）をインストールします。

   ```bash
   sudo apt update
   sudo apt install wpasupplicant
   ```

2. **Wi-Fiインターフェースの確認**

   `ip link show`コマンドを実行し、Wi-Fiインターフェースの名前を確認します。通常、`wlan0`や`wlp*`などの名前が付けられています。

   ```bash
   ip link show
   ```

3. **Netplan設定ファイルの作成**

   `/etc/netplan/99-custom-config.yaml`という名前で新しい設定ファイルを作成し、以下の内容を記述します。`wifiname`、`your_username`、`your_password`、および`your_interface_name`は実際の値に置き換えてください。

	`renderer`は省略可

   ```yaml
   network:
     version: 2
     renderer: networkd
     wifis:
       your_interface_name:
         access-points:
           "wifiname":
             auth:
               key-management: 802.1X
               eap: peap
               identity: "your_username"
               password: "your_password"
               method: mschapv2
         dhcp4: true
   ```

   この設定では、`networkd`をレンダラーとして使用し、指定したWi-FiネットワークにPEAP認証で接続します。

4. **設定の適用**

   以下のコマンドでNetplanの設定を適用します。

   ```bash
   sudo netplan apply
   ```

5. **接続の確認**

   `ip addr`コマンドでIPアドレスが割り当てられていることを確認し、`ping`コマンドでインターネット接続をテストします。

   ```bash
   ip addr show your_interface_name
   ping -c 4 google.com
   ```


これらの手順により、Ubuntu ServerでPEAP認証を使用したWi-Fi接続が可能となります。セキュリティ向上のため、サーバー証明書の検証を行う設定を追加することも検討してください。 



Wi-Fi接続の設定を行う際、接続するWi-Fiネットワークによって変更が必要な部分は以下のとおりです。

1. **`your_interface_name`**: Wi-Fiインターフェースの名前を指定します。これは、`ip link show`コマンドなどで確認できます。

2. **`wifiname`**: 接続先のWi-FiネットワークのSSID（Service Set Identifier）を指定します。SSIDは、Wi-Fiネットワークの名前として表示されるものです。

3. **`your_username`**: Wi-Fiネットワークへの接続に必要なユーザー名を指定します。これは、企業や教育機関などで使用される認証方式で必要となる場合があります。

4. **`your_password`**: Wi-Fiネットワークへの接続に必要なパスワードを指定します。これは、ネットワークのセキュリティ設定により異なります。

5. **`key-management`**: Wi-Fiネットワークのキー管理方式を指定します。例えば、WPA2-Personalの場合は`wpa-psk`、WPA2-Enterpriseの場合は`802.1X`を指定します。

6. **`eap`**: Extensible Authentication Protocol（EAP）の種類を指定します。例えば、PEAP（Protected EAP）やTLS（Transport Layer Security）などがあります。

7. **`method`**: EAPの内部で使用する認証方式を指定します。例えば、MSCHAPv2やGTC（Generic Token Card）などがあります。

8. **`dhcp4`**: IPv4のDHCP（Dynamic Host Configuration Protocol）を使用するかどうかを指定します。`true`に設定すると、DHCPでIPアドレスが自動的に割り当てられます。

これらのパラメータは、接続するWi-Fiネットワークのセキュリティ設定や認証方式に応じて適切に設定する必要があります。詳細な設定方法や各パラメータの説明については、以下の参考資料をご参照ください。
