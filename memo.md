# 2017/08/20

## プロジェクトインポート

- Mavenのプロジェクトとしてインポート。この時点だとpom.xmlの<parent>に×が付く。
- maven installすると<parent>の×はなくなるが、Project configuration is not up-to-date with pom.xml.というエラーが出る。
- Mavenプロジェクトを右クリック→[Maven] _ [Update Project…]で更新する。これでOK。

## postgresql設定コマンド

```bash
docker run --name mrs -d -p 5432:5432 postgres
docker exec -it mrs /bin/bash

psql -U postgres

CREATE ROLE mrs LOGIN UNENCRYPTED PASSWORD 'mrs' NOSUPERUSER INHERIT NOCREATEDB NOCREATEROLE NOREPLICATION;

CREATE DATABASE mrs WITH OWNER = mrs ENCODING = 'UTF8' TABLESPACE = pg_default LC_COLLATE = 'C' LC_CTYPE = 'C' TEMPLATE = 'template0' CONNECTION LIMIT = -1;
```

# 2017/08/25

## アプリ実行

- maven installするとschema.sqlなどのSQLは実行しなくても、会議室のデータが入った状態で普通にアプリ動く。  
  書籍に記載の通り、Spring Bootがクラスパス直下のschema.sql, data.sqlを実行しているのだろう。

## 14.2.6 JPAのエンティティの作成

- エンティティは4つ。MeeingRoom, ReservableRoom, Reservation, User。
- ここはずっとエンティティの作成をしている。[参考](http://terasolunaorg.github.io/guideline/5.3.0.RELEASE/ja/ImplementationAtEachLayer/DomainLayer.html#entity)
- 作成しているエンティティは上記４つに加えて以下の通り。
  + ReservableRoomId: ReservableRoomはroomIdとreservedDateが複合主キーとなる。  
    複合主キーを扱うために、複合主キークラスとしてReservableRoomIdを用意している。定石なのか？
  + その他JPA2.1の制約のために、本質的ではないが作成が必要な3つのエンティティ。

# 2017/08/26

## 14.2.7 会議室一覧表示機能の実装

### リポジトリクラスの作成

- [参考](http://terasolunaorg.github.io/guideline/5.3.0.RELEASE/ja/ImplementationAtEachLayer/DomainLayer.html#repository)
- リポジトリの役割は２つ。
  + Serviceに対して、Entityのライフサイクルを制御するための操作（Repositoryインタフェース）を提供する。
  + Entityを永続化する処理(Repositoryインタフェースの実装クラス)を提供する。
- リポジトリは基本エンティティと１：１で作るものっぽい。
- JPAの書き方が使われていて、経験ないと辛そうな感じに見える。

### サービスクラスの作成

- サービスの役割は２つ。
  + Controllerに対して業務ロジックを提供する。
  + トランザクション境界を宣言する。
- Service = ビジネスロジック
- リポジトリはサービスに手段を提供するためにある。  
  だからサービスはリポジトリをimportしている。

### コントローラクラスおよびHTMLの作成

- コントローラはモデルとサービスをimportしているが、リポジトリはimportしていない。なるほど疎結合だ。
- メソッドの戻り値はURLになっている。

### まとめ

- とりあえず１機能作る時は以下の流れで進めるっぽいことがわかった。
  + エンティティを作成する。
  + エンティティを操作するためのCRUD操作（リポジトリ）を作成する。
  + ビジネスロジック（サービス）を実装する。
  + Viewへの振り分け処理（コントローラ）を実装する。
- この例はJPAだが、自分でアプリ実装する時はMyBatisで実装予定だから、そこは要変更。

# 参考サイト

- [TERASOLUNA開発者ガイド 3.2. ドメイン層の開発](http://terasolunaorg.github.io/guideline/5.3.0.RELEASE/ja/ImplementationAtEachLayer/DomainLayer.html)

# DI

- SpringではApplicatonContextがDIコンテナの役割を担う。

# 用語集

|用語|略称|説明|出典|
|---|---|---|---|
|エンティティ|-|DBのテーブルとして扱われることが多い。テーブルとする場合は主キーの設定が必須。||
|Bean|-|DIコンテナに登録するコンポーネント|Spring徹底入門|
|Bean定義||Configurationのこと。Java(@Configuration)、XML(<beans>)、アノテーション(@Component)ベースがある。|Spring徹底入門|
|ルックアップ|-|DIコンテナからBeanを取得すること|Spring徹底入門|
