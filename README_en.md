# yt-dlp-manager

A database-driven CLI tool to manage and automate video download queues using yt-dlp.

```text
yt-dlp-manager(1)               User Commands Manual               yt-dlp-manager(1)

NAME
       yt-dlp-manager - Video download queue manager and automation tool using yt-dlp

SYNOPSIS
       yt-dlp-manager [OPTIONS]
       yt-dlp-manager channel url ...
       yt-dlp-manager -p base_dir channel url ...
       yt-dlp-manager -s [id_range] [-v]
       yt-dlp-manager -m id_range [-p base_dir] [-c channel] [-u url]
       yt-dlp-manager -l [id_range]
       yt-dlp-manager -d id_range
       yt-dlp-manager -c url ...

DESCRIPTION
       yt-dlp-manager is a database-driven CLI utility designed to manage execution
       queues for the video download tool yt-dlp(1). It manages download tasks
       via SQLite and can run in the background as a daemon process.

       The standard way to register a task is to navigate to your desired destination
       directory (base_dir) and execute the command by specifying channel and url
       without any options. In this case, the current working directory from which
       the command is run is automatically treated as the base_dir. This design
       eliminates the need to repeatedly type out long absolute paths.

       Multiple space-separated URLs can be specified for the url argument. If a URL
       contains shell special characters such as '?' or '&', the argument must be
       enclosed in quotes or escaped to prevent misinterpretation by the shell.

       Each registered task consists of the following elements:
       ID         Unique identification number for the task (automatically assigned sequentially from 1)
       Channel    Channel name (used as the sub-directory name inside the Base Dir)
       URL        The source URL of the video
       Base Dir   The absolute path to the parent directory of the download destination

OPTIONS
       -s, --start [id_range]
              Executes tasks.
              If id_range is omitted, it enters daemon mode to sequentially process
              all pending tasks in the queue.
              If id_range is specified, the script terminates immediately after
              processing the specified tasks.

       -p, --path base_dir channel url ...
              Registers a task into the queue by explicitly specifying the base_dir.
              Useful when you want to queue tasks from outside the destination directory.

       -m, --modify id_range [-p base_dir] [-c channel] [-u url]
              Modifies task information for the specified id_range.
              Specifying base_dir, channel, or url overwrites the corresponding
              fields of the target tasks with the new values.

       -l, --list [id_range]
              Displays a list of registered tasks.
              If id_range is omitted, all tasks are listed in ascending order.
              If a single numeric value is specified, that number of most recently
              registered tasks are listed in descending order.

       -d, --delete id_range
              Deletes the tasks within the specified id_range from the queue.

       -c, --check url ...
              Checks whether a task matching the exact specified url has already
              been registered in the queue.

       -v, --verbose
              Enables verbose mode, outputting detailed logs from internal yt-dlp
              executions during task processing.

ID_RANGE SPECIFICATION
       Options requiring an id_range accept the following formats:

       Single ID
              Specifies exactly one target task ID.
              Example: 1

       Comma-separated IDs
              Specifies multiple task IDs individually.
              Example: 1,3,5

       Range Specification
              Specifies a continuous range from a start ID to an end ID.
              Example: 1:10 (From ID 1 to 10 inclusive)

              Omitting the start ID specifies everything from the beginning to the end ID.
              Example: :5  (From the very first task up to ID 5)

              Omitting the end ID specifies everything from the start ID to the end of the queue.
              Example: 10: (From ID 10 to the last task)

       Combinations
              Any of the formats above can be combined freely using commas.
              Example: 1:3,5,:7,10:

EXAMPLES
       Task Registration:
         $ yt-dlp-manager "TechChannel" "https://url1 https://url2"
           Registers two URLs into the queue with Channel set to "TechChannel".
           The Base Dir becomes the current working directory.

         $ yt-dlp-manager -p ~/Downloads/Videos "MusicChannel" "https://url3"
           Registers one URL into the queue with Base Dir explicitly set to
           "~/Downloads/Videos" and Channel set to "MusicChannel".

       Task Listing:
         $ yt-dlp-manager -l
           Displays all currently registered tasks in ascending order.

           Output Example:
           ID  Channel        Status           Created              Finished             ...
           --  -------------  ---------------  -------------------  -------------------
            1  TechChannel    [o] Done         2026-07-18 12:00:00  2026-07-18 12:03:15  ...
            2  TravelChannel  [x] Failed       2026-07-18 12:01:01  -------- --:--:--    ...
            3  GamerChannel   [!] Interrupted  2026-07-18 12:05:00  -------- --:--:--    ...
            4  MusicChannel   [*] Processing   2026-07-18 12:10:00  -------- --:--:--    ...
            5  AnimeChannel   [-] Waiting      2026-07-18 12:15:00  -------- --:--:--    ...

           Each column displays the respective task component along with the following metadata:
           Status     Current processing status of the task
                      [-] Waiting      Pending download
                      [o] Done         Download completed successfully
                      [!] Interrupted  Process interrupted
                      [x] Failed       Download failed
                      [*] Processing   Currently downloading
           Created    Date and time when the task was added to the queue
           Finished   Date and time when the task was completed

         $ yt-dlp-manager -l 10
           Lists the 10 most recently registered tasks in descending order.

         $ yt-dlp-manager -l 1:5
           Filters and displays only the tasks with IDs from 1 to 5.

       Task Execution:
         Note: This command only targets tasks whose Status is "Waiting", "Interrupted", or "Failed".

         $ yt-dlp-manager -s
           Launches in daemon mode to process tasks in the queue sequentially.

         $ yt-dlp-manager -s 1:5
           Executes only the tasks within the ID range of 1 to 5.

         $ yt-dlp-manager -s :5
           Processes tasks sequentially up to ID 5.

         $ yt-dlp-manager -v -s
           Runs tasks in verbose mode, displaying full execution logs from internal
           yt-dlp instances directly in the terminal.

       Modifying Task Information:
         $ yt-dlp-manager -m 1 -u "https://new.url"
           Changes the URL of ID 1 to "https://new.url".

         $ yt-dlp-manager -m 1:5 -c "GamerChannel"
           Changes the Channel of IDs 1 through 5 to "GamerChannel".

         $ yt-dlp-manager -m 10: -p ~/NewDir
           Changes the Base Dir of tasks from ID 10 to the end of the queue to "~/NewDir".

       Deleting Tasks:
         $ yt-dlp-manager -d 1
           Removes the task with ID 1 from the queue.

         $ yt-dlp-manager -d 1:3,5,10:12
           Deletes tasks within the specified ID range all at once.

       Duplicate Checking:
         $ yt-dlp-manager -c "https://url1"
           Checks if a task with the URL "https://url1" is already registered in the database.

FILES
       ~/.config/yt-dlp-manager/yt-dlp-manager.cfg
              Configuration file for yt-dlp-manager.

       ~/.config/yt-dlp-manager/queue.db
              SQLite database for task management queue.

       ~/.config/yt-dlp-manager/archive.txt
              Download history archive file used internally by yt-dlp(1) to prevent duplicate downloads.

       ~/.config/yt-dlp-manager/cookie.txt
              Cookie file used internally by yt-dlp(1) to bypass authentication on video platforms.

SEE ALSO
       yt-dlp(1)

AUTHOR
       Ucchi (@Ucchi98)

LICENSE
       MIT License

FreeBSD                      July 18, 2026                   yt-dlp-manager(1)
```
