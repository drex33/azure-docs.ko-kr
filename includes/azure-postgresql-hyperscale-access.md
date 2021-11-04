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
ms.openlocfilehash: ac789ee1971eb5e310b2f7d3ae35412d9386b560
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131570978"
---
Azure Database for PostgreSQL – 하이퍼스케일(Citus) 세 가지 네트워킹 옵션을 지원합니다.

* 액세스 권한 없음
  * 퍼블릭 또는 프라이빗 액세스를 사용하지 않는 경우 새로 만든 서버 그룹의 기본값입니다. Azure 내부 또는 외부에 관계없이 데이터베이스 노드에 연결할 수 있는 컴퓨터는 없습니다.
* 퍼블릭 액세스
  * 공용 IP 주소는 코디네이터 노드에 할당됩니다.
  * 코디네이터 노드에 대한 액세스는 방화벽으로 보호됩니다.
  * 필요에 따라 모든 작업자 노드에 대한 액세스를 사용하도록 설정할 수 있습니다. 이 경우 공용 IP 주소가 작업자 노드에 할당되고 동일한 방화벽으로 보호됩니다.
* 프라이빗 액세스(미리 보기)
  * 개인 IP 주소만 서버 그룹의 노드에 할당됩니다.
  * 각 노드에는 선택한 가상 네트워크의 호스트가 하이퍼스케일(Citus) 노드에 액세스할 수 있도록 하는 프라이빗 엔드포인트가 필요합니다.
  * 네트워크 보안 그룹과 같은 Azure 가상 네트워크의 보안 기능을 액세스 제어에 사용할 수 있습니다.

하이퍼스케일(Citus) 서버 그룹을 만들 때 공용 또는 프라이빗 액세스를 사용하도록 설정하거나 액세스 권한 없음의 기본값을 선택할 수 있습니다. 서버 그룹이 만들어지면 퍼블릭 또는 프라이빗 액세스 간에 전환하거나 한 번에 둘 다 활성화하도록 선택할 수 있습니다.
