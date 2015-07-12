# Yii Framework 开发教程(15) UI 组件 MultiFileUpload 示例

CMultiFileUpload 用于上传文件，支持一次同时上传多个文件。这个 UI 组件是基于 [jQuery Multi File Upload](http://www.fyneworks.com/jquery/multiple-file-upload/) 插件。Yii 内置的很多 UI 组件都是基于 JQuery，因此需要创建 assets 目录用于存放动态生成的 javascripts 等。

所上传的文件的信息可以通过$_FILES[widget-name]来访问，比如，CMultiFileUpload 的 name 为”files” 所上传的文件信息可以通过$_FILES[‘files’]来访问。此外包含 CMultiFileUpload 的 Form 属性需要设置 enctype=multipart/form-data。

本例创建一个 upload 目录用于存放上传的文件。我们通过配置文件设置中个上传文件导入目录。

修改/config/main.php 添加项目代码

```

    // application-level parameters that can be accessed
    // using Yii::app()->params['paramName']
    'params'=>require(dirname(__FILE__).'/params.php'),

```

为 Application 添加一些参数，存放参数的文件为 config/param.php

定义上传文件的目录如下：

```

    // this contains the application parameters that can be maintained via GUI
    return array(
    
    //upload directory
    'uploadDir' => 'upload/',
    );

```

在代码中可以通过 Yii::app()->params[‘uploadDir’] 来访问这个参数，对于这个简单的例子，你也可以直接使用 upload/ 做为固定的常量而无需定义 Application 的参数 params.

本例不需使用 Model，我们定义 View 如下：

```

    <div class="form">
    <?php $form=$this->beginWidget('CActiveForm',array(
    
    	'method' =>'post',
    	'htmlOptions'=>array('enctype'=>'multipart/form-data'),
    
    	)); ?>
    
       <?php $this->widget('CMultiFileUpload',array(
       	'name'=>'files',
       	'accept'=>'jpg|png',
       	'max'=>3,
       	'remove'=>'Remove',
       	//'denied'=>'', message that is displayed when a file type is not allowed
       	//'duplicate'=>'', message that is displayed when a file appears twice
       	'htmlOptions'=>array('size'=>25),
       )); ?>
    
        <div class="row submit">
        <?php echo CHtml::submitButton('Upload'); ?>
        </div>
    
    <?php $this->endWidget(); ?>
    </div><!-- form -->
    
    <ul>
    <?php foreach($this->findFiles() as $filename): ?>
    	<li><?php echo CHtml::link($filename,
    			Yii::app()->baseUrl.'/'.Yii::app()->params['uploadDir'].$filename,
    	array('rel'=>'external'));?></li>
    <?php endforeach; ?>
    </ul>

```

使用 CMultiFileUpload 上传扩展名为 jpg|png 的文件，CMultiFileUpload 可以通过配置定义一些选项，具体可以[参考](http://www.yiiframework.com/doc/api/1.1/CMultiFileUpload)

修改其对应的 Controller/Action。

```

    class SiteController extends CController
    {
    	/**
    	 * Index action is the default action in a controller.
    	 */
    	public function actionIndex()
    	{
    
    		if(isset($_FILES['files']))
    		{
    			// delete old files
    			foreach($this->findFiles() as $filename)
    				unlink(Yii::app()->params['uploadDir'].$filename);
    
    			//upload new files
    			foreach($_FILES['files']['name'] as $key=>$filename)
    				move_uploaded_file($_FILES['files']['tmp_name'][$key],
    				Yii::app()->params['uploadDir'].$filename);
    		}
    		$this->render('index');
    	}
    
    	/**
    	 * @return array filename
    	 */
    	public function findFiles()
    	{
    		return array_diff(scandir(Yii::app()->params['uploadDir']),
    		array('.', '..'));
    	}
    }

```

Action 方法首先删除 upload 目录下的文件，然后将上传的文件存放到该目录下。

![picture15.1](images/15.1.jpg)

本例[下载](http://www.imobilebbs.com/download/yii/MultiFileUploadDemo.zip)

Tags: [PHP](http://www.imobilebbs.com/wordpress/archives/tag/php), [Yii](http://www.imobilebbs.com/wordpress/archives/tag/yii)
    