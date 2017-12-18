##设置协处理器

alter 't1', METHOD => 'table_att', 'coprocessor'=>'hdfs:///lib/hbase-replicator-v1.3.jar|com.lenovo.cloud.DataSyncObserver|1001|namesrv=mq-namesrv1:9876,topic=hbase-replicator,group=HBASE_CONSUMER_GROUP,tags=hbase'


##卸载协处理器

alter 't1', METHOD => 'table_att_unset', NAME=>'coprocessor$1'


##opencv 截帧

--rootId 5 --appId 90 --targetId 260 --pcUrl http://10.4.65.187:8080 --inputFile /home/color.mp4

alter 'Dl:Image', METHOD => 'table_att', 'coprocessor'=>'hdfs:///lib/hbase-replicator-v1.3.jar|com.lenovo.cloud.DataSyncObserver|1001|namesrv=mq-namesrv1:9876,topic=hbase-replicator,group=HBASE_CONSUMER_GROUP,tags=dl-image'