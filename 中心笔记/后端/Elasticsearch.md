## 部署

```Shell
docker run -d \
  --name es7 \
  -e "ES_JAVA_OPTS=-Xms512m -Xmx512m" \
  -e "discovery.type=single-node" \
  -v /usr/local/es7/data:/usr/share/elasticsearch/data \
  -v /usr/local/es7/plugins:/usr/share/elasticsearch/plugins \
  -v /usr/local/es7/config:/usr/share/elasticsearch/config \
  --privileged \
  -p 9200:9200 \
  -p 9300:9300 \
  elasticsearch:7.12.1
```

> ik分词器(es默认只支持英文分词):
> 
> 进入容器执行
> 
> `bin/elasticsearch-plugin install` `https://get.infini.cloud/elasticsearch/analysis-ik/<``es的版本>`
> 
> 可以使用 `docker cp <es容器名>:/usr/share/elasticsearch/<文件/文件夹名> /usr/local/<要复制到的地方>`来复制容器中内容出来再进行挂载,方便修改配置文件

## 分词

搜索引擎的核心是倒排索引，而倒排索引的基础就是分词(term)。所谓分词可以简单理解为将一个完整的句子切割为一个个单词的过程。
![[Pasted image 20250715134529.png]]
ES 的倒排索引即是根据分词后的单词创建，即 我、爱、北京、天安门这4个单词。这也意味着在搜索的时候也只能搜索这4个单词才能命中该文档。

实际上 ES 的分词不仅仅发生在文档创建的时候，也发生在搜索的时候，如下图所示：
![[Pasted image 20250715134537.png]]
**读时分词**发生在用户查询时，ES会即时地对用户输入的关键词进行分词，分词结果只存在内存中，当查询结束时，分词结果也会随即消失。

**写时分词**发生在文档写入时，ES会对文档进行分词后，将结果存入倒排索引，该部分最终会以文件的形式存储于磁盘上，不会因查询结束或者 ES 重启而丢失。

ES 中处理分词的部分被称作分词器(Analyzer)，它决定了分词的规则。ES 自带了很多默认的分词器，比如Standard、Keyword、Whitespace等等，默认是 Standard。当我们在读时或者写时分词时可以指定要使用的分词器。写时分词器需要在 mapping 中指定，而且一经指定就不能再修改，若要修改必须新建索引

读时分词器和写时分词器默认相同(对一个字段来说),也可以自己指定读时分词器

```Bash
POST test/_search 
{
    "query":
        { 
            "match":
                { 
                    "msg":
                        { 
                            "query": "eating", 
                            "analyzer": "english" 
                        } 
                }
        } 
}
```

可以使用如下API查看分词后结果,该 api 的作用就是告诉我在指定索引中使用某一字段存储一段文本时，es 会如何分词。

```HTTP
POST <index名>/_analyze
{   
    "field": "<要查看的字段名>", 
    "text": "<具体值>"
}
```

一个分词器由三部分组成：char filter、tokenizer 和 tokenfilter
![[Pasted image 20250715134553.png]]
# Kibana

> kibana只是es等一系列中间件的可视化工具,并不是必须的

## 部署

```Shell
docker run -d \
--name kibana \
-e ELASTICSEARCH_HOSTS=http://<docker的网络ip>:9200 \
-p 5601:5601 \
kibana:7.12.1
```

> 不设置docker网组不可以使用容器名代替ip地址,可以使用`ip a`指令获取docker的网络ip,docker0的就是

# Canal

> canal可以用于同步mysql和es,需要canal-server和canal-adapter