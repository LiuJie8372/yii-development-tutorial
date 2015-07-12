# Yii Framework 开发教程(5) URL 管理

在上一篇 [Yii Framework开发简明教程(4) Hangman](guess-word-game.md) 猜单词游戏实例略去了几个方面的问题，一是配置文件 main.php 的 URLManager，二是 Controller 的基类 CComponent ，三是定义 View 使用的 CHtml 帮助类。本篇战开介绍 URLManager，URL 管理。

```

    return array(
    	...
    	'components'=>array(
    		'urlManager'=>array(
    			'urlFormat'=>'path',
    			'rules'=>array(
    				'game/guess/<g:\w>'=>'game/guess',
    			),
    		),
    
    	),
    );

```

Web 应用程序完整的 URL 管理包括两个方面。首先， 当用户请求约定的 URL，应用程序需要解析它变成可以理解的参数。第二，应用程序需求提供一种创造 URL 的方法，以便创建的 URL 应用程序可以理解的。对于 Yii 应用程序，这些通过 CUrlManager 辅助完成。

当用 path 格式 URL，我们可以指定某些 URL 规则使我们的网址更用户友好性。例如，我们可以产生一个短短的 URL/post/100 ，而不是冗长/index.php/post/read/id/100。网址创建和解析都是通过 CUrlManager 指定网址规则。

要指定的 URL 规则，我们必须设定 urlManager 应用元件的属性 rules：

```

    array(
        ......
        'components'=>array(
            ......
            'urlManager'=>array(
                'urlFormat'=>'path',
                'rules'=>array(
                    'pattern1'=>'route1',
                    'pattern2'=>'route2',
                     'pattern3'=>'route3',
                ),
            ),
        ),
    );

```

这些规则以一系列的路线格式对数组指定，每对对应于一个单一的规则。路线（route）的格式必须是有效的正则表达式，没有分隔符和修饰语。它是用于匹配网址的路径信息部分。还有 route 应指向一个有效的路线控制器。

规则可以绑定少量的 GET 参数。参数的一般格式如下：

```

    <ParamName:ParamPattern>

```

ParamName 表示 GET 参数名字，可选项 ParamPattern 表示将用于匹配 GET 参数值的正则表达式。当生成一个网址（URL）时，这些参数令牌将被相应的参数值替换；当解析一个网址时，相应的 GET 参数将通过解析结果来生成。

我们使用一些例子来解释网址工作规则。我们假设我们的规则包括如下三个：

```

    array(
    'posts'=>'post/list',
    'post/<id:\d+>'=>'post/read',
    'post/<year:\d{4}>/<title>'=>'post/read',
    )

```

  - 调用$this->createUrl('post/list')生成/index.php/posts。第一个规则适用。
  - 调用$this->createUrl('post/read',array('id'=>100))生成/index.php/post/100。第二个规则适用。
  - 调用$this->createUrl('post/read',array('year'=>2008,'title'=>'a sample post'))生成/index.php/post/2008/a%20sample%20post。第三个规则适用。
  - 调用$this->createUrl('post/read')产生/index.php/post/read。请注意，没有规则适用。

总之，当使用 [createUrl](tp://www.imobilebbs.com/wordpress/教程/yii-framework-开发教程) 生成网址，路线和传递给该方法的 GET  参数被用来决定哪些网址规则适用。如果关联规则中的每个参数可以在 GET 参数找到的，将被传递给 [createUrl](tp://www.imobilebbs.com/wordpress/教程/yii-framework-开发教程) ，如果路线的规则也匹配路线参数，规则将用来生成网址。

如果 GET 参数传递到 [createUrl](tp://www.imobilebbs.com/wordpress/教程/yii-framework-开发教程) 是以上所要求的一项规则，其他参数将出现在查询字符串。例如，如果我们调用$this->createUrl('post/read',array('id'=>100,'year'=>2008)) ，我们将获得/index.php/post/100?year=2008。为了使这些额外参数出现在路径信息的一部分，我们应该给规则附加/*。 因此，该规则 post/<id:\d+>/* ，我们可以获取网址/index.php/post/100/year/2008 。

正如我们提到的，URL 规则的其他用途是解析请求网址。当然，这是 URL 生成的一个逆过程。例如， 当用户请求/index.php/post/100 ，上面例子的第二个规则将适用来解析路线 post/read 和 GET 参数 array('id'=>100) （可通过$_GET获得） 。

createurl 方法所产生的是一个相对地址。为了得到一个绝对的 url ，我们可以用前缀 yii">

注：使用的 URL 规则将降低应用的性能。这是因为当解析请求的 URL ，[ CUrlManager ]尝试使用每个规则来匹配它，直到某个规则可以适用。因此，高流量网站应用应尽量减少其使用的 URL 规则。

来看下 Hangman 中使用的规则

    ‘game/guess/<g:\w>’=>’game/guess’,

也就是将所有类似 /game/guess/xx 全部映射到 game/guess 也就是 GameController 的 actionGuess 方法，传入 GET 参数以 g=’x’ 的方式。参照每个字母的链接

```

    CHtml::linkButton(chr($i),array('submit'=>array('guess','g'=>chr($i))));

```

点击的字母链接为 /game/guess/?g=x 或 /game/guess/x 根据 main.php 定义的 urlManager 的匹配规则 Yii 框架调用 GameController  的 actionGuess 方法，传入 GET 参数。这样在 actionGuess 就可以通过$_GET[‘g’]来访问这个参数的值。

```

    // check to see if the letter is guessed correctly
    if(isset($_GET['g'][0]) && ($result=$this->guess($_GET['g'][0]))!==null)
    	$this->render($result ? 'win' : 'lose');
    else // the letter is guessed correctly, but not win yet
    {
    	$guessed=$this->getPageState('guessed',array());
    	$guessed[$_GET['g'][0]]=true;
    	$this->setPageState('guessed',$guessed,array());
    	$this->render('guess');
    }

```

使用 urlManager 也允许自定义规则，或者隐藏 index.php ,具体可以参考 [Yii 开发文档](http://www.imobilebbs.com/wordpress/教程/yii-framework-开发教程)

Tags: [PHP](http://www.imobilebbs.com/wordpress/archives/tag/php), [Yii](http://www.imobilebbs.com/wordpress/archives/tag/yii)