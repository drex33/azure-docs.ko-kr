---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: rishabv90
ms.service: virtual-machines
ms.topic: include
ms.date: 10/20/2021
ms.author: risverma
ms.custom: include file
ms.openlocfilehash: b5d51a00a35fa34236a62c48f1ee01402ca2f1c9
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131520474"
---
Azure Compute는 특정 하드웨어 유형에서 격리되고 단일 고객 전용인 가상 머신 크기를 제공합니다. 격리된 크기는 특정 하드웨어 세대에 적용되고 작용하며 하드웨어 세대가 사용 중지되면 사용되지 않습니다.

격리된 가상 머신 크기는 규정 준수 및 규정 요구 사항 충족 등의 이유로 다른 고객의 워크로드로부터 높은 수준의 격리가 필요한 워크로드에 가장 적합합니다.  격리 크기를 사용하면 가상 머신이 특정 서버 인스턴스에서 하나만 실행되도록 보장합니다. 


또한 격리된 크기의 VM이 크기 때문에 고객은 [중첩된 가상 머신에 대한 Azure 지원](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)을 사용하여 이러한 VM의 리소스를 세분화하도록 선택할 수 있습니다.

현재 격리 가상 머신 제품에는 다음이 포함됩니다.
* Standard_E80ids_v4
* Standard_E80is_v4
* Standard_E104i_v5
* Standard_E104is_v5
* Standard_E104id_v5
* Standard_E104ids_v5
* Standard_F72s_v2
* Standard_M128ms
* Standard_DC8_v2


> [!NOTE]
> 격리된 VM 크기는 하드웨어 수명이 제한적입니다. 자세한 내용은 아래를 참조하세요.

## <a name="deprecation-of-isolated-vm-sizes"></a>격리된 VM 크기의 사용 중단

격리된 VM 크기는 하드웨어 수명이 제한적입니다. Azure는 크기의 공식 사용 중단 날짜로부터 12개월 전에 미리 알리고 사용자가 고려할 수 있는 업데이트된 격리된 제품을 제공합니다.

| 크기 | 격리 사용 중지 날짜 | 
| --- | --- |
| Standard_DS15_v2  | 2021년 5월 15일      |
| Standard_D15_v2   | 2021년 5월 15일      |
| Standard_G5       | 2022년 2월 15일 |
| Standard_GS5      | 2022년 2월 15일 |
| Standard_E64i_v3  | 2022년 2월 15일 |
| Standard_E64is_v3 | 2022년 2월 15일 |
| Standard_DC8_v2   | 2022년 2월 15일 |


## <a name="faq"></a>FAQ
### <a name="q-is-the-size-going-to-get-retired-or-only-its-isolation-feature"></a>Q: 크기가 사용 중지되나요 아니면 해당 "격리" 기능만 사용 중지되나요?
**A**: 현재 VM 크기의 격리 기능만 사용 중지됩니다. 사용되지 않는 격리된 크기는 격리되지 않은 상태로 계속 유지됩니다. 격리가 필요 없는 경우 수행할 작업이 없으며 VM은 예상대로 계속 작동합니다.

### <a name="q-is-there-a-downtime-when-my-vm-lands-on-a-non-isolated-hardware"></a>Q: VM이 격리되지 않은 하드웨어에 있을 때 가동 중지 시간이 있나요?
**A**: 격리가 필요하지 않으면 작업이 필요하지 않으며 가동 중지 시간도 없습니다. 반대로 격리가 필요한 경우에는 권장 교체 크기가 발표에 포함됩니다. 교체 크기를 선택하면 고객이 VM의 크기를 조정해야 합니다.  

### <a name="q-is-there-any-cost-delta-for-moving-to-a-non-isolated-virtual-machine"></a>Q: 격리되지 않은 가상 머신으로 이동을 위한 비용 델타가 있나요?
**A**: 아니요.

### <a name="q-when-are-the-other-isolated-sizes-going-to-retire"></a>Q: 다른 격리된 크기는 언제 사용 중지될 예정인가요?
**A**: 격리된 크기의 공식 지원 중단 12개월 전에 미리 알림을 제공합니다. 최신 알림에는 Standard_G5, Standard_GS5, Standard_E64i_v3 및 Standard_E64i_v3의 격리 기능 사용 중지가 포함되어 있습니다.  

### <a name="q-im-an-azure-service-fabric-customer-relying-on-the-silver-or-gold-durability-tiers-does-this-change-impact-me"></a>Q: 실버 또는 골드 내구성 계층을 사용하는 Azure Service Fabric 고객입니다. 이 변경이 저에게 영향을 미치나요?
**A**: 아니요. 이 변경 후에도 Service Fabric의 [내구성 계층](../articles/service-fabric/service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster)에서 제공하는 보증은 계속 작동합니다. 다른 이유로 물리적 하드웨어 격리가 필요한 경우 위에서 설명한 작업 중 하나를 수행해야 할 수 있습니다. 
 
### <a name="q-what-are-the-milestones-for-d15_v2-or-ds15_v2-isolation-retirement"></a>Q: D15_v2 또는 DS15_v2 격리 사용 중지에 대한 마일스톤은 무엇인가요? 
**A**: 
 
| 날짜 | 작업 |
|---|---| 
| 2020년 5월 15일<sup>1</sup> | D/DS15_v2 격리 사용 중지 알림| 
| 2021년 5월 15일 | D/DS15_v2 격리 보증 제거됨| 

<sup>1</sup> 이 크기를 사용하는 기존 고객은 다음 단계에 대한 자세한 지침이 포함된 알림 이메일을 받게 됩니다.  

### <a name="q-what-are-the-milestones-for-g5-gs5-e64i_v3-and-e64is_v3-isolation-retirement"></a>Q: G5, Gs5, E64i_v3 및 E64is_v3 격리 사용 중지에 대한 마일스톤은 무엇인가요? 
**A**: 
 
| 날짜 | 작업 |
|---|---|
| 2021년 2월 15일<sup>1</sup> | G5/GS5/E64i_v3/E64is_v3 격리 사용 중지 알림 |
| 2022년 2월 28일 | G5/GS5/E64i_v3/E64is_v3 격리 보증 제거됨 |

<sup>1</sup> 이 크기를 사용하는 기존 고객은 다음 단계에 대한 자세한 지침이 포함된 알림 이메일을 받게 됩니다.  

## <a name="next-steps"></a>다음 단계

고객은 [중첩된 가상 머신에 대한 Azure 지원](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)을 사용하여 이러한 격리된 가상 머신의 리소스를 보다 세분화할 수도 있습니다.
