## 安装依赖

```Shell
go get github.com/olivere/elastic/v7(看es版本安装)
```

## 使用

### 新建客户端

```Go
func InitES() {
    client, err := elastic.NewClient(elastic.SetSniff(false), elastic.SetURL("http://101.126.153.13:9200"))
    if err != nil {
       log.Fatal(err.Error())
    }
    global.ES = client
}
```

> 注意使用docker部署需要加上elastic.SetSniff(false)这一参数来禁用嗅探器,因为es默认配置的监听地址是内网的,如果要启用则需要修改配置文件

可用的参数

- `elastic.SetURL(url)`用来设置`ES`服务地址，如果是本地，就是`127.0.0.1:9200`。支持多个地址，用逗号分隔即可。
    
- `elastic.SetBasicAuth("user", "secret")`这个是基于http base auth 验证机制的账号密码。
    
- `elastic.SetGzip(true)`启动`gzip`压缩
    
- `elastic.SetHealthcheckInterval(10*time.Second)`用来设置监控检查时间间隔
    
- `elastic.SetMaxRetries(5)`设置请求失败最大重试次数，v7版本以后已被弃用
    
- `elastic.SetSniff(false)`允许指定弹性是否应该定期检查集群（默认为true）
    
- `elastic.SetErrorLog(log.New(os.Stderr, "ELASTIC ", log.LstdFlags))` 设置错误日志输出
    
- `elastic.SetInfoLog(log.New(os.Stdout, "", log.LstdFlags))` 设置info日志输出
    

### 创建index和mapping(表)

```Go
var mappingTpl = `{
    "mappings":{
       "dynamic": "strict", ----------->这条是用来限制之后新增doc时无法直接创建不存在字段的,新增会直接报错
       "properties":{
          "id":           { "type": "long" },
          "username":       { "type": "keyword" },
          "nickname":          { "type": "text" },
          "phone":         { "type": "keyword" },
          "age":          { "type": "long" },
          "ancestral":      { "type": "text" },
          "identity":         { "type": "text" },
          "update_time":    { "type": "long" },
          "create_time":    { "type": "long" }
          }
       }
    }`
type UserES struct { //务必写好json格式,否则es会识别为新字段
    Id         uint64 `json:"id"`
    Username   string `json:"username"`
    Nickname   string `json:"nickname"`
    Phone      string `json:"phone"`
    Age        uint64 `json:"age"`
    Ancestral  string `json:"ancestral"`
    Identity   string `json:"identity"`
    UpdateTime uint64 `json:"update_time"`
    CreateTime uint64 `json:"create_time"`
}
//多行字符串使用``(反引号),定义mapping结构
func Test() {
    ctx := context.Background()

    exists, err := global.ES.IndexExists("<索引名>").Do(ctx) //检查表是否存在
    if err != nil {
       fmt.Printf("userEs init exist failed err is %s\n", err)
       return
    }

    if !exists { //不存在则新建
       _, err := global.ES.CreateIndex("<索引名>").Body(mappingTpl).Do(ctx) 
       if err != nil {
          fmt.Printf("userEs init failed err is %s\n", err)
          return
       }
    }
}
```

dynamic 是 mapping 的一个很重要的属性 ，用来控制是否动态添加新字段，并接受以下参数：

|   |   |
|---|---|
|参数|说明|
|TRUE|New fields are added to the mapping (default).|
|runtime|New fields are added to the mapping as runtime fields. These fields are not indexed, and are loaded from _source at query time.|
|FALSE|New fields are ignored. These fields will not be indexed or searchable, but will still appear in the _source field of returned hits. These fields will not be added to the mapping, and new fields must be added explicitly.|
|strict|If new fields are detected, an exception is thrown and the document is rejected. New fields must be explicitly added to the mapping.|

- dynamic 为 true 表示动态映射（dynamic mapping）。
true 为缺省值。添加的文档中如果有新增的字段，则 ES 会自动把新的字段添加到映射中。新增的字段可以被索引，也就是这个字段可以被搜索，mapping 同时也被更新。

- dynamic 为 false 表示静态（显式）映射（explicit mapping）。
    

当 ES 察觉到有新增字段时，会写入新字段，但不会索引新字段，即无法通过新字段进行查询。在有些情况下，静态映射依然不够，所以还需要更严谨的策略来进一步做限制。

- dynamic 为 strict 表示精确（严格）映射（strict mapping）。
    

字段需要严格匹配，新增字段写入将会报错。

### 增删改

这里使用的是`bulk`API，`bulk`API允许在单个步骤中进行多次`create`、`index`，`update`、`delete`请求。`bulk` 与其他的请求体格式稍有不同，如下所示：

```Go
{ action: { metadata }}\n
{ request body        }\n
{ action: { metadata }}\n
{ request body        }\n
...
```

这种格式类似一个有效的单行 JSON 文档流 ，它通过换行符(`\n`)连接到一起。注意两个要点：

- 每行一定要以换行符(`\n`)结尾， 包括最后一行 。这些换行符被用作一个标记，可以有效分隔行。
    
- 这些行不能包含未转义的换行符，因为他们将会对解析造成干扰。这意味着这个 JSON 不能使用 pretty 参数打印。
    

`action/metadata` 行指定 _哪一个文档_ 做 _什么操作_ 。

`action` 必须是以下选项之一:

- **`create`**：如果文档不存在，那么就创建它。
    
- **`index`**：创建一个新文档或者替换一个现有的文档。
    
- **`update`**：部分更新一个文档
    
- **`delete`**：删除一个文档
    

> 这些已经被封装好了,无需自己调用,代码示例如下

```Go
func BatchAdd(user []*model.UserES) error {
    var err error
    esRetryLimit := 3
    for i := 0; i < esRetryLimit ; i++ { //这里是失败重试,可以不加
       if err = batchAdd(user); err != nil {
          fmt.Println("batch add failed ", err)
          continue
       }
       return err
    }
    return err
}

func batchAdd(user []*model.UserES) error {
    req := global.ES.Bulk().Index("user") //创建bulk请求
    for _, u := range user {
       u.UpdateTime = uint64(time.Now().UnixNano()) / uint64(time.Millisecond)
       u.CreateTime = uint64(time.Now().UnixNano()) / uint64(time.Millisecond)
       doc := elastic.NewBulkIndexRequest().Id(strconv.FormatUint(u.Id, 10)).Doc(u)
       req.Add(doc)
    } // 为每个对象创建请求并添加到bulk请求中
    if req.NumberOfActions() < 0 {
       return nil
    }
    if _, err := req.Do(context.Background()); err != nil {
       return err
    }
    return nil
}
```

> NewBulkIndexRequest() 对应 index action,其他方法同理

```Go
//改
func batchUpdate(user []*model.UserES) error {
    req := global.ES.Bulk().Index("user") //创建bulk请求
    for _, u := range user {
       u.UpdateTime = uint64(time.Now().UnixNano()) / uint64(time.Millisecond)
       //u.CreateTime = uint64(time.Now().UnixNano()) / uint64(time.Millisecond)
       doc := elastic.NewBulkUpdateRequest().Id(strconv.FormatUint(u.Id, 10)).Doc(u)
       req.Add(doc)
    } // 为每个对象创建请求并添加到bulk请求中
    if req.NumberOfActions() < 0 {
       return nil
    }
    if _, err := req.Do(context.Background()); err != nil {
       return err
    }
    return nil
}
//删
func batchDelete(user []*model.UserES) error {
    req := global.ES.Bulk().Index("user") //创建bulk请求
    for _, u := range user {
       //u.UpdateTime = uint64(time.Now().UnixNano()) / uint64(time.Millisecond)
       //u.CreateTime = uint64(time.Now().UnixNano()) / uint64(time.Millisecond)
       doc := elastic.NewBulkDeleteRequest().Id(strconv.FormatUint(u.Id, 10))
       req.Add(doc)
    } // 为每个对象创建请求并添加到bulk请求中
    if req.NumberOfActions() < 0 {
       return nil
    }
    if _, err := req.Do(context.Background()); err != nil {
       return err
    }
    return nil
}
```

### 查

```Go
func Gets() {
    //通过id查找
    get, err := global.ES.Get().Index("user").Id("2").Do(context.Background())
    if err != nil {
       log.Fatal(err.Error())
    }
    if get.Found { //是否存在
       log.Printf("Got document %s in version %d from index %s, type %s\n", get.Id, get.Version, get.Index, get.Type)
       marshal, err := json.Marshal(get)
       if err != nil {
          log.Fatal(err.Error())
          return
       }
       log.Print(string(marshal))
    }
}

// 搜索
func Query() {
    var res *elastic.SearchResult
    var err error
    //取所有
    res, err = global.ES.Search("user").Do(context.Background())
    printEmployee(res, err)

    //字段相等
    q := elastic.NewQueryStringQuery("username:user2")
    res, err = global.ES.Search("user").Query(q).Do(context.Background())
    if err != nil {
       println(err.Error())
    }
    printEmployee(res, err)

    //条件查询
    //年龄大于-1岁的叫user1的
    boolQ := elastic.NewBoolQuery()
    boolQ.Must(elastic.NewMatchQuery("username", "user1"))
    boolQ.Filter(elastic.NewRangeQuery("age").Gt(-1))
    res, err = global.ES.Search("user").Query(boolQ).Do(context.Background())
    printEmployee(res, err)

}

// 简单分页
func List(size, page int) {
    if size < 0 || page < 1 {
       fmt.Printf("param error")
       return
    }
    res, err := global.ES.Search("user").
       Size(size).
       From((page - 1) * size).
       Do(context.Background())
    printEmployee(res, err)
}

// 打印查询到的Employee
func printEmployee(res *elastic.SearchResult, err error) {
    if err != nil {
       print(err.Error())
       return
    }
    var typ model.UserES
    for _, item := range res.Each(reflect.TypeOf(typ)) { //从搜索结果中取数据的方法
       t := item.(model.UserES)
       fmt.Printf("%#v\n", t)
    }
    println("-----------------------------------------")
}
```

**bool组合查询:**

要同时匹配多个条件和字段时,可以使用bool组合查询

`bool`查询的基本语法结构：

```HTTP
GET /{索引名}/_search
{
  "query": {
    "bool": { // bool查询
      "must": [], // must条件，类似SQL中的and, 代表必须匹配条件
      "must_not": [], // must_not条件，跟must相反，必须不匹配条件
      "should": [] // should条件，类似SQL中or, 代表匹配其中一个条件
    }
  }
}
```

- must条件：类似SQL的and，代表必须匹配的条件。
    
- must_not条件：跟must作用刚好相反，相当于`sql`语句中的 `!=`
    
- should条件：类似SQL中的 or， 只要匹配其中一个条件即可
    

### 模糊查询

es支持三种模糊查询

> 假设库里有张三,张三丰,张飞,三德子,张二丰,孙权,马三丰这几个人名

#### Match(分词匹配检索)

> 可以对查询条件分词，查到更多匹配的内容，结合不同的分词器，可以得到不同的效果

会把搜索内容和库里分好的词进行检索,如搜索张三会去匹配 张 , 三 两个字段(根据分词器的不同可能会分为不同的字段,这里使用默认分词器,即会把中文分成一个一个字),可以匹配成功的可以是 张三,张三丰,张飞,三德子,张二丰,马三丰

#### Wildcard(通配符检索)

类似sql的like模糊匹配,可以使用通配符去匹配,如 张三* 会匹配到张三和张三丰

#### Fuzzy(纠错检索)

使用fuzzy会根据编辑距离来模糊匹配,即会根据你的搜索内容与字段的编辑距离决定是否匹配,如 邓子棋 和 邓紫棋 的编辑距离为2,如果搜索的设置最大编辑距离为2就会匹配出来,让输入条件有容错性,如 张三 可以匹配到 张三(0),张三丰(1),张飞(1),张二丰(2),马三丰(2)

#### 案例

**匹配多个字段:**

```Go
func SelectDrink(query string) (drinks []model.Drink, err error) {
    drinks = make([]model.Drink, 0)
    //err = global.Db.Where("name LIKE ?", "%"+query+"%").Find(&drinks).Error
    fq := elastic.NewMultiMatchQuery(query, "name", "description", "tag"). 
       Fuzziness("AUTO").
       PrefixLength(1).
       MaxExpansions(50)
```

MultiMatch可以同时匹配多个字段,其参数第一个为搜索的内容,其后的任意个是要匹配的字段

Fuzziness()可以设置编辑距离,可以为0,1,2,AUTO

PrefixLength()可以设置前缀长度,用于固定前几个字不被模糊匹配,保证搜索内容的相关性

MaxExpansions()可以设置最大膨胀数,保证性能

**设置高亮:**

```Go
    hl := elastic.NewHighlight().
       PreTags("<span style='color:red'>"). // 高亮开始标签
       PostTags("</span>").                 // 高亮结束标签
       Fields(
          elastic.NewHighlighterField("name"),
          elastic.NewHighlighterField("description"),
          elastic.NewHighlighterField("tag"),
       )

    searchResult, err := global.ES.
       Search().
       Index("drink").
       Query(fq).
       Highlight(hl).
       Do(context.Background())
    if err != nil {
       return
    }

    for _, hit := range searchResult.Hits.Hits {
       var doc model.Drink
       err = json.Unmarshal(hit.Source, &doc)
       if err != nil {
          return
       }
       for field, fragments := range hit.Highlight {
          switch field {
          case "name":
             doc.Name = fragments[0]
          case "description":
             doc.Description = fragments[0]
          case "tag":
             doc.Tag = fragments[0]
          }
       }
       drinks = append(drinks, doc)
    }
    return
}
```

NewHighlight()构建高亮查询条件,使用field制定需要匹配高光的字段,其结果会存在searchResult.Hits.Hits.Highlight中,这是一个map,key为匹配的字段,value为一个字符数组中,第一个元素即为匹配高光后的结果,可以替换原来的文本返回前端,即可显示高光