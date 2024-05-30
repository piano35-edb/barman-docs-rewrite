The script definition is passed to a shell and can return any exit code.

Only in case of retry hook scripts, the exit code of the script is checked by Barman. Output of hook scripts is written in the log file.

Barman scripts can contain the following variables:

|**Variable**|**Description**|**Script type**|
|------------|---------------|---------------|
|BARMAN_CONFIGURATION|Configuration file used by barman|Shell|
|BARMAN_ERROR|Error message, if any (only for the 'post' phase)|Shell|
|BARMAN_PHASE|'pre' or 'post'|Shell|
|BARMAN_RETRY|1 if it is a retry script (from 1.5.0), 0 if not|Shell|
|BARMAN_SERVER|Name of the server|Shell|
|BARMAN_BACKUP_DIR|Backup destination directory|Backup|
|BARMAN_BACKUP_ID|ID of the backup|Backup|
|BARMAN_PREVIOUS_ID|ID of the previous backup (if present)|Backup|
|BARMAN_NEXT_ID|ID of the next backup (if present)|Backup|
|BARMAN_STATUS|Status of the backup|Backup|
|BARMAN_VERSION|Version of Barman|Backup|
|BARMAN_SEGMENT|Name of the WAL file|Archive|
|BARMAN_FILE|Full path of the WAL file|Archive|
|BARMAN_SIZE|Size of the WAL file|Archive|
|BARMAN_TIMESTAMP|WAL file timestamp|Archive|
|BARMAN_COMPRESSION|Type of compression used for the WAL file|Archive|
|BARMAN_DESTINATION_DIRECTORY|The directory where the new instance is recovered|Recovery|
|BARMAN_TABLESPACES|Tablespace relocation map (JSON, if present)|Recovery|
|BARMAN_REMOTE_COMMAND|Secure shell command used by the recovery (if present)|Recovery|
|BARMAN_RECOVER_OPTIONS|Recovery additional options (JSON, if present)|Recovery|