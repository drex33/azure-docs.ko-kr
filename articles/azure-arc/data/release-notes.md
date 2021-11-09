---
title: Azure Arc 지원 데이터 서비스 - 릴리스 정보
description: 최신 릴리스 정보
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 11/03/2021
ms.topic: conceptual
ms.custom: references_regions
ms.openlocfilehash: ac5b33de1a95b413c7eba92d2bdeaa8cc05b3a05
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132059567"
---
# <a name="release-notes---azure-arc-enabled-data-services"></a>릴리스 정보 - Azure Arc 지원 데이터 서비스

이 문서에서는 Azure Arc 지원 데이터 서비스의 기능, 특징, 최근에 릴리스된 기능 향상 및 개선된 기능을 중점적으로 설명합니다.

## <a name="november-2021"></a>2021년 11월

이 릴리스는 2021 년 11 월 3 일에 게시 됩니다.

#### <a name="tools"></a>도구

##### <a name="azure-data-studio"></a>Azure Data Studio

[Azure Data Studio에 대 한](/sql/azure-data-studio/extensions/azure-arc-extension)최신 버전의 Arc 확장을 설치 하거나 업데이트 합니다.

##### <a name="azure-az-cli"></a>Azure ( `az` ) CLI

`arcdata`CLI 용 확장을 설치 하거나 업데이트 `az` 하 여 직접 연결 된 배포를 지원 합니다.

다음 `sql` 명령은 이제 직접 연결 모드를 지원 합니다.

   ```console
   az arcdata dc create
   az arcdata dc delete
   az arcdata sql mi-arc create
   az arcdata sql mi-arc delete
   ```
 
#### <a name="data-controller"></a>데이터 컨트롤러

- 직접 연결 모드 일반적으로 사용 가능
- Azure arc에서 직접 연결 된 azure arc 데이터 컨트롤러 확장 Azure Arc 사용 Kubernetes 클러스터는 이제 서비스 사용자 이름 대신 시스템 생성 관리 되는 id를 사용 합니다. 관리 id는 새 Azure Arc 데이터 컨트롤러 확장을 만들 때 자동으로 생성 됩니다. 사용 및 메트릭을 업로드할 수 있는 적절 한 권한을 부여 해야 합니다.
- 메트릭 업로드는 직접 연결 된 Azure Arc 데이터 컨트롤러를 사용 하 여 시스템 생성 관리 id를 활용 합니다. 
- Azure CLI ()에서 직접 연결 된 모드 Azure Arc 데이터 컨트롤러를 만듭니다 `az` .
- Azure Monitor에 자동으로 메트릭 업로드
- Azure Log Analytics에 자동으로 로그 업로드
- Azure Arc 데이터 컨트롤러를 배포한 후 메트릭 및/또는 로그의 자동 업로드를 사용 하거나 사용 하지 않도록 설정 합니다.
- Azure CLI를 사용 하 여 7 월 2021 릴리스 (일반적으로 사용 가능한 서비스 (예: Azure Arc data controller) 및 범용 SQL Managed Instance)로 업그레이드 합니다.
- 새 환경 변수를 사용 하 여 메트릭 및 로그 대시보드 사용자 이름 및 암호를 DC 배포 시간에 별도로 설정 합니다.

   ```console
   AZDATA_LOGSUI_USERNAME
   AZDATA_LOGSUI_PASSWORD
   AZDATA_METRICSUI_USERNAME
   AZDATA_METRICSUI_PASSWORD
   ```
- 새 명령- `az arcdata dc list-upgrades` 현재 배포 된 데이터 컨트롤러에서 사용할 수 있는 업그레이드 목록을 표시 합니다.


`AZDATA_USERNAME`이전 처럼 및 환경 변수를 계속 사용할 수 있습니다 `AZDATA_PASSWORD` . 만 제공 하 `AZDATA_USERNAME` 는 경우 `AZDATA_PASSWORD` 배포는 로그 및 메트릭 대시보드 모두에 대해이를 사용 합니다.

##### <a name="region-availability"></a>지역 가용성

이 릴리스에는 다음과 같은 Azure 지역에서 직접 연결 된 모드 가용성이 도입 되었습니다.

- 미국 중북부
- 미국 서부
- 미국 서부 3

전체 목록은 [지원 되는 지역](overview.md#supported-regions)을 참조 하세요.

#### <a name="azure-arc-enabled-sql-managed-instance"></a>Azure Arc 지원 SQL Managed Instance

- Azure Arc 사용 SQL Managed Instance 일반 용도의 업그레이드 인스턴스
- SQL 이진 파일은 새 버전으로 업데이트 됩니다.
- Azure CLI를 사용 하 여 Azure Arc의 직접 연결 모드 배포 SQL Managed Instance 사용
- Azure Arc의 특정 시점 복원 사용 SQL Managed Instance는이 릴리스와 함께 일반 공급 됩니다. 현재 지정 시간 복원은 Managed Instance 범용 SQL만 지원 됩니다. 업무상 중요 SQL에 대 한 특정 시점 복원은 아직 미리 보기 상태 Managed Instance.
- `--dry-run`지정 시간 복원에 대해 제공 되는 새 옵션
- 복구 지점 목표는 기본적으로 5 분으로 설정 되며 구성할 수 없습니다.
- 백업 보존 기간은 기본적으로 7 일로 설정 됩니다. 보존 기간을 0으로 설정 하는 새 옵션은 백업이 필요 하지 않은 개발 및 테스트 인스턴스에 대해 자동 백업을 사용 하지 않도록 설정 합니다.
- 지정 시간 복원 작업이 구성 된 표준 시간대를 준수 하지 않는 문제를 해결 함 
- Azure CLI 또는 Azure Data Studio의 특정 시점으로 복원
 

### <a name="known-issues"></a>알려진 문제

#### <a name="data-controller-upgrade"></a>데이터 컨트롤러 업그레이드

- 현재 CLI 또는 포털을 통해 직접 연결 된 데이터 컨트롤러의 업그레이드는 지원 되지 않습니다.
- 현재 Azure Arc data controller 및 범용 SQL Managed Instance와 같은 일반적으로 사용 가능한 서비스만 업그레이드할 수 있습니다. 비즈니스에 중요 한 SQL Managed Instance 및/또는 Azure Arc PostgreSQL hyperscale을 사용 하는 경우 업그레이드를 계속 하기 전에 먼저 제거 합니다.

#### <a name="commands"></a>명령

다음 명령은이 시점에서 직접 연결 된 모드를 지원 하지 않습니다.

```console
az arcdata dc update
az arcdata sql mi-arc update
```

#### <a name="azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc 지원 PostgreSQL 하이퍼스케일

- Azure Arc 지원 PostgreSQL 하이퍼스케일 서버의 백업 및 복원은 현재 미리 보기 릴리스에서 지원되지 않습니다.

- `pg_cron` 확장을 동시에 사용하도록 설정하고 구성할 수는 없습니다. 이 경우 두 개의 명령을 사용해야 합니다. 사용하도록 설정하는 명령 하나와 구성하는 명령 하나를 사용해야 합니다. 예를 들면 다음과 같습니다.

   1. 확장 사용:

      ```console
      az postgres arc-server edit -n myservergroup --extensions pg_cron
      ```

   1. 서버 그룹을 다시 시작합니다.

   1. 확장 구성:

      ```console
      az postgres arc-server edit -n myservergroup --engine-settings cron.database_name='postgres'
      ```

   다시 시작이 완료되기 전에 두 번째 명령을 실행하면 명령이 실패합니다. 이 경우 잠시 기다린 후 두 번째 명령을 다시 실행하면 됩니다.

- 추가 확장을 사용하도록 서버 그룹의 구성을 편집할 때 `--extensions` 매개 변수에 잘못된 값을 전달하면 사용되는 확장 목록이 서버 그룹을 만든 시간의 상태로 다시 설정되며 사용자가 추가 확장을 만들 수 없습니다. 이 문제가 발생할 때 사용할 수 있는 유일한 해결 방법은 서버 그룹을 삭제하고 다시 배포하는 것입니다.

#### <a name="azure-arc-enabled-sql-managed-instance"></a>Azure Arc 지원 SQL Managed Instance

- pod를 다시 프로 비전 하면 SQL Managed Instance는 모든 데이터베이스에 대해 새로운 전체 백업 집합을 시작 합니다.
- 데이터 컨트롤러를 직접 연결 하는 경우에는 SQL Managed Instance를 프로 비전 하기 전에 먼저 데이터 컨트롤러를 최신 버전으로 업그레이드 해야 합니다. 데이터 컨트롤러의 imageversion SQL Managed Instance를 프로 비전 하는 `v1.0.0_2021-07-30` 작업이 실패 합니다.


##### <a name="other-limitations"></a>기타 제한 사항

- 트랜잭션 복제는 현재 지원되지 않습니다.
- 현재 로그 전달이 차단되었습니다.
- SQL Server 인증만 지원됩니다.

## <a name="july-2021"></a>2021년 7월

이 릴리스는 2021년 7월 30일에 게시되었습니다.

이 릴리스는 간접 연결 모드에서 Azure Arc 지원 SQL Managed Instance [범용 서비스 계층](service-tiers.md)의 일반 공급을 발표합니다.

   > [!NOTE]
   > 또한 이 릴리스는 미리 보기에서 다음과 같은 Azure Arc 지원 서비스를 제공합니다. 
   > - 직접 연결 모드의 SQL Managed Instance
   > - SQL Managed Instance [중요 비즈니스용 서비스 계층](service-tiers.md)
   > - PostgreSQL 하이퍼스케일

### <a name="breaking-changes"></a>주요 변경 내용

#### <a name="tools"></a>도구

다음 도구를 사용합니다.
- [Azure Data Studio의 참가자 빌드](https://github.com/microsoft/azuredatastudio#try-out-the-latest-insiders-build-from-main)
- [Azure (`az`) CLI용 `arcdata` 확장](install-arcdata-extension.md). 


#### <a name="data-controller"></a>데이터 컨트롤러

- `--azure-subscription`이라는 `az arcdata dc create` 매개 변수가 표준 `--subscription` 매개 변수를 사용하도록 변경되었습니다.
- AKS HCI에 배포하려면 특별한 스토리지 클래스 구성이 필요합니다. [스토리지 구성(AKS-HCI가 포함된 Azure Stack HCI)](create-data-controller-indirect-cli.md#configure-storage-azure-stack-hci-with-aks-hci)에서 자세한 내용을 참조하세요.
- 데이터를 내보낼 때 비 SSL 연결을 허용하는 새로운 요구 사항이 있습니다. 대화형 프롬프트를 표시하지 않도록 환경 변수를 설정합니다.

### <a name="whats-new"></a>새로운 기능

#### <a name="data-controller"></a>데이터 컨트롤러

- 직접 연결 모드는 미리 보기 중입니다. 

- 직접 연결 모드(미리 보기)는 이 릴리스의 다음 Azure 지역에서만 사용할 수 있습니다.

   - 미국 중 북부 *
   - 미국 중부
   - 미국 동부
   - 미국 동부 2
   - 미국 서부 *
   - 미국 서부 2
   - 미국 서부 3 *
   - 영국 남부
   - 서유럽
   - 북유럽
   - 오스트레일리아 동부
   - 동남아시아
   - 한국 중부
   - 프랑스 중부 \* 2021년 11월에 새로 추가되었습니다.

- 현재 Grafana 관리 환경을 사용하여 추가 기본 인증 사용자를 Grafana에 추가할 수 있습니다. Grafana .ini 파일을 수정하여 Grafana를 사용자 지정하는 것은 지원되지 않습니다.

- 현재 ElasticSearch 및 Kibana의 구성 수정은 Kibana 관리 환경을 통해 사용 가능한 경우에만 지원됩니다. 단일 사용자에 대한 기본 인증만 지원됩니다.
    
- Azure Portal의 사용자 지정 메트릭은 미리 보기 상태입니다.

- `az arcdata dc export` 명령을 사용하여 사용량/결제 정보, 메트릭 및 로그를 내보내려면 현재로서는 SSL 확인을 바이패스해야 합니다.  SSL 확인을 바이패스하라는 메시지가 표시되거나 메시지가 표시되지 않도록 `AZDATA_VERIFY_SSL=no` 환경 변수를 설정할 수 있습니다.  현재 데이터 컨트롤러 내보내기 API에 대한 SSL 인증서를 구성할 수 있는 방법이 없습니다.

#### <a name="azure-arc-enabled-sql-managed-instance"></a>Azure Arc 지원 SQL Managed Instance

- 자동 백업 및 특정 시점 복원이 미리 보기로 제공됩니다.
- Azure Arc 지원 SQL Managed Instance 있는 기존 데이터베이스에서 동일한 인스턴스 내의 새 데이터베이스로의 시점 복원을 지원합니다.
- 현재 날짜/시간이 UTC 형식의 특정 시점으로 주어지면 가장 최근의 유효한 복원 시간으로 해석되어 마지막 유효한 트랜잭션까지 지정된 데이터베이스를 복원합니다.
- 데이터베이스는 트랜잭션이 발생한 모든 시점으로 복원할 수 있습니다.
- Azure Arc 사용 가능한 SQL Managed Instance 대한 특정 복구 지점 목표를 설정하려면 SQL Managed Instance CRD를 편집하여 `recoveryPointObjectiveInSeconds` 속성을 설정합니다. 지원되는 값은 300~600입니다.
- 자동 백업을 사용하지 않도록 설정하려면 SQL 인스턴스 CRD를 편집하고 `recoveryPointObjectiveInSeconds` 속성을 0으로 설정합니다.

### <a name="known-issues"></a>알려진 문제

#### <a name="platform"></a>플랫폼

- Azure Portal 사용하여 직접 연결된 모드 클러스터에서 데이터 컨트롤러, SQL Managed Instance 또는 PostgreSQL 하이퍼스케일 서버 그룹을 만들 수 있습니다. 직접 연결 모드 배포는 다른 Azure Arc 지원 데이터 서비스 도구에서 지원되지 않습니다. 특히 이 릴리스 중에 다음 도구를 사용하여 직접 연결 모드에서 데이터 컨트롤러를 배포할 수 없습니다.
   - Azure Data Studio
   - Kubernetes 네이티브 도구(`kubectl`)
   - Azure CLI(`az`)용 `arcdata` 확장

   [Azure Portal에서 직접 연결 모드로 Azure Arc 데이터 컨트롤러 만들기](create-data-controller-direct-azure-portal.md)에서는 포털에서 데이터 컨트롤러를 만드는 방법을 설명합니다.

- `kubectl`을 사용하여 Kubernetes 클러스터에서 직접 리소스를 만들 수 있지만 직접 연결 모드를 사용하는 경우 Azure Portal에 반영되지 않습니다.

- 직접 연결 모드에서 `az arcdata dc upload`를 사용한 사용량, 메트릭 및 로그의 업로드는 설계상 차단됩니다. 사용량은 자동으로 업로드됩니다. 간접 연결 모드에서 만든 데이터 컨트롤러에 대한 업로드는 계속 작동합니다.
- 직접 연결 모드에서 사용량 현황 데이터의 자동 업로드는 `–proxy-cert <path-t-cert-file>`을 통해 프록시를 사용하는 경우 실패합니다.
- Azure Arc 지원 SQL Managed Instance 및 Azure Arc 지원 PostgreSQL 하이퍼스케일은 GB18030 인증되지 않았습니다.
- 현재 Kubernetes 클러스터당 하나의 Azure Arc 데이터 컨트롤러만 지원됩니다.

#### <a name="data-controller"></a>데이터 컨트롤러

- Azure Arc 데이터 컨트롤러가 Azure Portal 삭제되면 이 Arc 데이터 컨트롤러에 배포된 Azure Arc 사용 가능한 SQL Managed Instance가 있는 경우 삭제를 차단하기 위해 유효성 검사가 수행됩니다. 현재 이 유효성 검사는 Azure Arc 데이터 컨트롤러의 개요 페이지에서 삭제를 수행한 경우에만 적용됩니다. 

#### <a name="azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc 지원 PostgreSQL 하이퍼스케일

- 현재 PostgreSQL 하이퍼스케일은 Kubernetes 버전 1.22 이상에서 사용할 수 없습니다. 
- 백업 및 복원 작업은 7월 30일 릴리스에서 더 이상 작동하지 않습니다. 이것은 일시적인 제한 사항입니다. 백업 또는 복원이 필요한 경우 지금은 2021년 6월 릴리스를 사용합니다. 이 문제는 향후 릴리스에서 수정됩니다.

- `pg_cron` 확장을 동시에 사용하도록 설정하고 구성할 수는 없습니다. 이 경우 두 개의 명령을 사용해야 합니다. 사용하도록 설정하는 명령 하나와 구성하는 명령 하나를 사용해야 합니다. 예를 들면 다음과 같습니다.

   1. 확장 사용:

      ```console
      azdata postgres arc-server edit -n myservergroup --extensions pg_cron
      ```

   1. 서버 그룹을 다시 시작합니다.

   1. 확장 구성:

      ```console
      azdata postgres arc-server edit -n myservergroup --engine-settings cron.database_name='postgres'
      ```

   다시 시작이 완료되기 전에 두 번째 명령을 실행하면 명령이 실패합니다. 이 경우 잠시 기다린 후 두 번째 명령을 다시 실행하면 됩니다.

- 추가 확장을 사용하도록 서버 그룹의 구성을 편집할 때 `--extensions` 매개 변수에 잘못된 값을 전달하면 사용되는 확장 목록이 서버 그룹을 만든 시간의 상태로 다시 설정되며 사용자가 추가 확장을 만들 수 없습니다. 이 문제가 발생할 때 사용할 수 있는 유일한 해결 방법은 서버 그룹을 삭제하고 다시 배포하는 것입니다.

- 현재 NFS 스토리지에서는 특정 시점 복원이 지원되지 않습니다.

#### <a name="azure-arc-enabled-sql-managed-instance"></a>Azure Arc 지원 SQL Managed Instance

##### <a name="cant-see-resources-in-portal"></a>포털에서 리소스를 볼 수 없음

- 포털은 6월 릴리스에서 만들어진 Azure Arc 지원 SQL Managed Instance 리소스를 표시하지 않습니다. 리소스 그룹 목록 보기에서 SQL Managed Instance 리소스를 삭제합니다. 먼저 사용자 지정 위치 리소스를 삭제해야 할 수 있습니다.

##### <a name="point-in-time-restorepitr-supportability-and-limitations"></a>PITR(특정 시점 복원) 지원 가능성 및 제한 사항:
    
- 한 Azure Arc 지원 SQL Managed Instance 다른 Azure Arc 지원 SQL Managed Instance 복원을 지원하지 않습니다.  데이터베이스는 백업이 만들어진 동일한 Azure Arc 지원 SQL Managed Instance 복원할 수 있습니다.
- 특정 시점 복원을 위해 데이터베이스 이름 바꾸기는 현재 지원되지 않습니다.
- 현재 특정 시점 복원에 대해 허용된 기간 정보를 제공하는 CLI 명령 또는 API가 없습니다. 데이터베이스가 만들어진 시간과 타임스탬프가 유효한 경우 복원이 작동하므로 합리적인 기간 내에서 시간을 제공할 수 있습니다. 타임스탬프가 유효하지 않은 경우 허용된 기간이 오류 메시지를 통해 제공됩니다.
- TDE 사용 데이터베이스 복원을 지원하지 않습니다.
- 삭제된 데이터베이스는 현재 복원할 수 없습니다.

#####   <a name="automated-backups"></a>자동화된 백업

- 데이터베이스 이름을 바꾸면 이 데이터베이스에 대한 자동 백업이 중지됩니다.
- 보존이 적용되지 않습니다. 사용 가능한 공간이 있는 한 모든 백업을 보존합니다. 
- 단순 복구 모델을 사용하는 사용자 데이터베이스는 백업되지 않습니다.
- 시스템 데이터베이스 `model`은 데이터베이스 만들기/삭제 간섭을 방지하기 위해 백업되지 않습니다. 관리자 작업을 수행하면 DB가 잠깁니다. 
- 현재 `master` 및 `msdb` 시스템 데이터베이스만 백업됩니다. 12시간마다 전체 백업만 수행됩니다.
- `ONLINE` 사용자 데이터베이스만 백업됩니다.
- 기본 RPO(복구 지점 목표): 5분. 현재 릴리스에서는 수정할 수 없습니다.
- 백업은 무기한 보존됩니다. 공간을 복구하려면 백업을 수동으로 삭제합니다.

##### <a name="other-limitations"></a>기타 제한 사항
- 트랜잭션 복제는 현재 지원되지 않습니다.
- 현재 로그 전달이 차단되었습니다.
- SQL Server 인증만 지원됩니다.

## <a name="june-2021"></a>2021년 6월

이 미리 보기 릴리스는 2021년 7월 13일에 게시됩니다.

### <a name="breaking-changes"></a>주요 변경 내용

#### <a name="new-deployment-templates"></a>새 배포 템플릿

- Kubernetes 네이티브 배포 템플릿은 데이터 컨트롤러, 부트스트래퍼 & SQL Managed Instance 대해 수정되었습니다. .yaml 템플릿을 업데이트합니다. [샘플 yaml 파일](https://github.com/microsoft/azure_arc/tree/main/arc_data_services/deploy/yaml)

#### <a name="new-azure-cli-extension-for-data-controller-and-azure-arc-enabled-sql-managed-instance"></a>데이터 컨트롤러 및 Azure Arc 지원 SQL Managed Instance를 위한 새로운 Azure CLI 확장

이 릴리스에는 Azure CLI에 대한 `arcdata` 확장이 도입되었습니다. 확장을 추가하려면 다음 명령을 실행합니다.

```azurecli
az extension add --name arcdata
```

확장은 데이터 컨트롤러 및 SQL Managed Instance 및 PostgreSQL 하이퍼스케일 리소스와의 명령줄 상호 작용을 지원합니다.

데이터 컨트롤러에 대한 스크립트를 업데이트하려면 `azdata arc dc...`를 `az arcdata dc...`로 바꿉니다.

관리형 인스턴스에 대한 스크립트를 업데이트하려면 `azdata arc sql mi...`를 `az sql mi-arc...`로 바꿉니다.

Azure Arc 지원 PostgreSQL 하이퍼스케일의 경우 `azdata arc sql postgres...`를 `az postgres arc-server...`로 바꿉니다.

`azdata` 명령에 일반적으로 존재했던 매개 변수 외에도 `arcdata` Azure CLI 확장의 동일한 명령에는 이제 `--k8s-namespace` 및 `--use-k8s`와 같은 몇 가지 새로운 매개 변수가 필요합니다. `--use-k8s` 매개 변수는 명령을 Kubernetes API 또는 ARM API로 전송해야 하는 시기를 구별하는 데 사용됩니다. 지금은 Azure Arc 지원 데이터 서비스에 대한 모든 Azure CLI 명령은 Kubernetes API만 대상으로 합니다.

매개 변수 이름의 일부 짧은 형식(예: `--core-limit`를 `-cl`로)이 제거되거나 변경되었습니다. 새 매개 변수 짧은 이름 또는 긴 이름을 사용합니다.

`azdata arc dc export` 명령은 더 이상 작동하지 않습니다. 대신 `az arcdata dc export`를 사용하세요.

#### <a name="required-property-infrastructure"></a>필수 속성: `infrastructure`

`infrastructure` 속성은 데이터 컨트롤러를 배포할 때 새로운 필수 속성입니다. 이 속성 값을 지정하기 위해 yaml 파일, azdata/az 스크립트 및 ARM 템플릿을 조정합니다. 허용된 값은 `alibaba`, `aws`, `azure`, `gpc`, `onpremises`, `other`입니다.

#### <a name="kibana-login"></a>Kibana 로그인

OpenDistro 보안 팩이 제거되었습니다. 이제 일반 브라우저 사용자 이름/암호 프롬프트를 통해 Kibana에 로그인할 수 있습니다. 추가 인증/권한 부여 옵션을 구성하는 방법에 대한 자세한 내용은 나중에 제공됩니다.

#### <a name="crd-version-bump-to-v1beta1"></a>CRD 버전이 `v1beta1`로 변경

모든 CRD에는 이 릴리스의 버전이 `v1alpha1`에서 `v1beta1`로 변경되었습니다. 2021년 6월 릴리스 이전에 Azure Arc 지원 데이터 서비스의 버전을 배포한 경우 제거 프로세스의 일부로 모든 CRD를 삭제해야 합니다. 2021년 6월 릴리스와 함께 배포된 새 CRD의 버전은 v1beta1입니다.

#### <a name="azure-arc-enabled-sql-managed-instance"></a>Azure Arc 지원 SQL Managed Instance

자동 백업 서비스를 사용할 수 있으며 기본적으로 켜져 있습니다. 백업 볼륨의 공간 가용성을 면밀히 주시합니다.

### <a name="whats-new"></a>새로운 기능

이 릴리스에는 Azure Arc 지원 데이터 서비스를 위한 `az` CLI 확장이 도입되었습니다. 위의 [호환성이 손상되는 변경](#breaking-change) 정보를 참조하세요.

#### <a name="platform"></a>플랫폼

#### <a name="data-controller"></a>데이터 컨트롤러

- Azure Portal에서 직접 연결 모드로 데이터 컨트롤러를 배포하기 위한 간소화된 사용자 환경. Kubernetes 클러스터가 Azure Arc 사용하도록 설정되면 Arc 데이터 컨트롤러 만들기 마법사를 사용하여 포털에서 데이터 컨트롤러를 한 번에 완전히 배포할 수 있습니다. 또한 이 배포는 사용자 지정 위치 및 Azure Arc 지원 데이터 서비스 확장(부트스트래퍼)을 만듭니다. 사용자 지정 위치 및/또는 확장을 미리 만들고 이를 사용하도록 데이터 컨트롤러 배포를 구성할 수도 있습니다.
- 새 `Infrastructure` 속성은 Arc 데이터 컨트롤러를 배포할 때 필요한 속성입니다. 이 속성은 청구 목적으로 필요합니다. 자세한 내용은 일반 공급 시 제공될 예정입니다.
- Kubernetes 클러스터에서 배포 프로세스에 있는 리소스의 배포 상태를 더 잘 볼 수 있는 기능을 포함하여 Azure Portal의 데이터 컨트롤러 사용자 환경에서 다양한 사용 편의성이 향상되었습니다.
- 데이터 컨트롤러는 자동으로 로그(선택 사항)와 메트릭을 직접 연결 모드에서 Azure에 업로드합니다.
- 모니터링 스택(메트릭 및 로그 데이터베이스/대시보드)은 이제 자체 CRD(사용자 지정 리소스 정의)인 `monitors.arcdata.microsoft.com`에 패키지되었습니다. 이 사용자 지정 리소스를 만들면 모니터링 스택 Pod가 만들어집니다. 이를 삭제하면 모니터링 스택 Pod가 삭제됩니다. 데이터 컨트롤러를 만들면 모니터 사용자 지정 리소스가 자동으로 만들어집니다.
- 직접 연결 모드(미리 보기)에 대해 지원되는 새로운 지역: 미국 동부 2, 미국 서부 2, 미국 중남부, 영국 남부, 프랑스 중부, 동남아시아, 호주 동부.
- 이제 개요 블레이드의 사용자 지정 위치 리소스 차트에 배포된 Azure Arc 지원 데이터 서비스 리소스가 표시됩니다.
- 진단 및 솔루션이 데이터 컨트롤러용 Azure Portal에 추가되었습니다.
- 모든 Arc 관련 사용자 지정 리소스에 새 `Observed Generation` 속성을 추가했습니다.
- 이제 자격 증명 관리자 서비스가 포함되어 데이터 컨트롤러가 관리하는 모든 서비스에 대한 인증서 자동 배포를 처리합니다.

#### <a name="azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc 지원 PostgreSQL 하이퍼스케일

- Azure Arc PostgreSQL 하이퍼스케일은 이제 NFS 스토리지를 지원합니다.
- Azure Arc PostgreSQL 하이퍼스케일 배포는 이제 nodeSelector, nodeAffinity 및 anti-affinity를 사용하여 노드 할당 전략에 대한 Kubernetes Pod를 지원합니다.
- 이제 PostgreSQL 하이퍼스케일 서버 그룹을 배포할 때 또는 배포 후 Azure Data Studio 및 Azure Portal에서 역할(코디네이터 또는 작업자)별로 컴퓨팅 매개 변수(vCore 및 메모리)를 구성할 수 있습니다.
- 이제 Azure Portal에서 PostgreSQL 하이퍼스케일 서버 그룹에 만들어진 PostgreSQL 확장 목록을 볼 수 있습니다.
- Azure Portal Azure에 직접 연결된 데이터 컨트롤러에서 Azure Arc 사용하도록 설정된 PostgreSQL 하이퍼스케일 그룹을 삭제할 수 있습니다.


#### <a name="azure-arc-enabled-sql-managed-instance"></a>Azure Arc 지원 SQL Managed Instance

- 이제 자동 백업이 사용하도록 설정되었습니다.
- 이제 `sqlmanagedinstancerestoretasks.tasks.sql.arcdata.microsoft.com` CRD(사용자 지정 리소스 정의)를 기반으로 새 사용자 지정 리소스를 만들어 동일한 SQL 인스턴스에서 새 데이터베이스로 데이터베이스 백업을 복원할 수 있습니다. 자세한 내용은 설명서를 참조하십시오. 아직 데이터베이스 복원을 위한 명령줄 인터페이스(`azdata` 또는 `az`), Azure Portal 또는 Azure Data Studio 환경이 없습니다.
- 이 릴리스에 포함된 SQL 엔진 바이너리 버전은 Azure SQL Managed Instance(Azure의 PaaS)에 전역적으로 배포되는 최신 바이너리에 맞춰 조정됩니다. 이 정렬을 통해 Azure SQL Managed Instance PaaS와 Azure Arc 지원 Azure SQL Managed Instance 간에 백업/복원이 가능합니다. 호환성에 대한 자세한 내용은 나중에 제공됩니다.
- 이제 직접 연결 모드의 Azure Portal에서 Azure Arc SQL Managed Instances를 삭제할 수 있습니다.
- 이제 가격 책정 계층(`GeneralPurpose`, `BusinessCritical`), 라이선스 유형(`LicenseIncluded`, `BasePrice`(AHB 가격 책정에 사용) 및 `developer`를 포함하도록 SQL Managed Instance를 구성할 수 있습니다. 일반 공급 날짜(2021년 7월 30일로 예정된 대로 공개적으로 발표됨)와 서비스의 일반 공급 버전으로 업그레이드할 때까지 Azure Arc 지원 SQL Managed Instance를 사용하는 데 비용이 발생하지 않습니다.
- 이제 Azure Data Studio용 `arcdata` 확장에 SQL Managed Instances 배포 및 편집을 위해 구성할 수 있는 추가 매개 변수가 있습니다. 에이전트, 관리자 로그인 비밀, 주석, 레이블, 서비스 주석, 서비스 레이블, SSL/TLS 구성 설정, 데이터 정렬, 언어 및 추적 플래그를 사용하거나 사용하지 않습니다.
- 분산 가용성 그룹을 설정하기 위한 `azdata`/사용자 지정 리소스 작업의 새 명령. 이러한 명령은 미리 보기의 초기 단계에 있으며 문서가 곧 제공될 예정입니다.

   > [!NOTE]
   > 이 명령은 `az arcdata` 확장으로 마이그레이션됩니다.

- `azdata arc dc export`는 사용되지 않습니다. Azure CLI(`az`)용 `arcdata` 확장에서 `az arcdata dc export`로 대체됩니다. 다른 접근 방식을 사용하여 데이터를 내보냅니다. 더 이상 데이터 컨트롤러 API에 직접 연결하지 않습니다. 대신 `exporttasks.tasks.arcdata.microsoft.com` CRD(사용자 지정 리소스 정의)를 기반으로 내보내기 작업을 만듭니다. 만들어진 내보내기 작업 사용자 지정 리소스는 워크플로를 구동하여 다운로드 가능한 패키지를 만듭니다. Azure CLI는 이 작업이 완료될 때까지 기다린 다음 작업 사용자 지정 리소스 상태에서 보안 URL을 검색하여 패키지를 다운로드합니다.
- NFS 기반 스토리지 클래스 사용 지원.
- Arc SQL Managed Instance를 위해 Azure Portal에 진단 및 솔루션이 추가되었습니다.

## <a name="may-2021"></a>2021년 5월

이 미리 보기 릴리스는 2021년 6월 2일에 게시됩니다.

미리 보기 기능으로, 이 문서에 제시된 기술에는 [Microsoft Azure 미리 보기에 대한 보충 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)이 적용됩니다.

### <a name="breaking-change"></a>주요 변경 내용

- Kubernetes 네이티브 배포 템플릿이 수정되었습니다. .yml 템플릿을 업데이트합니다.
    - 데이터 컨트롤러, 부트스트래퍼 및 SQL 관리형 인스턴스의 업데이트된 템플릿: [GitHub microsoft/azure-arc pr 574](https://github.com/microsoft/azure_arc/pull/574)
    - PostgreSQL 하이퍼스케일의 업데이트된 템플릿: [GitHub microsoft/azure-arc pr 574](https://github.com/microsoft/azure_arc/pull/574)

### <a name="whats-new"></a>새로운 기능

#### <a name="platform"></a>플랫폼

- Azure Portal 데이터 컨트롤러, SQL Managed Instance 및 PostgreSQL 하이퍼스케일 서버 그룹을 만들고 삭제합니다.
- Azure Arc 데이터 서비스를 삭제할 때 포털 작업의 유효성을 검사합니다. 예를 들어 데이터 컨트롤러를 사용하여 배포된 SQL Managed Instance가 있는 경우 포털은 데이터 컨트롤러를 삭제하려고 할 때 경고를 표시합니다.
- Azure Portal 사용하여 Azure Arc 지원 데이터 컨트롤러를 배포할 때 사용자 지정 설정을 지원하는 사용자 지정 구성 프로필을 만듭니다.
- 필요에 따라 직접 연결된 모드에서 Azure Log Analytics 작업 영역에 로그를 자동으로 업로드합니다.

#### <a name="azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc 지원 PostgreSQL 하이퍼스케일

이 릴리스에는 다음 기능이 추가되었습니다.

- 데이터 컨트롤러가 직접 연결 모드로 구성된 경우 Azure Portal에서 Azure Arc PostgreSQL 하이퍼스케일을 삭제합니다.
- Azure Portal의 Postgres 배포 페이지에 대한 Azure 데이터베이스에서 Azure Arc 지원 PostgreSQL 하이퍼스케일을 배포합니다. [Azure Database for PostgreSQL 배포 옵션 선택 - Microsoft Azure](https://ms.portal.azure.com/#create/Microsoft.PostgreSQLServer)를 참조하세요.
- Azure Portal에서 Azure Arc 지원 PostgreSQL 하이퍼스케일을 배포할 때 스토리지 클래스 및 Postgres 확장을 지정합니다.
- Azure Arc 지원 PostgreSQL 하이퍼스케일의 작업자 노드 수를 줄입니다. `azdata` 명령줄에서 이 작업을 수행할 수 있습니다(작업자 노드 수를 늘릴 때 스케일 아웃과 반대로 스케일 인이라고 함).

#### <a name="azure-arc-enabled-sql-managed-instance"></a>Azure Arc 지원 SQL Managed Instance

- Azure Arc 지원 SQL Managed Instance 대한 새 Azure CLI [확장에는](/cli/azure/azure-cli-extensions-overview) 명령과 동일한 명령이 `az sql mi-arc <command>` 있습니다. 모든 Azure Arc 사용 SQL Managed Instance 명령은 에 `az sql mi-arc` 있습니다. 모든 Arc 관련 `azdata` 명령은 더 이상 사용되지 않으며 이후 릴리스에서 Azure CLI로 이동됩니다.

   확장을 추가하려면 다음을 수행합니다.

   ```azurecli
   az extension add --source https://azurearcdatacli.blob.core.windows.net/cli-extensions/arcdata-0.0.1-py2.py3-none-any.whl -y
   az sql mi-arc --help
   ```

- Transact-SQL을 사용하여 장애 조치(failover)를 수동으로 트리거합니다. 다음 명령을 순서대로 실행합니다.

   1. 주 복제본 엔드포인트 연결에서:

      ```sql
       ALTER AVAILABILITY GROUP current SET (ROLE = SECONDARY);
      ```

   1. 보조 복제본 엔드포인트 연결에서:

      ```sql
      ALTER AVAILABILITY GROUP current SET (ROLE = PRIMARY);
      ```

- `COPY_ONLY` 설정을 사용하지 않는 경우 Transact-SQL `BACKUP` 명령이 차단됩니다. 특정 시점 복원 기능을 지원합니다.

## <a name="april-2021"></a>2021년 4월

이 미리 보기 릴리스는 2021년 4월 29일에 게시됩니다.

### <a name="whats-new"></a>새로운 기능

이 섹션에서는 이 릴리스에 대해 도입되거나 사용되는 새로운 기능에 대해 설명합니다.

#### <a name="platform"></a>플랫폼

- 직접 연결된 클러스터는 원격 분석 정보를 자동으로 Azure에 업로드합니다.

####    <a name="azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc 지원 PostgreSQL 하이퍼스케일

- Azure Arc 지원 PostgreSQL 하이퍼스케일은 이제 직접 연결 모드에서 지원됩니다. 이제 Azure Portal의 Azure Market Place에서 Azure Arc 지원 PostgreSQL 하이퍼스케일을 배포할 수 있습니다.
- Azure Arc 지원 PostgreSQL 하이퍼스케일은 칼럼 형식 테이블 스토리지 기능을 제공하는 Citus 10.0 확장과 함께 제공됩니다.
- Azure Arc 지원 PostgreSQL 하이퍼스케일은 이제 전체 사용자/역할 관리를 지원합니다.
- Azure Arc 지원 PostgreSQL 하이퍼스케일은 이제 `Tdigest` 및 `pg_partman`을 사용하여 추가 확장을 지원합니다.
- 이제 Azure Arc 지원 PostgreSQL 하이퍼스케일에서 서버 그룹의 PostgreSQL 인스턴스 역할당 vCore 및 메모리 설정 구성을 지원합니다.
- 이제 Azure Arc 지원 PostgreSQL 하이퍼스케일에서 서버 그룹의 PostgreSQL 인스턴스 역할당 데이터베이스 엔진/서버 설정 구성을 지원합니다.

#### <a name="azure-arc-enabled-sql-managed-instance"></a>Azure Arc 지원 SQL Managed Instance

- 3개의 복제본을 사용하여 SQL Managed Instance로 데이터베이스를 복원하면 데이터베이스가 가용성 그룹에 자동으로 추가됩니다.
- 3개의 복제본과 함께 배포된 SQL Managed Instance에서 보조 읽기 전용 엔드포인트에 연결합니다. `azdata arc sql endpoint list`를 사용하여 보조 읽기 전용 연결 엔드포인트를 확인합니다.

## <a name="march-2021"></a>2021년 3월

2021년 3월 릴리스는 2021년 4월 5일에 처음 도입되었으며, 최종 릴리스 단계는 2021년 4월 9일에 완료되었습니다.

Azure Data CLI(`azdata`) 버전 번호: 20.3.2. [Azure Data CLI(`azdata`) 설치](/sql/azdata/install/deploy-install-azdata)에서 `azdata`를 설치할 수 있습니다.

### <a name="data-controller"></a>데이터 컨트롤러

- 포털의 직접 연결 모드에서 Azure Arc 지원 데이터 서비스 데이터 컨트롤러를 배포합니다. [데이터 컨트롤러 배포 - 직접 연결 모드 - 필수 구성 요소](create-data-controller-direct-prerequisites.md)에서 시작합니다.

### <a name="azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc 지원 PostgreSQL 하이퍼스케일

PostgreSQL에 대한 CRD(사용자 지정 리소스 정의)가 모두 단일 CRD로 통합되었습니다. 다음 표를 참조하세요.

|Release |CRD |
|-----|-----|
|2021년 2월 이전| postgresql-11s.arcdata.microsoft.com<br/>postgresql-12s.arcdata.microsoft.com |
|2021년 3월부터 | postgresqls.arcdata.microsoft.com

이전 설치를 정리할 때 이전 CRD를 삭제합니다. [이전 설치에서 정리](create-data-controller-using-kubernetes-native-tools.md#cleanup-from-past-installations)를 참조하세요.

### <a name="azure-arc-enabled-sql-managed-instance"></a>Azure Arc 지원 SQL Managed Instance

- 이제 직접 연결 모드의 Azure Portal SQL Managed Instance 만들 수 있습니다.

- 이제 3개의 복제본을 사용하여 SQL Managed Instance로 데이터베이스를 복원할 수 있습니다. 그러면 데이터베이스가 가용성 그룹에 자동으로 추가됩니다.

- 이제 3개의 복제본과 함께 배포된 SQL Managed Instance에서 보조 읽기 전용 엔드포인트에 연결할 수 있습니다. `azdata arc sql endpoint list`를 사용하여 보조 읽기 전용 연결 엔드포인트를 확인합니다.

## <a name="february-2021"></a>2021년 2월

### <a name="new-capabilities-and-features"></a>새로운 기능 및 특징

Azure Data CLI(`azdata`) 버전 번호: 20.3.1. [Azure Data CLI(`azdata`) 설치](/sql/azdata/install/deploy-install-azdata)에서 `azdata`를 설치할 수 있습니다.

추가 업데이트는 다음과 같습니다.

- Azure Arc 지원 SQL Managed Instance
   - Always On 가용성 그룹을 사용한 고가용성

- Azure Arc 지원 PostgreSQL 하이퍼스케일 Azure Data Studio:
   - 개요 페이지에 노드당 항목별로 작성된 서버 그룹의 상태 표시
   - 새 속성 페이지에 서버 그룹에 대한 자세한 정보 표시
   - **노드 매개 변수** 페이지에서 Postgres 엔진 매개 변수 구성

## <a name="january-2021"></a>2021년 1월

### <a name="new-capabilities-and-features"></a>새로운 기능 및 특징

Azure Data CLI(`azdata`) 버전 번호: 20.3.0. [Azure Data CLI(`azdata`) 설치](/sql/azdata/install/deploy-install-azdata)에서 `azdata`를 설치할 수 있습니다.

추가 업데이트는 다음과 같습니다.
- 17가지 새로운 언어에서 사용할 수 있도록 지역화된 포털
- Kube 네이티브 .yaml 파일의 사소한 변경
- Grafana 및 Kibana의 새 버전
- Azure Data Studio의 Notebook에서 azdata를 사용할 때 발생하는 Python 환경 문제가 해결됨
- pg_audit 확장을 PostgreSQL 하이퍼스케일에 사용할 수 있음
- PostgreSQL 하이퍼스케일 데이터베이스의 전체 복원을 수행할 때 백업 ID가 더 이상 필요하지 않음
- 서버 그룹을 구성하는 각 PostgreSQL 인스턴스에 대한 상태(성능 상태)가 보고됨

   이전 릴리스에서는 상태가 서버 그룹 수준에서 집계되었으며 PostgreSQL 노드 수준에서 항목별로 작성되지 않았습니다.

- PostgreSQL 배포가 create 명령에 표시된 볼륨 크기 매개 변수를 적용함
- 서버 그룹을 편집할 때 엔진 버전 매개 변수가 적용됨
- Azure Arc 지원 PostgreSQL 하이퍼스케일에 대한 Pod의 명명 규칙이 변경되었습니다.

    이제 `ServergroupName{c, w}-n` 형식으로 표시됩니다. 예를 들어 세 개의 노드(하나의 코디네이터 노드와 두 개의 작업자 노드)가 있는 서버 그룹은 다음과 같이 표시됩니다.
   - `Postgres01c-0`(코디네이터 노드)
   - `Postgres01w-0`(작업자 노드)
   - `Postgres01w-1`(작업자 노드)

## <a name="december-2020"></a>2020년 12월

### <a name="new-capabilities--features"></a>새로운 기능 및 특징

Azure Data CLI(`azdata`) 버전 번호: 20.2.5. [Azure Data CLI(`azdata`) 설치](/sql/azdata/install/deploy-install-azdata)에서 `azdata`를 설치할 수 있습니다.

`azdata arc sql endpoint list` 및 `azdata arc postgres endpoint list` 명령과 함께 Azure Data CLI(`azdata`)를 사용하여 SQL Managed Instance 및 PostgreSQL 하이퍼스케일의 엔드포인트를 확인합니다.

Azure Data Studio를 사용하여 SQL Managed Instance 리소스(CPU 코어 및 메모리) 요청 및 한도를 편집합니다.

Azure Arc 지원 PostgreSQL 하이퍼스케일은 PostgreSQL의 버전 11과 12에 대해 전체 백업 복원뿐만 아니라 특정 시점 복원을 지원합니다. 특정 시점 복원 기능을 사용하여 복원할 특정 날짜 및 시간을 나타낼 수 있습니다.

Azure Arc 지원 PostgreSQL 하이퍼스케일에 대한 Pod의 명명 규칙이 변경되었습니다. 이제 ServergroupName{r, s}-_n_ 형식으로 표시됩니다. 예를 들어 세 개의 노드(하나의 코디네이터 노드와 두 개의 작업자 노드)가 있는 서버 그룹은 다음과 같이 표시됩니다.
- `postgres02r-0`(코디네이터 노드)
- `postgres02s-0`(작업자 노드)
- `postgres02s-1`(작업자 노드)

### <a name="breaking-change"></a>주요 변경 내용

#### <a name="new-resource-provider"></a>새 리소스 공급자

이 릴리스에는 `Microsoft.AzureArcData`라는 업데이트된 [리소스 공급자](../../azure-resource-manager/management/azure-services-resource-providers.md)가 도입됩니다. 이 기능을 사용하려면 먼저 이 리소스 공급자를 등록해야 합니다.

리소스 공급자를 등록하려면 다음을 수행합니다.

1. Azure Portal에서 **구독** 을 선택합니다.
2. 구독 선택
3. **설정** 아래에서 **리소스 공급자** 를 선택합니다.
4. `Microsoft.AzureArcData`를 검색하고 **등록** 을 선택합니다.

[Azure 리소스 공급자 및 종류](../../azure-resource-manager/management/resource-providers-and-types.md)에서 자세한 단계를 검토할 수 있습니다. 이렇게 변경하면 Azure Portal에 업로드한 기존 Azure 리소스도 모두 제거됩니다. 리소스 공급자를 사용하려면 데이터 컨트롤러를 업데이트하고 최신 `azdata` CLI를 사용해야 합니다.

### <a name="platform-release-notes"></a>플랫폼 릴리스 정보

#### <a name="direct-connectivity-mode"></a>직접 연결 모드

이 릴리스에서는 직접 연결 모드가 도입되었습니다. 직접 연결 모드를 사용하면 데이터 컨트롤러에서 사용량 현황 정보를 Azure에 자동으로 업로드할 수 있습니다. 사용량 현황 업로드의 일부로 Arc 데이터 컨트롤러 리소스는 `azdata` 업로드를 통해 이미 만들어지지 않은 경우 포털에서 자동으로 만들어집니다.

데이터 컨트롤러를 만들 때 직접 연결을 지정할 수 있습니다. 다음 예제에서는 `az arcdata dc create`를 사용하여 직접 연결 모드(`connectivity-mode direct`)를 사용하는 `arc`라는 데이터 컨트롤러를 만듭니다. 예제를 실행하려면 먼저 `<subscription id>`를 구독 ID로 바꿉니다.

```azurecli
az arcdata dc create --profile-name azure-arc-aks-hci  --k8s-namespace <namespace> --use-k8s --name arc --subscription <subscription id> --resource-group my-resource-group --location eastus --connectivity-mode direct
```

## <a name="october-2020"></a>2020년 10월

Azure Data CLI(`azdata`) 버전 번호: 20.2.3. [Azure Data CLI(`azdata`) 설치](/sql/azdata/install/deploy-install-azdata)에서 `azdata`를 설치할 수 있습니다.

### <a name="breaking-changes"></a>주요 변경 내용

이 릴리스에는 다음과 같은 호환성이 손상되는 변경이 포함되었습니다.

* PostgreSQL CRD(사용자 지정 리소스 정의)에서 `shards`라는 용어의 이름이 `workers`로 바뀌었습니다. 이 용어(`workers`)는 명령줄 매개 변수 이름과 일치합니다.

* `azdata arc postgres server delete`는 postgres 인스턴스를 삭제하기 전에 확인 메시지를 표시합니다. 프롬프트를 건너뛰려면 `--force`를 사용하세요.

### <a name="additional-changes"></a>추가 변경 내용

* 새로운 선택적 매개 변수가 `--volume-claim mounts`라는 `azdata arc postgres server create`에 추가되었습니다. 값은 쉼표로 구분된 볼륨 클레임 탑재 목록입니다. 볼륨 클레임 탑재는 볼륨 유형 및 PVC 이름의 쌍입니다. 현재 지원되는 유일한 볼륨 유형은 `backup`입니다. PostgreSQL에서 볼륨 유형이 `backup`이면 PVC가 `/mnt/db-backups`에 탑재됩니다. 따라서 PostgresSQL 인스턴스 간에 백업을 공유하여 한 PostgresSQL 인스턴스의 백업을 다른 인스턴스에서 복원할 수 있습니다.

* PostgresSQL 사용자 지정 리소스 정의의 새로운 짧은 이름은 다음과 같습니다.
  * `pg11`
  * `pg12`
* 원격 분석 업로드에서는 다음 중 하나를 사용자에게 제공합니다.
   * Azure에 업로드된 지점 수 또는
   * Azure에 로드된 데이터가 없는 경우 다시 시도하라는 프롬프트
* 또한 `az arcdata dc debug copy-logs`는 `/var/opt/controller/log` 폴더에서 읽고 Linux에서 PostgreSQL 엔진 로그를 수집합니다.
*   PostgreSQL 하이퍼스케일을 사용하여 백업을 만들고 복원하는 동안 작업 표시기를 표시합니다.
* `azdata arc postrgres backup list`에 백업 크기 정보가 포함됩니다.
* SQL Managed Instance 관리자 이름 속성이 Azure Portal에서 개요 블레이드의 오른쪽 열에 추가되었습니다.
* Azure Data Studio는 PostgreSQL 하이퍼스케일에 대한 작업자 노드 수, vCore 및 메모리 설정 구성을 지원합니다.
* 미리 보기는 Postgres 버전 11 및 12의 백업/복원을 지원합니다.

## <a name="september-2020"></a>2020년 9월

Azure Arc 지원 데이터 서비스는 퍼블릭 미리 보기용으로 릴리스되었습니다. Azure Arc 사용 데이터 서비스를 사용 하면 어디서 나 데이터 서비스를 관리할 수 있습니다.

- SQL Managed Instance
- PostgreSQL 하이퍼스케일

해당 지침은 [What are Azure Arc 지원 데이터 서비스란?](overview.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

> **작업을 시도해보시겠습니까?**
> AKS, AWS EKS(Elastic Kubernetes Service), GKE(Google Cloud Kubernetes Engine) 또는 Azure VM에서 [Azure Arc 빠른 시작](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/)으로 빠르게 시작합니다.

- [클라이언트 도구 설치](install-client-tools.md)
- [Azure Arc 데이터 컨트롤러 만들기](create-data-controller.md)(먼저 클라이언트 도구를 설치해야 함)
- azure [arc에서 azure SQL Managed Instance 만들기](create-sql-managed-instance.md) (먼저 azure arc 데이터 컨트롤러를 만들어야 함)
- [Azure Arc에서 Azure Database for PostgreSQL 하이퍼스케일 서버 그룹 만들기](create-postgresql-hyperscale-server-group.md)(먼저 Azure Arc 데이터 컨트롤러를 만들어야 함)
- [Azure 서비스의 리소스 공급자](../../azure-resource-manager/management/azure-services-resource-providers.md)
