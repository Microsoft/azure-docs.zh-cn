---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/11/2020
ms.author: mikben
ms.openlocfilehash: 90bc9def9c1a78799f4931b58ad0a661602985e3
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2021
ms.locfileid: "98013186"
---
## <a name="setting-up"></a>设置

### <a name="create-a-new-nodejs-application"></a>创建新的 Node.js 应用程序

打开终端或命令窗口，为应用创建一个新目录，并导航到该目录。

```console
mkdir calling-quickstart && cd calling-quickstart
```

运行 `npm init -y` 以使用默认设置创建 package.json 文件。

```console
npm init -y
```

### <a name="install-the-package"></a>安装包

使用 `npm install` 命令安装适用于 JavaScript 的 Azure 通信服务呼叫客户端库。

```console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save
```

此快速入门建议使用以下版本的 webpack：

```console
"webpack": "^4.42.0",
"webpack-cli": "^3.3.11",
"webpack-dev-server": "^3.10.3"
```

`--save` 选项将该库作为 package.json 文件中的依赖项列出。

### <a name="set-up-the-app-framework"></a>设置应用框架

此快速入门使用 webpack 捆绑应用程序资产。 运行以下命令以安装 webpack、 webpack-cli 和 webpack-dev-server npm 包，并将它们作为 package.json 中的开发依赖项列出：

```console
npm install webpack@4.42.0 webpack-cli@3.3.11 webpack-dev-server@3.10.3 --save-dev
```

在项目的根目录中创建一个 index.html 文件。 我们将使用此文件来配置基本布局，该布局允许用户拨打电话。
