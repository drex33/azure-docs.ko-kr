---
author: baanders
description: Azure Digital Twins에서 작동하도록 Azure 함수를 구성하는 방법을 설명하는 포함 파일 - CLI 지침
ms.service: digital-twins
ms.topic: include
ms.date: 7/20/2021
ms.author: baanders
ms.openlocfilehash: fffa878ca69b0704417c7db7492eb5523217e4e3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121802558"
---
[Azure Cloud Shell](https://shell.azure.com) 또는 [로컬 Azure CLI](/cli/azure/install-azure-cli)에서 다음 명령을 실행합니다.

#### <a name="assign-an-access-role"></a>액세스 역할 할당

Azure 함수에는 전달자 토큰을 전달해야 합니다. 전달자 토큰이 전달되었는지 확인하려면 함수 앱에 Azure Digital Twins 인스턴스에 대한 **Azure Digital Twins 데이터 소유자** 역할을 부여합니다. 그러면 함수 앱에 인스턴스에 대한 데이터 평면 작업을 수행할 수 있는 권한이 제공됩니다.

[!INCLUDE [digital-twins-permissions-required.md](digital-twins-permissions-required.md)]

1. 다음 명령을 사용하여 함수의 [시스템 관리 ID](../articles/active-directory/managed-identities-azure-resources/overview.md) 세부 정보를 확인합니다. 출력에서 `principalId` 필드를 기록해 둡니다. 이 ID를 사용하여 다음 단계에서 해당 권한을 부여할 수 있도록 함수를 참조합니다.

    ```azurecli-interactive 
    az functionapp identity show --resource-group <your-resource-group> --name <your-function-app-name> 
    ```

    >[!NOTE]
    > ID의 세부 정보를 표시하지 않고 결과가 비어 있는 경우 다음 명령을 사용하여 함수에 대한 시스템 관리 ID를 새로 만듭니다.
    > 
    >```azurecli-interactive    
    >az functionapp identity assign --resource-group <your-resource-group> --name <your-function-app-name>  
    >```
    >
    > 출력에는 다음 단계에 필요한 `principalId` 값을 포함하여 ID의 세부 정보가 표시됩니다. 

1. 다음 명령에 `principalId` 값을 사용하여 함수에 Azure Digital Twins 인스턴스의 **Azure Digital Twins 데이터 소유자** 역할을 부여합니다.

    ```azurecli-interactive 
    az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<principal-ID>" --role "Azure Digital Twins Data Owner"
    ```

#### <a name="configure-application-settings"></a>애플리케이션 설정 구성

다음으로, [환경 변수](../articles/azure-functions/functions-how-to-use-azure-function-app-settings.md?tabs=portal#use-application-settings)를 설정하여 함수에서 **Azure Digital Twins 인스턴스** 의 URL에 액세스할 수 있도록 합니다.

> [!TIP]
> Azure Digital Twins 인스턴스의 URL은 인스턴스의 호스트 이름 앞에 *https://* 가 추가되어 만들어집니다. 인스턴스의 모든 속성과 함께 호스트 이름을 확인하려면 `az dt show --dt-name <your-Azure-Digital-Twins-instance>` 명령을 실행합니다.

다음 명령은 인스턴스에 액세스해야 할 때마다 함수에서 사용할 인스턴스의 URL에 대한 환경 변수를 설정합니다.

```azurecli-interactive 
az functionapp config appsettings set --resource-group <your-resource-group> --name <your-function-app-name> --settings "ADT_SERVICE_URL=https://<your-Azure-Digital-Twins-instance-host-name>"
```