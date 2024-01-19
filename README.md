# インターネットTV

## ステップ1（テーブル設計）
**channels テーブル**
|カラム名|データ型|NULL|キー|初期値|AUTO INCREMENT|
| ----- | ----- | ----- | ----- | ----- | ----- |
|channel_id  |BIGINT      |NO     |PRIMARY|       |YES    |
|channel_name|VARCHAR(100)|NO     |UNIQUE |       |       |
- ユニークキー制約：channel_name  

---

**programs テーブル**
|カラム名|データ型|NULL|キー|初期値|AUTO INCREMENT|
| ----- | ----- | ----- | ----- | ----- | ----- |
|program_id    |BIGINT      |NO     |PRIMARY|       |YES    |
|program_name  |VARCHAR(100)|NO     |UNIQUE |       |       |
|program_detail|TEXT        |NO     |       |       |       |
- ユニークキー制約：program_name

---

**time_slots テーブル**
|カラム名|データ型|NULL|キー|初期値|AUTO INCREMENT|
| ----- | ----- | ----- | ----- | ----- | ----- |
|time_slot_id|BIGINT  |NO     |PRIMARY|       |YES    |
|channel_id  |BIGINT  |NO     |FOREIGN|       |       |
|program_id  |BIGINT  |NO     |FOREIGN|       |       |
|start_time  |DATETIME|NO     |       |       |       |
|end_time    |DATETIME|NO     |       |       |       |
|view_count  |BIGINT  |NO     |FOREIGN|       |       |
- 外部キー制約：channel_id
- 外部キー制約：program_id

---

**genres テーブル**
|カラム名|データ型|NULL|キー|初期値|AUTO INCREMENT|
| ----- | ----- | ----- | ----- | ----- | ----- |
|genre_id  |BIGINT      |NO     |PRIMARY|       |YES    |
|genre_name|VARCHAR(100)|NO     |UNIQUE |       |       |
- ユニークキー制約：genre_name  

---

**program_genres テーブル**
|カラム名|データ型|NULL|キー|初期値|AUTO INCREMENT|
| ----- | ----- | ----- | ----- | ----- | ----- |
|programs_genres_id|BIGINT |NO     |PRIMARY|       |YES    |
|program_id        |BIGINT |NO     |FOREIGN|       |       |
|genre_id          |BIGINT |NO     |FOREIGN|       |       |
- 外部キー制約：program_id
- 外部キー制約：genre_id

---

**seasons テーブル**
|カラム名|データ型|NULL|キー|初期値|AUTO INCREMENT|
| ----- | ----- | ----- | ----- | ----- | ----- |
|season_id    |BIGINT |NO     |PRIMARY|       |YES    |
|season_number|INT    |NO     |       |       |       |
|program_id   |BIGINT |NO     |FOREIGN|       |       |
- 外部キー制約：program_id

---

**episodes テーブル**
|カラム名|データ型|NULL|キー|初期値|AUTO INCREMENT|
| ----- | ----- | ----- | ----- | ----- | ----- |
|episode_id    |BIGINT      |NO     |PRIMARY|       |YES    |
|program_id    |BIGINT      |NO     |FOREIGN|       |       |
|season_id     |BIGINT      |YES    |FOREIGN|       |       |
|episode_number|BIGINT      |NO     |       |       |       |  
|episode_name  |VARCHAR(100)|NO     |       |       |       |
|epicode_detail|TEXT        |NO     |       |       |       |
|duration      |TIME        |NO     |       |       |       |
|release_date  |DATE        |NO     |       |       |       |
|view_count    |BIGINT      |NO     |       |0      |       |
- 外部キー制約：program_id
- 外部キー制約：season_id

## テーブル構築手順
1. **データベース作成**
  ```sql
  CREATE DATABASE internet_tv;
  USE internet_tv;
  ```

---

2. **テーブル作成**  
    1. **channelテーブル作成**
      ```sql
      CREATE TABLE channel
      (channel_id BIGINT NOT NULL AUTO_INCREMENT,
      channel_name VARCHAR(100) NOT NULL,
      PRIMARY KEY (channel_id),
      UNIQUE KEY (channel_name));
      ```
    
    2. **programテーブル作成**
      ```sql
      CREATE TABLE program
      (program_id BIGINT NOT NULL AUTO_INCREMENT,
      program_title VARCHAR(100) NOT NULL,
      program_content TEXT NOT NULL,
      PRIMARY KEY (program_id));
      ```

    3. **program_slotテーブル作成**
      ```sql
      CREATE TABLE program_slot
      (program_slot_id BIGINT NOT NULL AUTO_INCREMENT,
      channel_id BIGINT NOT NULL,
      program_id BIGINT NOT NULL,
      PRIMARY KEY (program_slot_id),
      FOREIGN KEY (channel_id) REFERENCES channel (channel_id),
      FOREIGN KEY (program_id) REFERENCES program (program_id));
      ```

    4. **time_slotテーブル作成**
      ```sql
      CREATE TABLE time_slot
      (time_slot_id BIGINT NOT NULL AUTO_INCREMENT,
      start_time DATETIME NOT NULL,
      end_time DATETIME NOT NULL,
      program_slot_id BIGINT NOT NULL,
      PRIMARY KEY (time_slot_id),
      FOREIGN KEY (program_slot_id) REFERENCES program_slot (program_slot_id));
      ```

    5. **genreテーブル作成**
      ```sql
      CREATE TABLE genre
      (genre_id BIGINT NOT NULL AUTO_INCREMENT,
      genre_name VARCHAR(100) NOT NULL,
      PRIMARY KEY (genre_id),
      UNIQUE KEY (genre_name));
      ```

    6. **programs_genresテーブル作成**
      ```sql
      CREATE TABLE programs_genres
      (programs_genres_id BIGINT NOT NULL AUTO_INCREMENT,
      program_id BIGINT NOT NULL,
      genre_id BIGINT NOT NULL,
      PRIMARY KEY (programs_genres_id),
      FOREIGN KEY (program_id) REFERENCES program (program_id),
      FOREIGN KEY (genre_id) REFERENCES genre (genre_id));
      ```

    7. **seasonテーブル作成**
      ```sql
      CREATE TABLE season
      (season_id BIGINT NOT NULL AUTO_INCREMENT,
      season_number INT NOT NULL,
      program_id BIGINT NOT NULL,
      PRIMARY KEY (season_id),
      FOREIGN KEY (program_id) REFERENCES program (program_id));
      ```

    8. **episodeテーブル作成**
      ```sql
      CREATE TABLE episode
      (episode_id BIGINT NOT NULL AUTO_INCREMENT,
      episode_number INT NOT NULL,
      episode_title VARCHAR(100) NOT NULL,
      episode_content TEXT NOT NULL,
      play_time TIME NOT NULL,
      release_date DATE NOT NULL,
      views BIGINT NOT NULL DEFAULT 0,
      season_id BIGINT,
      PRIMARY KEY (episode_id),
      FOREIGN KEY (season_id) REFERENCES season (season_id));
      ```

---

3. **サンプルデータの格納**  
    1. **channelテーブル データ格納**
      ```sql
      INSERT INTO channel (channel_name)
      VALUES ('ニュース')
             ('ドラマ1'),
             ('ドラマ2'),
             ('アニメ1'),
             ('アニメ2'),
             ('映画');
      ```

    2. **programテーブル データ格納**
      ```sql
      INSERT INTO program (program_title, program_content)
      VALUES ('鬼滅の刃', '『鬼滅の刃』の舞台は大正時代。 親兄弟を人喰い鬼に殺されてしまった少年・竈門 炭治郎（かまど たんじろう）が、唯一生き残るも
              鬼となってしまった妹の禰豆子（ねずこ）を人間に戻す方法を探すため、鬼たちとの戦いに身を投じる物語である。'),
             ('正直不動産', '『千の言葉のうち真実は三つしかない』という意味で、千三つといわれる不動産業界。そんな業界に身を置く永瀬財地は、契約のためなら嘘をいとわずに営業成績をあげてきた。しかし、地鎮祭のときに祠を破壊してしまったことで、嘘がつけない体質になってしまった。わがままな顧客の要望、一癖も二癖もある資産家大家たち、次々起こる不動産にまつわるトラブル、そしてライバル不動産会社としのぎを削る闘いに、嘘をつかない正直営業で立ち向かう永瀬の姿を描いた皮肉喜劇。'),
             ('進撃の巨人', '人間を襲う巨大な異形の生物「巨人」との戦いを描くダークファンタジーアクションアニメ。壁に囲まれた都市での生活と、巨人との壮絶な戦いが織り交ぜられたストーリー。'),
             ('半沢直樹', '銀行業界を舞台に、半沢直樹という銀行員が経済的な陰謀に立ち向かうビジネスドラマ。'),
             ('コード・ブルー -ドクターヘリ緊急救命-','ドクターヘリで活動する医師たちの姿を描く医療ドラマ。緊急救命医療のリアリティと人間ドラマが組み合わさっています。'),
             ('ごくせん','女性教師が問題児の生徒たちと向き合いながら成長していく学園ドラマ。コミカルな要素と感動的なエピソードが魅力です。'),
             ('相棒','警視庁特命係の刑事と彼の相棒である助手が難事件に挑む刑事ドラマ。推理とミステリーが絡み合ったストーリー展開が特徴的です。'),
             ('リーガルハイ','天才弁護士と非常識な新米弁護士のコンビが様々な法廷事件に挑む法律コメディドラマ。ユニークな登場人物たちが魅力的です。'),
             ('花より男子','人気の高い少女漫画を原作にした学園ドラマ。貧しい女子高生が華族の学園で不良グループ「花の4人組」に挑む姿が描かれています。'),
             ('もののけ姫','スタジオジブリ制作のアニメ映画をもとにしたドラマ。自然と人間の共存をテーマにした幻想的なファンタジードラマ。');
      ```

    3. **genreテーブル データ格納**
      ```sql
      INSERT INTO genre (genre_name)
      VALUES ('ニュース／報道'),
             ('スポーツ'),
             ('情報／ワイドショー'),
             ('ドラマ'),
             ('音楽'),
             ('バラエティ'),
             ('映画'),
             ('アニメ／特撮'),
             ('ドキュメンタリー／教養'),
             ('劇場／公演'),
             ('趣味／教育'),
             ('福祉'),
             ('その他');
      ```

    4. **programs_genresテーブル データ格納**
      ```sql
      INSERT INTO programs_genres (program_id, genre_id)
      VALUES (1, 8),
             (2, 4),
             (3, 8),
             (4, 4),
             (5, 4),
             (6, 4),
             (7, 4),
             (8, 4),
             (9, 4),
             (10, 7),
             (10, 8);
      ```

    5. **seasonテーブル データ格納**
      ```sql
      INSERT INTO season (season_number, program_id)
      VALUES (1, 1),
             (2, 1),
             (3, 1),
             (1, 2),
             (1, 3),
             (2, 3),
             (3, 3),
             (4, 3),
             (1, 4),
             (2, 4),
             (1, 5),
             (2, 5),
             (3, 5),
             (1, 6),
             (2, 6),
             (3, 6),
             (1, 7),
             (2, 7),
             (3, 7),
             (1, 8),
             (2, 8),
             (1, 9),
             (2, 9);
      ```

    6. **episodeテーブル データ格納**
      ```sql
      INSERT INTO episode (episode_number, episode_title, episode_content, play_time, release_date, season_id)
      VALUES (1, '刃の始まり', '主人公・炭治郎の家族が鬼に襲われる。', '00:23:00', '2021-01-01', 1),
             (2, '鬼殺隊への道', '炭治郎が鬼殺隊の隊士になるための修行開始。', '00:22:30', '2021-01-08', 1),
             (1, '遊郭編開始', '炭治郎が次なる任務に挑む。', '00:24:00', '2022-07-01', 2),
             (2, '新たな仲間', '新たな仲間たちとの出会い。', '00:22:45', '2022-07-08', 2),
             (1, '新たな取引', '不動産業者が新しい取引を開始する。', '00:25:00', '2023-03-01', 1),
             (2, '契約の落とし穴', '契約の際に予測できない問題が発生。', '00:24:30', '2023-03-08', 1),
             (3, '不動産市場の変動', '市場の変動により不動産業者たちが奮闘。', '00:26:00', '2024-09-01', 1),
             (4, '家族の事情', '主人公の家族に関連する新たな展開。', '00:23:45', '2024-09-08', 1),
             (1, '立身出世', '主人公・エレンが巨人に襲われる。', '00:24:00', '2013-04-06', 1),
             (2, '新たな同盟', 'エレンと仲間たちが新たな同盟を結成。', '00:23:30', '2013-04-13', 1),
             (1, '壁の中の秘密', '壁の中に隠された秘密が明らかになる。', '00:25:00', '2017-04-01', 2),
             (2, '巨人の脅威', '新たな巨人の脅威に立ち向かうエレンたち。', '00:24:15', '2017-04-08', 2),
             (1, '人類の過去', '人類と巨人の関係についての真実が明らかになる。', '00:23:45', '2018-07-22', 3),
             (2, '反撃開始', 'エレンたちが反撃を開始する。', '00:24:30', '2018-07-29', 3),
             (1, '自由の翼', '新たな敵との戦いが始まる。', '00:26:00', '2020-12-07', 4),
             (2, '秘密の地', '主人公たちが秘密の地を目指す。', '00:24:45', '2020-12-14', 4),
             (1, '半沢、銀行に立つ', '主人公・半沢直樹が銀行に着任する。', '00:24:00', '2013-07-07', 1),
             (2, '不正に立ち向かう', '半沢が不正に立ち向かう決意をする。', '00:23:30', '2013-07-14', 1),
             (1, '復讐の始まり', '半沢が復讐を始める。', '00:25:00', '2020-07-19', 2),
             (2, '新たな挑戦', '半沢が新たな挑戦に取り組む。', '00:24:15', '2020-07-26', 2),
             (1, 'ドクターヘリ出動', '主人公たちがドクターヘリで緊急出動する。', '00:24:00', '2008-07-03', 1),
             (2, '救命のレース', '医師たちが患者の命を救うために競争する。', '00:23:30', '2008-07-10', 1),
             (1, '新たな挑戦', '医師たちが新たな医療の挑戦に取り組む。', '00:25:00', '2010-01-11', 2),
             (2, '仲間たちの成長', '仲間たちが成長し、新たな仲間が加わる。', '00:24:15', '2010-01-18', 2),
             (1, '危機一髪', '医師たちが危機に立ち向かう。', '00:23:45', '2017-07-17', 3),
             (2, '感動の診療', '感動的な診療が描かれる。', '00:24:30', '2017-07-24', 3),
             (1, '新しい教師', '主人公が新しく教師として着任する。', '00:24:00', '2008-04-19', 1),
             (2, '問題児たち', '学校には問題児たちが集まっている。', '00:23:30', '2008-04-26', 1),
             (1, '変わりゆく関係', '教師と生徒たちの関係が変化していく。', '00:25:00', '2009-01-16', 2),
             (2, '学園祭の準備', '学園祭の準備に奮闘する仲間たち。', '00:24:15', '2009-01-23', 2),
             (1, '新たな試練', '新たな試練に立ち向かう教師と生徒たち。', '00:23:45', '2010-07-03', 3),
             (2, '友情の絆', '友情が試される出来事が起きる。', '00:24:30', '2010-07-10', 3),
             (1, '初めての舞台', '新たな刑事と助手のコンビが初めての事件に挑む。', '00:45:00', '2000-10-07', 1),
             (2, '新たな仲間', '刑事たちの仲間入りが決まる。', '00:42:30', '2000-10-14', 1),
             (1, '再会の日', 'かつての仲間との再会。', '00:48:00', '2001-10-06', 2),
             (2, '対決の予感', '新たな事件に直面し、対決の予感が漂う。', '00:46:15', '2001-10-13', 2),
             (1, '新たなスタート', '新メンバーが加わり、新たなスタートを迎える。', '00:47:45', '2002-10-12', 3),
             (2, '事件の真相', '事件の真相が明らかになる。', '00:45:30', '2002-10-19', 3),
             (1, '法廷の奇跡', '主人公が法廷で奇跡的な勝利を収める。', '00:48:00', '2012-04-17', 1),
             (2, '異例の弁護', '異例の手法でクライアントを弁護する。', '00:46:30', '2012-04-24', 1),
             (1, '法曹界の風雲児', '主人公が法曹界で注目を集める。', '00:50:00', '2013-04-17', 2),
             (2, '新たな挑戦', '新たな難事件に挑む。', '00:48:15', '2013-04-24', 2),
             (1, '花の学園生活', '主人公が華族学園に入学し、学園生活が始まる。', '00:24:00', '2005-10-21', 1),
             (2, 'F4の登場', '学園の最強グループ「F4」が登場し、主人公に注目が集まる。', '00:23:30', '2005-10-28', 1),
             (1, '新たな恋の始まり', '新たな登場人物たちとの恋模様が始まる。', '00:25:00', '2007-01-05', 2),
             (2, '別れと再会', '主人公とF4メンバーたちの別れと再会が描かれる。', '00:24:15', '2007-01-12', 2),
             (1, 'もののけ姫', 'スタジオジブリ制作のアニメーション映画。人間と自然、そして神秘的なもののけとの物語。', '02:30:00', '1997-07-12'),
      ```
    
    7. **program_slotテーブル データ格納**
      ```sql
      INSERT INTO program_slot (channel_id, program_id)
      VALUES (4, 1),
             (5, 3),
             (2, 2),
             (3, 4),
             (3, 5),
             (3, 6),
             (2, 7),
             (2, 8),
             (3, 9),
             (6, 10);
      ```

    8. **time_slotテーブル データ格納**
      ```sql
      INSERT INTO time_slot (start_time, end_time, program_slot_id)
      VALUES ('2024-01-01 00:00:00', '2024-01-01 00:30:00', 1),
             ('2024-01-01 00:30:00', '2024-01-01 01:00:00', 2),
             ('2024-01-01 01:00:00', '2024-01-01 01:30:00', 3),
             ('2024-01-01 01:30:00', '2024-01-01 02:00:00', 4),
             ('2024-01-01 02:00:00', '2024-01-01 02:30:00', 5),
             ('2024-01-01 02:30:00', '2024-01-01 03:00:00', 6),
             ('2024-01-01 03:00:00', '2024-01-01 03:30:00', 7),
             ('2024-01-01 03:30:00', '2024-01-01 04:00:00', 8),
             ('2024-01-01 04:00:00', '2024-01-01 04:30:00', 9),
             ('2024-01-01 04:00:00', '2024-01-01 06:30:00', 10);
      ```

---

## データ抽出
**1. よく見られているエピソードを知りたいです。エピソード視聴数トップ3のエピソードタイトルと視聴数を取得してください**
```sql
```

**2. よく見られているエピソードの番組情報やシーズン情報も合わせて知りたいです。エピソード視聴数トップ3の番組タイトル、シーズン数、エピソード数、エピソードタイトル、視聴数を取得してください**
```sql
```

**3. 本日の番組表を表示するために、本日、どのチャンネルの、何時から、何の番組が放送されるのかを知りたいです。本日放送される全ての番組に対して、チャンネル名、放送開始時刻(日付+時間)、放送終了時刻、シーズン数、エピソード数、エピソードタイトル、エピソード詳細を取得してください。なお、番組の開始時刻が本日のものを本日方法される番組とみなすものとします**
```sql
```

**4. ドラマというチャンネルがあったとして、ドラマのチャンネルの番組表を表示するために、本日から一週間分、何日の何時から何の番組が放送されるのかを知りたいです。ドラマのチャンネルに対して、放送開始時刻、放送終了時刻、シーズン数、エピソード数、エピソードタイトル、エピソード詳細を本日から一週間分取得してください**
```sql
```

**5. 直近一週間で最も見られた番組が知りたいです。直近一週間に放送された番組の中で、エピソード視聴数合計トップ2の番組に対して、番組タイトル、視聴数を取得してください**
```sql
```

**6. ジャンルごとの番組の視聴数ランキングを知りたいです。番組の視聴数ランキングはエピソードの平均視聴数ランキングとします。ジャンルごとに視聴数トップの番組に対して、ジャンル名、番組タイトル、エピソード平均視聴数を取得してください。**
```sql
```
