---
title: '빠른 시작: 포털에서 청사진 만들기'
description: 이 빠른 시작에서는 Azure Blueprints를 사용하여 Azure Portal을 통해 아티팩트를 만들고 정의하고 배포합니다.
ms.date: 08/17/2021
ms.topic: quickstart
ms.custom: mode-portal
ms.openlocfilehash: 3968504b23a5ee936d373594bde0db2eee9a4944
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132937489"
---
# <a name="quickstart-define-and-assign-a-blueprint-in-the-portal"></a>포털에서 청사진 정의 및 할당

청사진을 만들고 할당하는 방법을 학습하면 공통 패턴 정의를 통해 ARM 템플릿(Azure Resource Manager 템플릿), 정책, 보안 등을 기반으로 재사용이 가능하고 신속하게 배포할 수 있는 구성을 정의할 수 있습니다. 이 자습서에서는 Azure Blueprints를 사용하여 조직 내에서 청사진을 작성, 게시 및 할당하는 것과 관련된 일반적인 작업을 수행하는 방법을 알아봅니다. 이러한 태스크는 다음과 같습니다.

## <a name="prerequisites"></a>사전 요구 사항

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free)을 만듭니다.

## <a name="create-a-blueprint"></a>청사진 만들기

규정 준수를 위한 표준 패턴을 정의하는 첫 단계는 사용 가능한 리소스로 청사진을 작성하는 것입니다. 이 예제에서는 **MyBlueprint** 라는 새 청사진을 만들어 구독의 역할 및 정책 할당을 구성합니다. 그런 다음, 새 리소스 그룹을 추가하고 새 리소스 그룹에 대한 Resource Manager 템플릿 및 역할을 만듭니다.

1. 왼쪽 창에서 **모든 서비스** 를 선택합니다. **청사진** 을 검색하고 선택합니다.

1. 왼쪽 페이지에서 **청사진 정의** 를 선택하고 페이지 위쪽에 있는 **+ 청사진 만들기** 단추를 선택합니다.

   또는 **시작** 페이지에서 **만들기** 를 선택하여 청사진 만들기로 즉시 이동할 수도 있습니다.

   :::image type="content" source="./media/create-blueprint-portal/create-blueprint-button.png" alt-text="청사진 정의 페이지의 '청사진 만들기' 단추 스크린샷" border="false":::

1. 기본 제공 청사진 목록의 맨 위에 있는 카드에서 **빈 청사진으로 시작** 을 선택합니다.

1. **MyBlueprint** 와 같은 **청사진 이름** 을 제공합니다. (최대 48개의 문자 및 숫자를 사용하지만 공백 또는 특수 문자를 포함하지 않습니다.) **청사진 설명** 은 지금은 비워 둡니다.

1. **정의 위치** 상자에서 오른쪽의 줄임표를 클릭하고 청사진을 저장할 [관리 그룹](../management-groups/overview.md) 또는 구독을 선택한 후에 **선택** 을 선택합니다.

1. 정보가 올바른지 확인합니다. **청사진 이름** 및 **정의 위치** 필드는 나중에 변경할 수 없습니다. 페이지 아래쪽의 **다음: 아티팩트** 또는 지 위쪽의 **아티팩트** 탭을 클릭합니다.

1. 구독 수준에서 역할 할당 추가:

   1. **구독** 아래의 **+ 아티팩트 추가** 행을 선택합니다. 브라우저의 오른쪽에 **아티팩트 추가** 창이 열립니다.

   1. **아티팩트 형식** 으로 **역할 할당** 을 선택합니다.

   1. **역할** 아래에서 **기여자** 를 선택합니다. 동적 매개 변수를 나타내는 확인란이 있는 **사용자, 앱 또는 그룹** 상자를 비워 둡니다.

   1. **추가** 를 선택하여 이 아티팩트를 청사진에 추가합니다.

   :::image type="content" source="./media/create-blueprint-portal/add-role-assignment.png" alt-text="청사진 정의에 추가할 역할 할당 아티팩트 옵션 스크린샷" border="false":::

   > [!NOTE]
   > 대부분의 아티팩트는 매개 변수를 지원합니다. 청사진 생성 중에 값이 할당되는 매개 변수는 _정적 매개 변수_ 입니다. 청사진 할당 중에 할당되는 매개 변수는 _동적 매개 변수_ 입니다. 자세한 내용은 [청사진 매개 변수](./concepts/parameters.md)를 참조하세요.

1. 구독 수준에서 정책 할당 추가:

   1. 역할 할당 아티팩트 아래의 **+ 아티팩트 추가** 행을 선택합니다.

   1. **아티팩트 형식** 으로 **정책 할당** 을 선택합니다.

   1. **형식** 을 **기본 제공** 으로 변경합니다. **검색** 에 **태그** 를 입력합니다.

   1. 필터링이 발생하도록 **검색** 에서 포커스를 변경합니다. **리소스 그룹에 태그 및 해당 값 추가** 를 선택합니다.

   1. **추가** 를 선택하여 이 아티팩트를 청사진에 추가합니다.

1. 정책 할당 **리소스 그룹에 태그 및 값 추가** 의 행을 선택합니다.

1. 청사진 정의의 일부분으로 아티팩트에 매개 변수를 제공하는 창이 열리며, 이 창에서 할당 중에 매개 변수를 설정하는 대신(동적 매개 변수) 해당 청사진을 기준으로 하여 모든 할당의 매개 변수를 설정할 수 있습니다(정적 매개 변수). 이 예제에서는 청사진 할당 중에 동적 매개 변수를 사용하므로 기본값을 유지하고 **취소** 를 선택합니다.

1. 구독 수준에서 리소스 그룹 추가:

   1. **구독** 아래의 **+ 아티팩트 추가** 행을 선택합니다.

   1. **아티팩트 형식** 으로 **리소스 그룹** 을 선택합니다.

   1. **아티팩트 표시 이름**, **리소스 그룹 이름** 및 **위치** 상자는 비워 두되, 각 매개 변수 속성의 확인란을 선택하여 해당 속성을 동적 매개 변수로 설정합니다.

   1. **추가** 를 선택하여 이 아티팩트를 청사진에 추가합니다.

1. 리소스 그룹 아래에 템플릿 추가:

   1. **ResourceGroup** 항목 아래의 **+ 아티팩트 추가** 행을 선택합니다.

   1. **아티팩트 형식** 으로 **Azure Resource Manager 템플릿** 을 선택하고, **아티팩트 표시 이름** 을 **StorageAccount** 로 설정하고, **설명** 은 비워 둡니다.

   1. 편집기 상자의 **템플릿** 탭에 다음 ARM 템플릿을 붙여넣습니다. 템플릿을 붙여넣은 후, **매개 변수** 탭을 선택하면 템플릿 매개 변수 **storageAccountType** 및 **location** 이 검색됩니다. 각 매개 변수는 자동으로 검색되고 채워지지만 동적 매개 변수로 구성됩니다.

      > [!IMPORTANT]
      > 템플릿을 가져오는 경우에는 파일이 JSON으로만 되어 있으며 HTML은 포함하지 않는지 확인하세요. Github의 URL을 가리키는 경우에는 **RAW** 를 선택하여 GitHub에 표시하기 위해 HTML로 래핑된 파일이 아니라 순수 JSON 파일을 가져왔는지 확인해야 합니다. 가져온 템플릿이 순수 JSON이 아니면 오류가 발생합니다.

      ```json
      {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
              "storageAccountType": {
                  "type": "string",
                  "defaultValue": "Standard_LRS",
                  "allowedValues": [
                      "Standard_LRS",
                      "Standard_GRS",
                      "Standard_ZRS",
                      "Premium_LRS"
                  ],
                  "metadata": {
                      "description": "Storage Account type"
                  }
              },
              "location": {
                  "type": "string",
                  "defaultValue": "[resourceGroup().location]",
                  "metadata": {
                      "description": "Location for all resources."
                  }
              }
          },
          "variables": {
              "storageAccountName": "[concat('store', uniquestring(resourceGroup().id))]"
          },
          "resources": [{
              "type": "Microsoft.Storage/storageAccounts",
              "name": "[variables('storageAccountName')]",
              "location": "[parameters('location')]",
              "apiVersion": "2018-07-01",
              "sku": {
                  "name": "[parameters('storageAccountType')]"
              },
              "kind": "StorageV2",
              "properties": {}
          }],
          "outputs": {
              "storageAccountName": {
                  "type": "string",
                  "value": "[variables('storageAccountName')]"
              }
          }
      }
      ```

   1. **storageAccountType** 확인란의 선택을 취소하면 드롭다운 목록에는 **allowedValues** 아래의 ARM 템플릿에 포함된 값만 들어 있습니다. 확인란을 선택하면 해당 항목이 다시 동적 매개 변수로 설정됩니다.

   1. **추가** 를 선택하여 이 아티팩트를 청사진에 추가합니다.

   :::image type="content" source="./media/create-blueprint-portal/add-resource-manager-template.png" alt-text="청사진 정의에 추가할 Resource Manager 템플릿 아티팩트 옵션 스크린샷" border="false":::

1. 완성된 청사진은 다음과 같이 표시됩니다. 각 아티팩트의 **매개 변수** 열에는 매개 변수 _y_ 개 중 _x_ 개 **가** 채워졌습니다. 동적 매개 변수는 청사진의 각 할당 중 설정됩니다.

   :::image type="content" source="./media/create-blueprint-portal/completed-blueprint.png" alt-text="각 아티팩트 형식으로 채워진 청사진 정의 스크린샷" border="false":::

1. 계획한 아티팩트를 모두 추가했으므로 페이지 아래쪽의 **초안 저장** 을 선택합니다.

## <a name="edit-a-blueprint"></a>청사진 편집

[청사진 만들기](#create-a-blueprint)에서는 새 리소스 그룹에 설명을 제공하거나 역할 할당을 추가하지 않았습니다. 다음 단계를 수행하여 두 가지 문제를 모두 수정할 수 있습니다.

1. 왼쪽 페이지에서 **청사진 정의** 를 선택합니다.

1. 청사진 목록에서 이전에 만든 청사진을 선택한 상태로 유지(또는 마우스 오른쪽 단추로 클릭)하고 **청사진 편집** 을 선택합니다.

1. **청사진 설명** 에 청사진과 해당 청사진을 구성하는 아티팩트에 대한 정보를 입력합니다. 여기서는 “이 청사진은 구독에서 태그 정책 및 역할 할당을 설정하고, ResourceGroup을 만들고, 해당 ResourceGroup에 리소스 템플릿 및 역할 할당을 배포합니다.”와 같은 설명을 입력합니다.

1. 페이지 아래쪽의 **다음: 아티팩트** 또는 지 위쪽의 **아티팩트** 탭을 클릭합니다.

1. 리소스 그룹 아래에 역할 할당 추가:

   1. **ResourceGroup** 항목 바로 아래의 **+ 아티팩트 추가** 행을 선택합니다.

   1. **아티팩트 형식** 으로 **역할 할당** 을 선택합니다.

   1. **역할** 아래에서 **소유자** 를 선택하고 **사용자, 앱 또는 그룹 추가** 상자 아래에 있는 확인란의 선택을 취소합니다.

   1. 추가할 사용자, 앱 또는 그룹을 검색하여 선택합니다. 이 아티팩트는 이 청사진의 모든 할당에서 동일하게 설정되는 정적 매개 변수를 사용합니다.

   1. **추가** 를 선택하여 이 아티팩트를 청사진에 추가합니다.

   :::image type="content" source="./media/create-blueprint-portal/add-role-assignment-2.png" alt-text="청사진 정의에 추가할 두 번째 역할 할당 아티팩트 옵션 스크린샷" border="false":::

1. 완성된 청사진은 다음과 같이 표시됩니다. 새로 추가한 역할 할당에 **매개 변수 1개 중 1개를 입력했습니다.** 가 표시됩니다. 즉, 이는 정적 매개 변수입니다.

   :::image type="content" source="./media/create-blueprint-portal/completed-blueprint-2.png" alt-text="추가 역할 할당 아티팩트로 채워진 두 번째 청사진 정의 스크린샷" border="false":::

1. 청사진이 업데이트되었으므로 **초안 저장** 을 선택합니다.

## <a name="publish-a-blueprint"></a>청사진 게시

계획한 아티팩트를 청사진에 모두 추가했으므로 이제 청사진을 게시합니다.
게시한 후에는 청사진을 구독에 할당할 수 있습니다.

1. 왼쪽 페이지에서 **청사진 정의** 를 선택합니다.

1. 청사진 목록에서 이전에 만든 청사진을 선택한 상태로 유지(또는 마우스 오른쪽 단추로 클릭)하고 **청사진 게시** 를 선택합니다.

1. 이때 열리는 창에 **버전** 을 **v1** 과 같이 입력합니다. 버전은 문자, 숫자 및 하이픈으로 20자까지 입력할 수 있습니다. 필요에 따라 **메모 변경** 에 **먼저 게시** 와 같은 텍스트를 입력합니다.

1. 페이지 아래쪽의 **게시** 를 선택합니다.

## <a name="assign-a-blueprint"></a>청사진 할당

게시한 청사진은 구독에 할당할 수 있습니다. 작성한 청사진을 관리 그룹 계층 구조에 속하는 구독 중 하나에 할당합니다. 구독에 청사진이 저장되면 해당 구독에만 할당될 수 있습니다.

1. 왼쪽 페이지에서 **청사진 정의** 를 선택합니다.

1. 청사진 목록에서 이전에 만든 청사진을 선택한 상태로 유지(또는 마우스 오른쪽 단추로 클릭)하거나 줄임표를 선택하고 **청사진 할당** 을 선택합니다.

1. **청사진 할당** 페이지의 **구독** 드롭다운 목록에서 이 청사진을 배포하려는 구독을 선택합니다.

   [Azure 청구](../../cost-management-billing/index.yml)에서 사용할 수 있는 지원되는 엔터프라이즈 제품이 있는 경우 **새로 만들기** 링크가 **구독** 상자에서 활성화됩니다. 다음 단계를 수행하세요.

   1. 기존 청사진을 선택하는 대신 새 구독을 만들려면 **새로 만들기** 링크를 선택합니다.

   1. 새 구독에 사용할 **표시 이름** 을 입력합니다.

   1. 드롭다운 목록에서 사용 가능한 **제안** 을 선택합니다.

   1. 줄임표를 사용하여 구독이 자식이 되는 [관리 그룹](../management-groups/overview.md)을 선택합니다.

   1. 페이지의 맨 아래에서 **만들기** 를 선택합니다.

      :::image type="content" source="./media/create-blueprint-portal/assignment-create-subscription.png" alt-text="구독 만들기 창과 새 구독에 대한 옵션 스크린샷" border="false":::

      > [!IMPORTANT]
      > **만들기** 를 선택하면 새 구독이 즉시 생성됩니다.

   > [!NOTE]
   > 선택하는 각 구독에 대해 할당이 생성됩니다. 선택한 나머지 구독을 강제로 변경하지 않고도 나중에 구독 할당 하나만 변경할 수 있습니다.

1. **할당 이름** 에 이 할당의 고유한 이름을 입력합니다.

1. **위치** 에서 관리 ID 및 구독 배포 개체가 생성될 지역을 선택합니다. Azure Blueprints는 이 관리 ID를 사용하여 할당된 청사진의 모든 아티팩트를 배포합니다. 자세히 알아보려면 [Azure 리소스의 관리 ID](../../active-directory/managed-identities-azure-resources/overview.md)를 참조하세요.

1. **v1** 항목에서 **게시됨** 버전의 **청사진 정의 버전** 드롭다운 목록 선택을 유지합니다. (기본값은 가장 최근에 게시된 버전입니다.)

1. **잠금 할당** 에서는 기본값인 **잠그지 않음** 을 유지합니다. 자세한 내용은 [청사진 리소스 잠금](./concepts/resource-locking.md)을 참조하세요.

   :::image type="content" source="./media/create-blueprint-portal/assignment-locking-mi.png" alt-text="청사진 할당에 대한 잠금 할당 및 관리 ID 옵션 스크린샷" border="false":::

1. **관리 ID** 에서 **시스템 할당 항목** 의 기본값을 유지합니다.

1. 구독 수준 역할 할당 **[사용자 그룹 또는 애플리케이션 이름]: 참가자** 에서는 사용자, 앱 또는 그룹을 검색하여 선택합니다.

1. 구독 수준 정책 할당에서는 **태그 이름** 을 **CostCenter**, **태그 값** 을 **ContosoIT** 로 설정합니다.

1. **ResourceGroup** 에서는 **이름** 을 **StorageAccount** 로 입력하고 **위치** 로는 드롭다운 목록에서 **미국 동부 2** 를 선택합니다.

   > [!NOTE]
   > 청사진 정의 중에 리소스 그룹 아래에 추가한 각 아티팩트는 함께 배포할 리소스 그룹이나 개체에 맞게 들여씁니다.
   > 매개 변수를 사용하지 않거나 할당에서 정의할 매개 변수가 없는 아티팩트는 상황 정보 제공용으로만 나열됩니다.

1. ARM 템플릿 **StorageAccount** 에서 **storageAccountType** 매개 변수에 대해 **Standard_GRS** 를 선택합니다.

1. 페이지 아래쪽의 정보 상자 내용을 확인한 다음, **할당** 을 선택합니다.

## <a name="track-deployment-of-a-blueprint"></a>청사진 배포 추적

구독 하나 이상에 청사진을 할당하면 다음의 두 작업이 수행됩니다.

- 청사진이 각 구독별 **할당된 청사진** 페이지에 추가됩니다.
- 청사진으로 정의된 모든 아티팩트를 배포하는 프로세스가 시작됩니다.

청사진이 구독에 할당되었으므로 배포 진행률을 확인합니다.

1. 왼쪽 페이지에서 **할당된 청사진** 을 선택합니다.

1. 청사진 목록에서 이전에 할당한 청사진을 선택한 상태로 유지(또는 마우스 오른쪽 단추로 클릭)하고 **할당 정보 보기** 를 선택합니다.

   :::image type="content" source="./media/create-blueprint-portal/view-assignment-details.png" alt-text="'할당 정보 보기' 옵션이 선택된 청사진 할당 상황에 맞는 메뉴 스크린샷" border="false":::

1. **청사진 할당** 페이지에서 모든 아티팩트가 정상적으로 배포되었으며 배포 중에 오류가 발생하지 않았는지 확인합니다. 오류가 발생한 경우 [청사진 문제 해결](./troubleshoot/general.md)에서 문제를 확인하는 단계를 참조하세요.

## <a name="clean-up-resources"></a>리소스 정리

### <a name="unassign-a-blueprint"></a>청사진 할당 취소

더 이상 필요 없는 청사진 할당은 구독에서 제거합니다. 이 청사진은 패턴, 정책 및 디자인이 업데이트된 최신 청사진으로 대체되었을 수 있습니다. 청사진을 제거해도 해당 청사진의 일부분으로 할당된 아티팩트는 남아 있습니다. 청사진 할당을 제거하려면 다음 단계를 수행합니다.

1. 왼쪽 페이지에서 **할당된 청사진** 을 선택합니다.

1. 청사진 목록에서 할당을 취소하려는 청사진을 선택합니다. 페이지 위쪽의 **청사진 할당 취소** 단추를 선택합니다.

1. 확인 메시지를 살펴본 다음, **확인** 을 선택합니다.

### <a name="delete-a-blueprint"></a>청사진 삭제

1. 왼쪽 페이지에서 **청사진 정의** 를 선택합니다.

1. 삭제할 청사진을 마우스 오른쪽 단추로 클릭하고 **청사진 삭제** 를 선택합니다. 그런 다음, 확인 대화 상자에서 **예** 를 선택합니다.

> [!NOTE]
> 이 메서드에서 청사진을 삭제하면 선택한 청사진의 게시된 버전도 모두 삭제됩니다.
> 단일 버전을 삭제하려면 청사진을 열고 **게시된 버전** 탭을 선택하고 삭제할 버전을 선택한 다음, **이 버전 삭제** 를 선택합니다. 또한 청사진 정의의 청사진 할당을 모두 삭제할 때까지 해당 청사진을 삭제할 수 없습니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure Portal을 사용하여 청사진을 생성, 할당 및 제거했습니다. Azure Blueprints에 대해 자세히 알아보려면 청사진 수명 주기 문서를 참조하세요.

> [!div class="nextstepaction"]
> [청사진 수명 주기에 대해 알아보기](./concepts/lifecycle.md)
