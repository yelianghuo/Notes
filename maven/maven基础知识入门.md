
  记录Maven基础概念！


### mvn compile
  在pom.xml的同级目录下执行该命令，Maven会下载所有的插件和依赖然后编译我们的源文件到${basedir}/target/classes目录下；${basedir}即我们项目的根路径。

### mvn package
  打包，在pom.xml文件中通过<packaging>标签指定打包类型(e.g. JAR, WAR, EAR, etc.)，产生的JAR文件位于${basedir}/target目录中，其实该命令包含了编译过程。

### mvn clean
  删除编译产生的文件，即${basedir}/target/下的编译文件

### mvn install
  该指令是将我们产生的JAR包安装到本地的仓库中，想想自己以前在项目中经常会适用"mvn clean install"，嘿嘿！虽然说这样执行也可以达到重新编译并生成JAR包的效果，但是还是得弄清楚每一个指令的执行意图。


### todo dependencyManagerment

  愿我们都做一个明白的人；本文记录maven的基础知识，愿在以后的开发中不能浅尝即止，要明白自己在做什么、为什么要这样做、这样做的结果。