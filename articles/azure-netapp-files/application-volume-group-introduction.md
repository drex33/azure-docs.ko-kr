---
title: SAP HANA | Azure NetApp Files 애플리케이션 볼륨 그룹 이해 Microsoft Docs
description: SAP HANA Azure NetApp Files 애플리케이션 볼륨 그룹의 사용 사례 및 주요 기능에 대해 설명합니다.
services: azure-netapp-files
documentationcenter: ''
author: b-hchen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/19/2021
ms.author: b-hchen
ms.openlocfilehash: face9df57ae0cd59cc0e34191b241a37ffc2ff4c
ms.sourcegitcommit: 9ef0965834870700468c822ddcafc011881fc2d5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133483634"
---
# <a name="understand-azure-netapp-files-application-volume-group-for-sap-hana"></a>SAP HANA Azure NetApp Files 애플리케이션 볼륨 그룹 이해 

이 문서는 SAP HANA Azure NetApp Files 애플리케이션 볼륨 그룹의 사용 사례 및 주요 기능을 이해하는 데 도움이 됩니다. 

> [!IMPORTANT]
> SAP HANA Azure NetApp Files 애플리케이션 볼륨 그룹은 현재 미리 보기로 제공됩니다. SAP HANA 대기 목록 제출 페이지에 대한 Azure NetApp Files 애플리케이션 볼륨 그룹을 통해 기능에 [**액세스하기 위한 대기 목록**](https://aka.ms/anfavgpreviewsignup)요청을 제출해야 합니다. SAP HANA 애플리케이션 볼륨 그룹을 사용하기 전에 Azure NetApp Files 팀의 공식 확인 이메일을 기다립니다. 

SAP HANA 애플리케이션 볼륨 그룹을 사용하면 모범 사례에 따라 SAP HANA 데이터베이스를 설치하고 운영하는 데 필요한 모든 볼륨을 배포할 수 있습니다. 필요한 SAP HANA 볼륨(데이터, 로그, 공유, 로그 백업 및 데이터 백업 볼륨 포함)을 개별적으로 만드는 대신 SAP HANA 대한 애플리케이션 볼륨 그룹은 단일 "원자성" 호출로 이러한 볼륨을 만듭니다. 원자성 호출은 모든 볼륨 또는 볼륨이 전혀 생성되지 않도록 합니다.

SAP HANA 애플리케이션 볼륨 그룹은 프로세스를 단순화하고 표준화하여 SAP HANA 볼륨 배포를 간소화하는 데 도움이 되는 기술적 개선 기능을 제공합니다. 따라서 볼륨에 대한 개별 QoS 또는 크기와 같은 기술 설정을 관리하는 대신 애플리케이션 요구에 집중할 수 있습니다. 

## <a name="key-features"></a>주요 특징

SAP HANA 애플리케이션 볼륨 그룹은 모든 지역에서 지원됩니다. 다음과 같은 주요 기능을 제공합니다.

* 다음을 포함하여 단일 및 여러 호스트 설정에 대한 SAP HANA 구성 지원 
    * 단일 또는 주 SAP HANA 데이터베이스의 볼륨
    * HSR(SAP HANA 시스템 복제) 보조 시스템의 볼륨
    * [지역 간 복제를](cross-region-replication-introduction.md) 사용하는 DR(재해 복구) 시나리오용 볼륨

* 다음 볼륨 만들기: 
    * SAP HANA 데이터 볼륨(각 데이터베이스 호스트에 대해 하나씩)
    * SAP HANA 로그 볼륨(각 데이터베이스 호스트에 대해 하나씩)
    * 공유 볼륨 SAP HANA(첫 번째 SAP HANA 호스트에만 해당)
    * 로그 백업 볼륨(선택 사항)
    * 파일 기반 데이터 백업 볼륨(선택 사항)

* [수동 QoS 용량 풀에서](manage-manual-qos-capacity-pool.md)볼륨 만들기 볼륨 크기 및 필요한 성능(MiB/s)은 데이터베이스의 메모리 크기에 대한 사용자 입력을 기반으로 제안됩니다.

* 애플리케이션 볼륨 그룹 GUI 및 ARM(Azure Resource Manager) 템플릿은 크기 조정 관리 및 볼륨 생성을 간소화하는 모범 사례를 제공합니다. 예: 
    * SAP SID(시스템 ID) 및 볼륨 유형에 따라 볼륨 명명 규칙 제안
    * 메모리 크기에 따라 크기 및 성능 계산

SAP HANA 애플리케이션 볼륨 그룹을 사용하면 배포 프로세스를 간소화하고 SAP HANA 워크로드의 스토리지 성능을 높일 수 있습니다. 새로운 기능 중 일부는 다음과 같습니다.

* 수동 고정 대신 PPG(근접 배치 그룹)를 사용합니다.
    * PPG를 사용하여 SAP HANA VM을 고정하여 가능한 가장 낮은 대기 시간을 최소화합니다. 이 PPG는 데이터, 로그 및 공유 볼륨이 SAP HANA VM과 가까운 곳에 생성하도록 강제하는 데 사용됩니다. 자세한 내용은 [근접 배치 그룹에 대한 모범 사례를](application-volume-group-considerations.md#best-practices-about-proximity-placement-groups) 참조하세요.

* 데이터 및 로그 볼륨에 대한 다른 IP 주소.
    * 이 설정은 SAP HANA 데이터베이스에 대해 더 나은 성능과 처리량을 제공합니다.

## <a name="next-steps"></a>다음 단계

* [SAP HANA 애플리케이션 볼륨 그룹에 대한 요구 사항 및 고려 사항](application-volume-group-considerations.md)
* [SAP HANA 애플리케이션 볼륨 그룹을 사용하여 첫 번째 SAP HANA 호스트 배포](application-volume-group-deploy-first-host.md)
* [SAP HANA 애플리케이션 볼륨 그룹을 사용하여 다중 호스트 SAP HANA 시스템에 호스트 추가](application-volume-group-add-hosts.md)
* [HSR에서 SAP HANA 시스템의 볼륨을 보조 데이터베이스로 추가](application-volume-group-add-volume-secondary.md)
* [지역 간 복제를 사용하여 SAP HANA 시스템에 대한 볼륨을 DR 시스템으로 추가](application-volume-group-disaster-recovery.md)
* [애플리케이션 볼륨 그룹의 볼륨 관리](application-volume-group-manage-volumes.md)
* [애플리케이션 볼륨 그룹 삭제](application-volume-group-delete.md)
* [애플리케이션 볼륨 그룹 FAQ](faq-application-volume-group.md)
* [애플리케이션 볼륨 그룹 오류 문제 해결](troubleshoot-application-volume-groups.md)
