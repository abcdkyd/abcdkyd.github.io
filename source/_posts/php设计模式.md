---
title: php设计模式
date: 2018-12-29 22:26:48
tags: 
	- php
categories: 设计模式
---

### 适配器模式

> 适配器设计模式只是将某个对象的接口适配为另外一个对象所期望的接口

当添加新功能时，功能要求采用不同的方式使用现有的对象，而不是采用远行设计的方式，可以采用适配器模式构建另一个对象的方案。

同时，适配器设计模式也是针对数据源改变的优秀解决方案，常见有`改变数据库引擎`和`第三方提供数据格式变化`

#### 关系说明

1. MyObject类包含一个名为 methodA()的公共方法。OriginalConsumer类获取MyObject的一个实例，并且在执行其doSomthing()函数期间调用methodA()
2. 引入NewConsumer类。这个类的doSomthing( )函数在执行期间期望调用MyObject实例的一个公共方法: methodB()
3. 通过扩展MyObject类创建MyObjectAdapterForNewConsumer类。像NewConsumer期望的样，新创建的类会提供名为methodB()的公共方法。在这个简单的示例中，所有methodB()都会调用methodA()

<!-- more -->

#### 代码示例

1. 类适配器模式（类适配器使用的是继承）

	```
	// 目标角色
	interface Target {
	 
	    // 源类也有的方法1
	    public function sampleMethod1();
	 
	    // 源类没有的方法2
	    public function sampleMethod2();
	}
	
	// 源角色
	class Adaptee {
	
	    // 源类含有的方法
	    public function sampleMethod1() {
	        echo 'Adaptee sampleMethod1 <br />';
	    }
	}
	
	// 类适配器角色
	class Adapter extends Adaptee implements Target {
	 
	    // 源类中没有sampleMethod2方法，在此补充
	    public function sampleMethod2() {
	        echo 'Adapter sampleMethod2 <br />';
	    }
	 
	}
	 
	class Client {
	    public static function main() {
	        $adapter = new Adapter();
	        $adapter->sampleMethod1();
	        $adapter->sampleMethod2();
	 
	    }
	}
	
	```

2. 对象适配器模式（对象适配器使用的是委派）
	
	```
	// 目标角色
	interface Target {
	 
	    // 源类也有的方法1
	    public function sampleMethod1();
	 
	    // 源类没有的方法2
	    public function sampleMethod2();
	}
	 
	// 源角色
	class Adaptee {
	 
	    // 源类含有的方法
	    public function sampleMethod1() {
	        echo 'Adaptee sampleMethod1 <br />';
	    }
	}
	 
	// 类适配器角色
	class Adapter implements Target {
	 
	    private $_adaptee;
	 
	    public function __construct(Adaptee $adaptee) {
	        $this->_adaptee = $adaptee;
	    }
	 
	    // 委派调用Adaptee的sampleMethod1方法
	    public function sampleMethod1() {
	        $this->_adaptee->sampleMethod1();
	    }
	 
	    // 源类中没有sampleMethod2方法，在此补充
	    public function sampleMethod2() {
	        echo 'Adapter sampleMethod2 <br />';
	    }
	 
	}
	 
	class Client {
	    public static function main() {
	        $adaptee = new Adaptee();
	        $adapter = new Adapter($adaptee);
	        $adapter->sampleMethod1();
	        $adapter->sampleMethod2();
	 
	    }
	}
	```
	
### 建造者模式

> 建造者设计模式定义了处理其他对象的复杂构建的对象设计

通常，复杂性是某个方法内包含的实际逻辑，但当提及一个与对象实例化相关联的复杂对象时，复杂意味着创建这个完整对象所需的步骤等级，建造者设计模式的目的是`消除其他对象的复杂创建过程`。

建造者模式是一步一步创建一个复杂的对象，它允许用户只通过指定复杂对象的类型和内容就可以构建它们，用户不需要知道内部的具体构建细节，`把构建复杂对象的过程封装起来`。

#### 关系说明

1. MyObject类具有能够完全实现对象构造的两个方法。为了具有完整的MyObject对象，需要执行complexFunctionA()和complexFunctionB()方法。
2. MyObjectBuilder类包含一个名为createInstanceOfMyObject()的方法。这个类负责创建MyObject类的一个简单实例。需要注意没有用于进一步构造的配置选项。这个类还存储MyObjectBuilder类创建的实例中的私有实例。
3. buildMyObject()方法接受参数configurationOptions.这个方法用于调用在MyObjectBuilder对象中存储的MyObject对象的complexFunctionA()和complexFunctionB()方法。
4. getBuildMyObject()方法返回MyObjectBuilder 对象内部MyObject对象的私有实例，该实例既是完整的，也是正确构建的。

#### 代码示例

```
class People{
    public $head;
    public $body;
    
    //不太此类中直接给属性赋值
    function __construct(){

    }
}

//定义具备建造者所需实现的方法
abstract class PeopleBuilder{
    abstract function buildHead();
    abstract function buildBody();
}

//具体建造类实现具体业务
class ChildBuilder extends PeopleBuilder{
    private $people;
    function __construct(){
        $this->people = new people;
    }

    function buildHead(){
       $this->people->head = "this is a child's head!";
    }
    function buildBody(){
       $this->people->body = "this is a child's body!";
    }
    function returnResult(){
       return $this->people;
    }
}

class AdultBuilder extends PeopleBuilder{
    private $people;
    function __construct(){
        $this->people = new people;
    }

    function buildHead(){
       $this->people->head =  "this is a adult's head!";
    }
    function buildBody(){
       $this->people->body =  "this is a adult's body!";
    }
    function returnResult(){
       return $this->people;
    }
}

//指挥者，指挥者负责与调用客户端交互，决定创建什么样的产品
class Director{
    function __construct(Peoplebuilder $builder){
        $builder->buildHead();
        $builder->buildBody(); 
    }
}


//测试
$builder = new AdultBuilder();  
$director = new Director($builder);  
$person = $builder->returnResult(); 
//this is a adult's head! this is a adult's body!
```

### 数据访问对象模式

> 数据访问对象设计模式描述了如何创建提供透明访问任何数据源的对象

数据访问对象封装了创建SQL调用、减少实例创建的复杂性和重复以及更新进程的智能方式，其编写方式应当是：该对象的使用者不会知道实际使用的表结构和数据库引擎。

管理数据访问对象类中简单性的一个好办法是创建父-子关系。首先，创建一个基本的父对象。这个对象应当负责数据库连接、抽象地执行查询以及与子对象通信。使用数据访问对象设计模式时，最好开始就将一对一关系的子类与数据库中的表相关联。这些子类具有必不可少的信息，如表名和主键。此外，子类可能包含一些特定的公共方法，这些方法通过只对子类有意义的方式执行父类的查询

#### 代码示例

```
/*
*	MySQL数据库拥有一条记录，该记录包含对每个用户来说都是具体和特有的信息。这种功能性必须允许我们通过用户的主键或对用户名称的查找返回一个用户。此外，我们必须能够对用户实体记录的任意字段执行更新操作。
*	针对此需求，需要使用两个类：第一个类应当是基本数据访问对象类，它具有获取和更新数据方法：
*/

abstract class baseDAO{
    private $__connection;
    
    public function __construct(){
        $this->__connectionToDB(DB_USER,DB_PASS,DB_HOST,DB_DATABASE);
    }

    private function __connectToDB($user,$pass,$host,$database){
        $this->__connection = mysql_connection($host,$user,$pass);
        mysql_select_db($database,$this->__connection);
    }

    public function fetch($vale,$key = NULL){
        if(is_null($key)){
            $key = $this->_primaryKey;
        }

        $sql = "select * from {$this->_tablename} where {$key} = '{$value}'";

        $results = mysql_query($sql,$this->__connection);

        $rows = array();

        while($result = mysql_fetch_array($results)){
            $rows[] = $result;
        }

        return $rows;
    }

    
    public function update($keyedArray){
        $sql = "update {$this->_tableName} set ";
        $updates = array();

        foreach($keyedArray as $column =>$value){
            $updates[] = "{$column} = '{$value}'";
        }

        $sql .= implode(',',$updates);
        $sql .= " where {$this->_primaryKey} = '{$keyedArray[$this->_primaryKey]}'";

        mysql_query($sql,$this->__connection);
    }
}

/*
*	这是一个抽象类，为了能够使用该类必须扩展该类。
*	因为很可能会同时打开多个数据库连接，所以在数据访问对象类中存储内部的数据库连接并且每个查询都进行引用是十分重要的。这个数据访问对象类应当唯一地引用自其自己的连接。通常，在更多可拓展的模型中，接口被创建用于共享连接。
*/

/*
*	任何子类都可以任意扩展这个抽象类：
*/
class UserDAO extends baseDAO{
    protected $_tableName = "userTable";
    protected $_primaryKey = 'id';

    public function getUserByFirstName($name){
        $result = $this->fetch($name,'firstName');
        return $result;
    }
}

// 为了获得一个起作用的数据访问对象子实体，至少需要定义两个受保护变量。
// 要使用的数据访问对象的示例：

define('DB_USER','user');
define('DB_PASS','pass');
define('DB_HOST','localhost');
define('DB_DATABASE','test');

$user = new userDAO();
$userDetailsArray = $user->fetch(1);

$updates = array('id'=>1,'firstName'=>'aaron');
$user->update($updates);

$allAarons = $user->getUserByFirstName('aaron');
```

### 装饰器模式

> 如果已有对象的部分内容或功能性发生改变，但是不需要修改原始对象的结构，那么使用装饰器设计模式最适合。

装饰器设计模式适用于编程人员花费大量时间所处的下列工作场合：变化是快速和细小的，而且几乎不影响应用程序的其余部分。使用装饰器设计模式设计类的目的是：不必重写任何已有的功能性，而是对某个基对象应用增量变化。装饰器采用这样的构建方式：在主代码流中应当能够直接插入一个或多个更改或“装饰”目标对象的装饰器，同时不影响其他代码流。

#### 关系说明

1. MyObject是具有现有功能性的基类。这个类包含名为items的公共数组和名为show ItemsFormatted()的公共方法。
2. showltemsFormatted()方法负责接受items数组，并且使用预定义的功能性格式化该数组后提交输出。
3. MyObjectDecorator类包含MyObject 的一个私有实例和两个公共方法: MyoObjec-Decorator()和decorateItems()。
4. MyobjcDecoratol )方法代表构造函数，它接受^个MyObjcet 类型参数并将其存储在内部。
5. dortetfer )方法可修改Myobjcet 实例的tems数组。

#### 代码示例

1. 《PHP设计模式》里面的一个案例

	```
	// 被修饰类 现在的需求： 要求能够动态为CD添加音轨、能显示CD音轨列表。 显示时应采用单行并且为每个音轨都以音轨好为前缀。
	 
	class CD {
	    public $trackList;
	 
	    function __construct()   {
	        # code...
	        $this->trackList=array();
	    }
	 
	    public function addTrack($track){
	        $this->trackList[]=$track;
	    }
	 
	    public function getTrackList(){
	        $output=" ";
	        foreach ($this->trackList as $key => $value) {
	            # code...
	            $output.=($key+1).") {$value}. ";
	        }
	        return $output;
	    }
	}

	// 现在需求发生变化： 要求将当前实例输出的音轨都采用大写形式。 这个需求并不是一个变化特别大的需求，不需要修改基类或创建一个父子关系的子类，此时创建一个基于装饰器模式的装饰器类。

	class CDTrackListDecoratorCaps{
	    private $_cd;
	 
	    public function __construct(CD $CD){
	        $this->_cd=$CD;
	    }

	    public function makeCaps(){
	        foreach ($this->_cd->trackList as $key => $value) {
	            # code...
	            $this->_cd->trackList[$key]=strtoupper($value); //转换成大写
	        }
	    }
	}

	//客户端测试
	$myCD=new CD();
	$trackList=array(   "what It Means",   "brr",   "goodBye"  );
	foreach ($trackList as $key => $value) {
	    # code...
	    $myCD->addTrack($value);
	}

	$myCDCaps=new CDTrackListDecoratorCaps($myCD);
	$myCDCaps->makeCaps();
	print "The CD contains the following tracks:".$myCD->getTrackList();
	```

2. REST 服务提供 JSON 和 XML 装饰器

	```
	RendererInterface.php
	<?php
	
	namespace DesignPatterns\Structural\Decorator;
	
	interface RendererInterface
	{
	    public function renderData();
	}
	 
	Webservice.php
	<?php
	 
	namespace DesignPatterns\Structural\Decorator;
	
	class Webservice implements RendererInterface
	{
	    protected $data;
	
	    public function __construct($data)
	    {
	        $this->data = $data;
	    }
	
	    public function renderData()
	    {
	        return $this->data;
	    }
	}
	 
	Decorator.php
	<?php
	 
	namespace DesignPatterns\Structural\Decorator;
	 
	/**
	 * 装饰器必须实现 RendererInterface 接口, 这是装饰器模式的主要特点，
	 * 否则的话就不是装饰器而只是个包裹类
	 */
	abstract class Decorator implements RendererInterface
	{
	    protected $wrapped;
	 
	    /**
	     * 必须类型声明装饰组件以便在子类中可以调用renderData()方法
	     * @param RendererInterface $wrappable
	     */
	    public function __construct(RendererInterface $wrappable)
	    {
	        $this->wrapped = $wrappable;
	    }
	}
	 
	RenderInXml.php
	<?php
	 
	namespace DesignPatterns\Structural\Decorator;
	
	class RenderInXml extends Decorator
	{
	    public function renderData()
	    {
	        $output = $this->wrapped->renderData();
	 
	        // do some fancy conversion to xml from array ...
	 
	        $doc = new \DOMDocument();
	 
	        foreach ($output as $key => $val) {
	            $doc->appendChild($doc->createElement($key, $val));
	        }
	 
	        return $doc->saveXML();
	    }
	}
	 
	RenderInJson.php
	<?php
	 
	namespace DesignPatterns\Structural\Decorator;
	
	class RenderInJson extends Decorator
	{
	    public function renderData()
	    {
	        $output = $this->wrapped->renderData();
	        return json_encode($output);
	    }
	}
	 
	Tests/DecoratorTest.php
	<?php
	 
	namespace DesignPatterns\Structural\Decorator\Tests;
	 
	use DesignPatterns\Structural\Decorator;
	 
	/**
	 * DecoratorTest 用于测试装饰器模式
	 */
	class DecoratorTest extends \PHPUnit_Framework_TestCase
	{
	 
	    protected $service;
	 
	    protected function setUp()
	    {
	        $this->service = new Decorator\Webservice(array('foo' => 'bar'));
	    }
	 
	    public function testJsonDecorator()
	    {
	        // Wrap service with a JSON decorator for renderers
	        $service = new Decorator\RenderInJson($this->service);
	        // Our Renderer will now output JSON instead of an array
	        $this->assertEquals('{"foo":"bar"}', $service->renderData());
	    }
	 
	    public function testXmlDecorator()
	    {
	        // Wrap service with a XML decorator for renderers
	        $service = new Decorator\RenderInXml($this->service);
	        // Our Renderer will now output XML instead of an array
	        $xml = '<?xml version="1.0"?><foo>bar</foo>';
	        $this->assertXmlStringEqualsXmlString($xml, $service->renderData());
	    }
	}
	```