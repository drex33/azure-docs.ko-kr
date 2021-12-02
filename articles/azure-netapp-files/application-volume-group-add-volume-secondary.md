---
title: Azure NetApp Files | 사용하여 HSR에서 SAP HANA 시스템의 볼륨을 보조 데이터베이스로 추가합니다. Microsoft Docs
description: 애플리케이션 볼륨 그룹을 사용하여 HSR(HANA 시스템 복제)에서 SAP HANA 시스템의 볼륨을 보조 데이터베이스로 추가하는 방법을 설명합니다.
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
ms.topic: how-to
ms.date: 11/19/2021
ms.author: b-hchen
ms.openlocfilehash: c5c940e820c10bcadcddb9e913674a74f66054de
ms.sourcegitcommit: 9ef0965834870700468c822ddcafc011881fc2d5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133487590"
---
# <a name="add-volumes-for-an-sap-hana-system-as-a-secondary-database-in-hsr"></a>HSR에서 SAP HANA 시스템의 볼륨을 보조 데이터베이스로 추가

이 문서에서는 애플리케이션 볼륨 그룹을 사용하여 HSR(HANA 시스템 복제)에서 SAP HANA 시스템의 볼륨을 보조 데이터베이스로 추가하는 방법을 설명합니다.

## <a name="hana-system-replication"></a>HANA 시스템 복제 

HSR(HANA 시스템 복제) 기능을 사용하면 SAP HANA 데이터베이스를 기본 SAP HANA 시스템에서 보조 SAP HANA 시스템으로 동기적으로 또는 비동기적으로 복제할 수 있습니다. HSR은 애플리케이션 수준 동기화를 사용합니다. 기본 및 보조 SAP HANA 시스템은 다음과 같은 차이점을 제외하고 동일한 스토리지 요구 사항을 갖습니다.

* 주 데이터베이스와 보조 SAP HANA 데이터베이스의 SAP ID(SID)는 동일하지만 볼륨 이름은 달라야 합니다.

* 보조 SAP HANA 시스템은 다른 위치(일반적으로 다른 영역 또는 지역)에 있을 수 있습니다.  따라서 근접 배치 그룹(PPG, 가용성 집합)은 다릅니다.

다음 다이어그램에서는 HSR의 개념을 보여 줍니다. 

 ![HANA 시스템 복제를 설명하는 다이어그램.](../media/azure-netapp-files/application-hana-system-replication.png) 


HSR을 사용하도록 설정하려면 보조 SAP HANA 시스템의 구성이 기본 SAP HANA 시스템과 동일해야 합니다. 즉, 기본 시스템이 단일 호스트 HANA 시스템인 경우 보조 SAP HANA 시스템도 단일 호스트 시스템이어야 합니다. 여러 호스트 시스템에도 마찬가지입니다.

보조 SAP HANA 시스템을 만드는 워크플로는 기본 SAP HANA 시스템을 만드는 워크플로와 비슷합니다. 그러나 주요 차이점은 SAP HANA 탭에서 **HSR 보조** 옵션을 선택하여 SAP HANA 시스템에 대한 복제 보조 시스템을 만들고 있음을 나타내야 한다는 것입니다.

## <a name="steps"></a>단계

이 섹션에서는 단일 호스트, 보조 SAP HANA 시스템을 만드는 예제를 보여줍니다.

1. NetApp 계정에서 **애플리케이션 볼륨 그룹을** 선택하고 **+그룹 추가를** 클릭합니다. 그런 다음 배포 유형에서 **SAP HANA** 선택하고 **다음을** 클릭합니다. 

2. **SAP HANA** 탭에서 HANA 관련 정보를 제공합니다. 

    > [!IMPORTANT]
    > **HSR 보조** 옵션을 선택하여 HANA 시스템에 대한 복제 보조 시스템을 만들고 있음을 나타내야 합니다.  

    * **SAP ID(SID)**:   
        세 개의 영숫자 문자 SAP HANA 시스템 식별자입니다.
    * **그룹 이름:**  
        볼륨 그룹 이름입니다. 
    * **SAP 노드 메모리:**  
        이 값은 호스트에서 SAP HANA 데이터베이스의 크기를 정의합니다. 필요한 볼륨 크기 및 처리량을 계산하는 데 사용됩니다. 
    * **용량 오버헤드(%)**:  
        데이터 보호를 위해 스냅샷을 사용하는 경우 추가 용량을 계획해야 합니다. 이 필드는 데이터 볼륨에 대한 추가 크기(%)를 추가합니다.  
        를 사용하여 이 값을 추정할 수 `"change rate per day" X "number of days retention"` 있습니다.
    * **단일 호스트:**  
        SAP HANA 단일 호스트 시스템 또는 다중 호스트 시스템의 첫 번째 호스트에 대해 이 옵션을 선택합니다. 공유 및 백업 볼륨은 첫 번째 호스트에서만 만들 수 있습니다.
    * **다중 호스트:**  
        다중 호스트 HANA 시스템에 호스트를 추가하려면 이 옵션을 선택합니다.
    * **HSR 보조:**  
        HSR(SAP HANA 시스템 복제)의 복제 보조 시스템이 될 HANA 데이터베이스를 만들려면 이 옵션을 선택합니다.

        **HSR 보조를** 선택하면 HA 설정을 나타내기 위해 포함할 볼륨 그룹 이름에 대한 명명 규칙도 `"-HA-"` 트리거됩니다.   

    **다음: 볼륨 그룹을** 클릭하여 계속합니다.

    [![HSR 구성의 HANA 섹션을 보여 주는 스크린샷. ](../media/azure-netapp-files/application-secondary-sap-hana.png) ](../media/azure-netapp-files/application-secondary-sap-hana.png#lightbox)

3.  볼륨 **그룹** 탭에서 볼륨 그룹을 만들기 위한 정보를 제공합니다.  

    * **PPG(근접 배치 그룹)**:  
        VM 가까이에서 데이터, 로그 및 공유 볼륨을 만들도록 지정합니다.
    * **용량 풀:**  
        모든 볼륨은 단일 수동 QoS 용량 풀에 배치됩니다.  
        로그 백업 및 데이터 백업 볼륨을 별도의 용량 풀에 만들려는 경우 볼륨 그룹에 해당 볼륨을 추가하지 않도록 선택할 수 있습니다.
    * **가상 네트워크**:  
        VM이 배치되는 기존 VNet을 지정합니다. 
    * **서브넷:**  
        NFS 내보내기용 IP 주소를 만들 위임된 서브넷을 지정합니다. 충분한 여유 IP 주소가 있는 위임된 서브넷이 있는지 확인합니다.

    **다음: 프로토콜** 을 클릭합니다.

4. 볼륨 그룹 탭의 **프로토콜** 섹션에서 모든 볼륨에 공통적인 **정책 내보내기** 를 수정할 수 있습니다.  

    **다음: 태그를** 클릭합니다.

5. **HSR 보조** 옵션이 선택되어 있으므로 볼륨 그룹 탭의 **태그** 섹션에 태그가 `HSRPartnerStorageResourceId` 채워집니다. 

    이 태그는 HSR 설정에서 해당 기본 볼륨의 볼륨 리소스 ID를 표시하므로 생성될 각 보조 볼륨에 대해 기본 볼륨을 식별할 수 있습니다. 

    각 볼륨에 대해 이 태그를 수정할 수 있습니다. 

    > [!IMPORTANT]
    > 그룹 수준에서 태그를 채우면 그룹의 모든 볼륨이 동일한 볼륨 ID로 채워집니다. 나중에 워크플로에서 또는 볼륨을 업데이트할 때 각 개별 볼륨의 볼륨 ID를 변경해야 합니다. 이 태그를 사용하는 것은 선택 사항입니다. 설명서 용도로만 사용됩니다.

    **다음: 볼륨을** 클릭합니다.

    [![볼륨 그룹 탭의 태그 섹션을 보여 주는 스크린샷. ](../media/azure-netapp-files/application-secondary-volume-group-tags.png) ](../media/azure-netapp-files/application-secondary-volume-group-tags.png#lightbox)

6. **볼륨 탭에는** 생성되는 볼륨에 대한 정보가 표시됩니다.  

    볼륨 명명 규칙에는 `"HA-"` 볼륨이 HSR 설정의 보조 시스템에 속함을 나타내는 접두사도 포함되어 있습니다.

    [![볼륨 그룹 탭을 보여 주는 스크린샷. ](../media/azure-netapp-files/application-secondary-volumes-tags.png) ](../media/azure-netapp-files/application-secondary-volumes-tags.png#lightbox)

7. **볼륨** 탭에서 볼륨에 대한 프로토콜 및 태그를 포함하여 볼륨 세부 정보를 보거나 변경할 각 볼륨을 선택할 수 있습니다.

    볼륨의 **태그** 섹션에서 태그를 해당 주 `HSRPartnerStorageResourceId` 볼륨의 리소스 ID로 채울 수 있습니다. 이 작업은 기본 볼륨만 표시합니다. 제공된 리소스 ID의 유효성을 검사하지 않습니다.

    [![태그 세부 정보를 보여 주는 스크린샷. ](../media/azure-netapp-files/application-secondary-volumes-tag-details.png) ](../media/azure-netapp-files/application-secondary-volumes-tag-details.png#lightbox)

    **볼륨을 클릭하여** 볼륨 개요 페이지로 돌아갑니다.  

8. **검토 + 만들기를** 클릭하여 만들 모든 볼륨을 나열합니다. **볼륨 그룹 만들기를** 클릭하여 볼륨 그룹 만들기를 시작합니다. 

다중 호스트 SAP HANA 시스템에 대한 HANA 호스트를 더 추가하려면 다중 호스트 SAP HANA 시스템에 호스트 추가의 단계를 [수행합니다.](application-volume-group-add-hosts.md) 복제 보조 시스템을 나타내려면 SAP HANA 탭에서 **HSR** 보조 옵션을 선택해야 합니다.   

## <a name="next-steps"></a>다음 단계  

* [SAP HANA Azure NetApp Files 애플리케이션 볼륨 그룹 이해](application-volume-group-introduction.md)
* [SAP HANA 애플리케이션 볼륨 그룹에 대한 요구 사항 및 고려 사항](application-volume-group-considerations.md)
* [SAP HANA 애플리케이션 볼륨 그룹을 사용하여 첫 번째 SAP HANA 호스트 배포](application-volume-group-deploy-first-host.md)
* [SAP HANA 애플리케이션 볼륨 그룹을 사용하여 다중 호스트 SAP HANA 시스템에 호스트 추가](application-volume-group-add-hosts.md)
* [지역 간 복제를 사용하여 SAP HANA 시스템에 대한 볼륨을 DR 시스템으로 추가](application-volume-group-disaster-recovery.md)
* [애플리케이션 볼륨 그룹의 볼륨 관리](application-volume-group-manage-volumes.md)
* [애플리케이션 볼륨 그룹 삭제](application-volume-group-delete.md)
* [애플리케이션 볼륨 그룹 FAQ](faq-application-volume-group.md)
* [애플리케이션 볼륨 그룹 오류 문제 해결](troubleshoot-application-volume-groups.md)
