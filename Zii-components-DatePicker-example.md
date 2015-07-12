# Yii Framework 开发教程(36) Zii 组件-DatePicker 示例

CJuiDatePicker 用于日期输入，它封装了 JUI datepicker插件，其基本用法如下：

```

    <?php echo $form->errorSummary($model); ?>
    
     <?php
     $this->widget('zii.widgets.jui.CJuiDatePicker', array(
     	'name'=>'my_date',
     	'language'=>'en',
     	'options'=>array(
    	// 'show' (the default), 'slideDown', 'fadeIn', 'fold'
     				'showAnim'=>'fold',
     				'showOn'=>'button', // 'focus', 'button', 'both'
     				'buttonText'=>'Select form calendar',
     				'buttonImage'=>'images/calendar.png',
     				'buttonImageOnly'=>true,
     				),
     			'htmlOptions'=>array(
     				'style'=>'width:80px;vertical-align:top'
     				),
     			));
    
     ?>
    <div class="row submit">
    <?php echo CHtml::submitButton('Submit'); ?>
    </div>
    
    <?php $this->endWidget(); ?>
    </div><!-- form -->

```

为了获取输入的日期，首先为 CJuiDatePicker 的 Name 属性赋值，本例为 my_date，然后定义 DataModel

```

    class DataModel extends CFormModel
    {
    	public $my_date;
    }
    

```

当用户提交时，显示用户输入的日期，修改 SiteController 的 actionIndex

```

    public function actionIndex()
    {
    
    	$model=new DataModel();
    
    	if(!empty($_POST['my_date']))
    	{
    		$model->my_date=$_POST['my_date'];
    
    		if($model->validate()) {
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

![picture36.1](images/36.1.jpg)


本例[下载](http://www.imobilebbs.com/download/yii/DatePickerDemo.zip)

Tags: [PHP](http://www.imobilebbs.com/wordpress/archives/tag/php), [Yii](http://www.imobilebbs.com/wordpress/archives/tag/yii)
