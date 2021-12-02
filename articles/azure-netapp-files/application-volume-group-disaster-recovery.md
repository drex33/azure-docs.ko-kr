---
title: 지역 간 복제 | 사용하여 SAP HANA 시스템에 대한 볼륨을 DR 시스템으로 Azure NetApp Files 추가합니다. Microsoft Docs
description: 애플리케이션 볼륨 그룹을 사용하여 SAP HANA 시스템의 볼륨을 DR(재해 복구) 시스템으로 추가하는 방법을 설명합니다.
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
ms.openlocfilehash: f67488fc5d16b742cbff1257e3afd1332ab53c98
ms.sourcegitcommit: 9ef0965834870700468c822ddcafc011881fc2d5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133483691"
---
# <a name="add-volumes-for-an-sap-hana-system-as-a-dr-system-using-cross-region-replication"></a>지역 간 복제를 사용하여 SAP HANA 시스템에 대한 볼륨을 DR 시스템으로 추가

이 문서에서는 애플리케이션 볼륨 그룹을 사용하여 dr(재해 복구) 시스템으로 SAP HANA 시스템에 대한 볼륨을 추가하는 방법을 설명합니다. 이 구성에서는 Azure NetApp Files CRR(지역 간 복제) 기능을 사용합니다.

## <a name="crr-between-source-and-destination-hana-servers"></a>원본 및 대상 HANA 서버 간 CRR

Azure NetApp Files 지역 [간 복제](cross-region-replication-introduction.md) 기능을 사용하면 [지원되는 지역 간 복제 쌍](cross-region-replication-introduction.md#supported-region-pairs)간에 볼륨을 복제할 수 있습니다. 이 기능을 사용하면 DR(재해 복구)을 위해 원본 지역에서 대상 지역의 볼륨으로 볼륨을 [복제할](cross-region-replication-create-peering.md) 수 있습니다. 

HSR(HANA 시스템 복제)을 사용하는 대신 지역 간 복제를 사용하여 항상 실행되는 HANA 데이터베이스 서버 없이 데이터베이스를 보호할 수 있습니다. 지역 간 복제에 대해 지원되는 지역에 복제 대상 볼륨을 만들어야 합니다. SAP HANA 애플리케이션 볼륨 그룹은 대상 볼륨이 모든 SAP HANA 특정 요구 사항을 충족하는 올바른 볼륨 유형으로 생성되도록 합니다.

다음 다이어그램에서는 원본 HANA 서버와 대상 HANA 서버 간의 지역 간 복제를 보여 줍니다. 지역 간 복제는 비동기입니다. 따라서 모든 볼륨을 복제해야 하는 것은 아닙니다.  

 ![원본 HANA 서버와 대상 HANA 서버 간의 지역 간 복제를 보여 주는 다이어그램](../media/azure-netapp-files/application-cross-region-replication.png) 

> [!IMPORTANT]
> * 대상 지역에서 HANA 데이터베이스를 복구하려면 HANA 백업에 애플리케이션 일치 스토리지 스냅샷을 사용해야 합니다. AzAcSnap(Azure 애플리케이션 일관성 스냅샷 도구)과 같은 데이터 보호 솔루션을 사용하여 이러한 [스냅샷을](azacsnap-introduction.md) 만들 수 있습니다.
> * 적어도 데이터 볼륨 및 로그 백업 볼륨을 복제해야 합니다. 
> * 필요에 따라 데이터 백업 볼륨 및 공유 볼륨을 복제할 수 있습니다. 
> * 로그 볼륨을 *복제하면* 안 됩니다. 애플리케이션 볼륨 그룹은 로그 볼륨을 표준 볼륨으로 만듭니다.

## <a name="replication-schedules-rto-and-rpo"></a>복제 일정, RTO 및 RPO

다음 표에서는 복제 일정 옵션을 요약합니다. 또한 애플리케이션 볼륨 그룹에서 제안하는 기본 설정에 대해서도 설명합니다.

|     볼륨 유형    |     기본 복제 일정    |     사용 가능한 옵션    |     참고    |
|---|---|---|---|
|     데이터    |     매일    |     매일, 매시간    |     선택한 선택은 RTO(복구 시간 목표) 및 전송된 데이터의 양에 영향을 미칩니다.    |
|     로그    |     -    |     -    |     로그 볼륨은 복제되지 않습니다.    |
|     SAP 공유    |     10분마다    |     매시간, 매일 10분마다    |     SLA 요구 사항 및 공유 볼륨에 저장된 데이터에 따라 일정을 선택해야 합니다.    |
|     데이터 백업    |     매일    |     매일, 매주    |     데이터 백업 볼륨 복제는 선택 사항입니다.    |
|     로그 백업    |     10분마다    |     10분마다    |     이 설정은 RPO(복구 지점 목표)에 영향을 미칩니다.     |

복제 빈도 일정은 SLA에 영향을 미칩니다. 

* **RTO(복구 시간 목표)**:  
    복구하는 데 걸리는 최소 시간입니다.   
    사용 가능한 최신 애플리케이션 일치 스냅샷을 사용하여 복구하려면 사용 가능한 모든 로그 백업을 재생해야 합니다. RTO는 백업 빈도 및 데이터 볼륨 복제 빈도에 따라 달라집니다. 예를 들어 백업 빈도가 6시간마다이고 복제 일정이 "매일"인 경우 가장 오래된 백업은 30시간(24시간 + 6시간)이 될 수 있습니다. 이 시나리오에서는 최대 30시간의 로그 백업을 재생해야 합니다.
* **RPO(복구 지점 목표)**:  
    발생할 수 있는 최소 데이터 손실입니다.   
    SAP HANA 로그 백업 빈도는 일반적으로 15분이지만 이 설정은 다르게 구성할 수 있습니다. 로그 백업에 대해 10분 복제 일정을 가정할 경우 트랜잭션의 최대 손실은 `[15+10+transfer_time]` 분입니다.

## <a name="add-volumes"></a>볼륨 추가  

다음 예제에서는 볼륨을 SAP HANA 시스템에 추가합니다. 시스템은 지역 간 복제를 사용하는 DR 대상 시스템 역할을 합니다.

1. NetApp 계정에서 **애플리케이션 볼륨 그룹을** 선택하고 **+그룹 추가를** 클릭합니다. 그런 다음 배포 유형에서 **SAP HANA** 선택하고 **다음을** 클릭합니다. 

2. **SAP HANA** 탭에서 HANA 관련 정보를 제공합니다. 

    > [!IMPORTANT]
    > 지역 간 복제 대상으로 HANA 시스템을 만들고 있음을 나타내려면 **재해 복구 대상** 옵션을 선택해야 합니다.  

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
        SAP HANA 단일 호스트 시스템 또는 다중 호스트 시스템의 첫 번째 호스트에 대해 이 옵션을 선택합니다. 공유, 로그 백업 및 데이터 백업 볼륨만 첫 번째 호스트를 통해 생성됩니다.
    * **다중 호스트:**  
        다중 호스트 HANA 시스템에 호스트를 추가하는 경우 이 옵션을 선택합니다.
    * **재해 복구 대상:**  
        [지역 간 복제를](cross-region-replication-introduction.md)사용하여 HANA 시스템에 대한 볼륨을 DR 사이트로 만들려면 이 옵션을 선택합니다.  
    
        재해 **복구 대상을** 선택하면 재해 복구 설정을 나타내기 위해 포함할 볼륨 그룹 이름에 대한 명명 `"-DR-"` 규칙이 트리거됩니다. 

    **다음: 볼륨 그룹 을** 클릭합니다.    

    [![지역 간 복제 구성에서 볼륨 그룹 만들기 페이지를 보여 주는 스크린샷. ](../media/azure-netapp-files/application-cross-region-create-volume.png) ](../media/azure-netapp-files/application-cross-region-create-volume.png#lightbox)

3. 볼륨 **그룹** 탭에서 볼륨 그룹을 만들기 위한 정보를 제공합니다.

    * **PPG(근접 배치 그룹)**:  
        재해 복구 VM 가까이에 데이터 및 공유 볼륨을 만들도록 지정합니다.  
        복제에 VM이 필요하지 않더라도 볼륨을 프로비전하는 동안 PPG를 고정하기 위해 하나 이상의 VM을 시작해야 합니다.
    * **용량 풀:**  
        모든 볼륨은 단일 수동 QoS 용량 풀에 배치됩니다.   
        로그 백업 및 데이터 백업 볼륨을 별도의 용량 풀에 만들려는 경우 볼륨 그룹에 해당 볼륨을 추가하지 않도록 선택할 수 있습니다.
    * **가상 네트워크**:  
        VM이 배치되는 기존 VNet을 지정합니다. 
    * **서브넷:**  
        NFS 내보내기용 IP 주소를 만들 위임된 서브넷을 지정합니다. 충분한 여유 IP 주소가 있는 위임된 서브넷이 있는지 확인합니다.

    **다음: 프로토콜을** 클릭합니다. 

4. 볼륨 그룹 탭의 **프로토콜** 섹션에서 모든 볼륨에 공통적인 **정책 내보내기** 를 수정할 수 있습니다.

    **다음: 복제를** 클릭합니다.

5. 볼륨 그룹 탭의 **복제** 섹션에서 복제 일정 필드의 기본값은 "Multiple" (사용 안 함)입니다. 복제 된 볼륨에 대 한 기본 복제 일정은 서로 다릅니다. 따라서 볼륨 탭에서 각 볼륨에 대 한 복제 일정을 개별적으로 수정할 수 있으며 전체 볼륨 그룹에 대해서는 전역적으로 수정할 수 없습니다. 

    [![볼륨 그룹 만들기 페이지에서 여러 필드를 표시 하는 스크린샷 사용 하지 않도록 설정 됩니다. ](../media/azure-netapp-files/application-cross-region-multiple-disabled.png) ](../media/azure-netapp-files/application-cross-region-multiple-disabled.png#lightbox)

    **다음: 태그** 를 클릭 합니다.

6. 볼륨 그룹 탭의 **태그** 섹션에서 볼륨에 필요한 만큼 태그를 추가할 수 있습니다.   

    **다음: 볼륨** 을 클릭 합니다. 

7. 볼륨 **탭에** 는 볼륨 목록이 표시 됩니다.

    볼륨 명명 규칙에는 `"DR-"` 볼륨이 설치의 재해 복구 (대상) 쪽에 속하도록 지정 하는 접두사가 포함 되어 있습니다.

    볼륨 탭에는 볼륨 종류도 표시 됩니다. 
 
    * **DP** -지역 간 복제 설정의 대상을 나타냅니다. 이 유형의 볼륨은 온라인 상태가 아니지만 복제 모드에서 사용할 수 있습니다.
    * **RW** -읽기 및 쓰기가 허용 됨을 나타냅니다.

    로그 볼륨의 기본 형식은 RW 이며 설정을 변경할 수 없습니다.

    데이터, 공유 및 로그 백업 볼륨의 기본 유형은 DP 이며 설정을 변경할 수 없습니다.

    데이터 백업 볼륨의 기본 유형은 DP 이지만이 설정은 RW로 변경할 수 있습니다.  

    [![볼륨 그룹 만들기 페이지 ](../media/azure-netapp-files/application-cross-region-volume-types.png) 에서 볼륨 유형을 보여 주는 스크린샷 ](../media/azure-netapp-files/application-cross-region-volume-types.png#lightbox)

8. **원본 볼륨 ID** 를 지정 하려면 DP 유형으로 각 볼륨을 클릭 합니다. 자세한 내용은 [원본 볼륨 리소스 ID 찾기](cross-region-replication-create-peering.md#locate-the-source-volume-resource-id)를 참조 하세요. 
 
    필요에 따라 볼륨의 기본 복제 일정을 변경할 수 있습니다. 복제 일정 옵션은 [복제 일정, RTO 및 RPO를](#replication-schedules-rto-and-rpo) 참조 하세요. 

    [![볼륨 그룹 만들기 페이지 ](../media/azure-netapp-files/application-cross-region-replication-tab.png) 에서 복제 탭을 보여 주는 스크린샷 ](../media/azure-netapp-files/application-cross-region-replication-tab.png#lightbox)

9. 볼륨 그룹을 만든 후 [원본 볼륨에서 복제 권한 부여](cross-region-replication-create-peering.md#authorize-replication-from-the-source-volume)의 지침에 따라 복제를 설정 합니다.  

    1. 만든 각 DP 볼륨에 대해 볼륨 **리소스 ID** 를 복사 합니다.

    2. 각 원본 볼륨에 대해 **복제** 를 클릭 한 다음 **권한 부여** 를 클릭 합니다. 각 해당 대상 볼륨의 **리소스 ID** 를 붙여 넣습니다. 

## <a name="next-steps"></a>다음 단계  

* [SAP HANA에 대 한 Azure NetApp Files 응용 프로그램 볼륨 그룹 이해](application-volume-group-introduction.md)
* [SAP HANA의 응용 프로그램 볼륨 그룹에 대 한 요구 사항 및 고려 사항](application-volume-group-considerations.md)
* [SAP HANA의 응용 프로그램 볼륨 그룹을 사용 하 여 첫 번째 SAP HANA 호스트 배포](application-volume-group-deploy-first-host.md)
* [SAP HANA의 응용 프로그램 볼륨 그룹을 사용 하 여 다중 호스트 SAP HANA 시스템에 호스트 추가](application-volume-group-add-hosts.md)
* [HSR에서 보조 데이터베이스로 SAP HANA 시스템의 볼륨 추가](application-volume-group-add-volume-secondary.md)
* [응용 프로그램 볼륨 그룹의 볼륨 관리](application-volume-group-manage-volumes.md)
* [응용 프로그램 볼륨 그룹 삭제](application-volume-group-delete.md)
* [응용 프로그램 볼륨 그룹 Faq](faq-application-volume-group.md)
* [응용 프로그램 볼륨 그룹 오류 문제 해결](troubleshoot-application-volume-groups.md)
