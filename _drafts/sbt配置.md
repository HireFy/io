## ubuntu 安装 sbt

```shell
echo "deb https://dl.bintray.com/sbt/debian /" | sudo tee -a /etc/apt/sources.list.d/sbt.list
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 2EE0EA64E40A89B84B2DF73499E82A75642AC823
sudo apt-get update
sudo apt-get install sbt
```

在`/usr/share/sbt/conf`下编辑sbtconfig.txt，添加:
```
-Dsbt.boot.directory=/home/jw/.sbt/boot
-Dsbt.global.base=/home/jw/.sbt
-Dsbt.repository.config=/home/jw/.sbt/repo.properties
-Dsbt.repository.secure=false
```

然后编辑用户主目录下`.sbt/repositories`:
```
[repositories]  
local
Nexus osc : https://code.lds.org/nexus/content/groups/main-repo
Nexus osc thirdparty : https://code.lds.org/nexus/content/groups/plugin-repo/
typesafe: http://repo.typesafe.com/typesafe/ivy-releases/, [organization]/[module]/(scala_[scalaVersion]/)(sbt_[sbtVersion]/)[revision]/[type]s/[artifact](-[classifier]).[ext], bootOnly  
typesafe2: http://repo.typesafe.com/typesafe/releases/
sbt-plugin: http://repo.scala-sbt.org/scalasbt/sbt-plugin-releases/
sonatype: http://oss.sonatype.org/content/repositories/snapshots  
uk_maven: http://uk.maven.org/maven2/  
ibibli: http://mirrors.ibiblio.org/maven2/  
repo2: http://repo2.maven.org/maven2/

comp-maven:http://mvnrepository.com/artifact/
store_cn:http://maven.oschina.net/content/groups/public/
store_mir:http://mirrors.ibiblio.org/maven2/
store_0:http://maven.net.cn/content/groups/public/
store_1:http://repo.typesafe.com/typesafe/ivy-releases/
store_2:http://repo2.maven.org/maven2/

sbt-releases-repo: http://repo.typesafe.com/typesafe/ivy-releases/, [organization]/[module]/(scala_[scalaVersion]/)(sbt_[sbtVersion]/)[revision]/[type]s/[artifact](-[classifier]).[ext]
sbt-plugins-repo: http://repo.scala-sbt.org/scalasbt/sbt-plugin-releases/, [organization]/[module]/(scala_[scalaVersion]/)(sbt_[sbtVersion]/)[revision]/[type]s/[artifact](-[classifier]).[ext]
maven-central: http://repo1.maven.org/maven2/
```

