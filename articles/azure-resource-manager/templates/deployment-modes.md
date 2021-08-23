---
title: 배포 모드
description: Azure Resource Manager를 사용하여 전체 또는 증분 배포 모드를 사용할지 여부를 지정하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 07/22/2020
ms.openlocfilehash: 98e92cfc58acd85d1ed49cec0e29d1d73b5b8efb
ms.sourcegitcommit: 6a3096e92c5ae2540f2b3fe040bd18b70aa257ae
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112320877"
---
# <a name="azure-resource-manager-deployment-modes"></a>Azure Resource Manager 배포 모드

리소스를 배포할 때 배포를 증분 업데이트 또는 전체 업데이트할지 지정합니다. 이러한 두 모드 간 차이점은 Resource Manager가 템플릿에 없는 리소스 그룹에서 기존 리소스를 처리하는 방식입니다.

두 모드에서 모두, Resource Manager는 템플릿에 지정된 모든 리소스를 만들려고 합니다. 리소스 그룹에 이미 리소스가 있고 해당 설정이 변경되지 않은 경우 해당 리소스에 대한 작업이 수행되지 않습니다. 리소스의 속성 값을 변경하는 경우 리소스가 새 값으로 업데이트됩니다. 기존 리소스의 위치 또는 종류를 업데이트하려고 하면 오류가 발생하여 배포에 실패합니다. 대신, 새 리소스를 필요한 위치 또는 유형으로 배포합니다.

기본 모드는 증분입니다.

## <a name="complete-mode"></a>전체 모드

전체 모드에서는 Resource Manager가 리소스 그룹에 존재하지만 템플릿에는 지정되지 않은 리소스를 **삭제** 합니다.

> [!NOTE]
> 템플릿을 전체 모드로 배포하기 전에 항상 [가상 작업](./deploy-what-if.md)을 사용합니다. 가상은 삭제 또는 수정되는 리소스를 표시합니다. 리소스를 실수로 삭제하지 않도록 하려면 가상을 사용합니다.

[조건](conditional-resource-deployment.md)이 false로 평가되므로 템플릿에 배포되지 않은 리소스가 포함된 경우에는 템플릿을 배포하는 데 사용하는 REST API 버전에 따라 결과가 달라집니다. 2019년 5월 10일 이전 버전을 사용하는 경우 리소스는 **삭제되지 않습니다**. 2019년 5월 10일 이후 버전에서는 리소스가 **삭제됩니다**. 최신 버전의 Azure PowerShell 및 Azure CLI는 리소스를 삭제합니다.

[복사 루프](copy-resources.md)에서 전체 모드를 사용할 때는 주의해야 합니다. 복사 루프를 확인한 후 템플릿에 지정되지 않은 모든 리소스는 삭제됩니다.

[템플릿에서 둘 이상의 리소스 그룹](./deploy-to-resource-group.md)에 배포하는 경우 배포 작업에 지정된 리소스 그룹의 리소스를 삭제할 수 있습니다. 보조 리소스 그룹의 리소스는 삭제되지 않습니다.

리소스 종류에서 전체 모드 삭제를 처리하는 방법에는 몇 가지 차이점이 있습니다. 전체 모드로 배포된 템플릿에 없는 경우 부모 리소스가 자동으로 삭제됩니다. 일부 자식 리소스는 템플릿에 없더라도 자동으로 삭제되지 않습니다. 그러나 부모 리소스가 삭제되면 이러한 자식 리소스도 삭제됩니다.

예를 들어 리소스 그룹에 DNS 영역(`Microsoft.Network/dnsZones` 리소스 종류)과 CNAME 레코드(`Microsoft.Network/dnsZones/CNAME` 리소스 종류)가 포함된 경우 DNS 영역은 CNAME 레코드의 부모 리소스입니다. 전체 모드로 배포하고 템플릿에 DNS 영역을 포함하지 않으면 DNS 영역과 CNAME 레코드가 둘 다 삭제됩니다. 템플릿에 DNS 영역을 포함하지만 CNAME 레코드를 포함하지 않으면 CNAME은 삭제되지 않습니다.

리소스 종류의 삭제 처리 방식에 대한 목록은 [전체 모드 배포에 대한 Azure 리소스 삭제](./deployment-complete-mode-deletion.md)를 참조하세요.

리소스 그룹이 [잠긴](../management/lock-resources.md) 경우, 전체 모드에서 리소스를 삭제하지 않습니다.

> [!NOTE]
> 루트 수준 템플릿만 전체 배포 모드를 지원합니다. [연결된 또는 중첩된 템플릿](linked-templates.md)의 경우 증분 모드만 사용해야 합니다.
>
> [구독 수준 배포](deploy-to-subscription.md)는 전체 모드를 지원하지 않습니다.
>
> 현재 포털은 전체 모드를 지원하지 않습니다.
>

## <a name="incremental-mode"></a>증분 모드

증분 모드에서는 Resource Manager가 리소스 그룹에 존재하지만 템플릿에는 지정되지 않은 리소스를 **변경하지 않고 유지** 합니다. 템플릿의 리소스는 리소스 그룹에 **추가** 됩니다.

> [!NOTE]
> 증분 모드에서 기존 리소스를 다시 배포하는 경우 모든 속성이 다시 적용됩니다. **속성은 증분 방식으로 추가되지 않습니다**. 일반적인 오해는 템플릿에 지정되지 않은 속성은 변경되지 않는다고 생각하는 것입니다. 특정 속성을 지정하지 않을 경우 Resource Manager는 배포를 해당 값을 덮어쓰는 것으로 해석합니다. 템플릿에 포함되지 않은 속성은 기본값으로 다시 설정됩니다. 업데이트하는 것뿐만 아니라 리소스에 대해 기본값이 아닌 모든 값을 지정합니다. 템플릿의 리소스 정의는 항상 리소스의 최종 상태를 포함합니다. 기존 리소스에 대한 부분 업데이트를 나타낼 수 없습니다.
>
> 드문 경우지만 리소스에 대해 지정하는 속성은 실제로 자식 리소스로 구현됩니다. 예를 들어 웹앱에 대한 사이트 구성 값을 제공하는 경우 해당 값은 자식 리소스 종류 `Microsoft.Web/sites/config`에서 구현됩니다. 웹앱을 다시 배포하고 사이트 구성 값에 대해 빈 개체를 지정하면 자식 리소스가 업데이트되지 않습니다. 그러나 새 사이트 구성 값을 제공하는 경우 자식 리소스 종류가 업데이트됩니다.

## <a name="example-result"></a>결과 예제

증분 및 전체 모드 간의 차이를 보여주려면 다음 시나리오를 고려합니다.

**리소스 그룹** 은 다음 항목을 포함합니다.

* 리소스 A
* 리소스 B
* 리소스 C

**템플릿** 은 다음 항목을 포함합니다.

* 리소스 A
* 리소스 B
* 리소스 D

리소스 그룹이 **증분** 모드로 배포된 경우 다음 항목을 포함합니다.

* 리소스 A
* 리소스 B
* 리소스 C
* 리소스 D

리소스 C가 **전체** 모드로 배포된 경우 삭제됩니다. 리소스 그룹은 다음 항목을 포함합니다.

* 리소스 A
* 리소스 B
* 리소스 D

## <a name="set-deployment-mode"></a>배포 모드 설정

PowerShell을 사용하여 배포하는 경우 배포 모드를 설정하려면 `Mode` 매개 변수를 사용합니다.

```azurepowershell-interactive
New-AzResourceGroupDeployment `
  -Mode Complete `
  -Name ExampleDeployment `
  -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json
```

Azure CLI를 사용하여 배포하는 경우 배포 모드를 설정하려면 `mode` 매개 변수를 사용합니다.

```azurecli-interactive
az deployment group create \
  --mode Complete \
  --name ExampleDeployment \
  --resource-group ExampleResourceGroup \
  --template-file storage.json
```

다음 예제에서는 증분 배포 모드로 연결된 템플릿 세트를 보여줍니다.

```json
"resources": [
  {
    "type": "Microsoft.Resources/deployments",
    "apiVersion": "2020-10-01",
    "name": "linkedTemplate",
    "properties": {
      "mode": "Incremental",
          <nested-template-or-external-template>
    }
  }
]
```

## <a name="next-steps"></a>다음 단계

* Resource Manager 템플릿을 만드는 방법에 대해 알아보려면 [ARM 템플릿의 구조 및 구문 이해](./syntax.md)를 참조하세요.
* 배포 리소스에 대해 알아보려면 [ARM 템플릿 및 Azure PowerShell을 사용하여 리소스 배포](deploy-powershell.md)를 참조하세요.
* 리소스 공급자에 대한 작업을 보려면 [Azure REST API](/rest/api/)를 참조하세요.
