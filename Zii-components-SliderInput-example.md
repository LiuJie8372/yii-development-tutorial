# Yii Framework 开发教程(40) Zii 组件-SliderInput 示例

CJuiSliderInput 显示一滑动条，它也封装了 [JUI slider](http://jqueryui.com/demos/slider/) 插件，可以用在 Form 中作为用户输入 UI 组件。

基本用法如下：

```

    <div class="form">
    <?php $form=$this->beginWidget('CActiveForm'); ?>
    
    <?php echo $form->errorSummary($model); ?>
    
     <?php $this->widget('zii.widgets.jui.CJuiSliderInput', array(
     	'model'=>$model,
     	'attribute'=>'size',
     	'name'=>'my_slider',
     	'value'=>50,
     	'event'=>'change',
     	'options'=>array(
     				'min'=>0,
     				'max'=>100,
     				'slide'=>'js:function(event,ui){$("#amount").text(ui.value);}',
     				),
     			'htmlOptions'=>array(
     				'style'=>'width:200px; float:left;'
     				),
     )); ?>
    
    <div id="amount" style="margin-left:215px;">50</div>
    <div class="row submit">
    <?php echo CHtml::submitButton('Submit'); ?>
    </div>
    
    <?php $this->endWidget(); ?>
    </div><!-- form -->
    
```

用户提交后，使用 result.php 来显示用户输入的值，这里定义 DataModel 如下：

```

    class DataModel extends CFormModel
    {
    	public $size;
    
    	public function rules()
    	{
    		return array(
    			array('size', 'safe'),
    		);
    	}
    }
    
```

修改 SiteController 的 indexAction 方法：

```

    public function actionIndex()
    {
    
    	$model=new DataModel();
    	$model->size=50;
    	if(!empty($_POST['DataModel']))
    	{
    		$model->attributes=$_POST['DataModel'];
    
    		if($model->validate())
    		{
    			$this->render('result', array(
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

![picture40.1](images/40.1.jpg)

本例[下载](http://www.imobilebbs.com/download/yii/JuiSliderInputDemo.zip)

Tags: [PHP](http://www.imobilebbs.com/wordpress/archives/tag/php), [Yii](http://www.imobilebbs.com/wordpress/archives/tag/yii)
