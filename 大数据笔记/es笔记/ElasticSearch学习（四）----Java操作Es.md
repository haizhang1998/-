## ElasticSearch学习（四）----Java操作Es

使用JAVA客户端管理ES

1. 创建索引库
   * 创建JAVA项目----》添加jar，maven-----》编写测试方法实现创建索引库
   * 创建Settings对象，Settings.builder().put("cluster.name","elasticSearch集群的名字").build
   * 创建一个客户端对象（TransportClient）
   * 使用客户端对象创建一个索引库
   * 关闭索引库

```java
//创建一个Settings对象
Settings settings = Settings.builder().put("cluster.name","my-elasticsearch").build();
//传概念一个客户端Client对象，这里把集群的所有ip：port添加进来
TransportClient client = new PreBuiltTransportClient(settings).
    addTransportAddress(new InetSocketTransportAddress(InetAddress.getName("127.0.0.1"),9301).
    addTransportAddress(new InetSocketTransportAddress(InetAddress.getName("127.0.0.1"),9302).
    addTransportAddress(new InetSocketTransportAddress(InetAddress.getName("127.0.0.1"),9303)
    //使用client对象创建一个索引，get()就是执行操作
   client.admin().indices().prepareCreate("index_hello")
                        .get()
    //关闭client对象
   client.close();
```

2. 创建mappings
   * 创建Settings对象
   * 创建一个客户端对象（TransportClient）
   * 创建mapping信息，应该是一个json数据格式（可用字符串，也可用XContentBuilder构建）。
   * 使用client像es服务器发送mapping数据
   * 关闭连接

```java
//创建一个Settings对象
Settings settings = Settings.builder().put("cluster.name","my-elasticsearch").build();
//传概念一个客户端Client对象，这里把集群的所有ip：port添加进来
TransportClient client = new PreBuiltTransportClient(settings).
    addTransportAddress(new InetSocketTransportAddress(InetAddress.getName("127.0.0.1"),9301).
    addTransportAddress(new InetSocketTransportAddress(InetAddress.getName("127.0.0.1"),9302).
    addTransportAddress(new InetSocketTransportAddress(InetAddress.getName("127.0.0.1"),9303)
  
 //客户端构建json的方式，startObejct()相当于{ 。endObject相当于 }
 XContentBuilder b = XContentFactory.jsonBuilder().
        startObject()
             .startObject("article")
                    .startObject("properties")
                         .startObject("id")
                        		.field("type","long")
                        		.field("store",true)
                          .endObject()
                         .startObject("title")
                        		.field("type","text")
                        		.field("store",true)
                                .field("analyzer","ik_smart")
                          .endObject()
                     .endObject()
               .endObject()
          .endObject();         
    
  //使用client把mapping信息设置到索引库中
  client.admin().indices()
          //设置要做映射的索引
         .preparePutMapping("index_hello")
          //设置要做映射的type
         .setType("article")
          //mapping信息，可以是XContentBuilder对象也可以是json格式的字符串
         .setSource(builder)
          //执行
          .get();
  client.close();
```

3. 向索引库添加文档
   - 创建Settings对象
   - 创建一个客户端对象（TransportClient）
   - 创建文档对象，创建一个json格式的字符串或XContentBuilder构建json
   - 使用CLient对象把文档添加到索引库中
   - 关闭client

```java
//创建settings对象，以及客户端略去，同上面两部（这里用client对象表示）
 XContentBuilder builder = XContentFactory.jsonBuilder().
    startObject()
      .startObject("article")
         .field("id",1L)
         .filed("title"，"myTitle")
      .endObject()
    client
       //设置索引名称，也可以prepareIndex().setIndex("index_hello")
      .prepareIndex("index_hello")
       //设置类型
      .setType("article")
      //设置文档id
      .setId("1")
      //设置 XContentBuilder对象
      .setSource(builder)
      .get();
```

4. 使用Jackson创建文档

   1. 创建一个javabean设置属性

      ```java
       public class Article {
          private String title;
          private long id ;
      
          public String getTitle() {
              return title;
          }
      
          public void setTitle(String title) {
              this.title = title;
          }
      
          public long getId() {
              return id;
          }
      
          public void setId(long id) {
              this.id = id;
          }
      }
      
      ```

   2. 创建一个方法将bean导入到es中存储
   
    ```java
   //initClient对连接对象进行了初始化封装。 
   public static void inserData() throws JsonProcessingException {
           initClient();
           Article article = new Article();
           article.setId(312124L);
           article.setTitle("想你的夜");
           ObjectMapper objectMapper = new ObjectMapper();
           String jsonStr = objectMapper.writeValueAsString(article);
           System.out.println(jsonStr);
           transportClient.prepareIndex().setIndex("index_hello")
                   //设置类型
                   .setType("article")
                   //设置文档id
                   .setId("1")
                   //设置 XContentBuilder对象
                   .setSource(jsonStr)
                   .get();
   
       }
    ```
   
   



#### 搜索

1. 根据id搜索

   * 创建一个Client对象
   * 创建一个查询对象，QeuryBuilders工具类可以创建QueryBuilder对象
   * 使用client执行查询，查询之前要设置参数（哪个索引哪个type哪个字段等）
   * 得到查询结果
   * 默认返回10条这个是es的默认分页。
   * 可以取到查询结果的总记录数
   * 取查询结果列表
   * 关闭client

   ```java
       public static void queryDataByID(){
           initClient();
           QueryBuilder queryBuilder = QueryBuilders.idsQuery().addIds("1","2","14","19");
           SearchResponse searchResponse = transportClient
                   .prepareSearch("index_hello").setTypes("article").setQuery(queryBuilder).get();
           //查询结果
           SearchHits searchHits = searchResponse.getHits();
           int totalHits = (int) searchHits.totalHits;
           SearchHit[] hits = searchHits.getHits();
           float maxScore = searchHits.getMaxScore();
           System.out.println("总命中数:"+totalHits+" 最大得分:"+maxScore);
           Iterator<SearchHit> iterator = searchHits.iterator();
           while(iterator.hasNext()){
               SearchHit hit = iterator.next();
               System.out.println(hit.getSourceAsString());
               System.out.println("===属性===");
               Map<String, Object> source = hit.getSource();
               System.out.println("id :"+source.get("id"));
               System.out.println("title :"+source.get("title"));
           }
           transportClient.close();
       }
   ```

   

2. 根据term查询

   ```java
   //通用方法
     public static void search(QueryBuilder queryBuilder){
           initClient();
           SearchResponse searchResponse = transportClient
                   .prepareSearch("index_hello").setTypes("article").setQuery(queryBuilder).get();
           //查询结果
           SearchHits searchHits = searchResponse.getHits();
           int totalHits = (int) searchHits.totalHits;
           SearchHit[] hits = searchHits.getHits();
           float maxScore = searchHits.getMaxScore();
           System.out.println("总命中数:"+totalHits+" 最大得分:"+maxScore);
           Iterator<SearchHit> iterator = searchHits.iterator();
           while(iterator.hasNext()){
               SearchHit hit = iterator.next();
               //获取原文档（_source）中存放的内容
               System.out.println(hit.getSourceAsString());
               System.out.println("===属性===");
               Map<String, Object> source = hit.getSource();
               System.out.println("id :"+source.get("id"));
               System.out.println("title :"+source.get("title"));
           }
           transportClient.close();
       }
   
   //关键字查询
   public static void queryDataByTerm(){
           QueryBuilder queryBuilder = QueryBuilders.termQuery("title","想");
           search(queryBuilder);
   
       }
   ```

3. QueryString查询方式（待分析的查询）

```java
 //search同上略去
    public static void queryDataByQueryString(){
        QueryBuilder queryBuilder = QueryBuilders.queryStringQuery("想你夜晚").defaultField("title");
        search(queryBuilder);
    }
```

4. 分页处理

   在Client对象执行查询之前设置分页信息，然后再执行查询。

   ```java
    SearchResponse searchResponse = transportClient
                   .prepareSearch("index_hello").setTypes("article").setQuery(queryBuilder).setFrom(0).setSize(5).get();
   ```

   分页需要设置两个值，一个from，一个size。

   from:其实的行号。从0开始

   size:每页显示的记录数目

5. **查询结果高亮显示**

   比如我们去百度搜索关键字，所有文档匹配到关键字的都会变红，这就是高亮希纳是的功能了

1. 高亮的配置
   1. 指定高亮显示的字段/域（位置）
   2. 设置高亮显示的前缀，比如<em></em>
   3. 设置高亮显示的后缀

   2.在client对象执行查询之前设置高亮显示的信息

3. 遍历结果高亮显示(HighlightField.getFragments() )

​    代码部分：

      ```java
public static void search(QueryBuilder queryBuilder, String highlightField){

        initClient();
        //设置高亮的内容
        HighlightBuilder highlightBuilder = new HighlightBuilder();
        highlightBuilder.field(highlightField);
        highlightBuilder.preTags("<em>");
        highlightBuilder.postTags("</em>");

        SearchResponse searchResponse = transportClient
                .prepareSearch("index_hello").setTypes("article").setQuery(queryBuilder).setFrom(0).setSize(5).highlighter(highlightBuilder).get();
        //查询结果
        SearchHits searchHits = searchResponse.getHits();
        int totalHits = (int) searchHits.totalHits;
        SearchHit[] hits = searchHits.getHits();
        float maxScore = searchHits.getMaxScore();
        System.out.println("总命中数:"+totalHits+" 最大得分:"+maxScore);
        for(int i=0;i<hits.length;i++){
            SearchHit h =  hits[i];
            Map<String, HighlightField> stringHighlightFieldMap = h.highlightFields();
            //取高亮结果
            HighlightField highlightField1 = stringHighlightFieldMap.get(highlightField);
            Text[] fragments = highlightField1.fragments();
            System.out.println("text[]"+ fragments);
            System.out.println(fragments[0].toString());
        }

//        Iterator<SearchHit> iterator = searchHits.iterator();
//        while(iterator.hasNext()){
//            SearchHit hit = iterator.next();
//            System.out.println(hit.getSourceAsString());
//            System.out.println("===属性===");
//            Map<String, Object> source = hit.getSource();
//            System.out.println("id :"+source.get("id"));
//            System.out.println("title :"+source.get("title"));
//        }
        transportClient.close();
    }
      ```

