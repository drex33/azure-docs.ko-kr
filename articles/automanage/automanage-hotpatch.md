---
title: Windows Server Azure Edition에 대한 핫패치(미리 보기)
description: Windows Server Azure Edition에 대한 핫패치의 작동 방식 및 사용 방법을 알아봅니다.
author: ju-shim
ms.service: virtual-machines
ms.subservice: hotpatch
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: jushiman
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 32441732385ebd16c2edaa9b77a75382051b6ee1
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131502885"
---
# <a name="hotpatch-for-new-virtual-machines-preview"></a>새 가상 머신에 대한 핫패치(미리 보기)

> [!IMPORTANT]
> Hotpatch은 현재 공개 미리 보기로 제공 됩니다. 아래에 설명된 핫패치 기능을 사용하려면 옵트인 절차가 필요합니다.
> 이 미리 보기는 서비스 수준 약정 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

핫패칭은 설치 후 다시 부팅할 필요가 없는 지원되는 _Windows Server Azure 버전_ VM(가상 머신)에 업데이트를 설치하는 새로운 방법입니다. 이 문서에서는 다음과 같은 이점을 제공하는 지원되는 _Windows Server Azure 버전_ VM에 대한 핫패치 정보를 다룹니다.
* 더 적은 다시 부팅으로 인해 워크로드 영향을 줄입니다.
* Azure 업데이트 관리자를 사용하여 패키지 크기가 더 작아지고, 더 빠르게 설치되며, 패치 오케스트레이션이 더 쉬워지므로 업데이트를 더 빠르게 배포할 수 있습니다.
* 핫패치 업데이트 패키지는 다시 부팅하지 않고 더 빠르게 설치되는 Windows 보안 업데이트로 범위가 지정되므로 보호 기능이 향상됩니다.

## <a name="how-hotpatch-works"></a>핫패치 작동 방식

핫패치는 먼저 Windows 업데이트 최신 누적 업데이트를 사용하여 기준을 설정하여 작동합니다. 핫패치는 해당 기준을 기반으로 하는 해당 월의 두 번째 화요일에 정기적으로 릴리스됩니다. 핫패치에는 다시 부팅할 필요가 없는 업데이트가 포함됩니다. 정기적으로(3개월마다 시작) 새로운 최신 누적 업데이트를 사용하여 기준이 새로 고쳐집니다.

:::image type="content" source="media\automanage-hotpatch\hotpatch-sample-schedule.png" alt-text="핫패치 샘플 일정.":::

**계획된 기준** 과 **계획되지 않은 기준** 이라는 두 가지 기준 유형이 있습니다.
*  **계획된 기준** 은 핫패치 릴리스가 포함된 일반 주기에 따라 릴리스됩니다.  계획된 기준에는 해당 월에 대한 유사 _최신 누적 업데이트_ 의 모든 업데이트가 포함되며 다시 부팅해야 합니다.
    * 위의 샘플 일정은 역년 기준 계획된 기준 릴리스 4개(다이어그램에서 총 5개)와 8개의 핫패치 릴리스를 보여 줍니다.
* **계획되지 않은 기준** 은 중요한 업데이트(예: 제로 데이 픽스)가 릴리스되고 특정 업데이트를 핫패치로 릴리스할 수 없을 때 릴리스됩니다.  계획되지 않은 기준이 릴리스되는 경우 핫패치 릴리스는 해당 월의 계획되지 않은 기준으로 교체됩니다.  계획되지 기준에도 해당 월에 대한 유사 _최신 누적 업데이트_ 의 모든 업데이트가 포함되며 마찬가지로 다시 부팅해야 합니다.
    * 위의 샘플 일정은 해당 월에 대한 핫패치 릴리스를 대체하는 계획되지 않은 두 개의 기준을 보여 줍니다. 1년에 계획되지 않은 기준의 실제 수는 사전에 알려지지 않습니다.

## <a name="regional-availability"></a>국가별 가용성
핫패치는 모든 글로벌 Azure 지역에서 미리 보기 상태로 사용할 수 있습니다. Azure Government 지역은 미리 보기에서 지원되지 않습니다.

## <a name="how-to-get-started"></a>시작하는 방법

> [!NOTE]
> 미리 보기 단계 중에는 [이 링크](https://aka.ms/ws2022ae-portal-preview)를 사용해야 Azure Portal을 시작할 수 있습니다.

새 VM에서 핫패치 사용을 시작하려면 다음 단계를 수행합니다.
1.  미리 보기 액세스 사용
    * 구독 당 일회성 미리 보기 액세스를 사용해야 합니다.
    * 아래의 ' 미리 보기 액세스 사용 ' 섹션에 설명 된 대로 API, PowerShell 또는 CLI를 통해 미리 보기 액세스를 사용 하도록 설정할 수 있습니다.
1.  Azure Portal에서 새 VM 만들기를 시작 합니다.
    * 미리 보기 중에는 [이 링크](https://aka.ms/ws2022ae-portal-preview)를 사용하여 시작해야 합니다.
1.  VM을 만드는 동안 세부 정보 제공
    * 이미지 드롭다운에서 지원 되는 _Windows Server Azure 버전_ 이미지가 선택 되어 있는지 확인 합니다.  [이 가이드](automanage-windows-server-services-overview.md#getting-started-with-windows-server-azure-edition) 를 사용 하 여 지원 되는 이미지를 확인 합니다.
    * 관리 탭의 ' 게스트 OS 업데이트 ' 섹션에서 ' hotpatch 사용 ' 확인란을 선택 하 여 미리 보기 상태에서 hotpatch를 평가 합니다.  패치 오케스트레이션 옵션은 ' 오케스트레이션 '로 설정 됩니다. 
    * ' Azure Automanage ' 섹션의 관리 탭에서 ' Azure Automanage environment '의 ' 개발/테스트 ' 또는 ' 프로덕션 '을 선택 하 여 미리 보기 중에 자동 관리 컴퓨터 모범 사례를 평가 합니다.
    
1. 새 VM 만들기

## <a name="enabling-preview-access"></a>미리 보기 액세스 사용 설정

### <a name="rest-api"></a>REST API

다음 예제에서는 구독에 미리 보기를 사용하도록 설정하는 방법을 설명합니다.

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/ InGuestHotPatchVMPreview/register?api-version=2015-12-01`
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview/register?api-version=2015-12-01`
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestPatchVMPreview/register?api-version=2015-12-01`
```

기능 등록에는 최대 15분이 걸립니다. 등록 상태를 확인하는 방법은 다음과 같습니다.

```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestHotPatchVMPreview?api-version=2015-12-01`
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview?api-version=2015-12-01`
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestPatchVMPreview?api-version=2015-12-01`
```

기능이 구독에 등록되면 변경 내용을 Compute 리소스 공급자로 전파하여 옵트인 프로세스를 완료합니다.

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2019-12-01`
```

### <a name="azure-powershell"></a>Azure PowerShell

```Register-AzProviderFeature``` cmdlet을 사용하여 구독에 대한 미리 보기를 사용하도록 설정합니다.

``` PowerShell
Register-AzProviderFeature -FeatureName InGuestHotPatchVMPreview -ProviderNamespace Microsoft.Compute
Register-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
Register-AzProviderFeature -FeatureName InGuestPatchVMPreview -ProviderNamespace Microsoft.Compute
```

기능 등록은 최대 15분까지 걸릴 수 있습니다. 등록 상태를 확인하는 방법은 다음과 같습니다.

``` PowerShell
Get-AzProviderFeature -FeatureName InGuestHotPatchVMPreview -ProviderNamespace Microsoft.Compute
Get-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
Get-AzProviderFeature -FeatureName InGuestPatchVMPreview -ProviderNamespace Microsoft.Compute
```

기능이 구독에 등록되면 변경 내용을 Compute 리소스 공급자로 전파하여 옵트인 프로세스를 완료합니다.

``` PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="azure-cli"></a>Azure CLI

```az feature register```를 사용하여 구독에 대한 미리 보기를 사용하도록 설정합니다.

```
az feature register --namespace Microsoft.Compute --name InGuestHotPatchVMPreview
az feature register --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview
az feature register --namespace Microsoft.Compute --name InGuestPatchVMPreview
```

기능 등록은 최대 15분까지 걸릴 수 있습니다. 등록 상태를 확인하는 방법은 다음과 같습니다.
```
az feature show --namespace Microsoft.Compute --name InGuestHotPatchVMPreview
az feature show --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview
az feature show --namespace Microsoft.Compute --name InGuestPatchVMPreview
```

기능이 구독에 등록되면 변경 사항을 Compute 리소스 공급자로 전파하여 옵트인 프로세스를 완료합니다.

```
az provider register --namespace Microsoft.Compute
```

## <a name="patch-installation"></a>패치 설치

미리 보기 중에는 [자동 VM 게스트 패치가](../virtual-machines/automatic-vm-guest-patching.md)가 지원되는 _Windows Server Azure 버전_ 이미지를 사용하여 만들어진 모든 VM에 대해 자동으로 사용하도록 설정됩니다. 자동 VM 게스트 패치를 사용하는 경우:
* 중요 또는 보안으로 분류된 패치가 자동으로 다운로드되어 VM에 적용됩니다.
* 패치는 VM의 표준 시간대에서 사용량이 적은 시간에 적용됩니다.
* 패치 오케스트레이션은 Azure에서 관리되며 [가용성 우선 원칙](../virtual-machines/automatic-vm-guest-patching.md#availability-first-updates)에 따라 패치가 적용됩니다.
* 패치 실패를 검색하기 위해 플랫폼 상태 신호를 통해 결정되는 가상 머신 상태를 모니터링합니다.

### <a name="how-does-automatic-vm-guest-patching-work"></a>자동 VM 게스트 패치는 어떻게 작동하나요?

VM에서 [자동 VM 게스트 패치](../virtual-machines/automatic-vm-guest-patching.md)를 사용하도록 설정하면 사용 가능한 중요 및 보안 패치가 자동으로 다운로드되고 적용됩니다. 이 프로세스는 새 패치가 릴리스될 때마다 매월 자동으로 시작됩니다. 패치 평가 및 설치는 자동으로 이루어지며, 필요에 따라 이 프로세스 중 VM을 다시 부팅해야 합니다.

지원되는 _Windows Server Azure 버전_ VM에서 핫패치를 사용하는 경우: Azure 버전 VM, 대부분의 월간 보안 업데이트는 다시 부팅이 필요하지 않은 핫패치로 제공됩니다. 계획되거나 계획되지 않은 기준 월에 전송된 최신 누적 업데이트의 경우 VM을 다시 부팅해야 합니다. 정기적으로 이용할 수 있는 추가 중요 또는 보안 패치의 경우 VM을 다시 부팅해야 할 수도 있습니다.

VM은 해당 VM에 대해 적용 가능한 패치를 결정하기 위해 며칠마다 자동으로, 30일 동안 여러 번 평가됩니다. 이러한 자동 평가를 통해 누락된 패치를 언제 가장 빠르게 적용할 수 있는지 검색됩니다.

패치는 [가용성 우선 원칙](../virtual-machines/automatic-vm-guest-patching.md#availability-first-updates)에 따라 월별 패치 릴리스 30일 이내에 설치됩니다. 패치는 VM의 표준 시간대에 따라 VM 사용률이 낮은 시간에만 설치됩니다. 패치를 자동으로 설치하려면 사용량이 많지 않은 시간 중 VM이 실행 중이어야 합니다. 정기적으로 평가하는 동안 VM의 전원이 꺼져 있는 경우 VM이 평가되고, VM의 전원이 켜지면 다음 주기 평가 중 적용 가능한 패치가 자동으로 설치됩니다. 다음 주기적 평가는 일반적으로 며칠 이내에 발생합니다.

중요 또는 보안으로 분류되지 않은 정의 업데이트 및 기타 패치는 자동 VM 게스트 패치를 통해 설치되지 않습니다.

## <a name="understanding-the-patch-status-for-your-vm"></a>VM에 대한 패치 상태 이해

VM에 대한 패치 상태를 보려면 Azure Portal에서 VM에 대한 **게스트 + 호스트 업데이트** 섹션으로 이동합니다. **게스트 OS 업데이트** 섹션에서 '핫패치로 이동(미리 보기)'을 클릭하여 VM에 대한 최신 패치 상태를 확인합니다.

이 화면에서는 VM에 대한 핫패치 상태를 표시할 수 있습니다. 또한 설치되지 않은 VM에 대해 사용 가능한 패치가 있는지 검토할 수 있습니다. 위의 '패치 설치' 섹션에 설명된 대로 모든 보안 및 중요 업데이트는 [자동 VM 게스트 패치](../virtual-machines/automatic-vm-guest-patching.md)를 사용하여 VM에 자동으로 설치되며 추가 작업이 필요하지 않습니다. 다른 업데이트 분류의 패치는 자동으로 설치되지 않습니다. 대신 이러한 패치는 '호환성 업데이트' 탭의 사용 가능한 패치 목록에서 볼 수 있습니다. '업데이트 기록'을 통해 VM에서 업데이트 배포 기록을 볼 수도 있습니다. 최근 30일 동안의 업데이트 기록이 패치 설치 세부 정보와 함께 표시됩니다.


:::image type="content" source="media\automanage-hotpatch\hotpatch-management-ui.png" alt-text="핫패치 관리.":::

자동 VM 게스트 패치를 사용하면 VM에서 주기적으로 사용 가능한 업데이트를 자동으로 평가합니다. 이러한 정기 평가를 통해 사용 가능한 패치가 검색되는지 확인합니다. 위의 업데이트 화면에서 마지막 평가 시간을 포함하여 평가 결과를 볼 수 있습니다. '지금 평가' 옵션을 사용하여 언제든지 VM에 대한 주문형 패치 평가를 트리거하고 평가를 완료한 후에 결과를 검토하도록 선택할 수도 있습니다.

주문형 평가와 마찬가지로 '지금 업데이트 설치' 옵션을 사용하여 VM에 대한 주문형 패치를 설치할 수도 있습니다. 여기서 특정 패치 분류에 따라 모든 업데이트를 설치하도록 선택할 수 있습니다. 개별 기술 자료 문서 목록을 제공하여 포함하거나 제외할 업데이트를 지정할 수도 있습니다. 요청 시에 설치된 패치는 가용성 우선 원칙을 사용하여 설치되지 않으며, 업데이트 설치를 위한 더 많은 재부팅 및 VM 가동 중지 시간이 필요할 수 있습니다.

## <a name="supported-updates"></a>지원되는 업데이트

핫패치는 Windows 보안 업데이트를 포함하며, 일반(핫패치 외) Windows 업데이트 채널에 생성된 보안 업데이트 콘텐츠의 패리티를 유지 관리합니다.

핫패치를 사용하도록 설정된 지원되는 _Windows Server Azure 버전_ VM을 실행하려면 몇 가지 중요한 사항을 고려해야 합니다. 핫패치 프로그램에 포함되지 않은 업데이트를 설치하려면 다시 부팅해야 합니다. 새 기준을 설치한 후에도 정기적으로 다시 부팅해야 합니다. 이러한 재부팅을 통해 VM은 최신 누적 업데이트에 포함된 비보안 패치와 동기화된 상태로 유지됩니다.
* 현재 핫패치 프로그램에 포함되지 않은 패치에는 Windows 용으로 릴리스된 비보안 업데이트 및 비 Windows 업데이트(예: .NET 패치)가 포함됩니다.  이러한 유형의 패치는 기준 월에 설치해야 하며 다시 부팅해야 합니다.

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="what-is-hotpatching"></a>핫패칭이란?

* 핫패칭은 Azure에서 설치 후 다시 부팅할 필요가 없는 지원되는 _Windows Server Azure 버전_ VM에 업데이트를 설치하는 새로운 방법입니다. 프로세스를 다시 시작할 필요 없이 실행 중인 프로세스의 메모리 내 코드를 패치하는 방식으로 작동합니다.

### <a name="how-does-hotpatching-work"></a>핫패칭 작동 방식

* 핫패칭은 Windows 업데이트 최신 누적 업데이트로 기준을 설정한 뒤, 적용하는데 재부팅이 필요하지 않은 업데이트 기준에 따라 빌드하는 방식으로 작동합니다.  기준은 새 누적 업데이트를 사용하여 주기적으로 업데이트됩니다. 누적 업데이트에는 모든 보안 및 품질 업데이트가 포함되며, 다시 부팅해야 합니다.

### <a name="why-should-i-use-hotpatch"></a>핫패치를 사용해야하는 이유

* 지원되는 _Windows Server Azure 버전_ 이미지에서 핫패치를 사용하는 경우 VM은 더 높은 가용성(더 적은 수의 재부팅)을 제공하고 더 빠른 업데이트(프로세스를 다시 시작하지 않고 더 빠르게 설치되는 더 작은 패키지)를 제공합니다. 이 프로세스를 통해 항상 최신 상태로 유지되는 VM이 생성됩니다.

### <a name="what-types-of-updates-are-covered-by-hotpatch"></a>핫패치에서 적용되는 업데이트 유형

* 핫패치에는 현재 Windows 보안 업데이트가 포함되어 있습니다.

### <a name="when-will-i-receive-the-first-hotpatch-update"></a>첫 번째 핫패치 업데이트는 언제 받게 되나요?

* 핫패치 업데이트는 일반적으로 매월 두 번째 화요일에 릴리스됩니다. 자세한 내용은 아래를 참조하세요.

### <a name="what-will-the-hotpatch-schedule-look-like"></a>핫패치 일정은 어떻게 이루어지나요?

* 핫패칭은 Windows 업데이트 최신 누적 업데이트로 기준을 설정한 뒤, 매달 릴리스되는 핫패치 업데이트 기준에 따라 빌드하는 방식으로 작동합니다.  미리 보기 중에는 3개월마다 시작하는 기준이 릴리스됩니다. 연간 3개월 일정(제로 데이 픽스로 인해 계획되지 않은 기준 예제 포함)의 예는 아래 이미지를 참조하세요.

    :::image type="content" source="media\automanage-hotpatch\hotpatch-sample-schedule.png" alt-text="핫패치 샘플 일정.":::

### <a name="are-reboots-still-needed-for-a-vm-enrolled-in-hotpatch"></a>핫패치에 등록된 VM의 경우에도 다시 부팅해야 하나요?

* 핫패치 프로그램에 포함되지 않은 업데이트를 설치하는 경우에도 다시 부팅해야 하며, 기준(최신 누적 업데이트 Windows 업데이트)이 설치된 후 주기적으로 해야 합니다. 이렇게 다시 부팅하면 VM이 누적 업데이트에 포함된 모든 패치와 동기화된 상태로 유지됩니다. 다시 부팅해야 하는 기준은 3개월 주기로 시작되며, 시간이 지남에 따라 증가합니다.

### <a name="are-my-applications-affected-when-a-hotpatch-update-is-installed"></a>핫패치 업데이트가 설치될 때 애플리케이션이 영향을 받습니까?

* 핫패치는 프로세스를 다시 시작할 필요없이 실행중인 프로세스의 메모리 내 코드를 패치하므로 애플리케이션은 패치 프로세스의 영향을 받지 않습니다. 이는 패치 자체의 잠재적인 성능 및 기능 영향과 구분됩니다.

### <a name="can-i-turn-off-hotpatch-on-my-vm"></a>내 VM에서 핫패치를 끌 수 있나요?

* Azure Portal를 통해 VM에서 핫패치를 끌 수 있습니다.  핫패치를 끄면 핫패치에서 VM 등록을 취소하여 Windows Server에 대한 일반적인 업데이트 동작으로 VM을 되돌립니다.  VM에서 핫패치의 등록을 취소한 후에는 다음 핫패치 기준이 릴리스될 때 해당 VM을 다시 등록할 수 있습니다.

### <a name="can-i-upgrade-from-my-existing-windows-server-os"></a>기존 Windows Server OS에서 업그레이드할 수 있나요?

* 예. 기존 버전의 Windows Server(즉, Windows Server 2016 또는 Windows Server 2019 버전)에서 Windows Server 2022 Datacenter: Azure 버전으로의 업그레이드가 지원됩니다. 

### <a name="can-i-use-hotpatch-for-production-workloads-during-the-preview"></a>미리 보기 중에 프로덕션 작업에 핫패치를 사용할 수 있나요?

* 미리 보기는 테스트 목적으로만 제공되며 프로덕션 환경에서 사용하기 위한 것이 아닙니다.

### <a name="will-i-be-charged-during-the-preview"></a>미리 보기 기간 중에 요금이 청구되나요?

* _Windows Server Azure 버전_ 에 대한 라이선스는 미리 보기 기간 동안 무료입니다. 그러나 VM에 설정된 기본 인프라(스토리지, 컴퓨팅, 네트워킹 등)의 비용은 여전히 구독에 청구됩니다.

### <a name="how-can-i-get-troubleshooting-support-for-hotpatching"></a>핫패칭에 대한 문제 해결 지원을 받으려면 어떻게 해야 하나요?

* [기술 지원사례 티켓](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)을 입력할 수 있습니다. 서비스 옵션에 대해 계산에서 Windows를 실행하는 **Virtual Machine** 를 검색하고 선택합니다. 문제 유형에 대해 **Azure 기능** 을 선택하고 문제 하위 유형에 대해 **자동 VM 게스트 패치** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

* [Azure 업데이트 관리](../automation/update-management/overview.md) 에 대 한 자세한 정보
* [자동 VM 게스트 패치](../virtual-machines/automatic-vm-guest-patching.md) 에 대 한 자세한 정보
* [Windows 서버에 대 한 automanage](automanage-windows-server-services-overview.md) 에 대해 자세히 알아보기