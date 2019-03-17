### 				MongoDB安全认证

#### 1.用户认证

​	mongodb 默认启动不加任何参数时，是没有身份认证的，任何人都可以登录进行任何操作。

​	启动时添加--auth参数开启身份验证模式(或在配置文件中auth=ture，强制不使用身份验证则添加--noauth)——当设置为true时候，进入数据库需要auth验证，当数据库里没有用户，则不需要验证也可以操作。直到创建了第一个用户，之后操作都需要验证。

##### 1.1创建用户管理员

```mongodb
use admin #只有在admin上才能创建权限用户，用于管理所有数据库管限的用户。
db.createUser({user:"root",pwd:"root",roles:			          					[{role:"root",db:"admin"}]})  #创建root用户
```

常用的权限roles:

​	　root：即root权限，可以管理所有的数据库及用户，拥有所有角色的所有权限。只能创建在admin上

​	userAdminAnyDatabase:拥有所有数据库的用户管理权限

​	dbAdminAnyDatabase:拥有所有数据库的数据库管理权限

　	readWriteAnyDatabase:拥有所有数据库的读写权限

​	这些角色都只能创建在admin数据库上。

　	去掉AnyDatabase后可以创建在普通数据库上。

​	