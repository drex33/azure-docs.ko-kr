---
title: 포함 파일
description: 포함 파일
author: jonels-msft
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: include
ms.date: 10/15/2021
ms.author: jonels
ms.custom: include file
ms.openlocfilehash: 4e0840dc3aa24f744eaac74b44b2d7972ff40900
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130072192"
---
Azure Database for PostgreSQL – Hyperscale (Citus)은 다음과 같은 세 가지 네트워킹 옵션을 지원 합니다.

* 액세스 권한 없음
    * 공용 또는 개인 액세스를 사용 하지 않는 경우 새로 만든 서버 그룹에 대 한 기본값입니다. Azure의 내부 또는 외부에 있는 컴퓨터 없음--데이터베이스 노드에 연결할 수 있습니다.
* 퍼블릭 액세스
    * 공용 IP 주소가 코디네이터 노드에 할당 됩니다.
    * 코디네이터 노드에 대 한 액세스는 방화벽으로 보호 됩니다.
    * 필요에 따라 모든 작업자 노드에 대 한 액세스를 설정할 수 있습니다. 이 경우 공용 IP 주소는 작업자 노드에 할당 되며 동일한 방화벽으로 보호 됩니다.
* 개인 액세스 (미리 보기)
    * 개인 IP 주소만 서버 그룹의 노드에 할당 됩니다.
    * 각 노드에는 선택한 가상 네트워크의 호스트가 Hyperscale (Citus) 노드에 액세스할 수 있도록 하는 개인 끝점이 필요 합니다.
    * 네트워크 보안 그룹과 같은 Azure 가상 네트워크의 보안 기능을 사용 하 여 액세스를 제어할 수 있습니다.

Citus (Hyperscale) 서버 그룹을 만들 때 공용 또는 개인 액세스를 사용 하도록 설정 하거나 기본값 없음을 선택할 수 있습니다. 서버 그룹을 만든 후에는 공용 또는 개인 액세스를 전환 하거나 한 번에 둘 다 활성화 하도록 선택할 수 있습니다.
