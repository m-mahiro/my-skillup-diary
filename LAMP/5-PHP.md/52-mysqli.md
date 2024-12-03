# mysqli
mysqlをphpで使うためのモジュールです。
モジュールはプログラムの部品のことです。
モジュール、パッケージ、プラグイン。これらの違いって何なんでしょうね。
mysqliのiはimproveのiです。

データベースに接続し、インスタンスを生成
```php
$mysqli = new mysqli("localhost", $username, $password, $db_name);
```
dbとはDataBaseのことですね。
SQL