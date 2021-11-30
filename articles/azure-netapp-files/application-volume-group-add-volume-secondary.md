---
title: Azure NetApp Files를 사용 하 여 HSR에서 보조 데이터베이스로 SAP HANA 시스템의 볼륨 추가 Microsoft Docs
description: 응용 프로그램 볼륨 그룹을 사용 하 여 HSR (HANA System Replication)에서 보조 데이터베이스로 SAP HANA 시스템의 볼륨을 추가 하는 방법에 대해 설명 합니다.
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
ms.topic: how-to
ms.date: 11/19/2021
ms.author: b-juche
ms.openlocfilehash: 67f35cbe3b659119f814d980b149f55260e6c4e3
ms.sourcegitcommit: dcf3424d7149fceaea0340eb0657baa2c27882a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133271986"
---
# <a name="add-volumes-for-an-sap-hana-system-as-a-secondary-database-in-hsr"></a>HSR에서 보조 데이터베이스로 SAP HANA 시스템의 볼륨 추가

이 문서에서는 HSR (HANA System Replication)에서 응용 프로그램 볼륨 그룹을 사용 하 여 SAP HANA 시스템용 볼륨을 보조 데이터베이스로 추가 하는 방법을 설명 합니다.

## <a name="hana-system-replication"></a>HANA 시스템 복제 

HSR (HANA System Replication) 기능을 사용 하면 SAP HANA 데이터베이스가 주 SAP HANA 시스템에서 보조 SAP HANA 시스템으로 동기적 또는 비동기적으로 복제할 수 있습니다. HSR에서는 응용 프로그램 수준 동기화를 사용 합니다. 기본 및 보조 SAP HANA 시스템의 저장소 요구 사항은 다음과 같습니다. 단, 다음과 같은 차이점이 있습니다.

* 주 데이터베이스와 보조 SAP HANA 데이터베이스 모두 동일한 SAP ID (SID)를 갖지만 볼륨 이름은 달라 야 합니다.

* 보조 SAP HANA 시스템은 다른 위치 (일반적으로 다른 영역 또는 지역)에 있을 수 있습니다.  따라서 근접 배치 그룹 (PPG, 가용성 집합)이 다릅니다.

다음 다이어그램에서는 HSR의 개념을 보여 줍니다. 

 ![HANA 시스템 복제를 설명 하는 다이어그램입니다.](../media/azure-netapp-files/application-hana-system-replication.png) 


HSR를 사용 하도록 설정 하려면 보조 SAP HANA 시스템의 구성이 주 SAP HANA 시스템과 동일 해야 합니다. 즉, 기본 시스템이 단일 호스트 HANA 시스템이 면 보조 SAP HANA 시스템도 단일 호스트 시스템 이어야 합니다. 여러 호스트 시스템에도 동일 하 게 적용 됩니다.

보조 SAP HANA 시스템을 만들기 위한 워크플로는 주 SAP HANA 시스템을 만들기 위한 워크플로와 비슷합니다. 그러나 주요 차이점은 SAP HANA 탭에서 **Hsr 보조** 옵션을 선택 하 여 SAP HANA 시스템에 대 한 복제 보조 시스템을 만들고 있음을 나타내는 것입니다.

## <a name="steps"></a>단계

이 섹션에서는 단일 호스트, 보조 SAP HANA 시스템을 만드는 예를 보여 줍니다.

1. NetApp 계정에서 **응용 프로그램 볼륨 그룹** 을 선택 하 고 **+ 그룹 추가** 를 클릭 합니다. 그런 다음 배포 유형에서 **SAP HANA** 를 선택 하 고 **다음** 을 클릭 합니다. 

2. **SAP HANA** 탭에서 HANA 관련 정보를 제공 합니다. 

    > [!IMPORTANT]
    > HANA 시스템용 복제 보조 시스템을 만들고 있음을 나타내려면 **Hsr 보조** 옵션을 선택 해야 합니다.  

    * **SAP ID (SID)**:   
        3 개의 영숫자 문자 SAP HANA 시스템 식별자입니다.
    * **그룹 이름**:  
        볼륨 그룹 이름입니다. 
    * **SAP 노드 메모리**:  
        이 값은 호스트의 SAP HANA 데이터베이스 크기를 정의 합니다. 필요한 볼륨 크기와 처리량을 계산 하는 데 사용 됩니다. 
    * **용량 오버 헤드 (%)**:  
        데이터 보호를 위해 스냅숏을 사용 하는 경우 추가 용량에 대 한 계획을 세워야 합니다. 이 필드는 데이터 볼륨의 크기 (%)를 추가 합니다.  
        을 사용 하 여이 값을 예측할 수 있습니다 `"change rate per day" X "number of days retention"` .
    * **단일 호스트**:  
        SAP HANA 단일 호스트 시스템 또는 여러 호스트 시스템에 대 한 첫 번째 호스트에 대해이 옵션을 선택 합니다. 공유 볼륨과 백업 볼륨은 첫 번째 호스트 에서만 만들 수 있습니다.
    * **다중 호스트**:  
        다중 호스트 HANA 시스템에 호스트를 더 추가 하려면이 옵션을 선택 합니다.
    * **Hsr 보조**:  
        HSR (SAP HANA 시스템 복제)을 위한 복제 보조 시스템이 될 HANA 데이터베이스를 만들려면이 옵션을 선택 합니다.

        또한 **Hsr 보조** 를 선택 하면 `"-HA-"` HA 설정을 나타내는 데 포함할 볼륨 그룹 이름에 대 한 명명 규칙이 트리거됩니다.   

    계속 하려면 **다음: 볼륨 그룹** 을 클릭 합니다.

    [![HSR 구성 ](../media/azure-netapp-files/application-secondary-sap-hana.png) 의 HANA 섹션을 보여 주는 스크린샷 ](../media/azure-netapp-files/application-secondary-sap-hana.png#lightbox)

3.  볼륨 **그룹** 탭에서 볼륨 그룹 만들기에 대 한 정보를 제공 합니다.  

    * **근접 배치 그룹 (PPG)**:  
        Vm에 가까운 데이터, 로그 및 공유 볼륨을 만들도록 지정 합니다.
    * **용량 풀**:  
        모든 볼륨이 단일 수동 QoS 용량 풀에 배치 됩니다.  
        별도의 용량 풀에서 로그 백업 및 데이터 백업 볼륨을 만들려는 경우 해당 볼륨을 볼륨 그룹에 추가 하지 않도록 선택할 수 있습니다.
    * **가상 네트워크**:  
        Vm이 배치 되는 기존 VNet을 지정 합니다. 
    * **서브넷**:  
        NFS 내보내기의 IP 주소를 만들 위임 된 서브넷을 지정 합니다. 사용 가능한 IP 주소가 충분 한 위임 된 서브넷이 있는지 확인 합니다.

    **다음: 프로토콜** 을 클릭 합니다.

4. 볼륨 그룹 탭의 **프로토콜** 섹션에서 모든 볼륨에 공통적인 **내보내기 정책을** 수정할 수 있습니다.  

    **다음: 태그** 를 클릭 합니다.

5. **Hsr 보조** 옵션을 선택 했기 때문에 볼륨 그룹 탭의 **태그** 섹션은 태그로 채워집니다 `HSRPartnerStorageResourceId` . 

    이 태그는 생성 될 각 보조 볼륨에 대해 기본 볼륨을 식별할 수 있도록 HSR 설정에서 해당 주 볼륨의 볼륨 리소스 ID를 표시 합니다. 

    각 볼륨에 대해이 태그를 수정할 수 있습니다. 

    > [!IMPORTANT]
    > 그룹 수준에서 태그를 채우면 동일한 볼륨 ID를 사용 하 여 그룹의 모든 볼륨을 채웁니다. 나중에 워크플로의 각 개별 볼륨에 대 한 볼륨 ID를 변경 하거나 볼륨을 업데이트할 때 변경 해야 합니다. 이 태그를 사용 하는 것은 선택 사항입니다. 설명서 목적 으로만 사용 됩니다.

    **다음: 볼륨** 을 클릭 합니다.

    [![볼륨 그룹 탭 ](../media/azure-netapp-files/application-secondary-volume-group-tags.png) 의 태그 섹션을 보여 주는 스크린샷 ](../media/azure-netapp-files/application-secondary-volume-group-tags.png#lightbox)

6. **볼륨** 탭은 생성 되는 볼륨에 대 한 정보를 표시 합니다.  

    볼륨 명명 규칙에는 `"HA-"` 볼륨이 HSR 설치의 보조 시스템에 속함을 나타내는 접두사가 포함 되어 있습니다.

    [![볼륨 그룹 탭 ](../media/azure-netapp-files/application-secondary-volumes-tags.png) 을 보여 주는 스크린샷 ](../media/azure-netapp-files/application-secondary-volumes-tags.png#lightbox)

7. **볼륨 탭에서** 볼륨에 대 한 프로토콜 및 태그를 포함 하 여 볼륨 세부 정보를 보거나 변경 하려면 각 볼륨을 선택할 수 있습니다.

    볼륨의 **태그** 섹션에서 `HSRPartnerStorageResourceId` 해당 주 볼륨의 리소스 ID로 태그를 채울 수 있습니다. 이 작업은 주 볼륨만 표시 합니다. 제공 된 리소스 ID의 유효성은 검사 하지 않습니다.

    [![태그 세부 정보 ](../media/azure-netapp-files/application-secondary-volumes-tag-details.png) 를 표시 하는 스크린샷 ](../media/azure-netapp-files/application-secondary-volumes-tag-details.png#lightbox)

    볼륨 개요 페이지로 돌아가려면 **볼륨** 을 클릭 합니다.  

8. **검토 + 만들기** 를 클릭 하 여 생성 될 모든 볼륨을 나열 합니다. 볼륨 그룹 **만들기** 를 클릭 하 여 볼륨 그룹 만들기를 시작 합니다. 

다중 호스트 SAP HANA 시스템에 대해 HANA 호스트를 더 추가 하려면 [다중 호스트 SAP HANA 시스템에 호스트 추가](application-volume-group-add-hosts.md)의 단계를 따르세요. SAP HANA 탭에서 **Hsr 보조** 옵션을 선택 하 여 복제 보조 시스템을 지정 해야 합니다.   

## <a name="next-steps"></a>다음 단계  

* [SAP HANA에 대 한 Azure NetApp Files 응용 프로그램 볼륨 그룹 이해](application-volume-group-introduction.md)
* [SAP HANA의 응용 프로그램 볼륨 그룹에 대 한 요구 사항 및 고려 사항](application-volume-group-considerations.md)
* [SAP HANA의 응용 프로그램 볼륨 그룹을 사용 하 여 첫 번째 SAP HANA 호스트 배포](application-volume-group-deploy-first-host.md)
* [SAP HANA의 응용 프로그램 볼륨 그룹을 사용 하 여 다중 호스트 SAP HANA 시스템에 호스트 추가](application-volume-group-add-hosts.md)
* [지역 간 복제를 사용 하 여 SAP HANA 시스템용 볼륨을 DR 시스템으로 추가](application-volume-group-disaster-recovery.md)
* [응용 프로그램 볼륨 그룹의 볼륨 관리](application-volume-group-manage-volumes.md)
* [응용 프로그램 볼륨 그룹 삭제](application-volume-group-delete.md)
* [응용 프로그램 볼륨 그룹 Faq](faq-application-volume-group.md)
* [응용 프로그램 볼륨 그룹 오류 문제 해결](troubleshoot-application-volume-groups.md)
