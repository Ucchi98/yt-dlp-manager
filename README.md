# yt-dlp-manager

`yt-dlp` と `SQLite3` を使用し、動画ダウンロードタスクを管理する軽量なBash製CLIツールです。複数プロセスの同時アクセスに対応し、IDの範囲指定を用いた柔軟な一括操作が可能です。

### 基本機能
- **`-s` [ID/範囲]**: ダウンロードデーモンを起動。IDや範囲（例: `-s 1:5,10`）を指定した場合は対象のみを処理して終了し、引数なしの場合は最古のタスクから順に永続的に常駐監視を行います。
- **`-v`, `--verbose`**: `yt-dlp` の詳細なデバッグログを表示。`-s` との組み合わせでのみ有効（例: `-s 5 -v` または `-v -s 5`）。
- **`[チャンネル] "[URL群]"`**: 現在のディレクトリ（`$PWD`）をデフォルトの保存先として、新しいURLをキューに登録します。
- **`-p` <保存先> [チャンネル] "[URL群]"**: 保存先の親ディレクトリを任意に指定して、新しいURLを登録します。
- **`-l` [行数/範囲]**: 登録履歴を一覧表示。出力が行数を上回る場合は自動的に `less -S` でスクロール表示します。
- **`-m` <ID/範囲> [変更オプション]**: 指定したIDの保存先（`-p`）、チャンネル名（`-c`）、単一URL（`-u`）を修正。実行中（`Processing`）の行は自動スキップされます。
- **`-d` <ID/範囲>**: 指定したIDのレコードをデータベースから削除。実行中の行は自動スキップされます。
- **`-c` "[URL群]"**: 指定したURLがすでにデータベースに登録されているか重複をチェックします。
- **`Ctrl+C` (即時中断)**: 実行中に中断された場合、即座にプロセスを停止し、該当IDのステータスを `[!] Interrupted`（中断）としてデータベースに記録します。

*※ すべてのID指定項目（`-s`, `-l`, `-m`, `-d`）において、単一ID、カンマ区切り、およびコロンを用いたオープン範囲指定（例: `1:10`, `:20`, `50:`）が共通で利用可能です。*

# yt-dlp-manager

A lightweight Bash CLI tool to manage a video download queue using `yt-dlp` and `SQLite3`. It supports multi-process concurrent access and versatile bulk operations via ID range selections.

### Basic Options
- **`-s` [ID/Range]**: Starts the download daemon. If IDs/ranges are specified (e.g., `-s 1:5,10`), it processes only those tasks and exits. If no arguments are given, it runs persistently, processing from the oldest entry.
- **`-v`, `--verbose`**: Enables detailed `yt-dlp` debugging output. Must be used in combination with `-s` (e.g., `-s 5 -v` or `-v -s 5`).
- **`[Channel] "[URLs]"`**: Registers new URLs using the current directory (`$PWD`) as the default base directory.
- **`-p` <Path> [Channel] "[URLs]"**: Registers new URLs under a custom base directory.
- **`-l` [Lines/Range]**: Displays registration history. Automatically pipes to `less -S` if the output exceeds the terminal height.
- **`-m` <ID/Range> [Modifiers]**: Modifies paths (`-p`), channels (`-c`), or single URLs (`-u`) for the specified entries. Active downloads (`Processing`) are automatically skipped.
- **`-d` <ID/Range>**: Deletes the specified entries from the database. Active downloads are automatically skipped.
- **`-c` "[URLs]"**: Checks if the specified URLs are already registered.
- **`Ctrl+C` (Signal Handling)**: Instantly halts execution and sets the current task status to `[!] Interrupted` in the database.

*Note: All ID options (`-s`, `-l`, `-m`, `-d`) accept a single numerical ID, comma-separated IDs, and colon range formats including open ranges (e.g., `1:10`, `:20`, `50:`).*

