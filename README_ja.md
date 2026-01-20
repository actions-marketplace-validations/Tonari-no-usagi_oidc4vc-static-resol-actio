# oidc4vc-static-resolver

[English version (英語版)](README.md)

- **5分でVC発行**: GitHub Pages への Push だけで VC 発行サーバーが完成します。
- **最新規格準拠**: OIDC4VCI (Draft 13+) / SD-JWT VC / did:web に対応。

## デモ
実際にデプロイされたデモサイトはこちらです：
[https://tonari-no-usagi.github.io/oidc4vc-static-resol/](https://tonari-no-usagi.github.io/oidc4vc-static-resol/)

## 動作確認済みウォレット
以下のウォレットアプリで正常な VC 発行・表示を確認しています。
- **Lissi ID-Wallet** (iOS/Android)

## 主要機能

1. メタデータ・ジェネレーター
    - RFC 9101 および OpenID4VCI 規格に準拠した .well-known ディレクトリ構造を生成。
    - Go の型定義を用いて正確な JSON スキームを生成。

2. SD-JWT VC 生成
    - ビルド時に「事前承認済み」の VC を生成。
    - SD-JWT (Selective Disclosure JWT) 形式に対応し、属性のハッシュ化とチルダ連結を含む構造をサポート。

3. did:web 対応
    - 発行者用の did.json を自動生成。
    - ドメインとパスに基づいた正確な did:web 識別子のマッピング。

4. インタラクティブな検証用 HTML 生成
    - `openid-initiate-issuance://` カスタムスキームを含む QR コードを表示する index.html を生成。
    - モバイルウォレットアプリからの直接的な VC 取得体験を提供。

## システム構成

ツールは GitHub Pages 用に以下の構造を生成します。

```text
/public
|-- .well-known
|   |-- openid-credential-issuer  # 発行者のメタデータ
|   |-- did.json                  # did:web ドキュメント
|   `-- oauth-authorization-server # 認可サーバーのメタデータ
|-- credentials
|   `-- push-metadata             # 署名済み SD-JWT VC
`-- index.html                    # ウォレット読み取り用 QR コードを表示する GUI
```

## クイックスタート

### ローカルビルド
1. `issuer.yaml` で発行者の設定を行います。
2. ビルドコマンドを実行します。
   ```bash
   go run main.go build
   ```
3. `public` ディレクトリの内容を GitHub Pages にデプロイします。

### GitHub Actions (自動化)
リポジトリに以下のワークフローファイル (`.github/workflows/deploy.yml`) を作成することで、自動的にメタデータを生成してデプロイできます。

```yaml
name: Deploy VC Issuer
on:
  push:
    branches: [ main ]
jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    permissions:
      contents: write
    steps:
      - uses: actions/checkout@v4
      - name: Build Metadata
        uses: tonari-no-usagi/oidc4vc-static-resol-actio@main
      - name: Deploy to GitHub Pages
        uses: peaceiris/actions-gh-pages@v4
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./public
```

## 技術的背景

このプロジェクトは 2026 年のデジタルアイデンティティ・エコシステム（EUDI Wallet / eIDAS 2.0）および SD-JWT 規格への相互運用性を重視して設計されています。

## ライセンス

このプロジェクトは MIT ライセンスの下で公開されています。詳細は [LICENSE](LICENSE) ファイルを参照してください。
