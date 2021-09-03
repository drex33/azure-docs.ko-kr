---
title: Azure Arc 지원 PostgreSQL 하이퍼스케일에서 지원되는 Postgres 버전
description: Azure Arc 지원 PostgreSQL 하이퍼스케일에서 지원되는 Postgres 버전
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 0a00475eacdeb741eca20d4a6c43282df9cfd17d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536403"
---
# <a name="supported-versions-of-postgres-with-azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc 지원 PostgreSQL 하이퍼스케일에서 지원되는 Postgres 버전

이 문서에서는 Azure Arc 지원 PostgreSQL 하이퍼스케일에서 사용할 수 있는 Postgres 버전을 설명합니다.
지원되는 버전 목록은 시간 경과에 따라 발전합니다. 현재 지원되는 주 버전은 다음과 같습니다.
- Postgres 12(기본값)
- Postgres 11

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="how-to-chose-between-versions"></a>버전 간 선택 방법
애플리케이션이 설계된 대상 버전과 각 버전의 기능을 살펴보는 것이 좋습니다. 자세한 내용은 공식 Postgres 사이트에서 각 버전에 대한 정보를 참조하세요.
- [Postgres 12(기본값)](https://www.postgresql.org/docs/12/index.html)
- [Postgres 11](https://www.postgresql.org/docs/11/index.html)

## <a name="how-to-create-a-particular-version-in-azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc 지원 PostgreSQL 하이퍼스케일에서 특정 버전을 만드는 방법
만들 때 _--engine-version_ 매개 변수를 전달하여 만들려는 버전을 지정할 수 있습니다. 버전 정보를 지정하지 않으면 기본적으로 Postgres 버전 12의 서버 그룹이 생성됩니다.

## <a name="how-can-i-be-notified-when-other-versions-are-available"></a>다른 버전을 사용할 수 있을 때 알림을 받으려면 어떻게 해야 하나요?
돌아와서 이 문서를 확인합니다. 문서가 적절하게 업데이트됩니다. Kubernetes 클러스터의 Arc 데이터 컨트롤러에 있는 CRD(사용자 지정 리소스 정의) 종류를 나열할 수도 있습니다.
다음 명령 실행:
```console
kubectl get crds
```

다음과 같은 출력이 반환됩니다.
```console
NAME                                            CREATED AT
datacontrollers.arcdata.microsoft.com           2020-08-31T20:15:16Z
postgresql-11s.arcdata.microsoft.com            2020-08-31T20:15:16Z
postgresql-12s.arcdata.microsoft.com            2020-08-31T20:15:16Z
sqlmanagedinstances.sql.arcdata.microsoft.com   2020-08-31T20:15:16Z
```

예제에서 이 출력은 Postgres와 관련된 두 가지 종류의 CRD가 있음을 나타냅니다.
- postgresql-12s.arcdata.microsoft.com은 Postgres 12용 CRD입니다.
- postgresql-11s.arcdata.microsoft.com은 Postgres 11용 CRD입니다.

서버 그룹이 아니라 CRD입니다. CRD의 현재 상태가 해당 버전의 서버 그룹을 만들었는지 여부를 나타내는 것은 아닙니다.
CRD는 만들 수 있는 리소스 종류를 나타냅니다.

## <a name="next-steps"></a>다음 단계:
- [Azure Arc 지원 PostgreSQL 하이퍼스케일을 만드는 방법 확인](create-postgresql-hyperscale-server-group.md)
- [Arc 데이터 컨트롤러에서 만든 Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹 목록을 가져오는 방법 확인](list-server-groups-postgres-hyperscale.md)
