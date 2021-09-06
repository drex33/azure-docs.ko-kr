---
title: 방화벽 또는 프록시 서버에서 Azure Portal URL 허용
description: 네트워크와 Azure Portal 및 해당 서비스 간의 연결을 최적화하려면 이러한 URL을 허용 목록에 추가하는 것이 좋습니다.
ms.date: 06/21/2021
ms.topic: conceptual
ms.openlocfilehash: 70e2f55a381c38b0a4244f742b7b2c51e6ca81f6
ms.sourcegitcommit: 096e7972e2a1144348f8d648f7ae66154f0d4b39
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2021
ms.locfileid: "112516364"
---
# <a name="allow-the-azure-portal-urls-on-your-firewall-or-proxy-server"></a>방화벽 또는 프록시 서버에서 Azure Portal URL 허용

네트워크와 Azure Portal 및 해당 서비스 간의 연결을 최적화하려면 특정 Azure Portal URL을 허용 목록에 추가하는 것이 좋습니다. 이렇게 하면 로컬 또는 광역 네트워크와 Azure 클라우드 간의 성능 및 연결을 향상시킬 수 있습니다.

네트워크 관리자는 프록시 서버, 방화벽 또는 기타 디바이스를 배포하여 사용자가 인터넷에 액세스하는 방식을 보호하고 제어할 수 있습니다. 사용자를 보호하도록 설계된 규칙은 경우에 따라 합법적인 비즈니스 관련 인터넷 트래픽을 차단하거나 느리게 만들 수 있습니다. 이 트래픽에는 여기에 나열된 URL을 통한 사용자와 Azure 간의 통신이 포함됩니다.

> [!TIP]
> 이러한 도메인에 대한 네트워크 연결 문제를 진단하는 데 도움이 필요하면 https://portal.azure.com/selfhelp 를 확인하세요.

## <a name="azure-portal-urls-for-proxy-bypass"></a>프록시 무시에 대한 Azure Portal URL

Azure Portal에 허용되는 URL 엔드포인트는 조직이 배포되는 Azure 클라우드와 관련이 있습니다. 이러한 엔드포인트에 대한 네트워크 트래픽이 제한을 무시하도록 허용하려면 클라우드를 선택한 다음, URL 목록을 프록시 서버 또는 방화벽에 추가합니다. 다른 Microsoft 제품 및 서비스와 관련된 URL을 추가하려는 경우에도 여기에 나열된 것과 다른 포털 관련 URL은 추가하지 않는 것이 좋습니다.

#### <a name="public-cloud"></a>[퍼블릭 클라우드](#tab/public-cloud)

```
*.aadcdn.microsoftonline-p.com
*.aka.ms
*.applicationinsights.io
*.azure.com
*.azure.net
*.azure-api.net
*.azuredatalakestore.net
*.azureedge.net
*.loganalytics.io
*.microsoft.com
*.microsoftonline.com
*.microsoftonline-p.com
*.msauth.net
*.msftauth.net
*.trafficmanager.net
*.visualstudio.com
*.windows.net
*.windows-int.net
```

#### <a name="us-government-cloud"></a>[미국 정부 클라우드](#tab/us-government-cloud)

```
*.applicationinsights.us
*.azure.us
*.loganalytics.us
*.microsoft.us
*.microsoftonline.us
*.msauth.net
*.usgovcloudapi.net
*.usgovtrafficmanager.net
*.windowsazure.us
```

#### <a name="china-government-cloud"></a>[중국 정부 클라우드](#tab/china-government-cloud)

```
*.azure.cn
*.microsoft.cn
*.microsoftonline.cn
*.chinacloudapi.cn
*.trafficmanager.cn
*.chinacloudsites.cn
*.windowsazure.cn
```
---

> [!NOTE]
> 이러한 엔드포인트에 대한 트래픽은 HTTP에 대한 표준 TCP 포트(80) 및 HTTPS에 표준 TCP 포트(443)를 사용합니다.
