## idea远程连接hbase

### 下载idea

​		官网下载，官网下载链接：https://www.jetbrains.com/zh-cn/idea/download/other.html

​		不建议下载最新版本，安装idea的相关教程可在网上查找

​		切记：运行idea时需先启动  zookeeper  hadoop  hbase

### 1.更改windows的配置文件hosts

​	注意自己是集群还是单机

​	位置：C:\Windows\System32\drivers\etc\hosts

​	注意：可能不能修改，需更改系统权限，方式如下，勾选允许完全控制。

​	![image-20230411204317387](https://gitee.com/syj_123/idea-remote-connection---hbase/raw/master/img/202304131805902.png)

![image-20230413180757525](https://gitee.com/syj_123/idea-remote-connection---hbase/raw/master/img/202304131807595.png)

### 2.在idea下创建一个maven模块    (jdk1.8)

​	注意maven不建议使用idea自带的，可从官网下载对应版本

​			链接：https://maven.apache.org/download.cgi

![image-20230413180919369](https://gitee.com/syj_123/idea-remote-connection---hbase/raw/master/img/202304131809452.png)

![image-20230409213208371](https://gitee.com/syj_123/idea-remote-connection---hbase/raw/master/img/202304131808748.png)

​	注意：记得修改maven位置

​		>setting>build....>Maven

![image-20230413180939275](https://gitee.com/syj_123/idea-remote-connection---hbase/raw/master/img/202304131809375.png)

### 3.在pom文件中导入相关配置文件(hadoop/hbase/hive.....)

​		注意：1.导入时版本需和虚拟机安装的对应软件版本一致

​					2.只需在dependencies下添加依赖，其他不用替换，用生成的即可

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <groupId>com.it</groupId>
    <artifactId>Hbase</artifactId>
    <version>1.0-SNAPSHOT</version>

    <properties>
        <maven.compiler.source>8</maven.compiler.source>
        <maven.compiler.target>8</maven.compiler.target>
    </properties>
<!--    依赖所导入的版本需要和hadoop/hbase/hive等文件安装版本一致-->
    <dependencies>
        <!--    导入hadoop相关包 -->
        <dependency>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-hdfs</artifactId>
            <version>2.7.1</version>
        </dependency>
        <dependency>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-client</artifactId>
            <version>2.7.1</version>
        </dependency>
        <dependency>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-common</artifactId>
            <version>2.7.1</version>
        </dependency>
        <!--    导入hbase相关包  -->
        <dependency>
            <groupId>org.apache.hbase</groupId>
            <artifactId>hbase-server</artifactId>
            <version>1.1.2</version>
        </dependency>
        <dependency>
            <groupId>org.apache.hbase</groupId>
            <artifactId>hbase-client</artifactId>
            <version>1.1.2</version>
        </dependency>
<!--        导入hive相关包-->
<!--        <dependency>-->
<!--            <groupId>org.apache.hive</groupId>-->
<!--            <artifactId>hive-exec</artifactId>-->
<!--            <version>1.2.2</version>-->
<!--        </dependency>-->
<!--        <dependency>-->
<!--            <groupId>org.apache.hive</groupId>-->
<!--            <artifactId>hive-jdbc</artifactId>-->
<!--            <version>1.2.2</version>-->
<!--        </dependency>-->
    </dependencies>
</project>
```

### 4.将相关文件导入

​	将Hbase下的hbase-site.xml   log4j.properties文件以及hadoop下的core-site.xml文件导入maven下的resources目录下

#### 	将虚拟机文件导入本机方法（ubantu)

![image-20230413181001453](https://gitee.com/syj_123/idea-remote-connection---hbase/raw/master/img/202304131810557.png)

![image-20230413181030429](https://gitee.com/syj_123/idea-remote-connection---hbase/raw/master/img/202304131810509.png)

![image-20230413181051188](https://gitee.com/syj_123/idea-remote-connection---hbase/raw/master/img/202304131810248.png)

![image-20230409170208484](https://gitee.com/syj_123/idea-remote-connection---hbase/raw/master/img/202304131808038.png)

### 5.编写Java类

​		注意更改配置信息

```java
package com.it.Hbase;

import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.hbase.*;
import org.apache.hadoop.hbase.client.*;
import java.io.IOException;

public class ExampleForHbase {
    // 配置类，用于配置相关信息
    public static Configuration configuration;
    // 连接类，用于连接hbase数据库
    public static Connection connection;

    public static Admin admin;

    // 主函数中的语句请逐句执行，只需删除其前的//即可，如：执行insertRow时请将其他语句注释
    public static void main(String[] args) throws IOException {
        // 创建一个表，表名为Score，列族为sname,course
        // createTable("Score", new String[] { "sname", "course" });

        // 在Score表中插入一条数据，其行键为95001,sname为Mary（因为sname列族下没有子列所以第四个参数为空）
        // 等价命令：put 'Score','95001','sname','Mary'
        // insertRow("Score", "95001", "sname", "", "Mary");
        // 在Score表中插入一条数据，其行键为95001,course:Math为88（course为列族，Math为course下的子列）
        // 等价命令：put 'Score','95001','score:Math','88'
        // insertRow("Score", "95001", "course", "Math", "88");
        // 在Score表中插入一条数据，其行键为95001,course:English为85（course为列族，English为course下的子列）
        // 等价命令：put 'Score','95001','score:English','85'
        // insertRow("Score", "95001", "course", "English", "85");

        // 1、删除Score表中指定列数据，其行键为95001,列族为course，列为Math
        // 执行这句代码前请deleteRow方法的定义中，将删除指定列数据的代码取消注释注释，将删除制定列族的代码注释
        // 等价命令：delete 'Score','95001','score:Math'
        // deleteRow("Score", "95001", "course", "Math");

        // 2、删除Score表中指定列族数据，其行键为95001,列族为course（95001的Math和English的值都会被删除）
        // 执行这句代码前请deleteRow方法的定义中，将删除指定列数据的代码注释，将删除制定列族的代码取消注释
        // 等价命令：delete 'Score','95001','score'
        // deleteRow("Score", "95001", "course", "");

        // 3、删除Score表中指定行数据，其行键为95001
        // 执行这句代码前请deleteRow方法的定义中，将删除指定列数据的代码注释，以及将删除制定列族的代码注释
        // 等价命令：deleteall 'Score','95001'
        // deleteRow("Score", "95001", "", "");

        // 4、查询Score表中，行键为95001，列族为course，列为Math的值
        // getData("Score", "95001", "course", "Math");
        // 查询Score表中，行键为95001，列族为sname的值（因为sname列族下没有子列所以第四个参数为空）
        // getData("Score", "95001", "sname", "");
        // getData("student", "001", "Sname", "");

        // 5、查询所有表
        // listTables();

        // 6、查询表的所有信息
        // scanData("student");
        // scanData("test1");

        // 7、删除Score表
        // deleteTable("Score");
    }

    // 建立连接
    public static void init() {
        // 创建一个hbase的配置类
        configuration = HBaseConfiguration.create();
        // 设置将数据写入hdfs的存储位置
        configuration.set("hbase.rootdir", "hdfs://master:9000/hbase");
        try {
            // 创建一个连接
            connection = ConnectionFactory.createConnection(configuration);
            admin = connection.getAdmin();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    // 关闭连接
    public static void close() {
        try {
            if (admin != null) {
                admin.close();
            }
            if (null != connection) {
                connection.close();
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    /**
     * 建表。HBase的表中会有一个系统默认的属性作为主键，主键无需自行创建，默认为put命令操作中表名后第一个数据，因此此处无需创建id列
     *
     * @param myTableName
     *            表名
     * @param colFamily
     *            列族名
     * @throws IOException
     */
    public static void createTable(String myTableName, String[] colFamily)
        throws IOException {
        // 初始化，建立连接
        init();
        // 创建表名
        TableName tableName = TableName.valueOf(myTableName);
        // 判断hbase数据库中是否存在该表
        if (admin.tableExists(tableName)) {
            System.out.println("table is exists!");
        } else {
            HTableDescriptor hTableDescriptor = new HTableDescriptor(tableName);
            for (String str : colFamily) {
                HColumnDescriptor hColumnDescriptor = new HColumnDescriptor(str);
                hTableDescriptor.addFamily(hColumnDescriptor);
            }
            admin.createTable(hTableDescriptor);
            System.out.println("create table success");
        }
        close();
    }

    /**
     * 删除指定表
     *
     * @param tableName
     *            表名
     * @throws IOException
     */
    public static void deleteTable(String tableName) throws IOException {
        init();
        TableName tn = TableName.valueOf(tableName);
        if (admin.tableExists(tn)) {
            admin.disableTable(tn);
            admin.deleteTable(tn);
        }
        close();
    }

    /**
     * 查看已有表
     *
     * @throws IOException
     */
    public static void listTables() throws IOException {
        init();
        HTableDescriptor hTableDescriptors[] = admin.listTables();
        for (HTableDescriptor hTableDescriptor : hTableDescriptors) {
            System.out.println(hTableDescriptor.getNameAsString());
        }
        close();
    }

    public static void scanData(String tableName) throws IOException {
        init();
        Table table = connection.getTable(TableName.valueOf(tableName));

        Scan scan = new Scan();

        ResultScanner scanner = table.getScanner(scan);

        for (Result result :
                scanner) {
            showCell(result);
        }

        close();
    }

    /**
     * 向某一行的某一列插入数据
     *
     * @param tableName
     *            表名
     * @param rowKey
     *            行键
     * @param colFamily
     *            列族名
     * @param col
     *            列名（如果其列族下没有子列，此参数可为空）
     * @param val
     *            值
     * @throws IOException
     */
    public static void insertRow(String tableName, String rowKey,
                                 String colFamily, String col, String val) throws IOException {
        init();
        Table table = connection.getTable(TableName.valueOf(tableName));
        Put put = new Put(rowKey.getBytes());
        put.addColumn(colFamily.getBytes(), col.getBytes(), val.getBytes());
        table.put(put);
        table.close();
        close();
    }

    /**
     * 删除数据
     *
     * @param tableName
     *            表名
     * @param rowKey
     *            行键
     * @param colFamily
     *            列族名
     * @param col
     *            列名
     * @throws IOException
     */
    public static void deleteRow(String tableName, String rowKey,
                                 String colFamily, String col) throws IOException {
        init();
        Table table = connection.getTable(TableName.valueOf(tableName));
        Delete delete = new Delete(rowKey.getBytes());
        // 删除指定列族的所有数据
        // delete.addFamily(colFamily.getBytes());
        // 删除指定列的数据
        // delete.addColumn(colFamily.getBytes(), col.getBytes());

        table.delete(delete);
        table.close();
        close();
    }

    /**
     * 根据行键rowkey查找数据
     *
     * @param tableName
     *            表名
     * @param rowKey
     *            行键
     * @param colFamily
     *            列族名
     * @param col
     *            列名
     * @throws IOException
     */
    public static void getData(String tableName, String rowKey,
                               String colFamily, String col) throws IOException {
        init();
        Table table = connection.getTable(TableName.valueOf(tableName));
        Get get = new Get(rowKey.getBytes());
        get.addColumn(colFamily.getBytes(), col.getBytes());
        Result result = table.get(get);
        showCell(result);
        table.close();
        close();
    }

    /**
     * 格式化输出
     *
     * @param result
     */
    public static void showCell(Result result) {
        Cell[] cells = result.rawCells();
        for (Cell cell : cells) {
            System.out.print("RowName:" + new String(CellUtil.cloneRow(cell))
                    + " ");
            System.out.print("Timetamp:" + cell.getTimestamp() + " ");
            System.out.print("column Family:"
                    + new String(CellUtil.cloneFamily(cell)) + " ");
            System.out.print("row Name:"
                    + new String(CellUtil.cloneQualifier(cell)) + " ");
            System.out.println("value:" + new String(CellUtil.cloneValue(cell))
                    + " ");

        }
    }
}
```

### 6.可能遇到的问题

#### 目前遇到的问题

![Snipaste_2023-04-09_16-13-56](https://gitee.com/syj_123/idea-remote-connection---hbase/raw/master/img/202304131811208.png)

##### 解决方法

​		链接：https://blog.csdn.net/qq_39141486/article/details/97828664   （也可看下边）

###### 		1）下载winutils.exe 和hadoop.dll文件到hadoop的bin目录下

​			注意：该处hadoop的路径是指    虚拟机    的安装路径（安装时取的名字可能不同）

​			下载地址：https://github.com/cdarlint/winutils   （截至目前可下载 2023/04/09) 链接为GitHub地址，可能不好进

![image-20230409170852149](https://gitee.com/syj_123/idea-remote-connection---hbase/raw/master/img/202304131811927.png)

###### 		2)配置环境变量

​	注意：在路径中不建议使用中文以及空格

![image-20230413181219843](https://gitee.com/syj_123/idea-remote-connection---hbase/raw/master/img/202304131812893.png)

![image-20230413181233313](https://gitee.com/syj_123/idea-remote-connection---hbase/raw/master/img/202304131812385.png)

###### 		3）重启电脑

![image-20230413181247628](https://gitee.com/syj_123/idea-remote-connection---hbase/raw/master/img/202304131812705.png)

![image-20230409171621199](https://gitee.com/syj_123/idea-remote-connection---hbase/raw/master/img/202304131811954.png)

### 其他问题

​		参考链接：https://blog.csdn.net/yetaodiao/article/details/127371246