---
title: Azure NetApp Files | 애플리케이션 볼륨 그룹 삭제 Microsoft Docs
description: 애플리케이션 볼륨 그룹을 삭제하는 방법을 설명합니다.
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
ms.openlocfilehash: 6d02ce57d234f9d36c91e61c37584fc6e1eeb46b
ms.sourcegitcommit: dcf3424d7149fceaea0340eb0657baa2c27882a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133271946"
---
# <a name="delete-an-application-volume-group"></a>애플리케이션 볼륨 그룹 삭제

이 문서에서는 애플리케이션 볼륨 그룹을 삭제하는 방법을 설명합니다.

> [!IMPORTANT]
> 볼륨 그룹에 볼륨이 없는 경우에만 볼륨 그룹을 삭제할 수 있습니다. 볼륨 그룹을 삭제하기 전에 그룹의 모든 볼륨을 삭제합니다. 그렇지 않으면 볼륨 그룹을 삭제할 수 없으면 오류가 발생합니다.  

## <a name="steps"></a>단계

1. **애플리케이션 볼륨 그룹** 을 클릭합니다. 삭제하려는 볼륨 그룹을 선택합니다.

    [![애플리케이션 볼륨 그룹 목록을 보여 주는 스크린샷. ](../media/azure-netapp-files/application-volume-group-list.png) ](../media/azure-netapp-files/application-volume-group-list.png#lightbox)

2. 볼륨 그룹을 삭제하려면 **삭제를** 클릭합니다. 메시지가 표시되면 볼륨 그룹 이름을 입력하여 삭제를 확인합니다.  

    [![애플리케이션 볼륨 그룹 삭제를 보여 주는 스크린샷](../media/azure-netapp-files/application-volume-group-delete.png)](../media/azure-netapp-files/application-volume-group-delete.png#lightbox) 

## <a name="next-steps"></a>다음 단계  

* [SAP HANA Azure NetApp Files 애플리케이션 볼륨 그룹 이해](application-volume-group-introduction.md)
* [SAP HANA 애플리케이션 볼륨 그룹에 대한 요구 사항 및 고려 사항](application-volume-group-considerations.md)
* [SAP HANA 애플리케이션 볼륨 그룹을 사용하여 첫 번째 SAP HANA 호스트 배포](application-volume-group-deploy-first-host.md)
* [SAP HANA 애플리케이션 볼륨 그룹을 사용하여 다중 호스트 SAP HANA 시스템에 호스트 추가](application-volume-group-add-hosts.md)
* [HSR에서 SAP HANA 시스템의 볼륨을 보조 데이터베이스로 추가](application-volume-group-add-volume-secondary.md)
* [지역 간 복제를 사용하여 dr 시스템으로 SAP HANA 시스템에 대한 볼륨 추가](application-volume-group-disaster-recovery.md)
* [애플리케이션 볼륨 그룹의 볼륨 관리](application-volume-group-manage-volumes.md)
* [애플리케이션 볼륨 그룹 FAQ](faq-application-volume-group.md)
* [애플리케이션 볼륨 그룹 오류 문제 해결](troubleshoot-application-volume-groups.md)
