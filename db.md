# 打包部署流程

## 打包：

### Ta404:

#### 后台：

1. 如图，打开后台项目的application.yml配置文件，找到profiles属性，测试环境指定开启mybatis,dev,cas这三个配置文件（本机启动时去掉cas即可），正式环境打包时启用mybatis,prod,cas,log这四个配置文件

   ![](C:\Users\蔡君\AppData\Roaming\Typora\typora-user-images\image-20201201204602344.png)

2. 如图，打开启动模块的pom文件，找到ojdbc版本配置，测试环境使用oracle 11g，所以使用6.0版本；正式环境使用oracle 12c，所以打包到正式环境时需要改成8.0版本。

   ![image-20201201205200921](C:\Users\蔡君\AppData\Roaming\Typora\typora-user-images\image-20201201205200921.png)

3. 如图，打开idea的maven工具，然后在root模块的生命周期中先执行clean，然后package。（通过命令行执行也可以）

   ![image-20201201205620813](C:\Users\蔡君\AppData\Roaming\Typora\typora-user-images\image-20201201205620813.png)

4. 默认打成jar包，在启动模块的target目录下。

   ##### 注：后续网络环境变化以后，测试环境和正式环境统一存储路径，数据库地址以及cas配置信息要根据实际IP做相应更改。

#### 前端：

1. 如图，打开前端项目的faceConfigonfig.js文件，根据汉字提示，启用相应代码。如果前端没有配置其他路径，那就只需要更改basePath属性，指向该项目后台地址（目前正式和测试环境都是通过nginx转发，所以只想nginx地址即可）；如果还配置有其他属性，根据环境做相应更改。

   ![image-20201201210714539](C:\Users\蔡君\AppData\Roaming\Typora\typora-user-images\image-20201201210714539.png)

2. 到前端项目路径下，命令行执行命令 

   ~~~shell
   npm run build
   ~~~

   ![image-20201202232142435](C:\Users\蔡君\AppData\Roaming\Typora\typora-user-images\image-20201202232142435.png)

   打包完成后会在项目路径下级生成一个dist文件夹（如果打包完成后发现basePath路径写错了，可以在dist目录下的js文件夹中找xxx-faceConfig文件，然后修改即可）

3. 修改dist文件夹名，规则为 后端项目上下文加 _front（例如：portal_front,cbass_front），然后将整个文件夹压缩，注意压缩方式选择.zip



### Ta3cloud:

1. 需要修改的文件如图：

   ![image-20201201213041034](C:\Users\蔡君\AppData\Roaming\Typora\typora-user-images\image-20201201213041034.png)

2. 修改的内容，根据各文件中说明，启用或者注释相应代码即可，具体如下：

   ![image-20201201213218933](C:\Users\蔡君\AppData\Roaming\Typora\typora-user-images\image-20201201213218933.png)

   ![image-20201201213258285](C:\Users\蔡君\AppData\Roaming\Typora\typora-user-images\image-20201201213258285.png)

   ![image-20201201213326841](C:\Users\蔡君\AppData\Roaming\Typora\typora-user-images\image-20201201213326841.png)

   ![image-20201201213404962](C:\Users\蔡君\AppData\Roaming\Typora\typora-user-images\image-20201201213404962.png)

   ![image-20201201213431266](C:\Users\蔡君\AppData\Roaming\Typora\typora-user-images\image-20201201213431266.png)

   ![image-20201201213513127](C:\Users\蔡君\AppData\Roaming\Typora\typora-user-images\image-20201201213513127.png)

   ![image-20201201213539621](C:\Users\蔡君\AppData\Roaming\Typora\typora-user-images\image-20201201213539621.png)

3. 配置更改完成以后，使用idea工具打包:

   ![image-20201201213722781](C:\Users\蔡君\AppData\Roaming\Typora\typora-user-images\image-20201201213722781.png)

   ![image-20201201213754111](C:\Users\蔡君\AppData\Roaming\Typora\typora-user-images\image-20201201213754111.png)

4. 正式环境使用oracle 12c，所以项目中用到的jar包ojdbc 6要换成ojdbc 8。

5. 打包完成后会在项目的out目录下生成相应的war包，注意将包名改成项目上下文。

   

## 部署：

### 连接服务器

具体连接正式环境的步骤参考 <u>福州智慧养老运维</u>。

### 前后端分离项目部署：

#### 前端：

1. 进入跳板机以后将前端项目zip包上传到跳板机指定目录，打开FinalShell,连接ng主机

   ![image-20201202223930693](C:\Users\蔡君\AppData\Roaming\Typora\typora-user-images\image-20201202223930693.png)

2. 连接成功以后进入项目资源目录

   ~~~shell
   cd /u01/projects
   
   ls
   ~~~

   ![image-20201202224054532](C:\Users\蔡君\AppData\Roaming\Typora\typora-user-images\image-20201202224054532.png)

3. 点击上传，选择指定资源

   ![image-20201202224435416](C:\Users\蔡君\AppData\Roaming\Typora\typora-user-images\image-20201202224435416.png)

4. 上传成功以后将之前的项目备份（更名为-bak即可，以免修改代码出现问题，能及时回滚，避免生产事故），然后加压当前项目资源压缩包，最后删除压缩包

   ~~~shell
   mv cbass_front cbass_front-bak
   
   unzip cbass_front.zip
   
   rm -rf cbass_front.zip
   ~~~

5. 如果需要修改nginx配置

   ~~~shell
   cd /usr/local/nginx/conf // nginx配置地址
   
   vim nginx.conf
   
   /usr/local/nginx/sbin/nginx // nginx 启动
   
   /usr/local/nginx/sbin/nginx -s stop // nginx 停止
   
   /usr/local/nginx/sbin/nginx -s reload // nginx 重载
   ~~~

#### 后台：

1. 连接跳板机，上传jar包，打开FInalShell,根据项目找到指定服务器

   ![image-20201202230412041](C:\Users\蔡君\AppData\Roaming\Typora\typora-user-images\image-20201202230412041.png)

2. 找到当前项目进程，杀掉

   ~~~shell 
   netstat -anp|grep 8090
   
   kill -9 进程id
   ~~~

   ![image-20201202230612704](C:\Users\蔡君\AppData\Roaming\Typora\typora-user-images\image-20201202230612704.png)

3. 进入项目目录,删除上次备份jar包，将当前项目jar包备份

   ~~~shell
   cd /u01/projects/hbascs
   
   ls
   
   rm -rf hbascs.jar.bak
   
   mv hbascs.jar hbascs.jar.bak
   ~~~

4. 上传jar包，启动项目（上传方式同上传项目静态资源）

   ~~~shell
   ls 
   
   ./hbascs-run.sh
   ~~~

5. 查看日志(注意查看最新日志文件)

   ~~~shell
   cd ../log
   
   ls
   
   tail -100f ../logfile_2020-12-02_1.log
   ~~~

### 普通web项目部署：

普通web项目部署在tomcat容器下。

1. 连接跳板机，上传war包，打开FInalShell,根据项目找到指定服务器

2. 进入tomcat的bin目录，关闭tomcat（如果关闭不了，尝试通过进程ID直接杀掉）

   ~~~shell
   cd /u01/software/web/tomcat/apache-tomcat-8.5.59/bin/
   
   ./shutdown.sh
   ~~~

3. 进入webapps目录，备份替换项目资源

   ~~~shell
   cd ../webapps
   
   rm -rf scorgmg.war.bak
   
   mv scorgmg.war scorgmg.war.bak
   ~~~

4. 上传最新的war包到webapps目录下，然后启动

   ~~~
   cd ../bin
   
   ./startup.sh
   ~~~

5. 查看日志

   ~~~
    tail -100f ../logs/catalina.out
   ~~~

   