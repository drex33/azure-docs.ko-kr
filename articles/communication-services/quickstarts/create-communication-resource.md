---
title: 빠른 시작 - Azure Communication Services에서 리소스 만들기 및 관리
titleSuffix: An Azure Communication Services quickstart
description: 이 빠른 시작에서는 첫 번째 Azure Communication Services 리소스를 만들고 관리하는 방법에 대해 알아봅니다.
author: probableprime
manager: chpalm
services: azure-communication-services
ms.author: rifox
ms.date: 06/30/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.subservice: arm
zone_pivot_groups: acs-plat-azp-azcli-net-ps
ms.custom: mode-other
ms.openlocfilehash: 735467e3ea89192e6abde070a8eeefc99693701a
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133046409"
---
# <a name="quickstart-create-and-manage-communication-services-resources"></a>빠른 시작: Communication Services 리소스 만들기 및 관리

첫 번째 Communication Services 리소스를 프로비저닝하여 Azure Communication Services를 시작합니다. Communication Services 리소스는 [Azure Portal](https://portal.azure.com) 또는 .NET 관리 SDK를 통해 프로비저닝될 수 있습니다. 관리 SDK 및 Azure Portal을 사용하면 리소스를 만들고, 구성, 업데이트 및 삭제하고 Azure의 배포 및 관리 서비스인 [Azure Resource Manager](../../azure-resource-manager/management/overview.md)와 상호 연결할 수 있습니다. SDK에서 사용할 수 있는 모든 기능은 Azure Portal에서 사용할 수 있습니다. 


> [!WARNING]
> Communication Services는 여러 지역에서 사용 가능하지만 전화 번호를 얻으려면 리소스의 데이터 위치가 ‘US’로 설정되어야 합니다. 또한 Azure Communication Services용 리소스와 리소스 그룹을 동시에 만들 수는 없습니다. 리소스를 만들 때 이미 만들어진 리소스 그룹을 사용해야 합니다.

::: zone pivot="platform-azp"
[!INCLUDE [Azure portal](./includes/create-resource-azp.md)]
::: zone-end

::: zone pivot="platform-azcli"
[!INCLUDE [Azure CLI](./includes/create-resource-azcli.md)]
::: zone-end

::: zone pivot="platform-net"
[!INCLUDE [.NET](./includes/create-resource-net.md)]
::: zone-end

::: zone pivot="platform-powershell"
[!INCLUDE [PowerShell](./includes/create-resource-powershell.md)]
::: zone-end


## <a name="access-your-connection-strings-and-service-endpoints"></a>연결 문자열 및 서비스 엔드포인트 액세스

연결 문자열을 통해 Communication Services SDK를 Azure에 연결하고 인증할 수 있습니다. Azure Portal에서 또는 Azure Resource Manager API를 사용하여 프로그래밍 방식으로 Communication Services 연결 문자열 및 서비스 엔드포인트에 액세스할 수 있습니다.

Communication Services 리소스로 이동한 후 탐색 메뉴에서 **키** 를 선택하고 Communication Services SDK의 사용에 대한 **연결 문자열** 또는 **엔드포인트** 값을 복사합니다. 기본 키와 보조 키에 대한 액세스 권한이 있는지 확인합니다. 이는 타사 또는 스테이징 환경에 Communication Services 리소스에 대한 임시 액세스를 제공하려는 시나리오에서 유용할 수 있습니다.

:::image type="content" source="./media/key.png" alt-text="Communication Services 키 페이지의 스크린샷.":::

리소스 그룹 또는 특정 리소스에 대한 키와 같은 Azure CLI를 사용하여 키 정보에 액세스할 수도 있습니다. 

[Azure CLI](/cli/azure/install-azure-cli-windows?tabs=azure-cli)를 설치하고 다음 명령을 사용하여 로그인합니다. Azure 계정과 연결하려면 자격 증명을 제공해야 합니다.
```azurecli
az login
```

이제 리소스에 대한 중요한 정보에 액세스할 수 있습니다.
```azurecli
az communication list --resource-group "<resourceGroup>"

az communication list-key --name "<communicationName>" --resource-group "<resourceGroup>"
```

특정 구독을 선택하려는 경우 ```--subscription``` 플래그를 지정하고 구독 ID를 제공할 수도 있습니다.
```
az communication list --resource-group  "resourceGroup>"  --subscription "<subscriptionID>"

az communication list-key --name "<communicationName>" --resource-group "resourceGroup>" --subscription "<subscriptionID>"
```

## <a name="store-your-connection-string"></a>연결 문자열 저장

Communication Services SDK는 연결 문자열을 사용하여 Communication Services에 대한 요청에 권한을 부여합니다. 연결 문자열을 저장하기 위한 여러 가지 옵션이 있습니다.

* 데스크톱 또는 디바이스에서 실행 중인 애플리케이션의 경우 연결 문자열을 **app.config** 또는 **web.config** 파일에 저장할 수 있습니다. 이러한 파일의 **AppSettings** 섹션에 연결 문자열을 추가합니다.
* Azure App Service에서 실행 중인 애플리케이션의 경우, 연결 문자열을 [Azure Service 애플리케이션 설정](../../app-service/configure-common.md)에 저장할 수 있습니다. 포털 내 애플리케이션 설정 탭의 **연결 문자열** 섹션에 연결 문자열을 추가합니다.
* [Azure Key Vault](../../data-factory/store-credentials-in-key-vault.md)에 연결 문자열을 저장할 수 있습니다.
* 애플리케이션을 로컬로 실행하는 경우 환경 변수에 연결 문자열을 저장하는 것이 좋습니다.

### <a name="store-your-connection-string-in-an-environment-variable"></a>환경 변수에 연결 문자열 저장

환경 변수를 구성하려면 콘솔 창을 열고 아래 탭에서 운영 체제를 선택합니다. `<yourconnectionstring>`을 실제 연결 문자열로 바꿉니다.

#### <a name="windows"></a>[Windows](#tab/windows)

콘솔 창을 열고 다음 명령을 입력합니다.

```console
setx COMMUNICATION_SERVICES_CONNECTION_STRING "<yourconnectionstring>"
```

환경 변수를 추가한 후에는 콘솔 창을 포함하여 실행 중인 프로그램 중에서 환경 변수를 읽어야 하는 프로그램을 다시 시작해야 할 수도 있습니다. 예를 들어 편집기로 Visual Studio를 사용하는 경우 Visual Studio를 다시 시작한 후 예제를 실행합니다.

#### <a name="macos"></a>[macOS](#tab/unix)

**.zshrc** 를 편집하고 환경 변수를 추가합니다.

```bash
export COMMUNICATION_SERVICES_CONNECTION_STRING="<yourconnectionstring>"
```

환경 변수를 추가한 후에는 콘솔 창에서 `source ~/.zshrc` 명령을 실행하여 변경 내용을 적용합니다. IDE가 열린 상태에서 환경 변수를 만든 경우 해당 변수에 액세스하려면 편집기, IDE 또는 셸을 닫고 다시 열어야 합니다.

#### <a name="linux"></a>[Linux](#tab/linux)

**.bash_profile** 을 편집하고, 환경 변수를 추가합니다.

```bash
export COMMUNICATION_SERVICES_CONNECTION_STRING="<yourconnectionstring>"
```

환경 변수를 추가한 후에는 콘솔 창에서 `source ~/.bash_profile` 명령을 실행하여 변경 내용을 적용합니다. IDE가 열린 상태에서 환경 변수를 만든 경우 해당 변수에 액세스하려면 편집기, IDE 또는 셸을 닫고 다시 열어야 합니다.

---

## <a name="clean-up-resources"></a>리소스 정리

Communication Services 구독을 정리하고 제거하려면 리소스 또는 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 해당 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다. 

리소스를 삭제하는 동안 리소스에 할당된 전화 번호가 있으면 리소스에서 전화 번호는 동시에 자동으로 해제됩니다. 

> [!Note]
> 리소스 삭제는 **영구적** 이며 리소스를 삭제하는 경우 이벤트 그리드 필터, 전화 번호 또는 리소스에 연결된 기타 데이터를 포함한 데이터를 복구할 수 없습니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 다음을 수행하는 방법을 알아보았습니다.

> [!div class="checklist"]
> * Communication Services 리소스 만들기
> * 리소스 지리 및 태그 구성
> * 해당 리소스에 대한 키 액세스
> * 리소스 삭제

> [!div class="nextstepaction"]
> [첫 번째 사용자 액세스 토큰 만들기](access-tokens.md)
