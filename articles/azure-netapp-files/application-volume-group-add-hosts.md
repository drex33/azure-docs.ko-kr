---
title: 용 Azure NetApp Files 애플리케이션 볼륨 그룹을 사용하여 다중 호스트 SAP HANA 시스템에 호스트 추가 SAP HANA | Microsoft Docs
description: SAP HANA 다중 호스트 설정에서 첫 번째 호스트를 만든 후 HANA 호스트를 추가하는 방법을 설명합니다.
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
ms.openlocfilehash: 28216e880a79cf233ed2312d4e84d1ea39b3ac1c
ms.sourcegitcommit: 9ef0965834870700468c822ddcafc011881fc2d5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133480512"
---
# <a name="add-hosts-to-a-multiple-host-sap-hana-system-using-application-volume-group-for-sap-hana"></a>용 애플리케이션 볼륨 그룹을 사용하여 다중 호스트 SAP HANA 시스템에 호스트 추가 SAP HANA

다중 호스트 SAP HANA 데이터베이스를 빌드하는 것은 항상 첫 번째 호스트에 대한 볼륨 그룹을 만드는 것부터 시작합니다. SAP HANA 다중 호스트 설정에서 첫 번째 호스트를 만들어야 [합니다(첫 번째 SAP HANA 호스트 배포](application-volume-group-deploy-first-host.md)참조). 그런 다음, 이 문서의 단계에 따라 HANA 호스트를 더 추가할 수 있습니다. 

> [!IMPORTANT]
> SAP에서는 다중 호스트 SAP HANA 데이터베이스의 4개 SAP HANA 호스트마다 HANA 공유 볼륨의 크기를 HANA 호스트의 RAM 1 x로 크기를 정하는 것이 좋습니다. 필요한 용량에 맞게 초기 호스트를 사용하여 만든 공유 볼륨의 크기를 조정해야 할 수 있습니다.

## <a name="steps"></a>단계

1. NetApp 계정에서 **애플리케이션 볼륨 그룹을** 선택하고 **+그룹 추가를** 클릭합니다.  그런 다음 배포 유형에서 **SAP HANA** 선택하고 **다음을** 클릭합니다.   

2. 볼륨 그룹을 추가하기 위한 **SAP HANA** 탭에서 다음 정보를 지정합니다.  

    * **SAP ID(SID)**:    
        세 개의 영숫자 문자 SAP HANA 시스템 식별자입니다.
    * **그룹 이름:**  
        볼륨 그룹 이름입니다. 다중 호스트 SAP HANA 경우 각 호스트는 자체 그룹을 만듭니다.   
        아래 예제 스크린샷에서 그룹 이름은 `SAP-HANA-SH9-{HostId}` 일반 이름입니다. `{HostId}` 는 추가하는 각 호스트에 해당하는 숫자 호스트 ID로 대체됩니다. `{HostId}` 는 볼륨 그룹 설명에 대한 숫자 호스트 ID로도 대체됩니다. 를 지정하지 않으면 `{HostId}` 그룹 이름과 설명 모두에 대한 끝에 추가됩니다.
    * **SAP 노드 메모리:**   
        이 값은 호스트에서 SAP HANA 데이터베이스의 크기를 정의합니다. 필요한 볼륨 크기 및 처리량을 계산하는 데 사용됩니다. 
    * **용량 오버헤드(%)**:   
        데이터 보호를 위해 스냅샷을 사용하는 경우 추가 용량을 계획해야 합니다. 이 필드는 데이터 볼륨에 대한 추가 크기(%)를 추가합니다.  
        를 사용하여 이 값을 추정할 수 `"change rate per day" X "number of days retention"` 있습니다.
    * **다중 호스트:**   
        다중 호스트 HANA에 호스트를 추가하려면 이 옵션을 선택합니다.
    * **첫 번째 호스트 ID:**   
        추가하려는 첫 번째 호스트의 수를 지정합니다. 호스트를 처음 추가하는 경우 이 값은 일반적으로 "2"입니다.
    * **호스트 수:**   
        다중 호스트 설정에 추가할 호스트 수를 지정합니다.

    **다음: 볼륨 그룹 을** 클릭합니다.

    [![호스트를 추가하기 위한 HANA 섹션을 보여 주는 스크린샷 ](../media/azure-netapp-files/application-multiple-hosts-sap-hana.png) ](../media/azure-netapp-files/application-multiple-hosts-sap-hana.png#lightbox)

3. 볼륨 **그룹** 탭에서 첫 번째 HANA 호스트를 만들 때와 동일한 입력을 제공합니다.  

    그런 **다음, 다음을** 클릭하고 **프로토콜** 및 **태그 섹션을** 계속 진행하여 첫 번째 HANA 호스트와 동일한 입력을 제공합니다. (첫 [번째 SAP HANA 호스트 배포의](application-volume-group-deploy-first-host.md)4-6단계를 참조하세요.)   

    섹션의 끝에 도달하면 **다음: 볼륨을** 클릭합니다. 

    추가하는 추가 호스트의 경우 데이터 및 로그 볼륨만 생성됩니다. **볼륨** 페이지는 를 자리 표시자로 사용하여 두 볼륨을 일반 `{HostId}` 형식으로 표시합니다. 이 프로세스는 생성되는 모든 호스트의 데이터 및 로그 볼륨에 대해 한 번만 변경되므로 워크플로를 간소화합니다.

    > [!NOTE]
    > SAP HANA 사용하면 다중 호스트 설정에 다른 컴퓨팅 볼륨 유형을 추가할 수 있습니다. 이 설정을 사용하는 경우 동일한 컴퓨팅 유형에 대해 한 번에 하나의 호스트만 추가해야 합니다.

    **다음: 검토 + 만들기를** 클릭합니다.  

    [![호스트를 추가하기 위한 볼륨 섹션을 보여 주는 스크린샷 ](../media/azure-netapp-files/application-multiple-hosts-volumes.png) ](../media/azure-netapp-files/application-multiple-hosts-volumes.png#lightbox)

4. 검토 **+ 만들기** 탭에서 `{HostId}` 자리 표시자는 생성될 각 볼륨 그룹의 개별 번호로 대체됩니다. 

    **다음 그룹을** 클릭하여 생성되는 모든 볼륨 그룹(각 호스트에 대해 하나씩)을 탐색할 수 있습니다. 특정 볼륨을 클릭하여 세부 정보를 볼 수도 있습니다.

    [![호스트를 추가하기 위한 검토 및 만들기 섹션을 보여 주는 스크린샷. ](../media/azure-netapp-files/application-multiple-review-create.png) ](../media/azure-netapp-files/application-multiple-review-create.png#lightbox)

5. 볼륨 그룹을 탐색한 후 **모든 그룹 만들기를** 클릭하여 추가하는 HANA 호스트에 대한 모든 볼륨을 만듭니다. 

    [![모든 그룹 만들기 단추를 보여 주는 스크린샷. ](../media/azure-netapp-files/application-multiple-create-groups.png) ](../media/azure-netapp-files/application-multiple-create-groups.png#lightbox)

    **볼륨 그룹 만들기** 페이지에는 "만들기" 상태의 추가된 볼륨 그룹이 표시됩니다.

## <a name="next-steps"></a>다음 단계  

* [SAP HANA Azure NetApp Files 애플리케이션 볼륨 그룹 이해](application-volume-group-introduction.md)
* [SAP HANA 애플리케이션 볼륨 그룹에 대한 요구 사항 및 고려 사항](application-volume-group-considerations.md)
* [SAP HANA 애플리케이션 볼륨 그룹을 사용하여 첫 번째 SAP HANA 호스트 배포](application-volume-group-deploy-first-host.md)
* [HSR에서 SAP HANA 시스템의 볼륨을 보조 데이터베이스로 추가](application-volume-group-add-volume-secondary.md)
* [지역 간 복제를 사용하여 SAP HANA 시스템에 대한 볼륨을 DR 시스템으로 추가](application-volume-group-disaster-recovery.md)
* [애플리케이션 볼륨 그룹의 볼륨 관리](application-volume-group-manage-volumes.md)
* [애플리케이션 볼륨 그룹 삭제](application-volume-group-delete.md)
* [애플리케이션 볼륨 그룹 FAQ](faq-application-volume-group.md)
* [애플리케이션 볼륨 그룹 오류 문제 해결](troubleshoot-application-volume-groups.md)
