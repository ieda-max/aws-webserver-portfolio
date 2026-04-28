# AWS Lightsailを用いたWebサーバー構築（HTTPS対応）

---

## ■ 概要

AWS Lightsail上にLinuxサーバーを構築し、独自ドメイン設定・Web公開・HTTPS化まで一貫して実施しました。  
また、発生したトラブルに対して原因を切り分け、解決まで対応しました。

---

## ■ 構成図

[ユーザー（ブラウザ）]
　　　　↓
https://iemn.link
　　　　↓
[Route53（DNS）]
　　　　↓
[Lightsail（サーバー）]
　　　　↓
[Nginx（Webサーバー）]
　　　　↓
[HTML]


---

## ■ 使用技術

- AWS（Lightsail）
- Route53（DNS）
- Nginx（Webサーバー）
- Let's Encrypt（SSL証明書）
- Linux（Ubuntu）

---

## ■ 発生したトラブルと対応

---

### ① ポート競合エラー（nginx起動失敗）

**内容**

nginx起動時に以下のエラーが発生しました。

"nginx: [emerg] bind() to 0.0.0.0:80 failed (98: Address already in use)"


**原因**

ポート80がDockerコンテナで使用されていたため。

**対応**

```bash
"sudo lsof -i :80"
---
```bash
"sudo docker ps"

"sudo docker stop <コンテナID>"

"sudo docker rm <コンテナID>"
---
```bash
"sudo systemctl start nginx"

"sudo systemctl status nginx"
---

###　② Docker権限エラー

内容

"permission denied while trying to connect to the Docker daemon socket"

原因

Docker実行権限が付与されていなかったため。

対応

sudo docker ps

### ③ nginx起動エラー

内容

"Job for nginx.service failed because the control process exited with error code."

原因

設定やプロセス状態の不整合。

対応

sudo systemctl stop nginx

sudo pkill nginx

sudo systemctl start nginx

### ④ 文字化け

内容

Webページ表示時に日本語が文字化けした。

原因

文字コード未設定。

対応

<meta charset="UTF-8">

■ 工夫した点

・エラー内容から原因を切り分けて対応

・コマンドを用いて問題箇所を特定

・一つずつ検証しながら解決

■ 成果

・Webサイト公開成功

・HTTPS化（SSL）対応完了

<img width="533" height="656" alt="スクリーンショット 2026-04-24 134145" src="https://github.com/user-attachments/assets/c36db56f-5fdc-4b95-9622-8aa05dd439f2" />

　 https://iemn.link
