---
title: Azure NetApp Files 응용 프로그램 볼륨 그룹의 볼륨 관리 | Microsoft Docs
description: 볼륨의 크기 조정, 삭제 또는 변경 등 응용 프로그램 볼륨 그룹에서 볼륨을 관리 하는 방법을 설명 합니다.
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
ms.openlocfilehash: b0f00bf3c3081f533cc71a862f05d7ae97eff546
ms.sourcegitcommit: dcf3424d7149fceaea0340eb0657baa2c27882a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133271935"
---
# <a name="manage-volumes-in-an-application-volume-group"></a>응용 프로그램 볼륨 그룹의 볼륨 관리

볼륨 그룹에서 볼륨을 관리할 수 있습니다. 볼륨의 크기를 조정, 삭제 또는 변경할 수 있습니다. 

## <a name="steps"></a>단계

1. NetApp 계정에서 **응용 프로그램 볼륨 그룹** 을 선택 합니다. 볼륨 그룹을 클릭 하 여 그룹에 볼륨을 표시 합니다. 크기를 조정 하거나, 삭제 하거나, 처리량을 변경 하려는 볼륨을 선택 합니다. 볼륨 개요가 표시 됩니다. 

    [![응용 프로그램 볼륨 그룹 개요 페이지를 보여 주는 스크린샷](../media/azure-netapp-files/application-volume-group-overview.png)](../media/azure-netapp-files/application-volume-group-overview.png#lightbox)  

    1. 볼륨 크기를 조정 하려면 **크기 조정** 을 클릭 하 고 GiB의 할당량을 지정 합니다.
    
    ![볼륨 할당량 업데이트 창을 보여 주는 스크린샷입니다.](../media/azure-netapp-files/application-volume-resize.png) 

    2. 볼륨에 대 한 처리량을 변경 하려면 **처리량 변경** 을 클릭 하 고 MiB/s에서 원하는 처리량을 지정 합니다.

    ![처리량 변경 창을 보여 주는 스크린샷](../media/azure-netapp-files/application-volume-change-throughput.png) 

    3. 볼륨 그룹에서 볼륨을 삭제 하려면 **삭제** 를 클릭 합니다. 메시지가 표시 되 면 볼륨 이름을 입력 하 여 삭제를 확인 합니다.  

    > [!IMPORTANT]
    > 볼륨 삭제 작업은 실행 취소할 수 없습니다.
    
    ![볼륨 삭제 창을 보여 주는 스크린샷](../media/azure-netapp-files/application-volume-delete.png) 

## <a name="next-steps"></a>다음 단계  

* [SAP HANA에 대 한 Azure NetApp Files 응용 프로그램 볼륨 그룹 이해](application-volume-group-introduction.md)
* [SAP HANA의 응용 프로그램 볼륨 그룹에 대 한 요구 사항 및 고려 사항](application-volume-group-considerations.md)
* [SAP HANA의 응용 프로그램 볼륨 그룹을 사용 하 여 첫 번째 SAP HANA 호스트 배포](application-volume-group-deploy-first-host.md)
* [SAP HANA의 응용 프로그램 볼륨 그룹을 사용 하 여 다중 호스트 SAP HANA 시스템에 호스트 추가](application-volume-group-add-hosts.md)
* [HSR에서 보조 데이터베이스로 SAP HANA 시스템의 볼륨 추가](application-volume-group-add-volume-secondary.md)
* [지역 간 복제를 사용 하 여 SAP HANA 시스템용 볼륨을 DR 시스템으로 추가](application-volume-group-disaster-recovery.md)
* [응용 프로그램 볼륨 그룹 삭제](application-volume-group-delete.md)
* [응용 프로그램 볼륨 그룹 Faq](faq-application-volume-group.md)
* [응용 프로그램 볼륨 그룹 오류 문제 해결](troubleshoot-application-volume-groups.md)
