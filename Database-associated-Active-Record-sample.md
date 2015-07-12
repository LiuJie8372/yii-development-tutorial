# Yii Framework 开发教程(27) 数据库-关联 Active Record 示例

我们已经了解了怎样使用 Active Record (AR) 从单个数据表中获取数据。 在本节中，我们讲解怎样使用 AR 连接多个相关数据表并取回关联（join）后的数据集。

为了使用关系型 AR，我们建议在需要关联的表中定义主键-外键约束。这些约束可以帮助保证相关数据的一致性和完整性。

本例通过修改 [Yii Framework 开发教程(25) 数据库-Query Builder 示例](http://www.imobilebbs.com/wordpress/archives/4041)来介绍多个有关系的表如何使用 Active Record。

在我们使用 AR 执行关联查询之前，我们需要让 AR 知道一个 AR 类是怎样关联到另一个的。

两个 AR 类之间的关系直接通过 AR 类所代表的数据表之间的关系相关联。 从数据库的角度来说，表 A 和 B 之间有三种关系：一对多（one-to-many，例如 tbl_user 和 tbl_post），一对一（ one-to-one 例如 tbl_user 和 tbl_profile）和多对多（many-to-many 例如 tbl_category 和 tbl_post）。 在 AR 中，有四种关系：

- BELONGS_TO（属于）: 如果表 A 和 B 之间的关系是一对多，则 表 B 属于 表 A (例如 Post 属于 User);
- HAS_MANY（有多个）: 如果表 A 和 B 之间的关系是一对多，则 A 有多个 B (例如 User 有多个 Post);
- HAS_ONE（有一个）: 这是 HAS_MANY 的一个特例，A 最多有一个 B (例如 User 最多有一个 Profile);
- MANY_MANY: 这个对应于数据库中的 多对多 关系。 由于多数 DBMS 不直接支持 多对多 关系，因此需要有一个关联表将 多对多 关系分割为 一对多 关系。 在我们的示例数据结构中，tbl_post_category 就是用于此目的的。在 AR 术语中，我们可以解释 MANY_MANY 为 BELONGS_TO 和 HAS_MANY 的组合。 例如，Post 属于多个（belongs to many） Category ，Category 有多个（has many） Post.

AR 中定义关系需要覆盖 [CActiveRecord](http://www.yiiframework.com/doc/api/1.1/CActiveRecord) 中的 [relations()](http://www.yiiframework.com/doc/api/1.1/CActiveRecord#relations) 方法。此方法返回一个关系配置数组。每个数组元素通过如下格式表示一个单一的关系。

在 Query Builder 中我们使用了下面 SQL 查询语句：

```

    SELECT c.FirstName, c.LastName , c.Address,c.Email
    FROM customer c
    INNER JOIN
    employee e
    ON c.SupportRepId=e.EmployeeId
    WHERE e.EmployeeId=4
    
```

涉及到两个表格 Employee 和 Customer，Employee 和 Customer 之间是一对多的关系，也就是说一个员工可以负责多个客户。Employee 到 Customer 的关系为 HAS_MANY， Customer 到 Employee 的关系为 HAS_ONE。因此可以定义 Employee 和 Customer 如下：

```

    //Customer.php
    class Customer extends CActiveRecord
    {
    	public static function model($className=__CLASS__)
    	{
    		return parent::model($className);
    	}
    
    	public function tableName()
    	{
    		return 'Customer';
    	}
    
    }
    
    //Employee.php
    class Employee extends CActiveRecord
    {
    	public static function model($className=__CLASS__)
    	{
    		return parent::model($className);
    	}
    
    	public function tableName()
    	{
    		return 'Employee';
    	}
    
    	public function relations()
    	{
    		return array(
    			'customers'=>array(self::HAS_MANY, 'Customer', 'SupportRepId'),
    
    			);
    	}
    }

```

因为本例只使用到由 Employee 查询对应的 Customer，因此只为类定义了 relations 方法。对应的表和外键为 Customer 和 SupportRepId。

然后修改 SiteController 的 indexAction 方法：

```

    public function actionIndex()
    {
    
    	$employee=Employee::model()->findByPk(4);
    
    	$this->render('index', array(
    		'model' => $employee->customers,
    
    		));
    }

```

AR 类中的关系定义为每个关系向类中隐式添加了一个属性。在一个关联查询执行后，相应的属性将将被以关联的 AR 实例填充。因此由 $employee->customers 可以查询到 Employee 对应的 Customers 记录。
执行关联查询最简单的方法是读取一个 AR 实例中的关联属性。如果此属性以前没有被访问过，则一个关联查询将被初始化，它将两个表关联并使用当前 AR 实例的主键过滤。 查询结果将以所关联 AR 类的实例的方式保存到属性中。这就是传说中的 懒惰式加载（lazy loading，也可译为延迟加载） 方式，例如，关联查询只在关联的对象首次被访问时执行。
本例使用的为延迟加载，延迟加载在某些情况下并不高效。如果我们想获取 N 个帖子的作者，使用这种延迟加载将会导致执行 N 个关联查询。 这种情况下，我们应该改为使用 渴求式加载（eager loading）方式。
渴求式加载方式会在获取主 AR 实例的同时获取关联的 AR 实例。 这是通过在使用 AR 中的 find 或 findAll 方法时配合使用 with 方法完成的。例如：

```


    $employee=Post::model()->with('customers')->findAll();

```

最后修改一下显示结果的 View 的代码：

```

    <?php foreach($model as $customer)
    {
    
    	echo 'First Name:' . $customer->FirstName . '<br />';
    	echo 'Last Name:' . $customer->LastName . '<br />';
    	echo 'Address:' . $customer->Address . '<br />';
    	echo 'Email:' . $customer->Email . '<br />';
    	echo '---------------------- <br />';
    }
    
     ?>

```

不同的数据对列名大小写处理方式不同，有的数据库区分大小写，保险起见，Customer 的属性使用和列定义同样的大小写。

![picture27.1](images/27.1.jpg)

本例[下载](http://www.imobilebbs.com/download/yii/RelationalActiveRecordDemo.zip)

本例介绍了关联 Active Record 的最基本的用法，其它功能和属性可以参见 Yii 中文文档，此外如果借助类似 CodeSmith 这样的工具，如果能够自动生成数据库定义的 ActiveRecord 代码，就可以大大减轻程序员的代码手工编写工作量。

此外，使用 Active Record 的便利是以性能为代价的，通常情况下使用 Active Record 与使用DAO读写数据库性能相比要差一个级别。下表为一个参考值，查找 200 个演员和 1000部电影。



<table>
<tbody>
<tr><td><em>方法</em></td><td><em>内存使用（M）</em></td><td><em>执行时间（秒)</em></td></tr>
<tr><td>Active Record</td><td>-19.74 </td><td>1.14109 </td></tr>
<tr><td>Query Builder</td><td>17.98 </td><td>0.35732 </td></tr>
<tr><td>DAO </td><td>-17.74 </td><td>0.35038 </td></tr>
</tbody>
</table>

Tags: [PHP](http://www.imobilebbs.com/wordpress/archives/tag/php), [Yii](http://www.imobilebbs.com/wordpress/archives/tag/yii)




