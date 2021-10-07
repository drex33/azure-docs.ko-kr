---
title: Azure Arc 지원 데이터 서비스 유효성 검사
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 09/30/2021
ms.topic: conceptual
author: MikeRayMSFT
ms.author: mikeray
description: Azure Arc 지원 데이터 서비스에 대한 Kubernetes 배포에 대한 유효성 검사 프로그램을 설명합니다.
keywords: Kubernetes, Arc, Azure, K8s, 유효성 검사, 데이터 서비스, SQL Managed Instance
ms.openlocfilehash: 45c6967a536bc4e32e2382acd795f508a4bea0a4
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129615185"
---
# <a name="azure-arc-enabled-data-services-kubernetes-validation"></a>Azure Arc 지원 데이터 서비스 Kubernetes 유효성 검사

Azure Arc 지원 데이터 서비스 팀은 업계 파트너와 협력하여 Azure Arc 지원 데이터 서비스를 호스팅하는 특정 배포 및 솔루션의 유효성을 검사했습니다. 이 유효성 검사는 데이터 서비스에 대한 [Azure Arc 지원 Kubernetes 유효성 검사](../kubernetes/validation-program.md)를 확장합니다. 이 문서에서는 데이터 서비스를 지원하기 위해 확인된 파트너 솔루션, 버전, Kubernetes 버전, SQL 엔진 버전 및 PostgreSQL 하이퍼스케일 버전을 식별합니다. 

모든 Azure Arc 지원 구성 요소의 유효성이 검사되는 방법을 보려면 [유효성 검사 프로그램 개요](../validation-program/overview.md)를 참조하세요.

> [!NOTE]
> 현재 Azure Arc 지원 SQL Managed Instance는 일부 지역에서 일반 공급됩니다.
>
> Azure Arc 지원 PostgreSQL 하이퍼스케일은 일부 지역에서 미리 볼 수 있습니다.

## <a name="partners"></a>파트너

### <a name="cisco"></a>시스코

|솔루션 및 버전 | Kubernetes 버전 | Azure Arc 지원 데이터 서비스 버전 | SQL 엔진 버전 | PostgreSQL 하이퍼스케일 버전
|-----|-----|-----|-----|-----|
|VMware의 Cisco Hyperflex <br/> Cisco IKS ESXi 6.7 U3 |v1.19.5|v1.0.0_2021-07-30|15.0.2148.140| 확인 안 함 |

### <a name="dell"></a>Dell

|솔루션 및 버전 | Kubernetes 버전 | Azure Arc 지원 데이터 서비스 버전 | SQL 엔진 버전 | PostgreSQL 하이퍼스케일 버전
|-----|-----|-----|-----|-----|
| Dell EMC PowerFlex |1.19.7|v1.0.0_2021-07-30|15.0.2148.140 | 확인 안 함 |
| PowerFlex 버전 3.6 |1.19.7|v1.0.0_2021-07-30|15.0.2148.140 | 확인 안 함 |
| PowerFlex CSI 버전 1.4 |1.19.7|v1.0.0_2021-07-30|15.0.2148.140 | 확인 안 함 |
| PowerStore X|1.20.6|v1.0.0_2021-07-30|15.0.2148.140 |postgres 12.3(Ubuntu 12.3-1) |
| Powerstore T|1.20.6|v1.0.0_2021-07-30|15.0.2148.140 |postgres 12.3(Ubuntu 12.3-1)|

### <a name="nutanix"></a>Nutanix

|솔루션 및 버전 | Kubernetes 버전 | Azure Arc 지원 데이터 서비스 버전 | SQL 엔진 버전 | PostgreSQL 하이퍼스케일 버전
|-----|-----|-----|-----|-----|
| Karbon 2.2<br/>AOS: 5.19.1.5<br/>AHV:20201105.1021<br/>PC: 버전 pc.2021.3.02<br/> | 1.19.8-0 | v1.0.0_2021-07-30 | 15.0.2148.140|postgres 12.3(Ubuntu 12.3-1)|

### <a name="platform-9"></a>플랫폼 9

|솔루션 및 버전 | Kubernetes 버전 | Azure Arc 지원 데이터 서비스 버전 | SQL 엔진 버전 | PostgreSQL 하이퍼스케일 버전
|-----|-----|-----|-----|-----|
| Platform9 관리되는 Kubernetes v5.3.0 | 1.20.5 | v1.0.0_2021-07-30| 15.0.2148.140 | 확인 안 함 |

### <a name="purestorage"></a>PureStorage

|솔루션 및 버전 | Kubernetes 버전 | Azure Arc 지원 데이터 서비스 버전 | SQL 엔진 버전 | PostgreSQL 하이퍼스케일 버전
|-----|-----|-----|-----|-----|
| Portworx Enterprise 2.7 | 1.20.7 | v1.0.0_2021-07-30 | 15.0.2148.140 | 확인 안 함 |

### <a name="red-hat"></a>Red Hat

|솔루션 및 버전 | Kubernetes 버전 | Azure Arc 지원 데이터 서비스 버전 | SQL 엔진 버전 | PostgreSQL 하이퍼스케일 버전
|-----|-----|-----|-----|-----|
| OpenShift 7.13 | 1.20.0 | v1.0.0_2021-07-30 | 15.0.2148.140 | postgres 12.3(Ubuntu 12.3-1)|

### <a name="vmware"></a>VMware

|솔루션 및 버전 | Kubernetes 버전 | Azure Arc 지원 데이터 서비스 버전 | SQL 엔진 버전 | PostgreSQL 하이퍼스케일 버전
|-----|-----|-----|-----|-----|
| TKGm v1.3.1 | 1.20.5 | v1.0.0_2021-07-30 | 15.0.2148.140|postgres 12.3(Ubuntu 12.3-1)|

## <a name="data-services-validation-process"></a>데이터 서비스 유효성 검사 프로세스

Sonobuoy Azure Arc 지원 데이터 서비스 플러그 인은 Kubernetes 클러스터에서 Azure Arc 지원 데이터 서비스의 프로비전 및 테스트를 자동화합니다.

### <a name="prerequisites"></a>필수 구성 요소

다음과 같은 도구를 설치합니다. 

- [Azure Data CLI(`azdata`)](/sql/azdata/install/deploy-install-azdata)
- [kubectl](https://kubernetes.io/docs/home/)
- [Azure Data Studio - 참가자 빌드](https://github.com/microsoft/azuredatastudio)

대상 Kubernetes 클러스터에 액세스하고 현재 컨텍스트로 설정하도록 구성된 Kubernetes 구성 파일을 작성합니다. 이 파일을 생성하고 컴퓨터에 로컬로 가져오는 방법은 플랫폼과 플랫폼에 따라 다릅니다. [Kubernetes.io](https://kubernetes.io/docs/home/)참조하세요.

### <a name="process"></a>프로세스

적합성 테스트는 Azure Arc 지원 데이터 서비스 유효성 검사의 일부로 실행됩니다. 이러한 테스트를 실행하기 위한 필수 조건은 사용 중인 Kubernetes 배포에 대한 Azure Arc 지원 Kubernetes 테스트를 통과하는 것입니다.

이 테스트는 제품이 데이터 서비스 실행 및 운영 요구 사항을 준수하는지 확인합니다. 이 프로세스는 제품이 엔터프라이즈 배포 준비가 되었는지 평가하는 데 도움이 됩니다.

데이터 서비스에 대한 테스트는 간접 연결 모드에서 다음을 다룹니다.

1. 간접 모드에서 데이터 컨트롤러 배포
2. [Azure Arc 지원 SQL Managed Instance](create-sql-managed-instance.md) 배포
3. [Azure Arc 지원 PostgreSQL 하이퍼스케일](create-postgresql-hyperscale-server-group.md) 배포
4. [PostgreSQL 하이퍼스케일](scale-out-in-postgresql-hyperscale-server-group.md) 스케일 아웃

Azure Arc 지원 데이터 서비스의 향후 릴리스에는 더 많은 테스트가 추가될 예정입니다.

## <a name="additional-information"></a>추가 정보

- [유효성 검사 프로그램 개요](../validation-program/overview.md)
- [Azure Arc 지원 Kubernetes 유효성 검사](../kubernetes/validation-program.md)
- [Azure Arc 유효성 검사 프로그램 - GitHub 프로젝트](https://github.com/Azure/azure-arc-validation/)

## <a name="next-steps"></a>다음 단계

[데이터 컨트롤러 만들기](create-data-controller.md)
