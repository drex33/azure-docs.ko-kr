---
title: Azure Portal에서 계정 만들기
description: 클라우드에서 대규모 병렬 작업을 실행하도록 Azure Portal에서 Azure Batch 계정을 만드는 방법에 대해 알아봅니다.
ms.topic: how-to
ms.date: 07/01/2021
ms.custom: subject-rbac-steps
ms.openlocfilehash: 745a2fc2e1279d05fa380b7bc053171d20a3fa4c
ms.sourcegitcommit: 6bd31ec35ac44d79debfe98a3ef32fb3522e3934
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2021
ms.locfileid: "113213276"
---
# <a name="create-a-batch-account-with-the-azure-portal"></a>Azure Portal에서 Batch 계정 만들기

[Azure Portal](https://portal.azure.com)에 [Azure Batch 계정](accounts.md)을 만들고 컴퓨팅 시나리오에 적합한 계정 속성을 선택하는 방법에 대하여 알아봅니다. 액세스 키 및 계정 URL과 같은 중요한 계정 속성을 찾는 위치에 대하여서도 알아봅니다.

Batch 계정 및 시나리오에 대한 자세한 내용은 [Batch 서비스 워크플로 및 리소스](batch-service-workflow-features.md)를 참조하세요.

## <a name="create-a-batch-account"></a>Batch 계정 만들기

[!INCLUDE [batch-account-mode-include](../../includes/batch-account-mode-include.md)]

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 홈페이지에서 **리소스 만들기** 를 선택합니다.

1. 검색 상자에 **Batch 서비스** 를 입력합니다. 결과에서 **Batch 서비스** 를 선택한 다음, **만들기** 를 선택합니다.

1. 다음 세부 정보를 입력합니다.

    :::image type="content" source="media/batch-account-create-portal/batch-account-portal.png" alt-text="새 배치 계정 화면의 스크린샷":::

    a. **구독**: Batch 계정을 만들 구독입니다. 하나의 구독만 보유하는 경우 기본적으로 선택됩니다.

    b. **리소스 그룹**: 새 Batch 계정에 대한 기존 리소스 그룹을 선택하거나 필요에 따라 새 리소스 그룹을 만듭니다.

    다. **계정 이름**: 선택한 이름은 계정을 만든 Azure 지역 내에서 고유해야 합니다(아래 **위치** 참조). 계정 이름은 소문자 또는 숫자만 포함할 수 있으며 길이는 3-24자여야 합니다.

    d. **위치**: Batch 계정을 만들 Azure 지역입니다. 구독 및 리소스 그룹에서 지원하는 지역만 옵션으로 표시됩니다.

    e. **스토리지 계정**: Batch 계정과 연결하는 [Azure Storage 계정](accounts.md#azure-storage-accounts)(선택 사항)입니다. 기존 스토리지 계정을 선택하거나 새 스토리지 계정을 만듭니다. 최상의 성능을 위해 범용 v2 스토리지 계정이 권장됩니다.

    :::image type="content" source="media/batch-account-create-portal/storage_account.png" alt-text="스토리지 계정 만들기 옵션 스크린샷":::

1. 원하는 경우, **고급** 을 선택하여 **ID 유형**, **퍼블릭 네트워크 액세스** 또는 **풀 할당 모드** 를 지정합니다. 대부분의 시나리오에서는 기본 옵션 정도면 괜찮습니다.

1. 계정을 만들려면 **검토 + 만들기** 를 선택한 다음, **만들기** 를 선택합니다.

## <a name="view-batch-account-properties"></a>Batch 계정 속성 보기

계정이 만들어지면 계정을 선택하여 해당 설정 및 속성에 액세스합니다. 왼쪽 메뉴를 사용하여 모든 계정 설정 및 속성에 액세스할 수 있습니다.

> [!NOTE]
> Batch 계정의 이름은 해당 ID이며 변경할 수 없습니다. Batch 계정의 이름을 변경해야 하는 경우 계정을 삭제하고 원하는 이름으로 새 계정을 만들어야 합니다.

:::image type="content" source="media/batch-account-create-portal/batch_blade.png" alt-text="Azure Portal의 배치 계정 페이지 스크린샷":::

[Batch API](batch-apis-tools.md#azure-accounts-for-batch-development)를 사용하여 애플리케이션을 개발하는 경우 Batch 리소스에 액세스하려면 계정 URL 및 키가 필요합니다. (Batch는 Azure Active Directory 인증도 지원합니다.) 배치 계정 액세스 정보를 조회하려면 **키** 를 선택합니다.

:::image type="content" source="media/batch-account-create-portal/batch-account-keys.png" alt-text="Azure Portal의 배치 계정 키 스크린샷":::

배치 계정에 연결된 스토리지 계정의 이름 및 키를 보려면 **Storage 계정** 을 선택합니다.

배치 계정에 적용되는 [리소스 할당량](batch-quota-limit.md)을 보려면 **할당량** 을 선택합니다.

## <a name="additional-configuration-for-user-subscription-mode"></a>사용자 구독 모드에 대한 추가 구성

사용자 구독 모드에서 배치 계정 만들기를 선택하면 계정을 만들기 전에 다음과 같은 추가 단계를 수행합니다.

> [!IMPORTANT]
> 사용자 구독 모드에서 배치 계정을 만드는 사용자는 해당 배치 계정을 만들어 넣을 구독에 대한 Contributor 또는 소유자 역할을 할당받아야 합니다.

### <a name="allow-azure-batch-to-access-the-subscription-one-time-operation"></a>Azure Batch에서 구독에 액세스하도록 허용(일회성 작업)

사용자 구독 모드에서 첫 번째 Batch 계정을 만들 때 구독을 Batch 계정에 등록합니다. (이 작업을 이미 수행한 경우 다음 섹션으로 건너뜁니다.)

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. **모든 서비스** > **구독** 을 선택하고, 배치 계정에 사용할 구독을 선택합니다.

1. **구독** 페이지에서 **리소스 공급자** 를 선택하고, **Microsoft.Batch** 를 검색합니다. **Microsoft.Batch** 리소스 공급자가 구독에 등록되었는지 확인합니다. 그렇지 않은 경우, 화면 상단 근처의 **등록** 링크를 선택합니다.

    :::image type="content" source="media/batch-account-create-portal/register_provider.png" alt-text="Microsoft.Batch 리소스 공급자를 보여 주는 스크린샷":::

1. **구독** 페이지에서 **액세스 제어(IAM)** 를 선택합니다.

1. Batch API에 **기여자** 또는 **소유자** 역할을 할당합니다. **Microsoft Azure Batch** 또는 **MicrosoftAzureBatch** 를 검색하여 이 계정을 찾을 수 있습니다. (Batch API의 개체 ID는 **f520d84c-3fd3-4cc8-88d4-2ed25b00d27a** 이고 애플리케이션 ID는 **ddbf3205-c6bd-46ae-8127-60eb93363864** 입니다.)

   세부 단계에 대해서는 [Azure Portal을 사용하여 Azure 역할 할당](../role-based-access-control/role-assignments-portal.md)을 참조하세요.

### <a name="create-a-key-vault"></a>주요 자격 증명 모음 만들기

사용자 구독 모드에는 [Azure Key Vault](../key-vault/general/overview.md)가 필요합니다. Key Vault는 반드시 배치 계정을 만들 구독 및 지역과 동일한 곳에 있어야 합니다.

1. [Azure Portal](https://portal.azure.com) 홈페이지에서 **리소스 만들기** 를 선택합니다.
1. 검색 상자에 **Key Vault** 를 입력합니다. 결과에서 **Key Vault** 를 선택한 다음, **만들기** 를 선택합니다.
1. **키 자격 증명 모음 만들기** 페이지에 해당 키 자격 증명 모음의 이름을 입력하고 배치 계정을 만들려는 지역에 새 리소스 그룹을 만듭니다. 나머지 설정은 기본값으로 그대로 두고 **만들기** 를 선택합니다.

사용자 구독 모드에서 배치 계정을 만들 경우, **사용자 구독** 을 풀 할당 모드로 지정하고, 만든 Key Vault를 선택한 다음, Azure Batch에서 해당 Key Vault에 액세스할 수 있도록 확인란을 선택합니다.

Key Vault에 대한 액세스를 수동으로 허용하려면, 해당 Key Vault의 **액세스 정책** 섹션으로 이동하여 **액세스 정책 추가** 를 선택합니다. **보안 주체 선택** 옆의 링크를 선택하고 **Microsoft Azure Batch** (애플리케이션 ID **ddbf3205-c6bd-46ae-8127-60eb93363864**)를 검색합니다. 해당 보안 주체를 선택한 다음 드롭다운 메뉴를 사용하여 **비밀 권한** 을 구성합니다. Azure Batch에 최소 **가져오기**, **나열**, **설정**, **삭제** 사용 권한을 지정해야 합니다. [일시 삭제를 사용하도록 설정한 Key Vault](../key-vault/general/soft-delete-overview.md)의 경우, Azure Batch가 **복구** 권한도 가지고 있어야 합니다.

:::image type="content" source="media/batch-account-create-portal/secret-permissions.png" alt-text="Azure Batch의 비밀 권한 선택 스크린샷":::

**추가** 를 선택한 다음, **배포용 Azure Virtual Machines** 및 **템플릿 배포용 Azure Resource Manager** 확인란이 연결된 **Key Vault** 리소스에 대하여 선택되어 있는지 확인합니다. 변경 내용을 적용하려면 **저장** 을 선택합니다.

:::image type="content" source="media/batch-account-create-portal/key-vault-access-policy.png" alt-text="액세스 정책 화면의 스크린샷":::

### <a name="configure-subscription-quotas"></a>구독 할당량 구성

사용자 구독 배치 계정의 경우, [코어 할당량](batch-quota-limit.md)은 반드시 수동으로 설정하여야 합니다. 표준 배치 코어 할당량은 사용자 구독 모드의 계정에 적용되지 않으며 지역 컴퓨팅 코어, 시리즈별 컴퓨팅 코어 및 기타 리소스에 대한 [구독 내 할당량](../azure-resource-manager/management/azure-subscription-service-limits.md)이 사용 및 적용됩니다.

1. [Azure Portal](https://portal.azure.com)에서 사용자 구독 모드 Batch 계정을 선택하여 해당 설정과 속성을 표시합니다.
1. 왼쪽 메뉴에서 **할당량** 을 선택하여 배치 계정과 연결된 코어 할당량을 보고 구성합니다.

## <a name="other-batch-account-management-options"></a>다른 Batch 계정 관리 옵션

Azure Portal을 사용하는 것 외에도 다음을 포함하는 도구로 Batch 계정을 만들고 관리할 수 있습니다.

- [Batch PowerShell cmdlets](batch-powershell-cmdlets-get-started.md)
- [Azure CLI](batch-cli-get-started.md)
- [Batch 관리 .NET](batch-management-dotnet.md)

## <a name="next-steps"></a>다음 단계

- 풀, 노드, 작업 및 태스크와 같은 [Batch 서비스 워크플로 및 기본 리소스](batch-service-workflow-features.md)에 대해 알아봅니다.
- [Batch .NET 클라이언트 라이브러리](quick-run-dotnet.md) 또는 [Python](quick-run-python.md)을 사용하여 Batch 지원 애플리케이션 개발에 대한 기본 사항을 알아봅니다. 이러한 빠른 시작에서는 Batch 서비스를 사용하여 여러 컴퓨팅 노드에서 워크로드를 실행하는 애플리케이션 예제를 단계별로 안내하며, Azure Storage를 사용하여 워크로드 파일을 준비하고 검색하는 방법을 설명합니다.