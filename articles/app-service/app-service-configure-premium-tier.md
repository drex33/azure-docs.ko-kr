---
title: PremiumV3 계층 구성
description: 새 PremiumV3 가격 책정 계층으로 스케일링하여 Azure App Service에서 웹, 모바일, API 앱 성능을 향상하는 방법에 대해 알아봅니다.
keywords: 앱 서비스, Azure 앱 서비스, 규모, 확장성, 앱 서비스 계획, 앱 서비스 비용
ms.assetid: ff00902b-9858-4bee-ab95-d3406018c688
ms.topic: article
ms.date: 10/01/2020
ms.custom: seodec18, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 89d5f45d7f8e05c0751d6dc4c1799252453bed0d
ms.sourcegitcommit: 93c7420c00141af83ed3294923b4826dd4dc6ff2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133436212"
---
# <a name="configure-premiumv3-tier-for-azure-app-service"></a>Azure App Service에 대한 PremiumV3 계층 구성

새 **PremiumV3** 가격 책정 계층은 더 빠른 프로세서와 SSD 스토리지를 제공하고, 기존 가격 책정 계층의 메모리 대 코어 비율을 4배로 만들어줍니다(**PremiumV2** 계층의 2배). 성능상의 이점 덕분에 더 적은 인스턴스에서 앱을 실행하여 비용을 절감할 수 있습니다. 이 문서에서는 **PremiumV3** 계층에서 앱을 만들거나 **PremiumV3** 계층으로 앱을 스케일 업하는 방법에 대해 알아봅니다.

## <a name="prerequisites"></a>사전 요구 사항

앱을 **PremiumV3** 로 스케일 업하려면 **PremiumV3** 보다 낮은 가격 책정 계층에서 실행되는 Azure App Service 앱이 있어야 하고, PremiumV3를 지원하는 App Service 배포에서 앱이 실행되고 있어야 합니다.

<a name="availability"></a>

## <a name="premiumv3-availability"></a>PremiumV3 가용성

**PremiumV3** 계층은 Windows 컨테이너와 Linux 컨테이너를 포함하여 네이티브 및 사용자 지정 컨테이너 모두에 사용할 수 있습니다.

> [!NOTE]
> 미리 보기 기간 동안 **Premium Container** 계층에서 실행되는 모든 Windows 컨테이너는 계속해서 그대로 작동하지만 **Premium Container** 계층은 계속 미리 보기로 유지됩니다. **PremiumV3** 계층은 **Premium Container** 계층의 공식 대체 계층입니다. 

**PremiumV3** 는 일부 Azure 지역에서 사용할 수 있으며 다른 지역에서의 가용성은 계속 추가됩니다. 해당 지역에서 사용할 수 있는지 확인하려면 [Azure Cloud Shell](../cloud-shell/overview.md)에서 다음 Azure CLI 명령을 실행합니다.

```azurecli-interactive
az appservice list-locations --sku P1V3
```

<a name="create"></a>

## <a name="create-an-app-in-premiumv3-tier"></a>PremiumV3 계층에서 앱 만들기

App Service 앱의 가격 책정 계층은 해당 앱이 실행되는 [App Service 계획](overview-hosting-plans.md)에 정의되어 있습니다. App Service 계획은 단독으로 또는 앱 만들기의 일부로 만들 수 있습니다.

<a href="https://portal.azure.com" target="_blank">Azure Portal</a>에서 App Service 계획을 구성하는 경우 **가격 책정 계층** 을 선택합니다. 

**프로덕션** 을 선택하고 **P1V3**, **P2V3** 또는 **P3V3** 를 선택한 다음 **적용** 을 클릭합니다.

![앱에 대해 권장되는 가격 책정 계층을 보여 주는 스크린샷.](media/app-service-configure-premium-tier/scale-up-tier-select.png)

> [!IMPORTANT] 
> **P1V3**, **P2V3**, **P3V3** 가 옵션으로 표시되지 않거나, 해당 옵션이 회색으로 표시되는 경우 **PremiumV3** 는 App Service 요금제를 포함하는 기본 App Service 배포에서 사용할 수 없습니다. 자세한 내용은 [지원되지 않는 리소스 그룹 및 지역 조합에서 강화](#unsupported)를 참조합니다.

## <a name="scale-up-an-existing-app-to-premiumv3-tier"></a>기존 앱을 PremiumV3 계층으로 스케일 업

기존 앱을 **PremiumV3** 계층으로 스케일링하기 전에 **PremiumV3** 를 사용할 수 있는지 확인합니다. 자세한 내용은 [PremiumV3 가용성](#availability)을 참조하세요. 사용할 수 없는 경우 [지원되지 않는 리소스 그룹 및 지역 조합에서 강화](#unsupported)를 참조합니다.

호스팅 환경에 따라 강화하는 데 추가 단계가 필요할 수 있습니다. 

<a href="https://portal.azure.com" target="_blank">Azure Portal</a>에서 App Service 앱 페이지를 엽니다.

App Service 앱 페이지의 왼쪽 탐색 영역에서 **강화(App Service 계획)** 를 선택합니다.

![App Service 요금제를 스케일 업하는 방법을 보여 주는 스크린샷.](media/app-service-configure-premium-tier/scale-up-tier-portal.png)

**프로덕션** 을 선택하고 **P1V3**, **P2V3** 또는 **P3V3** 를 선택한 다음 **적용** 을 클릭합니다.

![앱에 대해 권장되는 가격 책정 계층을 보여 주는 스크린샷.](media/app-service-configure-premium-tier/scale-up-tier-select.png)

작업이 성공적으로 완료되면 앱의 개요 페이지에 이제 앱이 **PremiumV3** 계층에 있다고 표시됩니다.

![앱의 개요 페이지에서 PremiumV3 가격 책정 계층을 보여 주는 스크린샷.](media/app-service-configure-premium-tier/finished.png)

### <a name="if-you-get-an-error"></a>오류가 발생하는 경우

일부 App Service 요금제는 기본 App Service 배포가 PremiumV3를 지원하지 않는 경우 PremiumV3 계층으로 스케일 업할 수 없습니다. 자세한 내용은 [지원되지 않는 리소스 그룹 및 지역 조합에서 강화](#unsupported)를 참조합니다.

<a name="unsupported"></a>

## <a name="scale-up-from-an-unsupported-resource-group-and-region-combination"></a>지원되지 않는 리소스 그룹 및 지역 조합에서 강화

**PremiumV3** 를 사용할 수 없는 App Service 배포에서 앱이 실행되는 경우 또는 현재 **PremiumV3** 를 지원하지 않는 지역에서 앱이 실행되는 경우 **PremiumV3** 를 활용하기 위해서는 앱을 다시 배포해야 합니다.  다음 두 가지 옵션을 사용할 수 있습니다.

- 새 리소스 그룹에 새 App Service 요금제로 앱을 만듭니다. App Service 요금제를 만들 때 **PremiumV3** 계층을 선택합니다. 이 단계를 통해 App Service 요금제가 **PremiumV3** 를 지원하는 배포 단위로 배포됩니다. 그런 다음, 새로 만든 앱에 애플리케이션 코드를 다시 배포합니다. App Service 요금제를 하위 계층으로 스케일 다운하여 비용을 절감하는 경우에도 배포 단위에서 지원하므로 항상 **PremiumV3** 로 다시 스케일 업할 수 있습니다.
- 앱이 이미 기존 **Premium** 계층에서 실행되는 경우 모든 앱 설정, 연결 문자열, 배포 구성을 사용하여 앱을 **PremiumV3** 를 사용하는 새 App Service 요금제의 새 리소스 그룹으로 복제할 수 있습니다.

    ![앱을 복제하는 방법을 보여 주는 스크린샷.](media/app-service-configure-premium-tier/clone-app.png)

    **앱 복제** 페이지에서 원하는 지역의 **PremiumV3** 를 사용하여 App Service 요금제를 만들고 복제하려는 앱 설정 및 구성을 지정할 수 있습니다.

## <a name="moving-from-premium-container-to-premium-v3-sku"></a>Premium Container에서 Premium V3 SKU로 이동

Premium Container SKU 미리 보기를 사용하는 앱이 있고 새로운 Premium V3 SKU로 이동하려는 경우 **PremiumV3** 를 활용하려면 앱을 다시 배포해야 합니다. 이 작업을 하려면 [지원되지 않는 리소스 그룹 및 지역 조합에서 스케일 업](#scale-up-from-an-unsupported-resource-group-and-region-combination)의 첫 번째 옵션을 참조하세요

## <a name="automate-with-scripts"></a>스크립트를 사용하여 자동화

[Azure CLI](/cli/azure/install-azure-cli) 또는 [Azure PowerShell](/powershell/azure/)을 통해 스크립트를 사용하여 **PremiumV3** 계층에서 앱 만들기를 자동화할 수 있습니다.

### <a name="azure-cli"></a>Azure CLI

다음 명령으로 _P1V3_ 에서 App Service 요금제를 만듭니다. Cloud Shell에서 실행할 수 있습니다. `--sku`의 옵션은 P1V3, _P2V3_, _P3V3_ 입니다.

```azurecli-interactive
az appservice plan create \
    --resource-group <resource_group_name> \
    --name <app_service_plan_name> \
    --sku P1V3
```

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

다음 명령으로 _P1V3_ 에서 App Service 요금제를 만듭니다. `-WorkerSize`에 대한 옵션은 _Small_, _Medium_ 및 _Large_ 입니다.

```powershell
New-AzAppServicePlan -ResourceGroupName <resource_group_name> `
    -Name <app_service_plan_name> `
    -Location <region_name> `
    -Tier "PremiumV3" `
    -WorkerSize "Small"
```

## <a name="more-resources"></a>추가 리소스

[Azure에서 앱 스케일 업](manage-scale-up.md)
[수동 혹은 자동으로 인스턴스 개수 스케일링](../azure-monitor/autoscale/autoscale-get-started.md)
