---
title: Azure Portal을 사용하여 리소스 배포
description: Azure Portal 및 Azure Resource Manage를 사용하여 구독의 리소스 그룹에 리소스를 배포합니다.
ms.topic: conceptual
ms.date: 05/05/2021
ms.openlocfilehash: 03b1cda06bf6865dda1b8d40096f6b76588d8184
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124762192"
---
# <a name="deploy-resources-with-arm-templates-and-azure-portal"></a>ARM 템플릿 및 Azure Portal을 사용하여 리소스 배포

[ARM 템플릿(Azure Resource Manager 템플릿)](overview.md)과 함께 [Azure Portal](https://portal.azure.com)을 사용하여 Azure 리소스를 배포하는 방법을 알아봅니다. 리소스 관리에 대한 자세한 내용은 [Azure Portal을 사용한 Azure 리소스 관리](../management/manage-resources-portal.md)를 참조하세요.

Azure Portal 사용하여 Azure 리소스를 배포하려면 일반적으로 다음 두 단계가 필요합니다.

- 리소스 그룹을 생성합니다.
- 리소스 그룹에 리소스 배포

또한 사용자 지정된 ARM 템플릿을 만들어 Azure 리소스를 배포할 수 있습니다.

이 문서에서는 두 가지 방법을 모두 보여줍니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

1. 새 리소스 그룹을 만들려면 [Azure Portal](https://portal.azure.com)에서 **리소스 그룹** 을 선택합니다.

   ![리소스 그룹 선택](./media/deploy-portal/select-resource-groups.png)

1. 리소스 그룹에서 **추가** 를 선택합니다.

   ![리소스 그룹 추가](./media/deploy-portal/add-resource-group.png)

1. 다음 속성 값을 선택하거나 입력합니다.

    - **구독**: Azure 구독을 선택합니다.
    - **리소스 그룹**: 리소스 그룹에 이름을 지정합니다.
    - **Azure 지역**: Azure 위치를 지정합니다. 이 위치는 리소스 그룹이 리소스에 대한 메타데이터를 저장하는 위치입니다. 규정 준수 때문에 메타데이터를 저장할 위치를 지정하려고 합니다. 일반적으로 대부분의 리소스가 있을 위치를 지정하는 것이 좋습니다. 동일한 위치를 사용하여 템플릿을 간소화할 수 있습니다.

   ![그룹값 설정](./media/deploy-portal/set-group-properties.png)

1. **검토 + 만들기** 를 선택합니다.
1. 값을 검토한 다음, **만들기** 를 선택합니다.
1. 목록에서 새 리소스 그룹을 보려면 **새로 고침** 을 선택합니다.

## <a name="deploy-resources-to-a-resource-group"></a>리소스 그룹에 리소스 배포

리소스 그룹을 만들면 Marketplace에서 그룹으로 리소스를 배포할 수 있습니다. Marketplace는 일반적인 시나리오에 사전 정의된 솔루션을 제공합니다.

1. 배포를 시작하려면 [Azure Portal](https://portal.azure.com)에서 **리소스 만들기** 를 선택합니다.

   ![새 리소스](./media/deploy-portal/new-resources.png)

1. 배포할 리소스의 유형을 찾습니다. 리소스는 범주로 구성됩니다. 배포하려는 특정 솔루션이 보이지 않는다면 Marketplace에서 이를 검색할 수 있습니다. 다음 스크린샷은 Ubuntu Server가 선택되어 있음을 보여줍니다.

   ![리소스 종류 선택](./media/deploy-portal/select-resource-type.png)

1. 선택한 리소스 유형에 따라 배포하기 전에 관련 속성의 컬렉션을 설정합니다. 모든 유형에 대해 대상 리소스 그룹을 선택해야 합니다. 다음 이미지는 Linux 가상 머신을 만들고 앞에서 만든 리소스 그룹에 배포하는 방법을 보여 줍니다.

   ![리소스 그룹 만들기](./media/deploy-portal/select-existing-group.png)

   리소스를 배포할 때 리소스 그룹을 만들도록 결정할 수 있습니다. **새로 만들기** 를 선택하고 리소스 그룹에 이름을 지정합니다.

1. 배포가 시작됩니다. 배포에는 몇 분 정도 걸릴 수 있습니다. 일부 리소스는 다른 리소스보다 시간이 더 오래 걸립니다. 배포가 완료되면 알림이 표시됩니다. **리소스 그룹으로 이동** 을 선택하여 열기

   ![알림 보기](./media/deploy-portal/view-notification.png)

1. 리소스를 배포한 후에 **추가** 를 선택하여 더 많은 리소스를 리소스 그룹에 추가할 수 있습니다.

   ![리소스 추가](./media/deploy-portal/add-resource.png)

표시되지 않았지만 포털에서 ARM 템플릿을 사용하여 선택한 리소스를 배포했습니다. 배포 기록에서 템플릿을 찾을 수 있습니다. 자세한 내용은 [배포 후 템플릿 내보내기](export-template-portal.md#export-template-after-deployment)를 참조하세요.

## <a name="deploy-resources-from-custom-template"></a>사용자 지정 템플릿에서 배포

배포를 실행하려고 하지만 Marketplace에서 템플릿 중 하나를 사용하지 않으려는 경우 솔루션에 대한 인프라를 정의하는 사용자 지정된 템플릿을 만들 수 있습니다. 템플릿을 만드는 방법에 대한 자세한 내용은 [ARM 템플릿의 구조 및 구문 이해](./syntax.md)를 참조하세요.

> [!NOTE]
> 포털 인터페이스는 [Key Vault에서 비밀](key-vault-parameter.md) 참조를 지원하지 않습니다. 대신 [PowerShell](deploy-powershell.md) 또는 [Azure CLI](deploy-cli.md)를 사용하여 외부 URI에서 또는 로컬로 템플릿을 배포합니다.

1. 포털을 통해 사용자 지정된 템플릿을 배포하려면 **리소스 만들기** 를 선택하고 **템플릿** 을 검색합니다. 그런 다음 **템플릿 배포** 를 선택합니다.

   ![템플릿 배포 검색](./media/deploy-portal/search-template.png)

1. **만들기** 를 선택합니다.
1. 템플릿을 만들기 위한 몇 가지 옵션이 표시됩니다.

    - **편집기에서 사용자 템플릿 빌드**: 포털 템플릿 편집기에서 사용자 템플릿을 만듭니다.
    - **일반 템플릿**: 일반 솔루션에서 선택합니다.
    - **GitHub 빠른 시작 템플릿 로드**: [빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/)에서 선택합니다.

   ![옵션 보기](./media/deploy-portal/see-options.png)

    이 자습서에서는 빠른 시작 템플릿을 로드하기 위한 지침을 제공합니다.

1. **GitHub 빠른 시작 템플릿 로드** 에서 **storage-account-create** 를 입력하거나 선택합니다.

    다음 두 가지 옵션을 사용할 수 있습니다.

    - **템플릿 선택**: 템플릿을 배포합니다.
    - **템플릿 편집**: 빠른 시작 템플릿을 배포하기 전에 편집합니다.

1. 포털 템플릿 편집기를 살펴보려면 **템플릿 편집** 을 선택합니다. 템플릿이 편집기에 로드됩니다. `storageAccountType` 및 `location`의 두 가지 매개 변수가 사용됩니다.

   ![템플릿 만들기](./media/deploy-portal/show-json.png)

1. 템플릿에 사소한 변경을 수행합니다. 예를 들어, `storageAccountName` 변수를 다음으로 업데이트합니다.

    ```json
    "storageAccountName": "[concat('azstore', uniquestring(resourceGroup().id))]"
    ```

1. **저장** 을 선택합니다. 이제 포털 템플릿 배포 인터페이스가 표시됩니다. 템플릿에 정의된 두 개의 매개 변수를 확인합니다.
1. 다음 속성 값을 입력하거나 선택합니다.

    - **구독**: Azure 구독을 선택합니다.
    - **리소스 그룹**: **새로 만들기** 를 선택하고 리소스 그룹에 이름을 지정합니다.
    - **위치**: Azure 위치를 선택합니다.
    - **스토리지 계정 형식**: 기본값을 사용합니다. 템플릿에 정의된 카멜식 대/소문자 매개 변수 이름 *storageAccountType* 은 포털에 표시될 때 공백으로 구분된 문자열로 바뀝니다.
    - **위치**: 기본값을 사용합니다.
    - **위에 명시된 사용 약관에 동의함**: (선택)

1. **구매** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

- 배포 오류를 해결하려면 [배포 작업 보기](deployment-history.md)를 참조하세요.
- 배포 또는 리소스 그룹에서 템플릿을 내보내려면 [ARM 템플릿 내보내기](export-template-portal.md)를 참조하세요.