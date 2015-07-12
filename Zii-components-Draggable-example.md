# Yii Framework 开发教程(42) Zii 组件-Draggable 示例

CJuiDraggable 可以使包含着其中的其它UI组件实现拖放功能，它封装了 [JUI Draggable](http://jqueryui.com/demos/draggable/) 插件。

要使某个 UI 组件可以拖放，下面为使用 CJuiDraggable 的基本用法：

```

    <?php $this->beginWidget('zii.widgets.jui.CJuiDraggable', array(
    	'options'=>array(
    				'cursor'=>'move',
    				),
    				'htmlOptions'=>array(
    					'style'=>'width: 200px; height: 200px;
    					 padding: 5px; border: none solid #e3e3e3;
    					 background: #0'
    					),
    			));
    //anything between beginwidget and endwidget is draggable.
    echo '<img id="image" width="200" src="images/7303.png">';
    
    $this->endWidget();
    ?>

```

所有包含在 beginWidget 和 endWidget 之间的内容都可以支持拖放，htmlOptions 部分定义了需要支持拖放的 UI 组件的大小。本例使用拖放一个图像。

![picture42.1](images/42.1.jpg)

本例[下载](http://www.imobilebbs.com/download/yii/JuiDraggableDemo.zip)

Tags: [PHP](http://www.imobilebbs.com/wordpress/archives/tag/php), [Yii](http://www.imobilebbs.com/wordpress/archives/tag/yii)