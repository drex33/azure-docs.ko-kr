---
title: Azure Virtual Machines의 상태 및 청구 상태
description: VM이 진입할 수 있는 상태 및 사용자에게 요금이 청구되는 시점에 대한 개요
services: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.subservice: billing
ms.topic: conceptual
ms.date: 03/8/2021
ms.author: mimckitt
ms.reviewer: cynthn
ms.openlocfilehash: 38c43b168e4524529139dcb5c0807d8563e484eb
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2021
ms.locfileid: "132025820"
---
# <a name="states-and-billing-status-of-azure-virtual-machines"></a>Azure Virtual Machines의 상태 및 청구 상태

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 균일한 확장 집합

Azure VM(Virtual Machines)은 *프로비전* 및 *전원* 상태로 분류될 수 있는 여러 상태를 거칩니다. 이 아티클은 이러한 상태를 설명하고, 고객에게 인스턴스 사용량에 요금이 청구된 경우 특히 강조 표시하려고 합니다. 

## <a name="get-states-using-instance-view"></a>인스턴스 보기를 사용하여 상태 가져오기

인스턴스 보기 API는 VM 실행 상태 정보를 제공합니다. 자세한 내용은 [Virtual Machines - 인스턴스 보기](/rest/api/compute/virtualmachines/instanceview) API 설명서를 참조하세요.

Azure 리소스 탐색기는 VM 실행 상태를 보기 위한 간단한 UI를 제공 합니다 ( [리소스 탐색기](https://resources.azure.com/)).

Vm 프로 비전 상태는 VM 속성 및 InstanceView 내에서 약간 다른 형식으로 사용할 수 있습니다 `provisioningState` . VM InstanceView의 형식에는 배열 내에 요소가 있습니다 `status` `ProvisioningState/<state>[/<errorCode>]` .

구독에 있는 모든 VM의 전원 상태를 검색하려면 매개 변수 **statusOnly** 가 *true* 로 설정된 [Virtual Machines - 모든 API 목록](/rest/api/compute/virtualmachines/listall)을 사용합니다.

> [!NOTE]
> 매개 변수 **statusOnly** 가 true로 설정된 [Virtual Machines - 모든 API 목록](/rest/api/compute/virtualmachines/listall)은 구독에 있는 모든 VM의 전원 상태를 검색합니다. 그러나 드물게 검색 프로세스에 발생하는 일시적인 문제로 인해 전원 상태를 사용할 수 없는 경우도 있습니다. 이러한 경우 동일한 API를 사용 하 여 다시 시도 하거나 [Azure Resource Health](../service-health/resource-health-overview.md) 를 사용 하 여 vm의 전원 상태를 확인 하는 것이 좋습니다.
 
## <a name="power-states-and-billing"></a>전원 상태 및 청구

전원 상태는 VM의 마지막 알려진 상태를 나타냅니다.

:::image type="content" source="./media/virtual-machines-common-states-lifecycle/vm-power-states.png" alt-text="이미지는 VM이 거칠 수 있는 전원 상태의 다이어그램을 보여 줍니다.":::

다음 표에서는 각 인스턴스 상태를 설명하고 인스턴스 사용량에 대해 요금이 청구되는지 여부를 나타냅니다.

| 전원 상태 | Description | 결제 |  
|---|---|---|
| 시작 중| 가상 머신이 켜지는 중입니다. | 청구됨 | 
| 실행 중 | 가상 머신이 완전히 작동합니다. 이는 표준 작업 상태입니다. | 청구됨 | 
| 중지 중 | 이는 실행 중과 중지됨 간의 전환 상태입니다. | 청구됨| 
|중지됨 | Virtual Machine이 호스트에 할당되었지만 실행되고 있지 않습니다. PoweredOff 상태 또는 *중지됨(할당됨)* 이라고도 합니다. 이는 PowerOff API 작업을 호출하거나 게스트 OS 내에서 종료를 호출한 결과일 수 있습니다. 중지됨 상태는 VM을 만드는 동안 또는 할당 해제된 상태에서 VM을 시작하는 동안 잠시 관찰될 수도 있습니다.  | 청구됨 | 
| 할당 취소 중 | 실행 중과 할당 취소됨 간의 전환 상태입니다. | 청구되지 않음* | 
| 할당 취소됨 | 가상 머신이 기본 하드웨어 임대를 해제하고 완전히 꺼졌습니다. 이 상태를 ‘중지됨(할당 취소됨)’이라고도 합니다. | 청구되지 않음* | 


**JSON의 PowerState 예**

```json
        {
          "code": "PowerState/running",
          "level": "Info",
          "displayStatus": "VM running"
        }
```

&#42; 일부 Azure 리소스(예: [디스크](https://azure.microsoft.com/pricing/details/managed-disks) 및 [네트워킹](https://azure.microsoft.com/pricing/details/bandwidth/))에는 계속 요금이 부과됩니다.


## <a name="provisioning-states"></a>프로비전 상태

프로 비전 상태는 VM에서 사용자가 시작한 제어 평면 작업의 상태입니다. 이러한 상태는 VM의 전원 상태와 별개입니다.

| 프로비저닝 상태 | Description |
|---|---|
| 만드는 중 | 가상 머신을 만들고 있습니다. |
| 업데이트 | 가상 머신을 최신 모델로 업데이트 하는 중입니다. 시작 및 다시 시작과 같은 가상 컴퓨터에 대 한 일부 비 모델 변경 내용이 업데이트 중 상태입니다. |
| 실패 | 가상 컴퓨터 리소스에 대 한 마지막 작업이 실패 했습니다. | 
| 성공 | 가상 컴퓨터 리소스에 대 한 마지막 작업이 성공 했습니다. | 
| 삭제 중 | 가상 컴퓨터를 삭제 하 고 있습니다. | 
| 마이그레이션 | Azure Service Manager에서 Azure Resource Manager로 마이그레이션하는 경우 표시 됩니다. | 

## <a name="os-provisioning-states"></a>OS 프로비전 상태
OS 프로 비전 상태는 [일반화](./linux/imaging.md#generalized-images) 된 os 이미지를 사용 하 여 만든 가상 컴퓨터에만 적용 됩니다. OS 디스크로 연결 된 [특수](./linux/imaging.md#specialized-images) 이미지 및 디스크는 이러한 상태를 표시 하지 않습니다. OS 프로 비전 상태는 별도로 표시 되지 않습니다. VM instanceView에서 프로 비전 상태의 하위 상태입니다. 예: `ProvisioningState/creating/osProvisioningComplete`.

:::image type="content" source="./media/virtual-machines-common-states-lifecycle/os-provisioning-states.png" alt-text="이미지는 VM이 거칠 수 있는 OS 프로비전 상태를 보여 줍니다.":::

| OS 프로비전 상태 | Description | 
|---|---|
| OSProvisioningInProgress | VM이 실행 중 이며 게스트 OS의 초기화 (설치)가 진행 중입니다. |
| OSProvisioningComplete | 일시적으로 존재하는 상태입니다. 가상 머신은 이 상태에서 곧 **성공** 으로 전환됩니다. 확장이 아직 설치 중인 경우 설치가 완료될 때까지 이 상태가 계속 표시됩니다. |
| 성공 | 사용자가 시작한 작업이 완료되었습니다. | 
| 실패 | 실패한 작업을 나타냅니다. 자세한 내용 및 가능한 해결 방법은 오류 코드를 참조합니다. | 

## <a name="troubleshooting-vm-states"></a>VM 상태 문제 해결

특정 vm 상태 문제를 해결 하려면 [vm 배포 Windows 문제 해결](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/troubleshoot-deployment-new-vm-windows) 및 [Linux vm 배포 문제](https://docs.microsoft.comtroubleshoot/azure/virtual-machines/troubleshoot-deployment-new-vm-linux)해결을 참조 하세요.

다른 문제 해결에 대 한 도움말은 [Azure Virtual Machines 문제 해결 설명서](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/welcome-virtual-machines)를 참조 하세요.


## <a name="next-steps"></a>다음 단계
- [Azure Cost Management + Billing 설명서](../cost-management-billing/index.yml)를 검토하세요.
- [Azure 가격 계산기](https://azure.microsoft.com/pricing/calculator/)를 사용하여 배포를 계획하세요.
- VM 모니터링에 대한 자세한 내용은 [Azure에서 가상 머신 모니터링](../azure-monitor/vm/monitor-vm-azure.md)을 참조하세요.
