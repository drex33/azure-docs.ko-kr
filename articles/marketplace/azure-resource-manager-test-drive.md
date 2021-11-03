---
title: 시험 사용의 유형, Microsoft 상업용 Marketplace
description: Microsoft 상업용 Marketplace의 시험 사용 유형
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.author: trkeya
author: trkeya
ms.date: 09/09/2021
ms.openlocfilehash: 7415e01f79c7982b2e56e1bb02fc84be13174388
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131065155"
---
# <a name="azure-resource-manager-test-drive"></a>Azure Resource Manager 시험 사용

Azure Marketplace 또는 AppSource에 제품이 있지만 Azure 리소스만 사용하여 시험 사용을 빌드하려는 경우 이 유형을 사용합니다. ARM(Azure Resource Manager) 템플릿은 솔루션을 가장 잘 나타내도록 디자인하는 Azure 리소스의 코딩된 컨테이너입니다. 시험 사용은 제공된 ARM 템플릿을 사용하여 필요한 모든 리소스를 리소스 그룹에 배포합니다. 이는 가상 머신 및 Azure 앱 제품의 경우 유일한 시험 사용 옵션입니다.

ARM 템플릿이 무엇인지 잘 모르는 경우 [Azure Resource Manager란?](../azure-resource-manager/management/overview.md) 및 [ARM 템플릿의 구조와 구문 이해](../azure-resource-manager/templates/syntax.md)를 읽고 고유한 템플릿을 빌드하고 테스트하는 방법을 더 잘 알아보세요.

**호스트된** 또는 **논리 앱** 시험 사용에 대한 자세한 내용은 [시험 사용이란?](what-is-test-drive.md)을 참조하세요.

## <a name="technical-configuration"></a>기술 구성

배포 템플릿에는 솔루션을 구성하는 모든 Azure 리소스가 포함되어 있습니다. 이 시나리오에 맞는 제품은 Azure 리소스만 사용합니다. 파트너 센터에서 다음 속성을 설정합니다.

- **지역**(필수) - 현재 시험 사용을 사용할 수 있는 26개의 Azure 지원 지역이 있습니다. 최상의 성능을 위해 가장 많은 수의 고객을 배치할 것으로 생각 하는 영역을 하나 선택 하는 것이 좋습니다. 구독에서 선택한 각 지역에 필요한 모든 리소스를 배포할 수 있는지 확인해야 합니다.

- **인스턴스** – 유형(핫 또는 콜드)을 선택하고, 제품을 사용할 수 있는 지역 수를 곱하여 사용 가능한 인스턴스 수를 선택합니다.

  - **핫** – 이 유형의 인스턴스는 배포된 후 선택한 지역별로 액세스를 기다립니다. 고객은 배포를 기다릴 필요 없이 시험 사용의 핫 인스턴스에 즉시 액세스할 수 있습니다. 단점은 이러한 인스턴스가 Azure 구독에서 항상 실행되고 있으므로 더 큰 가동 시간 비용이 발생한다는 것입니다. 대부분의 고객이 전체 배포를 기다리지 않으므로 핫 인스턴스를 하나 이상 사용하는 것이 좋습니다. 핫 인스턴스를 사용할 수 없는 경우 고객 사용량이 줄어들게 됩니다.

  - **콜드** - 이 유형의 인스턴스는 지역별로 배포할 수 있는 총 인스턴스 수를 나타냅니다. 콜드 인스턴스는 고객이 시험 사용을 요청할 때 배포할 전체 시험 사용 Resource Manager 템플릿이 필요하므로 콜드 인스턴스의 로드 속도는 핫 인스턴스보다 훨씬 느립니다. 단점은 시험 사용 기간 동안 비용을 지불하는 것이고 핫 인스턴스와 같이 Azure 구독에서 항상 실행되는 것은 아닙니다.

- **시험 사용 Azure Resource Manager 템플릿** – Azure Resource Manager 템플릿이 포함된 .zip 파일을 업로드합니다. 자세한 내용은 빠른 시작 문서 [Azure Portal을 사용하여 Azure Resource Manager 템플릿 만들기 및 배포](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)에서 Azure Resource Manager 템플릿을 만드는 방법을 참조하세요.

    > [!note]
    > 성공적으로 게시하려면 ARM 템플릿 서식의 유효성을 검사하는 것이 중요합니다. 이 작업을 수행하는 두 가지 방법은 (1) [온라인 API 도구](/rest/api/resources/deployments/validate)를 사용하거나 (2) [테스트 배포](../azure-resource-manager/templates/deploy-portal.md)를 사용하는 것입니다.

- **시험 사용 기간**(필수) – 시험 사용이 활성 상태로 유지되는 기간(시간)을 입력합니다. 이 기간이 끝나면 시험 사용이 자동으로 종료됩니다. 정수만 사용합니다(예: "2" 시간. "1.5"는 잘못됨).

## <a name="write-the-test-drive-template"></a>시험 사용 템플릿 작성

원하는 리소스 패키지를 디자인한 후에는 시험 사용 ARM 템플릿을 작성하고 빌드합니다. 시험 사용은 완전히 자동화된 모드로 배포를 실행하며, 이 때문에 시험 사용 템플릿에는 몇 가지 제한 사항이 있습니다.

### <a name="parameters"></a>매개 변수

대부분의 템플릿에는 리소스 이름, 리소스 크기(예: 스토리지 계정 유형 또는 가상 머신 크기), 사용자 이름 및 암호, DNS 이름 등을 정의하는 매개 변수 세트가 있습니다. Azure Portal을 사용하여 솔루션을 배포할 때 이러한 매개 변수를 모두 수동으로 채우고, 사용 가능한 DNS 이름 또는 스토리지 계정 이름을 선택할 수 있습니다.

![Azure Resource Manager의 매개 변수 목록](media/test-drive/resource-manager-parameters.png)

그러나 시험 사용은 사용자 상호 작용 없이 자동으로 작동하므로 제한된 매개 변수 범주의 세트만 지원합니다. 시험 사용 ARM 템플릿의 매개 변수가 지원되는 범주 중 하나에 해당하지 않을 경우 이 매개 변수를 변수 또는 상수 값으로 바꾸어야 합니다.

매개 변수에 유효한 이름을 사용할 수 있으며, 시험 사용은 metadata-type 값을 사용하여 매개 변수 범주를 인식합니다. 모든 템플릿 매개 변수에 대해 metadata-type을 지정합니다. 그렇지 않으면 템플릿이 유효성 검사를 통과하지 못합니다.

```JSON
"parameters": {
  ...
  "username": {
    "type": "string",
    "metadata": {
      "type": "username"
    }
  },
  ...
}
```

> [!NOTE]
> 모든 매개 변수는 선택 사항이므로 사용하고 싶지 않다면 사용하지 않아도 됩니다.

### <a name="accepted-parameter-metadata-types"></a>허용되는 매개 변수 메타데이터 유형

| 메타데이터 유형   | 매개 변수 유형  | 설명     | 샘플 값    |
|---|---|---|---|
| **baseuri**     | 문자열          | 배포 패키지의 기본 URI| `https://<..>.blob.core.windows.net/<..>` |
| **username**    | 문자열          | 새 임의 사용자 이름입니다.| admin68876      |
| **password**    | 보안 문자열    | 새 임의 암호 | Lp!ACS\^2kh     |
| **세션 ID**   | 문자열          | 고유한 시험 사용 세션 ID(GUID)    | b8c8693e-5673-449c-badd-257a405a6dee |

#### <a name="baseuri"></a>baseuri

시험 사용은 이 매개 변수를 배포 패키지의 **기본 URI** 로 초기화하므로 이 매개 변수를 사용하여 패키지에 포함된 파일의 URI를 구성할 수 있습니다.

```JSON
"parameters": {
  ...
  "baseuri": {
    "type": "string",
    "metadata": {
      "type": "baseuri",
      "description": "Base Uri of the deployment package."
    }
  },
  ...
}
```

템플릿 내에서 이 매개 변수를 사용하여 시험 사용 배포 패키지에 있는 파일의 URI를 구성합니다. 다음 예제는 연결된 템플릿의 URI를 구성하는 방법을 보여 줍니다.

```JSON
"templateLink": {
  "uri": "[concat(parameters('baseuri'),'templates/solution.json')]",
  "contentVersion": "1.0.0.0"
}
```

#### <a name="username"></a>사용자 이름

시험 사용은 이 매개 변수를 새 임의 사용자 이름으로 초기화합니다.

```JSON
"parameters": {
  ...
  "username": {
    "type": "string",
    "metadata": {
      "type": "username",
      "description": "Solution admin name."
    }
  },
  ...
}
```

샘플 값: `admin68876`

솔루션에 임의 또는 상수 사용자 이름을 사용할 수 있습니다.

#### <a name="password"></a>password

시험 사용은 이 매개 변수를 새 임의 암호로 초기화합니다.

```JSON
"parameters": {
  ...
  "password": {
    "type": "securestring",
    "metadata": {
      "type": "password",
      "description": "Solution admin password."
    }
  },
  ...
}
```

샘플 값: `Lp!ACS^2kh`

솔루션에 임의 또는 상수 암호를 사용할 수 있습니다.

#### <a name="session-id"></a>세션 ID

시험 사용은 이 매개 변수를 시험 사용 세션 ID에 해당하는 고유 GUID로 초기화합니다.

```JSON
"parameters": {
  ...
  "sessionid": {
    "type": "string",
    "metadata": {
      "type": "sessionid",
      "description": "Unique test drive session id."
    }
  },
  ...
}
```

샘플 값: `b8c8693e-5673-449c-badd-257a405a6dee`

필요한 경우 이 매개 변수를 사용하여 시험 사용 세션을 고유하게 식별할 수 있습니다.

### <a name="unique-names"></a>고유 이름

스토리지 계정 또는 DNS 이름과 같은 일부 Azure 리소스에는 전역적으로 고유한 이름이 필요합니다. 즉, 시험 사용이 ARM 템플릿을 배포할 때마다 모든 리소스에 대해 고유한 이름이 지정된 새 리소스 그룹을 만듭니다. 따라서 리소스 그룹 ID의 변수 이름과 연결된 [uniquestring](../azure-resource-manager/templates/template-functions.md) 함수를 사용하여 임의의 고유한 값을 생성해야 합니다.

```JSON
"variables": {
  ...
  "domainNameLabel": "[concat('contosovm',uniquestring(resourceGroup().id))]",
  "storageAccountName": "[concat('contosodisk',uniquestring(resourceGroup().id))]",
  ...
}
```

매개 변수/변수 문자열(`contosovm`)을 고유한 문자열 출력(`resourceGroup().id`)과 연결해야 합니다. 이렇게 하면 각 변수의 고유성과 안정성이 보장됩니다.

예를 들어, 대부분의 리소스 이름은 숫자로 시작할 수 없지만, 고유한 문자열 함수는 숫자로 시작하는 문자열을 반환할 수 있습니다. 따라서 원시 고유 문자열 출력을 사용하는 경우 배포에 실패합니다.

[이 문서](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)에서 리소스 명명 규칙 및 제한 사항에 대한 추가 정보를 확인할 수 있습니다.

### <a name="deployment-location"></a>배포 위치

여러 Azure 지역에서 시험 사용을 제공할 수 있습니다.

시험 사용에서 랩 인스턴스를 만드는 경우 항상 선택된 지역 중 하나에 리소스 그룹을 만든 다음, 이 그룹 컨텍스트에서 배포 템플릿을 실행합니다. 따라서 템플릿은 리소스 그룹에서 배포 위치를 선택해야 합니다.

```JSON
"variables": {
  ...
  "location": "[resourceGroup().location]",
  ...
}
```

그런 다음 이 위치를 특정 랩 인스턴스의 모든 리소스에 대해 사용합니다.

```JSON
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Network/publicIPAddresses",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Network/virtualNetworks",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Network/networkInterfaces",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Compute/virtualMachines",
    "location": "[variables('location')]",
    ...
  }
]
```

사용자 구독이 선택한 각 지역에 원하는 모든 리소스를 배포하도록 허용하는지 확인해야 합니다. 또한 사용하도록 설정하려는 모든 지역에서 가상 머신 이미지를 사용할 수 있는지 확인해야 합니다. 그렇지 않으면 일부 지역에서 배포 템플릿이 작동하지 않습니다.

### <a name="outputs"></a>출력

Resource Manager 템플릿을 사용하는 경우 일반적으로 출력을 생성하지 않고 배포할 수 있습니다. 이는 템플릿 매개 변수를 채우는 데 사용하는 모든 값을 알고 있으며 항상 리소스의 속성을 수동으로 검사할 수 있기 때문입니다.

그러나 시험 사용 Resource Manager 템플릿의 경우, 랩에 액세스하는 데 필요한 모든 정보(웹 사이트 URI, Virtual Machine 호스트 이름, 사용자 이름 및 암호)를 시험 사용에 반환하는 것이 중요합니다. 이러한 변수는 고객에게 표시되므로 모든 출력 이름을 읽을 수 있는지 확인합니다.

템플릿 출력과 관련된 제한 사항은 없습니다. 시험 사용은 모든 출력 값을 문자열로 변환하므로 개체를 출력으로 보내는 경우 사용자에게 JSON 문자열이 표시됩니다.

예제:

```JSON
"outputs": {
  "Host Name": {
    "type": "string",
    "value": "[reference(variables('pubIpId')).dnsSettings.fqdn]"
  },
  "User Name": {
    "type": "string",
    "value": "[parameters('adminName')]"
  },
  "Password": {
    "type": "string",
    "value": "[parameters('adminPassword')]"
  }
}
```

### <a name="subscription-limits"></a>구독 한도

구독 및 서비스 제한을 기억하세요. 예를 들어, 최대 10개의 4 코어 가상 머신을 배포하려는 경우 랩에 사용하는 구독이 40개의 코어 사용을 허용하는지 확인해야 합니다. Azure 구독 및 서비스 제한에 대한 자세한 내용은 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-resource-manager/management/azure-subscription-service-limits.md)을 참조하세요. 동시에 여러 개의 시험 사용을 수행할 수 있으므로 구독이 수행할 수 있는 동시 시험 사용의 총수에 코어 수를 곱한 개수를 처리할 수 있는지 확인합니다.

### <a name="what-to-upload"></a>업로드할 사항

시험 사용 ARM 템플릿은 다양한 배포 아티팩트를 포함할 수 있되, `main-template.json`이라는 하나의 파일이 반드시 있어야 하는 zip 파일로 업로드됩니다. 이는 시험 사용에서 랩을 인스턴스화하는 데 사용하는 ARM 배포 템플릿입니다. 이 파일 이외에 추가 리소스가 있는 경우 템플릿 내에서 외부 리소스로 참조하거나 zip 파일에 리소스를 포함할 수 있습니다.

게시 인증 중에 시험 사용은 배포 패키지의 압축을 풀고 해당 콘텐츠를 내부 시험 사용 Blob 컨테이너에 넣습니다. 컨테이너 구조는 배포 패키지의 구조를 반영합니다.

| package.zip                       | 시험 사용 Blob 컨테이너         |
|---|---|
| `main-template.json`                | `https:\//\<\...\>.blob.core.windows.net/\<\...\>/main-template.json`  |
| `templates/solution.json`           | `https:\//\<\...\>.blob.core.windows.net/\<\...\>/templates/solution.json` |
| `scripts/warmup.ps1`                | `https:\//\<\...\>.blob.core.windows.net/\<\...\>/scripts/warmup.ps1`  |
|||

이 Blob 컨테이너의 URI를 기본 URI라고 합니다. 랩의 수정 버전마다 자체 Blob 컨테이너가 있으므로 랩의 수정 버전마다 자체 기본 URI가 있습니다. 시험 사용은 템플릿 매개 변수를 통해 압축을 푼 배포 패키지의 기본 URI를 템플릿에 전달할 수 있습니다.

### <a name="transform-template-examples-for-test-drive"></a>시험 사용에 대한 템플릿 변환 예제

리소스의 아키텍처를 시험 사용 Resource Manager 템플릿으로 변환하는 프로세스는 까다로울 수 있습니다. 추가 도움말은 [시험 사용이란?](what-is-test-drive.md#transforming-examples)에서 현재 배포 템플릿을 가장 잘 변환하는 방법의 예제를 참조하세요.

## <a name="test-drive-deployment-subscription-details"></a>시험 사용 배포 구독 세부 정보

마지막으로 완료해야 하는 섹션은 Azure 구독과 Azure AD(Active Directory)를 연결하여 시험 사용을 자동으로 배포하는 것입니다.

![시험 사용 배포 구독 세부 정보](media/test-drive/deployment-subscription-details.png)

1. **Azure 구독 ID** 를 가져옵니다. Azure 서비스 및 Azure Portal 액세스 권한을 부여합니다. 해당 구독에서 리소스 사용량이 보고되며 서비스 대금이 청구됩니다. 별도의 시험 사용 전용 Azure 구독이 없으면 새로 만듭니다. Azure Portal에 로그인하고 왼쪽 탐색 메뉴에서 **구독** 을 선택하여 Azure 구독 ID(예: `1a83645ac-1234-5ab6-6789-1h234g764ghty1`)를 찾을 수 있습니다.

   ![Azure 구독](media/test-drive/azure-subscriptions.png)

2. **Azure AD 테넌트 ID** 를 가져옵니다. 사용 가능한 테넌트 ID가 이미 있는 경우 **Azure Active Directory** > **속성** > **디렉터리 ID** 에서 찾을 수 있습니다.

   ![Azure Active Directory 속성](media/test-drive/azure-active-directory-properties.png)

   테넌트 ID가 없는 경우 Azure Active Directory에서 새로 만듭니다. 테넌트 설정에 대한 도움말은 [빠른 시작: 테넌트 설정](../active-directory/develop/quickstart-create-new-tenant.md)을 참조하세요.

3. 테넌트에서 Microsoft Test-Drive 애플리케이션을 프로비전합니다. 이 애플리케이션을 사용하여 시험 사용 리소스에 대한 작업을 수행합니다.
    1. 아직 없는 경우 [Azure Az PowerShell 모듈을](/powershell/azure/install-az-ps)설치합니다.
    1. Microsoft Test-Drive 애플리케이션에 대한 서비스 주체를 추가합니다.
        1. 를 실행하고 `Connect-AzAccount` 자격 증명을 제공하여 Azure 계정에 로그인하려면 Azure Active Directory **전역 관리자** 기본 제공 [역할](../active-directory/roles/permissions-reference.md#global-administrator)이 필요합니다. 
        1. 새 서비스 주체 를 `New-AzADServicePrincipal -ApplicationId d7e39695-0b24-441c-a140-047800a05ede -DisplayName 'Microsoft TestDrive' -SkipAssignment` 만듭니다.
        1. 서비스 주체가 생성되었는지 `Get-AzADServicePrincipal -DisplayName 'Microsoft TestDrive'` 확인합니다.
      ![서비스 주체를 확인하는 코드를 표시합니다.](media/test-drive/commands-to-verify-service-principal.png)

4. **Azure AD 앱 ID의** 경우 애플리케이션 ID 에 붙여넣습니다. `d7e39695-0b24-441c-a140-047800a05ede`
5. **Azure AD 앱 키** 의 경우 비밀이 필요 없으므로 "비밀 없음"과 같은 더미 비밀을 삽입합니다.
6. 애플리케이션을 사용하여 구독에 배포하고 있으므로 Azure Portal 또는 PowerShell에서 구독에 대한 기여자로 애플리케이션을 추가해야 합니다.

   1. Azure 포털에서 다음을 수행합니다.

       1. 시험 사용 시 사용할 **구독을** 선택합니다.
       1. **액세스 제어(IAM)** 를 선택합니다.<br>

          ![새 Access Control(M) 기여자 추가](media/test-drive/access-control-principal.png)

       1. 역할 **할당 탭을** 선택한 **다음, + 역할 할당 추가를** 선택합니다.

          ![Access Control(M) 선택 창에서 역할 할당 탭을 선택한 다음, + 역할 할당 추가를 선택하는 방법을 보여줍니다.](media/test-drive/access-control-principal-add-assignments.jpg)

       1. Azure AD 애플리케이션 `Microsoft TestDrive` 이름()을 입력합니다. **기여자** 역할을 할당하려는 애플리케이션을 선택합니다.

          ![기여자 역할을 할당하는 방법](media/test-drive/access-control-permissions.jpg)

       1. **저장** 을 선택합니다.
   1. PowerShell을 사용하는 경우:
      1. ServicePrincipal object-id를 얻으려면 다음을 `(Get-AzADServicePrincipal -DisplayName 'Microsoft TestDrive').id` 실행합니다.
      1. ObjectId 및 구독 ID 를 통해 `New-AzRoleAssignment -ObjectId <objectId> -RoleDefinitionName Contributor -Scope /subscriptions/<subscriptionId>` 실행합니다.

> [!NOTE]
> 이전 appID를 삭제하기 전에 Azure Portal 이동한 다음 **리소스 그룹** 로 이동하여 를 검색합니다. `CloudTry_` 열을 통해 **시작된 이벤트를** 확인합니다.
>
> :::image type="content" source="media/test-drive/event-initiated-by-field.png" lightbox="media/test-drive/event-initiated-by-field.png" alt-text="이벤트 시작자 필드를 표시합니다.":::
>
> 하나 이상의 **리소스(작업 이름)가** **Microsoft TestDrive** 로 설정되어 있지 않으면 이전 appID를 삭제하지 마세요.
>
> appID를 삭제하려면 왼쪽 탐색 메뉴에서 **Azure Active Directory** 앱  >  **등록을** 선택한 **다음, 모든 애플리케이션 탭을** 선택합니다. 애플리케이션을 선택하고 **삭제를** 선택합니다.

## <a name="republish"></a>다시 게시

이제 모든 시험 사용 필드가 완료되었으므로 제품을 **다시 게시** 합니다. 시험 사용이 인증을 통과하면 제품 미리 보기에서 고객 경험을 포괄적으로 테스트합니다.

1. UI에서 시험 사용을 시작합니다.
1. Azure Portal 내에서 Azure 구독을 엽니다.
1. 시험 사용이 올바르게 배포되는지 확인합니다.

   ![Azure Portal](media/test-drive/azure-portal.png)

고객에 대해 프로비전된 시험 사용 인스턴스를 삭제하지 마세요. 고객이 작업을 완료한 후에는 시험 사용 서비스에서 이러한 리소스 그룹을 자동으로 정리합니다.

미리 보기 제품에 문제가 없는 것으로 확인되면 **라이브로 제공** 할 수 있습니다. 전체 엔드투엔드 환경을 두 번 확인하는 최종 검토 프로세스가 있습니다. 제품이 거부되는 경우 수정해야 하는 사항을 설명하는 제품에 관한 이메일을 엔지니어링 담당자에게 드립니다.

## <a name="next-steps"></a>다음 단계

- 파트너 센터에서 제품을 만드는 방법에 대한 지침을 수행하는 경우 뒤로 화살표를 사용하여 해당 항목으로 돌아옵니다.
- [시험 사용이란?](what-is-test-drive.md)에서 다른 유형의 시험 사용에 대해 자세히 알아보세요.