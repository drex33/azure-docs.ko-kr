---
title: 정의에서 기본 스토리지 비밀을 사용하지 않고 함수 앱 만들기
titleSuffix: Azure Functions
ms.service: azure-functions
description: 함수 앱 정의에서 스토리지 연결 문자열을 제거하는 방법을 알아봅니다.
ms.topic: tutorial
ms.date: 10/20/2021
ms.openlocfilehash: 5685c6f246ecf9829aaf48d042cea4de066dc937
ms.sourcegitcommit: 4298f71f502c70d601a7c58b28fc7bca62be3595
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/25/2021
ms.locfileid: "133133432"
---
# <a name="tutorial-create-a-function-app-that-connects-to-azure-services-using-identities-instead-of-secrets"></a>자습서: 암호 대신 ID를 사용하여 Azure 서비스에 연결하는 함수 앱 만들기

이 자습서에서는 가능한 경우 비밀 또는 연결 문자열 대신 Azure Active Directory ID를 사용하여 함수 앱을 구성하는 방법을 보여 줍니다. ID를 사용하면 실수로 중요한 암호를 누출하는 것을 방지할 수 있으며, 데이터에 액세스하는 방법을 보다 잘 파악할 수 있습니다. ID 기반 연결에 대해 자세히 알아보려면 [ID 기반 연결 구성](functions-reference.md#configure-an-identity-based-connection)을 참조하세요.

표시되는 절차는 기본적으로 모든 언어에 해당하지만 이 자습서에서는 현재 Windows의 C# 클래스 라이브러리 함수를 지원합니다. 

이 자습서에서는 다음 작업 방법을 알아봅니다.
> [!div class="checklist"]
> * ARM 템플릿을 사용하여 Azure에서 함수 앱 만들기
> * 함수 앱에서 시스템 할당 및 사용자 할당 관리 ID 모두 사용
> * 다른 리소스에 대한 사용 권한을 부여하는 역할 할당 만들기
> * ID로 바꿀 수 없는 암호를 Azure Key Vault로 이동
> * 관리 ID를 사용하여 기본 호스트 스토리지에 연결하도록 앱 구성

이 자습서를 완료한 후에는 [암호 대신 트리거와 바인딩을 사용하여 ID 기반 연결을 사용]하는 방법을 보여 주는 추가 정보 자습서를 완료해야 합니다. 

## <a name="prerequisites"></a>사전 요구 사항

+ 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download)

+ [Azure Functions Core Tools](functions-run-local.md#v2) 버전 3.x.

## <a name="why-use-identity"></a>ID를 사용하는 이유

암호 및 자격 증명을 관리하는 일은 모든 규모의 팀에서 공통되는 난제입니다. 암호를 도난당하거나 실수로 노출되지 않게 보호해야 하며 주기적으로 순환해야 할 수도 있습니다. 대부분의 Azure 서비스에서는 [Azure AD(Azure Active Directory)](../active-directory/fundamentals/active-directory-whatis.md)에서 ID를 사용하여 클라이언트를 인증하고 신속하게 수정 및 취소할 수 있는 권한을 확인할 수 있습니다. 이를 통해 운영 오버헤드를 줄이면서 애플리케이션 보안을 더 강력하게 제어할 수 있습니다. ID는 애플리케이션 개발자와 같은 사용자나 [관리 ID](../active-directory/managed-identities-azure-resources/overview.md)를 사용하는 Azure에서 실행 중인 애플리케이션일 수 있습니다.

일부 서비스는 Azure Active Directory 인증을 지원하지 않으므로 애플리케이션에서 암호를 요구하는 경우도 있습니다. 그러나 이러한 암호를 [Azure Key Vault](../key-vault/general/overview.md)에 저장하여 암호의 관리 수명 주기를 간소화할 수 있습니다. Key Vault에 대한 액세스도 ID를 사용하여 제어합니다.

사용할 수 있을 때 암호 대신 ID를 사용하고 사용할 수 없을 때 Key Vault를 사용하는 방법을 이해하면 위험을 줄이고, 운영 오버헤드를 줄이고, 일반적으로 애플리케이션의 보안 상태를 향상시킬 수 있습니다.

## <a name="create-a-function-app-that-uses-key-vault-for-necessary-secrets"></a>필요한 암호에 Key Vault를 사용하는 함수 앱 만들기

Azure Files는 아직 SMB 파일 공유에 대해 Azure Active Directory 인증을 지원하지 않는 서비스의 예입니다. Azure Files는 프리미엄 및 소비 플랜에서 Windows 배포를 위한 기본 파일 시스템입니다. [Azure Files 완전히 제거](./storage-considerations.md#create-an-app-without-azure-files)할 수는 있지만 이로 인해 원치 않는 제한이 적용됩니다. 대신, Azure Files 연결 문자열을 Azure Key Vault로 이동합니다. 이러한 방식으로 중앙에서 관리되며 ID를 통해 액세스를 제어합니다.

### <a name="create-an-azure-key-vault"></a>Azure Key Vault 만들기

먼저 암호를 저장할 Key Vault가 필요합니다. 자격 증명 모음에서 암호를 읽을 수 있는 사용자를 결정하기 위해 [Azure RBAC(역할 기반 액세스 제어)](../role-based-access-control/overview.md)를 사용하도록 구성합니다.

1. [Azure Portal](https://portal.azure.com)에서 **리소스 만들기(+)** 를 선택합니다.

1. **리소스 만들기** 페이지에서 **보안** > **Key Vault** 를 선택합니다.

1. **기본 사항** 페이지에서 다음 표를 사용하여 Key Vault를 구성합니다.

    | 옵션      | 제안 값  | Description |
    | ------------ | ---------------- | ----------- |
    | **구독** | 사용자의 구독 | 이 새 함수 앱이 만들어질 구독입니다. |
    | **[리소스 그룹](../azure-resource-manager/management/overview.md)** |  myResourceGroup | 함수 앱을 만들 새 리소스 그룹의 이름입니다. |
    | **Key Vault 이름** | 전역적으로 고유한 이름 | 새 함수 앱을 식별하는 이름입니다. 자격 증명 모음 이름은 영숫자와 대시만 포함해야 하고 숫자로 시작할 수 없습니다. |
    | **가격 책정 계층** | Standard | 청구 옵션입니다. 이 자습서에서는 표준이면 충분합니다. |
    |**지역**| 기본 지역 | 사용자 근처 또는 함수가 액세스할 기타 서비스에 가까운 [지역](https://azure.microsoft.com/regions/)을 선택합니다. |

    "복구 옵션" 섹션에서는 기본 선택 항목을 사용합니다. 

1. 나중에 필요하므로 사용한 이름을 적어 둡니다.

1. **다음: 액세스 정책** 을 클릭하여 **액세스 정책** 탭으로 이동합니다.

1. **권한 모델** 아래에서 **Azure 역할 기반 액세스 제어** 를 선택합니다.

1. **검토 + 만들기** 를 선택합니다. 구성을 검토한 후 **만들기** 를 클릭합니다.

### <a name="set-up-an-identity-and-permissions-for-the-app"></a>앱에 대한 ID 및 권한 설정

Azure Key Vault를 사용하려면 앱에 암호 읽기 권한을 부여할 수 있는 ID가 있어야 합니다. 이 앱은 사용자 할당 ID를 사용하여 앱을 만들기 전에 권한을 설정할 수 있도록 합니다. [Azure Functions에서 관리 ID를 사용하는 방법](../app-service/overview-managed-identity.md?toc=%2Fazure%2Fazure-functions%2Ftoc.json) 항목에서 Azure Functions용 관리 ID에 대해 자세히 알아볼 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에서 **리소스 만들기(+)** 를 선택합니다.

1. **리소스 만들기** 페이지에서 **ID** > **사용자 할당 관리 ID** 를 선택합니다.

1. **기본 사항** 페이지에서 다음 표를 사용하여 ID를 구성합니다.

    | 옵션      | 제안 값  | Description |
    | ------------ | ---------------- | ----------- |
    | **구독** | 사용자의 구독 | 이 새 함수 앱이 만들어질 구독입니다. |
    | **[리소스 그룹](../azure-resource-manager/management/overview.md)** |  myResourceGroup | 함수 앱을 만들 새 리소스 그룹의 이름입니다. |
    |**지역**| 기본 지역 | 사용자 근처 또는 함수가 액세스할 기타 서비스에 가까운 [지역](https://azure.microsoft.com/regions/)을 선택합니다. |
    | **이름** | 전역적으로 고유한 이름 | 새 사용자 할당 ID를 식별하는 이름입니다. |

1. **검토 + 만들기** 를 선택합니다. 구성을 검토한 후 **만들기** 를 클릭합니다.

1. ID가 만들어지면 Portal에서 해당 ID로 이동합니다. **속성** 을 선택하고 나중에 필요하므로 **리소스 ID** 를 기록해 둡니다.

1. **Azure 역할 할당** 을 선택하고 **역할 할당 추가(미리 보기)** 를 클릭합니다.

1. **역할 할당 추가(미리 보기)** 페이지에서 아래 표에 표시된 대로 옵션을 사용합니다.

    | 옵션      | 제안 값  | Description |
    | ------------ | ---------------- | ----------- |
    | **범위** |  Key Vault |  범위는 리소스 할당이 적용되는 리소스 세트입니다. 범위에는 하위 수준에서 상속되는 수준이 있습니다. 예를 들어, 구독 범위를 선택하면 역할 할당이 구독의 모든 리소스 그룹 및 리소스에 적용됩니다. |
    |**구독**| 사용자의 구독 | 이 새 함수 앱이 만들어질 구독입니다. |
    |**리소스**| 사용자의 Key Vault | 이전에 만든 Key Vault입니다. |
    | **역할** | Key Vault 비밀 사용자 | 역할은 부여되는 사용 권한 컬렉션입니다. Key Vault 비밀 사용자는 자격 증명 모음에서 비밀 값을 읽을 수 있는 권한을 ID에 부여합니다. |

1. **저장** 을 선택합니다. ID에 대한 역할 할당 목록을 새로 고칠 때 역할이 표시되는 데 1~2분 정도 걸릴 수 있습니다.

이제 ID가 자격 증명 모음에 저장된 비밀을 읽을 수 있습니다. 이 자습서의 후반부에서는 다른 용도로 추가 역할 할당을 추가합니다.

### <a name="generate-a-template-for-creating-a-function-app"></a>함수 앱을 만들기 위한 템플릿 생성

함수 앱을 만들기 위한 포털 환경은 Azure Key Vault와 상호 작용하지 않으므로 Azure Resource Manager 템플릿을 생성하고 편집해야 합니다. 그런 다음, 이 템플릿을 사용하여 Key Vault에서 Azure Files 연결 문자열을 참조하는 함수 앱을 만들 수 있습니다.

> [!IMPORTANT]
> ARM 템플릿을 편집할 때까지 함수 앱을 만들지 마세요. Azure Files 구성은 앱을 만들 때 설정해야 합니다.

1. [Azure Portal](https://portal.azure.com)에서 **리소스 만들기(+)** 를 선택합니다.

1. **새로 만들기** 페이지에서 **컴퓨팅** > **함수 앱** 을 선택합니다.

1. **기본 사항** 페이지에서 다음 표를 사용하여 함수 앱 설정을 구성합니다.

    | 옵션      | 제안 값  | Description |
    | ------------ | ---------------- | ----------- |
    | **구독** | 사용자의 구독 | 이 새 함수 앱이 만들어질 구독입니다. |
    | **[리소스 그룹](../azure-resource-manager/management/overview.md)** |  myResourceGroup | 함수 앱을 만들 새 리소스 그룹의 이름입니다. |
    | **함수 앱 이름** | 전역적으로 고유한 이름 | 새 함수 앱을 식별하는 이름입니다. 유효한 문자는 `a-z`(대/소문자 구분 안 함), `0-9`및 `-`입니다.  |
    |**게시**| 코드 | 코드 파일 또는 Docker 컨테이너를 게시하도록 선택합니다. |
    | **런타임 스택** | .NET | 이 자습서에서는 .NET을 사용합니다. |
    |**지역**| 기본 지역 | 사용자 근처 또는 함수가 액세스할 기타 서비스에 가까운 [지역](https://azure.microsoft.com/regions/)을 선택합니다. |

1. **검토 + 만들기** 를 선택합니다. 앱은 **호스팅** 및 **모니터링** 페이지에서 기본값을 사용합니다. 기본 옵션을 검토할 수 있으며 생성한 ARM 템플릿에 포함됩니다. 

1. 여기에서 함수 앱을 만드는 대신 **다음** 단추 오른쪽에 있는 **자동화를 위한 템플릿 다운로드** 를 선택합니다.

1. **배포** 를 선택한 다음, 사용자 지정 배포 페이지에서 **템플릿 편집** 을 선택합니다.

    :::image type="content" source="./media/functions-identity-connections-tutorial/function-app-portal-template-deploy-button.png" alt-text="템플릿 화면 맨 위에 있는 배포 단추를 찾을 수 있는 위치의 스크린샷":::

### <a name="edit-the-template"></a>템플릿 편집

이제 템플릿을 편집하여 Azure Files 연결 문자열을 Key Vault에 저장하고 함수 앱에서 참조할 수 있도록 합니다. 계속하기 전에 이전 섹션의 다음 값이 있는지 확인합니다.

- 사용자가 할당한 ID의 리소스 ID
- 키 자격 증명 모음의 이름

> [!NOTE]
> 자동화를 위한 전체 템플릿을 만드는 경우 ID 및 역할 할당 리소스에 대한 정의를 적절한 `dependsOn` 절과 함께 포함할 수 있습니다. 이렇게 하면 Portal을 사용했던 이전 단계가 대신 진행됩니다. 각 서비스에 대해서는 [Azure Resource Manager 지침](../azure-resource-manager/templates/syntax.md) 및 설명서를 참조하세요.


1. 편집기에서 `resources` 배열이 시작되는 위치를 찾습니다. 함수 앱 정의 앞에 Azure Files 연결 문자열을 Key Vault에 추가하는 다음 섹션을 추가합니다. "VAULT_NAME"을 Key Vault의 이름으로 바꿉니다.

    ```json
    {
        "type": "Microsoft.KeyVault/vaults/secrets",
        "apiVersion": "2016-10-01",
        "name": "VAULT_NAME/azurefilesconnectionstring",
        "properties": {
            "value": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2019-06-01').keys[0].value,';EndpointSuffix=','core.windows.net')]"
        },
        "dependsOn": [
            "[concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName'))]"
        ]
    },
    ``` 
    
1. 함수 앱 리소스에 대한 정의에서(`type`이 `Microsoft.Web/sites`로 설정됨) `Microsoft.KeyVault/vaults/VAULT_NAME/secrets/azurefilesconnectionstring`을 `dependsOn` 배열에 추가합니다. 다시 "VAULT_NAME"을 Key Vault의 이름으로 바꿉니다. 이렇게 하면 비밀을 정의해야만 앱이 만들어집니다. `dependsOn` 배열은 다음 예제와 같습니다.

    ```json
        {
            "type": "Microsoft.Web/sites",
            "apiVersion": "2018-11-01",
            "name": "[parameters('name')]",
            "location": "[parameters('location')]",
            "tags": null,
            "dependsOn": [
                "microsoft.insights/components/idcxntut",
                "Microsoft.KeyVault/vaults/VAULT_NAME/secrets/azurefilesconnectionstring",
                "[concat('Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "[concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName'))]"
            ],
            // ...
        }
    ```

1. 다음 예제의 `identity` 블록을 함수 앱 리소스에 대한 정의에 추가합니다. "IDENTITY_RESOURCE_ID"를 사용자 할당 ID의 리소스 ID로 대체합니다.

    ```json
    {
        "apiVersion": "2018-11-01",
        "name": "[parameters('name')]",
        "type": "Microsoft.Web/sites",
        "kind": "functionapp",
        "location": "[parameters('location')]",
        "identity": {
            "type": "SystemAssigned,UserAssigned",
            "userAssignedIdentities": {
                "IDENTITY_RESOURCE_ID": {}
            }
        },
        "tags": null,
        // ...
    }
    ```

    이 `identity` 블록은 이 자습서의 후반부에서 사용할 시스템 할당 ID도 설정합니다.

1. 아래 예제와 같이 함수 앱의 `properties` 개체에 `keyVaultReferenceIdentity` 속성을 추가합니다. "IDENTITY_RESOURCE_ID"를 사용자 할당 ID의 리소스 ID로 대체합니다.

    ```json
    {
        // ...
         "properties": {
                "name": "[parameters('name')]",
                "keyVaultReferenceIdentity": "IDENTITY_RESOURCE_ID",
                // ...
         }
    }
    ```

    앱에 여러 사용자 할당 ID가 구성되었을 수 있으므로 이 구성이 필요합니다. 사용자 할당 ID를 사용하려는 경우 ID를 통해 지정해야 합니다. 앱에는 ID가 하나만 있기 때문에 시스템 할당 ID에는 해당되지 않습니다. 관리 ID를 사용하는 많은 기능은 기본적으로 시스템 할당 ID를 사용해야 한다고 가정합니다.

1. 이제 다음 예제와 같이 `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` 애플리케이션 설정을 정의하는 JSON 개체를 찾습니다.

    ```json
    {
        "name": "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING",
        "value": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('storageAccountName')), '2019-06-01').keys[0].value,';EndpointSuffix=','core.windows.net')]"
    },
    ```

1. 다음 예제와 같이 `value` 필드를 비밀에 대한 참조로 바꿉니다. "VAULT_NAME"을 Key Vault의 이름으로 바꿉니다.

    ```json
    {
        "name": "WEBSITE_CONTENTAZUREFILECONNECTIONSTRING",
        "value": "[concat('@Microsoft.KeyVault(SecretUri=', reference(resourceId('Microsoft.KeyVault/vaults/secrets', 'VAULT_NAME', 'azurefilesconnectionstring')).secretUri, ')')]"
    },
    ```

1. **저장** 을 선택하여 업데이트된 ARM 템플릿을 저장합니다.

### <a name="deploy-the-modified-template"></a>수정된 템플릿 배포

1. **리소스 그룹** 을 비롯한 만들기 옵션이 여전히 올바른지 확인하고 **검토 + 만들기** 를 선택합니다.

1. 템플릿의 유효성을 검사한 후 **스토리지 계정 이름** 을 기록해 둡니다. 나중에 이 계정을 사용할 수 있습니다. 마지막으로 **만들기** 를 선택하여 Azure 리소스를 만들고 함수 앱에 코드를 배포합니다. 

1. 배포가 완료되면 **리소스 그룹으로 이동** 을 선택한 다음, 새 함수 앱을 선택합니다. 

축하합니다! Azure Key Vault에서 Azure Files 연결 문자열을 참조하는 함수 앱을 성공적으로 만들었습니다.

앱에서 비밀에 대한 참조를 추가해야 할 때마다 Key Vault에 저장된 값을 가리키는 새 애플리케이션 설정을 정의하기만 하면 됩니다. [Azure Functions에 대한 Key Vault 참조](../app-service/app-service-key-vault-references.md?toc=%2Fazure%2Fazure-functions%2Ftoc.json)에서 이에 대해 자세히 알아볼 수 있습니다.

> [!TIP]
> [Application Insights 연결 문자열](../azure-monitor/app/sdk-connection-string.md) 및 포함된 계측 키는 비밀로 간주되지 않으며 [읽기 권한자](../role-based-access-control/built-in-roles.md#reader) 권한을 사용하여 App Insights에서 검색할 수 있습니다. 확실히 가능하지만 Key Vault로 이동할 필요는 없습니다.

## <a name="use-managed-identity-for-azurewebjobsstorage-preview"></a>AzureWebJobsStorage에 관리 ID 사용(미리 보기)

다음으로 `AzureWebJobsStorage` 연결에 대해 이전 단계에서 구성한 시스템 할당 ID를 사용합니다. `AzureWebJobsStorage`는 함수 런타임과 여러 트리거 및 바인딩에서 실행 중인 여러 인스턴스 간에 조정하는 데 사용됩니다. 함수 앱이 작동해야 하며, Azure Files와 마찬가지로 새 함수 앱을 만들 때 기본적으로 연결 문자열로 구성됩니다.

### <a name="grant-the-system-assigned-identity-access-to-the-storage-account"></a>스토리지 계정에 시스템 할당 ID 액세스 권한 부여

이전에 사용자 할당 ID 및 Key Vault를 사용하여 수행한 단계와 마찬가지로, 이제 스토리지 계정에 시스템 할당 ID 액세스 권한을 부여하는 역할 할당을 만듭니다.

1. [Azure Portal](https://portal.azure.com)에서 이전에 함수 앱을 사용하여 만든 스토리지 계정으로 이동합니다.

1. **Access Control(IAM)** 을 선택합니다. 여기에서 리소스에 대한 액세스 권한이 있는 사용자를 보고 구성할 수 있습니다.

1. **추가** 를 클릭하고 **역할 할당 추가** 를 선택합니다.

1. **Storage Blob 데이터 소유자** 를 검색한 후 선택하고 **다음** 을 클릭합니다.

1. **구성원** 탭의 **액세스 권한 할당 대상** 아래에서 **관리 ID** 를 선택합니다.

1. **구성원 선택** 을 클릭하여 **관리 ID 선택** 패널을 엽니다.

1. **구독** 이 이전에 리소스를 만든 구독인지 확인합니다.

1. **관리 ID** 선택기에서 **시스템 할당 관리 ID** 범주에서 **함수 앱** 을 선택합니다. "함수 앱" 레이블의 옆에는 사용자 할당 ID가 있는 구독의 앱 수를 나타내는 괄호로 묶은 숫자가 표시될 수 있습니다.

1. 앱이 입력 필드 아래의 목록에 표시됩니다. 표시되지 않으면 **선택** 상자를 사용하여 앱 이름으로 결과를 필터링할 수 있습니다.

1. 애플리케이션을 클릭합니다. **선택한 구성원** 섹션 아래로 이동됩니다. **선택** 을 클릭합니다.

1. **역할 할당 추가** 화면으로 돌아가서 **검토 + 할당** 을 클릭합니다. 구성을 검토하고 **검토 + 할당** 을 클릭합니다.

### <a name="edit-the-azurewebjobsstorage-configuration"></a>AzureWebJobsStorage 구성 편집

그런 다음, 호스트 스토리지에 Blob service를 사용하는 경우 해당 시스템 할당 ID를 사용하도록 함수 앱을 업데이트합니다.

> [!IMPORTANT]
> `AzureWebJobsStorage` 구성은 일부 트리거 및 바인딩에서 사용되며 해당 확장에서도 ID 기반 연결을 사용할 수 있어야 합니다. Blob 트리거 또는 이벤트 허브 트리거를 사용하는 앱은 이러한 확장을 업데이트해야 할 수 있습니다. 이 앱에 대해 정의된 함수가 없으므로 아직 문제는 없습니다. 이 요구 사항에 대해 자세히 알아보려면 [ID를 사용하여 호스트 스토리지에 연결(미리 보기)](./functions-reference.md#connecting-to-host-storage-with-an-identity-preview)을 참조하세요.
>
> 마찬가지로 `AzureWebJobsStorage`는 Linux 사용판에서 서버 쪽 빌드를 사용하는 경우 배포 아티팩트에 사용됩니다. Linux 사용판에서 `AzureWebJobsStorage`에 대해 ID 기반 연결을 사용하도록 설정하는 경우 [외부 배포 패키지](run-functions-from-deployment-package.md)를 통해 배포해야 합니다.

1. [Azure Portal](https://portal.azure.com)에서 함수 앱으로 이동합니다.

1. **설정** 에서 **구성** 을 선택합니다.

1. **AzureWebJobsStorage** 애플리케이션 설정 옆의 **편집** 단추를 선택하고 다음 값을 기준으로 변경합니다.

    | 옵션      | 제안 값  | Description |
    | ------------ | ---------------- | ----------- |
    | **이름** |  AzureWebJobsStorage__accountName | **AzureWebJobsStorage** 의 이름을 정확한 이름 `AzureWebJobsStorage__accountName`으로 업데이트합니다. 이 설정은 호스트에 저장된 암호를 검색하지 말고 ID를 사용하도록 지시합니다. 새 설정에는 애플리케이션 설정에서 특수 문자에 해당하는 이중 밑줄(`__`)이 사용됩니다.  |
    | **값** | 사용자의 계정 이름 | 연결 문자열의 이름을 **AccountName** 으로만 업데이트합니다. |

    이 구성을 사용하면 시스템에서 리소스에 연결하는 데 ID를 사용해야 함을 알 수 있습니다.

1. **확인** 을 선택하고 **저장** > **계속** 을 선택하여 변경 내용을 저장합니다. 

대신 관리 ID를 사용하여 blob에 연결하도록 앱을 구성하여 AzureWebJobsStorage에 대한 스토리지 연결 문자열 요구 사항을 제거했습니다.  

## <a name="next-steps"></a>다음 단계 

이 자습서에서는 암호를 구성에 저장하지 않고 함수 앱을 만드는 방법을 살펴보았습니다.

다음 자습서에서는 트리거 및 바인딩 연결에서 ID를 사용하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [암호 대신 트리거와 바인딩을 사용하여 ID 기반 연결을 사용]

[암호 대신 트리거와 바인딩을 사용하여 ID 기반 연결을 사용]: ./functions-identity-based-connections-tutorial-2.md
