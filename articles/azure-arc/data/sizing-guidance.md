---
title: 크기 조정 지침
description: Azure Arc 지원 데이터 서비스 배포 크기 관련 계획.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 6ac2397823dffd2c31cbe534c8fea1886e84558d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566625"
---
# <a name="sizing-guidance"></a>크기 조정 지침


## <a name="overview-of-sizing-guidance"></a>크기 조정 지침 개요

Azure Arc 데이터 서비스 배포를 계획할 때 Azure Arc 데이터 컨트롤러를 실행하는 데 필요한 컴퓨팅, 메모리, 스토리지의 올바른 크기와 배포할 SQL 관리형 인스턴스 및 PostgreSQL 하이퍼스케일 서버 그룹의 수를 계획해야 합니다.  Azure Arc 지원 데이터 서비스는 Kubernetes에 배포되므로 시간이 지남에 따라 추가 컴퓨팅 노드나 스토리지를 추가하여 Kubernetes 클러스터에 용량을 더 추가할 수 있습니다.  이 가이드는 최소 요구 사항에 관한 참고 자료를 제공하고 몇 가지 일반적인 요구 사항에 권장되는 크기에 관한 지침을 제공합니다.

## <a name="general-sizing-requirements"></a>일반 크기 조정 요구 사항

> [!NOTE]
> 이 문서의 개념이 익숙하지 않으실 경우 [Kubernetes 리소스 거버넌스](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/) 및 [Kubernetes 크기 표기법](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#resource-units-in-kubernetes)에 관해 자세히 알아볼 수 있습니다.

코어 수는 1보다 크거나 같은 정수 값이어야 합니다.

배포에 Azure CLI(az)를 사용하는 경우 메모리 값은 두 숫자의 거듭제곱으로 지정해야 합니다. 즉, 접미사: Ki, Mi 또는 Gi를 사용합니다.

제한 값은 지정된 경우 항상 요청 값보다 커야 합니다.

코어 제한 값은 SQL 관리형 인스턴스 및 PostgreSQL 하이퍼스케일 서버 그룹에 관한 청구 가능 메트릭입니다.

## <a name="minimum-deployment-requirements"></a>최소 배포 요구 사항

최소 크기의 Azure Arc 지원 데이터 서비스 배포는 Azure Arc 데이터 컨트롤러와 하나의 SQL Managed Instance 및 두 개의 작업자 노드를 포함하는 하나의 PostgreSQL 하이퍼스케일 서버 그룹으로 간주할 수 있습니다.  해당 구성에서는 Kubernetes 클러스터에서 _사용 가능한_ 16GB의 RAM 및 코어 4개 이상의 용량이 필요합니다.  최소한 Kubernetes 노드 크기가 8GB RAM 및 코어 4개이고, 모든 Kubernetes 노드에서 총 16GB RAM을 사용할 수 있어야 합니다.  예를 들어 32GB RAM 및 4개 코어에 1개의 노트를 가질 수 있으며, 16GB RAM 및 4개 코어에 2개의 노드를 가질 수 있습니다.

스토리지 크기 조정에 관한 자세한 내용은 [스토리지 구성](storage-configuration.md) 문서를 참조하세요.

## <a name="data-controller-sizing-details"></a>데이터 컨트롤러 크기 조정 세부 정보

데이터 컨트롤러는 API, 컨트롤러 서비스, 부트스트래퍼, 모니터링 데이터베이스, 대시보드를 제공하기 위해 Kubernetes 클러스터에 배포되는 Pod의 컬렉션입니다.  다음 표에서는 메모리, CPU 요청, 한도의 기본값을 설명합니다.

|Pod 이름|CPU 요청|메모리 요청|CPU 제한|메모리 제한|참고|
|---|---|---|---|---|---|
|**부트스트래퍼**|100m|100Mi|200m|200Mi||
|**control**|400m|2Gi|1800m|2Gi||
|**controldb**|200m|3Gi|800m|6Gi||
|**logsdb**|200m|1600Mi|2|1600Mi||
|**logsui**|100m|500Mi|2|2Gi||
|**metricsdb**|200m|800Mi|400m|2Gi||
|**metricsdc**|100m|200Mi|200m|300Mi|Metricsdc는 클러스터의 각 Kubernetes 노드에 생성되는 DaemonSet입니다.  여기에 있는 표의 숫자는 _노드당_ 입니다. 데이터 컨트롤러를 만들기 전에 배포 프로필 파일에서 allowNodeMetricsCollection = false를 설정하는 경우 metricsdc daemonset가 생성되지 않습니다.|
|**metricsui**|20m|200Mi|500m|200Mi||

배포 프로필 파일 또는 datacontroller YAML 파일에서 controldb 및 컨트롤 Pod의 기본 설정을 재정의할 수 있습니다.  예:

```yaml
  resources:
    controller:
      limits:
        cpu: "1000m"
        memory: "3Gi"
      requests:
        cpu: "800m"
        memory: "2Gi"
    controllerDb:
      limits:
        cpu: "800m"
        memory: "8Gi"
      requests:
        cpu: "200m"
        memory: "4Gi"
```

스토리지 크기 조정에 관한 자세한 내용은 [스토리지 구성](storage-configuration.md) 문서를 참조하세요.

## <a name="sql-managed-instance-sizing-details"></a>SQL 관리형 인스턴스 크기 조정 정보

각 SQL Managed Instance에는 다음과 같은 최소 리소스 요청 및 제한이 있어야 합니다.

|서비스 계층|범용|중요 비즈니스용(미리 보기)|
|---|---|---|
|CPU 요청|최소: 1; 최대: 24; 기본값: 2|최소: 1; 최대: 무제한; 기본값: 4|
|CPU 제한|최소: 1; 최대: 24; 기본값: 2|최소: 1; 최대: 무제한; 기본값: 4|
|메모리 요청|최소: 2Gi; 최대: 128Gi; 기본값: 4Gi|최소: 2Gi; 최대: 무제한; 기본값: 4Gi|
|메모리 제한|최소: 2Gi; 최대: 128Gi; 기본값: 4Gi|최소: 2Gi; 최대: 무제한; 기본값: 4Gi|

생성된 각 SQL 관리형 인스턴스 Pod에는 세 개의 컨테이너가 있습니다.

|컨테이너 이름|CPU 요청|메모리 요청|CPU 제한|메모리 제한|참고|
|---|---|---|---|---|---|
|fluentbit|100m|100Mi|지정되지 않음|지정되지 않음|Fluentbit 컨테이너 리소스 요청은 SQL 관리형 인스턴스에 관해 지정된 요청에 _추가_ 됩니다.|
|arc-sqlmi|사용자가 지정되었거나 지정되지 않았습니다.|사용자가 지정되었거나 지정되지 않았습니다.|사용자가 지정되었거나 지정되지 않았습니다.|사용자가 지정되었거나 지정되지 않았습니다.|
|collectd|지정되지 않음|지정되지 않음|지정되지 않음|지정되지 않음|

모든 영구 볼륨의 기본 볼륨 크기는 5Gi입니다.

## <a name="postgresql-hyperscale-server-group-sizing-details"></a>PostgreSQL 하이퍼스케일 서버 그룹 크기 조정 세부 정보

각 PostgreSQL 하이퍼스케일 서버 그룹 노드에는 다음과 같은 최소 리소스 요청이 있어야 합니다.
- 메모리: 256Mi
- 코어: 1개

생성된 각 PostgreSQL 하이퍼스케일 서버 그룹 코디네이터 또는 작업자 Pod에는 세 개의 컨테이너가 있습니다.

|컨테이너 이름|CPU 요청|메모리 요청|CPU 제한|메모리 제한|참고|
|---|---|---|---|---|---|
|fluentbit|100m|100Mi|지정되지 않음|지정되지 않음|Fluentbit 컨테이너 리소스 요청은 PostgreSQL 하이퍼스케일 서버 그룹 노드에 관해 지정된 요청에 _추가_ 됩니다.|
|postgres|사용자가 지정되었거나 지정되지 않았습니다.|사용자 지정되었거나 256Mi(기본값).|사용자가 지정되었거나 지정되지 않았습니다.|사용자가 지정되었거나 지정되지 않았습니다.||
|telegraf|지정되지 않음|지정되지 않음|지정되지 않음|지정되지 않음||

## <a name="cumulative-sizing"></a>누적 크기 조정

Azure Arc 지원 데이터 서비스에 필요한 환경의 전체 크기는 주로 생성되는 데이터베이스 인스턴스의 수와 크기에 관한 함수입니다.  각 데이터베이스 인스턴스에 필요한 리소스의 양이 증가 및 축소하고, 각 인스턴스 수가 변경된다는 사실을 알고 전체 크기를 예측하기가 어려울 수 있습니다.

지정된 Azure Arc 지원 데이터 서비스 환경의 기준 크기는 4개 코어와 16GB RAM이 필요한 데이터 컨트롤러의 크기입니다.  여기에서 데이터베이스 인스턴스에 필요한 코어 및 메모리의 누적 합계를 추가할 수 있습니다.  SQL 관리형 인스턴스의 Pod 수는 생성된 SQL 관리형 인스턴스의 수와 같습니다.  PostgreSQL 하이퍼스케일 서버 그룹의 경우 Pod 수는 작업자 노드 수에 코디네이터 노드를 위한 포드 하나를 더한 값과 같습니다.  예를 들어 작업자 노드가 3개인 PostgreSQL 서버 그룹이 있는 경우 총 Pod 수는 4입니다.

각 데이터베이스 인스턴스에 관해 요청하는 코어 및 메모리 외에도 에이전트 컨테이너용으로 250M의 RAM 및 250m의 코어를 추가해야 합니다.

다음은 크기 조정 계산의 예제입니다.

요구 사항:

- **"SQL1"** : 1개의 SQL 관리형 인스턴스(16GB RAM, 4개 코어)
- **"SQL2"** : 1개의 SQL 관리형 인스턴스(256GB RAM, 16개 코어)
- **"Postgres1"** : 1 PostgreSQL 하이퍼스케일 서버 그룹(4명의 작업자, 12GB RAM, 4개 코어)

크기 조정 계산:

- Pod 당 에이전트 = 16.25Gi RAM, 4.25개 코어이므로 "SQL1"의 크기는 Pod 1개*([16Gi RAM, 4개 코어] + [250Mi RAM, 250m 코어])입니다.
- Pod 당 에이전트 = 256.25Gi RAM, 16.25개 코어이므로 "SQL2"의 크기는 Pod 1개*([256Gi RAM, 16개 코어] + [250Mi RAM, 250m 코어])입니다.
- SQL 1 및 SQL 2의 총 크기는 (16.25GB + 256.25Gi) = 272.5GB RAM, (4.25개 코어 + 16.25개 코어) = 20.5개 코어입니다.

- Pod 당 에이전트 = 61.25GB RAM, 21.25개 코어이므로 "Postgres1"의 크기는 (4개의 작업자 Pod + 1 코디네이터 Pod) * ([12GB RAM, 4개 코어] + [250Mi RAM, 250m 코어])입니다.

- 데이터베이스 인스턴스에 필요한 총 용량은 SQL용 272.5GB RAM, 20.5개 코어 + PostgreSQL 하이퍼스케일용 61.25GB RAM, 21.25개 코어 = 333.75GB RAM, 42.5개 코어입니다.

- 데이터베이스 인스턴스와 데이터 컨트롤러에 필요한 총 용량은 데이터베이스 인스턴스용 333.75GB RAM, 42.5개 코어 + 데이터 컨트롤러용 16GB RAM, 4개 코어 = 349.75GB RAM, 46.5개 코어입니다.

스토리지 크기 조정에 관한 자세한 내용은 [스토리지 구성](storage-configuration.md) 문서를 참조하세요.

## <a name="other-considerations"></a>기타 고려 사항

코어 또는 RAM용 지정된 데이터베이스 인스턴스 크기 요청은 클러스터에서 사용 가능한 Kubernetes 노드의 용량을 초과할 수 없습니다.  예를 들어 Kubernetes 클러스터에 있는 가장 큰 Kubernetes 노드가 256GB의 RAM과 24개 코어인 경우 512GB의 RAM 및 48개 코어 요청을 사용하여 데이터베이스 인스턴스를 만들 수 없습니다.  

Kubernetes에서 Pod 생성을 효율적으로 예약하고, 탄력적으로 스케일링하고, Kubernetes 노드의 롤링 업그레이드를 허용하고, 필요에 따라 장기적으로 늘릴 수 있도록 Kubernetes 노드에서 사용 가능한 용량의 25% 이상을 유지하는 것이 좋습니다.

크기 조정 계산에서 Kubernetes 시스템 Pod의 리소스 요구 사항 및 동일한 Kubernetes 클러스터에서 Azure Arc 지원 데이터 서비스와 용량을 공유할 수 있는 다른 워크로드를 반드시 추가해야 합니다.

계획된 유지 관리 및 재해 발생 중 고가용성을 유지하기 위해 지정된 시점에 사용할 수 없는 클러스터의 Kubernetes 노드 중 하나 이상을 계획해야 합니다.  Kubernetes는 유지 관리를 위해 또는 실패로 인해 지정된 노드에서 실행 중이던 Pod의 일정을 다시 조정하려고 합니다.  남은 노드에 사용 가능한 용량이 없는 경우 해당 Pod는 사용 가능한 용량이 다시 생길 때까지는 다시 생성 예약이 되지 않습니다.  대형 데이터베이스 인스턴스에 특별히 주의해야 합니다.  예를 들어 큰 데이터베이스 인스턴스의 리소스 요구 사항을 충족하기에 충분할 만큼 Kubernetes 노드가 크고 해당 노드가 실패하는 경우 Kubernetes는 데이터베이스 인스턴스 Pod를 다른 Kubernetes 노드로 예약할 수 없습니다.

[Kubernetes 클러스터 크기의 최대 제한](https://kubernetes.io/docs/setup/best-practices/cluster-large/)을 염두에 두세요.

Kubernetes 관리자가 네임스페이스/프로젝트에서 [리소스 할당량](https://kubernetes.io/docs/concepts/policy/resource-quotas/)을 설정했을 수 있습니다.  데이터베이스 인스턴스 크기를 계획할 때는 해당 사항을 염두에 두어야 합니다.
