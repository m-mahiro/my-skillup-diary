(ChatGPTコピペです。愛がなくてすみません。)
# PHPの基本構文

---

## 1. PHPタグ

### 開始タグ `<?php` と終了タグ `?>` の使い方
PHPコードは `<?php` で開始し、終了タグ `?>` で閉じます。
```php
<?php
echo "Hello, World!";
?>
```

### 短縮タグの注意点
短縮タグ `<?` や `<?=` はサーバー設定に依存します。特に古い環境では利用可能ですが、標準的なコードでは避けるべきです。
```php
<?= "Hello, World!"; // 短縮タグでの出力 ?>
```

### 終了タグの省略
終了タグを省略する利点：
- 意図しない空白や改行が出力されることを防ぐ。
- セキュリティリスクの軽減。
```php
<?php
echo "Hello, World!";
// 終了タグを省略
```

---

## 2. コメントの書き方

### コメントの種類
- シングルラインコメント:
  ```php
  // これはシングルラインコメント
  # これもシングルラインコメント
  ```

- マルチラインコメント:
  ```php
  /*
  これは
  マルチラインコメント
  */
  ```

### コメントの使いどころ
- コードの説明や意図を記述。
- デバッグ時の一時的なコード無効化。

---

## 3. 変数の宣言と初期化

### `$` で始まる変数
変数名は `$` で始まり、英数字やアンダースコアを使用します。
```php
$name = "John";
$age = 30;
$is_admin = true;
```

### 命名規則
- スネークケース: `$user_name`
- キャメルケース: `$userName`

### 型変換とキャスト
```php
$number = "123";
$int_number = (int)$number; // 文字列を整数型に変換
```

---

## 4. データ型と操作

### PHPの主なデータ型
1. **スカラー型**: `int`, `float`, `string`, `bool`
2. **複合型**: `array`, `object`
3. **特殊型**: `resource`, `NULL`

### 型確認と変換
- `gettype()`: 型を取得。
- `var_dump()`: 型と値を表示。
```php
$var = 10.5;
echo gettype($var); // "double"
var_dump($var);     // float(10.5)
```

---

## 5. 演算子

### 算術演算子
```php
$sum = 10 + 5;
```

### 比較演算子
```php
$is_equal = (10 == "10");   // true
$is_identical = (10 === "10"); // false
```

### Null合体演算子
```php
$value = $var ?? "default";
```

---

## 6. 条件分岐

### 基本構文
```php
if ($condition) {
    // 条件が真の場合
} elseif ($another_condition) {
    // 別の条件が真の場合
} else {
    // すべての条件が偽の場合
}
```

### `match` 式（PHP 8以降）
```php
$result = match($value) {
    1 => "One",
    2 => "Two",
    default => "Other",
};
```

---

## 7. 繰り返し処理

### `foreach`
```php
$array = [1, 2, 3];
foreach ($array as $value) {
    echo $value;
}
```

---

## 8. 配列

### 配列の操作
```php
$array = [1, 2, 3];
$array[] = 4; // 要素を追加
```

### 配列関数
- `array_map()`: 各要素に関数を適用。
- `array_filter()`: 条件に合う要素を抽出。
```php
$filtered = array_filter($array, fn($value) => $value > 2);
```

---

## 9. 関数の定義と呼び出し

### 無名関数
```php
$sum = function($a, $b) {
    return $a + $b;
};
echo $sum(1, 2);
```

---

## 10. スコープ

### 変数スコープ
- ローカル変数: 関数内のみ有効。
- グローバル変数: 関数外で定義。
```php
function test() {
    global $var; // グローバル変数を利用
}
```

---

## 11. クラスとオブジェクト

### クラスの定義
```php
class Person {
    public $name;
    public function __construct($name) {
        $this->name = $name;
    }
}
$person = new Person("John");
```

---

## 12. 名前空間

### 利用例
```php
namespace MyApp;
class MyClass {}
use MyApp\MyClass as AliasClass;
```

---

## 13. 例外処理

### 基本構文
```php
try {
    throw new Exception("エラー発生");
} catch (Exception $e) {
    echo $e->getMessage();
}
```

---

## 14. スーパーグローバル変数

### 主な変数
- `$_GET`, `$_POST`: ユーザーからの入力データ。
- `$_SERVER`: サーバー情報。

---

## 15. フォームとファイル操作

### フォームデータの受け取り
```php
$name = $_POST['name'] ?? '';
```

### ファイル操作
```php
file_put_contents("test.txt", "Hello, File!");
echo file_get_contents("test.txt");
```
