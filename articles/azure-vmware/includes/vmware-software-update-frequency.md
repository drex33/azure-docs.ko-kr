---
title: VMware 소프트웨어 업데이트 빈도
description: Azure VMware Solution에 대해 지원되는 VMware 소프트웨어 업데이트 빈도.
ms.topic: include
ms.date: 04/23/2021
author: shortpatti
ms.author: v-patsho
ms.service: azure-vmware
ms.openlocfilehash: 3295ab9da93b1e1ec1cace62653670de366c880d
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122324319"
---
<!-- Used in faq.md and concepts-private-clouds-clusters.md -->

호스트 유지 관리 및 수명 주기 관리는 프라이빗 클라우드 클러스터의 용량 또는 성능에 영향을 주지 않습니다. 프라이빗 클라우드 소프트웨어로의 업그레이드는 VMware의 소프트웨어 번들 릴리스를 따르는 일정에 따라 진행됩니다.  따라서 프라이빗 클라우드는 업그레이드로 인한 가동 중지 시간이 없습니다.

프라이빗 클라우드 소프트웨어 번들 업그레이드는 VMware의 최신 소프트웨어 번들 릴리스의 한 버전 내에서 소프트웨어를 유지합니다. 프라이빗 클라우드 소프트웨어 버전은 개별 소프트웨어 구성 요소(ESXi, NSX-T, vCenter, vSAN)의 최신 버전과 다를 수 있습니다. 업데이트에는 드라이버, 네트워크 스위치의 소프트웨어 및 운영 체제 미설치 노드의 펌웨어도 포함됩니다.

패치를 프라이빗 클라우드에 적용하기 전과 후에 알림이 표시됩니다. 또한 프라이빗 클라우드에 업데이트 또는 업그레이드를 적용하기 전에 유지 관리 기간을 예약하는 작업을 수행합니다. 

소프트웨어 업데이트 포함 사항:

- **패치** - VMware에서 출사한 보안 패치 또는 버그 수정 사항
- **업데이트** -VMware 스택 구성 요소의 부 버전 변경
- **업그레이드** -VMware 스택 구성 요소의 주 버전 변경

>[!NOTE]
>Microsoft는 VMware로부터 제공되는 대로 중요 보안 패치를 테스트합니다.

문서화된 VMware 해결 방법은 다음에 예약된 업데이트가 배포될 때까지 해당 패치를 설치하는 대신 구현됩니다.
