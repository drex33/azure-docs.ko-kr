---
title: SAP HANA에 대 한 Azure NetApp Files 응용 프로그램 볼륨 그룹에 대 한 요구 사항 및 고려 사항 | Microsoft Docs
description: SAP HANA에 Azure NetApp Files 응용 프로그램 볼륨 그룹을 사용 하기 전에 알아야 할 요구 사항 및 고려 사항을 설명 합니다.
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
ms.date: 11/19/2021
ms.author: b-juche
ms.openlocfilehash: e55d96af065d4068fcdcd4f375d0449727b2c341
ms.sourcegitcommit: dcf3424d7149fceaea0340eb0657baa2c27882a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133271992"
---
# <a name="requirements-and-considerations-for-application-volume-group-for-sap-hana"></a>SAP HANA의 응용 프로그램 볼륨 그룹에 대 한 요구 사항 및 고려 사항 

이 문서에서는 SAP HANA에 Azure NetApp Files 응용 프로그램 볼륨 그룹을 사용 하기 전에 알아야 할 요구 사항 및 고려 사항을 설명 합니다.

## <a name="requirements-and-considerations"></a>요구 사항 및 고려 사항

* [수동 QoS 용량 풀](manage-manual-qos-capacity-pool.md) 기능을 사용 해야 합니다.  
* 근접 배치 그룹 (PPG)을 만들고 SAP HANA 계산 리소스에 고정 해야 합니다. SAP HANA의 응용 프로그램 볼륨 그룹에서 SAP HANA 서버에 가까운 Azure NetApp Files 리소스를 검색 하려면이 설정이 필요 합니다. 자세한 내용은 [근접 배치 그룹에 대 한 모범 사례](#best-practices-about-proximity-placement-groups) 및 [Azure Portal를 사용 하 여 근접 배치 그룹 만들기](../virtual-machines/windows/proximity-placement-groups-portal.md)를 참조 하세요.  
* 다음 영역을 포함 하 여 크기 조정 및 SAP HANA 시스템 아키텍처를 완료 해야 합니다. 
    * SAP ID (SID)
    * 메모리
    * 단일 호스트 또는 다중 호스트 SAP HANA
    * HSR (HANA System Replication)을 사용할지 여부를 결정 합니다.
        HSR를 사용 하면 SAP HANA 데이터베이스가 주 SAP HANA 시스템에서 보조 SAP HANA 시스템으로 동기적 또는 비동기적으로 복제할 수 있습니다. 
    * 백업 목적으로 스냅숏을 사용 하는 경우 데이터 볼륨의 예상 변경 률입니다.
* Azure NetApp Files IP 주소를 매핑하기 위해 VNet 및 위임 된 서브넷을 만들어야 합니다.

    디자인 타임에 VNet 및 위임 된 서브넷의 레이아웃을 설정 하는 것이 좋습니다. 

    SAP HANA에 대 한 응용 프로그램 볼륨 그룹은 크기가 큰 자산에 대 한 IP 주소를 최대 6 개까지 만듭니다. 위임 된 서브넷에 사용 가능한 IP 주소가 충분 한지 확인 합니다. 서브넷 크기가/26 인 IP 주소가 최소 59 인 위임 된 서브넷을 사용 하는 것이 좋습니다. [Azure NetApp Files에 서브넷을 위임 하는 방법에 대 한 고려 사항을](azure-netapp-files-delegate-subnet.md#considerations)참조 하세요.

## <a name="best-practices-about-proximity-placement-groups"></a>근접 배치 그룹에 대 한 모범 사례

응용 프로그램 볼륨 그룹을 사용 하 여 SAP HANA 볼륨을 배포 하려면 HANA 데이터베이스 Vm을 근접 배치 그룹 (PPG)의 앵커로 사용 해야 합니다. 데이터베이스당 가용성 집합을 만들고 **[SAP HANA VM 고정 요청 양식](https://aka.ms/HANAPINNING)** 을 사용 하 여 가용성 집합을 전용 계산 클러스터에 고정 하는 것이 좋습니다. 고정 후에는 가용성 집합에 PPG를 추가 하 고 해당 가용성 집합을 사용 하 여 SAP HANA 데이터베이스의 모든 호스트를 배포 해야 합니다. 이렇게 하면 모든 가상 머신이 동일한 위치에 있게 됩니다. 가상 머신이 시작 되 면 PPG에 앵커가 있습니다.

> [!NOTE]
> PPG는 SAP HANA 볼륨에 사용 하려는 용량 풀과 동일한 리소스 그룹에 있어야 합니다.

고정 된 가용성 집합 없이 PPG를 사용 하는 경우 ppg에 있는 모든 가상 머신이 중지 되 면 PPG가 해당 앵커를 손실 합니다. 가상 컴퓨터가 다시 시작 되 면 다른 위치에서 시작 될 수 있습니다. 그러면 응용 프로그램 볼륨 그룹으로 만든 볼륨이 이동 되지 않으므로 대기 시간이 늘어날 수 있습니다. 

이 경우 다음과 같은 두 가지 시나리오를 사용할 수 있습니다.

* 안정적인 장기 설치:   
    가용성 집합이 수동으로 고정 된 PPG와 함께 가용성 집합을 사용 합니다.

    고정을 사용 하면 가용성 집합의 모든 컴퓨터가 중지 된 경우에도 항상 가상 컴퓨터의 배치가 변경 되지 않습니다.

* 임시 설치:   
    고정 없이 ppg와 함께 ppg 또는 가용성 집합을 사용 합니다.

    SAP HANA 지원 되는 가상 머신 시리즈 (즉, M 시리즈)는 일반적으로 Azure NetApp Files 가까이 배치 되므로 PPG를 사용 하는 응용 프로그램 볼륨 그룹은 가능한 낮은 대기 시간으로 필요한 볼륨을 만들 수 있습니다. 하나 이상의 가상 컴퓨터가 실행 되 고 있는 경우 볼륨 및 HANA 호스트 간의이 관계는 변경 되지 않습니다.

    > [!NOTE]
    > 응용 프로그램 볼륨 그룹을 사용 하 여 HANA 볼륨을 배포 하는 경우 가용성 집합에서 하나 이상의 VM을 시작 해야 합니다. 실행 중인 VM이 없으면 PPG를 사용하여 최적의 Azure NetApp Files 하드웨어를 찾을 수 없으며 프로비저닝이 실패합니다.

## <a name="next-steps"></a>다음 단계

* [SAP HANA에 대 한 Azure NetApp Files 응용 프로그램 볼륨 그룹 이해](application-volume-group-introduction.md)
* [SAP HANA의 응용 프로그램 볼륨 그룹을 사용 하 여 첫 번째 SAP HANA 호스트 배포](application-volume-group-deploy-first-host.md)
* [SAP HANA의 응용 프로그램 볼륨 그룹을 사용 하 여 다중 호스트 SAP HANA 시스템에 호스트 추가](application-volume-group-add-hosts.md)
* [HSR에서 보조 데이터베이스로 SAP HANA 시스템의 볼륨 추가](application-volume-group-add-volume-secondary.md)
* [지역 간 복제를 사용 하 여 SAP HANA 시스템용 볼륨을 DR 시스템으로 추가](application-volume-group-disaster-recovery.md)
* [응용 프로그램 볼륨 그룹의 볼륨 관리](application-volume-group-manage-volumes.md)
* [응용 프로그램 볼륨 그룹 삭제](application-volume-group-delete.md)
* [응용 프로그램 볼륨 그룹 Faq](faq-application-volume-group.md)
* [응용 프로그램 볼륨 그룹 오류 문제 해결](troubleshoot-application-volume-groups.md)
