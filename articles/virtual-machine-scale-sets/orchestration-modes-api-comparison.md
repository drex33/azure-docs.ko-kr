---
title: 오케스트레이션 모드 API 비교
description: 균일한 오케스트레이션 모드와 유연한 오케스트레이션 모드 간의 API 차이점에 대해 알아봅니다.
author: fitzgeraldsteele
ms.author: fisteele
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.date: 08/05/2021
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli, vmss-flex
ms.openlocfilehash: db141f863389d724cc1437beeed3b00b44020098
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130161836"
---
# <a name="orchestration-modes-api-comparison"></a>오케스트레이션 모드 API 비교 

이 문서에서는 가상 머신 확장 집합에 대한 균일한 오케스트레이션 모드와 [유연한 오케스트레이션](..\virtual-machines\flexible-virtual-machine-scale-sets.md) 모드 간의 API 차이점을 비교합니다. 균일한 가상 머신 확장 집합 및 유연한 가상 머신 확장 집합에 대한 자세한 내용은 [오케스트레이션 모드](virtual-machine-scale-sets-orchestration-modes.md)를 참조하세요.


## <a name="instance-view"></a>인스턴스 보기

| 균일한 API | 유연한 대체 |
|-|-|
| 가상 머신 확장 집합 인스턴스 보기 | 개별 VM에서 인스턴스 보기를 얻습니다. Resource Graph 사용하여 전원 상태를 쿼리합니다. |


## <a name="scale-set-lifecycle-batch-operations"></a>확장 집합 수명 주기 일괄 처리 작업  

| 균일한 API | 유연한 대체 |
|-|-|
| 가상 머신 확장 집합 VM 수명 주기 일괄 처리 작업:  | 특정 인스턴스에서 단일 VM API 호출: |
| [할당 취소](/rest/api/compute/virtualmachinescalesetvms/deallocate)  | [단일 VM API 호출 - 할당 취소](/rest/api/compute/virtualmachines/deallocate)   |
| [삭제](/rest/api/compute/virtualmachinescalesetvms/delete)  | [단일 VM API 호출 - 삭제](/rest/api/compute/virtualmachines/delete)  |
| [인스턴스 보기 가져오기](/rest/api/compute/virtualmachinescalesetvms/getinstanceview)  | [단일 VM API 호출 - 인스턴스 보기](/rest/api/compute/virtualmachines/instanceview)  |
| [유지 보수 수행](/rest/api/compute/virtualmachinescalesetvms/performmaintenance)  | [단일 VM API 호출 - 유지 관리 수행](/rest/api/compute/virtualmachines/performmaintenance)  |
| [전원 끄기](/rest/api/compute/virtualmachinescalesetvms/poweroff)  | [단일 VM API 호출 - 전원 끄기](/rest/api/compute/virtualmachines/poweroff)  |
| [재배포](/rest/api/compute/virtualmachinescalesetvms/redeploy)  | [단일 VM API 호출 - 재배포](/rest/api/compute/virtualmachines/redeploy)  |
| [이미지로 다시 설치](/rest/api/compute/virtualmachinescalesetvms/reimage)  | [단일 VM API 호출 - 이미지로 다시 설치](/rest/api/compute/virtualmachines/reimage)  |
| [모두 이미지로 다시 설치](/rest/api/compute/virtualmachinescalesetvms/reimageall)  | 해당 없음 |
| [다시 시작](/rest/api/compute/virtualmachinescalesetvms/restart)  | [단일 VM API 호출 - 다시 시작](/rest/api/compute/virtualmachines/restart)  |
| [제거 시뮬레이트](/rest/api/compute/virtualmachinescalesetvms/simulateeviction) | [단일 VM API 호출 - 제거 시뮬레이트](/rest/api/compute/virtualmachines/simulateeviction)  |
| [Start](/rest/api/compute/virtualmachinescalesetvms/start) | [단일 VM API 호출 - 시작](/rest/api/compute/virtualmachines/start) |


## <a name="get-or-update"></a>가져오기 또는 업데이트 

### <a name="uniform-api"></a>균일한 API
가상 머신 확장 집합 VM 가져오기 또는 인스턴스 업데이트:
- [가져오기](/rest/api/compute/virtualmachinescalesetvms/get) 
- [업데이트](/rest/api/compute/virtualmachinescalesetvms/update)

### <a name="flexible-alternative"></a>유연한 대체 
단일 VM API 호출:
- 인스턴스 보호 유형 동작을 위한 [ARM 잠금 리소스](../azure-resource-manager/management/lock-resources.md?tabs=json) 


## <a name="list-instances"></a>인스턴스 나열 

### <a name="uniform-api"></a>균일한 API
`VMSS List Instances`: 
- 각 인스턴스와 연결 된 확장 집합 ID를 반환 합니다.

### <a name="flexible-alternative"></a>유연한 대체
Azure Resource Graph: 

```armasm
resources 
| where type == "microsoft.compute/virtualmachines" 
| where properties.virtualMachineScaleSet.id contains "portalbb01" 
```

## <a name="scale-set-operations"></a>확장 집합 작업 

### <a name="uniform-api"></a>균일한 API
가상 머신 확장 집합 작업:
- [인스턴스 업데이트](/rest/api/compute/virtual-machine-scale-sets/update-instances)
- [할당 취소](/rest/api/compute/virtual-machine-scale-sets/deallocate)
- [유지 보수 수행](/rest/api/compute/virtual-machine-scale-sets/perform-maintenance)
- [전원 끄기](/rest/api/compute/virtual-machine-scale-sets/power-off)
- [재배포](/rest/api/compute/virtual-machine-scale-sets/redeploy)
- [이미지로 다시 설치](/rest/api/compute/virtual-machine-scale-sets/reimage)
- [모두 이미지로 다시 설치](/rest/api/compute/virtual-machine-scale-sets/reimage-all)
- [다시 시작](/rest/api/compute/virtual-machine-scale-sets/restart)
- [오케스트레이션 서비스 상태 설정](/rest/api/compute/virtual-machine-scale-sets/set-orchestration-service-state)
- [Start](/rest/api/compute/virtual-machine-scale-sets/start)

### <a name="flexible-alternative"></a>유연한 대체
개별 VM에서 작업을 호출합니다.

가상 컴퓨터 작업:
- [이미지로](/rest/api/compute/virtual-machines/reimage)다시 설치: 단일 VM API 호출-임시 OS vm에만 이미지로 다시 설치

## <a name="vm-extension"></a>VM 확장

### <a name="uniform-api"></a>균일한 API
가상 머신 확장 집합 VM 확장:
- [생성 또는 업데이트](/rest/api/compute/virtual-machine-scale-set-vm-extensions/create-or-update)
- [삭제](/rest/api/compute/virtual-machine-scale-set-vm-extensions/delete)
- [가져오기](/rest/api/compute/virtual-machine-scale-set-vm-extensions/get)
- [목록](/rest/api/compute/virtual-machine-scale-set-vm-extensions/list)
- [업데이트](/rest/api/compute/virtual-machine-scale-set-vm-extensions/update) 

### <a name="flexible-alternative"></a>유연한 대체
개별 VM에서 작업을 호출합니다.


## <a name="networking"></a>네트워킹 

### <a name="uniform-api"></a>균일한 API
- NAT 풀/포트 전달 
- 유연한 확장 집합에서 NAT 풀이 지원되지 않음  

### <a name="flexible-alternative"></a>유연한 대체
- 각 VM에 개별 NAT 규칙 설정


## <a name="scale-set-apis"></a>확장 집합 API

### <a name="uniform-api"></a>균일한 API
균일한 가상 머신 확장 집합 API:
- [단일 배치 그룹으로 변환](/rest/api/compute/virtual-machine-scale-sets/convert-to-single-placement-group)
- [강제 복구 Service Fabric 플랫폼 업데이트 도메인 워크](/rest/api/compute/virtual-machine-scale-sets/force-recovery-service-fabric-platform-update-domain-walk)

### <a name="flexible-alternative"></a>유연한 대체
유연한 가상 머신 확장 집합에서 지원되지 않습니다.


## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [다양한 오케스트레이션 모드에 대해 알아봅니다](virtual-machine-scale-sets-orchestration-modes.md)