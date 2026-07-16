# yt-dlp-manager

`yt-dlp` と `SQLite3` を組み合わせ、動画のダウンロードキュー（処理待ち行列）を効率的かつ安全に一括管理するために構築された、軽量で堅牢なBash製CLI自動化ツールです。バックグラウンドで永続的に稼働するデーモンモードを主軸とし、ターミナルからシームレスにタスクのメンテナンスを行えます。

### 主な機能
- **デーモン常駐モード (`-s`)**: データベースの待ち行列を24時間監視し、最も古い未処理タスクから順番に全自動ダウンロード。特定のID（範囲）のみを指定したピンポイント駆動も可能。
- **複数起動・並列処理対応 (WALモード)**: SQLite3のWAL（Write-Ahead Logging）機能と5秒のビジータイムアウトを直列指定。複数プロセスから同時に書き込み・読み込みが発生しても、データの破損や競合（二重ダウンロード）を完璧に防止。
- **高度な複数範囲指定 (`-m`, `-d`, `-l`)**: カンマ区切りだけでなく、コロンを用いたオープン/クローズド範囲指定（例：`:10`, `14:30`, `50:`）に対応。履歴確認、チャンネル名や保存先の一括修正、一括削除が直感的に実行可能。
- **即時中断とレジューム（途中再開）**: `Ctrl+C` による強制終了時、通信を即時遮断しつつステータスを `[!] Interrupted` として安全にDBに記録。`.part` ファイルを確実にその場に残すため、次回リトライ時にギガを消費せず続きから再開可能。
- **無駄なログの非表示化**: 範囲指定での一括操作時、すでに削除済みの存在しないIDは Notice 警告を出さずに静かに自動スキップし、ターミナルがログで埋まるのを防ぐクリーンな設計。
- **画面幅に応じたless連動機能**: 履歴確認（`-l`）の際、データが端末の画面の高さを超える場合のみ自動で `less -S`（折り返しなしスクロール）へパイプを繋ぎ、`q` キーでいつでも快適に終了可能。

# yt-dlp-manager

A lightweight, robust, and concurrent-safe CLI automation tool written in Bash to manage a video download queue with `yt-dlp` and `SQLite3`. It runs as a persistent background daemon while providing powerful maintenance features via terminal commands.

### Key Features
- **Daemon Mode (`-s`)**: Continuously monitors the database queue and downloads unique URLs sequentially based on oldest entries first. Supports pinpoint run ranges.
- **Concurrent-Safe (WAL Mode)**: Utilizes SQLite3's Write-Ahead Logging (WAL) and `busy_timeout` to ensure perfect data integrity and prevent race conditions when running multiple instances.
- **Smart Range Operations (`-m`, `-d`, `-l`)**: Supports single ID, comma-separated IDs, and open/closed colon range selections (e.g., `:10`, `15:30`, `50:`) for viewing, modifying paths/channels, or bulk deleting entries.
- **Immediate Interruption & Resume**: Captures `Ctrl+C` / termination signals to instantly save the task state as `[!] Interrupted` without losing `.part` files, allowing seamless resumption without wasting bandwidth.
- **No-Notice Filtering**: Automatically and silently skips non-existent records during bulk operations to prevent terminal log spam.
- **Smart Pager Integration**: Automatically pipes output to `less -S` when the history logs exceed the terminal height for comfortable horizontal and vertical scrolling.

