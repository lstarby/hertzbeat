---
id: package-deploy  
title: 通过安装包安装HertzBeat    
sidebar_label: 安装包方式部署    
---
> HertzBeat支持在Linux Windows Mac系统安装运行，CPU支持X86/ARM64。由于安装包自身不包含JAVA运行环境，需您提前准备JAVA运行环境。

安装部署视频教程: [HertzBeat安装部署-BiliBili](https://www.bilibili.com/video/BV1GY41177YL)   

1. 安装JAVA运行环境-可参考[官方网站](http://www.oracle.com/technetwork/java/javase/downloads/index.html)    
   要求：JDK11环境   
   下载JAVA安装包: [镜像站](https://repo.huaweicloud.com/java/jdk/)   
   安装后命令行检查是否成功安装   
   ```
   $ java -version
   openjdk version "1.8.0_312"
   OpenJDK Runtime Environment (Zulu 8.58.0.13-CA-macos-aarch64) (build 1.8.0_312-b07)
   OpenJDK 64-Bit Server VM (Zulu 8.58.0.13-CA-macos-aarch64) (build 25.312-b07, mixed mode)
   ```
2. 下载HertzBeat安装包
   下载您系统环境对应的安装包
   - 从[GITEE Release](https://gitee.com/dromara/hertzbeat/releases) 仓库下载
   - 从[GITHUB Release](https://github.com/dromara/hertzbeat/releases) 仓库下载

3. 配置HertzBeat的配置文件(可选)       
   解压安装包到主机 eg: /opt/hertzbeat  
   ``` 
   $ tar zxvf hertzbeat-[版本号].tar.gz   
   ```
   修改位于 `hertzbeat/config/application.yml` 的配置文件        
   替换里面的`warehouse`服务参数，IP端口账户密码   
   注意⚠️（若使用邮件告警，需替换里面的邮件服务器参数。若使用MYSQL数据源，需替换里面的datasource参数 参见[H2数据库切换为MYSQL](mysql-change)）
   具体替换参数如下:   
```yaml
warehouse:
   store:
      td-engine:
         enabled: false
         driver-class-name: com.taosdata.jdbc.rs.RestfulDriver
         url: jdbc:TAOS-RS://localhost:6041/hertzbeat
         username: root
         password: taosdata
      iot-db:
         enabled: false
         host: 127.0.0.1
         rpc-port: 6667
         username: root
         password: root
         # org.apache.iotdb.session.util.Version: V_O_12 || V_0_13
         version: V_0_13
         # if iotdb version >= 0.13 use default queryTimeoutInMs = -1; else use default queryTimeoutInMs = 0
         query-timeout-in-ms: -1
         # 数据存储时间：默认'7776000000'（90天,单位为毫秒,-1代表永不过期）
         expire-time: '7776000000'
         
spring:
   mail:
      # 请注意此为邮件服务器地址：qq邮箱为 smtp.qq.com qq企业邮箱为 smtp.exmail.qq.com
      host: smtp.exmail.qq.com
      username: example@tancloud.cn
      # 请注意此非邮箱账户密码 此需填写邮箱授权码
      password: example
      port: 465
```

4. 配置用户配置文件(可选,自定义配置用户密码)     
   HertzBeat默认内置三个用户账户,分别为 admin/hertzbeat tom/hertzbeat guest/hertzbeat     
   若需要新增删除修改账户或密码，可以通过修改位于 `hertzbeat/config/sureness.yml` 的配置文件实现，若无此需求可忽略此步骤 
   修改sureness.yml的如下**部分参数**：**[注意⚠️sureness配置的其它默认参数需保留]**

```yaml

# 用户账户信息
# 下面有 admin tom lili 三个账户
# eg: admin 拥有[admin,user]角色,密码为hertzbeat
# eg: tom 拥有[user],密码为hertzbeat
# eg: lili 拥有[guest],明文密码为lili, 加盐密码为1A676730B0C7F54654B0E09184448289
account:
   - appId: admin
     credential: hertzbeat
     role: [admin,user]
   - appId: tom
     credential: hertzbeat
     role: [user]
   - appId: guest
     credential: hertzbeat
     role: [guest]
 
```

5. 部署启动
   执行位于安装目录hertzbeat/bin/下的启动脚本 startup.sh 
   ``` 
   $ ./startup.sh 
   ```
6. 开始探索HertzBeat  
   浏览器访问 http://ip:1157/ 开始使用HertzBeat进行监控告警，默认账户密码 admin/hertzbeat。  

**HAVE FUN**

### 安装包部署常见问题

**最多的问题就是网络问题，请先提前排查**

1. **按照流程部署，访问 http://ip:1157/ 无界面**   
   请参考下面几点排查问题：
> 一：若切换了依赖服务MYSQL数据库，排查数据库是否成功创建，是否启动成功
> 二：HertzBeat的配置文件 `hertzbeat/config/application.yml` 里面的依赖服务IP账户密码等配置是否正确    
> 三：若都无问题可以查看 `hertzbeat/logs/` 目录下面的运行日志是否有明显错误，提issue或交流群或社区反馈

2. **日志报错TDengine连接或插入SQL失败**
> 一：排查配置的数据库账户密码是否正确，数据库是否创建   
> 二：若是安装包安装的TDengine2.3+，除了启动server外，还需执行 `systemctl start taosadapter` 启动 adapter    

3. **监控历史图表长时间都一直无数据**
> 一：Tdengine或IoTDB是否配置，未配置则无历史图表数据  
> 二：Tdengine的数据库`hertzbeat`是否创建
> 三: HertzBeat的配置文件 `application.yml` 里面的依赖服务 IotDB或Tdengine IP账户密码等配置是否正确   
