---
title: 업데이트 및 유지 관리 개요
description: Azure의 가상 머신에서 사용할 수 있는 업데이트 및 유지 관리 옵션에 대해 알아봅니다.
author: mimckitt
ms.author: mimckitt
ms.service: virtual-machines
ms.topic: overview
ms.date: 08/10/2021
ms.reviewer: cynthn
ms.openlocfilehash: 5e2d200e7133074ffc1dc2732cdedb692c1ea30c
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122698332"
---
# <a name="updates-and-maintenance-overview"></a>업데이트 및 유지 관리 개요

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 균일한 확장 집합

이 문서에서는 Azure VM(가상 머신)에 대한 다양한 업데이트 및 유지 관리 옵션에 대해 간략히 설명합니다.

## <a name="automatic-os-image-upgrade"></a>자동 OS 이미지 업그레이드

확장 집합에서 [자동 OS 이미지 업그레이드](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md?context=/azure/virtual-machines/context/context)를 사용하도록 설정하면 확장 집합의 모든 인스턴스에 대해 OS 디스크를 안전하게 자동으로 업그레이드하여 업데이트를 쉽게 관리할 수 있습니다.

[자동 OS 업그레이드](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md?context=/azure/virtual-machines/context/context)의 특징은 다음과 같습니다.

- 구성이 완료되면 이미지 게시자가 게시한 최신 OS 이미지가 사용자 개입 없이 확장 집합에 자동으로 적용됩니다.
- 게시자가 새 이미지를 게시할 때마다 인스턴스의 일괄 처리를 롤링 방식으로 업그레이드합니다.
- 애플리케이션 상태 프로브 및 [애플리케이션 상태 확장](../virtual-machine-scale-sets/virtual-machine-scale-sets-health-extension.md?context=/azure/virtual-machines/context/context)과 통합됩니다.
- 모든 VM 크기와 Windows 및 Linux 이미지 모두에서 작동합니다.
- 언제든지 자동 업그레이드를 옵트아웃할 수 있습니다(OS 업그레이드를 수동으로 시작 가능).
- VM의 OS 디스크는 최신 이미지 버전을 사용하여 만든 새 OS 디스크로 교체됩니다. 구성된 확장 및 사용자 지정 데이터 스크립트가 실행되고 지속형 데이터 디스크는 유지됩니다.
- [확장 시퀀스](../virtual-machine-scale-sets/virtual-machine-scale-sets-extension-sequencing.md?context=/azure/virtual-machines/context/context)가 지원됩니다.
- 자동 OS 이미지 업그레이드는 모든 크기의 확장 집합에서 사용하도록 설정할 수 있습니다.


## <a name="automatic-vm-guest-patching-preview"></a>자동 VM 게스트 패치(미리 보기)

[자동 VM 게스트 패치](automatic-vm-guest-patching.md)를 Azure VM에 사용하도록 설정하면 보안 규정 준수를 유지하기 위해 가상 머신을 안전하게 자동으로 패치하여 업데이트를 쉽게 관리할 수 있습니다.

[자동 VM 게스트 패치](automatic-vm-guest-patching.md)의 특징은 다음과 같습니다.
- *중요* 또는 *보안* 으로 분류된 패치가 자동으로 다운로드되어 VM에 적용됩니다.
- 패치는 VM의 표준 시간대에서 사용량이 적은 시간에 적용됩니다.
- 패치 오케스트레이션은 Azure에서 관리되며 [가용성 우선 원칙](automatic-vm-guest-patching.md#availability-first-updates)에 따라 패치가 적용됩니다.
- 패치 실패를 검색하기 위해 플랫폼 상태 신호를 통해 결정되는 가상 머신 상태를 모니터링합니다.
- 모든 VM 크기에 사용할 수 있습니다.


## <a name="automatic-extension-upgrade"></a>자동 확장 업그레이드

[자동 확장 업그레이드](automatic-extension-upgrade.md)는 Azure VM 및 Azure Virtual Machine Scale Sets에 사용할 수 있습니다. VM 또는 확장 집합에서 자동 확장 업그레이드를 사용하도록 설정하면 확장 게시자가 해당 확장에 대한 새 버전을 릴리스할 때마다 확장이 자동으로 업그레이드됩니다.

 자동 확장 업그레이드의 기능은 다음과 같습니다.
- Azure VM 및 Azure Virtual Machine Scale Sets에 지원됩니다.
- 업그레이드는 가용성 우선 배포 모델에 적용됩니다.
- Virtual Machine Scale Set의 경우 확장 집합 가상 머신의 20% 이하가 단일 일괄 처리로 업그레이드됩니다. 최소 일괄 처리 크기는 하나의 가상 머신입니다.
- 모든 VM 크기와 Windows 및 Linux 확장 모두에서 작동합니다.
- 자동 업그레이드는 언제든지 취소할 수 있습니다.
- 자동 확장 업그레이드는 모든 크기의 Virtual Machine Scale Sets에서 사용하도록 설정할 수 있습니다.
- 지원되는 각 확장은 개별적으로 등록되며, 자동으로 업그레이드할 확장을 선택할 수 있습니다.
- 모든 퍼블릭 클라우드 지역에서 지원됩니다.

## <a name="hotpatch"></a>핫패치

[핫패칭](../automanage/automanage-hotpatch.md?context=/azure/virtual-machines/context/context)은 설치 후 다시 부팅할 필요가 없는 새 Windows Server Azure 버전 VM(가상 머신)에 업데이트를 설치하는 새로운 방법입니다. Windows Server Azure 버전 VM용 핫패치의 이점은 다음과 같습니다.

- 더 적은 다시 부팅으로 인해 워크로드 영향을 줄입니다.
- Azure 업데이트 관리자를 사용하여 패키지 크기가 더 작아지고, 더 빠르게 설치되며, 패치 오케스트레이션이 더 쉬워지므로 업데이트를 더 빠르게 배포할 수 있습니다.
- 핫패치 업데이트 패키지는 다시 부팅하지 않고 더 빠르게 설치되는 Windows 보안 업데이트로 범위가 지정되므로 보호 기능이 향상됩니다.


## <a name="azure-update-management"></a>Azure 업데이트 관리

[Azure Automation의 업데이트 관리](../automation/update-management/overview.md?context=/azure/virtual-machines/context/context)를 사용하여 Azure, 온-프레미스 환경 및 다른 클라우드 환경에서 Windows 및 Linux 가상 머신에 대한 운영 체제 업데이트를 관리할 수 있습니다. 모든 에이전트 머신에서 사용 가능한 업데이트의 상태를 신속하게 평가하고 서버의 필수 업데이트를 설치하는 프로세스를 관리할 수 있습니다.

## <a name="maintenance-control"></a>유지 관리 제어

[유지 관리 제어](maintenance-control.md)를 사용하여 다시 부팅할 필요가 없는 플랫폼 업데이트를 관리합니다. Azure는 안정성, 성능, 보안을 향상시키거나 새로운 기능을 시작하기 위해 인프라를 자주 업데이트합니다. 대부분의 업데이트는 사용자에게 투명합니다. 게임, 미디어 스트리밍 및 금융 트랜잭션과 같은 일부 중요한 워크로드에서는 유지 관리를 위해 VM이 몇 초 동안 멈추거나 연결이 끊어지는 것을 허용할 수 없습니다. 유지 관리 제어는 플랫폼 업데이트를 기다렸다가 35일 롤링 기간 내에 적용할 수 있는 옵션을 제공합니다.

유지 관리 제어를 사용하면 업데이트를 격리된 VM 및 Azure 전용 호스트에 적용할 시기를 결정할 수 있습니다.

[유지 관리 제어](maintenance-control.md)를 사용하면 다음을 수행할 수 있습니다.
- 업데이트를 하나의 업데이트 패키지로 일괄 처리합니다.
- 업데이트를 적용하려면 최대 35일 동안 기다립니다.
- 유지 관리 일정을 구성하거나 [Azure Functions](https://github.com/Azure/azure-docs-powershell-samples/tree/master/maintenance-auto-scheduler)를 사용하여 플랫폼 업데이트를 자동화합니다.
- 유지 관리 구성은 구성 및 리소스 그룹에서 작동합니다.


## <a name="scheduled-events"></a>예약된 이벤트

예약된 이벤트는 애플리케이션이 VM(가상 머신) 유지 관리를 준비할 시간을 부여하는 Azure Metadata Service입니다. 향후 유지 관리 이벤트(예: 재부팅)에 대한 정보를 제공하여 애플리케이션이 이에 대비하고 서비스 중단을 제한할 수 있도록 합니다. 이 기능은 Windows와 Linux 모두에서 PaaS 및 IaaS를 포함한 모든 Azure Virtual Machines 유형에 사용할 수 있습니다.

Scheduled Events에 대한 자세한 내용은 [Windows VM에 대한 Scheduled Events](./windows/scheduled-events.md) 및 [Linux에 대한 Scheduled Events](./linux/scheduled-events.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

애플리케이션 및 서비스의 작동 시간을 늘리는 방법에 대한 자세한 내용은 [가용성 및 크기 조정](availability.md) 설명서를 검토하세요.
