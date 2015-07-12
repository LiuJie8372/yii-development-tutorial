# Yii Framework 开发教程(18) UI 组件 TextHighlighter 示例

CTextHighlighter 用来格式化显示代码，目前支持显示的语言有 ABAP, CPP, CSS, DIFF, DTD, HTML, JAVA, JAVASCRIPT, MYSQL, PERL, PHP, PYTHON, RUBY, SQL, XML，显示代码时也可以显示行号，通过 showLineNumbers=TRUE 打开行号显示。

本例显示PHP代码，一个带行号，一个不带行号：

```

    PHP code
    
    <!--?php $this--->beginWidget('CTextHighlighter',array('language'=>'PHP')); ?>
    // include Yii bootstrap file
    //require_once(dirname(__FILE__).'/../../framework/yii.php');
    $yii='C:/yiiframework/yii.php';
    // remove the following line when in production mode
    defined('YII_DEBUG') or define('YII_DEBUG',true);
    
    $config=dirname(__FILE__).'/protected/config/main.php';
    
    // remove the following line when in production mode
    // defined('YII_DEBUG') or define('YII_DEBUG',true);
    
    require_once($yii);
    Yii::createWebApplication($config)->run();
    <!--?php $this--->endWidget(); ?>
    
    PHP code with Line Number
    
    <!--?php $this--->beginWidget('CTextHighlighter',array('language'=>'PHP',
     'showLineNumbers'=>'true')); ?>
    /**
     * SiteController is the default controller to handle user requests.
     */
    class SiteController extends CController
    {
    	/**
    	 * Index action is the default action in a controller.
    	 */
    	public function actionIndex()
    	{
    
    		$model=new DataModel();
    
    		if(!empty($_POST[DataModel]))
    		{
    			$model->attributes=$_POST[DataModel];
    
    			if($model->validate()) $success=true;
    
    		}
    
    		$this->render('index', array(
    				'model' => $model,
    
    				));
    	}
    }
    <!--?php $this--->endWidget(); ?>

```

通过指定 language 的种类（不区分大小写） 指明需显示代码的种类。

![picture18.1](images18.1.jpg)

本例[下载](http://www.imobilebbs.com/download/yii/TextHighlighterDemo.zip)

Tags: [PHP](http://www.imobilebbs.com/wordpress/archives/tag/php), [Yii](http://www.imobilebbs.com/wordpress/archives/tag/yii)