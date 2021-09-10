---
title: CLI에서 Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹 만들기
description: CLI에서 Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹 만들기
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 24e81f9d83212b674b50acdb24042b6d29a9c266
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536404"
---
# <a name="create-an-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹 만들기

이 문서에서는 Azure Arc에서 PostgreSQL 하이퍼스케일 서버 그룹을 만드는 단계를 설명합니다.

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


## <a name="preliminary-and-temporary-step-for-openshift-users-only"></a>OpenShift 사용자만을 위한 임시 예비 단계
다음 단계로 이동하기 전에 이 단계를 구현합니다. PostgreSQL 하이퍼스케일 서버 그룹을 기본 프로젝트가 아닌 프로젝트의 Red Hat OpenShift에 배포하려면 클러스터에 대해 다음 명령을 실행하여 보안 제약 조건을 업데이트해야 합니다. 이 명령은 PostgreSQL 하이퍼스케일 서버 그룹을 실행하는 서비스 계정에 필요한 권한을 부여합니다. SCC(보안 컨텍스트 제약 조건) arc-data-scc는 Azure Arc 데이터 컨트롤러를 배포할 때 추가한 제약 조건입니다.

```Console
oc adm policy add-scc-to-user arc-data-scc -z <server-group-name> -n <namespace name>
```

**Server-group-name은 다음 단계에서 만든 서버 그룹의 이름입니다.**

OpenShift의 SCC에 대한 자세한 내용은 [OpenShift 설명서](https://docs.openshift.com/container-platform/4.2/authentication/managing-security-context-constraints.html)를 참조하세요. 이제 다음 단계를 구현할 수 있습니다.


## <a name="create-an-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹 만들기

Arc 데이터 컨트롤러에서 Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹을 만들려면 `az postgres arc-server create` 명령을 사용하고 여러 매개 변수를 전달합니다.

만들 때 설정할 수 있는 모든 매개 변수에 대한 자세한 내용은 다음 명령의 출력을 검토합니다.
```azurecli
az postgres arc-server create --help
```

고려해야 할 주요 매개 변수는 다음과 같습니다.
- 배치할 **서버 그룹의 이름**. `--name` 또는 `-n` 뒤에 길이가 11자를 초과하지 않는 이름을 지정합니다.

- 배포하려는 **PostgreSQL 엔진의 버전** 은 기본적으로 버전 12입니다. 버전 12를 배포하려면 이 매개 변수를 생략하거나 `--engine-version 12` 또는 `-ev 12` 매개 변수 중 하나를 전달할 수 있습니다. 버전 11을 배포하려면 `--engine-version 11` 또는 `-ev 11`을 지정합니다.

- 확장하고 잠재적으로 더 나은 성능에 도달하기 위해 배포하려는 **작업자 노드의 수**. 계속하기 전에 [Postgres 하이퍼스케일에 대한 개념](concepts-distributed-postgres-hyperscale.md)을 읽어보세요. 배포할 작업자 노드 수를 지정하려면 `--workers` 또는 `-w` 매개 변수 뒤에 정수를 사용합니다. 아래 표는 지원되는 값의 범위와 이러한 값과 함께 얻을 수 있는 Postgres 배포의 형식을 나타냅니다. 예를 들어, 2개의 작업자 노드가 있는 서버 그룹을 배치하려면 `--workers 2` 또는 `-w 2`를 지정합니다. 이렇게 하면 코디네이터 노드/인스턴스에 대해 하나씩, 작업자 노드/인스턴스에 대해 두 개(각 작업자에 대해 하나씩) 세 개의 Pod가 만들어집니다.



|다음 항목이 필요합니다.   |배포할 서버 그룹의 모양   |사용할 -w 매개 변수   |참고   |
|---|---|---|---|
|애플리케이션의 확장성 요구를 충족하기 위해 확장된 형태의 Postgres입니다.   |3개 이상의 Postgres 인스턴스, 1개는 코디네이터, n은 n >=2의 작업자입니다.   |-w n 사용, n>=2.   |하이퍼스케일 기능을 제공하는 Citus 확장이 로드됩니다.   |
|최소한의 비용으로 애플리케이션의 기능 유효성 검사를 수행할 수 있는 기본 형태의 Postgres 하이퍼스케일입니다. 성능 및 확장성 유효성 검사에는 유효하지 않습니다. 이를 위해 위에서 설명한 배포 유형을 사용해야 합니다.   |코디네이터 및 작업자인 1 Postgres 인스턴스.   |-w 0 사용, Citus 확장 로드. 명령줄에서 배포하는 경우 -w 0 --extensions Citus 매개 변수를 사용합니다.   |하이퍼스케일 기능을 제공하는 Citus 확장이 로드됩니다.   |
|필요할 때 확장할 준비가 된 Postgres의 간단한 인스턴스입니다.   |1 Postgres 인스턴스. 코디네이터 및 작업자에 대한 의미 체계는 아직 인식되지 않습니다. 배포 후 규모를 확장하려면 구성을 편집하고 작업자 노드 수를 늘리고 데이터를 배포합니다.   |-w 0 사용, -w 지정 안 함.   |하이퍼스케일 기능을 제공하는 Citus 확장은 배포에 있지만 아직 로드되지 않았습니다.   |
|   |   |   |   |

-w 1을 사용할 수도 있지만 사용하지 않는 것이 좋습니다. 이 배포는 많은 가치를 제공하지 않습니다. 이를 통해 Postgres 인스턴스 2개(코디네이터 1개 및 작업자 1개)를 얻게 됩니다. 이 설정을 사용하면 단일 작업자를 배포하기 때문에 실제로 데이터를 확장하지 않습니다. 따라서 성능 및 확장성 수준이 향상되지 않습니다. 이후 릴리스에서 이 배포에 대한 지원을 제거합니다.

- 서버 그룹에서 사용하려는 **스토리지 클래스**. 배포 후에는 변경할 수 없으므로 서버 그룹을 배포할 때 바로 스토리지 클래스를 설정하는 것이 중요합니다. 배포 후 스토리지 클래스를 변경하려면 데이터를 추출하고, 서버 그룹을 삭제하고, 새 서버 그룹을 만들고, 데이터를 가져와야 합니다. 데이터, 로그 및 백업에 사용할 스토리지 클래스를 지정할 수 있습니다. 기본적으로 스토리지 클래스를 지정하지 않으면 데이터 컨트롤러의 스토리지 클래스가 사용됩니다.
    - 데이터에 대한 스토리지 클래스를 설정하려면 매개 변수 `--storage-class-data` 또는 `-scd` 뒤에 스토리지 클래스 이름을 지정합니다.
    - 로그에 대한 스토리지 클래스를 설정하려면 매개 변수 `--storage-class-logs` 또는 `-scl` 뒤에 스토리지 클래스 이름을 지정합니다.
    - 백업에 대한 스토리지 클래스를 설정하려면 이 Azure Arc 지원 PostgreSQL 하이퍼스케일 미리 보기에서 수행하려는 백업/복원 작업 유형에 따라 스토리지 클래스를 설정하는 두 가지 방법이 있습니다. 이 경험을 단순화하기 위해 노력하고 있습니다. 스토리지 클래스 또는 볼륨 클레임 탑재를 지정합니다. 볼륨 클레임 탑재는 콜론으로 구분된 기존 영구 볼륨 클레임(동일한 네임스페이스에 있음) 및 볼륨 유형(및 볼륨 유형에 따른 선택적 메타데이터)의 쌍입니다. 영구 볼륨은 PostgreSQL 서버 그룹의 각 Pod에 탑재됩니다.
        - 전체 데이터베이스 복원만 수행하려면 매개 변수 `--storage-class-backups` 또는 `-scb` 뒤에 스토리지 클래스 이름을 설정합니다.
        - 전체 데이터베이스 복원과 특정 시점 복원을 모두 수행하려는 경우 매개 변수 `--volume-claim-mounts` 또는 `--volume-claim-mounts` 뒤에 볼륨 클레임 이름 및 볼륨 유형을 설정합니다.

create 명령을 실행할 때 기본 `postgres` 관리 사용자의 암호를 입력하라는 프롬프트가 표시됩니다. 이 미리 보기에서는 해당 사용자의 이름을 변경할 수 없습니다. create 명령을 실행하기 전에 `AZDATA_PASSWORD` 세션 환경 변수를 설정하여 대화형 프롬프트를 건너뛸 수 있습니다.

### <a name="examples"></a>예

**데이터 컨트롤러와 동일한 스토리지 클래스를 사용하는 작업자 노드가 2개 있는 postgres01이라는 Postgres 버전 12의 서버 그룹을 배포하려면 다음 명령을 실행합니다.**
```azurecli
az postgres arc-server create -n postgres01 --workers 2 --k8s-namespace <namespace> --use-k8s
```

**데이터 및 로그용 데이터 컨트롤러와 동일한 스토리지 클래스를 사용하지만 전체 복원과 특정 시점 복원을 모두 수행하는 특정 스토리지 클래스를 사용하는 작업자 노드가 2개 있는 postgres01이라는 이름의 Postgres 버전 12 서버 그룹을 배포하려면 다음 단계를 사용합니다.**

 이 예에서는 서버 그룹이 AKS(Azure Kubernetes Service) 클러스터에서 호스팅된다고 가정합니다. 이 예에서는 azurefile-premium을 스토리지 클래스 이름으로 사용합니다. 환경에 맞게 아래 예를 조정할 수 있습니다. 이 구성에는 **accessModes ReadWriteMany가 필요합니다**.  

먼저 백업 PVC에 대한 아래 설명이 포함된 YAML 파일을 만들고 이름을 CreateBackupPVC.yml 등으로 지정합니다.
```console
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: backup-pvc
  namespace: arc
spec:
  accessModes:
    - ReadWriteMany
  volumeMode: Filesystem
  resources:
    requests:
      storage: 100Gi
  storageClassName: azurefile-premium
```

다음으로 YAML 파일에 저장된 정의를 사용하여 PVC를 만듭니다.

```console
kubectl create -f e:\CreateBackupPVC.yml -n arc
``` 

그 다음, 서버 그룹을 만듭니다.

```azurecli
az postgres arc-server create -n postgres01 --workers 2 --volume-claim-mounts backup-pvc:backup --k8s-namespace <namespace> --use-k8s
```

> [!IMPORTANT]
> - [백업/복원과 관련된 현재 제한 사항](limitations-postgresql-hyperscale.md#backup-and-restore)을 읽어보세요.


> [!NOTE]  
> - 동일한 터미널 세션에서 `AZDATA_USERNAME` 및 `AZDATA_PASSWORD` 세션 환경 변수를 사용하여 데이터 컨트롤러를 배포한 경우 `AZDATA_PASSWORD`의 값이 PostgreSQL 하이퍼스케일 서버 그룹을 배포하는 데에도 사용됩니다. 다른 암호를 사용하려면 (1) `AZDATA_PASSWORD`의 값을 업데이트하거나 (2) `AZDATA_PASSWORD` 환경 변수를 삭제하거나 (3) 서버 그룹을 만들 때 대화형으로 암호를 입력하라는 메시지가 표시되도록 해당 값을 삭제합니다.
> - PostgreSQL 하이퍼스케일 서버 그룹을 만들면 Azure에 리소스가 즉시 등록되지 않습니다. [리소스 인벤토리](upload-metrics-and-logs-to-azure-monitor.md) 또는 [사용 데이터](view-billing-data-in-azure.md)를 Azure에 업로드하는 프로세스의 일부로 리소스가 Azure에서 만들어지고 Azure Portal에서 리소스를 볼 수 있습니다.



## <a name="list-the-postgresql-hyperscale-server-groups-deployed-in-your-arc-data-controller"></a>Arc 데이터 컨트롤러에 배포된 PostgreSQL 하이퍼스케일 서버 그룹 나열

Arc 데이터 컨트롤러에 배포된 PostgreSQL 하이퍼스케일 서버 그룹을 나열하려면 다음 명령을 실행합니다.

```azurecli
az postgres arc-server list --k8s-namespace <namespace> --use-k8s
```


```output
Name        State     Workers
----------  --------  ---------
postgres01  Ready     2
```

## <a name="get-the-endpoints-to-connect-to-your-azure-arc-enabled-postgresql-hyperscale-server-groups"></a>Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹에 연결하는 엔드포인트 가져오기

PostgreSQL 서버 그룹의 엔드포인트를 보려면 다음 명령을 실행합니다.

```azurecli
az postgres arc-server endpoint list -n <server group name> --k8s-namespace <namespace> --use-k8s
```
예를 들면 다음과 같습니다.
```console
[
  {
    "Description": "PostgreSQL Instance",
    "Endpoint": "postgresql://postgres:<replace with password>@12.345.123.456:1234"
  },
  {
    "Description": "Log Search Dashboard",
    "Endpoint": "https://12.345.123.456:12345/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:\"postgres01\"'))"
  },
  {
    "Description": "Metrics Dashboard",
    "Endpoint": "https://12.345.123.456:12345/grafana/d/postgres-metrics?var-Namespace=arc3&var-Name=postgres01"
  }
]
```

PostgreSQL 인스턴스 엔드포인트를 사용하여 선호하는 도구([Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio), [pgcli](https://www.pgcli.com/) psql, pgAdmin 등)에서 PostgreSQL 하이퍼스케일 서버 그룹에 연결할 수 있습니다. 이렇게 하면 분산 테이블을 만든 경우 쿼리를 적절한 작업자 노드/인스턴스로 라우팅하는 코디네이터 노드/인스턴스에 연결합니다. 자세한 내용은 [Azure Arc 지원 PostgreSQL 하이퍼스케일의 개념](concepts-distributed-postgres-hyperscale.md)을 참조하세요.

   [!INCLUDE [use-insider-azure-data-studio](includes/use-insider-azure-data-studio.md)]

## <a name="special-note-about-azure-virtual-machine-deployments"></a>Azure 가상 머신 배포에 대한 특별 메모

Azure 가상 머신을 사용하는 경우 엔드포인트 IP 주소는 _공용_ IP 주소를 표시하지 않습니다. 공용 IP 주소를 찾으려면 다음 명령을 사용합니다.

```azurecli
az network public-ip list -g azurearcvm-rg --query "[].{PublicIP:ipAddress}" -o table
```

그러면 공용 IP 주소와 포트를 결합하여 연결을 만들 수 있습니다.

NSG(네트워크 보안 게이트웨이)를 통해 PostgreSQL 하이퍼스케일 서버 그룹의 포트를 노출해야 할 수도 있습니다. NSG를 통한 트래픽을 허용하려면 다음 명령을 사용하여 수행할 수 있는 규칙을 추가해야 합니다.

규칙을 설정하려면 NSG의 이름을 알아야 합니다. 아래 명령을 사용하여 NSG를 결정합니다.

```azurecli
az network nsg list -g azurearcvm-rg --query "[].{NSGName:name}" -o table
```

NSG의 이름을 보유한 경우 다음 명령을 사용하여 방화벽 규칙을 추가할 수 있습니다. 이 예제 값에서는 포트 30655에 대한 NSG 규칙을 만들고 **모든** 원본 IP 주소에서 연결할 수 있도록 허용합니다.  이 방법은 보안 모범 사례가 아닙니다.  팀 또는 조직의 IP 주소를 포함하는 IP 주소 범위 또는 클라이언트 IP 주소와 관련된 -source-address-prefixes 값을 지정하면 더 잘 잠글 수 있습니다.

아래의 --destination-port-ranges 매개 변수 값을 위의 ‘az postgres arc-server list’ 명령에서 가져온 포트 번호로 바꿉니다.

```azurecli
az network nsg rule create -n db_port --destination-port-ranges 30655 --source-address-prefixes '*' --nsg-name azurearcvmNSG --priority 500 -g azurearcvm-rg --access Allow --description 'Allow port through for db access' --destination-address-prefixes '*' --direction Inbound --protocol Tcp --source-port-ranges '*'
```

## <a name="connect-with-azure-data-studio"></a>Azure Data Studio를 사용하여 연결

Azure Data Studio를 열고 위의 외부 엔드포인트 IP 주소 및 포트 번호와 인스턴스를 만들 때 지정한 암호를 사용하여 인스턴스에 연결합니다.  *연결 유형* 드롭다운에서 PostgreSQL을 사용할 수 없는 경우 확장 탭에서 PostgreSQL를 검색하여 PostgreSQL 확장을 설치할 수 있습니다.

> [!NOTE]
> 포트 번호를 입력하려면 연결 패널에서 [고급] 단추를 클릭해야 합니다.

Azure VM을 사용하는 경우 다음 명령을 통해 액세스할 수 있는 _공용_ IP 주소가 필요합니다.

```azurecli
az network public-ip list -g azurearcvm-rg --query "[].{PublicIP:ipAddress}" -o table
```

## <a name="connect-with-psql"></a>psql을 사용하여 연결

PostgreSQL 하이퍼스케일 서버 그룹에 액세스하려면 위에서 검색한 PostgreSQL 하이퍼스케일 서버 그룹의 외부 엔드포인트를 전달합니다.

이제 psql을 연결할 수 있습니다.

```console
psql postgresql://postgres:<EnterYourPassword>@10.0.0.4:30655
```

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
