# Framework托管到BAE


Framework是基于node.js开发的，所以需要node.js环境以及需要数据库的支持，百度开发者中心上BAE提供整套的环境的支持，只需简单的配置就可以快速运行基于framework开发的应用，具体方法如下：


（1）登陆[百度开发者中心](http://developer.baidu.com)，如果没有百度帐号请先注册百度帐号，并注册成为开发者；


（2）使用BAE，如果是第一次使用，需要先发送邮件到dev_support@baidu.com申请开通node.js权限。

![](images/intro_4_2.png)

（3）node.js权限开通后，点击 “创建应用” ，如图：

![](images/intro_4_3.png)

（4）点击 “确定”后在页面点击“云环境(BAE)”

![](images/intro_4_4.png)

（5）完成“应用域名”申请，以及选择使用“node.js”环境

![](images/intro_4_5.png)

（6）“创建新版本”，并使用SVN下载版本代码

![](images/intro_4_6.png)

（7）将本地sumeru工程文件拷贝到SVN下载版本代码目录下

（8）修改app.conf文件

	handlers:
  		- url : ^/socket/(.*)
    		script: $1.nodejs
    		
  		- url : ^/view/(.*)
    		script: /__bae__/bin/view/$1

  		- url : ^/bin/(.*)
    		script: /__bae__/bin/$1

  		- url : ^/hiUpload/(.*)
    		script: /__bae__/static/hiUpload/$1

  		- url : /server/(.*)
    		script: /index.html

  		- url : ^/publish/(.*)
    		script: /index.html

  		- url : (*.ico)
    		script: $1
  
  		- url : ^/sumeru/(.*)
    		script : /sumeru/$1
  
  		- url : ^/sumeru\.js
    		script : /sumeru/sumeru.js
    
  		- url : (.*)
    		script: /app/$1
    
  		- expire : .* modify 0 seconds
  		- expire : .jpg modify 10 years
  		- expire : .swf modify 10 years
  		- expire : .png modify 10 years
  		- expire : .gif modify 10 years
  		- expire : .JPG modify 10 years
  		- expire : .ico modify 10 years
  
  		- mime: .manifest text/cache-manifest
  		
  		
  也可点击<http://pan.baidu.com/share/link?shareid=474214&uk=1077217927>下载该文件
	
（9）修改app.js文件，使用下面的代码替代app.js文件中的全部内容

	 require("./sumeru/server/run.js");
	
（10）如果需要在BAE上使用MongoDB数据库，请按照下面方法创建MongoDB和获取数据库名

* 在“版本管理”页面，点击“服务管理”并选择 “MongoDB”

![](images/selectMongo.png)	

* 在“MongoDB”页面点击“创建MongoDB”，创建一个新的MongoDB数据库并**获取数据名**

![](images/createMongo.png)	

	 
（11） **仅0.7.14以下版本，需要执行本步骤**  修改sumeru/server/DbCollectionHandler.js

	//修改该文件第133 - 141行代码：
	if(process && process.BAE){
            host = process.env.BAE_ENV_ADDR_MONGO_IP;
            port = parseInt(process.env.BAE_ENV_ADDR_MONGO_PORT);
            username = process.env.BAE_ENV_AK;//fw.config.get('bae_user');
            password = process.env.BAE_ENV_SK;//fw.config.get('bae_password');
        }       
        var server = new mongodb.Server(host, port, serverOptions);
        //your dbname为在第（10）步获取的数据库名
        var db = new mongodb.Db('your dbname', server, {});
        
** 0.7.14（含）以上版本**，只需修改sumeru/src/frameworkConfig.js

	//修改该文件的第12行，将申请的dbname填入	
	dbname = "your dbname";
    
（12）修改app/config/sumeru.js文件

	将第7行
	     location.hostname + ':' + socketPort + '/socket/' : '';
	修改成：
	     location.hostname + '/socket/' : '';
      		
	
（13）修改根目录下的package.json文件

	将第5行：
	     "main": "index.js"
	修改成：
	     "main": "app.js"
	     
（14）进入sumeru/build/，并运行 node runBuild.js

（15）进入node_modules/，删除mongodb

（16）使用SVN上传代码，并在BAE上上线该应用，访问您自己的应用地址，如果您是使用示例程序，可以访问 域名+index.html#/itworks

-------

### FAQ

------

* 数据库连接数超限，{ [MongoError: Connect Number Excceed] name: 'MongoError', errmsg: 'Connect Number Excceed', ok: 0 }   
	在BAE目录中删除node_modules/mongodb目录并进行svn提交，本地目录不变。


###Contributor list

------

* 魏旭

