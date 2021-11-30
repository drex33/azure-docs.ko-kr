---
title: Azure NetApp Files에 대 한 응용 프로그램 볼륨 그룹 오류 문제 해결 | Microsoft Docs
description: Azure NetApp Files의 응용 프로그램 볼륨 그룹에 대 한 오류 또는 경고 조건 및 해결 방법을 설명 합니다.
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
ms.topic: troubleshooting
ms.date: 11/19/2021
ms.author: b-juche
ms.openlocfilehash: b853f0cdc42670146e675065af2e5789246ace8d
ms.sourcegitcommit: dcf3424d7149fceaea0340eb0657baa2c27882a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133271967"
---
# <a name="troubleshoot-application-volume-group-errors"></a>응용 프로그램 볼륨 그룹 오류 문제 해결

이 문서에서는 응용 프로그램 볼륨 그룹을 사용 하는 경우 발생할 수 있는 오류 또는 경고를 설명 하 고 가능한 해결 방법을 제안 합니다.

## <a name="errors-creating-replication"></a>복제 생성 오류  

|     오류 메시지    |     해결 방법    |
|-|-|
| 계산에 근접 한 볼륨 그룹을 만들 때 저장소 용량이 부족 합니다. <br> 오류 메시지: `No storage available with network locality that matches the provided Proximity Placement Group.`  |  이 오류는 사용자가 제공한 근접 배치 그룹 (PPG)에서 사용할 수 있는 리소스가 부족 함을 나타냅니다. <br> **[SAP HANA VM 고정 요청 양식](https://aka.ms/HANAPINNING)** 을 사용 하 여 SAP HANA 고정 프로세스를 사용 하 여 충분 한 리소스를 사용할 수 있도록 해야 합니다. |
|  배포 실패, 오류 메시지: <br> `Template parameter Token type is not valid. Expected 'Integer'. Actual 'Float'.`  |   응용 프로그램 볼륨 그룹에서 크기를 계산할 때 **볼륨** 탭의 모든 볼륨에 대 한 값이 표시 됩니다. 매우 작은 HANA 시스템의 경우 일부 볼륨이 부동 소수점 값으로 표시 됩니다. 최종 크기가 정수 GB 숫자가 아닌 경우 배포에 실패 합니다. <br> 이러한 볼륨에 대 한 볼륨 정보를 살펴보면이 배포 오류를 방지 하기 위해 부동 소수점 값이 정수로 자동 반올림 됩니다. |

## <a name="next-steps"></a>다음 단계  

* [SAP HANA에 대 한 Azure NetApp Files 응용 프로그램 볼륨 그룹 이해](application-volume-group-introduction.md)
* [SAP HANA의 응용 프로그램 볼륨 그룹에 대 한 요구 사항 및 고려 사항](application-volume-group-considerations.md)
* [SAP HANA의 응용 프로그램 볼륨 그룹을 사용 하 여 첫 번째 SAP HANA 호스트 배포](application-volume-group-deploy-first-host.md)
* [SAP HANA의 응용 프로그램 볼륨 그룹을 사용 하 여 다중 호스트 SAP HANA 시스템에 호스트 추가](application-volume-group-add-hosts.md)
* [HSR에서 보조 데이터베이스로 SAP HANA 시스템의 볼륨 추가](application-volume-group-add-volume-secondary.md)
* [지역 간 복제를 사용 하 여 SAP HANA 시스템용 볼륨을 DR 시스템으로 추가](application-volume-group-disaster-recovery.md)
* [응용 프로그램 볼륨 그룹의 볼륨 관리](application-volume-group-manage-volumes.md)
* [응용 프로그램 볼륨 그룹 삭제](application-volume-group-delete.md)
* [응용 프로그램 볼륨 그룹 Faq](faq-application-volume-group.md)
