---
title: flutter通过jenkins自动化打包
date: 2019-10-31 09:29:44
categories: flutter
tags:
     - jenkins
description: 为了实现CI/CD，可以引入jenkins。
---

### jenkins安装
推荐使用mac OS的homebrew来安装，方便后期管理，如果选用pkg安装请自行到官网下载。[mac版jenkins下载地址](https://jenkins.io/zh/download/)

#### jenkins安装通过homebrew
因为jenkins是java开发，安装前现确保系统已安装了jdk
1. 安装jenkins，terminal运行
```
brew install Jenkins
```
2. 输入如下命令运行，在浏览器中输入[网址](http://localhost:8080/)访问。
```
// 直接输入
jenkins
// 或者输入（2.202为你安装jenkins的版本号）
launchctl load /usr/local/Cellar/jenkins/2.202/homebrew.mxcl.jenkins.plist
```

3. 首次登录需要输入管理员密码，密码的获取地址在terminal中输入如下命令可查看。
```
sudo cat /Users/Shared/Jenkins/Home/secrets/initialAdminPassword
```
4. 首次进入jenkins会提示安装插件，之后就可以在里面部署我们的项目了。

5. 关闭jenkins
```
// 2.202为你安装jenkins的版本号
launchctl unload /usr/local/Cellar/jenkins/2.202/homebrew.mxcl.jenkins.plist
```

### jenkins插件安装
初步安装的jenkins对于部署移动项目的插件是缺失的，需要自行下载。

#### 基本软件
1. git或者svn，根据你的版本控制器自行下载安装到电脑。[git下载地址](https://git-scm.com/downloads)
2. JDK，Android开发的基础。[JDK下载地址](https://www.oracle.com/java/technologies/javase-downloads.html)

#### iOS插件
在jenkins的"系统设置"-"插件管理"页面搜索下面的插件安装
1. Keychains and Provisioning Profiles Management（用于iOS证书配置）
2. Xcode integration（用于iOS项目编译）

#### fir插件（用于将app的安装包发布到fir）
1. [fir plugin下载地址](fir-plugin-1.9.5.hpi)。
2. jenkins首页选择插件管理，选择高级，选择本地上传插件。

#### Android插件
无插件，只需配置Android的SDK、gradle编译工具和java环境。

#### flutter插件
无插件，只需配置flutter环境。

### 环境配置
#### flutter的jenkins环境配置
1. 在系统管理-环境变量栏目添加键值对
```
FLUTTER_HOME=[你flutterd的sdk目录]
```

#### 系统运行命令的jenkins环境配置
1. 在系统管理-环境变量栏目添加键值对
```
PATH=[你flutterd的sdk目录]
```

#### Android的jenkins环境配置
1. 在系统管理-环境变量栏目添加键值对
```
ANDROID_HOME=[你Android的sdk目录]
```

####  iOS的jenkins环境配置（Keychains 和 Provisioning Profiles配置）
在jenkins首页，找到Keychains and Provisioning Profiles进入配置
1. Keychains配置
上传login.keychain-db文件，上传之后jenkins会自动识别。login.Keychains的地址在 ~/Library/Keychains目录

2. Provisioning Profiles配置
上传Provisioning Profiles文件，文件地址在/Users/用户名/Library/MobileDevice/Provisioning Profiles目录，将目录下的所有文件上传。

###  创建任务
#### Android任务
1. 选择构建一个自由风格的项目，输入自己的任务名称，推荐是自己的项目名
2. 进入任务，选择进入配置页面配置
2.1  源码管理（git为例）
```
在Repository URL中输入自己项目的地址，如https://gitlab.xxx.com/app/xxx.git。
在Credentials中添加git的账号密码。
在指定分支中设置git分支，如*/v1.0.0
```
2.2 如有需要自行配置构建触发器
```
勾选Poll SCM，在输入框中输入想要的触发表达式即可。
SCM语法[分钟][小时][天][月][周]。
H/60 * * * *（每个小时构建一次）。
0 * * * *（每个小时的第0分钟构建）
0 10,14 * * 1-5(每周一到周五的10点和14点构建一次)
```
2.3 构建shell
```
flutter clean
# huawei为渠道标识
flutter build apk --flavor huawei
fir publish build/app/outputs/apk/baidu/release/app-baidu-release.apk -T "此处为fir的api token"
```

#### iOS任务
1. 选择构建一个自由风格的项目，输入自己的任务名称，推荐是自己的项目名
2. 进入任务，选择进入配置页面配置
2.1  源码管理（同Android）

2.2 如有需要自行配置构建触发器（同Android）

2.3 构架环境
```
勾选Keychains and Code Signing Identities，选择添加Keychain，选择已上传的login.keychain。
勾选Mobile Provisioning Profiles，选择添加Provisioning Profiles，选择已上传的Provisioning Profiles。
```

2.4 构建shell
```
security set-key-partition-list -S apple-tool:,apple: -s -k "你电脑的密码" ~/Library/Keychains/login.keychain-db
#假设脚本放置在与项目相同的路径下
project_path=$(pwd)
# flutter环境变量设置
export PUB_HOSTED_URL=https://pub.flutter-io.cn
export FLUTTER_STORAGE_BASE_URL=https://storage.flutter-io.cn
# for ios
export LANG=en_US.UTF-8
export LANGUAGE=en_US.UTF-8
export LC_ALL=en_US.UTF-8

cd ios
pod setup
pod install

cd ..
flutter build ios

cd $project_path/ios
#取当前时间字符串添加到文件结尾
now=$(date +"%Y_%m_%d_%H:%M")
#指定项目的scheme名称
scheme="Runner"
#指定要打包的配置名
configuration=${BUILD_TYPE}
#指定打包所使用的输出方式，目前支持app-store, package, ad-hoc, enterprise, development, 和developer-id，即xcodebuild的method参数
if [ $BUILD_TYPE == "Release" ]
then
    export_method="app-store"
elif [ $BUILD_TYPE == "AdHoc" ]
then
    export_method="ad-hoc"
else
    export_method="development"
fi
#指定项目地址
workspace_path="${project_path}/ios/Runner.xcodeproj"
#指定输出路径
rm -rf "${project_path}/ipa"
mkdir -p "${project_path}/ipa"
output_path="${project_path}/ipa"
#指定输出归档文件地址
archive_path="$output_path/app_${export_method}_${now}.xcarchive"
#指定输出ipa地址
ipa_path="$output_path/app_${BUILD_TYPE}_${BUILD_BRANCH}_${now}.ipa"
#指定输出ipa名称
ipa_name="app_${BUILD_TYPE}_${BUILD_BRANCH}_${now}.ipa"
commit_msg="$1"
#输出设定的变量值
echo "===workspace path: ${workspace_path}==="
echo "===archive path: ${archive_path}==="
echo "===ipa path: ${ipa_path}==="
echo "===export method: ${export_method}==="
echo "===commit msg: $1==="
#先清空前一次build
fastlane gym --scheme ${scheme} --clean --configuration ${configuration} --archive_path ${archive_path} --export_method ${export_method} --output_directory ${output_path} --output_name ${ipa_name}
#上传到fir
if [ ${export_method} != "app-store" ]
then
fir publish ${ipa_path} -T "此处为fir的api token" -c $commit_msg
fi
```

### 问题总结

#### 如果提示command not found
1. 在shell脚本前添加
```
export PATH=$PATH:$FLUTTER_HOME/bin/flutter
export PATH=$PATH:/usr/local/bin/fir
```
2. 在jenkins环境变量前添加PAT，值就是terminal中echo $PATH的值

#### 报错：can't find gem fir-cli (>= 0.a) with executable fir
在terminal中输入此命令
```
sudo gem install -n /usr/local/bin cocoapods
```

#### ios编译提示CocoaPods minimum required version 1.6.0 or greater not installed
在terminal中输入此命令
```
sudo gem install cocoapods
```

#### 提示invalid byte sequence in US-ASCII
1. 在shell脚本前添加如下命令（注意后面不要有空格，否则失效）
```
export LC_ALL=en_US.UTF-8
export LANG=en_US.UTF-8
```

#### 开启jenkins之后xcode的ad_hoc打包失败，错误信息如下
```
cannot load such file -- sqlite3 (LoadError)
```
1. 解决方案是关闭jenkins，重启电脑。如果还有问题查看是否机器安装了sqlite3，如果没有可运行如下命令安装。
```
gem install sqlite3
```