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
|episode_detail|TEXT        |NO     |       |       |       |
|duration      |TIME        |NO     |       |       |       |
|release_date  |DATE        |NO     |       |       |       |
|view_count    |BIGINT      |NO     |       |0      |       |
- 外部キー制約：program_id
- 外部キー制約：season_id

---

**time_slots テーブル**
|カラム名|データ型|NULL|キー|初期値|AUTO INCREMENT|
| ----- | ----- | ----- | ----- | ----- | ----- |
|time_slot_id|BIGINT  |NO     |PRIMARY|       |YES    |
|channel_id  |BIGINT  |NO     |FOREIGN|       |       |
|program_id  |BIGINT  |NO     |FOREIGN|       |       |
|episode_id  |BIGINT  |NO     |FOREIGN|       |       |
|start_time  |DATETIME|NO     |       |       |       |
|end_time    |DATETIME|NO     |       |       |       |
|view_count  |BIGINT  |NO     |FOREIGN|       |       |
- 外部キー制約：channel_id
- 外部キー制約：program_id
- 外部キー制約：episode_id

## ステップ2（テーブル構築）
### データベース作成
  ```sql
  CREATE DATABASE internet_tv;
  ```

  ```sql
  USE internet_tv;
  ```

### テーブル作成
```sql
-- channelsテーブル作成
CREATE TABLE channels (
channel_id BIGINT NOT NULL AUTO_INCREMENT,
channel_name VARCHAR(100) NOT NULL,
PRIMARY KEY (channel_id),
UNIQUE KEY (channel_name));

-- programsテーブル作成
CREATE TABLE programs (
program_id BIGINT NOT NULL AUTO_INCREMENT,
program_name VARCHAR(100) NOT NULL,
program_detail TEXT NOT NULL,
PRIMARY KEY (program_id),
UNIQUE KEY (program_name));

-- genresテーブル作成
CREATE TABLE genres (
genre_id BIGINT NOT NULL AUTO_INCREMENT,
genre_name VARCHAR(100) NOT NULL,
PRIMARY KEY (genre_id),
UNIQUE KEY (genre_name));

-- program_genresテーブル作成
CREATE TABLE program_genres (
program_genre_id BIGINT NOT NULL AUTO_INCREMENT,
program_id BIGINT NOT NULL,
genre_id BIGINT NOT NULL,
PRIMARY KEY (program_genre_id),
FOREIGN KEY (program_id) REFERENCES programs (program_id),
FOREIGN KEY (genre_id) REFERENCES genres (genre_id));

-- seasonsテーブル作成
CREATE TABLE seasons (
season_id BIGINT NOT NULL AUTO_INCREMENT,
program_id BIGINT NOT NULL,
season_number BIGINT NOT NULL,
PRIMARY KEY (season_id),
FOREIGN KEY (program_id) REFERENCES programs (program_id));

-- episodesテーブル作成
CREATE TABLE episodes (
episode_id BIGINT NOT NULL AUTO_INCREMENT,
program_id BIGINT NOT NULL,
season_id BIGINT,
episode_number BIGINT NOT NULL,
episode_name VARCHAR(100) NOT NULL,
episode_detail TEXT NOT NULL,
duration TIME NOT NULL,
release_date DATE NOT NULL,
view_count BIGINT NOT NULL DEFAULT 0,
PRIMARY KEY (episode_id),
FOREIGN KEY (program_id) REFERENCES programs (program_id),
FOREIGN KEY (season_id) REFERENCES seasons (season_id));

-- time_slotsテーブル作成
CREATE TABLE time_slots (
time_slot_id BIGINT NOT NULL AUTO_INCREMENT,
channel_id BIGINT NOT NULL,
program_id BIGINT NOT NULL,
episode_id BIGINT NOT NULL,
start_time DATETIME NOT NULL,
end_time DATETIME NOT NULL,
view_count BIGINT NOT NULL DEFAULT 0,
PRIMARY KEY (time_slot_id),
FOREIGN KEY (channel_id) REFERENCES channels (channel_id),
FOREIGN KEY (program_id) REFERENCES programs (program_id),
FOREIGN KEY (episode_id) REFERENCES episodes (episode_id));
```

### サンプルデータ格納  
```sql
-- channelsテーブル
INSERT INTO channels (channel_name) VALUES
('ニュース'),
('ドラマ1'),
('ドラマ2'),
('アニメ1'),
('アニメ2'),
('映画1'),
('映画2'),
('スポーツ');

-- programsテーブル
INSERT INTO programs (program_name, program_detail) VALUES
('鬼滅の刃', '家族を鬼に殺された少年・竈門炭治郎（かまど・たんじろう）が、鬼になった妹の禰󠄀󠄀豆子（ねずこ）を人間に戻すため、“鬼殺隊”へ入隊することから始まる。'),
('進撃の巨人', '人間を襲う巨大な異形の生物「巨人」との戦いを描くダークファンタジーアクションアニメ。壁に囲まれた都市での生活と、巨人との壮絶な戦いが織り交ぜられたストーリー。'),
('呪術廻戦', '驚異的な身体能力を持つ、少年・虎杖悠仁はごく普通の高校生活を送っていたが、ある日“呪い”に襲われた仲間を救うため、特級呪物“両面宿儺の指”を喰らい、己の魂に呪いを宿してしまう。'),
('正直不動産', '登坂不動産の営業マン・永瀬財地（山下智久）は、口八丁で成績Ｎｏ．１を維持し続けるやり手の営業マン。'),
('VIVANT', '大手商社「丸菱商事」の社員・乃木憂助（堺）が、所属部署で起きた誤送金事件の損失130億円を回収するため、中央アジアのバルカ共和国へ向かうところから物語はスタート。'),
('キングダム 運命の炎', '500年にわたり、七つの国が争い続ける中国春秋戦国時代。戦災孤児の信は、亡き親友と瓜二つの秦の国王・嬴政と出会い、中華統一を目指し、天下の大将軍になる夢に向けて突き進んでいた。'),
('グランツーリスモ', '世界的大ヒットのドライビングゲーム「グランツーリスモ」のプレイに夢中なヤン。'),
('ダイ・ハード', 'ブルース・ウィリス主演、ノンストップ・ハード・アクションの傑作！'),
('NNN ストレイトニュース', '『NNNストレイトニュース』は、2007年10月1日より、地上波NNN加盟フルネット28局 にて放送されている昼のニュース番組。'),
('news every.', '『news every.』は、2010年3月29日から日本テレビおよびNNN系列各局で、平日と土曜、年末年始の夕方に放送されているニュース・情報番組である。'),
('サッカーAFCアジアカップカタール', '4年に1度アジアNo.1決定戦!AFCアジアカップ!'),
('WBC2023', '野球の世界一を決める 国際大会。');

-- genresテーブル
INSERT INTO genres (genre_name) VALUES
('ドラマ'),
('コメディ'),
('アニメ'),
('アクション'),
('ファンタジー'),
('サスペンス'),
('映画'),
('冒険'),
('軍隊/戦争'),
('歴史'),
('スポーツ'),
('ニュース/報道');

-- program_genresテーブル
INSERT INTO program_genres (program_id, genre_id) VALUES
(1, 3),
(1, 4),
(1, 5),
(2, 3),
(2, 4),
(3, 3),
(3, 4),
(4, 1),
(4, 2),
(5, 1),
(5, 8),
(6, 1),
(6, 4),
(6, 7),
(6, 8),
(6, 9),
(6, 10),
(7, 1),
(7, 4),
(7, 7),
(7, 11),
(8, 4),
(8, 6),
(8, 7),
(9, 12),
(10, 12),
(11, 11),
(12, 11);

-- seasonsテーブル
INSERT INTO seasons (program_id, season_number) VALUE
(1, 1),
(1, 2),
(1, 3),
(2, 1),
(2, 2),
(2, 3),
(2, 4),
(3, 1),
(3, 2),
(4, 1),
(5, 1);

-- episodesテーブル
INSERT INTO episodes (program_id, season_id, episode_number, episode_name, episode_detail, duration, release_date, view_count) VALUES
(1, 1, 1, '刃の始まり', '主人公・炭治郎の家族が鬼に襲われる。', '00:23:00', '2021-01-01', 1000000),
(1, 1, 2, '鬼殺隊への道', '炭治郎が鬼殺隊の隊士になるための修行開始。', '00:22:30', '2021-01-08', 1200000),
(1, 2, 1, '遊郭編開始', '炭治郎が次なる任務に挑む。', '00:24:00', '2022-07-01', 1500000),
(1, 2, 2, '新たな仲間', '新たな仲間たちとの出会い。', '00:22:45', '2022-07-08', 1300000),
(1, 3, 1, '刀鍛冶の里編開始', '炭治郎たちと宇髄の活躍により、百年ぶりに上弦の鬼が倒された。', '00:24:00', '2023-04-10', 1100000),
(1, 3, 2, '300年以上前の刀', '霞柱・時透無一郎と里の少年・小鉄が言い争っている場に遭遇した炭治郎。', '00:23:30', '2023-04-17', 900000),
(2, 4, 1, '立身出世', '主人公・エレンが巨人に襲われる。', '00:24:00', '2013-04-06', 2000000),
(2, 4, 2, '新たな同盟', 'エレンと仲間たちが新たな同盟を結成。', '00:23:30', '2013-04-13', 2300000),
(2, 5, 1, '壁の中の秘密', '壁の中に隠された秘密が明らかになる。', '00:25:00', '2017-04-01', 2100000),
(2, 5, 2, '巨人の脅威', '新たな巨人の脅威に立ち向かうエレンたち。', '00:24:15', '2017-04-08', 1900000),
(2, 6, 1, '人類の過去', '人類と巨人の関係についての真実が明らかになる。', '00:25:00', '2018-07-22', 1500000),
(2, 6, 2, '反撃開始', 'エレンたちが反撃を開始する。', '00:24:30', '2018-07-29', 1700000),
(2, 7, 1, '自由の翼', '新たな敵との戦いが始まる。', '00:26:00', '2020-12-07', 3000000),
(2, 7, 2, '秘密の地', '主人公たちが秘密の地を目指す。', '00:24:30', '2020-12-14', 3500000),
(3, 8, 1, '両面宿儺', '並みはずれた身体能力を持つ高校生・虎杖悠仁。', '00:24:00', '2020-10-02', 2000000),
(3, 8, 2, '自分のために', '見知らぬ部屋で目を覚ます虎杖は、目の前にいた呪術高専の教師・五条 悟から自分の“秘匿死刑”が決定したと告げられる。', '00:23:30', '2020-10-09', 2100000),
(3, 9, 1, '懐玉', '行方不明者が多発する曰くつきの洋館の調査に派遣された術師の歌姫と冥冥。', '00:25:00', '2023-07-06', 2400000),
(3, 9, 2, '懐玉－弐－', '天元との適合者である“星漿体”、その少女の「護衛」と「抹消」という任務を課された五条と夏油。', '00:24:30', '2023-07-13', 2600000),
(4, 10, 1, '新たな取引', '不動産業者が新しい取引を開始する。', '00:45:00', '2023-03-01', 800000),
(4, 10, 2, '契約の落とし穴', '契約の際に予測できない問題が発生。', '00:45:30', '2023-03-08', 780000),
(4, 10, 3, '不動産市場の変動', '市場の変動により不動産業者たちが奮闘。', '00:45:00', '2023-03-15', 900000),
(5, 11, 1, '日本から世界へ翔る大冒険が始まる!', '丸菱商事に勤める乃木憂助（堺雅人）は、誤送金された１３０億円を取り戻すべく、送金先であるバルカ共和国へ向かった。', '00:55:00', '2023-07-16', 1200000),
(5, 11, 2, 'お前が別班？まさかな…', '誤送金された140億を取り戻すために中央アジアのバルカ共和国に向かった乃木（堺雅人）だったが、地元警察にテロリストだと誤解されたまま、公安刑事の野崎（阿部寛）と、医師の薫（二階堂ふみ）と日本へ向かうことに。', '01:00:00', '2023-07-23', 1500000),
(6, null, 1, 'キングダム 運命の炎', '500年にわたり、七つの国が争い続ける中国春秋戦国時代。戦災孤児の信は、亡き親友と瓜二つの秦の国王・嬴政と出会い、中華統一を目指し、天下の大将軍になる夢に向けて突き進んでいた。', '02:09:00', '2023-10-01', 4200000),
(7, null, 1, 'グランツーリスモ', '世界的大ヒットのドライビングゲーム「グランツーリスモ」のプレイに夢中なヤン。', '02:14:00', '2023-09-01', 2600000),
(8, null, 1, 'ダイ・ハード', 'ブルース・ウィリス主演、ノンストップ・ハード・アクションの傑作！', '02:12:00', '1988-02-04', 2300000),
(9, null, 1, 'NNN ストレイトニュース', '『NNNストレイトニュース』は、2007年10月1日より、地上波NNN加盟フルネット28局 にて放送されている昼のニュース番組。', '01:00:00', '2007-10-01', 500000),
(10, null, 1, 'news every.', '『news every.』は、2010年3月29日から日本テレビおよびNNN系列各局で、平日と土曜、年末年始の夕方に放送されているニュース・情報番組である。', '01:00:00', '2010-03-29', 670000),
(11, null, 1, 'サッカーAFCアジアカップカタール 日本vsベトナム', '4年に1度アジアNo.1決定戦!AFCアジアカップ! 相手はアジア強豪のベトナム！', '03:20:00', '2024-01-19', 2300000),
(12, null, 1, 'WBC2023', '野球の世界一を決める 国際大。日本vsアメリカ', '02:35:00', '2023-10-01', 4600000);

-- time_slotsテーブル
INSERT INTO time_slots (channel_id, program_id, episode_id, start_time, end_time, view_count) VALUES
(1, 1, 1, '2024-01-20 08:00:00', '2024-01-20 08:30:00', 500000),
(2, 2, 7, '2024-01-20 13:30:00', '2024-01-20 14:00:00', 700000),
(3, 3, 15, '2024-01-20 20:00:00', '2024-01-20 20:30:00', 600000),
(4, 4, 19, '2024-01-20 15:00:00', '2024-01-20 15:45:00', 800000),
(5, 5, 22, '2024-01-20 22:30:00', '2024-01-20 23:30:00', 900000),
(6, 6, 24, '2024-01-20 18:00:00', '2024-01-20 20:00:00', 1000000),
(7, 7, 25, '2024-01-20 19:30:00', '2024-01-20 21:30:00', 1200000),
(8, 8, 26, '2024-01-20 14:00:00', '2024-01-20 16:00:00', 800000),
(1, 9, 27, '2024-01-20 12:00:00', '2024-01-20 12:30:00', 600000),
(2, 10, 28, '2024-01-20 17:30:00', '2024-01-20 18:30:00', 900000),
(3, 11, 29, '2024-01-21 21:00:00', '2024-01-21 22:00:00', 1100000),
(4, 12, 30, '2024-01-21 16:30:00', '2024-01-21 17:30:00', 700000),
(5, 1, 2, '2024-01-21 09:30:00', '2024-01-21 10:00:00', 800000),
(6, 2, 8, '2024-01-21 18:30:00', '2024-01-21 19:00:00', 950000),
(7, 3, 16, '2024-01-21 20:30:00', '2024-01-21 21:00:00', 780000),
(8, 4, 20, '2024-01-21 15:45:00', '2024-01-21 16:30:00', 850000),
(1, 5, 23, '2024-01-21 22:00:00', '2024-01-21 23:00:00', 1200000),
(2, 6, 24, '2024-01-21 20:00:00', '2024-01-21 22:00:00', 1000000),
(3, 7, 25, '2024-01-21 19:00:00', '2024-01-21 21:00:00', 1100000),
(4, 8, 26, '2024-01-21 14:30:00', '2024-01-21 16:30:00', 900000);
```

## データ抽出
**1. よく見られているエピソードを知りたいです。エピソード視聴数トップ3のエピソードタイトルと視聴数を取得してください**
```sql
SELECT episode_name AS 'エピソードタイトル', view_count AS '視聴数' FROM episodes ORDER BY view_count DESC LIMIT 3;
```

**2. よく見られているエピソードの番組情報やシーズン情報も合わせて知りたいです。エピソード視聴数トップ3の番組タイトル、シーズン数、エピソード数、エピソードタイトル、視聴数を取得してください**
```sql
SELECT program_name AS '番組タイトル', season_number AS 'シーズン数', episode_number AS 'エピソード数', episode_name AS 'エピソードタイトル', view_count AS '視聴数' FROM episodes LEFT OUTER JOIN programs ON episodes.program_id = programs.program_id LEFT OUTER JOIN seasons ON episodes.season_id = seasons.season_id ORDER BY view_count DESC LIMIT 3;
```

**3. 本日の番組表を表示するために、本日、どのチャンネルの、何時から、何の番組が放送されるのかを知りたいです。本日放送される全ての番組に対して、チャンネル名、放送開始時刻(日付+時間)、放送終了時刻、シーズン数、エピソード数、エピソードタイトル、エピソード詳細を取得してください。なお、番組の開始時刻が本日のものを本日方法される番組とみなすものとします**
```sql
SELECT channel_name AS 'チャンネル名', start_time AS '放送開始時刻', end_time AS '放送終了時刻', season_number AS 'シーズン数', episode_number AS 'エピソード数', episode_name AS 'エピソードタイトル', episode_detail AS 'エピソード詳細' FROM time_slots INNER JOIN channels ON time_slots.channel_id = channels.channel_id INNER JOIN episodes ON time_slots.episode_id = episodes.episode_id LEFT OUTER JOIN seasons ON episodes.season_id = seasons.season_id WHERE DATE(start_time) = '2024-01-20' ORDER BY time_slot_id;
```

**4. ドラマというチャンネルがあったとして、ドラマのチャンネルの番組表を表示するために、本日から一週間分、何日の何時から何の番組が放送されるのかを知りたいです。ドラマのチャンネルに対して、放送開始時刻、放送終了時刻、シーズン数、エピソード数、エピソードタイトル、エピソード詳細を本日から一週間分取得してください**
```sql
SELECT start_time AS '放送開始時刻', end_time AS '放送終了時刻', season_number AS 'シーズン数', episode_number AS 'エピソード数', episode_name AS 'エピソードタイトル', episode_detail AS 'エピソード詳細' FROM time_slots INNER JOIN channels ON time_slots.channel_id = channels.channel_id INNER JOIN episodes ON time_slots.episode_id = episodes.episode_id LEFT OUTER JOIN seasons ON episodes.season_id = seasons.season_id WHERE channel_name LIKE '%ドラマ%' AND DATE(start_time) BETWEEN '2024-01-15' AND '2024-01-21' ORDER BY start_time;
```

**5. 直近一週間で最も見られた番組が知りたいです。直近一週間に放送された番組の中で、エピソード視聴数合計トップ2の番組に対して、番組タイトル、視聴数を取得してください**
```sql
-- SELECT program_name AS '番組タイトル', episodes.view_count AS '視聴数' FROM time_slots INNER JOIN programs ON time_slots.program_id = programs.program_id INNER JOIN episodes ON time_slots.episode_id = episodes.episode_id WHERE start_time BETWEEN '2024-01-15' AND '2024-01-22' ORDER BY episodes.view_count DESC LIMIT 2;
```

**6. ジャンルごとの番組の視聴数ランキングを知りたいです。番組の視聴数ランキングはエピソードの平均視聴数ランキングとします。ジャンルごとに視聴数トップの番組に対して、ジャンル名、番組タイトル、エピソード平均視聴数を取得してください。**
```sql
```
