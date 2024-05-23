---
title: "バックアップスクリプト"
---

## 前提条件

以下のdebパッケージをインストールしておく必要がある。

```bash:terminal
sudo apt -y install zip unzip smbclient cifs-utils
```


### ディレクトリ構成

```
/backup/
├─ bin
│   └ backup_gitlab.sh
├─ data
│   ├─ backup_gitlab_2023-04-23-06-00-01.zip
... ...
│   └─ backup_gitlab_2023-05-13-06-00-01.log
└ log
    ├─ backup_gitlab_2023-04-23-06-00-01.zip
    ...
    └─ backup_gitlab_2023-05-13-06-00-01.log
```

### スクリプト本体

```bash:terminal
cat /backup/bin/backup_gitlab.sh
```

```bash
#!/bin/sh

# 環境変数
DATA_DIR=/var/opt/gitlab/backups
ETC_DIR=/etc/gitlab
BACKUP_DIR=/backup
PERIOD="+5"

# 事前処理（バージョン情報などの転記）
BACKUP_TIME=`date "+%Y-%m-%d-%H-%M-%S"`

gitlab-rake gitlab:env:info  2>&1 > ${BACKUP_DIR}/log/backup_gitlab_${BACKUP_TIME}.log
echo ""   2>&1 >> ${BACKUP_DIR}/log/backup_gitlab_${BACKUP_TIME}.log
echo "----------------------------------------------------------------" 2>&1 >> ${BACKUP_DIR}/log/backup_gitlab_${BACKUP_TIME}.log
echo ""   2>&1 >> ${BACKUP_DIR}/log/backup_gitlab_${BACKUP_TIME}.log


## 処理開始

# データバックアップ
gitlab-rake gitlab:backup:create  2>&1 >> ${BACKUP_DIR}/log/backup_gitlab_${BACKUP_TIME}.log
echo "" 2>&1 >> ${BACKUP_DIR}/log/backup_gitlab_${BACKUP_TIME}.log
echo "----------------------------------------------------------------" 2>&1 >> ${BACKUP_DIR}/log/backup_gitlab_${BACKUP_TIME}.log
echo "" 2>&1 >> ${BACKUP_DIR}/log/backup_gitlab_${BACKUP_TIME}.log

# /etc/gitlab配下のバックアップ
cd ${BACKUP_DIR}/data
zip -r backup_gitlab_${BACKUP_TIME}.zip ${ETC_DIR} 2>&1 >> ${BACKUP_DIR}/log/backup_gitlab_${BACKUP_TIME}.log


## データ部分の転送処理

# バックアップファイルの管理(PERIODより古い日数のファイルは削除)
find ${DATA_DIR}/*.tar -type f -daystart -mtime ${PERIOD} -exec rm {} \;

for filePath in `find ${DATA_DIR}/*`; do
  # -cでputコマンドを送る
  smbclient '\\10.200.100.1\works001' -U admin%passw0rd -c "put ${filePath}  backup\10_255_1_1\\${filePath##*/}"   >&1
done

## etc配下の転送処理

# バックアップファイルの管理(PERIODより古い日数のファイルは削除)
find ${BACKUP_DIR}/data/*.zip -type f -daystart -mtime ${PERIOD} -exec rm {} \;

for filePath in `find ${BACKUP_DIR}/data/*`; do
  # -cでputコマンドを送る
  smbclient '\\10.200.100.1\works001' -U admin%passw0rd -c "put ${filePath}  backup\10_255_1_1\\${filePath##*/}"   >&1
done


## ログ部分の転送処理

find ${BACKUP_DIR}/log/*.log -type f -daystart -mtime ${PERIOD} -exec rm {} \;

for filePath in `find ${BACKUP_DIR}/log/*`; do
  # -cでputコマンドを送る
  smbclient '\\10.200.100.1\works001' -U admin%passw0rd -c "put ${filePath}  backup\10_255_1_1\\${filePath##*/}"   >&1
done
```


### crontab設定

```bash:terminal
sudo crontab -l
```

追記対象は以下。

```
0 6 * * * /backup/bin/backup_gitlab.sh
```
