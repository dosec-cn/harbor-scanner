# Harbor-Scanner

一个免费的镜像漏洞扫描工具, 可以扫描镜像中已安装软件包的漏洞，支持中文漏洞库，可与 [Harbor](https://github.com/goharbor/harbor) 无缝集成。

> DoSec 的商业客户可以使用企业版扫描功能，例如扫描开源框架中的漏洞以及扫描容器镜像中包含的敏感数据。同时企业版增加了WEB UI, 提供仪表板，资产管理，用户管理，镜像漏洞修复建议, 安全和策略评估报告，容器运行时防护，Docker 和 Kubernetes 合规检查以及其他功能和模块。

# 特点

* 漏洞扫描快速准确，支持CVE和CNNVD双漏洞编号，漏洞中文描述信息
* 自动更新漏洞库(在配置中开启自动更新参数)
* 快速部署使用，最新的发布版中已包含最近日期之前的全量漏洞库，安装完成即可立即扫描出结果

## 安装

推荐将 Harbor-Scanner 和 Harbor 镜像仓库部署在同一台服务器。

1. 下载 Harbor-Scanner 的离线安装包并解压

    ```shell
    wget https://github.com/dosec-cn/harbor-scanner/releases/download/v1.0/dosec-scanner.tgz
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

    ![扫描工具配置](http://img.dosec.cn/20191028154900.png)

    ① 填写扫描工具的名称

    ② 填写扫描工具的 IP 和端口

    ③ 测试 Harbor 和扫描工具是否能正常连接

    ④ 只有测试连接成功后才能添加

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

## DoSec 安全产品对比

|        功能        |   Harbor-Scanner   | [镜界容器安全防护平台](https://www.dosec.cn/) |
| ------------------ | :----------------: | :-------------------------------------------: |
| 许可               |        免费        |                    企业版                     |
| 支持与 Harbor 集成 | :heavy_check_mark: |               :heavy_check_mark:              |
| 系统软件包漏洞扫描 | :heavy_check_mark: |               :heavy_check_mark:              |
| 开源组件漏洞扫描   |                    |               :heavy_check_mark:              |
| 恶意软件扫描       |                    |               :heavy_check_mark:              |
| 敏感数据扫描       |                    |               :heavy_check_mark:              |
| 镜像配置检查       |                    |               :heavy_check_mark:              |
| 镜像历史行为分析   |                    |               :heavy_check_mark:              |
| 阻止非信任镜像运行 |                    |               :heavy_check_mark:              |
| 运行时保护         |                    |               :heavy_check_mark:              |
| 合规检查           |                    |               :heavy_check_mark:              |

## 社区交流

**微信群**: Harbor-Scanner 交流群, 扫描社区成员微信二维码, 添加时请备注姓名-公司/学校/组织/机构等。

![二维码](http://img.dosec.cn/2019_10_28_1838167633.png)
