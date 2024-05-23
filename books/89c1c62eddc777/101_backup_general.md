---
title: "バックアップスクリプト（一般）"
---

## NAS上へ一方的に転送するバックアップ


### 前提条件

以下のdebパッケージをインストールしておく必要がある。

```bash:terminal
sudo apt -y install zip unzip smbclient cifs-utils
```


### ディレクトリ構成

```
/backup/
├─ bin
│   └ backup_dokuwiki.sh
├─ data
│   ├─ backup_dokuwiki_2023-04-23-06-00-01.zip
... ...
│   └─ backup_dokuwiki_2023-05-13-06-00-01.log
└ log
    ├─ backup_dokuwiki_2023-04-23-06-00-01.zip
    ...
    └─ backup_dokuwiki_2023-05-13-06-00-01.log
```

### スクリプト本体

```bash:terminal
cat /backup/bin/backup_dokuwiki.sh
```

```bash
#!/bin/sh

# 環境変数
DATA_DIR=/var/www/html
BACKUP_DIR=/backup
PERIOD="+30"


# 処理開始
BACKUP_TIME=`date "+%Y-%m-%d-%H-%M-%S"`

cd ${DATA_DIR}
zip -r backup_dokuwiki_${BACKUP_TIME}.zip ./dokuwiki 2>&1 > ${BACKUP_DIR}/log/backup_dokuwiki_${BACKUP_TIME}.log
mv backup_dokuwiki_${BACKUP_TIME}.zip ${BACKUP_DIR}/data

# バックアップファイルの管理(PERIODより古い日数のファイルは削除)
find ${BACKUP_DIR}/data/*.zip -type f -daystart -mtime ${PERIOD} -exec rm {} \;

for filePath in `find ${BACKUP_DIR}/data/*`; do
  # -cでputコマンドを送る
  smbclient '\\10.200.100.1\works001' -U admin%passw0rd -c "put ${filePath}  backup\10_255_0_1\\${filePath##*/}"  >&1
done
```

### crontab設定

```bash:terminal
sudo crontab -l
```

追記対象は以下。

```
0 6 * * * /backup/bin/backup_dokuwiki.sh
```
