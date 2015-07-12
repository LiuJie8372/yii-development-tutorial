# Yii Framework 开发教程(11) UI 组件 ActiveForm 示例

   Contents  
1. [创建 DataModel](http://www.imobilebbs.com/wordpress/archives/3955#DataModel)  
2. [定义 Action](http://www.imobilebbs.com/wordpress/archives/3955#Action)  
3. [定义 View](http://www.imobilebbs.com/wordpress/archives/3955#View)  

前面在介绍[Yii Framework 开发教程(7) 使用CHtml 创建Form](http://www.imobilebbs.com/wordpress/archives/3906) 和 [Yii Framework 开发教程(8) 使用FormModel](http://www.imobilebbs.com/wordpress/archives/3911) 已经对 CActiveForm 做了简要的介绍。过几天就是圣诞节了，这里帮助圣诞老人做个调查表，调查每个人希望得到的圣诞节礼物和圣诞大餐想吃的食品：-）。

这里共享一部分代码，做为后续例子的框架，基本上是复制 Hello World 的例子，添加了一个空的 DataModel，使用缺省的 Controller（SiteController）和缺省的 Action（actionIndex），使用缺省的配置文件，代码[下载](http://www.imobilebbs.com/download/yii/DummyDemoProject.zip)。

通过 HTML 表单收集用户数据是 Web 程序开发的主要工作之一。除了表单设计外， 开发者还需要将现存的或默认的数据填充到表单，验证用户输入， 对无效的输入显示适当的错误信息，保存输入到持久性存储器。Yii 通过其 MVC 结构极大地简化了此工作流程。

在 Yii 中处理表单时，通常需要以下步骤：

1. 创建用于表现所要收集数据字段的模型类。
2. 创建一个控制器动作，响应表单提交。
3. 在视图脚本中创建与控制器动作相关的表单。

## 创建 DataModel

```

    class DataModel extends CFormModel
    {
    	public $firstName;
    	public $lastName;
    
    	public $favouriteGift;
    
    	public $favouriteDinner;
    
    	public function rules()
    	{
    		return array(
    			array('firstName, lastName', 'required'),
    			array('favouriteGift,favouriteDinner', 'safe')
    
    			);
    	}
    
    	static $gifts=array(
    		'1'=>'iPad',
    		'2'=>'Remote control helicopter',
    		'3'=>'60 inch 3D LED TV',
    		'4'=>'Holy Bible',
    		);
    
    	static $meals=array(
    		'1'=>'Egg',
    		'2'=>'Ham',
    		'3'=>'Chicken',
    		'4'=>'Pork',
    		'5'=>'Beer',
    		'6'=>'Coke',
    		'7'=>'Wine',
    		);
    }

```

firstName，lastName 用于记载用户姓名，$favouriteGift 和 $favouriteDinner 记载用户的礼物和食品名称。$gifts，$meals 静态定义了可供用户选择的礼物种类和食品种类。这里要注意的是 firstName，lastName 是必填的，而 $favouriteGift 和 $favouriteDinner 设为可以安全复制的，这些由 Model 的 rules 来定义。

## 定义Action

修改 SiteController 的 Action 方法：

```

    public function actionIndex()
    {
    
    	$model=new DataModel();
    
    	if(!empty($_POST['DataModel']))
    	{
    		$model->attributes=$_POST['DataModel'];
    
    		if($model->validate())
    		{
    			$this->render('choice', array(
    				'model' => $model,
    
    				));
    			return;
    		}
    
    	}
    
    	$this->render('index', array(
    			'model' => $model,
    
    			));
    }

```

这里定义了两个 View，index 用于获取用户输入，choice 显示用户选择结果。$model->attributes=$_POST[‘DataModel’]; 正如我们在 [安全的特性赋值](http://www.yiiframework.com/doc/guide/1.1/zh_cn/form.model#securing-attribute-assignments) 中所讲的， 这行代码使用用户提交的数据填充模型。 attributes 属性由 [CModel](http://www.yiiframework.com/doc/api/1.1/CModel) 定义，它接受一个名值对数组并将其中的每个值赋给相应的模型特性。

## 定义View

首先是定义 index.php ，本例使用 CActiveForm，CActiveForm 同时提供客户端及服务器端无缝的、一致的验证。

```

    <?php $form = $this->beginWidget('CActiveForm', array(
        'id'=>'user-form',
        'enableAjaxValidation'=>true,
        'enableClientValidation'=>true,
        'focus'=>array($model,'firstName'),
    )); ?>

```

本例比较简单，没有使用这些复杂的校验，只要求 FirstName,LastName 非空，这是由 DataModel 中的 rules 的 required 来定义的。

```

    <div class="form">
    <?php $form=$this->beginWidget('CActiveForm'); ?>

        <?php echo $form->errorSummary($model); ?>

         <div class="row">
            <?php echo $form->label($model,'firstName'); ?>
            <?php echo $form->textField($model,'firstName') ?>
        </div>

	    <div class="row">
            <?php echo $form->label($model,'lastName'); ?>
            <?php echo $form->textField($model,'lastName') ?>
	    </div>

	    <p>Choose your Christmas Gift</p>
	    <div class="row">
             <?php echo $form->radioButtonList($model,'favouriteGift',
             DataModel::$gifts) ?>

        </div>

       <p>Choose your Christmas dinner</p>
	    <div class="row">
             <?php echo $form->checkBoxList($model,'favouriteDinner',
             DataModel::$meals) ?>

        </div>

        <div class="row submit">
            <?php echo CHtml::submitButton('Submit'); ?>
        </div>

    <?php $this->endWidget(); ?>
    </div><!-- form -->

```

View Choice 就更简单，显示用户选择结果：

```

    <?php echo "Merry Christmas "; ?>
    <?php echo $model->firstName . ' ' . $model->lastName . '.' ;?>
    <p />
    <p>You will be given
    	<?php
    		echo DataModel::$gifts[$model->favouriteGift];
    	?>
    on Christmas Day.</p>
    
    <p>And you will have
    	<?php
    		foreach($model->favouriteDinner as $dinner)
    		{
    			echo DataModel::$meals[$dinner] . ' ';
    		}
    	?>
    for Christmas dinner.</p>
    
    <p><?php echo CHtml::link('Start Again',array('index')); ?></p>

```

运行结果如下：

![picture11.1](images/11.1.jpg)

本例[下载](http://www.imobilebbs.com/download/yii/ActionFormDemo.zip)

Tags: [PHP](http://www.imobilebbs.com/wordpress/archives/tag/php), [Yii](http://www.imobilebbs.com/wordpress/archives/tag/yii)