# ペネトレーションテスト検証環境

このプロジェクトは、セキュリティ学習とペネトレーションテスト検証用の Docker Compose 環境です。

## ⚠️ 警告

**この環境は意図的に脆弱性を含んでいます。本番環境や公開ネットワークでは絶対に使用しないでください。**

- ローカルネットワークまたは隔離された環境でのみ使用してください
- 教育目的およびセキュリティテストの学習専用です
- 第三者のシステムへの攻撃には使用しないでください

## 環境構成

### ネットワーク設定

- ネットワーク: `172.20.0.0/24`
- ゲートウェイ: `172.20.0.1`

### ターゲットシステム

#### 1. OWASP Juice Shop (`172.20.0.10`)

**イメージ**: `bkimminich/juice-shop`

**アクセス**: http://localhost:3000

**説明**:
- OWASP が開発した脆弱な Web アプリケーション
- 現代的な JavaScript/TypeScript ベースの SPA（Single Page Application）
- XSS、SQLi、認証バイパス、API の脆弱性など、多数の OWASP Top 10 の脆弱性を含む
- ゲーミフィケーション要素があり、脆弱性を発見するとスコアが記録される

**学習内容**:
- Web アプリケーションの脆弱性全般
- API セキュリティ
- JWT トークン操作
- ビジネスロジックの脆弱性

#### 2. Metasploitable3 Ubuntu (`172.20.0.20`)

**イメージ**: `tleemcclain/metasploitable3-ubuntu`

**アクセス**:
- HTTP: http://localhost:80
- SSH: `ssh <user>@localhost -p 22`
- その他多数のサービスが稼働

**説明**:
- Rapid7 が開発した脆弱な Ubuntu ベースのシステム
- 複数の脆弱なサービスとアプリケーションが稼働
- ネットワークサービスの脆弱性、権限昇格、エクスプロイトの実践に最適
- FTP、SSH、Telnet、HTTP、SMB、MySQL、PostgreSQL など多数のサービス

**学習内容**:
- ネットワークスキャン（Nmap）
- サービスの脆弱性スキャン
- Metasploit Framework の利用
- 権限昇格テクニック
- ネットワークサービスのエクスプロイト

#### 3. DVWA - Damn Vulnerable Web Application (`172.20.0.30`)

**イメージ**: `vulnerables/web-dvwa`

**アクセス**: http://localhost:8081

**デフォルト認証情報**:
- ユーザー名: `admin`
- パスワード: `password`

**説明**:
- PHP/MySQL ベースの脆弱な Web アプリケーション
- 4つの難易度レベル（Low、Medium、High、Impossible）で段階的に学習可能
- 各脆弱性に対する詳細な解説とソースコードが含まれる
- SQLi、XSS、CSRF、ファイルインクルージョンなどの基本的な脆弱性

**学習内容**:
- SQL インジェクション（SQLi）
- クロスサイトスクリプティング（XSS）
- コマンドインジェクション
- ファイルアップロードの脆弱性
- CSRF（クロスサイトリクエストフォージェリ）

## 使用方法

### 環境の起動

```bash
docker-compose up -d
```

### 環境の停止

```bash
docker-compose down
```

### ログの確認

```bash
docker-compose logs -f [サービス名]
```

### コンテナの状態確認

```bash
docker-compose ps
```

## 攻撃側からのアクセス

### IP アドレスでの直接アクセス

Docker ネットワーク内から攻撃する場合：

- Juice Shop: `172.20.0.10:3000`
- Metasploitable3: `172.20.0.20`
- DVWA: `172.20.0.30:80`

### Kali Linux などの攻撃側コンテナを追加する場合

docker-compose.yml に以下を追加：

```yaml
  kali:
    image: kalilinux/kali-rolling
    container_name: kali-attacker
    networks:
      pentest-network:
        ipv4_address: 172.20.0.100
    stdin_open: true
    tty: true
    privileged: true
    volumes:
      - ./workspace:/root/workspace
```

接続方法：

```bash
docker exec -it kali-attacker /bin/bash
```

## 推奨ツール

- **Nmap**: ポートスキャン、サービス検出
- **Metasploit Framework**: エクスプロイト実行
- **Burp Suite**: Web プロキシ、脆弱性スキャン
- **OWASP ZAP**: Web アプリケーションスキャナー
- **sqlmap**: SQL インジェクション自動化ツール
- **Nikto**: Web サーバースキャナー

## トラブルシューティング

### ポートの競合

既に使用されているポートがある場合、docker-compose.yml のポートマッピングを変更してください。

### コンテナが起動しない

```bash
docker-compose logs [サービス名]
```

でログを確認してください。

### ネットワークの問題

```bash
docker network inspect hacking-lab_pentest-network
```

でネットワーク設定を確認してください。

## ライセンスと責任

このプロジェクトは教育目的のみで提供されています。使用者は、適用される法律とガイドラインを遵守する責任があります。不正アクセス禁止法などの法律を遵守してください。

## 参考リンク

- [OWASP Juice Shop](https://owasp.org/www-project-juice-shop/)
- [Metasploitable3](https://github.com/rapid7/metasploitable3)
- [DVWA](https://github.com/digininja/DVWA)
