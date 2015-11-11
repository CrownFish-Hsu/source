title: PHP个人框架PAF开发(1)
categories: TECH
tags: [PHP]
---
### 个人框架开发(PAF = PHP Another Framework)
- #### 框架目录结构
![](http://7xo7qs.com1.z0.glb.clouddn.com/paf1.png?imageView2/1/w/200/h/300)

##### I.第一部分：基于VC的输入输出：
- 配置文件 paf.conf
定义全局变量
```php
define('PAF_PATH', 'paf');

//配置模板版本
define('PAF_VIEW_PATH', 'default');

define('PAF_VIEW_HEADER', 'header');
define('PAF_VIEW_FOOTER', 'footer');

//模板后缀定义
define('PAF_VIEW_EXT', '.php');
```
- 入口文件 index.php
参数解析，解析出controller和action，找到对应的文件。
核心代码：
```php
$_controller = isset($_GET['controller']) ? $_GET['controller'] : '';
$_action     = isset($_GET['action']) ? $_GET['action'] : '';
$_controller_path = PAF_PATH.'/MVC/controller/'.$_controller.'.inc';
$_init_controller = new $_controller();
$_init_controller->$_action();
$_init_controller->run();
```

- 渲染文件 
\MVC\controller\\{$_controller}.inc(进入controller)
```php
class {$_action} extends _Master {
	public function {$_action}() {
		$this->setView({$view_name});
		//设置变量
		$this->setVar('website', 'github.io');
	}
}
```
\MVC\controller\_Master.inc(基类)
```php
abstract class _Master {
	var $_view;
	var $_vars = [];

	function setView($viewName) {
		$this->_view = $viewName;
	}

	function getView() {
		return '\MVC\VIEW\default\{$view_name}.php';
	}

	function setVar($name, $value) {
		$this->_vars[$name] = $value;
	}

	function run() {
		//解包，把变量解析
		extract($this->_vars);

		//加载头部  加载尾部

		//加载模板
		include($this->getView());
	}
}
```
\MVC\View\default\{$view_name}
```php
echo $website;	//输出github.io
```

II.第二部分：加入Model，连接Mysql
- 前提：需要类库[adodb(下载)](https://sourceforge.net/projects/adodb/files/adodb-php5-only/)
[adodb与mysqli, pdo功能相同，adodb是php编写的，不需要安装额外的扩展，可以操作多种数据库]
- 解压后提取出drives文件夹和下图文件，一起放入\Library\DataBase下，其他文件不需要
- 在\Library\DataBase文件夹下新建myDataBase.inc.php，作为数据库调用类
![](http://7xo7qs.com1.z0.glb.clouddn.com/db.png?imageView2/1/w/250/h/200)

- myDataBase类
```php
	require('adodb.inc.php');

	class myDataBase {

		public $_dbAddr = 'db_address';
		public $_dbName = 'db_name';
		public $_dbUser = 'username';
		public $_dbPwd  = 'password';
		public $_db     = false;    //db实例

		//构造函数
		function myDataBase() {
			$this->initConnect();
		}

		//析构函数
		function __destruct() {
			if($this->_db && $this->_db->IsConnected()) {
				$this->_db->disconnect();
				unset($this->_db);
			}
		}

		function initConnect() {
			$this->_db = NewADOConnection('mysqli');
			$this->_db->Connect($this->_dbAddr, $this->_dbUser, $this->_dbPwd, $this->_dbName);
			$this->_db->Query('set names utf8');
			$this->_db->SetFetchMode(ADODB_FETCH_ASSOC);
		}

		//执行sql，返回数组
		function execForArray ($sql) {
			$result = $this->_db->Execute($sql);
			if($result) {
				$returnArray = [];
				while(!$result->EOF) {
					$returnArray[] = $result->fields;
					$result->MoveNext();
				}
				return $returnArray;
			}else {
				return false;
			}
		}
}
```

- Model基类(_Model.inc)，解析$where参数，拼接sql
```php
	abstract class _Model {
		public $_view_name = '';	//表名
		public $_id        = '1075866671';		//默认id值
		public $_id_key    = 'id';    //主键key

		function load($where = '') {
			if(!$this->_view_name || $this->_id == 0) {
				return;
			}

			//对象属性解析，属性值和数据库表字段保持一致
			$vars = get_object_vars($this);
			$sql  = '';
			foreach ($vars as $key => $value) {
				if(trim($value) == '') {
					if($sql != '') {
						$sql .= ',';
					}
					$sql .= $key;
				}
			}

			if(!$sql) {
				return;
			}

			$sql = 'select '.$sql.' from '.$this->_view_name.' where ';
			if(!$where) {
				$sql .= $this->_id_key.' = '.$this->_id;
			}else {
				$sql .= $where;
			}

			require(PAF_PATH.'/Library/DataBase/myDataBase.php');
			$db  = new myDataBase();
			$ret = $db->execForArray($sql);
			if($ret && 1 == count($ret)) {
				$ret 	  = $ret[0];
				$var_keys = array_keys($vars);
				foreach ($ret as $key => $value) {
					if(in_array($key, $var_keys)) {
						$this->$key = $value;
					}
				}
			}
		}
	}
```

- Controller调用Model
```php
class {$_action} extends _Master {

	public function {$_action}() {
		$this->setView({$view_name});

		//加载Model，后面可以封装到公共文件中
		require(PAF_PATH.'/MVC/Model/_Model.inc');

		//$modelName 需要extends _Master.inc，$modelName的属性还需要和表字段名一致，保证解析sql不用转化
		{$objModel} = new {$modelName}();
		{$objModel}->_view_name = '{$table_name}';
		{$objModel}->_id        = '{$id_number}';

		{$objModel}->load();
		$this->setVar('UserModel', {$objModel});
	}
}
```

- 这样可以在模板中获得变量，直接输出变量的属性值

一个最简单的MVC框架骨骼就完成了，以后需要的是不断在框架上加功能，包括路由重定向，缓存，SDK，API的提供。