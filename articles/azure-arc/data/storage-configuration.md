---
title: 스토리지 구성
description: Azure Arc 지원 데이터 서비스 스토리지 구성 옵션에 대해 설명합니다.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: uc-msft
ms.author: umajay
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: conceptual
ms.openlocfilehash: 35d53e58fa6b4cb1a3b460077d1401ebba3a2c24
ms.sourcegitcommit: 01b678462a4a390c30463c525432ffbbbe0195cf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132957282"
---
# <a name="storage-configuration"></a>스토리지 구성


## <a name="kubernetes-storage-concepts"></a>Kubernetes 스토리지 개념

Kubernetes는 기본 가상화 기술 스택(선택 사항) 및 하드웨어에 대한 인프라 추상화 레이어를 제공합니다. Kubernetes에서 스토리지를 추상화하는 방법은 **[스토리지 클래스](https://kubernetes.io/docs/concepts/storage/storage-classes/)** 를 사용하는 것입니다. Pod를 프로비저닝할 때 각 볼륨에 대해 스토리지 클래스를 지정할 수 있습니다. Pod를 프로비저닝하면 스토리지 클래스 **[프로비저닝 프로그램](https://kubernetes.io/docs/concepts/storage/dynamic-provisioning/)** 이 호출되어 스토리지를 프로비저닝하고 프로비저닝된 스토리지에 **[영구 볼륨](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)** 을 만든 다음, **[영구 볼륨 클레임](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#persistentvolumeclaims)** 에서 영구 볼륨에 Pod를 탑재합니다.

Kubernetes는 스토리지 인프라 공급자가 Kubernetes를 확장하는 드라이버(“추가 기능”이라고도 함)를 연결하는 방법을 제공합니다. 스토리지 추가 기능은 **[Container Storage Interface 표준](https://kubernetes.io/blog/2019/01/15/container-storage-interface-ga/)** 을 준수해야 합니다. 수십 개의 추가 기능은 이 확정되지 않은 **[CSI 드라이버 목록](https://kubernetes-csi.github.io/docs/drivers.html)** 을 참조하세요. 사용하는 CSI 드라이버는 클라우드 호스트, 관리되는 Kubernetes 서비스에서 실행 중인지 여부 또는 하드웨어에 어떤 OEM 공급자를 사용하는지와 같은 요인에 따라 달라집니다.

다음 명령을 실행하여 Kubernetes 클러스터에 구성된 스토리지 클래스를 볼 수 있습니다.

```console
kubectl get storageclass
```

AKS(Azure Kubernetes Service) 클러스터의 예제 출력:

```console
NAME                PROVISIONER                AGE
azurefile           kubernetes.io/azure-file   15d
azurefile-premium   kubernetes.io/azure-file   15d
default (default)   kubernetes.io/azure-disk   4d3h
managed-premium     kubernetes.io/azure-disk   4d3h
```

다음 명령을 실행하여 스토리지 클래스에 대한 세부 정보를 얻을 수 있습니다.

```console
kubectl describe storageclass/<storage class name>
```

예:

```console
kubectl describe storageclass/azurefile

Name:            azurefile
IsDefaultClass:  No
Annotations:     kubectl.kubernetes.io/last-applied-configuration={"allowVolumeExpansion":true,"apiVersion":"storage.k8s.io/v1beta1","kind":"StorageClass","metadata":{"annotations":{},"labels":{"kubernetes.io/cluster-service":"true"},"name":"azurefile"},"parameters":{"sku
Name":"Standard_LRS"},"provisioner":"kubernetes.io/azure-file"}

Provisioner:           kubernetes.io/azure-file
Parameters:            skuName=Standard_LRS
AllowVolumeExpansion:  True
MountOptions:          <none>
ReclaimPolicy:         Delete
VolumeBindingMode:     Immediate
Events:                <none>
```

다음 명령을 실행하여 현재 프로비저닝된 영구 볼륨 및 영구 볼륨 클레임을 확인할 수 있습니다.

```console
kubectl get persistentvolumes -n <namespace>

kubectl get persistentvolumeclaims -n <namespace>
```

영구 볼륨을 보여 주는 예시:

```console

kubectl get persistentvolumes -n arc

NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                      STORAGECLASS   REASON   AGE
pvc-07fc7b9f-9a37-4796-9442-4405147120da   15Gi       RWO            Delete           Bound    arc/sqldemo11-data-claim   default                 7d3h
pvc-3e772f20-ed89-4642-b34d-8bb11b088afa   15Gi       RWO            Delete           Bound    arc/data-metricsdb-0       default                 7d14h
pvc-41b33bbd-debb-4153-9a41-02ce2bf9c665   10Gi       RWO            Delete           Bound    arc/sqldemo11-logs-claim   default                 7d3h
pvc-4ccda3e4-fee3-4a89-b92d-655c04fa62ad   15Gi       RWO            Delete           Bound    arc/data-controller        default                 7d14h
pvc-63e6bb4c-7240-4de5-877e-7e9ea4e49c91   10Gi       RWO            Delete           Bound    arc/logs-controller        default                 7d14h
pvc-8a1467fe-5eeb-4d73-b99a-f5baf41eb493   10Gi       RWO            Delete           Bound    arc/logs-metricsdb-0       default                 7d14h
pvc-8e2cacbc-e953-4901-8591-e77df9af309c   10Gi       RWO            Delete           Bound    arc/sqldemo10-logs-claim   default                 7d14h
pvc-9fb79ba3-bd3e-42aa-aa09-3090135d4513   15Gi       RWO            Delete           Bound    arc/sqldemo10-data-claim   default                 7d14h
pvc-a39c85d4-5cd9-4249-9915-68a70a9bb5e5   15Gi       RWO            Delete           Bound    arc/data-controldb         default                 7d14h
pvc-c9cbd74a-76ca-4be5-b598-0c7a45749bfb   10Gi       RWO            Delete           Bound    arc/logs-controldb         default                 7d14h
pvc-d576e9d4-0a09-4dd7-b806-be8ed461f8a4   10Gi       RWO            Delete           Bound    arc/logs-logsdb-0          default                 7d14h
pvc-ecd7d07f-2c2c-421d-98d7-711ec5d4a0cd   15Gi       RWO            Delete           Bound    arc/data-logsdb-0          default                 7d14h
```

영구 볼륨 클레임을 보여 주는 예시:

```console

kubectl get persistentvolumeclaims -n arc

NAME                   STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
data-controldb         Bound    pvc-a39c85d4-5cd9-4249-9915-68a70a9bb5e5   15Gi       RWO            default        7d14h
data-controller        Bound    pvc-4ccda3e4-fee3-4a89-b92d-655c04fa62ad   15Gi       RWO            default        7d14h
data-logsdb-0          Bound    pvc-ecd7d07f-2c2c-421d-98d7-711ec5d4a0cd   15Gi       RWO            default        7d14h
data-metricsdb-0       Bound    pvc-3e772f20-ed89-4642-b34d-8bb11b088afa   15Gi       RWO            default        7d14h
logs-controldb         Bound    pvc-c9cbd74a-76ca-4be5-b598-0c7a45749bfb   10Gi       RWO            default        7d14h
logs-controller        Bound    pvc-63e6bb4c-7240-4de5-877e-7e9ea4e49c91   10Gi       RWO            default        7d14h
logs-logsdb-0          Bound    pvc-d576e9d4-0a09-4dd7-b806-be8ed461f8a4   10Gi       RWO            default        7d14h
logs-metricsdb-0       Bound    pvc-8a1467fe-5eeb-4d73-b99a-f5baf41eb493   10Gi       RWO            default        7d14h
sqldemo10-data-claim   Bound    pvc-9fb79ba3-bd3e-42aa-aa09-3090135d4513   15Gi       RWO            default        7d14h
sqldemo10-logs-claim   Bound    pvc-8e2cacbc-e953-4901-8591-e77df9af309c   10Gi       RWO            default        7d14h
sqldemo11-data-claim   Bound    pvc-07fc7b9f-9a37-4796-9442-4405147120da   15Gi       RWO            default        7d4h
sqldemo11-logs-claim   Bound    pvc-41b33bbd-debb-4153-9a41-02ce2bf9c665   10Gi       RWO            default        7d4h

```

## <a name="factors-to-consider-when-choosing-your-storage-configuration"></a>스토리지 구성을 선택할 때 고려해야 할 요소

올바른 스토리지 클래스를 선택하는 것은 데이터 복원력 및 성능에 중요합니다. 잘못된 스토리지 클래스를 선택하면 하드웨어 오류 발생 시 데이터가 총 데이터 손실 위험에 노출되거나 성능이 저하될 수 있습니다.

스토리지에는 보통 다음과 같은 두 종류가 있습니다.

- **로컬 스토리지** -지정된 노드의 로컬 하드 드라이브에서 프로비저닝된 스토리지입니다. 이러한 종류의 스토리지는 성능 측면에서 이상적이지만 여러 노드에 걸쳐 데이터를 복제하여 데이터 중복성을 위해 특별히 설계해야 합니다.
- **원격 공유 스토리지** - 일부 원격 스토리지 디바이스에 프로비저닝된 스토리지(예: SAN, NAS 또는 EBS나 Azure Files와 같은 클라우드 스토리지 서비스)입니다. 이러한 종류의 스토리지는 일반적으로 데이터 중복성을 자동으로 제공하지만 로컬 스토리지만큼 빠르지는 않습니다.

## <a name="nfs-based-storage-classes"></a>NFS 기반 스토리지 클래스

NFS 서버 및 스토리지 클래스 프로비저닝 프로그램의 구성에 따라 데이터베이스 인스턴스에 대한 pod 구성에서 `supplementalGroups`를 설정하고 클라이언트에서 전달된 그룹 ID를 사용하도록 NFS 서버 구성을 변경해야 할 수 있습니다(전달된 사용자 ID를 사용하여 서버에서 그룹 ID를 조회하는 것이 아님). 이 경우에 해당하는지 확인하려면 NFS 관리자에게 문의하세요.

`supplementalGroups` 속성은 값 배열을 사용하고 Azure Arc 데이터 컨트롤러 배포의 일부로 설정할 수 있으며 Azure Arc 데이터 컨트롤러에 의해 구성된 데이터베이스 인스턴스에서 사용됩니다.

이 속성을 설정하려면 다음 명령을 실행하세요.

```azurecli
az arcdata dc config add --path custom/control.json --json-values 'spec.security.supplementalGroups="1234556"'
```

### <a name="data-controller-storage-configuration"></a>데이터 컨트롤러 스토리지 구성

데이터 서비스에 대한 Azure Arc의 일부 서비스는 데이터를 복제하는 기능이 없기 때문에 원격 공유 스토리지를 사용하도록 구성되었는지에 따라 달라집니다. 관련 서비스는 데이터 컨트롤러 Pod의 컬렉션에서 찾을 수 있습니다.

|**서비스**|**영구 볼륨 클레임**|
|---|---|
|**ElasticSearch**|`<namespace>/logs-logsdb-0`, `<namespace>/data-logsdb-0`|
|**InfluxDB**|`<namespace>/logs-metricsdb-0`, `<namespace>/data-metricsdb-0`|
|**컨트롤러 SQL 인스턴스**|`<namespace>/logs-controldb`, `<namespace>/data-controldb`|
|**컨트롤러 API 서비스**|`<namespace>/data-controller`|

데이터 컨트롤러가 프로비저닝될 때 --스토리지 클래스 | -sc 매개 변수를 `az arcdata dc create` 명령에 전달하거나, 사용되는 control.json 배포 템플릿 파일에 스토리지 클래스를 설정하여 이러한 각 영구 볼륨에 사용할 스토리지 클래스가 지정됩니다.  Azure Portal을 사용하여 직접 연결 모드에서 데이터 컨트롤러를 만드는 경우 선택한 배포 템플릿에는 템플릿에 미리 정의된 스토리지 클래스가 있거나 미리 정의된 스토리지 클래스가 없는 템플릿을 선택한 경우에는 해당 템플릿을 선택하라는 메시지가 표시됩니다.  사용자 지정 배포 템플릿을 사용하는 경우 스토리지 클래스를 지정할 수 있습니다.

기본적으로 제공되는 배포 템플릿에는 대상 환경에 적합한 기본 스토리지 클래스가 지정되어 있지만 배포 시 재정의될 수 있습니다. 배포 시 pod 데이터 컨트롤러에 대 한 저장소 클래스 구성을 변경 하려면 [사용자 지정 구성 temmplates를 만드는](create-custom-configuration-template.md) 자세한 단계를 참조 하세요.

--스토리지 클래스 | -sc 매개 변수를 사용하여 스토리지 클래스를 설정하면 로그 및 데이터 스토리지 클래스 모두에 스토리지 클래스가 사용됩니다. 배포 템플릿 파일에서 스토리지 클래스를 설정하면 로그와 데이터에 서로 다른 스토리지 클래스를 지정할 수 있습니다.

데이터 컨트롤러 Pod의 스토리지 클래스를 선택할 때 고려해야 할 중요한 요소는 다음과 같습니다.

- 데이터 내구성을 보장하고 Pod 또는 노드가 종료되는 경우 Pod를 재시작하면 영구 볼륨에 다시 연결될 수 있도록 **반드시** 원격 공유 스토리지 클래스를 사용해야 합니다.
- 컨트롤러 SQL 인스턴스, 메트릭 DB, 로그 DB에 기록되는 데이터의 볼륨은 일반적으로 매우 낮으며 대기 시간에 민감하지 않으므로 초고속 성능 스토리지는 중요하지 않습니다. Grafana 및 Kibana 인터페이스를 자주 사용하는 사용자가 있고 많은 수의 데이터베이스 인스턴스가 있는 경우 사용자는 더 빠른 성능의 스토리지를 활용할 수 있습니다.
- 각 데이터베이스 인스턴스에 대해 로그 및 메트릭이 수집되기 때문에 필요한 스토리지 용량은 배포한 데이터베이스 인스턴스 수에 따라 달라집니다. 데이터는 2주 동안 로그 및 메트릭 DB에 보관되었다가 삭제됩니다. 
- 배포 후 스토리지 클래스 변경은 어렵고 설명서가 없으며 지원되지 않습니다. 배포 시 스토리지 클래스를 올바르게 선택해야 합니다.

> [!NOTE]
> 스토리지 클래스를 지정하지 않으면 기본 스토리지 클래스가 사용됩니다. Kubernetes 클러스터당 하나의 기본 스토리지 클래스만 있을 수 있습니다. [기본 스토리지 클래스를 변경](https://kubernetes.io/docs/tasks/administer-cluster/change-default-storage-class/)할 수 있습니다.

### <a name="database-instance-storage-configuration"></a>데이터베이스 인스턴스 스토리지 구성

각 데이터베이스 인스턴스에는 데이터, 로그, 백업 영구 볼륨이 있습니다. 이러한 영구 볼륨의 스토리지 클래스는 배포 시 지정할 수 있습니다. 스토리지 클래스를 지정하지 않으면 기본 스토리지 클래스가 사용됩니다.

`az sql mi-arc create` 또는 `az postgres arc-server create`를 사용하여 인스턴스를 만드는 경우 다음 네 가지 매개 변수를 사용하여 스토리지 클래스를 설정할 수 있습니다.

|매개 변수 이름, 짧은 이름|사용 대상|
|---|---|
|`--storage-class-data`, `-d`|트랜잭션 로그 파일을 포함한 모든 데이터 파일에 대한 스토리지 클래스를 지정하는 데 사용됩니다.|
|`--storage-class-logs`, `-g`|모든 로그 파일에 대한 스토리지 클래스를 지정하는 데 사용됩니다.|
|`--storage-class-data-logs`|데이터베이스 트랜잭션 로그 파일에 대한 스토리지 클래스를 지정하는 데 사용됩니다.|
|`--storage-class-backups`|모든 백업 파일에 대한 스토리지 클래스를 지정하는 데 사용됩니다.|

다음 표에서는 데이터 및 로그에 대해 영구적 볼륨에 매핑되는 Azure SQL Managed Instance 컨테이너 내의 경로를 나열합니다.

|매개 변수 이름, 짧은 이름|mssql-miaa 컨테이너 내의 경로|Description|
|---|---|---|
|`--storage-class-data`, `-d`|/var/opt|mssql 설치 및 기타 시스템 프로세스에 대한 디렉터리를 포함합니다. mssql 디렉터리는 기본 데이터(트랜잭션 로그 포함), 오류 로그, 백업 디렉터리를 포함합니다.|
|`--storage-class-logs`, `-g`|/var/log|콘솔 출력(stderr, stdout)을 저장하는 디렉터리와 컨테이너 내의 프로세스에 대한 기타 로깅 정보를 포함합니다.|

다음 표에서는 데이터 및 로그에 대해 영구 볼륨에 매핑되는 PostgreSQL 인스턴스 컨테이너 내의 경로를 나열합니다.

|매개 변수 이름, 짧은 이름|Postgres 컨테이너 내의 경로|Description|
|---|---|---|
|`--storage-class-data`, `-d`|/var/opt/postgresql|Postgres 설치용 데이터 및 로그 디렉터리를 포함합니다.|
|`--storage-class-logs`, `-g`|/var/log|콘솔 출력(stderr, stdout)을 저장하는 디렉터리와 컨테이너 내의 프로세스에 대한 기타 로깅 정보를 포함합니다.|

각 데이터베이스 인스턴스에는 데이터 파일, 로그, 백업에 대한 별도의 영구 볼륨이 있습니다. 즉, 볼륨 프로비저닝 프로그램에서 스토리지를 프로비저닝하는 방식에 따라 각 파일 형식에 대해 I/O를 분리하게 됩니다. 각 데이터베이스 인스턴스에는 고유한 영구 볼륨 클레임 및 영구 볼륨이 있습니다.

지정된 데이터베이스 인스턴스에 여러 데이터베이스가 있는 경우 모든 데이터베이스는 동일한 영구 볼륨 클레임, 영구 볼륨, 스토리지 클래스를 사용합니다. 모든 백업 - 차등 로그 백업과 전체 백업 모두 동일한 영구 볼륨 클레임과 영구 볼륨을 사용합니다. 데이터베이스 인스턴스 Pod에 대한 영구 볼륨 클레임은 다음과 같습니다.

|**인스턴스**|**영구 볼륨 클레임**|
|---|---|
|**Azure SQL Managed Instance**|`<namespace>/logs-<instance name>-0`, `<namespace>/data-<instance name>-0`|
|**Azure Database for PostgreSQL 인스턴스**|`<namespace>/logs--<instance name>-0`, `<namespace>/data--<instance name>-0`|
|**Azure PostgreSQL 하이퍼스케일**|`<namespace>/logs-<instance namme>-<ordinal>`, `<namespace>/data-<instance namme>-<ordinal>` *(서수는 0부터 W까지로, W는 작업자 수)*|

데이터베이스 인스턴스 Pod의 스토리지 클래스를 선택할 때 고려해야 할 중요한 요소는 다음과 같습니다.

- 데이터베이스 인스턴스는 단일 Pod 패턴 또는 여러 Pod 패턴으로 배포할 수 있습니다. 단일 Pod 패턴의 예로는 범용 가격 책정 계층 Azure SQL 관리형 인스턴스가 있습니다. 여러 Pod 패턴의 예시로는 고가용성 중요 비즈니스용 가격 책정 계층 Azure SQL 관리형 인스턴스가 있습니다. 단일 Pod 패턴을 사용하여 배포된 데이터베이스 인스턴스는 데이터 내구성을 보장하고 Pod 또는 노드가 종료되는 경우 Pod를 재시작하면 영구 볼륨에 다시 연결될 수 있도록 **반드시** 원격 공유 스토리지 클래스를 사용해야 합니다. 반면, 고가용성 Azure SQL 관리형 인스턴스는 Always On 가용성 그룹을 사용하여 동기식 또는 비동기식으로 한 인스턴스에서 다른 인스턴스로 데이터를 복제합니다. 특히 데이터가 동기식으로 복제되는 경우 데이터의 복사본은 항상 여러 개(일반적으로 3개)입니다. 따라서 데이터와 로그 파일에 로컬 스토리지 또는 원격 공유 스토리지 클래스를 사용할 수 있습니다. 로컬 스토리지를 활용하면 데이터 복사본이 여러 개 있기 때문에 Pod, 노드 또는 스토리지 하드웨어가 실패하는 경우에도 데이터는 계속 유지됩니다. 이러한 유연성을 통해, 더 나은 성능을 위해 로컬 스토리지를 사용하도록 선택할 수 있습니다.
- 데이터베이스 성능은 주로 지정된 스토리지 디바이스 I/O 처리량의 기능입니다. 데이터베이스에 읽기 또는 쓰기 작업이 많은 경우 하드웨어가 해당 유형의 워크로드를 위해 설계된 스토리지 클래스를 선택해야 합니다. 예를 들어 데이터베이스가 쓰기에 주로 사용되는 경우 RAID 0에서 로컬 스토리지를 선택할 수 있습니다. 데이터베이스가 적은 양의 “핫 데이터”에 대한 읽기에 주로 사용되지만 콜드 데이터의 전체 스토리지 볼륨이 큰 경우 계층화된 스토리지를 지원하는 SAN 디바이스를 선택할 수 있습니다. 올바른 스토리지 클래스를 선택하는 것은 데이터베이스에 사용할 스토리지 유형을 선택하는 것과 다르지 않습니다.
- 로컬 스토리지 볼륨 프로비저닝 프로그램을 사용하는 경우 디스크 I/O의 경합을 방지하기 위해 데이터, 로그, 백업에 대해 프로비저닝된 로컬 볼륨이 각각 서로 다른 기본 스토리지 디바이스에 있는지 확인합니다. 또한 OS는 별도의 디스크에 탑재된 볼륨에 있어야 합니다. 이는 기본적으로 실제 하드웨어의 데이터베이스 인스턴스에 대해 수행되는 것과 동일한 지침입니다.
- 지정된 인스턴스의 모든 데이터베이스가 영구 볼륨 클레임과 영구 볼륨을 공유하기 때문에 동일한 데이터베이스 인스턴스에서 사용 중인 데이터베이스 인스턴스를 공동 배치하지 않아야 합니다. 가능하면 I/O 경합을 방지하기 위해 사용 중인 데이터베이스를 자체 데이터베이스 인스턴스로 분리합니다. 또한 노드 레이블 대상 지정을 사용하여 여러 노드에 걸친 전체 I/O 트래픽을 분산하기 위해 데이터베이스 인스턴스를 별도의 노드에 배치할 수 있습니다. 가상화를 사용하는 경우 노드 수준뿐만 아니라 지정된 실제 호스트의 모든 노드 VM에서 발생하는 결합된 I/O 작업을 분산하는 것을 고려해야 합니다.

## <a name="estimating-storage-requirements"></a>스토리지 요구 사항 예측
상태 저장 데이터를 포함하는 모든 Pod는 두 개 이상의 영구 볼륨을 사용합니다. 하나는 데이터에 대한 영구 볼륨이고 다른 하나는 로그에 대한 영구 볼륨입니다. 아래 표에는 단일 데이터 컨트롤러, Azure SQL Managed 인스턴스, Azure Database for PostgreSQL 인스턴스, Azure PostgreSQL 하이퍼스케일 인스턴스에 필요한 영구 볼륨 수가 나열되어 있습니다.

|리소스 종류|상태 저장 Pod 수|필요한 영구 볼륨 수|
|---|---|---|
|데이터 컨트롤러|4 (`control`, `controldb`, `logsdb`, `metricsdb`)|4 * 2 = 8|
|Azure SQL Managed Instance|1|2|
|Azure Database for PostgreSQL 인스턴스|1| 2|
|Azure PostgreSQL 하이퍼스케일|1 + w(W = 작업자 수)|2 * (1 + W)|

아래 표에서는 샘플 배포에 필요한 총 영구 볼륨의 수를 보여 줍니다.

|리소스 종류|인스턴스 수|필요한 영구 볼륨 수|
|---|---|---|
|데이터 컨트롤러|1|4 * 2 = 8|
|Azure SQL Managed Instance|5|5 * 2 = 10|
|Azure Database for PostgreSQL 인스턴스|5| 5 * 2 = 10|
|Azure PostgreSQL 하이퍼스케일|2(작업자 수 = 인스턴스당 4)|2 * 2 * (1 + 4) = 20|
|***총 영구 볼륨의 수***||8 + 10 + 10 + 20 = 48|

이 계산은 스토리지 프로비저닝 프로그램 또는 환경에 따라 Kubernetes 클러스터에 대한 스토리지를 계획하는 데 사용할 수 있습니다. 예를 들어 로컬 스토리지 프로비저닝 프로그램이 5개 노드가 있는 Kubernetes 클러스터에 사용되는 경우 모든 노드 위의 샘플 배포에는 최소 10개의 영구 볼륨에 대한 스토리지가 필요합니다. 마찬가지로 5개의 노드가 있는 AKS(Azure Kubernetes Service) 클러스터를 프로비저닝하는 경우 10개의 데이터 디스크를 연결할 수 있도록 노드 풀에 적절 한 VM 크기를 선택하는 것이 중요합니다. AKS 노드의 스토리지 요구 사항에 맞게 노드 크기를 조정하는 방법에 대한 자세한 내용은 [여기](../../aks/operator-best-practices-storage.md#size-the-nodes-for-storage-needs)를 참조하세요.

## <a name="choosing-the-right-storage-class"></a>올바른 스토리지 클래스 선택

### <a name="on-premises-and-edge-sites"></a>온-프레미스 및 에지 사이트

Microsoft와 OEM, OS, Kubernetes 파트너는 Azure Arc 데이터 서비스에 대한 유효성 검사 프로그램을 제공합니다. 이 프로그램은 인증 테스트 도구 키트의 비교 가능한 테스트 결과를 고객에게 제공합니다. 테스트를 통해 기능 호환성, 스트레스 테스트 결과, 성능, 스케일링 성능을 평가할 수 있습니다. 각 테스트 결과는 사용된 OS, 사용된 Kubernetes 배포, 사용된 HW, 사용된 CSI 추가 기능, 사용된 스토리지 클래스를 표시합니다. 이를 통해 고객은 요구 사항에 가장 적합한 스토리지 클래스, OS, Kubernetes 배포, 하드웨어를 선택할 수 있습니다. 이 프로그램 및 테스트 결과에 대한 자세한 내용은 [여기](validation-program.md)에서 확인할 수 있습니다.

#### <a name="public-cloud-managed-kubernetes-services"></a>퍼블릭 클라우드, 관리되는 Kubernetes 서비스

퍼블릭 클라우드 기반의 관리되는 Kubernetes 서비스의 경우 다음과 같은 권장 사항을 적용할 수 있습니다.

|퍼블릭 클라우드 서비스|권장|
|---|---|
|**AKS(Azure Kubernetes Service)**|AKS(Azure Kubernetes Service)에는 Azure Files와 Azure Managed Disks의 두 가지 유형의 스토리지가 있습니다. 각 유형의 스토리지에는 표준(HDD)과 프리미엄(SSD)의 두 가지 가격 책정/성능 계층이 있습니다. 따라서 AKS에서 제공되는 네 개의 스토리지 클래스는 `azurefile`(Azure Files 표준 계층), `azurefile-premium`(Azure Files 프리미엄 계층), `default`(Azure Disks 표준 계층), `managed-premium`(Azure Disks 프리미엄 계층)입니다. 기본 스토리지 클래스는 `default`(Azure Disks 표준 계층)입니다. 의사 결정에 고려해야 하는 유형과 계층 간에는 상당한 **[가격 차이](https://azure.microsoft.com/pricing/details/storage/)** 가 있습니다. 고성능을 요구하는 프로덕션 워크로드의 경우 모든 스토리지 클래스에 `managed-premium`을 사용하는 것이 좋습니다. 비용을 고려해야 하는 개발/테스트 워크로드, 개념 증명 등의 경우 `azurefile`의 비용이 가장 저렴합니다. 네 가지 옵션은 모두 Azure에서 네트워크에 연결된 스토리지 디바이스이기 때문에 원격 공유 스토리지가 필요한 상황에 사용할 수 있습니다. [AKS 스토리지](../../aks/concepts-storage.md)에 대해 자세히 알아보세요.|
|**AWS EKS(Elastic Kubernetes Service)**| Amazon의 Elastic Kubernetes Service에는 [EBS CSI 스토리지 드라이버](https://docs.aws.amazon.com/eks/latest/userguide/ebs-csi.html)를 기반으로 하는 하나의 기본 스토리지 클래스가 있습니다. 프로덕션 워크로드에는 권장되지 않습니다. EKS 클러스터에 추가할 수 있는 새 스토리지 드라이버인 [EFS CSI 스토리지 드라이버](https://docs.aws.amazon.com/eks/latest/userguide/efs-csi.html)가 있지만 현재 베타 단계에 있으며 변경될 수 있습니다. AWS에서는 이 스토리지 드라이버가 프로덕션에 대해 지원된다고 하지만 아직 베타 버전이며 변경될 수 있으므로 사용하지 않는 것이 좋습니다. EBS 스토리지 클래스는 기본값이며 `gp2`라고 합니다. [EKS 스토리지](https://docs.aws.amazon.com/eks/latest/userguide/storage-classes.html)에 대해 자세히 알아보세요.|
|**GKE(Google Kubernetes Engine)**|GKE(Google Kubernetes Engine)에는 `standard`라는 단 하나의 스토리지 클래스가 있으며, [GCE 영구 디스크](https://kubernetes.io/docs/concepts/storage/volumes/#gcepersistentdisk)에 사용됩니다. 유일한 스토리지 클래스이기 때문에 기본값이기도 합니다. 직접 연결된 SSD와 함께 사용할 수 있는 GKE용 [로컬 정적 볼륨 프로비저닝 프로그램](https://cloud.google.com/kubernetes-engine/docs/how-to/persistent-volumes/local-ssd#run-local-volume-static-provisioner)이 있지만 Google에서 유지 관리하거나 지원하지 않기 때문에 사용하지 않는 것이 좋습니다. [GKE 스토리지](https://cloud.google.com/kubernetes-engine/docs/concepts/persistent-volumes)에 대해 자세히 알아보세요.
