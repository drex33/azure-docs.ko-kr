---
title: SAP HANA | Azure NetApp Files 애플리케이션 볼륨 그룹을 사용하여 첫 번째 SAP HANA 호스트 배포 Microsoft Docs
description: SAP HANA Azure NetApp Files 애플리케이션 볼륨 그룹을 사용하여 첫 번째 SAP HANA 호스트를 배포하는 방법을 설명합니다.
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
ms.openlocfilehash: ebd7366b744ac280928b408481570ed966f83856
ms.sourcegitcommit: dcf3424d7149fceaea0340eb0657baa2c27882a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133271969"
---
# <a name="deploy-the-first-sap-hana-host-using-application-volume-group-for-sap-hana"></a>SAP HANA 애플리케이션 볼륨 그룹을 사용하여 첫 번째 SAP HANA 호스트 배포

모든 배포는 단일 SAP HANA 호스트에 대한 볼륨으로 시작합니다. 대규모 다중 호스트 설치의 경우에도 마찬가지입니다. 공유, 로그 백업 및 데이터 백업 볼륨은 첫 번째 호스트에 대해서만 생성됩니다. 다중 호스트 환경의 다른 모든 호스트는 이러한 볼륨을 공유하고 각 추가 호스트에 대한 데이터 및 로그 볼륨만 추가합니다.

이 문서에서는 SAP HANA Azure NetApp Files 애플리케이션 볼륨 그룹을 사용하여 첫 번째 SAP HANA 호스트를 배포하는 방법을 설명합니다.

## <a name="before-you-begin"></a>시작하기 전에

> [!IMPORTANT]
> SAP HANA 대한 Azure NetApp Files 애플리케이션 볼륨 그룹은 현재 미리 보기로 제공됩니다. SAP HANA 대기 목록 제출 페이지에 대한 Azure NetApp Files 애플리케이션 볼륨 그룹을 통해 기능에 [**액세스하기 위한 대기 목록**](https://aka.ms/anfavgpreviewsignup)요청을 제출해야 합니다. SAP HANA 애플리케이션 볼륨 그룹을 사용하기 전에 Azure NetApp Files 팀의 공식 확인 이메일을 기다립니다. 

SAP HANA 애플리케이션 [볼륨 그룹에 대한 요구 사항 및 고려 사항을](application-volume-group-considerations.md)이해해야 합니다. 

**[고정 권장 사항을](https://aka.ms/HANAPINNING)** 따르고 가용성 집합에 하나 이상의 HANA VM이 시작되어야 합니다. 

## <a name="steps"></a>단계 

1. NetApp 계정에서 **애플리케이션 볼륨 그룹을** 선택하고 **+그룹 추가를** 클릭합니다.

    [![그룹을 추가하는 방법을 보여 주는 스크린샷 ](../media/azure-netapp-files/application-volume-group-add-group.png) ](../media/azure-netapp-files/application-volume-group-add-group.png#lightbox)

2. 배포 유형에서 **SAP HANA** 선택하고 **다음을** 클릭합니다. 

    [![볼륨 그룹 만들기 창을 보여 주는 스크린샷. ](../media/azure-netapp-files/application-volume-group-create-group.png) ](../media/azure-netapp-files/application-volume-group-create-group.png#lightbox)

3. **SAP HANA** 탭에서 HANA 관련 정보를 제공합니다.   

    * **SAP ID(SID):**    
        세 개의 영숫자 문자 SAP HANA 시스템 식별자입니다.
    * **그룹 이름:** 볼륨 그룹 이름입니다. 다중 호스트 SAP HANA 시스템의 경우 각 호스트는 자체 그룹을 만듭니다. 첫 번째 호스트를 만들기 때문에 그룹 이름은 로 `'Name-proposal'-00001` 시작합니다.
    * **SAP 노드 메모리:**  
        이 값은 호스트에서 SAP HANA 데이터베이스의 크기를 정의합니다. 필요한 볼륨 크기 및 처리량을 계산하는 데 사용됩니다. 
    * **용량 오버헤드(%)**:  
        데이터 보호를 위해 스냅샷을 사용하는 경우 추가 용량을 계획해야 합니다. 이 필드는 데이터 볼륨에 대한 추가 크기(%)를 추가합니다.  
        를 사용하여 이 값을 추정할 수 `"change rate per day" X "number of days retention"` 있습니다.
    * **단일 호스트:**  
        SAP HANA 단일 호스트 시스템 또는 다중 호스트 시스템의 첫 번째 호스트에 대해 이 옵션을 선택합니다. 공유, 로그 백업 및 데이터 백업 볼륨만 첫 번째 호스트를 통해 생성됩니다.
    * **다중 호스트:** 다중 호스트 HANA 시스템에 호스트를 추가하려면 이 옵션을 선택합니다.

    **다음: 볼륨 그룹 을** 클릭합니다.

    [![SAP HANA 태그를 ](../media/azure-netapp-files/application-sap-hana-tag.png) 보여 주는 스크린샷 ](../media/azure-netapp-files/application-sap-hana-tag.png#lightbox)

4. 볼륨 **그룹** 탭에서 볼륨 그룹을 만들기 위한 정보를 제공합니다.  

    * **PPG(근접 배치 그룹)**:  
        VM 가까이에서 데이터, 로그 및 공유 볼륨을 만들도록 지정합니다.
    * **용량 풀:**  
        모든 볼륨은 단일 수동 QoS 용량 풀에 배치됩니다.  
        로그 백업 및 데이터 백업 볼륨을 별도의 용량 풀에 만들려는 경우 볼륨 그룹에 해당 볼륨을 추가하지 않도록 선택할 수 있습니다.
    * **가상 네트워크**:  
        VM이 배치되는 기존 VNet을 지정합니다. 
    * **서브넷:**  
        NFS 내보내기용 IP 주소를 만들 위임된 서브넷을 지정합니다. 충분한 여유 IP 주소가 있는 위임된 서브넷이 있는지 확인합니다.

    **다음: 태그를** 클릭합니다. 

    [![볼륨 그룹 태그를 보여 주는 스크린샷. ](../media/azure-netapp-files/application-volume-group-tag.png) ](../media/azure-netapp-files/application-volume-group-tag.png#lightbox)

5. 볼륨 그룹 탭의 **태그** 섹션에서 필요에 따라 볼륨에 태그를 추가할 수 있습니다.   

    **다음: 프로토콜** 을 클릭합니다. 

    [![태그를 추가하는 방법을 보여 주는 스크린샷 ](../media/azure-netapp-files/application-add-tags.png) ](../media/azure-netapp-files/application-add-tags.png#lightbox)

6. 볼륨 그룹 탭의 **프로토콜** 섹션에서 모든 볼륨에 공통적인 **정책 내보내기** 를 수정할 수 있습니다.  

    **다음: 볼륨을** 클릭합니다. 

    [![프로토콜 태그를 ](../media/azure-netapp-files/application-protocols-tag.png) 보여 주는 스크린샷 ](../media/azure-netapp-files/application-protocols-tag.png#lightbox)

7. **볼륨** 탭에는 제안된 볼륨 이름, 할당량 및 처리량으로 생성되는 볼륨이 요약되어 있습니다. 

    볼륨 탭에는 HANA VM 가까이에 데이터, 로그 및 공유 볼륨만 생성된다는 것도 표시됩니다.  다른 볼륨(데이터 백업 및 로그 백업)은 지역 내의 다른 위치에 만들어집니다.

    데이터 백업 및 로그 백업 볼륨 만들기는 선택 사항입니다.

    [![생성되는 볼륨 목록을 보여 주는 스크린샷. ](../media/azure-netapp-files/application-volume-list.png) ](../media/azure-netapp-files/application-volume-list.png#lightbox)

8. 볼륨 탭에서 각 **볼륨을** 선택하여 볼륨 세부 정보를 보거나 변경할 수 있습니다. 예를 들어 "data-*volume-name"을* 선택합니다.  

    SAP HANA 인증 제한으로 인해 모든 설정을 변경할 수 있는 것은 아닙니다.
 
    볼륨을 선택하면 기본 탭에서 다음 값을 변경할 수 **있습니다.**  

    * **볼륨 이름:**   
        제안된 명명 규칙을 유지하는 것이 좋습니다.
    * **할당량:**   
        볼륨 크기입니다.
    * **처리량**:  
        사용 사례에 필요한 값에 따라 개발 또는 테스트 시스템의 처리량 요구 사항을 줄일 수 있습니다.

    **다음: 프로토콜을** 클릭하여 프로토콜 설정을 검토합니다. 

    [![볼륨 그룹 만들기 페이지의 기본 사항 탭을 보여 주는 스크린샷. ](../media/azure-netapp-files/application-create-volume-basics-tab.png) ](../media/azure-netapp-files/application-create-volume-basics-tab.png#lightbox)

9. 볼륨의 **프로토콜** 탭에서 필요에 따라 **파일 경로(볼륨을** 탑재할 수 있는 내보내기 이름) 및 **내보내기 정책을** 수정할 수 있습니다.

    데이터 및 로그 볼륨에 대한 프로토콜은 변경할 수 없습니다. 

    볼륨에 **대한 태그를** 지정하려면 태그 탭을 클릭합니다. 또는 **볼륨을** 클릭하여 볼륨 개요 페이지로 돌아갑니다.

    [![볼륨 그룹 만들기 페이지의 프로토콜 탭을 보여 주는 스크린샷. ](../media/azure-netapp-files/application-create-volume-protocol-tab.png) ](../media/azure-netapp-files/application-create-volume-protocol-tab.png#lightbox)

10. 볼륨 페이지에 **볼륨** 세부 정보가 표시됩니다.  

    [![볼륨 세부 정보가 있는 볼륨 페이지를 보여 주는 스크린샷. ](../media/azure-netapp-files/application-volume-details.png) ](../media/azure-netapp-files/application-volume-details.png#lightbox)

    볼륨 그룹에서 데이터 백업 볼륨 또는 로그 백업 볼륨과 같은 선택적 `*` 볼륨(로 표시)을 제거하려면 볼륨을 선택하고 **볼륨 제거를** 클릭합니다. 표시되는 대화 상자에서 제거를 확인합니다.

    > [!IMPORTANT]
    > 제거된 볼륨을 볼륨 그룹에 다시 추가할 수 없습니다.

    [![볼륨을 제거하는 방법을 보여 주는 스크린샷 ](../media/azure-netapp-files/application-volume-remove.png) ](../media/azure-netapp-files/application-volume-remove.png#lightbox)

    **볼륨을 클릭하여** 볼륨 개요 페이지로 돌아갑니다. **다음: 검토+생성** 을 클릭합니다.

11. **검토 + 만들기** 탭에는 모든 볼륨과 볼륨을 만드는 방법이 나열됩니다.  **볼륨 그룹 만들기를** 클릭하여 볼륨 그룹 만들기를 시작합니다.

    [![검토 및 만들기 탭을 보여 주는 스크린샷. ](../media/azure-netapp-files/application-review-create.png) ](../media/azure-netapp-files/application-review-create.png#lightbox)

12. **볼륨 그룹** 배포 워크플로가 시작되고 진행이 표시됩니다. 이 프로세스를 완료하는 데 몇 분이 걸릴 수 있습니다.

    [![진행 중인 배포 창을 보여 주는 스크린샷. ](../media/azure-netapp-files/application-deployment-in-progress.png) ](../media/azure-netapp-files/application-deployment-in-progress.png#lightbox)

    볼륨 그룹 목록을 표시하여 새 볼륨 그룹을 볼 수 있습니다. 새 볼륨 그룹을 선택하여 생성되는 각 볼륨의 세부 정보 및 상태를 확인할 수 있습니다.

    볼륨 그룹을 만드는 작업은 "all-or-none" 작업입니다. 하나의 볼륨을 만들 수 없는 경우 나머지 볼륨도 모두 제거됩니다.

    [![새 볼륨 그룹을 보여 주는 스크린샷. ](../media/azure-netapp-files/application-new-volume-group.png) ](../media/azure-netapp-files/application-new-volume-group.png#lightbox)

## <a name="next-steps"></a>다음 단계  

* [SAP HANA Azure NetApp Files 애플리케이션 볼륨 그룹 이해](application-volume-group-introduction.md)
* [SAP HANA의 응용 프로그램 볼륨 그룹에 대 한 요구 사항 및 고려 사항](application-volume-group-considerations.md)
* [SAP HANA의 응용 프로그램 볼륨 그룹을 사용 하 여 다중 호스트 SAP HANA 시스템에 호스트 추가](application-volume-group-add-hosts.md)
* [HSR에서 보조 데이터베이스로 SAP HANA 시스템의 볼륨 추가](application-volume-group-add-volume-secondary.md)
* [지역 간 복제를 사용 하 여 SAP HANA 시스템용 볼륨을 DR 시스템으로 추가](application-volume-group-disaster-recovery.md)
* [응용 프로그램 볼륨 그룹의 볼륨 관리](application-volume-group-manage-volumes.md)
* [응용 프로그램 볼륨 그룹 삭제](application-volume-group-delete.md)
* [응용 프로그램 볼륨 그룹 Faq](faq-application-volume-group.md)
* [응용 프로그램 볼륨 그룹 오류 문제 해결](troubleshoot-application-volume-groups.md)
