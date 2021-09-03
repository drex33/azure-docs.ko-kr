---
title: Azure Functions의 IP 주소
description: 함수 앱의 인바운드 및 아웃바운드 IP 주소를 찾는 방법과 변경되는 원인을 알아봅니다.
ms.topic: conceptual
ms.date: 12/03/2018
ms.openlocfilehash: a884edd23fa1538fcc2b00c80190eab6699e1e47
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/21/2021
ms.locfileid: "112414488"
---
# <a name="ip-addresses-in-azure-functions"></a>Azure Functions의 IP 주소

이 문서에서는 함수 앱의 IP 주소와 관련된 다음 개념을 설명합니다.

* 함수 앱에서 현재 사용 중인 IP 주소를 찾습니다.
* 함수 앱 IP 주소가 변경되는 조건입니다.
* 함수 앱에 액세스할 수 있는 IP 주소를 제한합니다.
* 함수 앱의 전용 IP 주소를 정의하는 중입니다.

IP 주소는 개별 함수가 아니라 함수 앱과 연결됩니다. 들어오는 HTTP 요청은 인바운드 IP 주소를 사용하여 개별 함수를 호출할 수 없습니다. 기본 도메인 이름(functionappname.azurewebsites.net) 또는 사용자 지정 도메인 이름을 사용해야 합니다.

## <a name="function-app-inbound-ip-address"></a>함수 앱 인바운드 IP 주소

각 함수 앱에는 하나의 인바운드 IP 주소가 있습니다. 해당 IP 주소를 찾으려면 다음을 수행합니다.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 함수 앱으로 이동합니다.
3. **설정** 아래에서 **속성** 을 선택합니다. 인바운드 IP 주소는 **가상 IP 주소** 아래에 나타납니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

로컬 클라이언트 컴퓨터에서 `nslookup` 유틸리티를 사용합니다.

```command
nslookup <APP_NAME>.azurewebsites.net
```

---

## <a name="function-app-outbound-ip-addresses"></a><a name="find-outbound-ip-addresses"></a>함수 앱 아웃바운드 IP 주소

각 함수 앱에는 사용 가능한 아웃바운드 IP 주소 집합이 있습니다. 백 엔드 데이터베이스와 같이 함수의 아웃바운드 연결은 사용 가능한 아웃바운드 IP 주소 중 하나를 원본 IP 주소로 사용합니다. 지정된 연결에서 사용할 IP 주소는 미리 알 수 없습니다. 따라서 백 엔드 서비스에서 모든 함수 앱의 아웃바운드 IP 주소에 대해 방화벽을 열어야 합니다.

함수 앱에서 사용 가능한 아웃바운드 IP 주소를 찾으려면 다음을 수행합니다.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

1. [Azure Resource Explorer](https://resources.azure.com)에 로그인합니다.
2. **구독 > {사용자 구독} > 공급자 > Microsoft.Web > 사이트** 를 차례로 선택합니다.
3. JSON 패널에서 함수 앱의 이름으로 끝나는 `id` 속성이 있는 사이트를 찾습니다.
4. `outboundIpAddresses` 및 `possibleOutboundIpAddresses`를 확인합니다. 

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

```azurecli-interactive
az functionapp show --resource-group <GROUP_NAME> --name <APP_NAME> --query outboundIpAddresses --output tsv
az functionapp show --resource-group <GROUP_NAME> --name <APP_NAME> --query possibleOutboundIpAddresses --output tsv
```
---

`outboundIpAddresses` 집합은 현재 함수 앱에서 사용할 수 있습니다. `possibleOutboundIpAddresses` 집합에는 함수 앱에서 [다른 가격 책정 계층으로 크기 조정](#outbound-ip-address-changes)하는 경우에만 사용할 수 있는 IP 주소가 포함됩니다.

> [!NOTE]
> [소비 계획](consumption-plan.md) 또는 [프리미엄 계획](functions-premium-plan.md)에서 실행되는 함수 앱의 크기를 조정하는 경우 새로운 아웃바운드 IP 주소 범위를 할당할 수 있습니다. 이러한 플랜 중 하나를 실행하는 경우 보고된 아웃바운드 IP 주소를 사용하여 확실한 허용 목록을 만들 수 없습니다. 동적 스케일링 중에 사용되는 모든 잠재적인 아웃바운드 주소를 포함하려면 전체 데이터 센터를 허용 목록에 추가해야 합니다.

## <a name="data-center-outbound-ip-addresses"></a>데이터 센터 아웃바운드 IP 주소

함수 앱에서 사용하는 아웃바운드 IP 주소를 허용 목록에 추가해야 하는 경우, 다른 옵션은 함수 앱의 데이터 센터(Azure 지역)를 허용 목록에 추가하는 것입니다. [모든 Azure 데이터 센터의 IP 주소를 나열하는 JSON 파일을 다운로드](https://www.microsoft.com/en-us/download/details.aspx?id=56519)할 수 있습니다. 그런 다음, 함수 앱이 실행되는 지역에 적용되는 JSON 조각을 찾습니다.

예를 들어 다음과 같은 JSON 조각은 서유럽에 대한 허용 목록을 나타냅니다.

```
{
  "name": "AzureCloud.westeurope",
  "id": "AzureCloud.westeurope",
  "properties": {
    "changeNumber": 9,
    "region": "westeurope",
    "platform": "Azure",
    "systemService": "",
    "addressPrefixes": [
      "13.69.0.0/17",
      "13.73.128.0/18",
      ... Some IP addresses not shown here
     "213.199.180.192/27",
     "213.199.183.0/24"
    ]
  }
}
```

 이 파일이 업데이트되는 시기와 IP 주소가 변경되는 시기에 대한 내용은 [다운로드 센터 페이지](https://www.microsoft.com/en-us/download/details.aspx?id=56519)의 **세부 정보** 섹션을 펼쳐보세요.

## <a name="inbound-ip-address-changes"></a><a name="inbound-ip-address-changes"></a>인바운드 IP 주소 변경

인바운드 IP 주소가 **변경될 수 있는 경우** 는 다음과 같습니다.

- 함수 앱을 삭제하고 다른 리소스 그룹에서 다시 만듭니다.
- 리소스 그룹 및 지역 조합에서 마지막 함수 앱을 삭제하고 다시 만듭니다.
- [인증서 갱신](../app-service/configure-ssl-certificate.md#renew-certificate) 중인 경우와 같이 TLS 바인딩을 삭제합니다.

함수 앱이 [소비 계획](consumption-plan.md) 또는 [프리미엄 계획](functions-premium-plan.md)에서 실행되는 경우 인바운드 IP 주소는 [위에서 나열](#inbound-ip-address-changes)한 것과 같은 작업을 수행하지 않은 경우에도 변경될 수 있습니다.

## <a name="outbound-ip-address-changes"></a>아웃바운드 IP 주소 변경

아웃바운드 IP 주소의 상대 안정성은 호스팅 계획에 따라 다릅니다.  

### <a name="consumption-and-premium-plans"></a>소비 및 프리미엄 계획

자동 크기 조정 동작으로 인해 [소비 계획](consumption-plan.md) 또는 [프리미엄 계획](functions-premium-plan.md)에서 실행될 때의 아웃바운드 IP는 언제든 변경될 수 있습니다. 

허용 목록에 추가해야 하는 경우와 같이 함수 앱의 아웃바운드 IP 주소를 제어해야 하는 경우에는 프리미엄 호스팅 플랜에서 실행하는 동안 [가상 네트워크 NAT 게이트웨이](#virtual-network-nat-gateway-for-outbound-static-ip)를 구현하는 것이 좋습니다. 전용(App Service) 플랜에서 실행하여 이 작업을 수행할 수도 있습니다.

### <a name="dedicated-plans"></a>전용 계획

전용(App Service) 계획에서 실행할 경우 다음 상황에서 함수 앱에 사용 가능한 아웃바운드 IP 주소 집합은 변경될 수 있습니다.

* 인바운드 IP 주소를 변경할 수 있는 작업을 수행합니다.
* 전용(App Service) 계획의 가격 책정 계층을 변경합니다. 모든 가격 책정 계층에 대해 앱에서 사용할 수 있는 모든 가능한 아웃바운드 IP 주소 목록은 `possibleOutboundIPAddresses` 속성에 있습니다. [아웃바운드 IP 찾기](#find-outbound-ip-addresses)를 참조하세요.

#### <a name="forcing-an-outbound-ip-address-change"></a>아웃바운드 IP 주소 변경 강제 적용

다음 절차를 사용하여 전용(App Service) 계획에서 아웃바운드 IP 주소 변경을 의도적으로 강제 적용합니다.

1. 표준 및 프리미엄 v2 가격 책정 계층 사이에서 App Service 계획의 크기를 위 또는 아래로 조정합니다.

2. 10분 동안 기다립니다.

3. 시작한 위치의 크기로 다시 조정합니다.

## <a name="ip-address-restrictions"></a>IP 주소 제한

함수 앱에 대한 액세스를 허용하거나 거부하려는 IP 주소의 목록을 구성할 수 있습니다. 자세한 내용은 [Azure App Service 고정 IP 제한](../app-service/app-service-ip-restrictions.md)을 참조하세요.

## <a name="dedicated-ip-addresses"></a>전용 IP 주소

함수 앱에 정적 전용 IP 주소가 필요한 경우를 탐색할 몇 가지 전략이 있습니다. 

### <a name="virtual-network-nat-gateway-for-outbound-static-ip"></a>아웃바운드 고정 IP용 가상 네트워크 NAT 게이트웨이

가상 네트워크 NAT 게이트웨이를 사용하여 고정 공용 IP 주소를 통해 트래픽을 전달함으로써 함수에서 아웃바운드 트래픽의 IP 주소를 제어할 수 있습니다. [프리미엄 플랜](functions-premium-plan.md) 또는 [전용(App Service) 플랜](dedicated-plan.md)에서 실행하는 경우 이 토폴로지를 사용할 수 있습니다. 자세히 알아보려면 [자습서: Azure 가상 네트워크 NAT 게이트웨이를 사용하여 Azure Functions 아웃바운드 IP 제어](functions-how-to-use-nat-gateway.md)를 참조하세요.

### <a name="app-service-environments"></a>App Service Environment

인바운드 및 아웃바운드 모두 IP 주소를 완전히 제어하려면 [App Service Environment](../app-service/environment/intro.md)(App Service 계획의 [격리 계층](https://azure.microsoft.com/pricing/details/app-service/))를 권장합니다. 자세한 내용은 [App Service Environment IP 주소](../app-service/environment/network-info.md#ase-ip-addresses) 및 [App Service Environment로의 인바운드 트래픽을 제어하는 방법](../app-service/environment/app-service-app-service-environment-control-inbound-traffic.md)을 참조하세요.

함수 앱이 App Service Environment에서 실행되는지 확인하려면 다음을 수행합니다.

# <a name="azure-porta"></a>[Azure Portal](#tab/portal)

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 함수 앱으로 이동합니다.
3. **개요** 탭을 선택합니다.
4. App Service 계획 계층이 **App Service 계획/가격 책정 계층** 아래에 표시됩니다. App Service Environment 가격 책정 계층은 **격리** 입니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query sku --output tsv
```

---

App Service Environment `sku`는 `Isolated`입니다.

## <a name="next-steps"></a>다음 단계

IP는 일반적으로 함수 앱 크기 조정 변경으로 인해 변경됩니다. [함수 앱 크기 조정에 대해 자세히 알아보세요](functions-scale.md).
