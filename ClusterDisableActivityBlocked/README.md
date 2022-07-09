# Automatic Unlock

```
mkdir /root/activity_unlock
```

```
ROOT="/root/activity_unlock"
cat << EOF >> $ROOT/cluster_is_blocked.sh
#!/bin/bash
exec="pvecm status"

# 取得執行結果
result=`$exec | grep "Activity blocked"`

# 檢查結果是否存在
if [ -z "$result" ];then
    # 如果是空值，表示沒被鎖住
    echo 0
else
    # 如果不是空值，表示被鎖住了
    echo 1
fi
```

```
ROOT="/root/activity_unlock"

cat << EOF >> cluster_activity_unlock.sh
is_blocked=`$ROOT/cluster_is_blocked.sh`

if [ $is_blocked == 1 ];then
    pvecm expected 1
    service pve-cluster restart

    if [ -f $ROOT/mailtoa.sh ];then
        $ROOT/mailto.sh "cluster_activity_unblocked"
    fi
fi
```

```
chmod +x /root/activity_unlock/*.sh
```


`crontab -e`:
```
0   1  *  *  * /root/pulipuli_scripts/cluster_activity_unblocked.sh
```

