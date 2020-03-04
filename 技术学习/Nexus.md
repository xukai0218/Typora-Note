[](https://www.cnblogs.com/toSeeMyDream/p/4230611.html)

用户名:admin

密码:admin123

cd /root/robot/nexus/nexus-3.14.0-04/bin

不中断 后台运行

nohup ./nexus run &

# Maven 打包 package install deploy 区别

## 一、打包区别

mvn package：打包到本项目，一般在项目target目录下。
mvn install：打包到本地仓库，如果没设置Maven本地仓库，一般在用户/.m2目录下。
mvn deploy：打包上传到远程仓库，如：私服nexus等，需要配置pom文件。

## 二、打包过程

mvn clean package
依次执行：clean、resources、compile、testResources、testCompile、test、jar(打包)。
mvn clean install
依次执行：clean、resources、compile、testResources、testCompile、test、jar(打包)、install。
mvn clean deploy
依次执行：clean、resources、compile、testResources、testCompile、test、jar(打包)、install、deploy。

## 由上面分析主要区别如下：

package命令：完成项目编译、单元测试、打包功能，但打包文件未部署到本地Maven仓库和远程Maven仓库。
install命令：完成项目编译、单元测试、打包功能，同时把打包文件部署到本地Maven仓库，但未部署到远程Maven仓库。
deploy命令：完成项目编译、单元测试、打包功能，同时把打包文件部署到本地Maven仓库和远程Maven仓库。

## 全局配置文件

```
        <server>
            <id>maven-repository-releases</id>
            <username>admin</username>
            <password>admin123</password>
        </server>

        <server>
            <id>maven-repository-snapshots</id>
            <username>admin</username>
            <password>admin123</password>
        </server>
```

pom配置

```
    <distributionManagement>
        <repository>
            <!-- 这里的ID要和setting的id一致 -->
            <id>maven-repository-releases</id>
            <url>http://maven.ruigu.com/repository/maven-releases/</url>
        </repository>
        <!--这是打成快照版本的配置，如果不用这个snapshotRepository标签，打包失败，会报权限问题 -->
        <snapshotRepository>
            <id>maven-repository-snapshots</id>
            <url>http://maven.ruigu.com/repository/maven-snapshots/</url>
        </snapshotRepository>
    </distributionManagement
```

