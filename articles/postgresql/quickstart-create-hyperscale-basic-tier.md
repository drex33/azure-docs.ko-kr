---
title: '빠른 시작: 기본 계층 서버 그룹 만들기 - 하이퍼스케일(Citus) - Azure Database for PostgreSQL'
description: Azure Database for PostgreSQL 하이퍼스케일(Citus) 기본 계층을 시작합니다.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: quickstart
ms.date: 11/16/2021
ms.openlocfilehash: e4620ae7f1cddb58504e212cfd05eef7d7362dee
ms.sourcegitcommit: 11ca7ba5a017429c22a6b0bc02acb70b83a2984a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2021
ms.locfileid: "132814253"
---
# <a name="create-a-hyperscale-citus-basic-tier-server-group-in-the-azure-portal"></a>Azure Portal에서 하이퍼스케일(Citus) 기본 계층 서버 그룹 만들기

Azure Database for PostgreSQL - 하이퍼스케일(Citus)은 클라우드에서 항상 사용 가능한 PostgreSQL 데이터베이스를 실행, 관리 및 크기 조정하는 데 사용하는 관리형 서비스입니다. [기본 계층](concepts-hyperscale-tiers.md)은 초기 개발 및 테스트를 위한 편리한 배포 옵션입니다.

이 빠른 시작에서는 Azure Portal을 사용하여 하이퍼스케일(Citus) 서버 그룹을 만드는 방법을 보여줍니다. 서버 그룹을 프로비저닝하고 연결하여 쿼리를 실행할 수 있는지 확인합니다.

[!INCLUDE [azure-postgresql-hyperscale-create-basic-tier](../../includes/azure-postgresql-hyperscale-create-basic-tier.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 하이퍼스케일(Citus) 서버 그룹을 프로비저닝하는 방법을 배웠습니다. 그 후 psql을 사용하여 이 서버 그룹에 연결하고, 스키마를 만들고, 데이터를 분산했습니다.

- 자습서에 따라 [확장성 있는 다중 테넌트 애플리케이션을 빌드](./tutorial-design-database-hyperscale-multi-tenant.md)합니다.
- 서버 그룹에 적합한 [처음 크기](howto-hyperscale-scale-initial.md)를 결정합니다.
