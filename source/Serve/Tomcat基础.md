---
title: Tomcat基础
date: 2025-07-17
cssclasses:
  - Serve
  - Tomcat
---
## 目录和文件

这些是 tomcat 的一些关键目录：
* **/bin** - 启动、关闭和其他脚本。 `*.sh` 文件（适用于 Unix 系统）是 `*.bat` 文件（适用于 Windows 系统）的功能性副本。由于 Win32 命令行缺少某些功能，这里有一些额外的文件。
* **/conf** - 配置文件和相关 DTD 文件。这里最重要的文件是 server.xml。它是容器的主配置文件。
* **/logs** - 默认情况下，日志文件存放在这里。
* **/webapps** - 这里是放置你的 web 应用的目录。

## CATALINA_HOME 和 CATALINA_BASE

在文档中，提到了以下两个关键环境变量：

* **CATALINA_HOME**: 表示你的 Tomcat 安装的根目录，例如 `/home/tomcat/apache-tomcat-11.0.0`。
* **CATALINA_BASE**：表示特定 Tomcat 实例的运行时配置的根目录。如果你想在同一台机器上运行多个 Tomcat 实例，请使用 `CATALINA_BASE` 属性。

当为这两个关键环境变量设置不同路径时，`CATALINA_HOME`存放的是**静态资源**，比如.jar 文件（Java 归档文件）或二进制文件，这些是 Tomcat 运行的基础程序文件，通常不会频繁变动。
而`CATALINA_BASE`则存放**动态或运行时相关的内容**，包括配置文件、日志文件、已部署的应用程序以及其他运行时所需的资源，这些内容会随着 Tomcat 的使用（如配置修改、应用部署、日志生成）而变化。

### 为何使用 CATALINA_BASE

默认情况下，CATALINA_HOME 和 CATALINA_BASE 指向同一个目录。当你需要在同一台机器上运行多个 Tomcat 实例时，手动设置 CATALINA_BASE。这样做有以下好处：

* 升级到 Tomcat 新版本的更便捷管理。由于所有具有单一 CATALINA_HOME 位置的实例共享同一套 `.jar` 文件和二进制文件，你可以轻松地将文件升级到新版本，并通过使用相同 CATALIA_HOME 目录将变更传播到所有 Tomcat 实例。
* 避免重复相同的静态 `.jar` 文件。
* 共享某些设置的可能性，例如 `setenv` shell 或 bat 脚本文件（取决于你的操作系统）。

### CATALINA_BASE 的内容

在使用 CATALINA_BASE 之前，首先考虑并创建 CATALINA_BASE 所使用的目录树。请注意，如果你没有创建所有推荐的目录，Tomcat 会自动创建这些目录。如果它无法创建必要的目录，例如由于权限问题，Tomcat 可能会无法启动，或者可能无法正常运行。

请考虑以下目录列表：
* 包含 `bin` 、 `setenv.sh` 、 `setenv.bat` 和 `tomcat-juli.jar` 文件的目录。