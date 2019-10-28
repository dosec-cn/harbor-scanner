# Harbor-Scanner

一个免费的镜像漏洞扫描工具, 可以扫描镜像中已安装软件包的漏洞，支持中文漏洞库，可与 [Harbor](https://github.com/goharbor/harbor) 无缝集成。

> DoSec 的商业客户可以使用其他企业版扫描功能，例如扫描开源框架中的漏洞以及扫描容器镜像中包含的敏感数据。同时企业版增加了WEB UI, 提供仪表板，资产管理，用户管理，镜像漏洞修复建议, 安全和策略评估报告，容器运行时防护，Docker 和 Kubernetes 合规检查以及其他功能和模块。

## 安装

推荐将 Harbor-Scanner 和 Harbor 镜像仓库部署在同一台服务器。

1. 下载 Harbor-Scanner 的离线安装包并解压

    ```shell
    wget dosec-scanner.tgz
    # 解压
    tar xf dosec-scanner.tgz
    # 进入项目
    cd dosec-scanner
    ```

2. 运行 Install 脚本

    > 需要提前安装 docker-compose

    ```shell
    ./Install.sh
    ```

3. 配置 Harbor 仓库

    登录 Harbor 管理界面 -> 配置管理 -> Scanners -> NEW SCANNER

    ![新建扫描工具](http://img.dosec.cn/20191028115555.png)

    填写扫描器配置 -> 点击 ADD 确认添加

    ![扫描工具配置](http://img.dosec.cn/20191028151114.png)

## 自定义配置

可根据需要更改 `docker-compose.yaml` 文件

```yaml
version: '2.2'

services:
  dosec-db-hb:
    image: hub.dosec.cn/library/dosec-db-hb:2019-10-24T18.39.33
    restart: always

  dosec-scannerapp:
    depends_on:
      - dosec-db-hb
    image: hub.dosec.cn/library/dosec-scannerapp:2019-10-24T19.17.35V1.0.0_prod
    # 默认映射了主机的 8899 端口
    ports:
      - "8899:8899"
    restart: always
    # 默认将程序日志映射到了主机的 /var/log/dosec-scanner 目录
    volumes:
      - /var/log/dosec-scanner:/dosec/log

  dosec-scanner-hb:
    depends_on:
      - dosec-db-hb
      - dosec-scannerapp
    image: hub.dosec.cn/library/dosec-scanner-hb:2019-10-25T11.58.04V1.0_release
    # command: ["-update_cve"]
    # 默认注释了在线更新 CVE 功能, 会消耗大量 CPU 和内存
    restart: always
    # 默认将程序日志映射到了主机的 /var/log/dosec-scanner 目录
    volumes:
      - /var/log/dosec-scanner:/dosec/log
```

## 卸载

进入 Harbor-Scanner 项目目录, 执行以下命令即可完全卸载

```shell
docker-compose down
```

## 支持扫描的操作系统

- Debian >= 7, unstable
- Ubuntu LTS releases >= 12.04
- Red Hat Enterprise Linux >= 5
- CentOS >= 5
- Alpine >= 3.3
- Oracle Linux >= 5

## DoSec 容器安全产品对比

|        功能        | Harbor-Scanner | [镜界容器安全防护平台](https://www.dosec.cn/) |
| ------------------ | :------------: | :-------------------------------------------: |
| 定价模式           |      免费      |                    企业版                     |
| 支持与 Harbor 集成 |       X        |                       X                       |
| 系统软件包漏洞扫描 |       X        |                       X                       |
| 开源组件漏洞扫描   |                |                       X                       |
| 恶意软件扫描       |                |                       X                       |
| 敏感数据扫描       |                |                       X                       |
| 镜像配置检查       |                |                       X                       |
| 镜像历史行为分析   |                |                       X                       |
| 阻止非信任镜像运行 |                |                       X                       |
| 运行时保护         |                |                       X                       |
| 合规检查           |                |                       X                       |
