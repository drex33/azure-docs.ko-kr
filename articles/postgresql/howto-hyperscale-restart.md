---
title: 서버 다시 시작 - 하이퍼스케일(Citus) - Azure Database for PostgreSQL
description: Azure Database for PostgreSQL에서 데이터베이스를 다시 시작하는 방법 - 하이퍼스케일(Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 7/9/2021
ms.openlocfilehash: 0de8108cde0b5017221bf3ab038d98c98bb94546
ms.sourcegitcommit: 2cff2a795ff39f7f0f427b5412869c65ca3d8515
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/10/2021
ms.locfileid: "113598718"
---
# <a name="restart-azure-database-for-postgresql---hyperscale-citus"></a>Azure Database for PostgreSQL 다시 시작 - 하이퍼스케일(Citus)

하이퍼스케일(Citus) 서버 그룹을 다시 시작하려면 Azure Portal의 그룹 **개요** 페이지에서 다시 시작할 수 있습니다. 상단 표시줄에서 **다시 시작** 단추를 선택합니다. 확인 대화 상자가 표시됩니다. **모두 다시 시작** 을 선택하여 계속 진행합니다.

> [!NOTE]
> 서버 그룹에 아직 다시 시작 단추가 없는 경우 Azure 지원 요청을 열어 서버 그룹을 다시 시작하세요.

서버 그룹을 다시 시작하면 모든 노드에 적용됩니다. 개별 노드를 선택적으로 다시 시작할 수 없습니다. 다시 시작은 PostgreSQL 서버 인스턴스뿐만 아니라 노드의 전체 가상 머신에 적용됩니다. 데이터베이스를 사용하려는 모든 애플리케이션은 다시 시작하는 동안 연결 가동 중지 시간이 발생합니다.

**다음 단계**

- 일부 서버 매개 변수를 변경하려면 다시 시작해야 합니다. 하이퍼스케일(Citus)에서 구성할 수 있는 [모든 서버 매개 변수](reference-hyperscale-parameters.md) 목록을 참조하세요.
