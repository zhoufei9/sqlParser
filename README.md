## 介绍：sqlParse项目主要是解析sql语句返回表名，数据库类型，操作类型，where条件等信息。支持mysql,oracle,db2数据库类型的sql语句。

## 一、测试样例：成功返回code代码为0，错误code代码为1000
### 1.insert操作
    
    @Test
	public void testMysqlInsert(){
		String sql = "insert into tbl_person  (id,name,age) values ('1001','mmtrix',24)";//单条sql入库 显式column
		//String sql = "insert into tbl_person   values ('1001','mmtrix',24)";//单条sql入库 不显示column
		//String sql = "insert into tbl_person  (id,name,age) values ('1001','mmtrix',24),('1002','test',22),('1003','gosun',25)";//批量入库
		String dbType = "mysql";
		SqlStatementVO statementVO = SqlParserUtil.querySqlParser(sql, dbType);
		String str = JsonUtil.object2JsonString(statementVO);
		System.out.println(str);
	}
	
    成功返回结果：
    {
        "code":0,
        "result":{
            "databaseType":"mysql",
            "operateType":"insert",
            "insertItemsMap":{
                "values":[
                    "'1001'",
                    "'mmtrix'",
                    "24"
                ],
                "columns":[
                    "id",
                    "name",
                    "age"
                ]   
            },
            "tableName":"tbl_person",
            "updateItemsMap":{

            },
            "whereCondition":[

            ]
        },
        "msg":""
    }
    错误返回结果：
    {
        "code":1000,
        "result":"",
        "msg":"illegal sql:insertinto tbl_person (id,name,age) values ('1001','mmtrix',24)"
    }
    
	
### 2.update操作 
   
    @Test
	public void testDB2Update(){
		//String sql = "update tbl_person t set name = 'mmtrix',age = 25 ,email = '764182087@qq.com' where id = '1001' and sex = 'M'";
		String sql = "update tbl_person t set (name,age,email) = ('mmtrix','25','764182087@qq.com') where id = '1001' and sex = 'M'";
		String dbType = "db2";
		SqlStatementVO statementVO = SqlParserUtil.querySqlParser(sql, dbType);
		String str = JsonUtil.object2JsonString(statementVO);
		System.out.println(str);
	}

#### （1）类似mysql 
        update tbl_person t set name = 'mmtrix',age = 25 ,email = '764182087@qq.com' where id = '1001' and sex = 'M',返回结果为：
        {
            "code":0,
            "result":{
                "databaseType":"db2",
                "operateType":"update",
                "insertItemsMap":{
    
                    },
                "tableName":"tbl_person",
                "updateItemsMap":{
                    "email":"'764182087@qq.com'",
                    "age":"25",
                    "name":"'he_jiebing'"
                    },
                "whereCondition":[
                    {
                        "key":"id",
                        "opertor":"Equality",
                        "value":"'1001'"
                    },
                    {
                        "key":"sex",
                        "opertor":"Equality",
                        "value":"'M'"
                    }
                ]
            },
            "msg":""
        }
#### （2）类似db2 
    update tbl_person t set (name,age,email) = ('mmtrix','25','764182087@qq.com') where id = '1001' and sex = 'M' 这样的sql，返回结果为：
        {
            "code":0,
            "result":{
                "databaseType":"db2",
                "operateType":"update",
                "insertItemsMap":{

                },
                "tableName":"tbl_person",
                "updateItemsMap":{
                    "email":"'764182087@qq.com'",
                    "age":"'25'",
                    "name":"'mmtrix'"
                },
                "whereCondition":[
                    {
                        "key":"id",
                        "operator":"Equality",
                        "value":"'1001'"
                    },
                    {
                        "key":"sex",
                        "operator":"Equality",
                        "value":"'M'"
                    }
                ]
            },
            "msg":""
        }

### 3.delete操作（针对多种where条件演示）
##### operator枚举类型：
    Equality("="),
    GreaterThan(">"), 
    GreaterThanOrEqual(">="), 
    LessThan("<"), 
    LessThanOrEqual("<="), 
    BetweenAnd("between and ")
    In("in")


    
    @Test
	public void testDB2Delete(){
		String sql = "delete from tbl_person where id = '1001'";//条件一
		//String sql = "delete from tbl_person where id = '1001' and name = 'mmtrix'";//条件二
		//String sql = "delete from tbl_person where id between '1001' and '1003' and name = 'mmtrix'";//条件三
		//String sql = "delete from tbl_person where id in ('1001','1002','1003' ) and name = 'mmtrix'";//条件四
		//String sql = "delete from tbl_person where id > '1001' and name = 'mmtrix'";//条件五
		//String sql = "delete from tbl_person where id = (select id from tbl_person2 where name = 'mmtrix')";//条件六
		String dbType = "db2";
		JsonResult jsonResult = SqlParserUtil.parseSqlParser(sql, dbType);
		String str = JsonUtil.object2JsonString(jsonResult);
		System.out.println(str);
	}
	
	1. 条件一返回结果：
	{
        "code":0,
        "result":{
            "databaseType":"db2",
            "operateType":"delete",
            "insertItemsMap":{

            },
            "tableName":"tbl_person",
            "updateItemsMap":{

            },
            "whereCondition":[
                {
                    "key":"id",
                    "operator":"Equality",
                    "value":"'1001'"
                }
            ]
        },
        "msg":""
    }
    2. 条件二返回结果：
    {
        "code":0,
        "result":{
            "databaseType":"db2",
            "operateType":"delete",
            "insertItemsMap":{

            },
            "tableName":"tbl_person",
            "updateItemsMap":{

            },
            "whereCondition":[
                {
                    "key":"id",
                    "operator":"Equality",
                    "value":"'1001'"
                },
                {
                    "key":"name",
                    "operator":"Equality",
                    "value":"'mmtrix'"
                }
            ]
        },
        "msg":""
    }
    3. 条件三返回结果：
    {
        "code":0,
        "result":{
            "databaseType":"db2",
            "operateType":"delete",
            "insertItemsMap":{

            },
            "tableName":"tbl_person",
            "updateItemsMap":{

            },
            "whereCondition":[
                {
                    "key":"id",
                    "operator":"BetweenAnd",
                    "value":"'1001','1003'"
                },
                {
                    "key":"name",
                    "operator":"Equality",
                    "value":"'mmtrix'"
                }
            ]
        },
        "msg":""
    }
    4. 条件四返回结果：
    {
        "code":0,
        "result":{
            "databaseType":"db2",
            "operateType":"delete",
            "insertItemsMap":{

            },
            "tableName":"tbl_person",
            "updateItemsMap":{

            },
            "whereCondition":[
                {
                    "key":"id",
                    "operator":"In",
                    "value":"'1001','1002','1003'"
                },
                {
                    "key":"name",
                    "operator":"Equality",
                    "value":"'mmtrix'"
                }
            ]
        },
        "msg":""
    }
    5. 条件五返回结果：
    {
        "code":0,
        "result":{
            "databaseType":"db2",
            "operateType":"delete",
            "insertItemsMap":{

            },
            "tableName":"tbl_person",
            "updateItemsMap":{

            },
            "whereCondition":[
                {
                    "key":"id",
                    "operator":"GreaterThan",
                    "value":"'1001'"
                },
                {
                    "key":"name",
                    "operator":"Equality",
                    "value":"'mmtrix'"
                }
            ]
        },
        "msg":""
    }
    6. 条件六返回结果：
    {
        "code":1000,
        "result":"",
        "msg":"illegal sql:delete from tbl_person where id = (select id from tbl_person2 where name = 'mmtrix')"
    }
## 二、命令行方式执行mmtrix-sql-parser.jar<br>
### 1.安装好jdk,配置相应环境变量<br>
### 2.命令行执行：java -jar mmtrix-sql-parser.jar mysql "insert into tbl_person  (id,name,age) values ('1001','mmtrix',24)" <br>
注意：<br>
#### （1）要进入mmtrix-sql-parser.jar所在目录，再执行java -jar 命令；<br>
#### （2）先是数据库类型，后是sql语句,sql必须要加双引号表示整体,中间空格表示<br>

