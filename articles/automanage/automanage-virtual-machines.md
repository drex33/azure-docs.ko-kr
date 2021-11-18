---
title: 가상 머신용 Azure Automanage
description: 가상 머신용 Azure Automanage에 대해 알아봅니다.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 10/19/2021
ms.author: deanwe
ms.custom: references_regions
ms.openlocfilehash: 9730f95f6a384a03fffa0fb3a56b37ef3292b3ab
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132715476"
---
# <a name="preview-azure-automanage-for-machine-best-practices"></a>미리 보기: 컴퓨터 모범 사례에 대 한 Azure Automanage

이 문서에서는 다음과 같은 이점을 제공 하는 컴퓨터에 대 한 Azure Automanage 모범 사례에 대 한 정보를 다룹니다.

- 가상 머신을 지능적으로 온보딩하여 Azure 서비스 모범 사례 선택
- Azure 모범 사례에 따라 각 서비스를 자동으로 구성
- 모범 사례 서비스의 사용자 지정 지원
- 드리프트를 모니터링하고 검색된 경우 수정
- 간단한 환경 제공(포인트, 클릭, 설정, 제거)

## <a name="overview"></a>개요

Azure Automanage machine 모범 사례는 Azure에서 가상 머신에 도움이 되는 특정 서비스를 검색 하 고 등록 하는 방법을 파악 하지 않아도 되는 서비스입니다. 이러한 서비스는 Azure 모범 사례 서비스로 간주되며, 가상 머신에 대한 안정성, 보안 및 관리를 개선하는 데 도움이 됩니다. 예제 서비스에는 [Azure 업데이트 관리](../automation/update-management/overview.md) 및 [Azure Backup](../backup/backup-overview.md)이 포함됩니다.

Azure Automanage에 컴퓨터를 온 보 딩 한 후에는 각 모범 사례 서비스가 권장 설정으로 구성 됩니다. 그러나 모범 사례 서비스 및 설정을 사용자 지정 하려는 경우 [사용자 지정 프로필](#custom-profiles) 옵션을 사용할 수 있습니다. 

또한 Azure Automanage는 자동으로 드리프트를 모니터링하고 검색된 경우 수정합니다. 즉, 가상 머신 또는 Arc 사용 서버를 Azure Automanage로 등록 컴퓨터를 모니터링 하 여 전체 수명 주기 동안 해당 [구성 프로필](#configuration-profile) 을 계속 준수 하는지 확인 합니다. 가상 머신이 프로필을 드리프트 하거나 등록 하는 경우 (예: 서비스가 오프 경우),이를 수정 하 고 컴퓨터를 원하는 상태로 다시 끌어옵니다.

Automanage는 VM이 있는 지리적 위치 외부에서 고객 데이터를 저장/처리하지 않습니다. 동남 아시아 지역에서 Automanage는 동남 아시아 외부의 데이터를 저장/처리 하지 않습니다.

> [!NOTE]
> Automanage는 azure virtual machines 및 Azure Arc 사용 서버에서 사용 하도록 설정할 수 있습니다. 현재 미국 정부 클라우드에서는 Automanage를 사용할 수 없습니다.

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
를 등록 하려면 자동 관리에 구독에서 처음으로 자동 관리를 사용 하는지 여부에 따라 약간 다른 RBAC 역할이 필요 합니다.

구독에서 처음으로 Automanage를 사용 하도록 설정 하는 경우:
* 컴퓨터를 포함 하는 구독에 대 한 **소유자** 역할 _**또는**_
* 컴퓨터를 포함 하는 구독에 대 한 **참가자** 및 **사용자 액세스 관리자** 역할

이미 컴퓨터를 자동으로 관리 하는 구독의 컴퓨터에서 Automanage를 사용 하도록 설정 하는 경우:
* 컴퓨터를 포함 하는 리소스 그룹의 **참가자** 역할

Automanage 서비스는 자동 관리 컴퓨터에서 작업을 수행 하기 위해이 자사 응용 프로그램 (Automanage API Application Id: d828acde-4b48-47f5-a6e8-52460104a052)에 대 한 **참가자** 권한을 부여 합니다.

> [!NOTE]
> 다른 구독의 작업 영역에 연결된 VM에서 Automanage를 사용하려면 각 구독에 대해 위에 설명된 사용 권한이 있어야 합니다.

## <a name="participating-services"></a>참여 서비스

:::image type="content" source="media\automanage-virtual-machines\intelligently-onboard-services-1.png" alt-text="지능적으로 서비스를 온보딩합니다.":::

참여 하는 Azure 서비스 및 지원 되는 프로필의 전체 목록은 다음을 참조 하세요.
- [Linux용 Automanage](automanage-linux.md)
- [Windows Server용 Automanage](automanage-windows-server.md)

 최선의 구현 방법 구성 프로필을 사용 하는 경우 참여 하는 서비스에 자동으로 등록 됩니다. 이는 [클라우드 채택 프레임워크](/azure/cloud-adoption-framework/manage/azure-server-management)에서 찾을 수 있는 모범 사례 백서에 필수적입니다.


## <a name="enabling-automanage-for-vms-in-azure-portal"></a>Azure Portal에서 VM용 Automanage 사용

Azure Portal에서 기존 가상 머신에서 Automanage를 사용 하도록 설정할 수 있습니다. 이 프로세스에 대한 간략한 단계는 [가상 머신용 Automanage 빠른 시작](quick-create-virtual-machines-portal.md)을 참조하세요.

VM에 대해 Automanage를 처음 사용 하는 경우 **automanage – Azure machine 모범 사례** 에 대 한 Azure Portal에서 검색할 수 있습니다. **기존 VM에서 사용** 을 클릭 하 고 사용할 [구성 프로필](#configuration-profile) 을 선택한 후에 등록할 컴퓨터를 선택 합니다. **사용** 을 클릭 하면 작업이 완료 됩니다.

이러한 서비스를 관리 하기 위해이 컴퓨터와 상호 작용 해야 하는 경우에만 VM을 수정 하려고 했지만 그렇게 하지 못한 이벤트에 있습니다. VM이 성공적으로 수정되면 경고를 표시하지 않고 규정 준수 상태로 전환합니다. 자세한 내용은 [VM의 상태](#status-of-vms)를 참조하세요.

## <a name="enabling-automanage-for-vms-using-azure-policy"></a>Azure Policy를 사용하여 VM용 Automanage 사용
기본 제공 Azure Policy를 사용하여 대규모로 VM에서 Automanage를 사용하도록 설정할 수도 있습니다. 이 정책에는 DeployIfNotExists 효과가 있습니다. 즉, 정책 범위 내에 있는 적격한 모든 VM이 자동으로 Automanage VM 모범 사례로 온보딩됩니다.

정책에 대한 직접 링크는 [여기](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F270610db-8c04-438a-a739-e8e6745b22d3)를 참조하세요.

자세한 내용은 [기본 제공 정책 자동 관리](virtual-machines-policy-enable.md)를 사용 하도록 설정 하는 방법을 참조 하세요. 

## <a name="configuration-profile"></a>구성 프로필

컴퓨터에 대해 Automanage를 사용 하도록 설정 하는 경우 구성 프로필이 필요 합니다. 구성 프로필은이 서비스의 기반이 됩니다. 머신을 온보딩하는 서비스와 해당 서비스의 구성 범위를 정의합니다.

### <a name="best-practice-configuration-profiles"></a>모범 사례 구성 프로필

현재 사용할 수 있는 두 가지 최상의 방법 구성 프로필이 있습니다.

- **개발/테스트 프로필은** 개발/테스트 컴퓨터용으로 설계 되었습니다.
- **프로덕션 프로필은** 프로덕션용입니다.

이러한 차이가 있는 이유는 실행하는 작업에 따라 특정 서비스가 권장되기 때문입니다. 예를 들어 프로덕션 머신에서는 Azure Backup에 자동으로 온보딩됩니다. 그러나 개발/테스트 머신은 일반적으로 비즈니스에 미치는 영향이 작기 때문에 개발/테스트 머신의 경우 백업 서비스는 불필요한 비용이 될 수 있습니다.

### <a name="custom-profiles"></a>사용자 지정 프로필

사용자 지정 프로필을 사용 하면 컴퓨터에 적용 하려는 서비스 및 설정을 사용자 지정할 수 있습니다. IT 요구 사항이 모범 사례와 다른 경우이 옵션을 선택 하는 것이 좋습니다. 예를 들어 IT 조직에서 다른 맬웨어 방지 솔루션을 사용 하도록 요구 하기 때문에 Microsoft Antimalware 솔루션을 사용 하지 않으려는 경우 사용자 지정 프로필을 만들 때 간단히 Microsoft Antimalware를 해제할 수 있습니다.

> [!NOTE]
> 모범 사례 개발/테스트 구성 프로필에서는 VM을 전혀 백업 하지 않습니다.

> [!NOTE]
> 컴퓨터의 구성 프로필을 변경 하려는 경우 원하는 구성 프로필을 사용 하 여 해당 프로필을 다시 사용 하도록 설정할 수 있습니다. 그러나 컴퓨터 상태가 "업그레이드 필요" 이면 먼저을 사용 하지 않도록 설정한 다음 Automanage를 다시 활성화 해야 합니다. 

참여 Azure 서비스의 전체 목록과 기본 설정이 지원되는 경우에 대해서는 여기를 참조하세요.
- [Linux용 Automanage](automanage-linux.md)
- [Windows Server용 Automanage](automanage-windows-server.md)


## <a name="status-of-vms"></a>VM의 상태

Azure Portal에서 automanage **– Azure 컴퓨터 모범 사례** 페이지로 이동 하 여 모든 automanage 컴퓨터를 나열 합니다. 여기에서 각 컴퓨터의 전반적인 상태를 확인할 수 있습니다.

:::image type="content" source="media\automanage-virtual-machines\configured-status.png" alt-text="구성된 가상 머신 목록.":::

나열된 각 머신에 대해 이름, 구성 프로필, 상태, 리소스 종류, 리소스 그룹, 구독 세부 정보가 표시됩니다.

**상태** 열에는 다음 상태가 표시될 수 있습니다.
- *진행 중* - VM이 방금 사용하도록 설정되었으며 구성되고 있습니다.
- *준수* - VM이 구성되고 드리프트가 검색되지 않습니다.
- *준수하지 않음* - VM이 드리프트되고 수정할 수 없거나 컴퓨터의 전원이 꺼지고 Automanage가 다음에 실행 중일 때 VM을 온보딩하거나 수정하려고 시도합니다.
- *업그레이드 필요* - VM이 이전 버전의 Automanage에 온보딩되고 최신 [버전으로 업그레이드해야](automanage-upgrade.md) 합니다.

**상태가** *준수하지 않음으로* 표시되는 경우 포털에서 상태를 클릭하고 제공된 문제 해결 링크를 사용하여 문제를 해결할 수 있습니다.


## <a name="disabling-automanage-for-vms"></a>VM용 Automanage를 사용하지 않도록 설정

특정 VM에서 1일 동안 Automanage를 사용하지 않도록 설정할 수 있습니다. 예를 들어, 머신에서 매우 중요한 보안 작업을 실행하고 있는 경우 Azure에서 일반적으로 수행하는 것보다 더 많이 머신을 잠가야 하므로 Azure 모범 사례 외에서 머신을 구성해야 합니다.

Azure Portal 이 작업을 수행하려면 자동 관리 **VM을** 모두 나열하는 자동 관리 – Azure 머신 모범 사례 페이지로 이동합니다. Automanage에서 사용하지 않도록 설정할 가상 머신 옆의 확인란을 선택하고 **Automanage 사용 안 함** 단추를 클릭합니다.

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

이 문서에서는 머신에 대한 자동 관리가 Azure 서비스를 알고, 온보딩하고, 모범 사례를 구성할 필요가 없는 방법을 제공한다는 것을 배웠습니다. 또한 가상 머신에 대한 Automanage에 온보딩한 컴퓨터가 구성 프로필에서 드리프트되는 경우 자동으로 규정 준수로 돌아갑니다.

Azure Portal Azure 가상 머신 또는 Arc 지원 서버에 대해 Automanage를 사용하도록 설정해 보세요.

> [!div class="nextstepaction"]
> [Azure Portal에서 가상 머신용 Automanage 사용](quick-create-virtual-machines-portal.md)
