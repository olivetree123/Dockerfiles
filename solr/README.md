# 介绍
基于 [Solr:Alpine](https://hub.docker.com/_/solr)，添加了对 MySQL 数据导入的支持。
## RUN
```
mkdir /data/solr
chown 8983:8983 /data/solr
docker run -d --name=solr -v /data/solr:/data/solr -p 8983:8983 -e SOLR_HOME=/data/solr -e INIT_SOLR_HOME=yes solr:my
```
其他运行方式和参数可以参考[官方文档](https://hub.docker.com/_/solr)

## 配置
默认情况下，只能按字段进行搜索，如果需要搜索所有字段，需要如下配置：
```
# managed-schema 文件
<field name="text" type="text_general" indexed="true"  stored="false" multiValued="true"/>
<copyField source="title" dest="text"/>
<copyField source="author" dest="text"/>
<copyField source="content" dest="text"/>
```

## 使用
DataImportHandler API 可以参考 [DataImportHandler 文档](https://lucene.apache.org/solr/guide/6_6/uploading-structured-data-store-data-with-the-data-import-handler.html#dataimporthandler-commands)
```
# 查询
import pysolr

solr = pysolr.Solr('http://class.h3c.com:8983/solr/Zeus/', timeout=10)
results = solr.search("text:文章")
print([r for r in results])

# 全量更新
curl "http://127.0.0.1:8983/solr/Zeus/dataimport/?command=full-import"

# 增量更新
curl "http://127.0.0.1:8983/solr/Zeus/dataimport/?command=delta-import"
```