---
title: 가상 머신용 Azure Automanage
description: 가상 머신용 Azure Automanage에 대해 알아봅니다.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: deanwe
ms.custom: references_regions
ms.openlocfilehash: 3e406f9ed38aa9360da3fed2ce256654ad7c10c0
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129707524"
---
# <a name="preview-azure-automanage-for-virtual-machines"></a>미리 보기: 가상 머신용 Azure Automanage

이 문서에서는 다음과 같은 이점을 제공하는 가상 머신용 Azure Automanage에 대해 설명합니다.

- 가상 머신을 지능적으로 온보딩하여 Azure 서비스 모범 사례 선택
- Azure 모범 사례에 따라 각 서비스를 자동으로 구성
- 드리프트를 모니터링하고 검색된 경우 수정
- 간단한 환경 제공(포인트, 클릭, 설정, 제거)

## <a name="overview"></a>개요

가상 머신용 Azure Automanage는 Azure에서 가상 머신에 유용한 특정 서비스를 검색하고 온보딩하는 방법과 구성하는 방법을 파악하지 않아도 서비스입니다. 이러한 서비스는 Azure 모범 사례 서비스로 간주되며, 가상 머신에 대한 안정성, 보안 및 관리를 개선하는 데 도움이 됩니다. 예제 서비스에는 [Azure 업데이트 관리](../automation/update-management/overview.md) 및 [Azure Backup](../backup/backup-overview.md)이 포함됩니다.

Azure Automanage에 가상 머신을 온보딩한 후에는 각 모범 사례 서비스가 권장 설정으로 구성됩니다. 모범 사례는 각 서비스마다 다릅니다. 예를 들어 Azure Backup은 하루에 한 번 가상 머신을 백업하고 6개월의 보존 기간을 설정하는 것이 가장 좋습니다.

또한 Azure Automanage는 자동으로 드리프트를 모니터링하고 검색된 경우 수정합니다. 즉, 가상 머신이 Azure Automanage에 온보딩되면 Azure 모범 사례에 따라 구성할 수 있을 뿐만 아니라 머신을 모니터링하여 전체 수명 주기 동안 이러한 모범 사례를 계속 준수하도록 할 수도 있습니다. 가상 머신이 드리프트하거나 이러한 사례에서 벗어나는 경우(예: 서비스가 오프보딩되는 경우)에는 이를 수정하고 머신을 필요한 상태로 되돌릴 수 있습니다.

Automanage는 VM이 있는 지리적 위치 외부에서 고객 데이터를 저장/처리하지 않습니다. 동남아시아 지역의 Automanage는 동남아시아 외부에서 데이터를 저장/처리하지 않습니다.

> [!NOTE]
> 자동 관리는 Azure 가상 머신뿐만 아니라 Azure Arc 지원 서버에서도 사용하도록 설정할 수 있습니다. 현재 미국 정부 클라우드에서는 Automanage를 사용할 수 없습니다.

## <a name="prerequisites"></a>사전 요구 사항

가상 머신에서 Azure Automanage를 사용하기 전에 고려해야 할 몇 가지 필수 구성 요소가 있습니다.

- 지원되는 [Windows Server 버전](automanage-windows-server.md#supported-windows-server-versions) 및 [Linux 배포판](automanage-linux.md#supported-linux-distributions-and-versions)
- VM은 지원되는 지역에 있어야 합니다(아래 참조).
- 사용자에게 올바른 사용 권한이 있어야 합니다(아래 참조).
- 지금은 Automanage에서 샌드박스 구독을 지원하지 않습니다.
- Automanage는 현재 Windows 10을 지원하지 않습니다.

### <a name="supported-regions"></a>지원되는 지역
Automanage는 다음 지역에 있는 VM만 지원합니다.
* 서유럽
* 북유럽
* 미국 중부
* 미국 동부
* 미국 동부 2
* 미국 서부
* 미국 서부 2
* 캐나다 중부
* 미국 중서부
* 미국 중남부
* 일본 동부
* 영국 남부
* 오스트레일리아 동부
* 오스트레일리아 남동부
* 동남아시아

### <a name="required-rbac-permissions"></a>필요한 RBAC 권한
새 Automanage 계정으로 Automanage를 사용하도록 설정할지 여부에 따라 계정에는 약간 다른 RBAC 역할이 필요합니다.

새 Automanage 계정으로 Automanage를 사용하도록 설정하는 경우
* VM을 포함하는 구독에 대한 **소유자** 역할 _**또는**_
* VM을 포함하는 구독에 대한 **기여자** 및 **사용자 액세스 관리자** 역할

기존 Automanage 계정으로 Automanage를 사용하도록 설정하는 경우
* VM을 포함하는 리소스 그룹에 대한 **기여자** 역할

Automanage 계정에는 자동 관리되는 머신에 대한 작업을 수행할 수 있는 **기여자** 및 **리소스 정책 기여자** 권한이 부여됩니다.

> [!NOTE]
> 다른 구독의 작업 영역에 연결된 VM에서 Automanage를 사용하려면 각 구독에 대해 위에 설명된 사용 권한이 있어야 합니다.

## <a name="participating-services"></a>참여 서비스

:::image type="content" source="media\automanage-virtual-machines\intelligently-onboard-services-1.png" alt-text="지능적으로 서비스를 온보딩합니다.":::

참여 Azure 서비스 및 지원되는 환경의 전체 목록은 다음을 참조하세요.
- [Linux용 Automanage](automanage-linux.md)
- [Windows Server용 Automanage](automanage-windows-server.md)

 사용자는 이러한 참여 서비스에 자동으로 온보딩됩니다. 이는 [클라우드 채택 프레임워크](/azure/cloud-adoption-framework/manage/azure-server-management)에서 찾을 수 있는 모범 사례 백서에 필수적입니다.

이러한 모든 서비스에 대해 자동으로 온보딩하고, 자동 구성하고, 드리프트가 있는지 모니터링하고, 드리프트가 검색되면 해결합니다.


## <a name="enabling-automanage-for-vms-in-azure-portal"></a>Azure Portal에서 VM용 Automanage 사용

Azure Portal에서 기존 가상 머신에 대해 또는 새 가상 머신을 만들 때 Automanage를 사용하도록 설정할 수 있습니다. 이 프로세스에 대한 간략한 단계는 [가상 머신용 Automanage 빠른 시작](quick-create-virtual-machines-portal.md)을 참조하세요.

VM에 대해 Automanage를 처음 사용하는 경우 **Automanage – Azure 가상 머신 모범 사례** 에 대해 Azure Portal에서 검색할 수 있습니다. **기존 VM에서 사용** 을 클릭하고, 온보딩하려는 VM을 선택하고, **선택** 과 **사용** 을 차례로 클릭하면 됩니다.

이러한 서비스를 관리하기 위해 이 VM과 상호 작용해야 할 수 있는 유일한 경우는 VM을 수정하려고 했지만 실패한 경우입니다. VM이 성공적으로 수정되면 경고를 표시하지 않고 규정 준수 상태로 전환합니다. 자세한 내용은 [VM의 상태](#status-of-vms)를 참조하세요.

## <a name="enabling-automanage-for-vms-using-azure-policy"></a>Azure Policy를 사용하여 VM용 Automanage 사용
기본 제공 Azure Policy를 사용하여 대규모로 VM에서 Automanage를 사용하도록 설정할 수도 있습니다. 이 정책에는 DeployIfNotExists 효과가 있습니다. 즉, 정책 범위 내에 있는 적격한 모든 VM이 자동으로 Automanage VM 모범 사례로 온보딩됩니다.

정책에 대한 직접 링크는 [여기](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F270610db-8c04-438a-a739-e8e6745b22d3)를 참조하세요.

### <a name="how-to-apply-the-policy"></a>정책을 적용하는 방법
1. 정책 정의가 표시되어 있을 때 **할당** 단추를 클릭합니다.
1. 정책을 적용할 범위를 선택합니다(관리 그룹, 구독 또는 리소스 그룹일 수 있음).
1. **매개 변수** 에서 Automanage 계정, 구성 프로필 및 효과에 대한 매개 변수를 지정합니다(효과는 일반적으로 DeployIfNotExists여야 함).
    1. Automanage 계정이 없는 경우 [계정을 만들어야](./automanage-account.md) 합니다.
1. **재구성** 아래에서 "수정 작업을 클릭하십시오" 확인란을 선택합니다. Automanage에 대한 온보딩이 수행됩니다.
1. **검토 + 만들기** 를 클릭하고 모든 설정이 양호한지 확인합니다.
1. **만들기** 를 클릭합니다.

## <a name="environment-configuration"></a>환경 구성

가상 머신에 대해 Automanage를 사용하도록 설정하는 경우 환경이 필요합니다. 환경은 이 서비스의 기본 사항입니다. 머신을 온보딩하는 서비스와 해당 서비스의 구성 범위를 정의합니다.

### <a name="default-environments"></a>기본 환경

현재 사용할 수 있는 환경은 두 가지입니다.

- **개발/테스트** 환경은 개발/테스트 머신용으로 설계되었습니다.
- **프로덕션** 환경은 프로덕션용으로 설계되었습니다.

이러한 차이가 있는 이유는 실행하는 작업에 따라 특정 서비스가 권장되기 때문입니다. 예를 들어 프로덕션 머신에서는 Azure Backup에 자동으로 온보딩됩니다. 그러나 개발/테스트 머신은 일반적으로 비즈니스에 미치는 영향이 작기 때문에 개발/테스트 머신의 경우 백업 서비스는 불필요한 비용이 될 수 있습니다.

### <a name="customizing-an-environment-using-preferences"></a>기본 설정을 사용하여 환경 사용자 지정

사용자를 온보딩하는 표준 서비스 외에도 기본 설정의 특정 하위 집합을 구성할 수 있습니다. 이러한 기본 설정은 구성 옵션의 범위 내에서 허용됩니다. 예를 들어 Azure Backup의 경우 백업 빈도와 백업이 수행되는 요일을 지정할 수 있습니다.

> [!NOTE]
> 개발/테스트 환경에서는 VM을 전혀 백업하지 않습니다.

기본 설정을 통해 기본 환경의 설정을 조정할 수 있습니다. 기본 설정을 만드는 방법을 배우려면 [여기](virtual-machines-custom-preferences.md)를 참조하세요.

> [!NOTE]
> Automanage를 사용 하는 동안에는 VM의 환경 구성을 변경할 수 없습니다. 해당 VM에 대해 Automanage를 사용하지 않도록 설정한 다음, 필요한 환경 및 기본 설정으로 Automanage를 사용하도록 다시 설정해야 합니다.

참여 Azure 서비스의 전체 목록과 기본 설정이 지원되는 경우에 대해서는 여기를 참조하세요.
- [Linux용 Automanage](automanage-windows-server.md)
- [Windows Server용 Automanage](automanage-windows-server.md)


## <a name="automanage-account"></a>Automanage 계정

Automanage 계정은 자동화된 작업이 수행되는 ID 또는 보안 컨텍스트입니다. 일반적으로 Automanage 계정 옵션을 선택할 필요가 없지만 리소스의 자동화된 관리를 (두 시스템 관리자 간에) 분할하려는 위임 시나리오가 있는 경우 지원 흐름의 Automanage 계정 옵션을 사용하면 각 관리자에 대해 Azure ID를 정의할 수 있습니다.

Automanage 계정에 관한 정보 및 계정을 만드는 방법을 자세히 알아보려면 [Automanage 계정 문서](./automanage-account.md)를 참고하세요.

## <a name="status-of-vms"></a>VM의 상태

Azure Portal에서 모든 자동 관리형 VM이 나열되는 **Automanage – Azure 가상 머신 모범 사례** 페이지로 이동합니다. 여기에서 각 가상 머신의 전반적인 상태를 확인할 수 있습니다.

:::image type="content" source="media\automanage-virtual-machines\configured-status.png" alt-text="구성된 가상 머신 목록.":::

나열된 각 VM에 대해 이름, 환경, 구성 기본 설정, 상태, 운영 체제, 계정, 구독 및 리소스 그룹 세부 정보가 표시됩니다.

**상태** 열에는 다음 상태가 표시될 수 있습니다.
- *진행 중* - VM을 사용하도록 설정하고 구성하는 중입니다.
- *구성됨* - VM이 구성되고 드리프트가 검색되지 않습니다.
- *실패* - VM이 드리프트되었으며 수정할 수 없습니다.
- *보류 중* - VM은 현재 실행되고 있지 않으며 Automanage가 다음에 실행될 때 VM을 온보딩하거나 재구성하려고 합니다.

**상태** 가 *실패* 로 표시되는 경우 VM이 있는 리소스 그룹을 통해 배포 문제를 해결할 수 있습니다. **리소스 그룹** 으로 이동하여 리소스 그룹을 선택하고 **배포** 를 클릭한 다음, *실패* 상태를 오류 정보와 함께 확인합니다.


## <a name="disabling-automanage-for-vms"></a>VM용 Automanage를 사용하지 않도록 설정

특정 VM에서 1일 동안 Automanage를 사용하지 않도록 설정할 수 있습니다. 예를 들어, 머신에서 매우 중요한 보안 작업을 실행하고 있는 경우 Azure에서 일반적으로 수행하는 것보다 더 많이 머신을 잠가야 하므로 Azure 모범 사례 외에서 머신을 구성해야 합니다.

Azure Portal에서 이 작업을 수행하려면 모든 자동 관리형 VM이 나열되는 **Automanage – Azure 가상 머신 모범 사례** 페이지로 이동합니다. Automanage에서 사용하지 않도록 설정할 가상 머신 옆의 확인란을 선택하고 **Automanage 사용 안 함** 단추를 클릭합니다.

:::image type="content" source="media\automanage-virtual-machines\disable-step-1.png" alt-text="가상 머신에서 Automanage 사용 안 함":::

**사용 안 함** 에 동의하기 전에 결과 팝업의 메시지를 자세히 읽어 보세요.

> [!NOTE]
> VM에서 Automanage를 사용하지 않도록 설정하면 다음과 같은 동작이 발생합니다.
>
> - VM의 구성 및 온보딩된 서비스가 변경되지 않습니다.
> - 이러한 서비스에 의해 발생하는 모든 요금은 청구 가능하며 계속 발생합니다.
> - Automanage 드리프트 모니터링이 즉시 중지됩니다.


무엇보다도, 온보딩하여 구성한 모든 서비스에서 가상 머신을 오프보딩하지 않습니다. 따라서 이러한 서비스로 발생하는 모든 요금을 계속해서 청구할 수 있습니다. 필요한 경우에는 오프보딩해야 합니다. 모든 Automanage 동작이 즉시 중지됩니다. 예를 들어 더 이상 VM에서 드리프트를 모니터링하지 않습니다.

## <a name="automanage-and-azure-disk-encryption"></a>Automanage 및 Azure Disk Encryption
Automanage는 ADE(Azure Disk Encryption)가 사용 설정된 VM과 호환됩니다.

프로덕션 환경을 사용하는 경우 Azure Backup에 온보딩됩니다. ADE 및 Azure Backup을 성공적으로 사용하기 위한 한 가지 필수 구성 조건이 있습니다.
* ADE 지원 VM을 Automanage의 프로덕션 환경에 온보딩하기 전에 [이 문서](../backup/backup-azure-vms-encryption.md#before-you-start)의 **시작하기 전에** 섹션에 나와 있는 단계를 따라야 합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 사용자가 Azure 서비스 모범 사례에 대해 알고, 온보딩 및 구성하지 않아도 되는 방법을 가상 머신용 Automanage에서 제공한다는 것을 배웠습니다. 또한 가상 머신용 Automanage에 온보딩한 머신이 환경 설정에서 드리프트하는 경우 자동으로 규정 준수 상태로 다시 전환됩니다.

Azure Portal에서 가상 머신용 Automanage를 사용해 보세요.

> [!div class="nextstepaction"]
> [Azure Portal에서 가상 머신용 Automanage 사용](quick-create-virtual-machines-portal.md)
