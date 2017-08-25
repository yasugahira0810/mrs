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

# 参考サイト

- [TERASOLUNA開発者ガイド 3.2. ドメイン層の開発](http://terasolunaorg.github.io/guideline/5.3.0.RELEASE/ja/ImplementationAtEachLayer/DomainLayer.html)

# 用語集

|用語|略称|説明|
|---|---|---|
|エンティティ|-|DBのテーブルとして扱われることが多い。テーブルとする場合は主キーの設定が必須。|
