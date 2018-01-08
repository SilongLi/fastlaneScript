# CocoaPods私有化组件流程 + fastlane自动维护升级脚本

>### **注意：如下几个步骤需要手动集成：**
> 
> 这几个步骤基本只是在第一次创建私有库的时候，需要手动操作。

## 1.直接使用Cocoapods自带的命令创建pod库
###使用pod的命令创建组件库的好处：

- 1. 直接可以直接编写组件代码；
- 2. 可以通过”pod install“命令，直接使用封装的组件代码，查看封装情况；
- 3. 组建源码和第三方使用的代码在一个项目中，方便编写和测试。

```Swift
pod lib create 项目名称
```

![Xcode文件目录](http://chuantu.biz/t6/199/1515375333x-1566638193.png)

## 2.把封装的代码放到Classes目录下

![文件目录](http://chuantu.biz/t6/199/1515375282x-1566638193.png)

## 3.创建远程私有库，并添加远程库关联： 

[repoName] 为**私有仓库名**；[repoURL]为**远程代码库地址**。

1). remote远程私有仓库

```Swift
pod remote add origin [repoURL]
``` 

2). 添加远程私有索引库

```Swift
pod repo add [repoName] [repoURL]
``` 

3). 需要我们关联下本地的分支和远程的分支

```Swift 
git branch --set-upstream 本地分支 远程分支 
```


## 4. 修改podspec和README.md文件

---------------------
---------------------
---------------------
---------------------

>### **注意：如下步骤已经使用fastlane脚本实现**
> 
> 以后我们在更新私有库时，直接执行脚本即可实现一步上传。

## 1. 本地验证podspec信息、
> 我习惯先本地验证podspec信息，这样可以统一只提交一个commit.

```Swift
// xxx为你的私有组件podspec的名称
pod lib lint [xxx.podspec]  --allow-warnings

// 如果想查看具体的执行信息可使用如下命令：
pod lib lint [xxx.podspec]  --verbose
```

## 2. 提交代码
```Swift
git add .
git commit -m “xxx”
git pull origin master
git push origin master
```

## 3. 提交版本号（标签）
```Swift
git tag -a 标签号 
git push origin --tag
```

## 4. 提交索引库
```Swift
pod repo push BruceLiLibs  xxx.podspec
```
BruceLiLibs为**私有库**名称，xxx为你的**podspec**名称。

---------------------
---------------------
---------------------
---------------------

# 【推荐】使用fastlane脚本提交
> 一行代码搞定私有库的维护升级工作。
> 
> 注意：
> 必须在根目录执行如下命令！(参数冒号后面不要有空格)


```Swift
// 命令组成（ManagerLibs为我的脚本名称），接收四个参数：
fastlane ManagerLibs tag:[版本号] message:"[本次升级的日志信息]" repo:[私有库名称] podspec:[podspec名称]

// 案例
fastlane ManagerLibs tag:1.0.0 message:"封装私有库" repo:BruceLiLibs  podspec: fastlaneDemo
```

如果忘了私有库名称，可以用**pod repo**查看。


---------------------
---------------------
---------------------
---------------------

## 可能会遇到的错误

### 本地地检查代码时，可能会遇见Swift版本的问题

```Swift
WARN  | [iOS] swift_version: The validator for Swift projects uses Swift 3.0 by default, if you are using a different version of swift you can use a `.swift-version` file to set the version for your Pod. For example to use Swift 2.3, run: 
    `echo "2.3" > .swift-version`
```

这时根据提示执行如下命令即可：

```Swift
echo "4.0" > .swift-version
```

###如果在执行fastlane脚本时报如下错误：

```Swift
There is no tracking information for the current branch.
Please specify which branch you want to merge with.
See git-pull(1) for details.
git pull <remote> <branch>
If you wish to set tracking information for this branch you can do so with:
git branch --set-upstream-to=origin/<branch> master 
```
执行如下命令关联一下本地和远程库即可：

```Swift 
git branch --set-upstream 本地分支 远程分支 
``` 


## 其他
### fastlane 
[**fastlane官网**](https://fastlane.tools)       
[**github地址**](https://github.com/fastlane/fastlane)
#### 安装 [官方安装文档](https://docs.fastlane.tools/getting-started/ios/setup/#use-a-gemfile)

> 安装fastlane之前，确保ruby版本>2.0.0，否则先升级ruby。
> [ruby升级](http://www.mamicode.com/info-detail-1574918.html)

```Swift 
// fastlane安装命令
sudo gem install fastlane --verbose

// 查看版本命令
fastlane --version
```

### fastlane的创建
首先在名命令控制窗口中，进入项目的根目录文件位置；然后执行如下代码创建fastlane脚本文件。这一步的创建需要一个**付费**的开发者账号。

```Swift
fastlane init  
```

我们也可以直接用如下命令直接创建fastlane脚本：

```Swift
touch fastlane
```

### 根据文档编程脚本内容[fastlane actions文档](https://docs.fastlane.tools/actions/Actions)

### 检查脚本是否正确命令

```Swift
fastlane lanes
```

### 自定义新的action命令
会在fastlane文件目录中生成一个actions的文件夹，里面有自定义action+rb格式的文件，我们直接在这个文件中添加代码即可。

这里自定义了**pod _ repo _ push**和**remove_tag**命令。
[github中大神开源的actions大全](https://github.com/fastlane/fastlane/tree/master/fastlane/lib/fastlane/actions)

```Swift
// 自定义action操作
fastlane new_action

// 可以通过如下命令查看，自己定义的action（xxx为action名称）
fastlane action xxx
```


