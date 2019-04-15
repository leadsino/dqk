### 				MongDB角色简述

​	MongoDB为了方便管理员管理权限，在DB级别上预先定义了内置角色；如果用户需要对权限进行更为细致的管理，MongoDB允许用户创建自定义的角色，能够在集合级别上控制User能够执行的操作。MongoDB使用角色（Role）授予User访问资源的权限，Role决定User能够访问的数据库资源和执行的操作。一个User能够被授予一个或多个Role，如果User没有被授予Role，那么就没有访问MongoDB系统的权限。

**1. 内置角色（Built-In Roles）**

​	内置角色是MongoDB预定义的角色，操作的资源是在DB级别上。MongoDB拥有一个最高权限的角色：`**root**，拥有最大权限，能够在系统的所有资源上执行任意操作。`

**数据库用户角色（Database User Roles）：**

+ **read：**授予User只读数据的权限
+ **readWrite：**授予User读写数据的权限

**数据库管理角色（Database Administration Roles）：**

+ **dbAdmin：**在当前dB中执行管理操作
+ **dbOwner：**在当前DB中执行任意操作
+ **userAdmin：**在当前DB中管理User

**备份和还原角色（Backup and Restoration Roles）：**

+ **backup**
+ **restore**

**跨库角色（All-Database Roles）：**

+ **readAnyDatabase：**授予在所有数据库上读取数据的权限
+ **readWriteAnyDatabase：**授予在所有数据库上读写数据的权限
+ **userAdminAnyDatabase：**授予在所有数据库上管理User的权限
+ **dbAdminAnyDatabase：**授予管理所有数据库的权限

**集群管理角色（Cluster Administration Roles）：**

+ **clusterAdmin**：授予管理集群的最高权限
+ **clusterManager**：授予管理和监控集群的权限，A user with this role can access the config and local databases, which are used in sharding and replication, respectively.
+ **clusterMonitor**：授予监控集群的权限，对监控工具具有readonly的权限
+ **hostManager**：管理Server

**2，用户自定义的角色（User-Defined Roles）**

​	内置角色只能控制User在DB级别上执行的操作，管理员可以创建自定义角色，控制用户在集合级别（Collection-Level）上执行的操作，即，控制User在当前DB的特定集合上执行特定的操作。

​	在创建角色时，必须明确Role的四个特性：

+ **Scope**：角色作用的范围，创建在Admin中的角色，能够在其他DB中使用；在其他DB中创建的角色，只能在当前DB中使用；
+ **Resource**：角色控制的资源，表示授予在该资源上执行特定操作的权限；
+ **Privilege Actions**：定义了User能够在资源上执行的操作，系统定义Action是：[Privilege Actions](https://docs.mongodb.com/manual/reference/privilege-actions/#security-user-actions)；
+ **Inherit**：角色能够继承其他角色权限

