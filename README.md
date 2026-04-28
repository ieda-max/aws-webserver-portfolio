# AWS Lightsailを用いたWebサーバー構築（HTTPS対応）

## ■ 概要

　　　AWS Lightsail上にLinuxサーバーを構築し、ドメイン設定からWebサーバーの公開、HTTPS化まで一連の構築を行いました。
　　　また、構築過程で発生したトラブルに対して原因を切り分け、解決まで対応しました。

---

## ■ 構成図

```

　　[ユーザー（ブラウザ）]
        ↓ https://iemn.link
　　[Route53（DNS）]
        ↓
　　[Lightsail（サーバー）]
        ↓
　　[Nginx（Webサーバー）]
        ↓
　　[HTML]
```

---

## ■ 使用技術

　　　・AWS（Lightsail）
   
　　　・Route53（DNS）
   
　　　・Nginx（Webサーバー）
   
　　　・Let's Encrypt（SSL証明書）
   
　　　・Linux（Ubuntu系）

---

## ■ 実装内容

　　　1. LightsailでLinuxサーバーを作成
   
　　　2. Route53でドメインを取得し、AレコードでIPアドレスと紐付け
   
　　　3. Nginxをインストールし、Webサーバーを構築
   
　　　4. Certbotを使用してHTTPS化を実施
   
　　　5. ブラウザから https://iemn.link でアクセス確認

---

## ■ 工夫した点

　　　・問題発生時にいきなり修正するのではなく、原因の切り分けを行ってから対応
   
　　　・エラーメッセージやログをもとに原因を特定
   
　　　・一つずつ検証しながら確実に解決

---
## ■ 発生したトラブルと対応

　    ① ポート競合エラー（nginx起動失敗）

　　　　■ 内容

　　　　　nginx起動時に以下のエラーが発生しました。

　　　　　nginx: [emerg] bind() to 0.0.0.0:80 failed (98: Address already in use)

　　　　■ 原因

　　　　　ポート80が別プロセス（Docker）で使用されていたため。

　　　　■ 対応
    
         ---bash
　　　　　sudo lsof -i :80
         ---
         
   　　　  Dockerコンテナが原因と判明したため、以下のコマンドで停止・削除を実施しました。
         
          ---bash
　　　　　 sudo docker ps
　　　　　 sudo docker stop <コンテナID>
　　　　　 sudo docker rm <コンテナID>
          ---
          
   　　　その後、nginxを再起動し正常に起動することを確認しました。
           
           ---bash 
　　　　　 sudo systemctl start nginx
　　　　　 sudo systemctl status nginx
          --- 
          
　    ② Docker権限エラー

　　　  ■ 内容
    
   　　　 dockerコマンド実行時に以下のエラーが発生しました。
         
          ---bash
   　　　 permission denied while trying to connect to the Docker daemon socket
          ---
          
 　　　 ■ 原因
    
   　　　 現在のユーザーにDocker実行権限が付与されていなかったため。

 　　　 ■ 対応
   
   　　　  sudoを付けて管理者権限で実行することで対応しました。
        
           ---bash
   　　　   sudo docker ps
           ---
           
　     ③ nginx起動失敗（設定不整合）

 　　　  ■ 内容
   　　　
      　    nginxの設定ファイルに問題はないものの、サービス起動時にエラーが発生しました。
             
             ---bash
   　　　     Job for nginx.service failed because the control process exited with error code.
             ---
             
 　　　 ■ 原因
   
   　　　  certbot実行途中の影響により、設定やプロセス状態が不整合になっていた可能性。

　　　  ■ 対応
   
   　　　  nginxを一度完全停止し、プロセスをリセットしました。
             
             ---bash
   　　　　   sudo systemctl stop nginx
   　　　　   sudo pkill nginx
             ---
             
   　　　   その後、再度起動することで復旧しました。
             
             ---bash
    　　　    sudo systemctl start nginx
             ---
             
　     ④ 文字化け（文字コード問題）

　　　　 ■ 内容
    
   　　　　Webページ表示時に日本語が文字化けしました。
   
 　　　  ■ 原因
   
   　　　　 HTMLに文字コード（UTF-8）の指定がなかったため。

 　　　  ■ 対応
    
   　　　   HTMLの<head>内に以下を追加し解決しました。
   　　　　 
       　　　<meta charset="UTF-8">
            
---

## ■ 学んだこと

　　　* トラブル対応では「原因の切り分け」が重要であること
　　　* ポートや権限など、基礎的な仕組みの理解の重要性
　　　* 実際に手を動かして構築することで理解が深まること

---

## ■ 今後の改善点

　　　* AWSの複数サービス（EC2やALBなど）を組み合わせた構成の理解
　　　* セキュリティ設定（ファイアウォールやアクセス制御）の強化
　　　* ログ監視や運用面の知識の向上

---

## ■ 実行画面

　　　  <img width="533" height="656" alt="スクリーンショット 2026-04-24 134145" src="https://github.com/user-attachments/assets/ecdb28d6-51db-4392-9d3e-bffde459ca15" />

## ■ 公開URL

　　　https://iemn.link
