---
title: Azure Resource Manager 템플릿을 사용하여 Azure Cache for Redis 배포
description: Azure Resource Manager 템플릿(ARM 템플릿)을 사용하여 Azure Cache for Redis 리소스를 배포하는 방법을 알아봅니다. 일반적인 시나리오를 위한 템플릿이 제공됩니다.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.custom: subject-armqs, devx-track-azurepowershell
ms.date: 04/28/2021
ms.openlocfilehash: 1f284169387209f3783a9621419bf17ea8240619
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129537715"
---
# <a name="quickstart-create-an-azure-cache-for-redis-using-an-arm-template"></a>빠른 시작: ARM 템플릿을 사용하여 Azure Cache for Redis 만들기

Azure Cache for Redis를 배포하는 ARM 템플릿(Azure Resource Manager 템플릿)을 만드는 방법에 대해 알아봅니다. 기존 스토리지 계정과 함께 캐시를 사용하여 진단 데이터를 유지할 수 있습니다. 어떤 리소스를 배포할지 정의하는 방법 및 배포를 실행할 때 매개 변수를 지정하는 방법도 알아봅니다. 배포를 위해 이 템플릿을 사용하거나 요구 사항에 맞게 사용자 지정을 할 수 있습니다. 현재 진단 설정은 구독에 대한 동일한 지역의 모든 캐시에서 공유됩니다. 지역의 캐시 하나를 업데이트하면 해당 지역의 다른 모든 캐시에 영향을 줍니다.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

환경이 필수 구성 요소를 충족하고 ARM 템플릿 사용에 익숙한 경우 **Azure에 배포** 단추를 선택합니다. 그러면 Azure Portal에서 템플릿이 열립니다.

[![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.cache%2Fredis-cache%2Fazuredeploy.json)

## <a name="prerequisites"></a>필수 구성 요소

* **Azure 구독**: Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* **스토리지 계정**: 계정을 만들려면 [Azure Storage 계정 만들기](../storage/common/storage-account-create.md?tabs=azure-portal)를 참조하세요. 스토리지 계정은 진단 데이터에 사용됩니다.

## <a name="review-the-template"></a>템플릿 검토

이 빠른 시작에서 사용되는 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/redis-cache/)에서 나온 것입니다.

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.cache/redis-cache/azuredeploy.json":::

다음 리소스는 템플릿에 정의되어 있습니다.

* [Microsoft.Cache/Redis](/azure/templates/microsoft.cache/redis)
* [Microsoft.Insights/diagnosticsettings](/azure/templates/microsoft.insights/diagnosticsettings)

새 [프리미엄 계층](cache-overview.md#service-tiers)을 위한 Resource Manager 템플릿을 사용할 수 있습니다.

* [클러스터링이 있는 프리미엄 Azure Cache for Redis 만들기](https://azure.microsoft.com/resources/templates/redis-premium-cluster-diagnostics/)
* [데이터 지속성이 있는 프리미엄 Azure Cache for Redis 만들기](https://azure.microsoft.com/resources/templates/redis-premium-persistence/)
* [Virtual Network에 배포된 프리미엄 Redis Cache 만들기](https://azure.microsoft.com/resources/templates/redis-premium-vnet/)

최신 템플릿을 확인하려면 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/)을 참조하고 _Azure Cache for Redis_ 를 검색하세요.

## <a name="deploy-the-template"></a>템플릿 배포

1. 다음 이미지를 선택하여 Azure에 로그인하고 템플릿을 엽니다.

    [![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.cache%2Fredis-cache%2Fazuredeploy.json)
1. 다음 값을 선택하거나 입력합니다.

    * **구독**: 데이터 공유 및 기타 리소스를 만드는 데 사용되는 Azure 구독을 선택합니다.
    * **리소스 그룹**: **새로 만들기** 를 선택하여 새 리소스 그룹을 만들거나 기존 리소스 그룹을 선택합니다.
    * **위치**: 리소스 그룹에 대한 위치를 선택합니다. 스토리지 계정과 Redis Cache는 동일한 지역에 있어야 합니다. 기본적으로 Redis Cache는 리소스 그룹과 동일한 위치를 사용합니다. 따라서 스토리지 계정과 동일한 위치를 지정합니다.
    * **Redis Cache 이름**: Redis Cache의 이름을 입력합니다.
    * **기존 진단 스토리지 계정**: 스토리지 계정의 리소스 ID를 입력합니다. 구문은 `/subscriptions/&lt;SUBSCRIPTION ID>/resourceGroups/&lt;RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/&lt;STORAGE ACCOUNT NAME>`입니다.

    나머지 설정에 대해서는 기본값을 사용합니다.
1. **위에 명시된 사용 약관에 동의함** 을 선택한 다음, **구매** 를 선택합니다.

## <a name="review-deployed-resources"></a>배포된 리소스 검토

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 만든 Redis Cache를 엽니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요 없으면 리소스 그룹을 삭제합니다. 그러면 리소스 그룹의 리소스가 삭제됩니다.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Cache for Redis를 배포하는 Azure Resource Manager 템플릿을 만드는 방법에 대해 알아봅니다. Azure Cache for Redis를 사용하여 Azure 웹앱을 배포하는 Azure Resource Manager 템플릿을 만드는 방법에 대한 자세한 내용은 [템플릿을 사용하여 Azure Cache for Redis가 포함된 웹앱 만들기](./cache-web-app-arm-with-redis-cache-provision.md)를 참조하세요.
