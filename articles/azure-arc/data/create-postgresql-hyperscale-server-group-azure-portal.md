---
title: Azure Portal에서 Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹 만들기
description: Azure Portal에서 Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹 만들기
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 382e4e855b0a4925cfaae2f7746587a7ab2fd951
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122567341"
---
# <a name="create-an-azure-arc-enabled-postgresql-hyperscale-server-group-from-the-azure-portal"></a>Azure Portal에서 Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹 만들기

이 문서에서는 Azure Portal의 Azure Arc에서 PostgreSQL 하이퍼스케일 서버 그룹을 만드는 단계를 설명합니다.

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="getting-started"></a>시작
아래 토픽에 이미 익숙하다면 이 단락을 건너뛸 수 있습니다.
만들기를 진행하기 전에 읽어야 할 중요한 토픽이 있습니다.
- [Azure Arc 지원 데이터 서비스 개요](overview.md)
- [연결 모드 및 요구 사항](connectivity.md)
- [스토리지 구성 및 Kubernetes 스토리지 개념](storage-configuration.md)
- [Kubernetes 리소스 모델](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)

전체 환경을 직접 프로비전하지 않고 작업을 시도하려면 AKS(Azure Kubernetes Service), AWS Elastic Kubernetes Service(EKS), Google Cloud Kubernetes Engine(GKE) 또는 Azure VM에서 [Azure Arc 빠른 시작](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/)을 사용하여 빠르게 시작하세요.


## <a name="deploy-an-arc-data-controller-configured-to-use-the-direct-connectivity-mode"></a>직접 연결 모드를 사용하도록 구성된 Arc 데이터 컨트롤러 배포

요구 사항: Azure Portal에서 작동하는 Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹을 배포하기 전에 먼저 *직접* 연결 모드를 사용하도록 구성된 Azure Arc 데이터 컨트롤러를 배포해야 합니다.
Arc 데이터 컨트롤러를 배포하려면 다음 문서의 지침을 완료합니다.
1. [데이터 컨트롤러 배포 - 직접 연결 모드(필수 구성 요소)](create-data-controller-direct-prerequisites.md)
1. [Azure Portal에서 직접 연결 모드로 Azure Arc 데이터 컨트롤러 배포](create-data-controller-direct-azure-portal.md)


## <a name="preliminary-and-temporary-step-for-openshift-users-only"></a>OpenShift 사용자만을 위한 임시 예비 단계
다음 단계로 이동하기 전에 이 단계를 구현합니다. PostgreSQL 하이퍼스케일 서버 그룹을 기본 프로젝트가 아닌 프로젝트의 Red Hat OpenShift에 배포하려면 클러스터에 대해 다음 명령을 실행하여 보안 제약 조건을 업데이트해야 합니다. 이 명령은 PostgreSQL 하이퍼스케일 서버 그룹을 실행하는 서비스 계정에 필요한 권한을 부여합니다. SCC(보안 컨텍스트 제약 조건) arc-data-scc는 Azure Arc 데이터 컨트롤러를 배포할 때 추가한 제약 조건입니다.

```Console
oc adm policy add-scc-to-user arc-data-scc -z <server-group-name> -n <namespace name>
```

**Server-group-name은 다음 단계에서 만든 서버 그룹의 이름입니다.**

OpenShift의 SCC에 대한 자세한 내용은 [OpenShift 설명서](https://docs.openshift.com/container-platform/4.2/authentication/managing-security-context-constraints.html)를 참조하세요. 

다음 단계를 진행합니다.

## <a name="deploy-an-azure-arc-enabled-postgresql-hyperscale-server-group-from-the-azure-portal"></a>Azure Portal에서 Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹 배포

Azure Portal에서 Azure Arc 지원 Postgres 하이퍼스케일 서버 그룹을 배포하고 작동하려면 *직접* 연결 모드를 사용하도록 구성된 Arc 데이터 컨트롤러에 배포해야 합니다. 

> [!IMPORTANT]
> *간접* 연결 모드를 사용하도록 구성된 Azure Arc 데이터 컨트롤러에 배포한 경우 Azure Portal에서 Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹을 작동할 수 없습니다. 

직접 연결 모드에 활성화된 Arc 데이터 컨트롤러를 배포한 후 다음 세 가지 옵션 중 하나를 선택하여 Azure Arc 지원 Postgres 하이퍼스케일 서버 그룹을 배포할 수 있습니다.

### <a name="option-1-deploy-from-the-azure-marketplace"></a>옵션 1: Azure Marketplace에서 배포
1. 브라우저를 다음 URL [https://portal.azure.com](https://portal.azure.com)으로 엽니다.
2. 페이지 맨 위에 있는 검색 창의 Azure Market Place에서 "*azure arc postgres*"를 검색하고 **Azure Database for PostgreSQL 서버 그룹 - Azure Arc** 를 선택합니다.
3. 열리는 페이지에서 왼쪽 위 모서리에 있는 **+ 만들기** 를 클릭합니다. 
4. 다른 Azure 리소스를 배포하는 것처럼 양식을 입력합니다.

### <a name="option-2-deploy-from-the-azure-database-for-postgresql-deployment-option-page"></a>옵션 2: Azure Database for PostgreSQL 배포 옵션 페이지에서 배포
1. 브라우저를 다음 URL https://ms.portal.azure.com/#create/Microsoft.PostgreSQLServer 로 엽니다.
2. 오른쪽 아래에 있는 타일을 클릭합니다. 제목은 Azure Arc 지원 PostgreSQL 하이퍼스케일(미리 보기)입니다.
3. 다른 Azure 리소스를 배포하는 것처럼 양식을 입력합니다.

### <a name="option-3-deploy-from-the-azure-arc-center"></a>옵션 3: Azure Arc 센터에서 배포
1. 브라우저를 다음 URL https://ms.portal.azure.com/#blade/Microsoft_Azure_HybridCompute/AzureArcCenterBlade/overview 로 엽니다.
1. 페이지 가운데의 *Azure 서비스 배포* 라는 타일 아래에서 [배포]를 클릭한 다음, PostgreSQL 하이퍼스케일(미리 보기)이라는 타일에서 [배포]를 클릭합니다.
2. 또는 페이지 왼쪽의 탐색 창의 서비스 섹션에서 [PostgreSQL 하이퍼스케일(미리 보기)]을 클릭한 다음, 창의 왼쪽 위에서 [+ 만들기]를 클릭합니다.


#### <a name="important-parameters-you-should-consider"></a>고려해야 할 중요한 매개 변수는 다음과 같습니다.

- 확장하고 잠재적으로 더 나은 성능에 도달하기 위해 배포하려는 **작업자 노드의 수**. 계속하기 전에 [Postgres 하이퍼스케일에 대한 개념](concepts-distributed-postgres-hyperscale.md)을 읽어보세요. 아래 표는 지원되는 값의 범위와 이러한 값과 함께 얻을 수 있는 Postgres 배포의 형식을 나타냅니다. 예를 들어 2개의 작업자 노드가 있는 서버 그룹을 배포하려는 경우 2를 표시합니다. 이렇게 하면 코디네이터 노드/인스턴스에 대해 하나씩, 작업자 노드/인스턴스에 대해 두 개(각 작업자에 대해 하나씩) 세 개의 Pod가 만들어집니다.



|다음 항목이 필요합니다.   |배포할 서버 그룹의 모양   |나타낼 작업자 노드 수   |참고   |
|---|---|---|---|
|애플리케이션의 확장성 요구를 충족하기 위해 확장된 형태의 Postgres입니다.   |3개 이상의 Postgres 인스턴스, 1개는 코디네이터, n은 n >=2의 작업자입니다.   |n>=2인 n.   |하이퍼스케일 기능을 제공하는 Citus 확장이 로드됩니다.   |
|최소한의 비용으로 애플리케이션의 기능 유효성 검사를 수행할 수 있는 기본 형태의 Postgres 하이퍼스케일입니다. 성능 및 확장성 유효성 검사에는 유효하지 않습니다. 이를 위해 위에서 설명한 배포 유형을 사용해야 합니다.   |코디네이터 및 작업자인 1 Postgres 인스턴스.   |0 및 로드할 확장 목록에 Citus를 추가합니다.   |하이퍼스케일 기능을 제공하는 Citus 확장이 로드됩니다.   |
|필요할 때 확장할 준비가 된 Postgres의 간단한 인스턴스입니다.   |1 Postgres 인스턴스. 코디네이터 및 작업자에 대한 의미 체계는 아직 인식되지 않습니다. 배포 후 규모를 확장하려면 구성을 편집하고 작업자 노드 수를 늘리고 데이터를 배포합니다.   |0   |하이퍼스케일 기능을 제공하는 Citus 확장은 배포에 있지만 아직 로드되지 않았습니다.   |
|   |   |   |   |

1개의 작업자 작동을 나타내는 동안에는 사용하지 않는 것이 좋습니다. 이 배포는 많은 가치를 제공하지 않습니다. 이를 통해 Postgres 인스턴스 2개(코디네이터 1개 및 작업자 1개)를 얻게 됩니다. 이 설정을 사용하면 단일 작업자를 배포하기 때문에 실제로 데이터를 확장하지 않습니다. 따라서 성능 및 확장성 수준이 향상되지 않습니다. 이후 릴리스에서 이 배포에 대한 지원을 제거합니다.

- 서버 그룹에서 사용하려는 **스토리지 클래스**. 배포 후에는 변경할 수 없으므로 서버 그룹을 배포할 때 바로 스토리지 클래스를 설정하는 것이 중요합니다. 배포 후 스토리지 클래스를 변경하려면 데이터를 추출하고, 서버 그룹을 삭제하고, 새 서버 그룹을 만들고, 데이터를 가져와야 합니다. 데이터, 로그 및 백업에 사용할 스토리지 클래스를 지정할 수 있습니다. 기본적으로 스토리지 클래스를 지정하지 않으면 데이터 컨트롤러의 스토리지 클래스가 사용됩니다.
    - 데이터에 대한 스토리지 클래스를 설정하려면 매개 변수 `--storage-class-data` 또는 `-scd` 뒤에 스토리지 클래스 이름을 지정합니다.
    - 로그에 대한 스토리지 클래스를 설정하려면 매개 변수 `--storage-class-logs` 또는 `-scl` 뒤에 스토리지 클래스 이름을 지정합니다.
    - 백업에 대한 스토리지 클래스를 설정하려면 이 Azure Arc 지원 PostgreSQL 하이퍼스케일 미리 보기에서 수행하려는 백업/복원 작업 유형에 따라 스토리지 클래스를 설정하는 두 가지 방법이 있습니다. 이 경험을 단순화하기 위해 노력하고 있습니다. 스토리지 클래스 또는 볼륨 클레임 탑재를 지정합니다. 볼륨 클레임 탑재는 콜론으로 구분된 기존 영구 볼륨 클레임(동일한 네임스페이스에 있음) 및 볼륨 유형(및 볼륨 유형에 따른 선택적 메타데이터)의 쌍입니다. 영구 볼륨은 PostgreSQL 서버 그룹의 각 Pod에 탑재됩니다.
        - 전체 데이터베이스 복원만 수행하려면 매개 변수 `--storage-class-backups` 또는 `-scb` 뒤에 스토리지 클래스 이름을 설정합니다.
        - 전체 데이터베이스 복원과 특정 시점 복원을 모두 수행하려는 경우 매개 변수 `--volume-claim-mounts` 또는 `--volume-claim-mounts` 뒤에 볼륨 클레임 이름 및 볼륨 유형을 설정합니다.


## <a name="next-steps"></a>다음 단계

- Azure Arc 지원 PostgreSQL 하이퍼스케일에 연결: [연결 엔드포인트 및 연결 문자열 가져오기](get-connection-endpoints-and-connection-strings-postgres-hyperscale.md)를 읽습니다.
- Azure Database for PostgreSQL 하이퍼스케일의 개념 및 방법 가이드를 읽고 여러 PostgreSQL 하이퍼스케일 노드에 데이터를 배포하고 잠재적으로 더 나은 성능을 활용할 수 있습니다.
    * [노드 및 테이블](../../postgresql/concepts-hyperscale-nodes.md)
    * [애플리케이션 유형 확인](../../postgresql/concepts-hyperscale-app-type.md)
    * [배포 열 선택](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [테이블 공동 배치](../../postgresql/concepts-hyperscale-colocation.md)
    * [테이블 분산 및 수정](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [다중 테넌트 데이터베이스 설계](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [실시간 분석 대시보드 설계](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

    > \* 위 문서에서는 **Azure Portal에 로그인** 과 **Azure Database for PostgreSQL 만들기 – 하이퍼스케일(Citus)** 섹션을 건너뛰었습니다. Azure Arc 배포의 나머지 단계를 구현합니다. 이 섹션은 Azure 클라우드에서 PaaS 서비스로 제공되는 Azure Database for PostgreSQL 하이퍼스케일(Citus)에 한정되지만, 문서의 다른 부분은 Azure Arc 지원 PostgreSQL 하이퍼스케일에 직접 적용할 수 있습니다.

- [Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹 스케일 아웃](scale-out-in-postgresql-hyperscale-server-group.md)
- [스토리지 구성 및 Kubernetes 스토리지 개념](storage-configuration.md)
- [영구 볼륨 클레임 확장](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#expanding-persistent-volumes-claims)
- [Kubernetes 리소스 모델](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)


