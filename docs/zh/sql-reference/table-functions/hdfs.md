---
machine_translated: true
machine_translated_rev: 72537a2d527c63c07aa5d2361a8829f3895cf2bd
toc_priority: 45
toc_title: hdfs
---

# hdfs {#hdfs}

从HDFS中的文件创建一个表。这个表函数类似于[url](url.md)和 [文件](file.md)函数

``` sql
hdfs(URI, format, structure)
```

**输入参数**

-   `URI` — 在HDFS文件中的相对URI。在只读模式下，文件路径支持以下globs（英文：Path to file support following globs in readonly mode）：`*`, `?`, `{abc,def}` and `{N..M}` where `N`, `M` — numbers, \``'abc', 'def'` — strings.
-   `format` — 文件[格式](../../interfaces/formats.md#formats)。
-   `structure` — 表的结构。 格式为“column1_name column1_type，column2_name column2_type，...”。

**返回值**

具有指定结构的表，用于读取或写入指定文件中的数据。

**示例**

从 `hdfs://hdfs1:9000/test`表中选择前两行:

``` sql
SELECT *
FROM hdfs('hdfs://hdfs1:9000/test', 'TSV', 'column1 UInt32, column2 UInt32, column3 UInt32')
LIMIT 2
```

``` text
┌─column1─┬─column2─┬─column3─┐
│       1 │       2 │       3 │
│       3 │       2 │       1 │
└─────────┴─────────┴─────────┘
```

**路径中的globs**

多个路径组件可以具有globs。 对于正在处理的文件应该存在并匹配到整个路径模式（不仅后缀或前缀）。

-   `*` — 替换任意数量的包括空字符串的任意字符串，除了 `/` 。
-   `?` — 替换任意单个字符。
-   `{some_string,another_string,yet_another_one}` — 替换任意字符串 `'some_string', 'another_string', 'yet_another_one'`.
-   `{N..M}` — 替换范围从N到M的任何数字（包括两个边界）。
带有 `{}` 的结构类似于[remote table function](../../sql-reference/table-functions/remote.md))

**示例**

1.  假设我们在HDFS上有几个具有以下Uri的文件：

-   ‘hdfs://hdfs1:9000/some\_dir/some\_file\_1’
-   ‘hdfs://hdfs1:9000/some\_dir/some\_file\_2’
-   ‘hdfs://hdfs1:9000/some\_dir/some\_file\_3’
-   ‘hdfs://hdfs1:9000/another\_dir/some\_file\_1’
-   ‘hdfs://hdfs1:9000/another\_dir/some\_file\_2’
-   ‘hdfs://hdfs1:9000/another\_dir/some\_file\_3’

1.  查询这些文件中的行数:

<!-- -->

``` sql
SELECT count(*)
FROM hdfs('hdfs://hdfs1:9000/{some,another}_dir/some_file_{1..3}', 'TSV', 'name String, value UInt32')
```

2.  查询这两个目录中所有文件的行数:

<!-- -->

``` sql
SELECT count(*)
FROM hdfs('hdfs://hdfs1:9000/{some,another}_dir/*', 'TSV', 'name String, value UInt32')
```

!!! 警告
    如果文件列表中包含带前导零的数字范围，则分别使用带大括号的结构或使用 `？`。

**示例**

从文件名为 `file000`, `file001`, … , `file999`:

``` sql
SELECT count(*)
FROM hdfs('hdfs://hdfs1:9000/big_dir/file{0..9}{0..9}{0..9}', 'CSV', 'name String, value UInt32')
```

## 虚拟列 {#virtual-columns}

-   `_path` — 文件路径。
-   `_file` — 文件名。

**另请参阅**

-   [虚拟列](https://clickhouse.tech/docs/en/operations/table_engines/#table_engines-virtual_columns)

[原始文章](https://clickhouse.tech/docs/en/query_language/table_functions/hdfs/) <!--hide-->
