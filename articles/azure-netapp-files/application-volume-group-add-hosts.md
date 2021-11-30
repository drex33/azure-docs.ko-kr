---
title: SAP HANA에 대 한 Azure NetApp Files 응용 프로그램 볼륨 그룹을 사용 하 여 다중 호스트 SAP HANA 시스템에 호스트 추가 | Microsoft Docs
description: SAP HANA 다중 호스트 설정에서 첫 번째 호스트를 만든 후 추가 HANA 호스트를 추가 하는 방법을 설명 합니다.
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
ms.openlocfilehash: 98d8da189ddf175de51ef7735f44eb823c217958
ms.sourcegitcommit: dcf3424d7149fceaea0340eb0657baa2c27882a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133271960"
---
# <a name="add-hosts-to-a-multiple-host-sap-hana-system-using-application-volume-group-for-sap-hana"></a>SAP HANA의 응용 프로그램 볼륨 그룹을 사용 하 여 다중 호스트 SAP HANA 시스템에 호스트 추가

다중 호스트 SAP HANA 데이터베이스를 빌드하는 것은 항상 첫 번째 호스트에 대 한 볼륨 그룹을 만드는 것부터 시작 됩니다. SAP HANA 다중 호스트 설정에서 첫 번째 호스트를 만들어야 합니다 ( [첫 번째 SAP HANA 호스트 배포](application-volume-group-deploy-first-host.md)참조). 그런 다음이 문서의 단계에 따라 추가 HANA 호스트를 추가할 수 있습니다. 

> [!IMPORTANT]
> SAP는 여러 호스트 SAP HANA 데이터베이스의 네 개 SAP HANA 호스트 마다 hana 공유 볼륨을 1 x RAM으로 크기를 조정 하는 것이 좋습니다. 초기 호스트를 사용 하 여 만든 공유 볼륨의 크기를 조정 하 여 필요한 용량을 조정 해야 할 수 있습니다.

## <a name="steps"></a>단계

1. NetApp 계정에서 **응용 프로그램 볼륨 그룹** 을 선택 하 고 **+ 그룹 추가** 를 클릭 합니다.  그런 다음 배포 유형에서 **SAP HANA** 를 선택 하 고 **다음** 을 클릭 합니다.   

2. 볼륨 그룹을 추가 하는 **SAP HANA** 탭에서 다음 정보를 지정 합니다.  

    * **SAP ID (SID)**:    
        3 개의 영숫자 문자 SAP HANA 시스템 식별자입니다.
    * **그룹 이름**:  
        볼륨 그룹 이름입니다. 다중 호스트 SAP HANA의 경우 각 호스트는 자체 그룹을 만듭니다.   
        아래 예제 스크린샷에서 그룹 이름은 `SAP-HANA-SH9-{HostId}` 일반 이름입니다. `{HostId}` 는 추가 하는 각 호스트에 해당 하는 숫자 호스트 ID로 바뀝니다. `{HostId}` 는 볼륨 그룹 설명의 숫자 호스트 ID로도 바뀝니다. `{HostId}`을 지정 하지 않으면 그룹 이름과 설명의 끝에 추가 됩니다.
    * **SAP 노드 메모리**:   
        이 값은 호스트의 SAP HANA 데이터베이스 크기를 정의 합니다. 필요한 볼륨 크기와 처리량을 계산 하는 데 사용 됩니다. 
    * **용량 오버 헤드 (%)**:   
        데이터 보호를 위해 스냅숏을 사용 하는 경우 추가 용량에 대 한 계획을 세워야 합니다. 이 필드에는 데이터 볼륨에 대 한 추가 크기 (%)가 추가 됩니다.  
        을 사용 하 여이 값을 예측할 수 있습니다 `"change rate per day" X "number of days retention"` .
    * **다중 호스트**:   
        여러 호스트 HANA에 호스트를 더 추가 하려면이 옵션을 선택 합니다.
    * **첫 번째 호스트 ID**:   
        추가 하려는 첫 번째 호스트의 번호를 지정 합니다. 처음으로 호스트를 추가 하는 경우이 값은 일반적으로 "2"입니다.
    * **호스트 수**:   
        다중 호스트 설정에 추가 하려는 호스트의 수를 지정 합니다.

    **다음: 볼륨 그룹** 을 클릭 합니다.

    [![호스트 ](../media/azure-netapp-files/application-multiple-hosts-sap-hana.png) 를 추가 하기 위한 HANA 섹션을 보여 주는 스크린샷 ](../media/azure-netapp-files/application-multiple-hosts-sap-hana.png#lightbox)

3. **볼륨 그룹** 탭에서 첫 번째 HANA 호스트를 만들 때와 동일한 입력을 제공 합니다.  

    그런 후에 **다음** 을 클릭 하 여 첫 번째 HANA 호스트와 동일한 입력을 제공 하는 **프로토콜** 및 **태그** 섹션을 계속 진행 합니다. [첫 번째 SAP HANA 호스트 배포](application-volume-group-deploy-first-host.md)에서 4-6 단계를 참조 하세요.   

    섹션의 끝에 도달 하면 **다음: 볼륨** 을 클릭 합니다. 

    추가 하는 호스트의 경우에는 데이터 및 로그 볼륨만 생성 됩니다. **볼륨** 페이지는를 자리 표시자로 사용 하 여 두 볼륨을 일반 형식으로 표시 합니다 `{HostId}` . 이 프로세스는 만들어지는 모든 호스트의 데이터 및 로그 볼륨에 대해 한 번만 변경 되므로 워크플로를 단순화 합니다.

    > [!NOTE]
    > SAP HANA를 사용 하 여 다중 호스트 설정에 다른 계산 볼륨 유형을 추가할 수 있습니다. 이 설정을 사용 하는 경우 한 번에 동일한 계산 형식에 대해 호스트를 하나만 추가 해야 합니다.

    **다음: 검토 + 만들기** 를 클릭 합니다.  

    [![호스트 ](../media/azure-netapp-files/application-multiple-hosts-volumes.png) 를 추가 하기 위한 볼륨 섹션을 보여 주는 스크린샷 ](../media/azure-netapp-files/application-multiple-hosts-volumes.png#lightbox)

4. **검토 + 만들기** 탭에서 `{HostId}` 자리 표시자는 생성 될 각 볼륨 그룹의 개별 숫자로 대체 됩니다. 

    **다음 그룹** 을 클릭 하 여 생성 되는 모든 볼륨 그룹 (각 호스트에 대해 하나씩)을 탐색할 수 있습니다. 특정 볼륨을 클릭 하 여 세부 정보를 볼 수도 있습니다.

    [![호스트 ](../media/azure-netapp-files/application-multiple-review-create.png) 를 추가 하기 위한 검토 및 만들기 섹션을 보여 주는 스크린샷 ](../media/azure-netapp-files/application-multiple-review-create.png#lightbox)

5. 볼륨 그룹을 탐색 한 후에는 **모든 그룹 만들기** 를 클릭 하 여 추가 중인 HANA 호스트의 모든 볼륨을 만듭니다. 

    [![모든 그룹 만들기 단추 ](../media/azure-netapp-files/application-multiple-create-groups.png) 를 표시 하는 스크린샷 ](../media/azure-netapp-files/application-multiple-create-groups.png#lightbox)

    **볼륨 그룹 만들기** 페이지에 "만들기" 상태의 추가 된 볼륨 그룹이 표시 됩니다.

## <a name="next-steps"></a>다음 단계  

* [SAP HANA에 대 한 Azure NetApp Files 응용 프로그램 볼륨 그룹 이해](application-volume-group-introduction.md)
* [SAP HANA의 응용 프로그램 볼륨 그룹에 대 한 요구 사항 및 고려 사항](application-volume-group-considerations.md)
* [SAP HANA의 응용 프로그램 볼륨 그룹을 사용 하 여 첫 번째 SAP HANA 호스트 배포](application-volume-group-deploy-first-host.md)
* [HSR에서 보조 데이터베이스로 SAP HANA 시스템의 볼륨 추가](application-volume-group-add-volume-secondary.md)
* [지역 간 복제를 사용 하 여 SAP HANA 시스템용 볼륨을 DR 시스템으로 추가](application-volume-group-disaster-recovery.md)
* [응용 프로그램 볼륨 그룹의 볼륨 관리](application-volume-group-manage-volumes.md)
* [응용 프로그램 볼륨 그룹 삭제](application-volume-group-delete.md)
* [응용 프로그램 볼륨 그룹 Faq](faq-application-volume-group.md)
* [응용 프로그램 볼륨 그룹 오류 문제 해결](troubleshoot-application-volume-groups.md)
