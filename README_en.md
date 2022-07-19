# Harbor-Scanner

A free image vulnerability scanner, implement [Harbor's](https://github.com/goharbor/harbor) pluggable scanner adapter.

## Features

* Accurate vulnerability scan result, support CVE and CNNVD vulnerability ID, Chinese vulnerability description
* CVE database auto update(config it in docker compose's yaml file)
* Avaliable immediately after deploy, without waiting for updating database, the offline package already include newest CVE database

## Install

1. Download Harbor-Scanner offline install package 

    ```shell
    wget https://github.com/dosec-cn/harbor-scanner/releases/download/v1.3/dosec-scanner.tgz
    # decompress
    tar zxf dosec-scanner.tgz
    # change work directory
    cd dosec-scanner
    ```

2. Run Install Shell

    > Requirement:docker-compose need to be installed

    ```shell
    ./Install.sh
    ```

3. Config Harbor

    Login Harbor UI -> Interrogation Services -> Scanners -> NEW SCANNER

    ![new scanner](http://img.dosec.cn/20191223112746.png)

    fill in the configuration -> click ADD to finish

    ![scanner config](http://img.dosec.cn/20200112194526.png)

    ① scanner name

    ② scanner service's IP and port

    ③ test scanner connection

    ④ the scanner can only be added after ping test success

## Custom Configuration

modify `docker-compose.yaml` if need

```yaml
version: '2.2'

services:
  dosec-db-hb:
    image: hub.dosec.cn/library/dosec-db:2022-07-07T16.56.50V2.0-20220706
    restart: always

  dosec-scannerapp:
    depends_on:
      - dosec-db-hb
    image: hub.dosec.cn/library/dosec-scannerapp:2022-07-19T13.14.25V1.0.1_prod
    # map port to host's 8899
    ports:
      - "8899:8899"
    restart: always
    # map log directory to host's /var/log/dosec-scanner
    volumes:
      - /var/log/dosec-scanner:/dosec/log

  dosec-scanner-hb:
    depends_on:
      - dosec-db-hb
      - dosec-scannerapp
    image: hub.dosec.cn/library/dosec-scanner-hb:2022-07-19T13.04.06V1.3_release
    # command: ["-update_cve"]
    # uncomment this command if you need auto updating cve database
    restart: always
    # map log directory to host's /var/log/dosec-scanner
    volumes:
      - /var/log/dosec-scanner:/dosec/log
```

## Uninstall

cd Harbor-Scanner's project directory, execute command below

```shell
docker-compose down
```

## Image OS Support

- Debian >= 7, unstable
- Ubuntu LTS releases >= 12.04
- Red Hat Enterprise Linux >= 5
- CentOS >= 5
- Alpine >= 3.3
- Oracle Linux >= 5

## Dosec Production Compare

|               Function              |   Harbor-Scanner   | [Dosec Container Security Platform](https://www.dosec.cn/) |
| ----------------------------------- | :----------------: | :--------------------------------------------------------: |
| Edition                             |        Free        |                     Enterprise Edition                     |
| Integration with Harbor             | :heavy_check_mark: |                     :heavy_check_mark:                     |
| OS package vulnerability            | :heavy_check_mark: |                     :heavy_check_mark:                     |
| Open source component vulnerability |                    |                     :heavy_check_mark:                     |
| Malware Detection                   |                    |                     :heavy_check_mark:                     |
| Sensitive Files Detection           |                    |                     :heavy_check_mark:                     |
| Image Configuration Analysis        |                    |                     :heavy_check_mark:                     |
| Docker File Analysis                |                    |                     :heavy_check_mark:                     |
| Runtime Protection                  |                    |                     :heavy_check_mark:                     |
| Benchmark Check                     |                    |                     :heavy_check_mark:                     |

## Community 

Wechat Group:Scan QR Code below to add community member and get invitation to join community group, please comment Name-Company/Organization/Others information when you add.

![QR Code](http://img.dosec.cn/2019_10_28_1838167633.png)
