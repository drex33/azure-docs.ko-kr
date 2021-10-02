---
title: 통합 계정 만들기 및 관리
description: Enterprise 통합 팩을 사용하여 Azure Logic Apps B2B 엔터프라이즈 통합 워크플로를 빌드하기 위한 통합 계정을 만들고 관리합니다.
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 09/14/2021
ms.openlocfilehash: c140c8495b1179298d6ca0029f430896aab55f7f
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129360839"
---
# <a name="create-and-manage-integration-accounts-for-b2b-workflows-in-azure-logic-apps-with-the-enterprise-integration-pack"></a>Enterprise 통합 팩을 사용하여 Azure Logic Apps B2B 워크플로에 대한 통합 계정 만들기 및 관리

Azure Logic Apps 사용하여 B2B(Business-to-Business) 및 엔터프라이즈 통합 워크플로를 빌드하려면 통합 *계정* 리소스를 만들어야 합니다. 이 계정은 B2B 시나리오에 대한 워크플로에서 정의하고 사용하는 B2B 아티팩트 저장 및 관리 방법을 간소화하는 Azure의 확장 가능한 클라우드 기반 컨테이너입니다. 이러한 아티팩트로는 [거래 업체,](logic-apps-enterprise-integration-partners.md) [계약,](logic-apps-enterprise-integration-agreements.md) [맵,](logic-apps-enterprise-integration-maps.md) [스키마,](logic-apps-enterprise-integration-schemas.md) [인증서](logic-apps-enterprise-integration-certificates.md)등이 있습니다. B2B 메시지를 다른 조직과 전자적으로 교환하려면 통합 계정도 있어야 합니다. 다른 조직에서 조직과 다른 프로토콜 및 메시지 형식을 사용하는 경우 조직의 시스템에서 해당 메시지를 처리할 수 있도록 이러한 형식을 변환해야 합니다. 지원되는 업계 표준 프로토콜에는 [AS2,](logic-apps-enterprise-integration-as2.md) [X12,](logic-apps-enterprise-integration-x12.md) [EDIFACT](logic-apps-enterprise-integration-edifact.md)및 [RosettaNet이](logic-apps-enterprise-integration-rosettanet.md)포함됩니다.

> [!TIP]
> [통합 서비스 환경에서](connect-virtual-network-vnet-isolated-environment-overview.md)사용할 통합 계정을 만들려면 [ISE에서 통합 계정 만들기를 검토합니다.](add-artifacts-integration-service-environment-ise.md#create-integration-account-environment)

이 문서에서는 다음 작업을 완료하는 방법을 보여줍니다.

* 통합 계정을 만듭니다.
* 통합 계정을 논리 앱 리소스에 연결합니다.
* 통합 계정의 가격 책정 계층을 변경합니다.
* 논리 앱에서 통합 계정 연결을 해제합니다.
* 통합 계정을 다른 Azure 리소스 그룹 또는 구독으로 이동합니다.
* 통합 계정을 삭제합니다.

Azure Logic Apps 경우 Azure Logic Apps 무엇인가요?를 [](logic-apps-overview.md)검토하세요. B2B 엔터프라이즈 통합에 대한 자세한 내용은 [Azure Logic Apps 및 Enterprise 통합 팩을 통해 B2B 엔터프라이즈 통합 워크플로를](logic-apps-enterprise-integration-overview.md)검토하세요.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 계정 및 구독 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)합니다. 통합 계정 및 논리 앱 리소스 모두에 대해 동일한 Azure 구독을 사용해야 합니다.

* [ **논리 앱(소비)** 리소스 종류](logic-apps-overview.md#resource-type-and-host-environment-differences)를 사용하는 경우 통합 계정 에 연결할 수 있는 논리 앱 리소스가 있어야 [합니다.](#link-account) 워크플로에서 아티팩트 사용하려면 이 링크가 필요합니다. 이 링크 없이 아티팩트만 만들 수 있지만 워크플로에서 이러한 아티팩트 사용 준비가 되면 링크가 필요합니다.

* [ **논리 앱(표준)** 리소스 종류](logic-apps-overview.md#resource-type-and-host-environment-differences)를 사용하는 경우 Azure Portal 또는 Visual Studio Code 사용하여 논리 앱 리소스에 맵 및 스키마를 직접 추가할 수 있습니다. 그런 다음, ‘동일한 논리 앱 리소스’ 내의 여러 워크플로에서 이 아티팩트를 사용할 수 있습니다. 다른 B2B 아티팩트를 위한 통합 계정을 만들고 AS2, X12, [EDIFACT](logic-apps-enterprise-integration-edifact.md)및 [RosettaNet](logic-apps-enterprise-integration-rosettanet.md) 작업과 같은 [B2B](logic-apps-enterprise-integration-as2.md)작업을 사용해야 합니다. [](logic-apps-enterprise-integration-x12.md) 그러나 통합 계정을 논리 앱 리소스에 연결할 필요가 없으므로 연결 기능이 존재하지 않습니다.

## <a name="create-integration-account"></a>통합 계정 만들기

통합 계정은 [가격 책정에 따라 다른 계층에서](https://azure.microsoft.com/pricing/details/logic-apps/)사용할 수 있습니다. 선택한 계층에 따라 통합 계정을 만들면 비용이 발생할 수 있습니다. 자세한 내용은 [Logic Apps 가격 책정 및 청구 모델](logic-apps-pricing.md#integration-accounts)과 [Logic Apps 가격 책정](https://azure.microsoft.com/pricing/details/logic-apps/)을 검토하세요.

요구 사항 및 시나리오에 따라 만들 적절한 통합 계정 계층을 결정합니다. 통합 계정과 논리 앱 리소스는 *모두 동일한* 위치 또는 Azure 지역을 사용해야 합니다. 다음 표에서는 사용 가능한 계층에 대해 설명합니다.

| 계층 | Description |
|------|-------------|
| **기본** | 메시지 처리만 원하는 시나리오 또는 대규모 비즈니스 엔터티와 거래 업체 관계를 맺고 있는 소규모 비즈니스 파트너 역할을 수행하는 시나리오의 경우. <p><p>Logic Apps SLA에서 지원됩니다. |
| **Standard** | 더 복잡한 B2B 관계가 있고 관리해야 하는 엔터티 수가 늘어난 시나리오의 경우. <p><p>Logic Apps SLA에서 지원됩니다. |
| **Free** | 프로덕션 시나리오가 아닌 예비 시나리오에 적합합니다. 이 계층에는 지역 가용성, 처리량 및 사용량에 대한 한도가 있습니다. 예를 들어, 무료 계층은 Azure의 공용 지역(예: 미국 서부 또는 동남 아시아)에서만 사용할 수 있고, [Azure 중국 21Vianet](/azure/china/overview-operations) 또는 [Azure Government](../azure-government/documentation-government-welcome.md)에서는 사용할 수 없습니다. <p><p>**참고**: Logic Apps SLA에서 지원되지 않습니다. |
|||

이 작업의 경우 Azure Portal, [Azure CLI](/cli/azure/resource#az_resource_create)또는 [Azure PowerShell](/powershell/module/Az.LogicApp/New-AzIntegrationAccount)사용할 수 있습니다.

### <a name="portal"></a>[포털](#tab/azure-portal)

1. Azure 계정 자격 증명을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 기본 Azure 검색 상자에 `integration accounts`를 입력하고, **통합 계정** 을 선택합니다.

1. **통합 계정에서** **만들기를** 선택합니다.

1. 통합 **계정 만들기** 창에서 통합 계정에 대한 다음 정보를 제공합니다.

   | 속성 | 필수 | 값 | Description |
   |----------|----------|-------|-------------|
   | **구독** | 예 | <*Azure-subscription-name*> | Azure 구독의 이름 |
   | **리소스 그룹** | 예 | <*Azure-resource-group-name*> | 관련 리소스를 구성하기 위해 사용할 [Azure 리소스 그룹](../azure-resource-manager/management/overview.md)의 이름입니다. 이 예제에서는 라는 새 리소스 그룹을 `FabrikamIntegration-RG` 만듭니다. |
   | **통합 계정 이름** | 예 | <*integration-account-name*> | 문자, 숫자, 하이픈(`-`), 밑줄(`_`), 매개 변수(`(`, `)`) 및 기간(`.`)만 포함할 수 있는 통합 계정의 이름입니다. 이 예에서는 `Fabrikam-Integration`를 사용합니다. |
   | **지역** | 예 | <*Azure-region*> | 통합 계정 메타데이터를 저장할 Azure 지역입니다. 논리 앱과 동일한 위치를 선택하거나 통합 계정과 동일한 위치에 논리 앱을 만듭니다. 이 예제에서는 `West US` 를 사용합니다. <p>**참고:** [ISE(통합 서비스 환경)](connect-virtual-network-vnet-isolated-environment-overview.md)내에 통합 계정을 만들려면 **통합 서비스 환경과 연결** 을 선택하고 위치로 ISE를 선택합니다. 자세한 내용은 [ISE에서 통합 계정 만들기](add-artifacts-integration-service-environment-ise.md#create-integration-account-environment)를 참조하세요. |
   | **가격 책정 계층** | Yes | <*pricing-level*> | 나중에 변경할 수 있는 통합 계정의 가격 책정 계층입니다. 이 예제에서는 **Free** 를 선택합니다. 자세한 내용은 다음 설명서를 검토하세요. <p>- [Logic Apps 가격 책정 모델](logic-apps-pricing.md#integration-accounts) <br>- [Logic Apps 제한 및 구성](logic-apps-limits-and-config.md#integration-account-limits) <br>- [Logic Apps 가격 책정](https://azure.microsoft.com/pricing/details/logic-apps/) |
   | **로그 분석 사용** | 예 | 선택 취소 | 이 예제에서는 이 옵션을 선택하지 마세요. |
   |||||

1. 완료되면 **검토 + 만들기** 를 선택합니다.

   배포가 완료되면 Azure가 통합 계정을 엽니다.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

1. [az logic integration-account](/cli/azure/logic/integration-account) 확장을 추가하려면 [az extension add](/cli/azure/extension#az_extension_add) 명령을 사용합니다.

   ```azurecli
   az extension add –-name logic
   ```

1. 리소스 그룹을 만들거나 기존 리소스 그룹을 사용하려면 [az group create](/cli/azure/group#az_group_create) 명령을 실행합니다.

   ```azurecli
   az group create --name myresourcegroup --location westus
   ```

   리소스 그룹에 대해 통합 계정을 나열하려면 [az logic integration-account list](/cli/azure/logic/integration-account#az_logic_integration_account_list) 명령을 사용합니다.

   ```azurecli
   az logic integration-account list --resource-group myresourcegroup
   ```

1. 통합 계정을 만들려면 [az logic integration-account create](/cli/azure/logic/integration-account#az_logic_integration_account_create) 명령을 실행합니다.

   ```azurecli
   az logic integration-account create --resource-group myresourcegroup \
       --name integration_account_01 --location westus --sku name=Standard
   ```

   통합 계정 이름에는 문자, 숫자, 하이픈(-), 밑줄(_), 괄호((, )) 및 마침표(.)만 사용할 수 있습니다.

   특정 통합 계정을 보려면 [az logic integration-account show](/cli/azure/logic/integration-account#az_logic_integration_account_show) 명령을 사용합니다.

   ```azurecli
   az logic integration-account show --name integration_account_01 --resource-group myresourcegroup
   ```

   [az logic integration-account update](/cli/azure/logic/integration-account#az_logic_integration_account_update) 명령을 사용하여 SKU 또는 가격 책정 계층을 변경할 수 있습니다.

   ```azurecli
   az logic integration-account update --sku name=Basic --name integration_account_01 \
       --resource-group myresourcegroup
   ```

   가격 책정에 대한 자세한 내용은 다음 리소스를 참조하세요.

   * [Logic Apps 가격 책정 모델](logic-apps-pricing.md#integration-accounts)
   * [Logic Apps 제한 및 구성](logic-apps-limits-and-config.md#integration-account-limits)
   * [Logic Apps 가격 책정](https://azure.microsoft.com/pricing/details/logic-apps/)

JSON 파일을 사용하여 통합 계정을 가져오려면 [az logic integration-account import](/cli/azure/logic/integration-account#az_logic_integration_account_import) 명령을 사용합니다.

```azurecli
az logic integration-account import --name integration_account_01 \
    --resource-group myresourcegroup --input-path integration.json
```

---

<a name="link-account"></a>

## <a name="link-to-logic-app-consumption-resource-only"></a>논리 앱에 연결 (소비 리소스 전용)

**논리 앱 (소비)** 워크플로가 통합 계정의 B2B 아티팩트에 액세스 하려면 먼저 논리 앱 리소스를 통합 계정에 연결 해야 합니다. 논리 앱과 통합 계정 모두 동일한 Azure 구독 및 Azure 지역을 사용 해야 합니다. 이 작업을 완료하려면 Azure Portal을 사용할 수 있습니다. Visual Studio를 사용하고 논리 앱이 [Azure 리소스 그룹 프로젝트](../azure-resource-manager/templates/create-visual-studio-deployment-project.md)에 있으면 [Visual Studio를 사용하여 논리 앱을 통합 계정에 연결](manage-logic-apps-with-visual-studio.md#link-integration-account)할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에서 기존 논리 앱을 열거나 새 논리 앱을 만듭니다.

1. 논리 앱의 메뉴에 있는 **설정** 아래에서 **워크플로 설정** 을 선택합니다. **통합 계정** 에서 **통합 계정 선택** 목록을 열고 원하는 통합 계정을 선택 합니다.

   !["워크플로 설정" 창이 열리고 "통합 계정 선택" 목록이 열려 있는 Azure Portal 통합 계정 메뉴와 함께 표시 되는 스크린샷](./media/logic-apps-enterprise-integration-create-integration-account/select-integration-account.png)

1. 연결을 완료하려면 **저장** 을 선택합니다.

   !["워크플로 설정" 창을 표시 하 고 "저장"을 선택 하는 스크린샷](./media/logic-apps-enterprise-integration-create-integration-account/save-link.png)

   통합 계정이 성공적으로 연결되었으면 Azure에서 확인 메시지가 표시됩니다.

   ![Azure 확인 메시지를 보여 주는 스크린샷](./media/logic-apps-enterprise-integration-create-integration-account/link-confirmation.png)

이제 논리 앱에서 통합 계정의 아티팩트와 B2B 커넥터(예: XML 유효성 검사 및 플랫 파일 인코딩 또는 디코딩)를 사용할 수 있습니다.  

<a name="change-pricing-tier"></a>

## <a name="change-pricing-tier"></a>가격 책정 계층 변경

통합 계정의 [한도](logic-apps-limits-and-config.md#integration-account-limits)를 늘리려면 가능한 경우 [더 높은 가격 책정 계층으로 업그레이드](#upgrade-pricing-tier)할 수 있습니다. 예를 들어 무료 계층에서 기본 계층 또는 표준 계층으로 업그레이드할 수 있습니다. 또한 가능한 경우 [더 낮은 계층으로 다운그레이드](#downgrade-pricing-tier)할 수도 있습니다. 가격 책정 정보에 대 한 자세한 내용은 다음 설명서를 검토 하세요.

* [Logic Apps 가격 책정](https://azure.microsoft.com/pricing/details/logic-apps/)
* [Logic Apps 가격 책정 모델](logic-apps-pricing.md#integration-accounts)

<a name="upgrade-pricing-tier"></a>

### <a name="upgrade-pricing-tier"></a>가격 책정 계층 업그레이드

이 항목을 변경하기 위해서는 Azure Portal 또는 Azure CLI를 사용할 수 있습니다.

#### <a name="portal"></a>[포털](#tab/azure-portal)

1. Azure 계정 자격 증명을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 기본 Azure 검색 상자에 `integration accounts`를 입력하고, **통합 계정** 을 선택합니다.

   Azure에 Azure 구독의 모든 통합 계정이 표시됩니다.

1. **통합 계정** 아래에서 이동하려는 통합 계정을 선택합니다. 통합 계정 메뉴에서 **개요** 를 선택합니다.

   ![통합 계정 메뉴와 "개요"가 선택 Azure Portal 표시 되는 스크린샷](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. 개요 창에서 사용 가능한 상위 계층을 나열 하는 **업그레이드 가격 책정 계층** 을 선택 합니다. 계층을 선택하면 변경 내용이 즉시 적용됩니다.

   !["업그레이드 가격 책정 계층"이 선택 된 통합 계정 "개요" 창을 보여 주는 스크린샷](media/logic-apps-enterprise-integration-create-integration-account/upgrade-pricing-tier.png)

<a name="upgrade-tier-azure-cli"></a>

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. 아직 수행하지 않았으면 [Azure CLI 필수 구성 요소](/cli/azure/get-started-with-azure-cli)를 설치합니다.

1. Azure Portal에서 [Azure Cloud Shell](../cloud-shell/overview.md) 환경을 엽니다.

   !["Cloud Shell"를 선택 하 여 Azure Portal 도구 모음을 보여 주는 스크린샷](./media/logic-apps-enterprise-integration-create-integration-account/open-azure-cloud-shell-window.png)

1. 명령 프롬프트에서 [**az resource** 명령](/cli/azure/resource#az_resource_update)을 입력하고 `skuName`을 원하는 상위 계층으로 설정합니다.

   ```azurecli
   az resource update --resource-group {ResourceGroupName} --resource-type Microsoft.Logic/integrationAccounts --name {IntegrationAccountName} --subscription {AzureSubscriptionID} --set sku.name={SkuName}
   ```
  
   예를 들어 기본 계층을 사용 중이면 `skuName`을 `Standard`로 설정할 수 있습니다.

   ```azurecli
   az resource update --resource-group FabrikamIntegration-RG --resource-type Microsoft.Logic/integrationAccounts --name Fabrikam-Integration --subscription XXXXXXXXXXXXXXXXX --set sku.name=Standard
   ```

---

<a name="downgrade-pricing-tier"></a>

### <a name="downgrade-pricing-tier"></a>가격 책정 계층 다운그레이드

이 항목을 변경하려면 [Azure CLI](/cli/azure/get-started-with-azure-cli)를 사용합니다.

1. 아직 수행하지 않았으면 [Azure CLI 필수 구성 요소](/cli/azure/get-started-with-azure-cli)를 설치합니다.

1. Azure Portal에서 [Azure Cloud Shell](../cloud-shell/overview.md) 환경을 엽니다.

   !["Cloud Shell"를 선택 하 여 Azure Portal 도구 모음을 보여 주는 스크린샷](./media/logic-apps-enterprise-integration-create-integration-account/open-azure-cloud-shell-window.png)

1. 명령 프롬프트에서 [**az resource** 명령](/cli/azure/resource#az_resource_update)을 입력하고 `skuName`을 원하는 하위 계층으로 설정합니다.

   ```azurecli
   az resource update --resource-group <resourceGroupName> --resource-type Microsoft.Logic/integrationAccounts --name <integrationAccountName> --subscription <AzureSubscriptionID> --set sku.name=<skuName>
   ```
  
   예를 들어 표준 계층을 사용 중이면 `skuName`을 `Basic`으로 설정할 수 있습니다.

   ```azurecli
   az resource update --resource-group FabrikamIntegration-RG --resource-type Microsoft.Logic/integrationAccounts --name Fabrikam-Integration --subscription XXXXXXXXXXXXXXXXX --set sku.name=Basic
   ```

## <a name="unlink-from-logic-app"></a>논리 앱에서 연결 해제

논리 앱을 또 다른 통합 계정에 연결하거나 논리 앱에서 통합 계정을 더 이상 사용하지 않으려면 Azure Resource Explorer를 사용하여 연결을 삭제합니다.

1. 브라우저 창을 열고 [Azure Resource Explorer(https://resources.azure.com)](https://resources.azure.com)로 이동합니다. 동일한 Azure 계정 자격 증명을 사용하여 로그인합니다.

   ![Azure Resource Explorer를 사용 하 여 웹 브라우저를 보여 주는 스크린샷](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer.png)

1. 검색 상자에서 논리 앱의 이름을 입력 하 여 논리 앱을 찾아 엽니다.

   ![논리 앱 이름을 포함 하는 탐색기 검색 상자를 보여 주는 스크린샷](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-find-logic-app.png)

1. 탐색기 제목 표시줄에서 **읽기/쓰기** 를 선택합니다.

   !["읽기/쓰기"가 선택 된 제목 표시줄을 보여 주는 스크린샷](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-select-read-write.png)

1. **데이터** 탭에서 **편집** 을 선택합니다.

   !["편집"이 선택 된 "데이터" 탭을 보여 주는 스크린샷](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-select-edit.png)

1. 편집기에서 `integrationAccount` 개체를 찾고, 다음 형식을 갖는 해당 속성을 삭제합니다.

   ```json
   {
      // <other-attributes>
      "integrationAccount": {
         "name": "<integration-account-name>",
         "id": "<integration-account-resource-ID>",
         "type": "Microsoft.Logic/integrationAccounts"  
   },
   ```

   예를 들면 다음과 같습니다.

   !["IntegrationAccount" 개체를 찾는 방법을 보여 주는 스크린샷](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-delete-integration-account.png)

1. **데이터** 탭에서 **배치** 를 선택하여 변경 내용을 저장합니다.

   !["Put"이 선택 된 "데이터" 탭을 보여 주는 스크린샷](./media/logic-apps-enterprise-integration-create-integration-account/resource-explorer-save-changes.png)

1. Azure Portal에서 논리 앱을 엽니다. 논리 앱 메뉴의 **워크플로 설정** 에서 **통합 계정** 속성이 이제 빈 상태로 표시 되는지 확인 합니다.

   ![논리 앱 메뉴와 "워크플로 설정"을 선택 하 여 Azure Portal를 보여 주는 스크린샷](./media/logic-apps-enterprise-integration-create-integration-account/unlinked-account.png)

## <a name="move-integration-account"></a>통합 계정 이동

통합 계정을 또 다른 Azure 리소스 그룹 또는 Azure 구독으로 이동할 수 있습니다. 리소스를 이동할 때 Azure는 새 리소스 ID를 만듭니다. 따라서 대신 새 ID를 사용하고, 이동된 리소스와 연관된 스크립트 또는 도구를 업데이트해야 합니다. 또한 구독을 변경하려면 기존 또는 새로운 리소스 그룹을 지정해야 합니다.

이 작업에서는 이 섹션 또는 [Azure CLI](/cli/azure/resource#az_resource_move)에서 해당 단계에 따라 Azure Portal을 사용할 수 있습니다.

1. Azure 계정 자격 증명을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 기본 Azure 검색 상자에 `integration accounts`를 입력하고, **통합 계정** 을 선택합니다.

   Azure에 Azure 구독의 모든 통합 계정이 표시됩니다.

1. **통합 계정** 아래에서 이동하려는 통합 계정을 선택합니다. 통합 계정 메뉴에서 **개요** 를 선택합니다.

1. 개요 창에서 **리소스 그룹** 또는 **구독 이름** 옆에 있는 **변경** 을 선택 합니다.

   !["리소스 그룹" 또는 "구독 이름" 옆에 "변경"이 선택 된 Azure Portal 및 "개요" 창을 보여 주는 스크린샷](./media/logic-apps-enterprise-integration-create-integration-account/change-resource-group-subscription.png)

1. 이동하려는 모든 관련 리소스를 선택합니다.

1. 선택 사항을 기준으로 다음 단계에 다라 리소스 그룹 또는 구독을 변경합니다.

   * 리소스 그룹: **리소스 그룹** 목록에서 대상 리소스 그룹을 선택합니다. 또는 다른 리소스 그룹을 만들려면 **새 리소스 그룹 만들기** 를 선택합니다.

   * 구독: **구독** 목록에서 대상 구독을 선택합니다. **리소스 그룹** 목록에서 대상 리소스 그룹을 선택합니다. 또는 다른 리소스 그룹을 만들려면 **새 리소스 그룹 만들기** 를 선택합니다.

1. 이동된 리소스와 연결된 모든 스크립트 또는 도구가 새 리소스 ID로 업데이트할 때까지 작동하지 않는다는 것을 이해하는 경우, 확인 상자를 선택한 후 **확인** 을 선택합니다.

1. 완료되면 이동한 리소스에 대해 새로운 리소스 ID를 사용하여 모든 스크립트를 업데이트합니다.  

## <a name="delete-integration-account"></a>통합 계정 삭제

이 작업에서는 이 섹션, [Azure CLI](/cli/azure/resource#az_resource_delete) 또는 [Azure PowerShell](/powershell/module/az.logicapp/remove-azintegrationaccount)의 단계에 따라 Azure Portal을 사용할 수 있습니다.

### <a name="portal"></a>[포털](#tab/azure-portal)

1. Azure 계정 자격 증명을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 기본 Azure 검색 상자에 `integration accounts`를 입력하고, **통합 계정** 을 선택합니다.

   Azure에 Azure 구독의 모든 통합 계정이 표시됩니다.

1. **통합 계정** 아래에서 삭제하려는 통합 계정을 선택합니다. 통합 계정 메뉴에서 **개요** 를 선택합니다.

   !["통합 계정" 목록과 "개요"가 선택된 통합 계정 메뉴가 있는 Azure Portal 보여 주는 스크린샷](./media/logic-apps-enterprise-integration-create-integration-account/integration-account-overview.png)

1. 개요 창에서 **삭제** 를 선택합니다.

   !["삭제"가 선택된 "개요" 창을 보여주는 스크린샷.](./media/logic-apps-enterprise-integration-create-integration-account/delete-integration-account.png)

1. 통합 계정을 삭제하려면 **예** 를 선택합니다.

   ![확인 상자 및 선택된 "예"를 보여주는 스크린샷.](./media/logic-apps-enterprise-integration-create-integration-account/confirm-delete.png)

<a name="delete-account-azure-cli"></a>

#### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[az logic integration-account delete](/cli/azure/logic/integration-account#az_logic_integration_account_delete) 명령을 사용하여 통합 계정을 삭제할 수 있습니다.

```azurecli
az logic integration-account delete --name integration_account_01 --resource-group myresourcegroup
```

---

## <a name="next-steps"></a>다음 단계

* [통합 계정에 거래 업체 만들기](logic-apps-enterprise-integration-partners.md)
* [통합 계정에서 파트너 간 계약 만들기](logic-apps-enterprise-integration-agreements.md)
