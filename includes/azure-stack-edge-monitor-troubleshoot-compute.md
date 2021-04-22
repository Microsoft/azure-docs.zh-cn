---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 08/30/2020
ms.author: alkohli
ms.openlocfilehash: 92ccb6127e624ace9e719ffd23324b3a1b971f72
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "89272180"
---
在配置了计算角色的 Azure Stack Edge 设备上，可以使用两个不同的命令集对设备进行故障排除或监视。

- 使用 `iotedge` 命令。 这些命令适用于针对设备的基本操作。
- 使用 `dkrdbe` 命令。 这些命令适用于针对设备的更广泛操作。

若要执行上述任一命令集，需要[连接到 PowerShell 界面](#connect-to-the-powershell-interface)。

### <a name="use-iotedge-commands"></a>使用 `iotedge` 命令

若要查看可用命令的列表，请[连接到 PowerShell 界面](#connect-to-the-powershell-interface)并使用 `iotedge` 函数。

```powershell
[10.100.10.10]: PS>iotedge -?                                                                                                                                                                                                 Usage: iotedge COMMAND

Commands:
   check
   list
   logs
   restart

[10.100.10.10]: PS>
```

下表简要说明了适用于 `iotedge` 的命令：

|command  |说明 |
|---------|---------|
|`check`     | 对常见配置和连接问题执行自动检查       |
|`list`     | 列出模块         |
|`logs`     | 提取模块的日志        |
|`restart`     | 停止和重启模块         |


### <a name="use-dkrdbe-commands"></a>使用 `dkrdbe` 命令

若要查看可用命令的列表，请[连接到 PowerShell 界面](#connect-to-the-powershell-interface)并使用 `dkrdbe` 函数。

```powershell
[10.100.10.10]: PS>dkrdbe -?
Usage: dkrdbe COMMAND

Commands:
   image [prune]
   images
   inspect
   login
   logout
   logs
   port
   ps
   pull
   start
   stats
   stop
   system [df]
   top

[10.100.10.10]: PS>
```
下表简要说明了适用于 `dkrdbe` 的命令：

|command  |说明 |
|---------|---------|
|`image`     | 管理映像。 若要删除未使用的映像，请使用 `dkrdbe image prune -a -f`       |
|`images`     | 列出映像         |
|`inspect`     | 返回 Docker 对象的详细信息         |
|`login`     | 登录到 Docker 注册表         |
|`logout`     | 从 Docker 注册表注销         |
|`logs`     | 提取容器的日志        |
|`port`     | 列出容器的端口映射或特定映射        |
|`ps`     | 列出容器        |
|`pull`     | 从注册表中拉取映像或存储库         |
|`start`     | 启动一个或多个已停止的容器         |
|`stats`     | 显示容器资源使用情况统计信息的实时传送流         |
|`stop`     | 停止一个或多个正在运行的容器        |
|`system`     | 管理 Docker         |
|`top`     | 显示正在运行的容器进程         |

若要获取任何可用命令的帮助，请使用 `dkrdbe <command-name> --help`。

例如，若要了解 `port` 命令的用法，请键入：

```powershell
[10.100.10.10]: P> dkrdbe port --help

Usage:  dkr port CONTAINER [PRIVATE_PORT[/PROTO]]

List port mappings or a specific mapping for the container
[10.100.10.10]: P> dkrdbe login --help

Usage:  docker login [OPTIONS] [SERVER]

Log in to a Docker registry.
If no server is specified, the default is defined by the daemon.

Options:
  -p, --password string   Password
      --password-stdin    Take the password from stdin
  -u, --username string   Username
[10.100.10.10]: PS>
```

`dkrdbe` 函数的可用命令使用的参数与普通 docker 命令所使用的参数相同。 对于 docker 命令使用的选项和参数，请参阅[使用 Docker 命令行](https://docs.docker.com/engine/reference/commandline/docker/)。

### <a name="to-check-if-the-module-deployed-successfully"></a>检查模块是否已成功部署

计算模块是已实现业务逻辑的容器。 若要检查计算模块是否已成功部署，请运行 `ps` 命令，并检查容器（对应于计算模块的容器）是否正在运行。

若要获取所有容器（包括暂停的容器）的列表，请运行 `ps -a` 命令。

```powershell
[10.100.10.10]: P> dkrdbe ps -a
CONTAINER ID        IMAGE                                                COMMAND                   CREATED             STATUS              PORTS                                                                  NAMES
d99e2f91d9a8        edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64   "dotnet FileMoveModuâ€¦"    2 days ago          Up 2 days                                                                                  movefile
0a06f6d605e9        edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64   "dotnet FileMoveModuâ€¦"    2 days ago          Up 2 days                                                                                  filemove
2f8a36e629db        mcr.microsoft.com/azureiotedge-hub:1.0               "/bin/sh -c 'echo \"$â€¦"   2 days ago          Up 2 days           0.0.0.0:443->443/tcp, 0.0.0.0:5671->5671/tcp, 0.0.0.0:8883->8883/tcp   edgeHub
acce59f70d60        mcr.microsoft.com/azureiotedge-agent:1.0             "/bin/sh -c 'echo \"$â€¦"   2 days ago          Up 2 days                                                                                  edgeAgent
[10.100.10.10]: PS>
```

如果在创建容器映像或在拉取映像时出现错误，请运行 `logs edgeAgent`。  `EdgeAgent` 是负责预配其他容器的 IoT Edge 运行时容器。

由于 `logs edgeAgent` 转储所有日志，因此查看最近发生的错误的适当方法是使用 `--tail 20` 选项。


```powershell
[10.100.10.10]: PS>dkrdbe logs edgeAgent --tail 20
2019-02-28 23:38:23.464 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Util.Uds.HttpUdsMessageHandler] - Connected socket /var/run/iotedge/mgmt.sock
2019-02-28 23:38:23.464 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Util.Uds.HttpUdsMessageHandler] - Sending request http://mgmt.sock/modules?api-version=2018-06-28
2019-02-28 23:38:23.464 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Agent.Core.Agent] - Getting edge agent config...
2019-02-28 23:38:23.464 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Agent.Core.Agent] - Obtained edge agent config
2019-02-28 23:38:23.469 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Agent.Edgelet.ModuleManagementHttpClient] - Received a valid Http response from unix:///var/run/iotedge/mgmt.soc
k for List modules
--------------------CUT---------------------
--------------------CUT---------------------
08:28.1007774+00:00","restartCount":0,"lastRestartTimeUtc":"2019-02-26T20:08:28.1007774+00:00","runtimeStatus":"running","version":"1.0","status":"running","restartPolicy":"always
","type":"docker","settings":{"image":"edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64","imageHash":"sha256:47778be0602fb077d7bc2aaae9b0760fbfc7c058bf4df192f207ad6cbb96f7cc","c
reateOptions":"{\"HostConfig\":{\"Binds\":[\"/home/hcsshares/share4-dl460:/home/input\",\"/home/hcsshares/share4-iot:/home/output\"]}}"},"env":{}}
2019-02-28 23:38:28.480 +00:00 [DBG] [Microsoft.Azure.Devices.Edge.Agent.Core.Planners.HealthRestartPlanner] - HealthRestartPlanner created Plan, with 0 command(s).
```

### <a name="to-get-container-logs"></a>获取容器日志

若要获取特定容器的日志，请首先列出该容器，然后获取你感兴趣的容器的日志。

1. [连接到 PowerShell 接口](#connect-to-the-powershell-interface)。
2. 若要获取正在运行的容器的列表，请运行 `ps` 命令。

    ```powershell
    [10.100.10.10]: P> dkrdbe ps
    CONTAINER ID        IMAGE                                                COMMAND                   CREATED             STATUS              PORTS                                                                  NAMES
    d99e2f91d9a8        edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64   "dotnet FileMoveModuâ€¦"    2 days ago          Up 2 days                                                                                  movefile
    0a06f6d605e9        edgecompute.azurecr.io/filemovemodule2:0.0.1-amd64   "dotnet FileMoveModuâ€¦"    2 days ago          Up 2 days                                                                                  filemove
    2f8a36e629db        mcr.microsoft.com/azureiotedge-hub:1.0               "/bin/sh -c 'echo \"$â€¦"   2 days ago          Up 2 days           0.0.0.0:443->443/tcp, 0.0.0.0:5671->5671/tcp, 0.0.0.0:8883->8883/tcp   edgeHub
    acce59f70d60        mcr.microsoft.com/azureiotedge-agent:1.0             "/bin/sh -c 'echo \"$â€¦"   2 days ago          Up 2 days                                                                                  edgeAgent
    ```

3. 记下需要其日志的容器的容器 ID。

4. 若要获取特定容器的日志，请运行提供容器 ID 的 `logs` 命令。

    ```powershell
    [10.100.10.10]: PS>dkrdbe logs d99e2f91d9a8
    02/26/2019 18:21:45: Info: Opening module client connection.
    02/26/2019 18:21:46: Info: Initializing with input: /home/input, output: /home/output.
    02/26/2019 18:21:46: Info: IoT Hub module client initialized.
    02/26/2019 18:22:24: Info: Received message: 1, SequenceNumber: 0 CorrelationId: , MessageId: 081886a07e694c4c8f245a80b96a252a Body: [{"ChangeType":"Created","ShareRelativeFilePath":"\\__Microsoft Data Box Edge__\\Upload\\Errors.xml","ShareName":"share4-dl460"}]
    02/26/2019 18:22:24: Info: Moving input file: /home/input/__Microsoft Data Box Edge__/Upload/Errors.xml to /home/output/__Microsoft Data Box Edge__/Upload/Errors.xml
    02/26/2019 18:22:24: Info: Processed event.
    02/26/2019 18:23:38: Info: Received message: 2, SequenceNumber: 0 CorrelationId: , MessageId: 30714d005eb048e7a4e7e3c22048cf20 Body: [{"ChangeType":"Created","ShareRelativeFilePath":"\\f [10]","ShareName":"share4-dl460"}]
    02/26/2019 18:23:38: Info: Moving input file: /home/input/f [10] to /home/output/f [10]
    02/26/2019 18:23:38: Info: Processed event.
    ```

### <a name="to-monitor-the-usage-statistics-of-the-device"></a>监视设备的使用情况统计信息

若要监视设备上的内存、CPU 使用率和 IO，请使用 `stats` 命令。

1. [连接到 PowerShell 接口](#connect-to-the-powershell-interface)。
2. 运行 `stats` 命令，以禁用实时传送流并仅拉取第一个结果。

   ```powershell
   dkrdbe stats --no-stream
   ```

   以下示例显示了此 cmdlet 的用法：

    ```
    [10.100.10.10]: P> dkrdbe stats --no-stream
    CONTAINER ID        NAME          CPU %         MEM USAGE / LIMIT     MEM %         NET I/O             BLOCK I/O           PIDS
    d99e2f91d9a8        movefile      0.0           24.4MiB / 62.89GiB    0.04%         751kB / 497kB       299kB / 0B          14
    0a06f6d605e9        filemove      0.00%         24.11MiB / 62.89GiB   0.04%         679kB / 481kB       49.5MB / 0B         14
    2f8a36e629db        edgeHub       0.18%         173.8MiB / 62.89GiB   0.27%         4.58MB / 5.49MB     25.7MB / 2.19MB     241
    acce59f70d60        edgeAgent     0.00%         35.55MiB / 62.89GiB   0.06%         2.23MB / 2.31MB     55.7MB / 332kB      14
    [10.100.10.10]: PS>
    ```

