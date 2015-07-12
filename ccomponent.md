# Yii Framework 开发教程(6) CComponent 组件

在 Hangman 中定义的 GameController 使用到一些属性 word，可以使用$this->word 的格式来读写这个属性，但实际上在 GameController 对应到这个属性的方法为

```

    /**
     * @return string the word to be guessed. This value is persistent
     * during the whole game session.
     */
    public function getWord()
    {
    	return $this->getPageState('word');
    }
    
    /**
     * @param string the word to be guessed. This value is persistent
     * during the whole game session.
     */
    public function setWord($value)
    {
    	$this->setPageState('word',$value);
    }

```

也就是通过定义 getWord, setWord 的方式定义一个支持读写操作的属性 word(名字是大小写不敏感的).当读取属性时，getWord() 就会被调用，其返回值则成为属性值；相似的， 当写入属性时，setWord() 被调用。如果 setter 方法没有定义，则属性将是只读的， 如果对其写入则会抛出一个异常。使用 getter 和 setter 方法定义一个属性有一个好处：即当读取或写入属性时， 可以执行额外的逻辑（例如，执行验证，触发事件）。
基类 CComponent 除了可以定义属性外，还支持触发事件，和 ASP.Net 中 UI 组件非常类似。

组件事件是一些特殊的属性，它们使用一些称作 事件句柄 （event handlers）的方法作为其值。 附加(分配)一个方法到一个事件将会引起方法在事件被唤起处自动被调用。因此， 一个组件的行为可能会被一种在部件开发过程中不可预见的方式修改。

组件事件以 on 开头的命名方式定义。和属性通过 getter/setter 方法来定义的命名方式一样， 事件的名称是大小写不敏感的。以下代码定义了一个 onClicked 事件:

```

    public function onClicked($event)
    {
      $this->raiseEvent('onClicked', $event);
    }

```
这里作为事件参数的 $event 是 CEvent 或其子类的实例。

我们可以附加一个方法到此 event，如下所示:

    $component->onClicked=$callback;

这里的 $callback 指向了一个有效的 PHP 回调。它可以是一个全局函数也可以是类中的一个方法。 如果是后者，它必须以一个数组的方式提供: array($object,’methodName’).

事件句柄的结构如下：

```

    function methodName($event)
    {
       ......
    }

```

这里的 $event 即描述事件的参数（它来源于 raiseEvent() 调用）。$event 参数是 CEvent 或其子类的实例。 至少，它包含了关于谁触发了此事件的信息。

从版本 1.0.10 开始，事件句柄也可以是一个 PHP 5.3 以后支持的匿名函数。例如，

```

    $component->onClicked=function($event) {
       ......
    }
```

如果我们现在调用 onClicked()，onClicked 事件将被触发（在 onClicked() 中）， 附属的事件句柄将被自动调用。

一个事件可以绑定多个句柄。当事件触发时， 这些句柄将被按照它们绑定到事件时的顺序依次执行。如果句柄决定组织后续句柄被执行，它可以设置 $event->handled 为 true。

从版本 1.0.2 开始，组件已添加了对 mixin 的支持，并可以绑定一个或多个行为。 行为 是一个对象，其方法可以被它绑定的部件通过收集功能的方式来实现 继承（inherited）， 而不是专有化继承(即普通的类继承).一个部件可以以’多重继承’的方式实现多个行为的绑定.

行为类必须实现 IBehavior 接口。 大多数行为可以继承自 CBehavior 。如果一个行为需要绑定到一个 模型, 它也可以从专为模型实现绑定特性的 CModelBehavior 或 CActiveRecordBehavior 继承。

要使用一个行为，它必须首先通过调用此行为的 attach() 方法绑定到一个组件。然后我们就可以通过组件调用此行为方法：

```

        // $name 在组件中实现了对行为的唯一识别
        $component->attachBehavior($name,$behavior);
        // test() 是行为中的方法。
        $component->test();
```

已绑定的行为可以像一个组件中的普通属性一样访问。 例如，如果一个名为 tree 的行为绑定到了一个组件，我们就可以通过如下代码获得指向此行为的引用。

```

    $behavior=$component->tree;
    // 等于下行代码：
    // $behavior=$component->asa('tree');
    行为是可以被临时禁止的,此时它的方法开就会在组件中失效.例如:
    
    $component->disableBehavior($name);
    // 下面的代码将抛出一个异常
    $component->test();
    $component->enableBehavior($name);
    // 现在就可以使用了
    $component->test();

```

两个同名行为绑定到同一个组件下是有可能的.在这种情况下,先绑定的行为则拥有优先权.

当和 events, 一起使用时,行为会更加强大. 当行为被绑定到组件时,行为里的一些方法就可以绑定到组件的一些事件上了. 这样一来,行为就有机观察或者改变组件的常规执行流程.

自版本 1.1.0 开始，一个行为的属性也可以通过绑定到的组件来访问。 这些属性包含公共成员变量以及通过 getters 和/或 setters 方式设置的属性。 例如， 若一个行为有一个 xyz 的属性，此行为被绑定到组件 $a，然后我们可以使用表达式 $a->xyz 访问此行为的属性。

Tags: [PHP](http://www.imobilebbs.com/wordpress/archives/tag/php), [Yii](http://www.imobilebbs.com/wordpress/archives/tag/yii)
    
    