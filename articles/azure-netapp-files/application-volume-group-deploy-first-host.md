---
title: SAP HANA에 대 한 Azure NetApp Files 응용 프로그램 볼륨 그룹을 사용 하 여 첫 번째 SAP HANA 호스트 배포 Microsoft Docs
description: SAP HANA에 대 한 Azure NetApp Files 응용 프로그램 볼륨 그룹을 사용 하 여 첫 번째 SAP HANA 호스트를 배포 하는 방법을 설명 합니다.
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
ms.openlocfilehash: 41b6390ba126fdabc1a051894d2b91e0f11a17ad
ms.sourcegitcommit: 9ef0965834870700468c822ddcafc011881fc2d5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133486526"
---
# <a name="deploy-the-first-sap-hana-host-using-application-volume-group-for-sap-hana"></a>SAP HANA의 응용 프로그램 볼륨 그룹을 사용 하 여 첫 번째 SAP HANA 호스트 배포

모든 배포는 단일 SAP HANA 호스트의 볼륨으로 시작 합니다. 이는 대량 다중 호스트 설치의 경우에도 마찬가지입니다. 공유, 로그 백업 및 데이터 백업 볼륨은 첫 번째 호스트에 대해서만 생성 됩니다. 다중 호스트 환경의 다른 모든 호스트는 이러한 볼륨을 공유 하며, 각 추가 호스트에 대 한 데이터 및 로그 볼륨만 추가 합니다.

이 문서에서는 SAP HANA에 대 한 Azure NetApp Files 응용 프로그램 볼륨 그룹을 사용 하 여 첫 번째 SAP HANA 호스트를 배포 하는 방법을 설명 합니다.

## <a name="before-you-begin"></a>시작하기 전에

> [!IMPORTANT]
> SAP HANA에 대 한 Azure NetApp Files 응용 프로그램 볼륨 그룹은 현재 미리 보기로 제공 됩니다. [**SAP HANA waitlist 제출 페이지의 Azure NetApp Files 응용 프로그램 볼륨 그룹**](https://aka.ms/anfavgpreviewsignup)을 통해 기능에 액세스 하기 위한 waitlist 요청을 제출 해야 합니다. SAP HANA에 대 한 응용 프로그램 볼륨 그룹을 사용 하기 전에 Azure NetApp Files 팀의 공식 확인 전자 메일을 기다리십시오. 

[SAP HANA의 응용 프로그램 볼륨 그룹에 대 한 요구 사항 및 고려](application-volume-group-considerations.md)사항을 이해 해야 합니다. 

**[고정 권장 사항을](https://aka.ms/HANAPINNING)** 따르고 가용성 집합에서 HANA VM을 하나 이상 시작 해야 합니다. 

## <a name="steps"></a>단계 

1. NetApp 계정에서 **응용 프로그램 볼륨 그룹** 을 선택 하 고 **+ 그룹 추가** 를 클릭 합니다.

    [그룹을 추가 하는 ![ ](../media/azure-netapp-files/application-volume-group-add-group.png) 방법을 보여 주는 스크린샷 ](../media/azure-netapp-files/application-volume-group-add-group.png#lightbox)

2. 배포 유형에서 **SAP HANA** 를 선택 하 고 **다음** 을 클릭 합니다. 

    [![볼륨 그룹 만들기 창을 ](../media/azure-netapp-files/application-volume-group-create-group.png) 보여 주는 스크린샷 ](../media/azure-netapp-files/application-volume-group-create-group.png#lightbox)

3. **SAP HANA** 탭에서 HANA 관련 정보를 제공 합니다.   

    * **SAP ID (SID**):    
        3 개의 영숫자 문자 SAP HANA 시스템 식별자입니다.
    * **그룹 이름**: 볼륨 그룹 이름입니다. 다중 호스트 SAP HANA 시스템의 경우 각 호스트는 자체 그룹을 만듭니다. 첫 번째 호스트를 만드는 중 이므로 그룹 이름은로 시작 `'Name-proposal'-00001` 합니다.
    * **SAP 노드 메모리**:  
        이 값은 호스트의 SAP HANA 데이터베이스 크기를 정의 합니다. 필요한 볼륨 크기와 처리량을 계산 하는 데 사용 됩니다. 
    * **용량 오버 헤드 (%)**:  
        데이터 보호를 위해 스냅숏을 사용 하는 경우 추가 용량에 대 한 계획을 세워야 합니다. 이 필드에는 데이터 볼륨에 대 한 추가 크기 (%)가 추가 됩니다.  
        을 사용 하 여이 값을 예측할 수 있습니다 `"change rate per day" X "number of days retention"` .
    * **단일 호스트**:  
        SAP HANA 단일 호스트 시스템 또는 여러 호스트 시스템에 대 한 첫 번째 호스트에 대해이 옵션을 선택 합니다. 공유, 로그 백업 및 데이터 백업 볼륨만 첫 번째 호스트로 생성 됩니다.
    * **다중 호스트**: 다중 호스트 HANA 시스템에 호스트를 더 추가 하려면이 옵션을 선택 합니다.

    **다음: 볼륨 그룹** 을 클릭 합니다.

    [![SAP HANA 태그 ](../media/azure-netapp-files/application-sap-hana-tag.png) 를 보여 주는 스크린샷 ](../media/azure-netapp-files/application-sap-hana-tag.png#lightbox)

4. 볼륨 **그룹** 탭에서 볼륨 그룹 만들기에 대 한 정보를 제공 합니다.  

    * **근접 배치 그룹 (PPG)**:  
        Vm에 가까운 데이터, 로그 및 공유 볼륨을 만들도록 지정 합니다.
    * **용량 풀**:  
        모든 볼륨이 단일 수동 QoS 용량 풀에 배치 됩니다.  
        별도의 용량 풀에서 로그 백업 및 데이터 백업 볼륨을 만들려는 경우 해당 볼륨을 볼륨 그룹에 추가 하지 않도록 선택할 수 있습니다.
    * **가상 네트워크**:  
        Vm이 배치 되는 기존 VNet을 지정 합니다. 
    * **서브넷**:  
        NFS 내보내기의 IP 주소를 만들 위임 된 서브넷을 지정 합니다. 사용 가능한 IP 주소가 충분 한 위임 된 서브넷이 있는지 확인 합니다.

    **다음: 태그** 를 클릭 합니다. 

    [![볼륨 그룹 태그 ](../media/azure-netapp-files/application-volume-group-tag.png) 를 보여 주는 스크린샷 ](../media/azure-netapp-files/application-volume-group-tag.png#lightbox)

5. 볼륨 그룹 탭의 **태그** 섹션에서 볼륨에 필요한 만큼 태그를 추가할 수 있습니다.   

    **다음: 프로토콜** 을 클릭 합니다. 

    [![태그 ](../media/azure-netapp-files/application-add-tags.png) 를 추가 하는 방법을 보여 주는 스크린샷 ](../media/azure-netapp-files/application-add-tags.png#lightbox)

6. 볼륨 그룹 탭의 **프로토콜** 섹션에서 모든 볼륨에 공통적인 **내보내기 정책을** 수정할 수 있습니다.  

    **다음: 볼륨** 을 클릭 합니다. 

    [![프로토콜 태그 ](../media/azure-netapp-files/application-protocols-tag.png) 를 보여 주는 스크린샷 ](../media/azure-netapp-files/application-protocols-tag.png#lightbox)

7. **볼륨** 탭은 제안 된 볼륨 이름, 할당량 및 처리량을 사용 하 여 생성 되는 볼륨을 요약 합니다. 

    볼륨 탭에는 HANA Vm에 가까운 데이터, 로그 및 공유 볼륨만 생성 됨도 표시 됩니다.  다른 볼륨 (데이터 백업 및 로그 백업)은 해당 지역 내의 다른 위치에 생성 됩니다.

    데이터 백업 및 로그 백업 볼륨을 만드는 것은 선택 사항입니다.

    [![만드는 ](../media/azure-netapp-files/application-volume-list.png) 볼륨의 목록을 보여 주는 스크린샷 ](../media/azure-netapp-files/application-volume-list.png#lightbox)

8. **볼륨 탭에서** 볼륨 세부 정보를 보거나 변경 하려면 각 볼륨을 선택할 수 있습니다. 예를 들어 "데이터-*볼륨-이름*"을 선택 합니다.  

    SAP HANA 인증 제한으로 인해 일부 설정을 변경할 수 없습니다.
 
    볼륨을 선택 하는 경우 **기본 사항** 탭에서 다음 값을 변경할 수 있습니다.  

    * **볼륨 이름**:   
        제안 된 명명 규칙을 유지 하는 것이 좋습니다.
    * **할당량**:   
        볼륨 크기입니다.
    * **처리량**:  
        사용 사례에 필요한 값에 따라 개발 또는 테스트 시스템의 처리량 요구 사항을 줄일 수 있습니다.

    **다음:** 프로토콜을 클릭 하 여 프로토콜 설정을 검토 합니다. 

    [![볼륨 그룹 만들기 페이지 ](../media/azure-netapp-files/application-create-volume-basics-tab.png) 의 기본 탭을 보여 주는 스크린샷 ](../media/azure-netapp-files/application-create-volume-basics-tab.png#lightbox)

9. 볼륨의 **프로토콜** 탭에서 **파일 경로** (볼륨을 탑재할 수 있는 내보내기 이름)를 수정 하 고 필요에 따라 **정책을 내보낼** 수 있습니다.

    데이터 및 로그 볼륨에 대 한 프로토콜을 변경할 수 없습니다. 

    볼륨에 대 한 태그를 지정 하려면 **태그** 탭을 클릭 합니다. 볼륨 개요 페이지로 돌아가려면 **볼륨** 을 클릭 합니다.

    [![볼륨 그룹 만들기 페이지 ](../media/azure-netapp-files/application-create-volume-protocol-tab.png) 의 프로토콜 탭을 보여 주는 스크린샷 ](../media/azure-netapp-files/application-create-volume-protocol-tab.png#lightbox)

10. 볼륨 **페이지에서** 볼륨 세부 정보를 표시 합니다.  

    [![볼륨 세부 정보 ](../media/azure-netapp-files/application-volume-details.png) 를 포함 하는 볼륨 페이지를 보여 주는 스크린샷 ](../media/azure-netapp-files/application-volume-details.png#lightbox)

    `*`볼륨 그룹에서 데이터 백업 볼륨 또는 로그 백업 볼륨과 같이 선택적 볼륨 (로 표시 됨)을 제거 하려면 볼륨을 선택 하 고 **볼륨 제거** 를 클릭 합니다. 표시 되는 대화 상자에서 제거를 확인 합니다.

    > [!IMPORTANT]
    > 제거한 볼륨을 다시 볼륨 그룹에 추가할 수 없습니다.

    [볼륨을 제거 하는 ![ ](../media/azure-netapp-files/application-volume-remove.png) 방법을 보여 주는 스크린샷 ](../media/azure-netapp-files/application-volume-remove.png#lightbox)

    볼륨 개요 페이지로 돌아가려면 **볼륨** 을 클릭 합니다. **다음: 검토+생성** 을 클릭합니다.

11. **검토 + 만들기** 탭에는 모든 볼륨 및 생성 방법이 나열 됩니다.  볼륨 그룹 **만들기** 를 클릭 하 여 볼륨 그룹 만들기를 시작 합니다.

    [![검토 및 만들기 탭 ](../media/azure-netapp-files/application-review-create.png) 을 보여 주는 스크린샷 ](../media/azure-netapp-files/application-review-create.png#lightbox)

12. **볼륨 그룹** 배포 워크플로가 시작 되 고 진행률이 표시 됩니다. 이 프로세스를 완료하는 데 몇 분이 걸릴 수 있습니다.

    [![진행 ](../media/azure-netapp-files/application-deployment-in-progress.png) 중인 배포를 보여 주는 스크린샷 ](../media/azure-netapp-files/application-deployment-in-progress.png#lightbox)

    볼륨 그룹 목록을 표시 하 여 새 볼륨 그룹을 볼 수 있습니다. 새 볼륨 그룹을 선택 하 여 생성 되는 각 볼륨의 세부 정보 및 상태를 볼 수 있습니다.

    볼륨 그룹을 만드는 작업은 "모두" 또는 "없음" 작업입니다. 한 볼륨을 만들 수 없는 경우 나머지 모든 볼륨도 제거 됩니다.

    [![새 볼륨 그룹 ](../media/azure-netapp-files/application-new-volume-group.png) 을 보여 주는 스크린샷 ](../media/azure-netapp-files/application-new-volume-group.png#lightbox)

## <a name="next-steps"></a>다음 단계  

* [SAP HANA에 대 한 Azure NetApp Files 응용 프로그램 볼륨 그룹 이해](application-volume-group-introduction.md)
* [SAP HANA 애플리케이션 볼륨 그룹에 대한 요구 사항 및 고려 사항](application-volume-group-considerations.md)
* [SAP HANA 애플리케이션 볼륨 그룹을 사용하여 다중 호스트 SAP HANA 시스템에 호스트 추가](application-volume-group-add-hosts.md)
* [HSR에서 SAP HANA 시스템의 볼륨을 보조 데이터베이스로 추가](application-volume-group-add-volume-secondary.md)
* [지역 간 복제를 사용하여 SAP HANA 시스템에 대한 볼륨을 DR 시스템으로 추가](application-volume-group-disaster-recovery.md)
* [애플리케이션 볼륨 그룹의 볼륨 관리](application-volume-group-manage-volumes.md)
* [애플리케이션 볼륨 그룹 삭제](application-volume-group-delete.md)
* [애플리케이션 볼륨 그룹 FAQ](faq-application-volume-group.md)
* [애플리케이션 볼륨 그룹 오류 문제 해결](troubleshoot-application-volume-groups.md)
