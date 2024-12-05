# mysqli
mysqlをphpで使うためのモジュールです。
モジュールはプログラムの部品のことです。
モジュール、パッケージ、プラグイン。これらの違いって何なんでしょうね。
mysqliのiはimproveのiです。

## コード例
### データベースに接続し、インスタンスを生成
```php
$mysqli = new mysqli("localhost", $username, $password, $db_name);
```
dbとはDataBaseのことですね。
### SQLクエリを実行する
#### 脆弱な方法
この方法ではSQLインジェクション攻撃を受けます。
```php
$result = $mysqli -> query("SQL文");
```
生成されたmysqliインスタンスを利用するわけです。
#### 安全な方法（プリペアードステートメント）
```php
$stmt = $mysqli->prepare("INSERT INTO test(id, label) VALUES (?, ?)");

$id = 1;
$label = 'PHP';
$stmt->bind_param("is", $id, $label); // "is" means that $id is bound as an integer and $label as a string

$stmt->execute();
```


## mysqliクラス
PHPとMySQLの接続を表すクラス
メンバの名前|返り値|説明
---|---|---
__constructor() | mysqli | DBに接続する
query() | mysqli_result | クエリを実行する
prepare() | mysqli_stmt | SQL文を準備する

## mysqli_stmtクラス
プリペアードステートメントを表す
メンバの名前|返り値|説明
---|---|---
execute() | bool | プリペアードステートメントを実行する
get_result() | mysqli_result | 実行結果をmysqli_resultオブジェクトとして受け取る
bind_param() | bool | プリペアードステートメントのパラメータに値をはめ込む
bind_result() | bool | (SELECT文の)実行結果を一行ずつ、カラムごとに変数に代入する。


## mysqli_resultクラス
データベースへのクエリにより得られた結果セットを表します。
メンバの名前|返り値|説明
---|---|---
fetch_all() | ※ | 指定した型で全行返す
fetch_array() | array | 一行を配列で返す
fetch_assoc() | array | 一行を連想配列で返す
※ 