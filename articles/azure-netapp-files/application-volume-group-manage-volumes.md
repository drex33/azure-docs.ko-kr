---
title: Azure NetApp Files 애플리케이션 볼륨 그룹의 볼륨 관리 | Microsoft Docs
description: 볼륨의 크기 조정, 삭제 또는 처리량 변경을 포함하여 애플리케이션 볼륨 그룹에서 볼륨을 관리하는 방법을 설명합니다.
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
ms.openlocfilehash: 2d5d474a88278d6252f065282e4ae4c2938f76eb
ms.sourcegitcommit: 9ef0965834870700468c822ddcafc011881fc2d5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133485918"
---
# <a name="manage-volumes-in-an-application-volume-group"></a>애플리케이션 볼륨 그룹의 볼륨 관리

볼륨 그룹에서 볼륨을 관리할 수 있습니다. 볼륨의 처리량을 조정, 삭제 또는 변경할 수 있습니다. 

## <a name="steps"></a>단계

1. NetApp 계정에서 **애플리케이션 볼륨 그룹 을** 선택합니다. 볼륨 그룹을 클릭하여 그룹의 볼륨을 표시합니다. 처리량을 조정, 삭제 또는 변경하려는 볼륨을 선택합니다. 볼륨 개요가 표시됩니다. 

    [![애플리케이션 볼륨 그룹 개요 페이지를 보여 주는 스크린샷.](../media/azure-netapp-files/application-volume-group-overview.png)](../media/azure-netapp-files/application-volume-group-overview.png#lightbox)  

    1. 볼륨 크기를 조정하려면 크기 **조정을** 클릭하고 GiB에서 할당량을 지정합니다.
    
    ![볼륨 할당량 업데이트 창을 보여 주는 스크린샷입니다.](../media/azure-netapp-files/application-volume-resize.png) 

    2. 볼륨의 처리량을 변경하려면 **처리량 변경을** 클릭하고 MiB/s에서 의도한 처리량을 지정합니다.

    ![처리량 변경 창을 보여 주는 스크린샷.](../media/azure-netapp-files/application-volume-change-throughput.png) 

    3. 볼륨 그룹의 볼륨을 삭제하려면 **삭제를** 클릭합니다. 메시지가 표시되면 볼륨 이름을 입력하여 삭제를 확인합니다.  

    > [!IMPORTANT]
    > 볼륨 삭제 작업을 실행 취소할 수 없습니다.
    
    ![볼륨 삭제 창을 보여 주는 스크린샷.](../media/azure-netapp-files/application-volume-delete.png) 

## <a name="next-steps"></a>다음 단계  

* [SAP HANA Azure NetApp Files 애플리케이션 볼륨 그룹 이해](application-volume-group-introduction.md)
* [SAP HANA 애플리케이션 볼륨 그룹에 대한 요구 사항 및 고려 사항](application-volume-group-considerations.md)
* [SAP HANA 애플리케이션 볼륨 그룹을 사용하여 첫 번째 SAP HANA 호스트 배포](application-volume-group-deploy-first-host.md)
* [용 애플리케이션 볼륨 그룹을 사용하여 다중 호스트 SAP HANA 시스템에 호스트 추가 SAP HANA](application-volume-group-add-hosts.md)
* [HSR에서 SAP HANA 시스템의 볼륨을 보조 데이터베이스로 추가](application-volume-group-add-volume-secondary.md)
* [지역 간 복제를 사용하여 SAP HANA 시스템에 대한 볼륨을 DR 시스템으로 추가](application-volume-group-disaster-recovery.md)
* [애플리케이션 볼륨 그룹 삭제](application-volume-group-delete.md)
* [애플리케이션 볼륨 그룹 FAQ](faq-application-volume-group.md)
* [애플리케이션 볼륨 그룹 오류 문제 해결](troubleshoot-application-volume-groups.md)
