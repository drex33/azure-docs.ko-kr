---
title: Azure Arc 지원 PostgreSQL 하이퍼스케일에서 지원되는 Postgres 버전
description: Azure Arc 지원 PostgreSQL 하이퍼스케일에서 지원되는 Postgres 버전
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: 2afdf621b2d5a7ba0b5b08669a892ed11f90826d
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131562811"
---
# <a name="supported-versions-of-postgres-with-azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc 지원 PostgreSQL 하이퍼스케일에서 지원되는 Postgres 버전
지원 되는 버전 목록은 Azure PaaS에서 Postgres 관리 서비스로 패리티를 보장 하기 위해 시간이 지남에 따라 진화 합니다. 현재 지원되는 주 버전은 다음과 같습니다.
- Postgres 12(기본값)
- Postgres 11


[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="how-to-chose-between-versions"></a>버전 간 선택 방법
애플리케이션이 설계된 대상 버전과 각 버전의 기능을 살펴보는 것이 좋습니다. 자세한 내용은 공식 Postgres 사이트에서 각 버전에 대한 정보를 참조하세요.
- [Postgres 12(기본값)](https://www.postgresql.org/docs/12/index.html)
- [Postgres 11](https://www.postgresql.org/docs/11/index.html)

## <a name="how-to-create-a-particular-version-in-azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc 지원 PostgreSQL 하이퍼스케일에서 특정 버전을 만드는 방법
만들 때 _--engine-version_ 매개 변수를 전달하여 만들려는 버전을 지정할 수 있습니다. 버전 정보를 지정하지 않으면 기본적으로 Postgres 버전 12의 서버 그룹이 생성됩니다.

지원 되는 버전에 관계 없이 Kubernetes 클러스터에는 Postgres 사용자 지정 리소스 정의 (CRD)가 하나만 있습니다.
예를 들어 다음 명령을 실행합니다.
```console
kubectl get crds
```

다음과 같은 출력을 반환합니다.
```console
NAME                                                             CREATED AT
dags.sql.arcdata.microsoft.com                                   2021-10-12T23:53:40Z
datacontrollers.arcdata.microsoft.com                            2021-10-13T01:00:27Z
exporttasks.tasks.arcdata.microsoft.com                          2021-10-12T23:53:39Z
healthstates.azmon.container.insights                            2021-10-12T19:04:44Z
monitors.arcdata.microsoft.com                                   2021-10-13T01:00:26Z
postgresqls.arcdata.microsoft.com                                2021-10-12T23:53:37Z
sqlmanagedinstancerestoretasks.tasks.sql.arcdata.microsoft.com   2021-10-12T23:53:38Z
sqlmanagedinstances.sql.arcdata.microsoft.com                    2021-10-12T23:53:37Z
```

이 예제에서이 출력은 Postgres: postgresqls.arcdata.microsoft.com, 짧은 이름 postgresqls와 관련 된 하나의 CRD가 있음을 나타냅니다. CRD는 Postgres 인스턴스 또는 Postgres server 그룹이 아닙니다. CRD가 있는지는 서버 그룹을가지고 있거나 만들지 않았음을 나타냅니다. CRD는 Kubernetes 클러스터에서 만들 수 있는 리소스 종류를 나타냅니다.

## <a name="how-can-i-be-notified-when-other-versions-are-available"></a>다른 버전을 사용할 수 있을 때 알림을 받으려면 어떻게 해야 하나요?
돌아와서 이 문서를 확인합니다. 문서가 적절하게 업데이트됩니다.


## <a name="next-steps"></a>다음 단계:
- [Azure Arc 지원 PostgreSQL 하이퍼스케일을 만드는 방법 확인](create-postgresql-hyperscale-server-group.md)
- [Arc 데이터 컨트롤러에서 만든 Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹 목록을 가져오는 방법 확인](list-server-groups-postgres-hyperscale.md)
