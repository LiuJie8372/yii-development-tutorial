# Yii Framework 开发教程(25) 数据库-Query Builder 示例

上一篇介绍 PHP 使用 DAO（数据库访问对象接口）访问数据库的方法，使用 DAO 需要程序员编写 SQL 语句，对于一些复杂的 SQL 语句，Yii 提供了 Query Builder 来帮助程序员生成 SQL 语句，Query Builder 提供了一中面向对象的方法动态创建 SQL 语句，打个不十分恰当的比较，PHP 的 DAO 和.Net 的 DAO 接口非常类型，Query builder 就有点像 LINQ 了，尽管和 LINQ 比起来功能小很多。对于一些简单的 SQL 查询，通常不需要借助于 Query Builder，比如上篇中的查询 Employee 表格。

和直接使用 SQL 语句相比，使用 Query Builder 具有下面好处：

- 支持通过程序动态创建比较复杂的 SQL 查询.
- 自动为创建的 SQL 语句中的表名，列表添加引号以避免和 SQL 保留的标志符产生冲突.
- 指定为参数添加引号并尽可能的使用参数绑定以减小 SQL Injection 的风险。.
- 使用 Query Builder 不直接编写 SQL 语句，而提供了一定程度上的数据库抽象，从而为切换数据库类型提供了便利。

本例查询 Chinook 的两个表 Customer 和 Employee, 查询 EmployeeId=4 管理的所有客户的联系信息。

如果使用 SQL 查询，可以写作：

```

    SELECT c.FirstName, c.LastName , c.Address,c.Email
    FROM customer c
    INNER JOIN
    employee e
    ON c.SupportRepId=e.EmployeeId
    WHERE e.EmployeeId=4

```

本例使用 Query Builder 创建 SQL 查询，修改 SiteController 的 indexAction 方法：

```

    public function actionIndex()
    {
    
    	$model = array();
    	$connection=Yii::app()->db;
    	$command=$connection->createCommand()
    		->select('c.FirstName, c.LastName, c.Address,c.Email')
    		->from('customer c')
    		->join('employee e','c.SupportRepId=e.EmployeeId')
    		->where('e.EmployeeId=4');
    
    	$dataReader=$command->query();
    
    	// each $row is an array representing a row of data
    	foreach($dataReader as $row)
    	{
    		$customer= new DataModel();
    
    		$customer->firstName=$row['FirstName'];
    		$customer->lastName=$row['LastName'];
    
    		$customer->address=$row['Address'];
    		$customer->email=$row['Email'];
    
    		$model[]=$customer;
    	}
    
    	$this->render('index', array(
    		'model' => $model,
    
    		));
    }

```

可以看到 Query Builder 也是使用 [CDbCommand](http://www.yiiframework.com/doc/api/1.1/CDbCommand) ， [CDbCommand](http://www.yiiframework.com/doc/api/1.1/CDbCommand) 提供了如下查询数据的方法：

- [select()](http://www.yiiframework.com/doc/api/1.1/CDbCommand#select())
- [selectDistinct()](http://www.yiiframework.com/doc/api/1.1/CDbCommand#selectDistinct)
- [from()](http://www.yiiframework.com/doc/api/1.1/CDbCommand#from())
- [where()](http://www.yiiframework.com/doc/api/1.1/CDbCommand#where())
- [join()](http://www.yiiframework.com/doc/api/1.1/CDbCommand#join())
- [leftJoin()](http://www.yiiframework.com/doc/api/1.1/CDbCommand#leftJoin)
- [rightJoin()](http://www.yiiframework.com/doc/api/1.1/CDbCommand#rightJoin)
- [crossJoin()](http://www.yiiframework.com/doc/api/1.1/CDbCommand#crossJoin)
- [naturalJoin()](http://www.yiiframework.com/doc/api/1.1/CDbCommand#naturalJoin)
- [group()](http://www.yiiframework.com/doc/api/1.1/CDbCommand#group())
- [having()](http://www.yiiframework.com/doc/api/1.1/CDbCommand#having())
- [order()](http://www.yiiframework.com/doc/api/1.1/CDbCommand#order())
- [limit()](http://www.yiiframework.com/doc/api/1.1/CDbCommand#limit())
- [offset()](http://www.yiiframework.com/doc/api/1.1/CDbCommand#offset())
- [union()](http://www.yiiframework.com/doc/api/1.1/CDbCommand#union())

此外数据定义方法：

- [createTable()](http://www.yiiframework.com/doc/api/1.1/CDbCommand#createTable)
- [renameTable()](http://www.yiiframework.com/doc/api/1.1/CDbCommand#renameTable)
- [dropTable()](http://www.yiiframework.com/doc/api/1.1/CDbCommand#dropTable)
- [truncateTable()](http://www.yiiframework.com/doc/api/1.1/CDbCommand#truncateTable)
- [addColumn()](http://www.yiiframework.com/doc/api/1.1/CDbCommand#addColumn)
- [renameColumn()](http://www.yiiframework.com/doc/api/1.1/CDbCommand#renameColumn)
- [alterColumn()](http://www.yiiframework.com/doc/api/1.1/CDbCommand#alterColumn)
- [dropColumn()](http://www.yiiframework.com/doc/api/1.1/CDbCommand#dropColumn)
- [createIndex()](http://www.yiiframework.com/doc/api/1.1/CDbCommand#createIndex)
- [dropIndex()](http://www.yiiframework.com/doc/api/1.1/CDbCommand#dropIndex)

可以看到 CDbCommand 支持的方法基本上和 SQL 语句的关键字一一对应，使不使用 Query Builder 全凭个人爱好，对应简单的 SQL 语句可以直接使用 SQL 语句，对于较复杂的查询可以借助于 Query Builder。

本例显示结果：

![picture25.1](images/25.1.jpg)

本例[下载](http://www.imobilebbs.com/download/yii/QueryBuilderDemo.zip)

Tags: [PHP](http://www.imobilebbs.com/wordpress/archives/tag/php), [Yii](http://www.imobilebbs.com/wordpress/archives/tag/yii)