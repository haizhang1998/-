###ElasticSearch学习（五）----SpringData操作Es

###创建索引并定义mappings信息

1. 导入pom文件

```java
 <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.12</version>
      <scope>test</scope>
    </dependency>
    <dependency>
      <groupId>org.elasticsearch</groupId>
      <artifactId>elasticsearch</artifactId>
      <version>5.6.8</version>
    </dependency>
    <dependency>
      <groupId>org.elasticsearch.client</groupId>
      <artifactId>transport</artifactId>
      <version>5.6.8</version>
    </dependency>
    <dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-databind</artifactId>
      <version>2.9.3</version>
    </dependency>
    <dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-annotations</artifactId>
      <version>2.9.0</version>
    </dependency>
    <dependency>
      <groupId>org.apache.logging.log4j</groupId>
      <artifactId>log4j-to-slf4j</artifactId>
      <version>2.9.1</version>
    </dependency>
    <dependency>
      <groupId>org.slf4j</groupId>
      <artifactId>slf4j-api</artifactId>
      <version>1.7.24</version>
    </dependency>
    <dependency>
      <groupId>org.slf4j</groupId>
      <artifactId>slf4j-simple</artifactId>
      <version>1.7.21</version>
    </dependency>
    <dependency>
      <groupId>org.springframework.data</groupId>
      <artifactId>spring-data-elasticsearch</artifactId>
      <version>3.0.5.RELEASE</version>
      <exclusions>
        <exclusion>
          <groupId>org.elasticsearch.plugin</groupId>
          <artifactId>transport-netty4-client</artifactId>
        </exclusion>
      </exclusions>
    </dependency>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-core</artifactId>
      <version>5.0.4.RELEASE</version>
    </dependency>
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-beans</artifactId>
      <version>5.0.4.RELEASE</version>
      <scope>compile</scope>
    </dependency>

    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-test</artifactId>
      <version>5.0.4.RELEASE</version>
    </dependency>
  </dependencies>
```

2. 创建一个spring的配置文件
   1. 配置elasticsearch:transport-client
   2. elasticsearch:repositories: 包扫描器，扫描dao
   3. 配置elasticsearchTemplate对象，就是一个bean

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:elasticsearch="http://www.springframework.org/schema/data/elasticsearch"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/data/elasticsearch http://www.springframework.org/schema/data/elasticsearch/spring-elasticsearch.xsd">
<!--  elastic客户端对象的配置-->
    <elasticsearch:transport-client id="esClient" cluster-name="my-elasticsearch" cluster-nodes="127.0.0.1:9301,127.0.0.1:9302,127.0.0.1:9303"/>
<!--配置包扫描器,扫描dap的接口-->
    <elasticsearch:repositories base-package="com.haizhang.springData"></elasticsearch:repositories>
    <bean id="elasticsearchTemplate" class="org.springframework.data.elasticsearch.core.ElasticsearchTemplate">
        <constructor-arg  name="client" ref="esClient"></constructor-arg>
    </bean>
</beans>
```

3. 管理索引库

   1. 创建一个Entity类，其实就是一个JAVABean（pojo）映射到一个type上

   ```java
   
   //@Doucument注解声明这个bean是一个文档，可以用它创建指定的索引(index_name)，指定的类型（type）
   @Document(type = "article",indexName = "spring_article")
   public class Article {
   
       //@Id则为编号主键（文档编号）
       @Id  
       //@Field代表这个字段的一些类型，是否存储，是否索引等信息
       @Field(type = FieldType.Long,store = true)
       private long id ;
   
       @Field(type = FieldType.text,store = true,analyzer = "standard")
       private String title;
   
   }
   
   ```

   2. 创建一个Dao接口继承ElasticsearchRepository

   ```java
   //在这里继承的接口中泛型分别是操作的对象，以及返回的逐渐类型
   public interface ArticleRepository extends ElasticsearchRepository<Article,Long> {
   }
   ```

   3. 创建一个test类来测试

   ```java
   
   @RunWith(SpringJUnit4ClassRunner.class)
   @ContextConfiguration("classpath:applicationContext.xml")
   public class TestEsForSpringData {
   
       @Autowired
       ArticleRepository articleRepository;
   
       @Autowired
       ElasticsearchTemplate elasticsearchTemplate;
   
       @Test
       public void creatIndexAndMappings(){
            elasticsearchTemplate.createIndex(Article.class);
       }
   }
   
   ```





### 添加文档

1. 创建一个javabean对象（文档对象）
2. 设置javabean中的属性
3. 把文档写入索引库（save方法）

实现代码：

```java

    @Test
    public void insertDocument(){
        Article article = new Article();
        article.setId(12312312L);
        article.setTitle("myFirstSpringArticle");
        articleRepository.save(article);
    }
```



### 删除文档

1. 直接使用ariticleRepository中的deleteById就可以根据文档id（标注了@Id的字段）删除文档了

```java
  @Test
    public void delDocument(){
        articleRepository.deleteById(123123121L);
    }

```

### 更新文档

```java

//这里只用保证文档id一样，修改其他的内容即可
    @Test
    public void updateDocument(){
        Article article = new Article();
        article.setId(12312312L);
        article.setTitle("update.......");
        articleRepository.save(article);
    }
```

### 查询数据

下面是springData中ElasticsearchRepository定义的一些方法命名规则，注意findByField1 ，Field1就是你javabean中定义了哪些作用域才会有提示，只有提示了，才可以定义再接口上，没有的话不能写！ **这些方法只管声明即可，不用实现**

![1565683807989](C:\Users\86137\AppData\Roaming\Typora\typora-user-images\1565683807989.png)

eg:

```java
//这个类主要是操作Article这个type的增删改
public interface ArticleRepository extends ElasticsearchRepository<Article,Long> {
     //按照文档的title取查询article( title是article 中拥有的)
     List<Article> findArticleByTitle(String title);
}

```

这是一些测试类，用法大同小异，都是通过springData提供的es模板或者ArticleRepository直接操作的。

唯一不一样的是NaticeSearchQuery，它可以用原生的方式取定义查询类型

```java
@Test
    public void queryArticle(){
        //使用articleRepository查询所有的记录
//        Iterable<Article> all = articleRepository.findAll();
//        all.forEach(a-> System.out.println(a));

        //使用articleRepository查询指定文档id记录 Optional为java8的新特性，返回个可选项，我们直接调用get()方法既可以拿到目标对象
        Optional<Article> byId = articleRepository.findById(20L);
        System.out.println(byId.get());

    }

    /**
     * 自定义在ArticleRepository中按照它所给的命名规则定义一个接口方法即可
     */
    @Test
    public void queryArticleByNamedMethod(){
          articleRepository.findArticleByTitle( "maven").forEach(a-> System.out.println(a));
    }

    /**
     * 使用naticeSearchQuery查询，可以自定义查询，再将这个对象传给es模板查询。
     */
    @Test
    public void queryArticleByNativeSearchQuery(){
        NativeSearchQuery nativeSearchQuery = new NativeSearchQueryBuilder().withQuery(QueryBuilders.queryStringQuery("maven是一个构建工具类"))
                .withFields("title").withPageable(new PageRequest(0,5)).build();
        List<Article> articles = elasticsearchTemplate.queryForList(nativeSearchQuery, Article.class);
        articles.forEach(a-> System.out.println(a));
    }
```

