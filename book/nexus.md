#### 然后重启就可以了
```
gitlab-ctl reconfigure
gitlab-ctl restart
```


### 部署nexus3
chown 200 /data/local/nexus3/nexus-data
```
docker run -tid -p 8081:8081 --name nexus --restart=always -e NEXUS_CONTEXT=nexus -v /data/local/nexus3/nexus-data:/nexus-data  sonatype/nexus3
```
### 部署nexus2
```
docker run -tid -p 8081:8081 --name nexus2 --restart=always -e CONTEXT_PATH="" -v /data/local/nexus2/sonatype-work:/sonatype-work sonatype/nexus
```
#### 手动索引更新nexus2
nexus-maven-repository-index.gz
nexus-maven-repository-index.properties
indexer-cli-X.0.0.jar

https://mvnrepository.com/artifact/org.apache.maven.indexer/indexer-cli
http://repo1.maven.org/maven2/.index/

java -jar indexer-cli-5.1.1.jar -u nexus-maven-repository-index.gz -d indexer

然后indexer内的文件拷贝到私服{nexus-home}/sonatype-work/nexus/indexer/central-ctx目录下，重新启动nexus，索引更新完毕
