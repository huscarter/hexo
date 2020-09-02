---
title: android发布到nexus私服
date: 2018-03-09 13:29:44
categories: android
tags:
     - maven
description: 为了减少编译时间，将项目中用到的library module做成maven类库，使之可以被其他项目引用
---

为了减少编译时间，将项目中用到的library module做成maven类库，使之可以被其他项目引用

### 创建自己的组建项目
1. android的发布至私服的项目最好是有app宿主和一个XxxLib2个module项目构成。

2. app可用于测试XxxLib，为XxxLib建立各种功能的测试例子，方便后续人员参照。

3. 建好之后将其上传到版本服务器，以做后期版本更新。 

### 将XxxLib发布到私服
1. 在gradle.properties文件中定义整个项目的配置信息
这里赘述一下gradle.properties是AndroidStudio自动创建用来设置项目属性的文件，对于有多个module的中大型项目，使用gradle.properties来统一各个module的配置将十分有利项目的维护。比如在gradle.properties中设置项目的版本，support版本和其他第三方库的版本，项目中一些属性的标志等等。

2. gradle.properties内容参照
```
org.gradle.jvmargs=-Xmx4096M

# 编译sdk版本号
COMPILE_SDK_VERSION=25
# 编译工具版本号
BUILD_TOOLS_VERSION=25.0.0
# 最小sdk版本号
MIN_SDK_VERSION=17
# 运行sdk版本号
TARGET_SDK_VERSION=25
# support版本号
SUPPORT_VERSION=25.0.0

# app版本号
VERSION_CODE=20171012
# app版本名称
VERSION_NAME=1.0.0

# fresco 版本号
# FRESCO_VERSION=1.3.0

# glide 版本号
GLIDE_VERSION=4.0.0

# 是否混淆
MINIFY_ENABLE=true

# nexus config
NEXUS_PUBLIC_URL=http://10.10.10.33:8081/repository/maven-public/
NEXUS_RELEASE_URL=http://10.10.10.33:8081/repository/maven-releases/
NEXUS_NAME=admin
NEXUS_PASSWORD=admin123

# maven publish config
# 本次发布至私服的版本
RELEASE_VERSION=1.0.3
# 发布至私服的group标识
RELEASE_GROUP=com.xxx.android
# 发布至私服的类型
RELEASE_TYPE=aar
```

3. 每个library module的发布脚本都写在改module目录的maven.gradle文件中，该文件被build.gradle引用。
3.1 build.gradle引用maven.gradle代码
```
apply from: "maven.gradle"
```
3.2 maven.gradle内容参照
```
apply plugin: 'maven'

task androidJavadocs(type: Javadoc) {
    source = android.sourceSets.main.java.srcDirs
    classpath += project.files(android.getBootClasspath().join(File.pathSeparator))
}

task androidJavadocsJar(type: Jar, dependsOn: androidJavadocs) {
    classifier = 'javadoc'
    from androidJavadocs.destinationDir
}

task androidSourcesJar(type: Jar) {
    classifier = 'sources'
    from android.sourceSets.main.java.srcDirs
}

artifacts {
    archives androidSourcesJar
//    archives androidJavadocsJar
}

// 闭包进行配置
uploadArchives {
    repositories {
        mavenDeployer {
            repository(url: "${NEXUS_RELEASE_URL}") {
                authentication(userName: NEXUS_NAME, password: NEXUS_PASSWORD)
            }

            pom.project {
                groupId = "${RELEASE_GROUP}"
                artifactId = 'gallerylibrary'
                version = "${RELEASE_VERSION}"
                packaging "${RELEASE_TYPE}"

                licenses {
                    license {
                        name 'The Apache Software License, Version 2.0'
                        url 'http://www.apache.org/licenses/LICENSE-2.0.txt'
                    }
                }

                developers {
                    developer {
                        id 'xxxxxx@xxx.com'
                        name 'xxxxxx'
                        email 'xxxxxx@xxx.com'
                    }
                }

            }// end pom.project

        }// end maven deploy
    }
}
```

4. 发布至maven命令
```
./gradlew upload
```

5. 发布完可登录[nexus服务器](http://10.10.10.33:8081/#browse/browse/components:maven-releases)查看。

### 引用发布好的lib
1. 需要在工程的build.gradle中加入maven私服的地址
```
allprojects {
    repositories {
        jcenter()
        mavenCentral()
        maven{
            url "${NEXUS_PUBLIC_URL}"
        }
    }
}
```

2. 在module的build.gradle直接引用
```
// 引用格式为[groupId]:[artifactId]:[version]
compile "com.xxx.android:gallerylibrary:1.0.0"
```

### 注意事项
1. 在gradle.properties不得设置proxy，否则无法引用私服。

2. 引用私服编译通过后,会在本地仓库留缓存，如果不升级lib版本重新发布，本地是不会更新的，除非删除本地缓存。

2.1 MAC 缓存地址:
```
/Users/用户名/.gradle/caches/modules-2/files-2.1
```
2.2 windows 缓存地址:
```
C:\Users\用户名\.gradle\caches\modules-2\files-2.1
```

3. 请统一遵守maven发布的命名规范，以使lib引用有章可循、方便简单。