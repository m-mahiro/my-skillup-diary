
参考文献というわけではないが、見とくと良いかもしれない。
https://qiita.com/spc_tokuda/items/e1fd51a57c867795354e

ちなみにこのーページはChatGPTからコピペしただけのメモです。


## 必要なパッケージ等

Raspberry PiにRC-S300（パソリ）を接続してICカードを読み取るための手順は以下の通りです。

### 1. **RC-S300（パソリ）の接続**
RC-S300をRaspberry Piに接続するためには、USBポートに直接接続するだけです。

### 2. **必要なパッケージのインストール**
次に、RC-S300をRaspberry Piで動作させるために、必要なライブラリやツールをインストールします。

#### a. **依存パッケージのインストール**
まず、システムを更新し、必要なパッケージをインストールします。

```bash
sudo apt update
sudo apt upgrade -y
sudo apt install libpcsclite-dev pcsc-tools build-essential git
```

- `libpcsclite-dev`: PC/SCライブラリの開発ヘッダ
- `pcsc-tools`: ICカードリーダーをテストするためのツール
- `build-essential`: コンパイルに必要な基本ツール
- `git`: ソースコードの管理に使用する

#### b. **pcsc-liteのインストール**
PC/SCは、スマートカードリーダーと通信するための標準的なインターフェースです。RC-S300もこれをサポートしています。

```bash
sudo apt install pcscd
```

#### c. **pcsc-toolsのテスト**
`pcsc-tools`が正常に動作するかを確認するため、`pcsc_scan`コマンドを実行して、RC-S300が認識されているかを確認します。

```bash
pcsc_scan
```

このコマンドを実行すると、カードリーダーが正しく認識されている場合、以下のような情報が表示されます：

```
waiting for card...
```

ICカードをリーダーにかざすと、そのカードの情報が表示されるはずです。

### 3. **RC-S300を使ってプログラムを作成**
RC-S300を使ってICカードを読み取るための基本的なコードを作成するには、以下の手順を参考にできます。

#### a. **ライブラリのインストール (Pythonの場合)**
もしPythonを使ってICカードを読み取る場合は、`pyscard`というライブラリを使うことができます。

```bash
pip install pyscard
```

#### b. **サンプルコード**
以下は、Pythonを使ってRC-S300でカードを読み取るサンプルコードです。

```python
import smartcard
from smartcard.System import readers

# リーダーを検出
r = readers()
if len(r) == 0:
    print("カードリーダーが見つかりません")
    exit()

# 最初のリーダーを選択
reader = r[0]
print(f"使用するリーダー: {reader}")

# カードを待つ
connection = reader.createConnection()
connection.connect()

# APDUコマンドを使ってICカードと通信
apdu = [0xFF, 0xCA, 0x00, 0x00, 0x00] 
response, sw1, sw2 = connection.transmit(apdu)

# レスポンスを表示
print(f"レスポンス: {response}, ステータス: {hex(sw1)} {hex(sw2)}")
```

### 4. **テスト**
ICカードをRC-S300にかざし、Pythonスクリプトを実行して動作を確認します。カードが正しく認識され、応答が表示されるはずです。

### 5. **デバッグとトラブルシューティング**
もしカードが認識されない場合、以下の確認を行ってください：

- `pcscd`が正常に動作していることを確認します。`sudo systemctl status pcscd`でサービスの状態を確認します。
- カードがRC-S300に適切にかざされているかを確認します。
- `pcsc_scan`でカードが認識されているかを再確認します。

この手順で、Raspberry PiでRC-S300を使ってICカードの読み取りを行う準備が整います。質問があれば、お知らせください！


## APDUとは

APDU（Application Protocol Data Unit）は、スマートカードとカードリーダー間でデータを交換する際の通信プロトコルの単位です。この規格は、ISO/IEC 7816-4で定義されています。

**APDUの種類:**
- **コマンドAPDU（Command-APDU）**: カードリーダーからスマートカードへ送信されるコマンドです。
- **レスポンスAPDU（Response-APDU）**: スマートカードからカードリーダーへ返される応答です。

**APDUの構成:**
1. **コマンドAPDUの構成:**
   - **CLA（Class byte）**: コマンドのクラスを示す1バイト。
   - **INS（Instruction byte）**: コマンドの種類を示す1バイト。
   - **P1-P2（Parameter bytes）**: コマンドに関連するパラメータを示す2バイト。
   - **Lc（Length of command data）**: コマンドデータの長さを示す1バイト（データがある場合）。
   - **Data（Command data）**: コマンドに関連するデータ（省略可能）。
   - **Le（Length of expected response data）**: 期待されるレスポンスデータの長さを示す1バイト（省略可能）。

2. **レスポンスAPDUの構成:**
   - **Data（Response data）**: レスポンスデータ。
   - **SW1-SW2（Status words）**: コマンドの実行結果を示す2バイトのステータスコード。

**APDUの使用例:**
例えば、スマートカードから特定のデータを読み取る場合、カードリーダーは適切なコマンドAPDUを送信し、スマートカードは要求されたデータとともにレスポンスAPDUを返します。

**参考情報:**
- [スマートカードと通信するには #NFC - Qiita](https://qiita.com/spc_tokuda/items/e1fd51a57c867795354e)
- [マイナンバーカードとAPDUで通信して署名データ作成 - 晴耕雨読](https://tex2e.github.io/blog/protocol/jpki-mynumbercard-with-apdu)

これらの資料では、APDUの詳細や実際の使用例についてさらに深く理解することができます。 

## pcsc_scan の見方

```
Mon Mar 17 00:50:50 2025
 Reader 0: SONY FeliCa RC-S300/P (0269614) 00 00
  Event number: 17
  Card state: Card inserted,
  ATR: 3B 8F 80 01 80 4F 0C A0 00 00 03 06 11 00 3B 00 00 00 00 42

ATR: 3B 8F 80 01 80 4F 0C A0 00 00 03 06 11 00 3B 00 00 00 00 42
+ TS = 3B --> Direct Convention
+ T0 = 8F, Y(1): 1000, K: 15 (historical bytes)
  TD(1) = 80 --> Y(i+1) = 1000, Protocol T = 0
-----
  TD(2) = 01 --> Y(i+1) = 0000, Protocol T = 1
-----
+ Historical bytes: 80 4F 0C A0 00 00 03 06 11 00 3B 00 00 00 00
  Category indicator byte: 80 (compact TLV data object)
    Tag: 4, len: F (initial access data)
      Initial access data: 0C A0 00 00 03 06 11 00 3B 00 00 00 00
+ TCK = 42 (correct checksum)

Possibly identified card (using /usr/share/pcsc/smartcard_list.txt):
3B 8F 80 01 80 4F 0C A0 00 00 03 06 11 00 3B 00 00 00 00 42
3B 8F 80 01 80 4F 0C A0 00 00 03 06 .. 00 3B 00 00 00 00 ..
        FeliCa (as per PCSC std part3)
3B 8F 80 01 80 4F 0C A0 00 00 03 06 11 00 3B 00 00 00 00 42
        RFID - FeliCa (generic) (as per PCSC std part3)
        Suica public transit card (Japan IC system)
        (also: Hayakaken, ICOCA, Kitaca, manaca, nimoca, PASMO, PiTaPa, SUGOCA, TOICA)
        https://en.wikipedia.org/wiki/Suica
        Octopus, MTR network from Hong Kong, 2014



```

この出力は、PCSC（Personal Computer/Smart Card）ライブラリを使用して、SONY FeliCa RC-S300/PリーダーでICカード（おそらくFeliCaタイプ）を読み取った結果です。以下に出力内容を解説します。

### 基本情報
- **日時**: Mon Mar 17 00:50:50 2025
- **リーダー**: SONY FeliCa RC-S300/P（型番：0269614）
- **カードの状態**: 「カードが挿入されています」
- **イベント番号**: 17

### ATR (Answer To Reset)
- **ATRの内容**: 3B 8F 80 01 80 4F 0C A0 00 00 03 06 11 00 3B 00 00 00 00 42

ATRは、カードの初期化時に返される情報で、カードとリーダー間の通信を開始するための基本情報を含んでいます。この出力には、以下の情報が含まれています：

- **TS = 3B**: 「ダイレクト・コンベンション（Direct Convention）」というプロトコル方式が使われていることを示しています。
- **T0 = 8F, Y(1) = 1000, K = 15**: ヒストリカルバイト（歴史的データ）の形式に関する情報です。
- **TD(1) = 80**: プロトコルT = 0を使用することを示します。
- **TD(2) = 01**: プロトコルT = 1に関する情報です。

### ヒストリカルバイト（Historical bytes）
- **ヒストリカルバイト**: 80 4F 0C A0 00 00 03 06 11 00 3B 00 00 00 00
  - カテゴリ指示子バイト：80（コンパクトTLVデータオブジェクト）
  - 初期アクセスデータ（Tag: 4、len: F）: 0C A0 00 00 03 06 11 00 3B 00 00 00 00

### チェックサム（TCK）
- **TCK = 42**: 正しいチェックサムです。

### カードの識別情報
この出力は、カードが以下のもののいずれかである可能性を示唆しています（PCSC標準ファイル`smartcard_list.txt`に基づいて識別されています）：
- **FeliCa（PCSC標準 Part3）**
- **RFID - FeliCa（一般的なFeliCa）**
- **Suica（日本のICカードシステム）**や他の日本の交通系ICカード（例：Hayakaken、ICOCA、Kitaca、PASMO、PiTaPa、SUGOCA、TOICA）など、これらは全てFeliCa技術を基にしたカードです。
  - Wikipediaのリンクも提供されています：[Suica](https://en.wikipedia.org/wiki/Suica)
- **香港のOctopusカードや、MTRネットワーク（2014年）**

--- 

### 1. **最初のブロック**
```
3B 8F 80 01 80 4F 0C A0 00 00 03 06 11 00 3B 00 00 00 00 42
3B 8F 80 01 80 4F 0C A0 00 00 03 06 .. 00 3B 00 00 00 00 ..
        FeliCa (as per PCSC std part3)
```

#### 解説：
この部分は、カードのATR（Answer To Reset）データをPCSC標準に基づき解析した結果を示しています。

- **ATRデータ**: `3B 8F 80 01 80 4F 0C A0 00 00 03 06 11 00 3B 00 00 00 00 42`
  - **3B**: 初期バイトで、カードのプロトコルを示します。
  - **8F 80 01 80 4F 0C A0 00 00 03 06 11 00 3B 00 00 00 00 42**: これらは、カードの特性やプロトコルに関する詳細な情報を含むバイト列です。

- **`FeliCa (as per PCSC std part3)`**:
  - この記述は、PCSC標準のPart3に基づき、このカードがFeliCa技術を使用していることを示しています。FeliCaは、ソニーが開発した非接触型ICカード技術で、日本の交通系ICカード（Suica、PASMOなど）や電子マネーで広く使用されています。

### 2. **2番目のブロック**
```
3B 8F 80 01 80 4F 0C A0 00 00 03 06 11 00 3B 00 00 00 00 42
        RFID - FeliCa (generic) (as per PCSC std part3)
        Suica public transit card (Japan IC system)
        (also: Hayakaken, ICOCA, Kitaca, manaca, nimoca, PASMO, PiTaPa, SUGOCA, TOICA)
        https://en.wikipedia.org/wiki/Suica
        Octopus, MTR network from Hong Kong, 2014
```

#### 解説：
この部分は、カードの識別結果と、それに関連する情報を提供しています。

- **`RFID - FeliCa (generic) (as per PCSC std part3)`**:
  - これは、カードが一般的なFeliCa技術を使用したRFIDカードであることを示しています。FeliCaは、非接触型ICカード技術で、主に日本で広く使用されています。

- **`Suica public transit card (Japan IC system)`**:
  - **Suica**は、JR東日本が発行する交通系ICカードで、電車やバスの乗車、ショッピングなどで利用できます。

- **その他のカード名**:
  - **Hayakaken**、**ICOCA**、**Kitaca**、**manaca**、**nimoca**、**PASMO**、**PiTaPa**、**SUGOCA**、**TOICA**:
    - これらは、すべてFeliCa技術を使用した日本の交通系ICカードです。地域や鉄道会社によって異なりますが、基本的な機能はSuicaと類似しています。

- **[SuicaのWikipediaページ](https://en.wikipedia.org/wiki/Suica)**:
  - Suicaに関する詳細な情報が記載されたWikipediaのページへのリンクです。

- **`Octopus, MTR network from Hong Kong, 2014`**:
  - **Octopusカード**は、香港の公共交通機関で使用されるICカードで、2014年からMTR（香港の地下鉄）ネットワークでも使用されています。FeliCa技術を基にしており、香港の多くの交通機関や店舗で利用可能です。


---

APDU（Application Protocol Data Unit）コマンドは、スマートカードと通信する際の標準プロトコルとして広く採用されています。しかし、その仕様はICカードの種類や製造元によって異なる場合があります。

**APDUコマンドの標準化とカード固有の仕様:**

- **ISO/IEC 7816規格:** 一般的なスマートカード通信のためのAPDUコマンドの基本的な構造や動作は、ISO/IEC 7816-4で標準化されています。

- **カード固有の拡張:** 一方で、FeliCaやMIFAREなど、特定のICカード技術には独自のAPDUコマンドや拡張が存在します。例えば、FeliCaカードのIDm取得や、MIFARE Standardカードのデータ読み出しには、カード固有のAPDUコマンドが使用されます。

**まとめ:**

APDUコマンドの基本的な構造は標準化されていますが、カードの種類や製造元によっては、標準APDUに加えて独自のコマンドや拡張が存在します。そのため、特定のICカードと通信する際には、該当カードの仕様書やマニュアルを参照し、必要なコマンドやプロトコルを確認することが重要です。 


---


APDU（Application Protocol Data Unit）通信において、レスポンスAPDUの最後の2バイトは、コマンドの実行結果を示す**ステータスワード（SW1-SW2）**として使用されます。これらのステータスコードは、ICカードの種類によって異なる場合がありますが、一般的なものとして以下のコードが広く使用されています。

**主要なステータスコード:**

- **0x9000**: コマンドが正常に実行されたことを示します。
- **0x6700**: コマンドの長さが不正であることを示します。
- **0x6A80**: 不正なインストラクション（命令）が指定されたことを示します。
- **0x6D00**: 不正なインストラクションクラスが指定されたことを示します。
- **0x6E00**: 不正なパラメータが指定されたことを示します。
- **0x6300**: 警告、処理が完了したが注意が必要であることを示します。

**ステータスコードの標準化について:**

これらのステータスコードは、ISO/IEC 7816-4規格で定義されており、一般的なスマートカード間で標準化されています。しかし、ICカードの種類や製造元によっては、追加のステータスコードや独自のコードが使用されることがあります。そのため、特定のICカードと通信する際には、該当カードの仕様書やマニュアルを参照し、使用されるステータスコードの詳細を確認することが重要です。 