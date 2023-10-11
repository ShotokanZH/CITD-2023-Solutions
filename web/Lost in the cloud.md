# Lost in the cloud
This challenge is based on a real world scenario that happened to me a couple years ago.

The nginx conf has a "misconfiguration/backdoor":
```nginx
    location ~ /data/ {
        try_files $uri $uri/ =404;
    }
```
This allows access to the `/data/` directory.

In `/data/` as per documentation we can find the `owncloud.db` database (nextdata is a owncloud fork)

Downloading `/data/owncloud.db` we can get the user list by running:
```sql
SELECT uid FROM oc_accounts;
```
...discovering in this way the user `snadolino`.\
Further analyzing the database we can find in several ways the uploaded filenames, and we can search for a flag file:
```sql
SELECT path FROM oc_filecache WHERE path LIKE "%flag%";
```
Flag file name: `notaregularflag.txt` .

Finally we can access the flag by downloading: `/data/snadolino/files/notaregularflag.txt`