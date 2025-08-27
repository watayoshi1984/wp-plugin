# WordPress Plugin Development

WordPressプラグイン開発用のリポジトリです。

## 概要

このリポジトリは、WordPressプラグインの開発を効率的に行うためのベースプロジェクトです。

## 必要な環境

- PHP 7.4 以上
- WordPress 5.0 以上
- Composer（依存関係管理）
- Node.js（フロントエンド開発用）

## ディレクトリ構造

```
wp-plugin/
├── assets/                 # CSSやJavaScriptファイル
│   ├── css/
│   ├── js/
│   └── images/
├── includes/               # PHPクラスファイル
├── languages/              # 翻訳ファイル
├── templates/              # テンプレートファイル
├── tests/                  # テストファイル
├── plugin-name.php         # メインプラグインファイル
├── composer.json           # Composer設定
├── package.json            # npm設定
└── README.md
```

## 開発の始め方

1. リポジトリをクローン
```bash
git clone https://github.com/watayoshi1984/wp-plugin.git
cd wp-plugin
```

2. 依存関係のインストール
```bash
composer install
npm install
```

3. 開発用ビルド
```bash
npm run dev
```

## プラグインの基本構造

### メインプラグインファイル
プラグインのエントリーポイントとなるPHPファイルで、以下の要素を含みます：

- プラグインヘッダー情報
- セキュリティチェック
- メインクラスの読み込み
- アクティベーション/ディアクティベーションフック

### クラス構造
- **Main Class**: プラグインの中心となるクラス
- **Admin Class**: 管理画面機能
- **Frontend Class**: フロントエンド機能
- **Database Class**: データベース操作
- **Settings Class**: 設定管理

## コーディング規約とベストプラクティス

### PHPコーディング規約

#### 基本原則
- [WordPress Coding Standards](https://developer.wordpress.org/coding-standards/wordpress-coding-standards/)に厳密準拠
- PSR-4オートローディングによるクラス自動読み込み
- 一つのファイルに一つのクラス構造（例：`class-example-class.php`）
- 可視性の明示的宣言（`public`, `protected`, `private`）必須

#### 構文規約
```php
// Yoda条件（定数を左側に配置）
if ( true === $the_force ) {
    $victorious = you_will( $be );
}

// 厳密比較演算子の使用
if ( 0 === strpos( $text, 'WordPress' ) ) {
    echo esc_html__( 'Yay WordPress!', 'textdomain' );
}

// 前置インクリメント/デクリメント推奨
--$a; // 正しい
$a--; // 非推奨
```

#### ファイル構造
```php
<?php
/**
 * Plugin Name: Example Plugin
 * Description: Example plugin description.
 * Version: 1.0.0
 * Author: Your Name
 */

// 直接アクセス防止
if ( ! defined( 'ABSPATH' ) ) {
    exit;
}

// メインクラスの読み込み
require_once plugin_dir_path( __FILE__ ) . 'includes/class-example-plugin.php';
```

### セキュリティベストプラクティス

#### 入力値の検証とサニタイズ
```php
// nonce検証
if ( isset( $_POST['example_nonce'] ) && 
     wp_verify_nonce( sanitize_key( $_POST['example_nonce'] ), 'example_action' ) ) {
    
    // 入力値のサニタイズ
    $user_input = sanitize_text_field( wp_unslash( $_POST['user_input'] ) );
}

// データベースクエリの準備
$wpdb->query( $wpdb->prepare( 
    "UPDATE {$wpdb->posts} SET post_title = %s WHERE ID = %d", 
    $title, 
    $post_id 
) );
```

#### 出力時のエスケープ
```php
// HTML出力
echo esc_html( $user_data );

// 属性値
echo '<input value="' . esc_attr( $value ) . '" />';

// URL
echo '<a href="' . esc_url( $url ) . '">Link</a>';

// JavaScript
echo '<script>var data = ' . wp_json_encode( $data ) . ';</script>';
```

### フック（Actions/Filters）開発

#### フックの命名と使用
```php
// 動的フックでの補間記法使用
do_action( "{$new_status}_{$post->post_type}", $post->ID, $post );

// カスタムフィルターの実装
apply_filters( 'my_plugin_custom_filter', $value, $additional_param );
```

#### フックの文書化
```php
/**
 * プラグイン初期化後に実行されるアクション
 *
 * @since 1.0.0
 *
 * @param string $plugin_version プラグインのバージョン
 * @param array  $config {
 *     設定オプション
 *
 *     @type bool   $debug      デバッグモード
 *     @type string $api_key    APIキー
 *     @type int    $cache_time キャッシュ時間（秒）
 * }
 */
do_action( 'my_plugin_initialized', $this->version, $this->config );
```

### 国際化（i18n）対応

#### 翻訳関数の使用
```php
// 基本的な翻訳
__( 'Hello World', 'my-plugin-textdomain' );
_e( 'Display Text', 'my-plugin-textdomain' );

// 複数形対応
_n( 
    'One item', 
    '%d items', 
    $count, 
    'my-plugin-textdomain' 
);

// 文脈付き翻訳
_x( 'Post', 'noun', 'my-plugin-textdomain' );
```

#### テキストドメインの登録
```php
function my_plugin_load_textdomain() {
    load_plugin_textdomain(
        'my-plugin-textdomain',
        false,
        dirname( plugin_basename( __FILE__ ) ) . '/languages/'
    );
}
add_action( 'plugins_loaded', 'my_plugin_load_textdomain' );
```

### データベース操作

#### カスタムテーブルの作成
```php
function create_custom_table() {
    global $wpdb;
    
    $table_name = $wpdb->prefix . 'my_plugin_data';
    
    $charset_collate = $wpdb->get_charset_collate();
    
    $sql = "CREATE TABLE $table_name (
        id mediumint(9) NOT NULL AUTO_INCREMENT,
        time datetime DEFAULT '0000-00-00 00:00:00' NOT NULL,
        name tinytext NOT NULL,
        text text NOT NULL,
        url varchar(55) DEFAULT '' NOT NULL,
        PRIMARY KEY  (id)
    ) $charset_collate;";
    
    require_once( ABSPATH . 'wp-admin/includes/upgrade.php' );
    dbDelta( $sql );
}
```

### エラーハンドリング

#### ログ記録
```php
function my_plugin_log( $message, $level = 'info' ) {
    if ( defined( 'WP_DEBUG' ) && WP_DEBUG ) {
        error_log( "[My Plugin] {$level}: {$message}" );
    }
}

// 使用例
try {
    // 何らかの処理
} catch ( Exception $e ) {
    my_plugin_log( 'Error: ' . $e->getMessage(), 'error' );
}
```

### パフォーマンス最適化

#### キャッシュの活用
```php
function get_expensive_data( $key ) {
    $cache_key = 'my_plugin_' . md5( $key );
    $data = wp_cache_get( $cache_key );
    
    if ( false === $data ) {
        // 重い処理
        $data = perform_expensive_operation( $key );
        wp_cache_set( $cache_key, $data, '', HOUR_IN_SECONDS );
    }
    
    return $data;
}
```

### 推奨ツール

#### 開発環境
- **PHP_CodeSniffer**: WordPress Coding Standards チェック
- **PHPStan**: 静的解析ツール
- **Composer**: 依存関係管理
- **WP-CLI**: WordPress コマンドラインツール

#### インストールコマンド
```bash
# WordPress Coding Standards
composer require --dev wp-coding-standards/wpcs

# コードチェック実行
vendor/bin/phpcs --standard=WordPress wp-load.php
```

## セキュリティ

- `wp_nonce_field()`を使用したCSRF対策
- `sanitize_*`関数による入力値の検証
- `esc_*`関数による出力時のエスケープ
- 直接ファイルアクセスの防止

## 国際化対応

- 翻訳可能な文字列は`__()`, `_e()`, `_n()`関数を使用
- テキストドメインの統一
- POTファイルの生成と管理

## テスト

```bash
# PHPUnit テスト実行
composer test

# JavaScript テスト実行
npm test
```

## ビルド

```bash
# 本番用ビルド
npm run build

# 開発用ビルド（ウォッチモード）
npm run dev
```

## デプロイ

1. プラグインディレクトリを作成
2. 必要なファイルのみをコピー
3. ZIPファイルとしてパッケージ化

## 貢献

1. このリポジトリをフォーク
2. フィーチャーブランチを作成 (`git checkout -b feature/amazing-feature`)
3. 変更をコミット (`git commit -m 'Add some amazing feature'`)
4. ブランチにプッシュ (`git push origin feature/amazing-feature`)
5. プルリクエストを作成

## ライセンス

このプロジェクトはMITライセンスの下で公開されています。詳細は[LICENSE](LICENSE)ファイルを参照してください。

## サポート

バグレポートや機能要求は[Issues](https://github.com/watayoshi1984/wp-plugin/issues)にて受け付けています。

## 参考リンク

- [WordPress Plugin Handbook](https://developer.wordpress.org/plugins/)
- [WordPress Coding Standards](https://developer.wordpress.org/coding-standards/)
- [WordPress Security](https://developer.wordpress.org/plugins/security/)
- [WordPress Internationalization](https://developer.wordpress.org/plugins/internationalization/)