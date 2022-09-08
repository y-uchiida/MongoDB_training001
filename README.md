# MongoDB_training001

MongoDB への入門として、Docker でのセットアップおよびブックレビューのデータを扱う DB の操作を行いました

# 開発環境

- Windows 11 21H2
- WSL2 Ubuntu 20.04
- Docker version 20.10.17, build 100c701

# ローカル環境での実行

## コンテナ起動

docker が動作しているコンピュータに当リポジトリをクローンし、  
当リポジトリの`docker-compose.yml` があるディレクトリで以下コマンドを実行します

```bash
$ docker-compose up -d

```

## コンテナ終了

終了時は、当リポジトリの`docker-compose.yml` があるディレクトリで以下コマンドを実行します

```
$ docker-compose down
```

コンテナ内のデータベースに作成したデータを削除する場合は、以下のコマンドでコンテナを終了すると、データボリュームも削除されます  
内容を初期状態にリセットする場合も、ボリューム削除後に再度コンテナを起動しなおします

```
$ docker-compose down -v
```

# 使用方法

## コンテナ接続

MongoDB を操作するため、サーバが動作しているコンテナへ接続する必要があります  
当リポジトリの`docker-compose.yml` があるディレクトリで以下コマンドを実行します

```
$ docker-compose exec mongo bash
```

## mongoDB のクライアント起動(mongo コマンド)

MongoDB を CLI で操作するため、`mongo` コマンドを実行します  
`booklist` データベースを利用するため、`init.js` で `mongo` というユーザーを作成して捜査権限を与えています  
mongo ユーザーでデータベースクライアントを利用するには、コンテナのターミナル上で以下のコマンドを実行します

```
root@1792fb28dc5b:/# mongo -u mongo -p password --authenticationDatabase admin
```

# 参考

- 以下の動画教材をベースに進めました  
  MongoDB 入門 ー演習しながら学ぶクエリ操作ー:  
  https://www.udemy.com/course/introductory-mongodb

- docker-compose.yml は、以下のページを参考に作成しました  
  Docker で MongoDB、Mongo Express の環境を立ち上げる(docker-compose):  
  https://de-milestones.com/docker_mongodb_mongo-express/

- 認証関連、およびセットアップスクリプトの作成は以下を参考にしました  
  はじめての MongoDB with Docker:  
  https://zenn.dev/optimisuke/articles/d6b248d45f4f5c#auth
