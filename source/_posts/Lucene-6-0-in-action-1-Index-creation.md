title: Lucene 6.0 实战（1）-创建索引
date: 2016-05-20 21:25:37
tags: [Lucene]
categories: Programming Notes

---

###引言
**Lucene6.0**于**2016年4月8日**发布，要求最低Java版本是**Java 8**。

相信大多数公司的数据库都需要采用分库分表等一些策略，而对于某些特定的业务需求，分别从不同的库不同的表中去检索特定的数据显得比较繁琐，而Lucene正好可以解决某些特殊需求，对于不同库不同表中的数据先建立全量索引，然后将需要检索的数据写入某个单独的表中，供其它业务需求方查询，以后的每天只需要做增量索引并写入数据表即可。

鉴于最近一直在做Lucene相关方面的工作，而本人一向又比较喜欢使用最新发布的版本，而网络上这类资源极少，故将一些要点及示例整理出来，本文主要从实战角度来介绍Lucene 6.0的使用，不涉及过多原理方面的东西，但是对于一些核心点也会有所提及。

Lucene是一个高效的，基于Java的全文检索库，生活中数据主要分为两种：结构化数据和非结构化数据。一般使用的XML、JSON、数据库等都是结构化数据，非结构化数据也叫全文数据，而这种全文数据正是Lucene的用武之地。全文检索主要有两个过程，索引创建（Indexing）和搜索索引（Search）。

###索引存储
索引由Lucene按照特定的格式创建，而创建出来的索引必然要存储在文件系统之上，Lucene在文件系统中存储索引的最基本的类是FSDirectory，如下是该类常用的子类
- SimpleFSDirectory：使用Files.newByteChannel实现，对并发支持不好，它会在多线程读取同一份文件时进行同步操作
- NIOFSDirectory：使用Java NIO中的FileChannel去读取同一份文件，可以避免同步操作，但是由于Windows平台上存在[Sun JRE bug](http://bugs.java.com/bugdatabase/view_bug.do?bug_id=6265734)，所以在Windows平台上不推荐使用
- MMapDirectory：在读取的时候使用内存映射IO，如果你的虚拟内存足够容纳索引文件大小的话，这是一个很棒的选择
- RAMDirectory：只对小索引好，大索引会出现频繁GC

通常情况下，我们无需自己选择使用FSDirectory的某个实现子类，只要使用FSDirectory中的open(Path path)方法即可，英文doc如下：
>Creates an FSDirectory instance, trying to pick the best implementation given the current environment. The directory returned uses the NativeFSLockFactory. The directory is created at the named location if it does not yet exist.

该方法可以自动根据当前使用的系统环境而选择一个最佳的实现子类，其选择策略是
- 对于Linux，MacOSX，Solaris，Windows 64-bit JREs返回MMapDirectory
- 对于其它非Windows上的JREs，返回NIOFSDirectory
- 对于其它Windows上的JREs，返回SimpleFSDirectory

###文本分析器
对于文本分析器Analyzer，需要注意一点，就是使用哪种Analyzer进行索引创建，查询的时候也要使用哪种Analyzer查询，否则查询结果不正确。

###创建索引
索引的创建方式有三种，通过IndexWriterConfig.OpenMode进行指定，分别是
- CREATE：创建一个新的索引或者覆写已经存在的索引
- APPEND：打开一个已经存在的索引
- CREATE_OR_APPEND：如果不存在则创建新的索引，如果存在则追加索引

```java
/**
 * 创建索引写入器
 *
 * @param indexPath
 * @param create
 * @throws IOException
 */
public IndexWriter getIndexWriter(String indexPath, boolean create) throws IOException {
    IndexWriterConfig indexWriterConfig = new IndexWriterConfig(new StandardAnalyzer());
    if (create) {
        indexWriterConfig.setOpenMode(IndexWriterConfig.OpenMode.CREATE);
    } else {
        indexWriterConfig.setOpenMode(IndexWriterConfig.OpenMode.CREATE_OR_APPEND);
    }
    Directory directory = FSDirectory.open(Paths.get(indexPath));
    IndexWriter indexWriter = new IndexWriter(directory, indexWriterConfig);
    return indexWriter;
}
```
如果仅仅做测试用，还可以将索引写入内存，此时需要使用RAMDirectory
```java
public class LuceneDemo {
    private Directory directory;
    private String[] ids = {"1", "2"};
    private String[] unIndex = {"Netherlands", "Italy"};
    private String[] unStored = {"Amsterdam has lots of bridges", "Venice has lots of canals"};
    private String[] text = {"Amsterdam", "Venice"};
    private IndexWriter indexWriter;

    private IndexWriterConfig indexWriterConfig = new IndexWriterConfig(new StandardAnalyzer());

    @Test
    public void createIndex() throws IOException {
        directory = new RAMDirectory();
        //指定将索引创建信息打印到控制台
        indexWriterConfig.setInfoStream(System.out);
        indexWriter = new IndexWriter(directory, indexWriterConfig);
        indexWriterConfig = (IndexWriterConfig) indexWriter.getConfig();
        FieldType fieldType = new FieldType();
        fieldType.setIndexOptions(IndexOptions.DOCS_AND_FREQS_AND_POSITIONS);
        fieldType.setStored(true);//存储
        fieldType.setTokenized(true);//分词
        for (int i = 0; i < ids.length; i++) {
            Document document = new Document();
            document.add(new Field("id", ids[i], fieldType));
            document.add(new Field("country", unIndex[i], fieldType));
            document.add(new Field("contents", unStored[i], fieldType));
            document.add(new Field("city", text[i], fieldType));
            indexWriter.addDocument(document);
        }
        indexWriter.commit();
    }
}
```
控制台输出索引创建信息如下：
>IFD 0 [2016-05-19T07:10:21.127Z; main]: init: current segments file is "segments"; deletionPolicy=org.apache.lucene.index.KeepOnlyLastCommitDeletionPolicy@691a7f8f
IFD 0 [2016-05-19T07:10:21.167Z; main]: delete []
IFD 0 [2016-05-19T07:10:21.167Z; main]: now checkpoint "" [0 segments ; isCommit = false]
IFD 0 [2016-05-19T07:10:21.167Z; main]: delete []
IFD 0 [2016-05-19T07:10:21.167Z; main]: 0 msec to checkpoint
IW 0 [2016-05-19T07:10:21.167Z; main]: init: create=true
IW 0 [2016-05-19T07:10:21.168Z; main]: 
dir=RAMDirectory@341b80b2 lockFactory=org.apache.lucene.store.SingleInstanceLockFactory@55a1c291
index=
version=6.0.0
analyzer=org.apache.lucene.analysis.standard.StandardAnalyzer
ramBufferSizeMB=16.0
maxBufferedDocs=-1
maxBufferedDeleteTerms=-1
mergedSegmentWarmer=null
delPolicy=org.apache.lucene.index.KeepOnlyLastCommitDeletionPolicy
commit=null
openMode=CREATE_OR_APPEND
similarity=org.apache.lucene.search.similarities.BM25Similarity
mergeScheduler=ConcurrentMergeScheduler: maxThreadCount=-1, maxMergeCount=-1, ioThrottle=true
codec=Lucene60
infoStream=org.apache.lucene.util.PrintStreamInfoStream
mergePolicy=[TieredMergePolicy: maxMergeAtOnce=10, maxMergeAtOnceExplicit=30, maxMergedSegmentMB=5120.0, floorSegmentMB=2.0, forceMergeDeletesPctAllowed=10.0, segmentsPerTier=10.0, maxCFSSegmentSizeMB=8.796093022207999E12, noCFSRatio=0.1
indexerThreadPool=org.apache.lucene.index.DocumentsWriterPerThreadPool@223f3642
readerPooling=false
perThreadHardLimitMB=1945
useCompoundFile=true
commitOnClose=true
writer=org.apache.lucene.index.IndexWriter@38c5cc4c
IW 0 [2016-05-19T07:10:21.176Z; main]: MMapDirectory.UNMAP_SUPPORTED=true
IW 0 [2016-05-19T07:10:21.226Z; main]: commit: start
IW 0 [2016-05-19T07:10:21.226Z; main]: commit: enter lock
IW 0 [2016-05-19T07:10:21.226Z; main]: commit: now prepare
IW 0 [2016-05-19T07:10:21.226Z; main]: prepareCommit: flush
IW 0 [2016-05-19T07:10:21.226Z; main]:   index before flush 
DW 0 [2016-05-19T07:10:21.226Z; main]: startFullFlush
DW 0 [2016-05-19T07:10:21.226Z; main]: anyChanges? numDocsInRam=2 deletes=false hasTickets:false pendingChangesInFullFlush: false
DWFC 0 [2016-05-19T07:10:21.226Z; main]: addFlushableState DocumentsWriterPerThread [pendingDeletes=gen=0, segment=_0, aborted=false, numDocsInRAM=2, deleteQueue=DWDQ: [ generation: 0 ]]
DWPT 0 [2016-05-19T07:10:21.229Z; main]: flush postings as segment _0 numDocs=2
IW 0 [2016-05-19T07:10:21.231Z; main]: 1 msec to write norms
IW 0 [2016-05-19T07:10:21.231Z; main]: 0 msec to write docValues
IW 0 [2016-05-19T07:10:21.231Z; main]: 0 msec to write points
IW 0 [2016-05-19T07:10:21.240Z; main]: 8 msec to finish stored fields
IW 0 [2016-05-19T07:10:21.260Z; main]: 20 msec to write postings and finish vectors
IW 0 [2016-05-19T07:10:21.260Z; main]: 0 msec to write fieldInfos
DWPT 0 [2016-05-19T07:10:21.261Z; main]: new segment has 0 deleted docs
DWPT 0 [2016-05-19T07:10:21.261Z; main]: new segment has no vectors; norms; no docValues; prox; freqs
DWPT 0 [2016-05-19T07:10:21.261Z; main]: flushedFiles=[_0_Lucene50_0.doc, _0_Lucene50_0.tim, _0_Lucene50_0.pos, _0.nvd, _0.fdx, _0_Lucene50_0.tip, _0.fdt, _0.nvm, _0.fnm]
DWPT 0 [2016-05-19T07:10:21.262Z; main]: flushed codec=Lucene60
DWPT 0 [2016-05-19T07:10:21.263Z; main]: flushed: segment=_0 ramUsed=0.095 MB newFlushedSize=0.002 MB docs/MB=1,296.94
IW 0 [2016-05-19T07:10:21.263Z; main]: create compound file
DWPT 0 [2016-05-19T07:10:21.264Z; main]: flush time 34.804052 msec
DW 0 [2016-05-19T07:10:21.265Z; main]: publishFlushedSegment seg-private updates=null
IW 0 [2016-05-19T07:10:21.265Z; main]: publishFlushedSegment
IW 0 [2016-05-19T07:10:21.265Z; main]: publish sets newSegment delGen=1 seg=_0(6.0.0):c2
IFD 0 [2016-05-19T07:10:21.265Z; main]: now checkpoint "_0(6.0.0):c2" [1 segments ; isCommit = false]
IFD 0 [2016-05-19T07:10:21.265Z; main]: delete []
IFD 0 [2016-05-19T07:10:21.265Z; main]: 0 msec to checkpoint
IFD 0 [2016-05-19T07:10:21.265Z; main]: will delete new file "_0_Lucene50_0.doc"
IFD 0 [2016-05-19T07:10:21.265Z; main]: will delete new file "_0_Lucene50_0.tim"
IFD 0 [2016-05-19T07:10:21.265Z; main]: will delete new file "_0_Lucene50_0.pos"
IFD 0 [2016-05-19T07:10:21.266Z; main]: will delete new file "_0.nvd"
IFD 0 [2016-05-19T07:10:21.266Z; main]: will delete new file "_0.fdx"
IFD 0 [2016-05-19T07:10:21.266Z; main]: will delete new file "_0_Lucene50_0.tip"
IFD 0 [2016-05-19T07:10:21.266Z; main]: will delete new file "_0.fdt"
IFD 0 [2016-05-19T07:10:21.266Z; main]: will delete new file "_0.nvm"
IFD 0 [2016-05-19T07:10:21.266Z; main]: will delete new file "_0.fnm"
IFD 0 [2016-05-19T07:10:21.266Z; main]: delete [_0_Lucene50_0.doc, _0_Lucene50_0.tim, _0_Lucene50_0.pos, _0.nvd, _0.fdx, _0_Lucene50_0.tip, _0.fdt, _0.nvm, _0.fnm]
IW 0 [2016-05-19T07:10:21.266Z; main]: apply all deletes during flush
IW 0 [2016-05-19T07:10:21.266Z; main]: now apply all deletes for all segments maxDoc=2
BD 0 [2016-05-19T07:10:21.267Z; main]: applyDeletes: open segment readers took 0 msec
BD 0 [2016-05-19T07:10:21.267Z; main]: applyDeletes: no segments; skipping
BD 0 [2016-05-19T07:10:21.267Z; main]: prune sis=segments: _0(6.0.0):c2 minGen=1 packetCount=0
DW 0 [2016-05-19T07:10:21.267Z; main]: main finishFullFlush success=true
TMP 0 [2016-05-19T07:10:21.267Z; main]: findMerges: 1 segments
TMP 0 [2016-05-19T07:10:21.268Z; main]:   seg=_0(6.0.0):c2 size=0.002 MB [floored]
TMP 0 [2016-05-19T07:10:21.268Z; main]:   allowedSegmentCount=1 vs count=1 (eligible count=1) tooBigCount=0
MS 0 [2016-05-19T07:10:21.268Z; main]: initDynamicDefaults spins=false maxThreadCount=4 maxMergeCount=9
MS 0 [2016-05-19T07:10:21.268Z; main]: now merge
MS 0 [2016-05-19T07:10:21.269Z; main]:   index: _0(6.0.0):c2
MS 0 [2016-05-19T07:10:21.269Z; main]:   no more merges pending; now return
IW 0 [2016-05-19T07:10:21.269Z; main]: startCommit(): start
IW 0 [2016-05-19T07:10:21.269Z; main]: startCommit index=_0(6.0.0):c2 changeCount=4
IW 0 [2016-05-19T07:10:21.269Z; main]: startCommit: wrote pending segments file "pending_segments_1"
IW 0 [2016-05-19T07:10:21.269Z; main]: done all syncs: [_0.cfe, _0.si, _0.cfs]
IW 0 [2016-05-19T07:10:21.269Z; main]: commit: pendingCommit != null
IW 0 [2016-05-19T07:10:21.269Z; main]: commit: done writing segments file "segments_1"
IFD 0 [2016-05-19T07:10:21.269Z; main]: now checkpoint "_0(6.0.0):c2" [1 segments ; isCommit = true]
IFD 0 [2016-05-19T07:10:21.270Z; main]: 0 msec to checkpoint
IFD 0 [2016-05-19T07:10:21.270Z; main]: delete []
IW 0 [2016-05-19T07:10:21.270Z; main]: commit: took 44.0 msec
IW 0 [2016-05-19T07:10:21.270Z; main]: commit: done
IW 0 [2016-05-19T07:10:21.270Z; main]: now flush at close
IW 0 [2016-05-19T07:10:21.270Z; main]:   start flush: applyAllDeletes=true
IW 0 [2016-05-19T07:10:21.270Z; main]:   index before flush _0(6.0.0):c2
DW 0 [2016-05-19T07:10:21.270Z; main]: startFullFlush
DW 0 [2016-05-19T07:10:21.270Z; main]: main finishFullFlush success=true
IW 0 [2016-05-19T07:10:21.270Z; main]: apply all deletes during flush
IW 0 [2016-05-19T07:10:21.270Z; main]: now apply all deletes for all segments maxDoc=2
BD 0 [2016-05-19T07:10:21.270Z; main]: applyDeletes: open segment readers took 0 msec
BD 0 [2016-05-19T07:10:21.270Z; main]: applyDeletes: no segments; skipping
BD 0 [2016-05-19T07:10:21.270Z; main]: prune sis=segments_1: _0(6.0.0):c2 minGen=1 packetCount=0
MS 0 [2016-05-19T07:10:21.270Z; main]: updateMergeThreads ioThrottle=true targetMBPerSec=10240.0 MB/sec
MS 0 [2016-05-19T07:10:21.270Z; main]: now merge
MS 0 [2016-05-19T07:10:21.270Z; main]:   index: _0(6.0.0):c2
MS 0 [2016-05-19T07:10:21.271Z; main]:   no more merges pending; now return
IW 0 [2016-05-19T07:10:21.271Z; main]: waitForMerges
IW 0 [2016-05-19T07:10:21.271Z; main]: waitForMerges done
IW 0 [2016-05-19T07:10:21.271Z; main]: commit: start
IW 0 [2016-05-19T07:10:21.271Z; main]: commit: enter lock
IW 0 [2016-05-19T07:10:21.271Z; main]: commit: now prepare
IW 0 [2016-05-19T07:10:21.271Z; main]: prepareCommit: flush
IW 0 [2016-05-19T07:10:21.271Z; main]:   index before flush _0(6.0.0):c2
DW 0 [2016-05-19T07:10:21.271Z; main]: startFullFlush
IW 0 [2016-05-19T07:10:21.271Z; main]: apply all deletes during flush
IW 0 [2016-05-19T07:10:21.271Z; main]: now apply all deletes for all segments maxDoc=2
BD 0 [2016-05-19T07:10:21.271Z; main]: applyDeletes: open segment readers took 0 msec
BD 0 [2016-05-19T07:10:21.271Z; main]: applyDeletes: no segments; skipping
BD 0 [2016-05-19T07:10:21.271Z; main]: prune sis=segments_1: _0(6.0.0):c2 minGen=1 packetCount=0
DW 0 [2016-05-19T07:10:21.271Z; main]: main finishFullFlush success=true
IW 0 [2016-05-19T07:10:21.271Z; main]: startCommit(): start
IW 0 [2016-05-19T07:10:21.271Z; main]:   skip startCommit(): no changes pending
IFD 0 [2016-05-19T07:10:21.271Z; main]: delete []
IW 0 [2016-05-19T07:10:21.271Z; main]: commit: pendingCommit == null; skip
IW 0 [2016-05-19T07:10:21.271Z; main]: commit: took 0.4 msec
IW 0 [2016-05-19T07:10:21.271Z; main]: commit: done
IW 0 [2016-05-19T07:10:21.271Z; main]: rollback
IW 0 [2016-05-19T07:10:21.271Z; main]: all running merges have aborted
IW 0 [2016-05-19T07:10:21.271Z; main]: rollback: done finish merges
DW 0 [2016-05-19T07:10:21.271Z; main]: abort
DW 0 [2016-05-19T07:10:21.271Z; main]: done abort success=true
IW 0 [2016-05-19T07:10:21.271Z; main]: rollback: infos=_0(6.0.0):c2
IFD 0 [2016-05-19T07:10:21.271Z; main]: now checkpoint "_0(6.0.0):c2" [1 segments ; isCommit = false]
IFD 0 [2016-05-19T07:10:21.272Z; main]: delete []
IFD 0 [2016-05-19T07:10:21.272Z; main]: 0 msec to checkpoint
IFD 0 [2016-05-19T07:10:21.272Z; main]: delete []
IFD 0 [2016-05-19T07:10:21.272Z; main]: delete []