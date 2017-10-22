1.使用luceneMatchVersion 的版本

```
<luceneMatchVersion>4.5</luceneMatchVersion>
```

2.索引文件存放的位置 

```
<dataDir>${solr.data.dir:}</dataDir>
```
 
 3. 索引的存储方案 

```
<directoryFactory name="DirectoryFactory" class="${solr.directoryFactory:solr.NRTCachingDirectoryFactory}"/>
```

4.可以自定义字段类型，但是有个问题需要注意，当主程序重新加载的时候，自定义的类型不一定有效果。

```
<codecFactory class="solr.SchemaCodecFactory"/>
```

使用步骤：

1).导入所需要的包

```
<lib dir="../../../../lucene/build/codecs/" />
```

2).定义类型，例如(注:字段postingsFormat只有在codecFacotry定义时才能使用)

```
<fieldType name="text_simpletext" class="solr.TextField" postingsFormat="SimpleText">
  <analyzer>
    <tokenizer class="solr.WhitespaceTokenizerFactory"/>
    <filter class="solr.LowerCaseFilterFactory"/>
  </analyzer>     
</fieldType>
```

3).定义字段，例如

```
<field name="simple_text" type="text_simpletext" indexed="true" stored="true"/>
```

5.读取xml的方式，当"ManagedIndexSchemaFactory"指定的时候，solr会加载"managedSchemaResourceName"节点指定名称的文件，而不是默认的schema.xml文件，如果managedschema不存在的话，solr会在读取schema.xml后自动创建，并且命名为schemal.xml.bak。

```
<schemaFactory class="ClassicIndexSchemaFactory"/>
<schemaFactory class="ManagedIndexSchemaFactory">
<bool name="mutable">true</bool>
<str name="managedSchemaResourceName">managed-schema</str>
</schemaFactory>
```

6.在4.0之后把maxFieldLength属性给去掉了，代替的是solr.LimitTokenCountFilterFactory,目的是控制fieldType的个数。

```
<filter class="solr.LimitTokenCountFilterFactory" maxTokenCount="10000"/>
```

7.等待写锁权限的最大时间。

```
<writeLockTimeout>1000</writeLockTimeout>
```

8.并发线程建立索引的最大线程数，超过这个线程数创建将处于等待状态。

```
<maxIndexingThreads>8</maxIndexingThreads>
```

9.是否使用复合文件功能，使用这个功能的话，将会减少索引的个数，但是会牺牲性能。

```
<useCompoundFile>false</useCompoundFile>
```

10.设置索引刷新到磁盘前，缓存在内存中文档的数量。Solr 默认情况下没有设置该值

```
<ramBufferSizeMB>100</ramBufferSizeMB>
```

11.单位为 M，设置索引刷新到磁盘前，缓存在内存时使用的最大内存。该参数和 maxBufferedDocs 同时设置，只要达到其中的一个限制时，都将缓存写入磁盘

```
<maxBufferedDocs>1000</maxBufferedDocs>
```

12.lucene块的合并策略，控制这块是如何被合并的。

```
<mergePolicy class="org.apache.lucene.index.TieredMergePolicy">
<int name="maxMergeAtOnce">10</int>
      <int name="segmentsPerTier">10</int>
</mergePolicy>
```

13.合并因子，合并因子控制多少个分片将会一次性和合并，对于层叠合并策略(TieredMergePolicy)，合并因子一个便利的参数，它将一次性设置MaxMergeAtOnce和SegmentsPerTier的值，对于日志字节大小的不合并策略(LogByteMergePolicy)，合并因子决定了多少片段合并到一个片段前允许有多少个片段。默认的这两个策略的默认值为10。

```
<mergeFactor>10</mergeFactor>
```

14.指定合并调度程序，在Lucene中，合并调度程序控制着rue好合并分片的实现

```
<mergeScheduler class="org.apache.lucene.index.ConcurrentMergeScheduler"/>
```

15.此选项指定Lucene的LockFactory的策略：

```
<lockType>${solr.lock.type:native}</lockType>
```

使用：
single =SingleInstanceLockFactory 当没有线程可以用来修改的时候，只能读取不能修改。

native=NativeFSLockFacory 使用系统本身的锁机制。但是当多个solr web使用同一个JVM，并且尝试共享索引的时候，这个锁机制不适合。

simple = SimpleFSLockFacotry 使用普通的文件进行锁定。
注意：native在solr 3.6和以后的版本中是默认的锁策略，其他的策略是'simple'

16.在启动的时候是否加锁，如果使用了single锁工厂的话，这个选项就没有必要。

```
<unlockOnStartup>false</unlockOnStartup>
```

17.控制Lucene多久一次将terms加载到内存中，默认的是128，这个默认的值对于大多数的应用来说都是挺好的。

```
<termIndexInterval>128</termIndexInterval>
```

18.如果为true,Indexreaders会从IndexWriter打开/重新打开，而不是从Directory，在master/slave中的主机需要设置成false,在solrCloud中的主机需要设置成true。

```
<nrtMode>true</nrtMode>
```

19.提交删除的策略，定制的删除策略能够在这里指定，这个类必须实现org.apache.lucene.index.IndexDeletionPolicy，不管具体指定的标准，最近提交点应该被保护起来。

```
<deletionPolicy class="solr.SolrDeletionPolicy">
```

20.保持最大的提交数。

```
<str name="maxCommitsToKeep">1</str>
```

21.保持最大优化的提交数

```
<str name="maxOptimizedCommitsToKeep">0</str>
```

22.一旦他们达到给定的时期，就删除所有的提交点，支持DateMathParser语法例子。maxCommitAge:设定提交时刻。

```         
<str name="maxCommitAge">30MINUTES</str>
<str name="maxCommitAge">1DAY</str>
```

23.调试帮助，lucene提供一个InfoStream来显示索引的时候的详细信息。设置为true会将Lucene e;IndexWriter写入到solr的日志中。默认的情况下次选项是true

```
<infoStream>true</infoStream>
```


24.使用JMX的时候，需要有且只有一个存在的MBeanServer被发现，使用JMX可以配置JVM的参数，去掉这个属性的话，就禁用暴漏JMX的配置

```
<jmx />
```

25.jmx配置的例子

```
<jmx agentId="myAgent" />
<jmx serviceUrl="service:jmx:rmi:///jndi/rmi://localhost:9999/solr"/>
```

26.配置高性能的更新操作类，默认的为DirectUpdateHandler2

```
<updateHandler class="solr.DirectUpdateHandler2">
```

27.使用事务日志，实时的获取，持久化solr cloud 还原的备份。日志的大小随着未提交的索引的增大而增大，所以使用自动提交是必要的。
dir：目标事务日志的目录，默认的是solr data目录

```
<updateLog>
      <str name="dir">${solr.ulog.dir:}</str>
</updateLog>
```

28.maxDocs:自动化触发一个新的提交之前，从最近提交文档后能够递增到最大文档的数目。

maxTime:自动化触发一个新的提交之前，从一个文件被添加以后能够允许最大化花费的时间（微秒）

openSearcher:如果为false,提交会引起最近改变的索引冲刷到稳定的存储器中，但是不会引起打开一个新的检索器来是这些改变能够可视。

如果updateLog能够使用，则强烈推荐去有一些固定自动提交(autocommit)来限制log的大小

```
<autoCommit>
<maxDocs></maxDocs>
<maxTime>${solr.autoCommit.maxTime:15000}</maxTime>
<openSearcher>false</openSearcher>
</autoCommit>
```


29.软自动提交就像自动提交一样，除了他会引起“软提交”外，这个软提交仅仅确保这个改变是可视的，但是不能确保数据一定同步到磁盘了，这个提交方式比硬提交更快而且更加友好的接近了实时。

```
<autoSoftCommit>
<maxTime>${solr.autoSoftCommit.maxTime:-1}</maxTime>
</autoSoftCommit>
```


30.更新相关的事务监听，不同的indexWriter相关的事务能够触发一个监听器去执行一个操作。

postCommit:每次提交或者是优化命名后就会激发事件。

postOptimize:每次优化命名后就会触发时间。

RunExecutableListener从一个内部方法比如postCommit或者是postOptimize执行内部命令。

exe:运行的执行器名称

dir:使用作为当前的工作目录

wait:调用线程等待知道executable 返回（默认是true）

args:传递到程序中的参数。默认是none

env:设置环境变量

```
<listener event="postCommit" class="solr.RunExecutableListener">
<str name="exe">solr/bin/snapshooter</str>
<str name="dir">.</str>
    <bool name="wait">true</bool>
    <arr name="args"> <str>arg1</str> <str>arg2</str> </arr>
    <arr name="env"> <str>MYVAR=val1</str> </arr>
</listener>
```

31.这个属性处于实验阶段，最好不要使用

```
<indexReaderFactory name="IndexReaderFactory" class="package.class">
    <str name="someArg">Some Value</str>
</indexReaderFactory >
```

32.最大布尔分句：指定在每个布尔查询最大的分句数，超过这个值会抛出一个异常
警告：这个选项实际上修改了一个能够影响到所有的solrCores的全局Lucene属性。这个属性在多个solrcofig.xml文件不一致，在提供那一刻会基于最接近的solrCore被初始化。

```
<maxBooleanClauses>1024</maxBooleanClauses>
```

33.solr内部查询缓存，对于solr有两个可用的缓存实现，LRUCache是以一个同步LinkedHashMap为基础，FastLRUCache是以一个ConcurrentHashMap为基础。

FastLRUCache获取更快而输入input更慢，当缓存的匹配率大于75时FastLRUCache可能更快。

过滤器缓存：对于过滤器(DocSets)， solrIndexSearcher所使用的缓存无序匹配一个"*all*"文档查询的集合。当一个搜索器打开，它的缓存可能会重新入住或是使用来自旧的检索器中的缓存来自动预热。autowarmCount指定了重新入住的item个数，对于LRUCache,自动预热items将会是最新可以访问的items。

参数介绍：

class:SolrCache实现了LRUCache(LRUCache或是FastRUCache)。

size:缓存中的最大条目数量.

initialSize:缓存的初始化容量。（看java.util.HashMap）

autowarmCount:重新入住的条目数量和旧缓存数。

```
<filterCache class="solr.FastLRUCache"
 size="512" initialSize="512" autowarmCount="0"/>
```

34.查询缓存：缓存查询的结果，文档idds有序的链表，lists基于一个查询，一个排序和请求文档的区间范围

```
<queryResultCache class="solr.LRUCache" size="512" initialSize="512" autowarmCount="0"/>
```


35.文档缓存：缓存Lucene文档对象(对于每个文档的存储值域)。既然Lucene内部文档ids是过度域，这种缓存是不会自动预热(autowarmed)。

```
<documentCache class="solr.LRUCache"
 size="512" initialSize="512" autowarmCount="0"/>
```

36.域值缓存：缓存过去常常掌控通过文档id能够快速访问域值。即时这儿没有配置，默认情况下会创建filedValueCache。

```
<fieldValueCache class="solr.FastLRUCache"
 size="512" autowarmCount="128" showItems="32" />
```

37.定制缓存:一般缓存的例子，大多是通过名字经过SolrIndexSearcher.getCache()，cacheLookup()以及cacheInsert()来访问的。这个目的就是能够很容易缓存用户/应用级别的数据。如果自动预热是需要的，交流换热器参数应该被指定作为solr.CacheRegenerator的实现。

```
<cache name="myUserCache"
 class="solr.LRUCache" size="4096" initialSize="1024" autowarmCount="1024" regenerator="com.mycompany.MyRegenerator" />
```

38.延迟域加载：如果设置为true，存储不再请求的域将不会被延迟加载。如果通常的情况是不用加载所有的存储的域，尤其是如果能跳过被压缩的文本域，这能够导致意义重大的速度提升。

```
<enableLazyFieldLoading>true</enableLazyFieldLoading>
```

39.对于一个有序查询使用过滤器。一个可能的优化就是尝试使用一个过滤器获得一次满意的检索。如果请求排序没有包括分数(score)，则过滤缓存(filterCache)将会检测对于正匹配的查询缓存器，如果发现，这个过滤器将作为文本ids的源，然后这个排序会被引用到那个查询上。
对于大多数情况，这个将不会有帮助直到你频繁地应用不同的排序获得相同地检索，并且都没有使用score。

```
<useFilterForSortedQuery>true</useFilterForSortedQuery>
```

40.结果窗口大小：一种使用queryResultCache的优化。当一个请求检索时，一个请求的文档ids的拓展文档会搜集，例如：如果对一个特殊查询请求，匹配文档10到19以及查询窗口时

```
<queryResultWindowSize>20</queryResultWindowSize>
```

41.在queryResultCache中最大缓存的文档数量。

```
<queryResultMaxDocsCached>200</queryResultMaxDocsCached>
```

42.相关查询的监听

```
    <listener event="newSearcher" class="solr.QuerySenderListener">
      <arr name="queries">
        <!--
           <lst><str name="q">solr</str><str name="sort">price asc</str></lst>
           <lst><str name="q">rocks</str><str name="sort">weight asc</str></lst>
          -->
      </arr>
    </listener>
    <listener event="firstSearcher" class="solr.QuerySenderListener">
      <arr name="queries">
        <lst>
          <str name="q">static firstSearcher warming in solrconfig.xml</str>
        </lst>
      </arr>
    </listener>
```

不同相关事件的IndexSearcher能够触发对于行为的监听。

newSearcher：任何时候都会有一个新的searcher正准备好，并且有一个正在检索器正在处理请求，它能激发某系缓存来阻止长时间的请求。

firstSearcher ：任何时候都会有一个新的检索器在准备，但是没有注册好的检索器等待初期请求或者是预热数据。


43.使用冷的检索器：如果一个检索请求进来，那儿当前没有一个注册的检索器，则立即注册正在预热的检索器并且使用它。如果为false的话，则进入阻塞状态直到预热完成。

```
<useColdSearcher>false</useColdSearcher>
```

44.在后台预热searcher的个数，如果超过限制就会返回错误。对于只读的slave，我们推荐是1到2个，对于w/o缓存预热就需要设置更高的值。

```
<maxWarmingSearchers>2</maxWarmingSearchers>
```

45.这个参数设置了当请求进来的时候solrDispatchFilter怎样处理请求，handleSelect是一个遗留下来的选项，影响请求的具体行为比如  select?qt=XXX，
handleSelect 设置为true的时候，会导致SolrDispatchfilter去处理请求并且调度查询去处理qt制定的参数，如果select 没有注册的话。

handleSelect 设置为false，会导致SolrDispatchFilter去忽略select请求，导致404的出现，除非handler注册了select。

handleSelect设置true对于新用户不推荐，但是它是默认的。

```
<requestDispatcher handleSelect="false" >
```


46.这是设置指明了solr的请求如何被转换和什么样的限制可以替换请求中的ContentStreams
enableRemoteStreaming 使用文件流或者是url流来制定远程的流.

multipartUploadLimitInKB  制定solr允许一个请求中多部件上传的最大文件大小。
formdataUploadLimitInKB  form表单中post方式提交的最大数据的量(application/x-www-form-urlencoded)，你可以使用post方式提交参数，而不是把参数都写到url中。

addHttpRequestToContext  如果设置为true,它会在solrQueryRequest中包含HttpServletRequest 对象，solrQueryRequest是个map，key为httpRequest。它不会使用任何已经存在的组建，如果在开发自定义组件的时候会非常有用。

注意：这个设置会允许solr去远程读取文件，你必须确定你的系统有一些安全认证，在使用enableRemoteStreaming的时候。

```
<requestParsers enableRemoteStreaming="true" multipartUploadLimitInKB="2048000" formdataUploadLimitInKB="2048" addHttpRequestToContext="false"/>
```

47.设置http的参数缓存，包含代理的和客户端的。这个选项不会输出http的任何缓存头信息。

```
<httpCaching never304="true" />
```

48.设置缓存，如果你设置了cacheControl,你将会用到Cache-Control头，默认是不设置Cache-Control头。你可以设置cacheControl选项尽管你设置never304为true。

```
<httpCaching never304="true" >
         <cacheControl>max-age=30, public</cacheControl>
       </httpCaching>
```

49.这个设置能使solr自动的生成http 的相应头，并且正确的返回Cache Validation,设置never304='false'。

这个可以使solr生成last-Modified和ETag 头，它是基于索引的属性文件。

lastModFrom  默认的值是openTime,它的意思是Last-Modified值会被关联当Searcher打开的时候。你可以改变lastModFrom="dirLastMod"如果你想在物理索引更新的时候被更新。

etagSeed   你可以强制的改变ETag头，即使索引没有改变。

注意：如果你never304设置为true的时候，lastModifiedFrom 和  etagSedd都会被忽略。

```
<httpCaching lastModifiedFrom="openTime" etagSeed="Solr">
         <cacheControl>max-age=30, public</cacheControl>
       </httpCaching>
```

50.请求的处理机制，进来的查询根据请求的路径名字调用一个具体的处理handler。如果声明请求处理机制startup=lazy的话，则它不会初始化，直到第一个请求进来。

```
<requestHandler name="/select" class="solr.SearchHandler">
```

51.默认的配置为10条数据，text类型，参数为explicit

```
<lst name="defaults">
       <str name="echoParams">explicit</str>
       <int name="rows">10</int>
       <str name="df">text</str>
     </lst>
```

52.向查询语句的查询参数后添加指定的值。
fq=instock:true 会添加到用户指定的查询参数的后边，作为分区索引的机制，独立于用户选择的过滤器。
注意：任何的客户端不能阻止添加的参数，所以最好不要使用他，除非你确实想使用他。

```
<lst name="appends">
         <str name="fq">inStock:true</str>
</lst>
```

53.让solr容器锁住对于solr客户端的有效选择的方式，不管什么请求指定参数值的查询，任何在这里配置的参数都将被使用。
注意：客户端不能阻止invariant被应用，所以一般的时候不要使用，除非你确定你必须使用。

```
<lst name="invariants">
 <str name="facet.field">cat</str>
 <str name="facet.field">manu_exact</str>
 <str name="facet.query">price:[* TO 500]</str>
 <str name="facet.query">price:[500 TO *]</str>
</lst>
```

54.如果默认的检索列表不能使你满意的话，可以在此处覆盖。
```
       <arr name="components">
         <str>nameOfCustomComponent1</str>
         <str>nameOfCustomComponent2</str>
       </arr>
```


55.默认的返回json类型的格式

```
  <requestHandler name="/query" class="solr.SearchHandler">
     <lst name="defaults">
       <str name="echoParams">explicit</str>
       <str name="wt">json</str>
       <str name="indent">true</str>
       <str name="df">text</str>
     </lst>
  </requestHandler>
```

56.实时的获得handler，保证获得最新的文档属性，不需要提交或者打开一个新的检索器，当前的实现必须依赖于updateLog 功能的打开。

```
<requestHandler name="/get" class="solr.RealTimeGetHandler">
     <lst name="defaults">
       <str name="omitHeader">true</str>
       <str name="wt">json</str>
       <str name="indent">true</str>
     </lst>
  </requestHandler>
```

57.一个完整的实例，这个实例searchHandler声明突出了SearchHandler与许多默认声明的使用。需要注意的是相同请求处理器的多个实例能够用不同的名称注册到事件上。

```
<requestHandler name="/browse" class="solr.SearchHandler">
     <lst name="defaults">
       <str name="echoParams">explicit</str>

       <!-- VelocityResponseWriter settings -->
       <str name="wt">velocity</str>
       <str name="v.template">browse</str>
       <str name="v.layout">layout</str>
       <str name="title">Solritas</str>

       <!-- Query settings -->
       <str name="defType">edismax</str>
       <str name="qf">
          text^0.5 features^1.0 name^1.2 sku^1.5 id^10.0 manu^1.1 cat^1.4
          title^10.0 description^5.0 keywords^5.0 author^2.0 resourcename^1.0
       </str>
       <str name="df">text</str>
       <str name="mm">100%</str>
       <str name="q.alt">*:*</str>
       <str name="rows">10</str>
       <str name="fl">*,score</str>

       <str name="mlt.qf">
         text^0.5 features^1.0 name^1.2 sku^1.5 id^10.0 manu^1.1 cat^1.4
         title^10.0 description^5.0 keywords^5.0 author^2.0 resourcename^1.0
       </str>
       <str name="mlt.fl">text,features,name,sku,id,manu,cat,title,description,keywords,author,resourcename</str>
       <int name="mlt.count">3</int>

       <!-- Faceting defaults -->
       <str name="facet">on</str>
       <str name="facet.field">cat</str>
       <str name="facet.field">manu_exact</str>
       <str name="facet.field">content_type</str>
       <str name="facet.field">author_s</str>
       <str name="facet.query">ipod</str>
       <str name="facet.query">GB</str>
       <str name="facet.mincount">1</str>
       <str name="facet.pivot">cat,inStock</str>
       <str name="facet.range.other">after</str>
       <str name="facet.range">price</str>
       <int name="f.price.facet.range.start">0</int>
       <int name="f.price.facet.range.end">600</int>
       <int name="f.price.facet.range.gap">50</int>
       <str name="facet.range">popularity</str>
       <int name="f.popularity.facet.range.start">0</int>
       <int name="f.popularity.facet.range.end">10</int>
       <int name="f.popularity.facet.range.gap">3</int>
       <str name="facet.range">manufacturedate_dt</str>
       <str name="f.manufacturedate_dt.facet.range.start">NOW/YEAR-10YEARS</str>
       <str name="f.manufacturedate_dt.facet.range.end">NOW</str>
       <str name="f.manufacturedate_dt.facet.range.gap">+1YEAR</str>
       <str name="f.manufacturedate_dt.facet.range.other">before</str>
       <str name="f.manufacturedate_dt.facet.range.other">after</str>

       <!-- Highlighting defaults -->
       <str name="hl">on</str>
       <str name="hl.fl">content features title name</str>
       <str name="hl.encoder">html</str>
       <str name="hl.simple.pre">&lt;b&gt;</str>
       <str name="hl.simple.post">&lt;/b&gt;</str>
       <str name="f.title.hl.fragsize">0</str>
       <str name="f.title.hl.alternateField">title</str>
       <str name="f.name.hl.fragsize">0</str>
       <str name="f.name.hl.alternateField">name</str>
       <str name="f.content.hl.snippets">3</str>
       <str name="f.content.hl.fragsize">200</str>
       <str name="f.content.hl.alternateField">content</str>
       <str name="f.content.hl.maxAlternateFieldLength">750</str>

       <!-- Spell checking defaults -->
       <str name="spellcheck">on</str>
       <str name="spellcheck.extendedResults">false</str>       
       <str name="spellcheck.count">5</str>
       <str name="spellcheck.alternativeTermCount">2</str>
       <str name="spellcheck.maxResultsForSuggest">5</str>       
       <str name="spellcheck.collate">true</str>
       <str name="spellcheck.collateExtendedResults">true</str>  
       <str name="spellcheck.maxCollationTries">5</str>
       <str name="spellcheck.maxCollations">3</str>           
     </lst>

     <!-- append spellchecking to our list of components -->
     <arr name="last-components">
       <str>spellcheck</str>
     </arr>
  </requestHandler>
```


58.通过指定XML , JSON ,CSV ,JAVABIN来修改索引的规范。
注意：solr1.1之后，如果在内容体内提交的话，requestHandlers需要校验类型的头，例如 -H 'Content-type:text/xml; charset=utf-8'
付给请求的内容类型，或者强制指定一个特殊的Content-type，使用请求参数   ?update.contentType=text/csv
这个handler或提取相应格式去匹配输入，如果wt参数没有指定

```
<requestHandler name="/update" class="solr.UpdateRequestHandler">
      <lst name="defaults">
         <str name="update.chain">dedupe</str>
       </lst>
  </requestHandler>
```

59.反向兼容客户端使用  /update/json   /update/csv

```
  <requestHandler name="/update/json" class="solr.JsonUpdateRequestHandler">
        <lst name="defaults">
         <str name="stream.contentType">application/json</str>
       </lst>
  </requestHandler>
  <requestHandler name="/update/csv" class="solr.CSVRequestHandler">
        <lst name="defaults">
         <str name="stream.contentType">application/csv</str>
       </lst>
  </requestHandler>
```


60.solr的单元格更新。

```
  <requestHandler name="/update/extract" startup="lazy" class="solr.extraction.ExtractingRequestHandler" >
    <lst name="defaults">
      <str name="lowernames">true</str>
      <str name="uprefix">ignored_</str>

      <!-- capture link hrefs but ignore div attributes -->
      <str name="captureAttr">true</str>
      <str name="fmap.a">links</str>
      <str name="fmap.div">ignored_</str>
    </lst>
  </requestHandler>
```

61.  field分析请求handler。requestHandler提供了如analysis.jsp一样许多相同功能。提供了这种能够在相同请求中指定多个field类型和名称的能力，输出检索时间和对任何项的查询时间分析。

analysis:fieldname  将被使用的field分析器。

analysis fieldType 类型

analysis.fieldvalue 索引时间的分析

q 查询时间分析的文本

analysis.showmatch(true | false)   当此值设为true的时候，查询分析的时候，会把每一个产生的分析都标记为"matched"。

```
<requestHandler name="/analysis/field"
 startup="lazy" class="solr.FieldAnalysisRequestHandler" />
```


62.每个文档必须有一个属性作为唯一标识。响应结果通过key关联文档的分析结果。
和FieldAnalysisRequestHandler 一样，这个handler也提供查询分析，通过analysis.query或者 q，请求参数查询字段，它也支持analysis.showmatch参数，当它设为true，所有的filed标记匹配查询标记都会被标记上match

```
<requestHandler name="/analysis/document" class="solr.DocumentAnalysisRequestHandler" startup="lazy" />
```

63. 如果你想要在${solr.home}.conf下隐藏文件，你需要像上边那样注册。

```
<requestHandler name="/admin/" class="solr.admin.AdminHandlers" />
所有的管理员handler在这儿注册，上边的语句和下边的组合语句是一样的效果
     <requestHandler name="/admin/luke"       class="solr.admin.LukeRequestHandler" />
     <requestHandler name="/admin/system"     class="solr.admin.SystemInfoHandler" />
     <requestHandler name="/admin/plugins"    class="solr.admin.PluginInfoHandler" />
     <requestHandler name="/admin/threads"    class="solr.admin.ThreadDumpHandler" />
     <requestHandler name="/admin/properties" class="solr.admin.PropertiesRequestHandler" />
     <requestHandler name="/admin/file"       class="solr.admin.ShowFileRequestHandler" >

     <requestHandler name="/admin/file" class="solr.admin.ShowFileRequestHandler" >
       <lst name="invariants">
         <str name="hidden">synonyms.txt</str>
         <str name="hidden">anotherfile.txt</str>
       </lst>
     </requestHandler>
```

64.健康检测，healthcheckFile能够激活/失效PingRequestHandler的healthCheckFile的handler。

```  
  <requestHandler name="/admin/ping" class="solr.PingRequestHandler">
    <lst name="invariants">
      <str name="q">solrpingquery</str>
    </lst>
    <lst name="defaults">
      <str name="echoParams">all</str>
    </lst>
    <!-- <str name="healthcheckFile">server-enabled.txt</str> -->
  </requestHandler>
```

65.发送请求到客户端

```
  <requestHandler name="/debug/dump" class="solr.DumpRequestHandler" >
    <lst name="defaults">
     <str name="echoParams">explicit</str>
     <str name="echoHandler">true</str>
    </lst>
  </requestHandler>
```

66.SolrReplicationHandler支持从master 上使用index复制索引，在slaves使用query复制索引。
需要SolrCloud功能。（在云功能上，replication handler需要传送大量的片段，当节点添加或者需要覆盖时）
使用简单的master/slave模式，不需要注释上边内部的内容，它依赖于solr的实例是master还是slave。如果实例是slave你需要填写masterUrl来指定真实的主机。

```
<requestHandler name="/replication" class="solr.ReplicationHandler" >
    <!--
       <lst name="master">
         <str name="replicateAfter">commit</str>
         <str name="replicateAfter">startup</str>
         <str name="confFiles">schema.xml,stopwords.txt</str>
       </lst>
    -->
    <!--
       <lst name="slave">
         <str name="masterUrl">http://your-master-hostname:8983/solr</str>
         <str name="pollInterval">00:00:60</str>
       </lst>
    -->
  </requestHandler>
```

67.Search component注册到SolrCore，并且被searchHandler的实例使用。默认的上边的组件都是可以使用的。

```
       <searchComponent name="query"     class="solr.QueryComponent" />
       <searchComponent name="facet"     class="solr.FacetComponent" />
       <searchComponent name="mlt"       class="solr.MoreLikeThisComponent" />
       <searchComponent name="highlight" class="solr.HighlightComponent" />
       <searchComponent name="stats"     class="solr.StatsComponent" />
       <searchComponent name="debug"     class="solr.DebugComponent" />

       <arr name="components">
         <str>query</str>
         <str>facet</str>
         <str>mlt</str>
         <str>highlight</str>
         <str>stats</str>
         <str>debug</str>
       </arr>

       <arr name="first-components">
         <str>myFirstComponentName</str>
       </arr>

       <arr name="last-components">
         <str>myLastComponentName</str>
       </arr>
```


68.
拼写检查组件。

```
<searchComponent name="spellcheck" class="solr.SpellCheckComponent">

    <str name="queryAnalyzerFieldType">text_general</str>

可以定义多个spellchecker

      <lst name="spellchecker">
      <str name="name">default</str>
      <str name="field">text</str>
 #字段名，表示基于schema中的某一个索引字段
      <str name="classname">solr.DirectSolrSpellChecker</str>
      <!-- 拼写检查使用的距离量度，默认的是internal -->
      <str name="distanceMeasure">internal</str>
      <!-- 使用拼写检查的时间间隔 -->
      <float name="accuracy">0.5</float>
      <!-- 编辑枚举条目的最大值，可以是1或2 -->
      <int name="maxEdits">2</int>
      <!-- 最小的共享的前缀当枚举条目的时候 -->
      <int name="minPrefix">1</int>
      <!-- 每次检查最大的条目 -->
      <int name="maxInspections">5</int>
      <!-- 查询的正确结果的最小长度 -->
      <int name="minQueryLength">4</int>
      <!--  文档查询条目能出现正确值的最大条目 -->
      <float name="maxQueryFrequency">0.01</float>
      <!-- 注释下边的建议，在1%的文档中发生
      	<float name="thresholdTokenFrequency">.01</float>
      -->
    </lst>

    <!--  拼写检查器，可以拆分或者是合并，详情参见spell -->
    <lst name="spellchecker">
      <str name="name">wordbreak</str>
      <str name="classname">solr.WordBreakSolrSpellChecker</str>      
      <str name="field">name</str>
      <str name="combineWords">true</str>
      <str name="breakWords">true</str>
      <int name="maxChanges">10</int>
    </lst>

    <!-- 拼写检查器，使用不同的距离量度 -->
    <!--
       <lst name="spellchecker">
         <str name="name">jarowinkler</str>
         <str name="field">spell</str>
         <str name="classname">solr.DirectSolrSpellChecker</str>
         <str name="distanceMeasure">
 org.apache.lucene.search.spell.JaroWinklerDistance </str>
       </lst>
     -->

    <!-- 拼写检查器，使用轮流比较,  比较类型可以是下边的其中一个 1. score (default)   2. freq (Frequency first, then score)    3. A fully qualified class name  -->
    <!--
       <lst name="spellchecker">
         <str name="name">freq</str>
         <str name="field">lowerfilt</str>
         <str name="classname">solr.DirectSolrSpellChecker</str>
         <str name="comparatorClass">freq</str>
      -->

    <!-- 拼写检查器，从文件中读取内容 -->
    <!--
       <lst name="spellchecker">
         <str name="classname">solr.FileBasedSpellChecker</str>
         <str name="name">file</str>
         <str name="sourceLocation">spellings.txt</str>
         <str name="characterEncoding">UTF-8</str>
         <str name="spellcheckIndexDir">spellcheckerFile</str>
       </lst>
      -->
  </searchComponent>
```

69.

```
  <requestHandler name="/spell" class="solr.SearchHandler" startup="lazy">
    <lst name="defaults">
      <str name="df">text</str>
      <!--  Solr会使用拼写器建议，默认的spellchecker 和wordbreak spellchecker，和将他们结合起来 -->
      <str name="spellcheck.dictionary">default</str>
      <str name="spellcheck.dictionary">wordbreak</str>
      <str name="spellcheck">on</str>
      <str name="spellcheck.extendedResults">true</str>       
      <str name="spellcheck.count">10</str>
      <str name="spellcheck.alternativeTermCount">5</str>
      <str name="spellcheck.maxResultsForSuggest">5</str>       
      <str name="spellcheck.collate">true</str>
      <str name="spellcheck.collateExtendedResults">true</str>  
      <str name="spellcheck.maxCollationTries">10</str>
      <str name="spellcheck.maxCollations">5</str>         
    </lst>
    <arr name="last-components">
      <str>spellcheck</str>
    </arr>
  </requestHandler>
```

70.云引擎组件的定义。

```
<searchComponent name="clustering"
 enable="${solr.clustering.enabled:false}" class="solr.clustering.ClusteringComponent" >
    <!-- 声明云引擎，只有一个可以定义为default -->
    <lst name="engine">
      <str name="name">lingo</str>

      <!-- 引擎算法只有下边三种
           * org.carrot2.clustering.lingo.LingoClusteringAlgorithm
           * org.carrot2.clustering.stc.STCClusteringAlgorithm
           * org.carrot2.clustering.kmeans.BisectingKMeansClusteringAlgorithm
       -->
      <str name="carrot.algorithm">org.carrot2.clustering.lingo.LingoClusteringAlgorithm</str>
      <str name="carrot.resourcesDir">clustering/carrot2</str>
    </lst>

    <!-- An example definition for the STC clustering algorithm. -->
    <lst name="engine">
      <str name="name">stc</str>
      <str name="carrot.algorithm">org.carrot2.clustering.stc.STCClusteringAlgorithm</str>
    </lst>

    <!-- An example definition for the bisecting kmeans clustering algorithm. -->
    <lst name="engine">
      <str name="name">kmeans</str>
      <str name="carrot.algorithm">org.carrot2.clustering.kmeans.BisectingKMeansClusteringAlgorithm</str>
    </lst>
  </searchComponent>
```


71.集群组件请求处理器。

```
<requestHandler name="/clustering" startup="lazy" enable="${solr.clustering.enabled:false}" class="solr.SearchHandler">
    <lst name="defaults">
      <bool name="clustering">true</bool>
      <bool name="clustering.results">true</bool>
      <!-- Field name with the logical "title" of a each document (optional) -->
      <str name="carrot.title">name</str>
      <!-- Field name with the logical "URL" of a each document (optional) -->
      <str name="carrot.url">id</str>
      <!-- Field name with the logical "content" of a each document (optional) -->
      <str name="carrot.snippet">features</str>
      <!-- Apply highlighter to the title/ content and use this for clustering. -->
      <bool name="carrot.produceSummary">true</bool>
      <!-- the maximum number of labels per cluster -->
      <!--<int name="carrot.numDescriptions">5</int>-->
      <!-- produce sub clusters -->
      <bool name="carrot.outputSubClusters">false</bool>

      <!-- Configure the remaining request handler parameters. -->
      <str name="defType">edismax</str>
      <str name="qf">
        text^0.5 features^1.0 name^1.2 sku^1.5 id^10.0 manu^1.1 cat^1.4
      </str>
      <str name="q.alt">*:*</str>
      <str name="rows">10</str>
      <str name="fl">*,score</str>
    </lst>
    <arr name="last-components">
      <str>clustering</str>
    </arr>
  </requestHandler>
```


72.属于组件，一个能返回术语和属于频率。

```
  <searchComponent name="terms" class="solr.TermsComponent"/>

  <!-- A request handler for demonstrating the terms component -->
  <requestHandler name="/js" class="org.apache.solr.handler.js.JavaScriptRequestHandler" startup="lazy"/>
  <requestHandler name="/terms" class="solr.SearchHandler" startup="lazy">
     <lst name="defaults">
      <bool name="terms">true</bool>
      <bool name="distrib">false</bool>
    </lst>     
    <arr name="components">
      <str>terms</str>
    </arr>
  </requestHandler>
```


73.能够让你配置最上边的数据，而忽略lucence的得分

```
<searchComponent name="elevator" class="solr.QueryElevationComponent" >
    <!-- pick a fieldType to analyze queries -->
    <str name="queryFieldType">string</str>
    <str name="config-file">elevate.xml</str>
  </searchComponent>
```

74.高亮组件

```
<searchComponent class="solr.HighlightComponent" name="highlight">
    <highlighting>
标准配置
      <fragmenter name="gap" default="true" class="solr.highlight.GapFragmenter">
        <lst name="defaults">
          <int name="hl.fragsize">100</int>
        </lst>
      </fragmenter>

基于正则表达式的分段
      <fragmenter name="regex" class="solr.highlight.RegexFragmenter">
        <lst name="defaults">
          <!-- slightly smaller fragsizes work better because of slop -->
          <int name="hl.fragsize">70</int>
          <!-- allow 50% slop on fragment sizes -->
          <float name="hl.regex.slop">0.5</float>
          <!-- a basic sentence pattern -->
          <str name="hl.regex.pattern">[-\w ,/\n\&quot;&apos;]{20,200}</str>
        </lst>
      </fragmenter>

      <!-- Configure the standard formatter -->
      <formatter name="html"
                 default="true"
                 class="solr.highlight.HtmlFormatter">
        <lst name="defaults">
          <str name="hl.simple.pre"><![CDATA[<em>]]></str>
          <str name="hl.simple.post"><![CDATA[</em>]]></str>
        </lst>
      </formatter>

标准编码器配置
      <encoder name="html" class="solr.highlight.HtmlEncoder" />      
      <fragListBuilder name="simple" class="solr.highlight.SimpleFragListBuilder"/>      
      <fragListBuilder name="single"  class="solr.highlight.SingleFragListBuilder"/>
      <fragListBuilder name="weighted"  default="true" class="solr.highlight.WeightedFragListBuilder"/>
      <fragmentsBuilder name="default" default="true" class="solr.highlight.ScoreOrderFragmentsBuilder">
        <!--
        <lst name="defaults">
          <str name="hl.multiValuedSeparatorChar">/</str>
        </lst>
        -->
      </fragmentsBuilder>

多颜色标记分段构造器
      <fragmentsBuilder name="colored" class="solr.highlight.ScoreOrderFragmentsBuilder">
        <lst name="defaults">
          <str name="hl.tag.pre"><![CDATA[
               <b style="background:yellow">,<b style="background:lawgreen">,
               <b style="background:aquamarine">,<b style="background:magenta">,
               <b style="background:palegreen">,<b style="background:coral">,
               <b style="background:wheat">,<b style="background:khaki">,
               <b style="background:lime">,<b style="background:deepskyblue">]]></str>
          <str name="hl.tag.post"><![CDATA[</b>]]></str>
        </lst>
      </fragmentsBuilder>

      <boundaryScanner name="default" default="true" class="solr.highlight.SimpleBoundaryScanner">
        <lst name="defaults">
          <str name="hl.bs.maxScan">10</str>
          <str name="hl.bs.chars">.,!? &#9;&#10;&#13;</str>
        </lst>
      </boundaryScanner>

      <boundaryScanner name="breakIterator" class="solr.highlight.BreakIteratorBoundaryScanner">
        <lst name="defaults">
          <!-- type should be one of CHARACTER, WORD(default), LINE and SENTENCE -->
          <str name="hl.bs.type">WORD</str>
          <!-- language and country are used when constructing Locale object.  -->
          <!-- And the Locale object will be used when getting instance of BreakIterator -->
          <str name="hl.bs.language">en</str>
          <str name="hl.bs.country">US</str>
        </lst>
      </boundaryScanner>
    </highlighting>
  </searchComponent>

```
