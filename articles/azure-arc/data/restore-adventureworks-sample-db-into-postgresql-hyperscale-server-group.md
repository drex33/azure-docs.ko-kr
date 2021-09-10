---
title: Azure Arc 지원 PostgreSQL 하이퍼스케일로 AdventureWorks 샘플 데이터베이스 가져오기
description: Azure Arc 지원 PostgreSQL 하이퍼스케일로 AdventureWorks 샘플 데이터베이스 복원
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 06/02/2021
ms.topic: how-to
ms.openlocfilehash: f802bb5d681e9e77d258d6071def53d4c5e4958f
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/14/2021
ms.locfileid: "113731110"
---
# <a name="import-the-adventureworks-sample-database-to-azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc 지원 PostgreSQL 하이퍼스케일로 AdventureWorks 샘플 데이터베이스 가져오기

[AdventureWorks](/sql/samples/adventureworks-install-configure)는 자습서 및 예에 사용되는 OLTP 데이터베이스가 포함된 샘플 데이터베이스입니다. [SQL Server 샘플 GitHub 리포지토리](https://github.com/microsoft/sql-server-samples/tree/master/samples/databases)의 일부로 Microsoft에서 제공하고 유지 관리합니다.

오픈 소스 프로젝트에서 Azure Arc 지원 PostgreSQL 하이퍼스케일과 호환되도록 AdventureWorks 데이터베이스를 변환했습니다.
- [원래 프로젝트](https://github.com/lorint/AdventureWorks-for-Postgres)
- [PostgreSQL과 호환되도록 CSV 파일을 사전 변환하는 프로젝트를 따르세요.](https://github.com/NorfolkDataSci/adventure-works-postgres)

이 문서에서는 AdventureWorks 샘플 데이터베이스를 PostgreSQL 하이퍼스케일 서버 그룹으로 가져오는 간단한 프로세스를 설명합니다.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="download-the-adventureworks-backup-file"></a>AdventureWorks 백업 파일 다운로드

AdventureWorks .sql 파일을 PostgreSQL 하이퍼스케일 서버 그룹 컨테이너에 다운로드합니다. 이 예에서는 `kubectl exec` 명령을 사용하여 PostgreSQL 하이퍼스케일 서버 그룹 컨테이너에서 원격으로 명령을 실행하여 파일을 컨테이너로 다운로드합니다. `curl`에 의해 액세스할 수 있는 모든 위치에서 이 파일을 다운로드할 수 있습니다. PostgreSQL 하이퍼스케일 서버 그룹 컨테이너에서 끌어오려는 다른 데이터베이스 백업 파일이 있는 경우 이와 동일한 방법을 사용합니다. PostgreSQL 하이퍼스케일 서버 그룹 컨테이너에 있으면 데이터베이스, 스키마를 쉽게 만들고 데이터를 채울 수 있습니다.

다음과 같은 명령을 실행하여 파일을 다운로드합니다. 실행하기 전에 Pod 이름 및 네임스페이스 이름 값을 바꿉니다.

> [!NOTE]
>  GitHub에서 파일을 다운로드하려면 컨테이너가 443을 통해 인터넷에 연결되어 있어야 합니다.

> [!NOTE]
>  Postgres 하이퍼스케일 서버 그룹의 코디네이터 노드의 Pod 이름을 사용합니다. 해당 이름은 <server group name>c-0(예: postgres01c-0, 여기서 c는 코디네이터 노드임을 나타냄)입니다.  Pod 이름이 확실하지 않은 경우 `kubectl get pod` 명령을 실행합니다.

```console
kubectl exec <PostgreSQL pod name> -n <namespace name> -c postgres  -- /bin/bash -c "cd /tmp && curl -k -O https://raw.githubusercontent.com/microsoft/azure_arc/main/azure_arc_data_jumpstart/aks/arm_template/postgres_hs/AdventureWorks.sql"

#Example:
#kubectl exec postgres02-0 -n arc -c postgres -- /bin/bash -c "cd /tmp && curl -k -O https://raw.githubusercontent.com/microsoft/azure_arc/main/azure_arc_data_jumpstart/aks/arm_template/postgres_hs/AdventureWorks.sql"
```

## <a name="step-2-import-the-adventureworks-database"></a>2단계: AdventureWorks 데이터베이스 가져오기

마찬가지로 데이터베이스를 만들고 로드하려면 kubectl exec 명령을 실행하여 PostgreSQL 하이퍼스케일 서버 그룹 컨테이너에 포함된 psql CLI 도구를 사용할 수 있습니다.

다음과 같은 명령을 실행하여 빈 데이터베이스를 만듭니다. 실행하기 전에 먼저 Pod 이름 및 네임스페이스 이름 값을 바꿉니다.

```console
kubectl exec <PostgreSQL pod name> -n <namespace name> -c postgres -- psql --username postgres -c 'CREATE DATABASE "adventureworks";'

#Example
#kubectl exec postgres02-0 -n arc -c postgres -- psql --username postgres -c 'CREATE DATABASE "adventureworks";'
```

다음과 같은 명령을 실행하여 데이터베이스를 가져옵니다. 실행하기 전에 먼저 Pod 이름 및 네임스페이스 이름 값을 바꿉니다.

```console
kubectl exec <PostgreSQL pod name> -n <namespace name> -c postgres -- psql --username postgres -d adventureworks -f /tmp/AdventureWorks.sql

#Example
#kubectl exec postgres02-0 -n arc -c postgres -- psql --username postgres -d adventureworks -f /tmp/AdventureWorks.sql
```


> **참고: 스케일 아웃하고 PostgreSQL 하이퍼스케일 서버 그룹의 작업자 노드에 데이터/테이블을 분할/분산시키기 전까지 Azure Arc 지원 PostgreSQL 하이퍼스케일에서 실행에 있어 성능상 이점이 별로 없습니다. [제안된 다음 단계](#suggested-next-steps)를 참조하세요.**

## <a name="suggested-next-steps"></a>제안된 다음 단계
- Azure Database for PostgreSQL 하이퍼스케일의 개념 및 방법 가이드를 읽고, 여러 PostgreSQL 하이퍼스케일 노드에 데이터를 분산하고 Azure Database for PostgreSQL 하이퍼스케일의 모든 기능을 활용하세요. :
    * [노드 및 테이블](../../postgresql/concepts-hyperscale-nodes.md)
    * [애플리케이션 유형 확인](../../postgresql/concepts-hyperscale-app-type.md)
    * [배포 열 선택](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [테이블 공동 배치](../../postgresql/concepts-hyperscale-colocation.md)
    * [테이블 분산 및 수정](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [다중 테넌트 데이터베이스 설계](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [실시간 분석 대시보드 설계](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

   > \* 위 문서에서는 **Azure Portal에 로그인** 과 **Azure Database for PostgreSQL 만들기 – 하이퍼스케일(Citus)** 섹션을 건너뛰었습니다. Azure Arc 배포의 나머지 단계를 구현합니다. 이 섹션은 Azure 클라우드에서 PaaS 서비스로 제공되는 Azure Database for PostgreSQL 하이퍼스케일(Citus)에 한정되지만, 문서의 다른 부분은 Azure Arc 지원 PostgreSQL 하이퍼스케일에 직접 적용할 수 있습니다.

- [Azure Database for PostgreSQL 하이퍼스케일 서버 그룹 스케일 아웃](scale-out-in-postgresql-hyperscale-server-group.md)
