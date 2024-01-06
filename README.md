CAS Overlay Template
============================

Generic CAS WAR overlay to exercise the latest versions of CAS. This overlay could be freely used as a starting template for local CAS war overlays. The CAS services management overlay is available [here](https://github.com/apereo/cas-services-management-overlay).

# 改造部分

## 介绍
本demo已集成jdbc和oauth2.0，默认开启https，本服务在测试时使用反向代理以解决公网证书问题，证书需替换，密码使用动态和静态加盐，默认1次。

## 登陆后获取code
https://cas.zuimeihui.com:88/cas/oauth2.0/authorize?response_type=code&client_id=oauth2-id-zuimeihui-com&redirect_uri=http://oauth.zuimeihui.com:8081

## 使用code获取accessToken
https://cas.zuimeihui.com:88/cas/oauth2.0/accessToken?grant_type=authorization_code&client_id=oauth2-id-zuimeihui-com&client_secret=oauth2-secret-zuimeihui-com&redirect_uri=http://oauth.zuimeihui.com:8081&code=OC-3-r4JbgvRbKb2jaFnfOMW8z3Ifeq8DN2qh

## 使用accessToken获取用户ID（用户名）
https://cas.zuimeihui.com:88/cas/oauth2.0/profile?access_token=AT-1-rx24ZXDGXMXMcKa3-epIiCZDk7DJkIsQ

## 运行
本demo可运行，参照下面官方文档。

## 端口
服务端口默认：8443
服务反向代理端口：88

## 免责声明
本demo开源，共学习、交流、初始化项目使用，若使用后造成各种损失，与本demo和开发者无关。

## 附录

### 用户表密码生成
public static String encode(String userPassword, String userPasswordSalt) {
	ConfigurableHashService hashService = new DefaultHashService();
    hashService.setPrivateSalt(ByteSource.Util.bytes("ZuiMeiHui.com"));
    hashService.setHashAlgorithmName("MD5");
    hashService.setHashIterations(1);
    HashRequest request = new HashRequest.Builder()
            .setSalt(userPasswordSalt)
            .setSource(userPassword)
            .build();
    return hashService.computeHash(request).toHex();
}

### 用户表结构
CREATE TABLE `user_info` (
  `id` bigint unsigned NOT NULL AUTO_INCREMENT COMMENT '用户ID，主键',
  `time_create` datetime DEFAULT NULL COMMENT '创建时间',
  `time_update` datetime DEFAULT NULL COMMENT '更新时间',
  `admin_id` varchar(200) DEFAULT NULL COMMENT '操作人ID',
  `admin_name` varchar(200) DEFAULT NULL COMMENT '操作人登录名',
  `user_name` varchar(200) DEFAULT NULL COMMENT '登陆名称',
  `user_password` varchar(64) DEFAULT NULL COMMENT '登陆密码',
  `user_password_salt` varchar(8) DEFAULT NULL COMMENT '用户密码加盐',
  `user_nickname` varchar(200) DEFAULT NULL COMMENT '用户昵称',
  `user_telphone` varchar(100) DEFAULT NULL COMMENT '用户电话',
  `user_email` varchar(100) DEFAULT NULL COMMENT '用户邮箱',
  `user_remark` varchar(500) DEFAULT NULL COMMENT '用户备注',
  `user_freeze` tinyint DEFAULT NULL COMMENT '是否冻结，true，false',
  `user_del` tinyint DEFAULT NULL COMMENT '是否删除，true，false',
  PRIMARY KEY (`id`),
  UNIQUE KEY `system_user_UN` (`user_name`)
) ENGINE=InnoDB AUTO_INCREMENT=2 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci;

# Versions

```xml
<cas.version>5.3.x</cas.version>
```

# Requirements

* JDK 1.8+

# Configuration

The `etc` directory contains the configuration files and directories that need to be copied to `/etc/cas/config`.

# Build

To see what commands are available to the build script, run:

```bash
./build.sh help
```

To package the final web application, run:

```bash
./build.sh package
```

To update `SNAPSHOT` versions run:

```bash
./build.sh package -U
```

# Deployment

- Create a keystore file `thekeystore` under `/etc/cas`. Use the password `changeit` for both the keystore and the key/certificate entries.
- Ensure the keystore is loaded up with keys and certificates of the server.

On a successful deployment via the following methods, CAS will be available at:

* `http://cas.server.name:8080/cas`
* `https://cas.server.name:8443/cas`

## Executable WAR

Run the CAS web application as an executable WAR.

```bash
./build.sh run
```

## Spring Boot

Run the CAS web application as an executable WAR via Spring Boot. This is most useful during development and testing.

```bash
./build.sh bootrun
```

### Warning!

Be careful with this method of deployment. `bootRun` is not designed to work with already executable WAR artifacts such that CAS server web application. YMMV. Today, uses of this mode ONLY work when there is **NO OTHER** dependency added to the build script and the `cas-server-webapp` is the only present module. See [this issue](https://github.com/spring-projects/spring-boot/issues/8320) for more info.


## Spring Boot App Server Selection

There is an app.server property in the `pom.xml` that can be used to select a spring boot application server.
It defaults to `-tomcat` but `-jetty` and `-undertow` are supported.

It can also be set to an empty value (nothing) if you want to deploy CAS to an external application server of your choice.

```xml
<app.server>-tomcat<app.server>
```

## Windows Build

If you are building on windows, try `build.cmd` instead of `build.sh`. Arguments are similar but for usage, run:

```
build.cmd help
```

## External

Deploy resultant `target/cas.war`  to a servlet container of choice.


## Command Line Shell

Invokes the CAS Command Line Shell. For a list of commands either use no arguments or use `-h`. To enter the interactive shell use `-sh`.

```bash
./build.sh cli


```