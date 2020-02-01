---
title: HBase基础操作(JAVA版)
date: 2019-12-15 22:07:23
category: HBase
tag: HBase
---

```java
public class HBaseTest {

    private Configuration conf;
    private Connection conn;

    @Before
    public void getConfigAndConnection() throws IOException {
        conf = HBaseConfiguration.create();
        // qurom与clientPort默认属性位于hbase-common包中hbase-default.xml中
        conf.set("hbase.zookeeper.quorum", "192.168.153.171");
        conf.set("hbase.zookeeper.property.clientPort", "2181");
        // 设置重试次数,第一次连接为了及时打印结果，重试1次
        conf.set("hbase.client.retries.number", "1");
        conn = ConnectionFactory.createConnection(conf);
    }

    @Test
    public void testCreateTable() throws IOException {
        Admin admin = conn.getAdmin();
        if (!admin.isTableAvailable(TableName.valueOf("testtable"))) {
            TableName tableName = TableName.valueOf("testtable");
            TableDescriptorBuilder tableDescriptorBuilder = TableDescriptorBuilder.newBuilder(tableName);
            // 获取列族描述器
            ColumnFamilyDescriptor columnFamilyDescriptor = ColumnFamilyDescriptorBuilder.newBuilder(Bytes.toBytes("user")).build();
            tableDescriptorBuilder.setColumnFamily(columnFamilyDescriptor);
            // 获取表描述器
            TableDescriptor tableDescriptor = tableDescriptorBuilder.build();
            admin.createTable(tableDescriptor);
        }
    }

    @Test
    public void testPuts() throws IOException {
        List<Put> puts = new ArrayList<>();

        Put putOne = new Put(Bytes.toBytes("row1"));
        putOne.addColumn(Bytes.toBytes("user"), Bytes.toBytes("qua1"), Bytes.toBytes("shenjian"));
        puts.add(putOne);

        Put putTwo = new Put(Bytes.toBytes("row1"));
        putTwo.addColumn(Bytes.toBytes("user"), Bytes.toBytes("qual2"), Bytes.toBytes("domi"));
        puts.add(putTwo);

        Table table = conn.getTable(TableName.valueOf("testtable"));
        table.put(puts);
    }

    @Test
    public void testCAS() throws IOException {
        Put put = new Put(Bytes.toBytes("row1"));
        put.addColumn(Bytes.toBytes("user"), Bytes.toBytes("qua1"), Bytes.toBytes("shenjian"));

        Table table = conn.getTable(TableName.valueOf("testtable"));
        table.checkAndMutate(Bytes.toBytes("row1"), Bytes.toBytes("user"))
                .qualifier(Bytes.toBytes("qual"))
                .ifNotExists()
                .thenPut(put);
    }

    @Test
    public void testGet() throws IOException {
        Table table = conn.getTable(TableName.valueOf("testtable"));
        Get get = new Get(Bytes.toBytes("row1"));
        get.addColumn(Bytes.toBytes("user"), Bytes.toBytes("qua1"));

        Result result = table.get(get);
        byte[] val = result.getValue(Bytes.toBytes("user"), Bytes.toBytes("qua1"));
        Assert.assertEquals("shenjian", Bytes.toString(val));
    }

    @Test
    public void testDelete() throws IOException {
        Table table = conn.getTable(TableName.valueOf("testtable"));
        Delete delete = new Delete(Bytes.toBytes("row1"));
        delete.addColumns(Bytes.toBytes("user"), Bytes.toBytes("qual2"), 1576391337431l);
        table.delete(delete);
        table.close();
    }

    @Test
    public void testScan() throws IOException {
        Table table = conn.getTable(TableName.valueOf("testtable"));
        Scan scan = new Scan();
        ResultScanner resultScanner = table.getScanner(scan);
        scan.addColumn(Bytes.toBytes("user"), Bytes.toBytes("name"));
        for (Result res : resultScanner) {
            System.out.println(res);
        }
    }

    @After
    public void close() throws IOException {
        conn.close();
    }
}
```
