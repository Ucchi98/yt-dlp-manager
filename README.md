```text
yt-dlp-manager(1)             ユーザーコマンドマニュアル             yt-dlp-manager(1)

名称
-       yt-dlp-manager - yt-dlpを用いた動画管理・ダウンロード自動化ツール
+       yt-dlp-manager - yt-dlpを用いたタスク管理・実行自動化ツール

書式 (SYNOPSIS)
       yt-dlp-manager [OPTIONS]
       yt-dlp-manager channel url ...
       yt-dlp-manager -p base_dir channel url ...
       yt-dlp-manager -s [id_range] [-v]
       yt-dlp-manager -m id_range [-p base_dir] [-c channel] [-u url]
       yt-dlp-manager -l [id_range]
       yt-dlp-manager -d id_range
       yt-dlp-manager -c url ...

解説 (DESCRIPTION)
       yt-dlp-managerは、動画ダウンロードツールyt-dlp(1)の実行キューを管理
       するためのデータベース駆動型CLIツールである。SQLiteを使用してダウン
-       ロードタスクを管理し、バックグラウンドでデーモンとして動作させるこ
-       とが可能である。
+       ロードタスクを管理し、常駐モードとして動作させることが可能である。

-       標準的なタスク的登録方法は、保存先としたいディレクトリ（base_dir）
-       に移動し、オプションを付けずに channel と url を指定して実行する
-       方法である。このとき、コマンドを実行したカレントディレクトリが自動
-       的に base_dir として処理される。保存先ディレクトリを指定する手間を
-       省略するための仕様である。
+       標準的なタスクの登録方法は、保存先としたいディレクトリ（base_dir）
+       に移動し、オプションを付けずに channel と url を指定して実行する
+       方法である。このとき、コマンドを実行したディレクトリが自動的に
+       base_dir として登録される。

-       引数 url にには、スペース区切りで複数のURLを指定できる。URLに '?' や
-       '&' などのシェル特殊文字が含まれる場合は、シェルによる誤解釈を防ぐ
-       ため、必ず引数をクォーテーションで囲むか、エスケープする必要がある。
+       引数 url は、引数全体を1つのクォーテーションで囲んで記述する。
+       複数の URL を指定する場合は、スペースで区切る。

-       登録された各タスクには、管理用の識別番号として「ID」（1からの連番）
-       が自動的に割り振られる。
-
       登録された各タスクは、以下の要素で構成される。
       ID         タスクの識別番号（1からの連番が自動的に割り振られる）
       Channel    チャンネル名（保存先ディレクトリ名として使用される）
       URL        動画のURL
       Base Dir   保存先ディレクトリの親ディレクトリとなる絶対パス

オプション (OPTIONS)
       -s, --start [id_range]
              タスクを実行する。
-              id_range を省略した場合、キュー内の待機中のタスクを順次処理
-              するデーモンモードとなる。
+              id_range を省略した場合、キュー内の待機中のタスクを順次処理
+              する常駐モードとなる。
              id_range を指定した場合、対象タスクの処理が完了した時点で
              スクリプトは終了する。

       -p, --path base_dir channel url ...
-              base_dir を明示的に指定してタスクをキューに登録する。
-              カレントディレクトリ以外の場所からタスクを登録したい場合に
-              使用する。
+              base_dir を指定してタスクをキューに登録する。

       -m, --modify id_range [-p base_dir] [-c channel] [-u url]
              指定した id_range のタスク情報を変更する。
-              引数として base_dir, channel, url を明示的に指定することに
-              より、対象タスクの該当項目をそれぞれ新しい内容に書き換える。
+              引数として base_dir, channel, url を指定することにより、
+              対象タスクのBase Dir, Channel, URLを新しい内容に書き換える。

       -l, --list [id_range]
              登録されているタスクの一覧を表示する。
              id_range を省略した場合は、すべてのタスクを昇順で表示する。
              id_range に単一の数値を指定した場合は、
              直近で登録されたタスクを指定された件数分だけ降順で表示する。

       -d, --delete id_range
              指定した id_range のタスクをキューから削除する。

       -c, --check url ...
              指定された url と一致するタスクが既に登録されていないか
-              確認する。
+              確認する。複数の url を指定することも可能である。

       -v, --verbose
              デバッグモードを有効にし、タスク実行時における yt-dlp の
              詳細なログを出力する。

id_range の指定方法 (ID_RANGE SPECIFICATION)
       id_range を要求するオプションでは、以下の形式が利用可能である。

       単一指定
              特定のタスクIDを1つだけ指定する。
              例: 1

       コンマ区切り
              複数のタスクIDを個別に指定する。
              例: 1,3,5

       範囲指定
              開始IDから終了IDまでの連続した範囲を指定する。
              例: 1:10（ID 1から10まで）

              開始IDを省略した場合、先頭から終了IDまでの範囲を指定する。
              例: :5 （先頭からID 5まで）

              終了IDを省略した場合、開始IDから末尾までの範囲を指定する。
              例: 10:（ID 10から末尾まで）

       組み合わせ
              上記のすべての形式を、コンマで自由に連結して指定する。
              例: 1:3,5,:7,10:

例 (EXAMPLES)
       タスクの登録:
         $ yt-dlp-manager "TechChannel" "https://url1 https://url2"
           Channel を「TechChannel」として、URL を2つキューに登録する。
-           Base Dir はカレントディレクトリとなる。
+           Base Dir はコマンドを実行したディレクトリとなる。

         $ yt-dlp-manager -p ~/Downloads/Videos "MusicChannel" "https://url3"
-           Base Dir を「~/Downloads/Videos」、Channel を「MusicChannel」
-           として、URL を1つキューに登録する。
+           Channel を「MusicChannel」として、URL を1つキューに登録する。
+           Base Dir は「~/Downloads/Videos」となる。

       タスクの一覧表示:
         $ yt-dlp-manager -l
           現在登録されている全タスクを昇順で表示する。

           表示例:
           ID  Channel        Status           Created              Finished             ...
           --  -------------  ---------------  -------------------  -------------------
            1  TechChannel    [o] Done         2026-07-18 12:00:00  2026-07-18 12:03:15  ...
            2  TravelChannel  [x] Failed       2026-07-18 12:01:01  -------- --:--:--    ...
            3  GamerChannel   [!] Interrupted  2026-07-18 12:05:00  -------- --:--:--    ...
            4  MusicChannel   [*] Processing   2026-07-18 12:10:00  -------- --:--:--    ...
            5  AnimeChannel   [-] Waiting      2026-07-18 12:15:00  -------- --:--:--    ...

           各カラムにはタスクの各構成要素、および以下の情報が表示される。
           Status     タスクの処理状況
                      [-] Waiting      待機中
                      [o] Done         完了
                      [!] Interrupted  中断
                      [x] Failed       失敗
                      [*] Processing   処理中
           Created    タスクがキューに登録された日時
           Finished   タスクが完了した日時

         $ yt-dlp-manager -l 10
           直近で登録された10件のタスクを降順で表示する。

         $ yt-dlp-manager -l 1:5
           ID が 1 から 5 までのタスクのみを抽出して表示する。

-       タスクの実行 (EXECUTION):
+       タスクの実行:
         注: 以下のタスクが処理の対象となる。
             Status: Waiting / Interrupted / Failed

         $ yt-dlp-manager -s
-           デーモンモードとして起動し、キュー内のタスクを順次処理する。
+           キュー内のタスクを順次処理する常駐モードを開始する。

         $ yt-dlp-manager -s 1:5
           ID が 1 から 5 の範囲内にあるタスクのみを実行する。

         $ yt-dlp-manager -s :5
           ID が 5 までのタスクを順次処理する。

         $ yt-dlp-manager -v -s
           デバッグモードでタスクを実行し、yt-dlp 内部のコマンド
           実行ログをターミナルに詳細表示する。

       タスク情報の修正:
         $ yt-dlp-manager -m 1 -u "https://new.url"
           ID 1 の URL を「https://new.url」に変更する。

         $ yt-dlp-manager -m 1:5 -c "GamerChannel"
           ID 1 から 5 の Channel を「GamerChannel」に変更する。

         $ yt-dlp-manager -m 10: -p ~/NewDir
           ID 10 から末尾までのタスクの Base Dir を「~/NewDir」に変更する。

       タスクの削除:
         $ yt-dlp-manager -d 1
           ID 1 のタスクをキューから削除する。

         $ yt-dlp-manager -d 1:3,5,10:12
           指定された ID 範囲のタスクを一括削除する。

-       重複・状況確認:
+       タスクの重複確認:
-         $ yt-dlp-manager -c "https://url1"
-           指定した URL の登録状況（Status や Base Dir 等）をデータベース
-           から検索して表示する。
+         $ yt-dlp-manager -c "https://url1 https://url2"
+           「https://url1」および「https://url2」の URL を持つタスクが
+           既に登録されているか確認する。

ファイル (FILES)
       ~/.config/yt-dlp-manager/yt-dlp-manager.cfg
              yt-dlp-manager の設定ファイル。

       ~/.config/yt-dlp-manager/queue.db
              タスク管理用SQLiteデータベース。

       ~/.config/yt-dlp-manager/archive.txt
              内部の yt-dlp(1) が使用するダウンロード済み動画のID記録ファイル。

       ~/.config/yt-dlp-manager/cookie.txt
              内部の yt-dlp(1) が使用するクッキー（Cookie）ファイル。

関連項目 (SEE ALSO)
       yt-dlp(1)

著者 (AUTHOR)
       Ucchi (@Ucchi98)

ライセンス (LICENSE)
       MIT License

FreeBSD                      July 18, 2026                   yt-dlp-manager(1)
```
