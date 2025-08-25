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

## コーディング規約

- [WordPress Coding Standards](https://developer.wordpress.org/coding-standards/wordpress-coding-standards/)に準拠
- PSR-4オートローディング
- セキュリティ：入力値のサニタイズ、出力時のエスケープ処理

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