---
title: 从守护程序应用调用 Web API - Microsoft 标识平台 | Azure
description: 了解如何生成可调用 web API 的守护程序应用。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 0dfd729a48b7e81028078fd035b3b900f4d7b9bc
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2020
ms.locfileid: "94444107"
---
# <a name="daemon-app-that-calls-web-apis---call-a-web-api-from-the-app"></a>调用 Web API 的守护程序应用 - 从应用调用 Web API

.NET 守护程序应用可以调用一个 Web API。 .NET 守护程序应用还可以调用多个预先批准的 Web API。

## <a name="calling-a-web-api-from-a-daemon-application"></a>从守护程序应用程序调用一个 Web API

下面介绍如何使用令牌来调用一个 API：

# <a name="net"></a>[.NET](#tab/dotnet)

[!INCLUDE [Call web API in .NET](../../../includes/active-directory-develop-scenarios-call-apis-dotnet.md)]

# <a name="python"></a>[Python](#tab/python)

```Python
endpoint = "url to the API"
http_headers = {'Authorization': 'Bearer ' + result['access_token'],
                'Accept': 'application/json',
                'Content-Type': 'application/json'}
data = requests.get(endpoint, headers=http_headers, stream=False).json()
```

# <a name="java"></a>[Java](#tab/java)

```Java
HttpURLConnection conn = (HttpURLConnection) url.openConnection();

// Set the appropriate header fields in the request header.
conn.setRequestProperty("Authorization", "Bearer " + accessToken);
conn.setRequestProperty("Accept", "application/json");

String response = HttpClientHelper.getResponseStringFromConn(conn);

int responseCode = conn.getResponseCode();
if(responseCode != HttpURLConnection.HTTP_OK) {
    throw new IOException(response);
}

JSONObject responseObject = HttpClientHelper.processResponse(responseCode, response);
```

---

## <a name="calling-several-apis"></a>调用多个 API

对于守护程序应用，需要预先批准调用的 Web API。 守护程序应用没有增量同意。 （没有用户交互。）租户管理员需要预先为应用程序提供同意和所有 API 权限。 如果要调用多个 API，则每次调用 `AcquireTokenForClient` 时都需要为每个资源获取一个令牌。 MSAL 将使用应用程序令牌缓存来避免不必要的服务调用。

## <a name="next-steps"></a>后续步骤

# <a name="net"></a>[.NET](#tab/dotnet)

转到本方案中的下一篇文章， [转到 "生产](./scenario-daemon-production.md?tabs=dotnet)"。

# <a name="python"></a>[Python](#tab/python)

转到本方案中的下一篇文章， [转到 "生产](./scenario-daemon-production.md?tabs=python)"。

# <a name="java"></a>[Java](#tab/java)

转到本方案中的下一篇文章， [转到 "生产](./scenario-daemon-production.md?tabs=java)"。

---
