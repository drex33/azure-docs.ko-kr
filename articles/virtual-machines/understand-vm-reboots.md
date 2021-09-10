---
title: VM 재부팅 이해
description: VM 다시 부팅 이해 - 유지 관리 및 가동 중지
author: mimckitt
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 03/08/2021
ms.author: mimckitt
ms.reviewer: cynthn
ms.openlocfilehash: dda6b0c5d2087b588e69e6d5507eea4cdcbc1caf
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122690858"
---
# <a name="understand-vm-reboots---maintenance-vs-downtime"></a>VM 다시 부팅 이해 - 유지 관리 및 가동 중지

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 단일 확장 집합

Azure의 가상 머신이 초래할 수 있는 세 가지 시나리오, 즉 계획되지 않은 하드웨어 유지 관리, 예기치 않은 가동 중지 및 계획된 유지 관리가 있습니다.

## <a name="unplanned-hardware-maintenance-event"></a>계획되지 않은 하드웨어 유지 관리 이벤트
계획되지 않은 하드웨어 유지 관리는 Azure 플랫폼에서 물리적 컴퓨터와 관련된 하드웨어 또는 플랫폼 구성 요소가 실패할 것으로 예측할 때 발생합니다. 플랫폼에서 오류를 예측하는 경우 계획되지 않은 하드웨어 유지 관리 이벤트를 발급하여 해당 하드웨어에서 호스팅되는 가상 머신에 미치는 영향을 줄입니다. Azure에서는 [실시간 마이그레이션](./maintenance-and-updates.md) 기술을 사용하여 오류가 발생한 하드웨어에서 정상적인 물리적 컴퓨터로 Virtual Machines를 마이그레이션합니다. 실시간 마이그레이션은 짧은 시간 동안 Virtual Machine을 일시 중지하는 VM 보존 작업입니다. 메모리, 열린 파일 및 네트워크 연결은 유지되지만 이벤트 전후에 성능이 저하될 수 있습니다. 실시간 마이그레이션을 사용할 수 없는 경우 아래에서 설명한 대로 VM에 예기치 않은 가동 중지가 발생합니다.


## <a name="unexpected-downtime"></a>예기치 않은 가동 중지 시간
예기치 않은 가동 중지 시간은 가상 머신의 하드웨어 또는 실제 인프라에 예기치 않게 문제가 발생하는 경우입니다. 여기에는 로컬 네트워크 오류, 로컬 디스크 오류 또는 기타 랙 수준의 오류가 포함될 수도 있습니다. 이러한 오류가 감지되면 Azure 플랫폼에서 가상 머신을 동일한 데이터 센터의 정상적인 물리적 컴퓨터로 자동으로 마이그레이션(복구)합니다. 복구 과정 중에 가상 머신에서 가동 중지(재부팅)가 발생하고 경우에 따라 임시 드라이브가 손실됩니다. 연결된 OS 및 데이터 디스크는 항상 유지됩니다.

전체 데이터 센터 또는 전체 지역에 영향을 주는 가동 중단 또는 재해가 발생한 경우 가상 머신에는 가동 중지 시간이 발생할 수도 있습니다. 이러한 시나리오의 경우 Azure에서는 [가용성 영역](../availability-zones/az-overview.md) 및 [쌍을 이루는 지역](regions.md#region-pairs)을 비롯한 보호 옵션을 제공합니다.

## <a name="planned-maintenance-events"></a>계획된 유지 관리 이벤트
계획된 유지 관리 이벤트는 가상 머신이 실행 중인 플랫폼 인프라의 전체적인 안정성, 성능 및 보안을 향상시키기 위해 Microsoft가 기본 Azure 플랫폼에 적용하는 주기적인 업데이트입니다. 이러한 업데이트는 대부분 Virtual Machines 또는 Cloud Services에 영향을 주지 않고 수행됩니다([재부팅이 필요 없는 유지 관리](maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot) 참조). Azure 플랫폼에서 가능한 모든 경우에 VM 보존 유지 관리를 사용하려고 시도하는 반면, 가상 머신을 다시 부팅해야 기본 인프라에 필요한 업데이트를 적용할 수 있는 경우가 드물게 있습니다. 이 경우 적절한 기간에 VM에 대한 유지 관리를 시작하여 유지 관리 - 재배포 작업이 포함된 Azure Planned Maintenance(Azure 계획된 유지 관리)를 수행할 수 있습니다. 자세한 내용은 [Virtual Machines에 대한 계획된 유지 관리](maintenance-and-updates.md)를 참조하세요.

## <a name="reduce-downtime"></a>가동 중지 시간 감소
이러한 이벤트로 인한 가동 중지 시간의 영향을 줄이기 위해 가상 머신에 다음과 같은 고가용성 모범 사례를 권장합니다.

* [가용성 영역](../availability-zones/az-overview.md)을 사용하여 데이터 센터 장애로부터 보호합니다.
* 중복성을 위해 [가용성 집합](availability-set-overview.md)에서 여러 가상 머신 구성
* [Linux에 대한 예약된 이벤트](./linux/scheduled-events.md) 또는 [Windows에 대한 예약된 이벤트](./windows/scheduled-events.md)를 사용하여 VM에 영향을 주는 이벤트에 사전에 대응합니다.
* 각 애플리케이션 계층을 별도의 가용성 집합으로 구성
* 가용성 영역 또는 집합과 [부하 분산 장치](../load-balancer/load-balancer-overview.md) 결합

## <a name="next-steps"></a>다음 단계
Azure에서 가용성 옵션에 대해 자세히 알아보려면 [가용성 개요](availability.md)를 참조하세요.