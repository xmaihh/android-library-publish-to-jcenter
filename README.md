#5分钟发布Android Library项目到JCenter

android-library-publish-to-jcenter是一个帮助Android开发者将AAR库发布到jcenter的项目，android-library-publish-to-jcenter已经将需要写的脚本封装好了，我们只需要配置相关属性即可在5分钟之内发布我们的项目到JCenter（不包括审核时间哦）

####1. 注册Bintray账号 ([传送门](https://bintray.com))
Bintray是jcenter的托管商，因此你必须注册一个Bintray账号，注册完账号后记下你的用户名以及API Key。

登陆后在首页右上角点击用户名进入个人主页，然后点击用户名下面的Edit进入个人信息编辑页面，接下来点击页面左边列表的最后一项API Key

![api_key](http://github.com/xiaopansky/android-library-publish-to-jcenter/raw/master/docs/sample_apikey.png)

如图所示点击最右边箭头指示的复制按钮即可复制你的API Key

####2. 配置插件
首先升级Android插件到最新版，然后添加maven插件bintray插件，完成后如下：
```groovy
// Top-level build file where you can add configuration options common to all sub-projects/modules.

buildscript {
    repositories {
        jcenter()
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:2.2.2'
        classpath 'com.github.dcendents:android-maven-gradle-plugin:1.5'
        classpath 'com.jfrog.bintray.gradle:gradle-bintray-plugin:1.7.2'
        // NOTE: Do not place your application dependencies here; they belong
        // in the individual module build.gradle files
    }
}

allprojects {
    repositories {
        jcenter()
    }
}
```

>* android-maven-gradle-plugin插件是用来打包Maven所需文件的
>* gradle-bintray-plugin插件是用来将生成的Maven所需文件上传到Bintray的

####3. 配置项目信息
下载[project.properties](https://github.com/xiaopansky/android-library-publish-to-jcenter/raw/master/project.properties)文件并放到你的library module目录下

project.properties文件的原始内容如下：
```properties
#project
project.name=
project.groupId=
project.artifactId=
project.packaging=aar
project.siteUrl=
project.gitUrl=

#javadoc
javadoc.name=
```

详解：
>* project.name：项目名称
>* project.groupId：项目组ID，通常情况下如果你的包名为com.example.test，那么项目组ID就是com.example
>* project.artifactId：项目ID，通常情况下如果你的包名为com.example.test，那么项目ID就是test
>* project.packaging：包类型，Android库是aar
>* project.siteUrl：项目官方网站的地址，没有的话就用Github上的地址，例如：https://github.com/xiaopansky/Sketch
>* project.gitUrl：项目的Git地址，例如：https://github.com/xiaopansky/Sketch.git
>* javadoc.name：生成的javadoc打开后主页显示的名称，通常跟项目名称一样即可

完成配置后如下所示：
```properties
#project
project.name=Sketch
project.groupId=me.xiaopan
project.artifactId=sketch
project.packaging=aar
project.siteUrl=https://github.com/xiaopansky/Sketch
project.gitUrl=https://github.com/xiaopansky/Sketch.git

#javadoc
javadoc.name=Sketch
```

你无需配置项目版本，会自动从你的build.gradle中获取版本名称作为项目版本

####4. 配置Bintray账号以及开发者信息
下载[local.properties](https://github.com/xiaopansky/android-library-publish-to-jcenter/raw/master/local.properties)文件并放到你的library module目录下

local.properties文件的原始内容如下：
```properties
#bintray
bintray.user=
bintray.apikey=

#developer
developer.id=
developer.name=
developer.email=
```

详解：
>* bintray.user：你的Bintray的用户名
>* bintray.apikey：你的的Bintray的API Key
>* developer.id：通常是你在开源社区的昵称
>* developer.name：你的姓名
>* developer.email：你的邮箱

完成配置后如下所示：
```properties
#bintray
bintray.user=xiaopansky
bintray.apikey=*****************************

#developer
developer.id=xiaopan
developer.name=********
developer.email=sky@xiaopan.me
```

*号显示的为个人信息不反方便透露，还请见谅

注意要将local.proerties文件加入忽略列表，以免被提交到Github或其他网站泄露个人信息

####5. 配置bintrayUpload.gradle
#####方法1：直接使用远程bintrayUpload.gradle文件
修改你的library module的build.gradle文件，在最后加上``apply from: "https://raw.githubusercontent.com/xiaopansky/android-library-publish-to-jcenter/master/bintrayUpload.gradle"``，如下所示：
```groovy
apply plugin: 'com.android.library'

android {
    compileSdkVersion 22
    buildToolsVersion "22.0.0"

    defaultConfig {
        minSdkVersion 7
        targetSdkVersion 22
        versionCode 100
        versionName "1.0.0"
    }

    buildTypes {
        release {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        }
    }
}

apply from: "https://raw.githubusercontent.com/xiaopansky/android-library-publish-to-jcenter/master/bintrayUpload.gradle"
```

#####方法2：下载后使用本地bintrayUpload.gradle文件
首先下载[bintrayUpload.gradle](https://github.com/xiaopansky/android-library-publish-to-jcenter/raw/master/bintrayUpload.gradle)文件并放到你的library module目录下

然后修改你的library module的build.gradle文件，在最后加上``apply from: "bintrayUpload.gradle"``，如下所示：
```groovy
apply plugin: 'com.android.library'

android {
    compileSdkVersion 22
    buildToolsVersion "22.0.0"

    defaultConfig {
        minSdkVersion 7
        targetSdkVersion 22
        versionCode 100
        versionName "1.0.0"
    }

    buildTypes {
        release {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        }
    }
}

apply from: "bintrayUpload.gradle"
```

推荐大家使用第一种方案，简单快捷，至此配置工作已全部结束

####6. 执行命令打包并上传到Bintray
打开终端进入项目目录下，执行``gradlew bintrayUpload``命令即可

另外，如果你的本地已经配置了Gradle了，那么执行``gradle bintrayUpload``命令也可以。gradlew是Gradle的一层封装，如果你本地没有安装Gradle gradlew就会自动下载Gradle

####7. 请求提交你的项目到jcenter
前面所有步骤走完之后实际上只是上传了你的项目到Bintray而已，并没有被包含在jcenter中，要想提交到jcenter中还需要Bintray的审核。

登入Bintray网站，进入个人中心，在右侧的Owned Repositories区域点击Maven的图标，进入你的Maven项目列表。

如果已经上传成功了，在这里就能看到你的项目，进入项目详情，在右下角的Linked To区域点击Add to JCenter，然后在Comments输入框里随便填写下信息，最后点Send提交请求即可

一般情况下审核需要4到5个小时，耐心等待就行了，审核通过后会给你发邮件通知你，并且以后更新项目就不需要再审核了。

####8. 一句话导入你的项目
当审核通过后，别人就可以一句话导入你的项目了，例如：
```groovy
compile 'me.xiaopan:sketch:2.0.0'
```

####9. 额外补充：
#####保持你的library module的名字同artifactId一样
因为在Bintray上你的项目的maven-metadata.xml文件的路径是``gruopId+"/"+module名称``。

例如你的groupId是com.example，artifactId是test，但module名称是library。

这时候项目文件是在com.example.test目录下的，但maven-metadata.xml文件却是在com.example.library目录下的。

这样一来如果你有多个项目groupId一样，artifactId不一样，但module名称都是library的话，可能就会冲突。

目前为止我还没有找到更好的解决办法，就只能让module名称和artifactId保持一致，如果你们谁有更好的办法，欢迎留言交流。

####10. 参考文章
>* [Android拓展系列(12)--使用Gradle发布aar项目到JCenter仓库](http://www.cnblogs.com/qianxudetianxia/p/4322331.html)
>* [使用Gradle发布Android开源项目到JCenter](http://blog.csdn.net/maosidiaoxian/article/details/43148643)
>* [使用Gradle发布项目到JCenter仓库](http://zhengxiaopeng.com/2015/02/02/使用Gradle发布项目到JCenter仓库/)
>* [Android 项目打包到 JCenter 的坑](http://www.jianshu.com/p/c721f9297b2f?utm_campaign=hugo&utm_medium=reader_share&utm_content=note)

####11. 常见问题
>* `Error:Cause: org/gradle/api/publication/maven/internal/DefaultMavenFactory`：当你使用的Gradle版本是2.4以上，Android插件版本是1.3.0以上的时候就会出现这个问题，这时候你只需将android-maven-gradle-plugin插件版本改为**classpath 'com.github.dcendents:android-maven-gradle-plugin:1.3'**即可
>* `You are using JDK version ‘java version “1.7.0_71”’. Some versions of JDK 1.7 (e.g. 1.7.0_10) may cause class loading errors in Gradle.Please update to a newer version (e.g. 1.7.0_67)`：当你使用的Gradle版本是2.4以上，Andriod插件版本是1.2.3的时候就会出现这个问题，同样的你只需要将android-maven-gradle-plugin插件版本改为**classpath 'com.github.dcendents:android-maven-gradle-plugin:1.3'**即可
>* `No value has been specified for property 'packageName'.`出这个问题肯定是看文档不仔细，把project.properties文件放在了项目根目录下，一定要放在mudule目录下才可以
>* `Could not upload to 'https://*****.pom': HTTP/1.1 400 Bad Request [message:Unable to upload files: Maven group, artifact or version defined in the pom file do not match the file path '****.pom']`这个问题一般都是你的module的名字和你在project.properties
配置的artifactId不一致导致的，改成一样的即可（感谢[Weizhou He
 captainbupt](https://github.com/captainbupt)发现的这个问题）
