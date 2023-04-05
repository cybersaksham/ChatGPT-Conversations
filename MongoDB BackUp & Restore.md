## How to take BackUp of MongoDB database and restore it again

<details>

<summary>Write shell script for mongodb backup</summary>

Here's an example shell script that can be used to backup a MongoDB database:

```bash
#!/bin/bash

# Set variables for the script
MONGO_DATABASE="your_database_name"
APP_NAME="your_app_name"
MONGO_HOST="localhost"
MONGO_PORT="27017"
TIMESTAMP=`date +%F-%H%M%S`
BACKUP_DIR="/path/to/backup/directory"
BACKUP_NAME="$APP_NAME-$MONGO_DATABASE-$TIMESTAMP"

# Create backup directory if it doesn't exist
mkdir -p $BACKUP_DIR

# Dump the database to a backup file
mongodump --host $MONGO_HOST --port $MONGO_PORT --db $MONGO_DATABASE --out $BACKUP_DIR/$BACKUP_NAME

# Compress the backup file
tar -zcvf $BACKUP_DIR/$BACKUP_NAME.tar.gz $BACKUP_DIR/$BACKUP_NAME

# Remove the uncompressed backup file
rm -rf $BACKUP_DIR/$BACKUP_NAME

# Print a message to confirm the backup has completed
echo "Backup of database '$MONGO_DATABASE' completed and saved as $BACKUP_NAME.tar.gz in $BACKUP_DIR"
```

Here's a breakdown of what this script does:

1.  Set variables for the database name, application name, host, port, timestamp, backup directory, and backup name.
2.  Create the backup directory if it doesn't already exist.
3.  Use the `mongodump` command to dump the database to a backup file with the specified name and location.
4.  Compress the backup file using the `tar` command.
5.  Remove the uncompressed backup file.
6.  Print a message to confirm that the backup has completed.

Note that this script assumes that you have MongoDB and the `mongodump` tool installed on your system. You may need to modify the variables in the script to match your specific configuration.

</details>

<details>

<summary>Write another script for restore the database</summary>

Sure! Here's an example shell script that can be used to restore a MongoDB database from a backup:

```bash
#!/bin/bash

# Set variables for the script
MONGO_DATABASE="your_database_name"
MONGO_HOST="localhost"
MONGO_PORT="27017"
BACKUP_DIR="/path/to/backup/directory"
BACKUP_NAME="your_app_name-your_database_name-backup.tar.gz"

# Extract the backup file
tar -zxvf $BACKUP_DIR/$BACKUP_NAME -C $BACKUP_DIR

# Restore the database from the backup
mongorestore --host $MONGO_HOST --port $MONGO_PORT --db $MONGO_DATABASE $BACKUP_DIR/$BACKUP_NAME/$MONGO_DATABASE

# Remove the uncompressed backup file
rm -rf $BACKUP_DIR/$BACKUP_NAME

# Print a message to confirm the restore has completed
echo "Restore of database '$MONGO_DATABASE' from backup '$BACKUP_NAME' completed"
```

Here's a breakdown of what this script does:

1.  Set variables for the database name, host, port, backup directory, and backup name.
2.  Extract the backup file using the `tar` command.
3.  Use the `mongorestore` command to restore the database from the backup.
4.  Remove the uncompressed backup directory.
5.  Print a message to confirm that the restore has completed.

Note that this script assumes that the backup file is in the specified directory and has the correct naming convention. You may need to modify the variables in the script to match your specific backup file and configuration.

</details>
