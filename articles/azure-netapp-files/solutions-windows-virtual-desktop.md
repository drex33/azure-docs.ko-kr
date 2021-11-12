---
title: Azure NetApp Files와 함께 Azure Virtual Desktop 사용 | Microsoft Docs
description: Azure NetApp Files를 사용하여 Azure Virtual Desktop 배포에 대한 모범 사례 지침과 샘플 청사진을 제공합니다.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/13/2020
ms.author: b-juche
ms.openlocfilehash: 8d4d9b393c698b2b638a119b7eb4386e0099d514
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132347920"
---
# <a name="benefits-of-using-azure-netapp-files-with-azure-virtual-desktop"></a>Azure Virtual Desktop에서 Azure NetApp Files를 사용할 경우의 이점 

이 문서에서는 Azure NetApp Files를 사용하여 Azure Virtual Desktop 배포에 대한 모범 사례 지침을 제공합니다.

Azure NetApp Files는 Azure의 고성능 파일 스토리지 서비스입니다. 최대 450,000IOPS를 비롯하여 밀리초 미만의 대기 시간을 제공하여 매우 큰 규모의 Azure Virtual Desktop 배포를 지원할 수 있습니다. 데이터 평면 액세스를 유지하면서 IO를 일시 중지하지 않고 대역폭을 조정하고 거의 즉시 주문형 Azure NetApp Files 볼륨의 서비스 수준을 변경할 수 있습니다. 해당 기능을 사용하면 Azure Virtual Desktop 배포 규모를 비용에 대해 간편하게 최적화할 수 있습니다. 볼륨 성능에 영향을 주지 않으며 공간 효율적인 특정 시점 볼륨 스냅샷을 만들 수도 있습니다. 해당 기능을 사용하면 `~snapshot` 디렉터리에서 복사본을 통해 개별 [사용자 프로필 컨테이너](../virtual-desktop/store-fslogix-profile.md)를 롤백하거나 볼륨 되돌리기 기능을 통해 한 번에 전체 볼륨을 즉시 롤백할 수 있습니다.  데이터 손실이나 손상으로부터 볼륨을 보호하기 위해 최대 255(회전)개의 스냅샷을 사용할 수 있으므로 관리자에게는 수행된 작업을 되돌릴 기회가 많이 있습니다.

## <a name="sample-blueprints"></a>샘플 청사진

다음 샘플 청사진에서는 Azure NetApp Files와 Azure Virtual Desktop의 통합을 보여 줍니다. 풀링된 데스크톱 시나리오에서 사용자는 [다중 세션 가상 머신](../virtual-desktop/windows-10-multisession-faq.yml#what-is-windows-10-enterprise-multi-session)을 사용하여 풀에서 가장 적합한 세션([폭 우선 모드](../virtual-desktop/host-pool-load-balancing.md#breadth-first-load-balancing-algorithm)) 호스트로 이동합니다. 반면 개인 데스크톱은 각 사용자가 고유의 가상 머신을 가지는 시나리오를 위해 예약되어 있습니다.

### <a name="pooled-desktop-scenario"></a>풀링된 데스크톱 시나리오

풀링된 시나리오의 경우 Azure Virtual Desktop 팀은 vCPU에 대한 사용자 수에 따라 다음 지침을 [권장](/windows-server/remote/remote-desktop-services/virtual-machine-recs#multi-session-recommendations)합니다. 해당 권장 사항에는 가상 머신의 크기가 지정되지 않습니다.

|     워크로드 유형     |     밝음    |     중간    |     Heavy    |
|-----------------------|--------------|---------------|--------------|
|     vCPU당 사용자 수    |     6        |     4         |     2        |


해당 권장 사항은 500-사용자 LoginVSI 테스트를 통해 확인되며 각 D16as_V4 가상 머신에 약 62명의 “지식/중간 사용자”를 기록합니다. 

예를 들어 D16as_V4 가상 머신당 62명의 사용자가 있으면 Azure NetApp Files는 환경당 6만 명의 사용자를 쉽게 지원할 수 있습니다. D32as_v4 가상 머신의 상한을 평가하는 테스트는 진행 중입니다. vCPU당 Azure Virtual Desktop 사용자 수 권장 사항이 D32as_v4에 적용되는 경우 다음 그림과 같이 [1,000 IP VNet 한도](./azure-netapp-files-network-topologies.md)에 도달하기 전에 12만 명 이상의 사용자가 1,000대의 가상 머신에 맞게 조정됩니다.  

![Azure Virtual Desktop 풀링된 데스크톱 시나리오](../media/azure-netapp-files/solutions-pooled-desktop-scenario.png)   

### <a name="personal-desktop-scenario"></a>개인용 데스크톱 시나리오 

개인용 데스크톱 시나리오에서 다음 그림은 범용 아키텍처 권장 사항을 보여 줍니다. 사용자는 특정 데스크톱 Pod에 매핑되고 각 Pod는 1,000대 미만의 가상 머신이 있으므로 관리 VNet에서 전파되는 IP 주소를 위한 공간이 있습니다. Azure NetApp Files는 단일 세션 호스트 풀 VNet당 900대 이상의 개인 데스크톱을 간편하게 처리할 수 있으며 가상 머신의 실제 수는 1,000에서 Hub VNet에서 발견되는 관리 호스트의 수를 뺀 값입니다. 더 많은 개인 데스크톱이 필요한 경우 다음 그림과 같이 추가 Pod(호스트 풀 및 가상 네트워크)를 간편하게 추가할 수 있습니다. 

![Azure Virtual Desktop 개인 데스크톱 시나리오](../media/azure-netapp-files/solutions-personal-desktop-scenario.png)  

이와 같은 POD 기반 아키텍처를 빌드하는 경우 로그인할 때 올바른 Pod에 사용자를 할당하는 것은 사용자가 항상 사용자 프로필을 찾을 수 있도록 하는 데 중요합니다. 

## <a name="next-steps"></a>다음 단계

- [Azure NetApp Files를 사용하는 솔루션 아키텍처](azure-netapp-files-solution-architectures.md)