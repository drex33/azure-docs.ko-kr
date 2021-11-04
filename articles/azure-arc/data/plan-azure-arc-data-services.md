---
title: Azure Arc 지원 데이터 서비스 배포 계획
description: 이 문서에서는 Azure Arc 사용 데이터 서비스 배포 계획에 대 한 고려 사항을 설명 합니다.
services: azure-arc
ms.service: azure-arc-data
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: 15688b89b319b884707e9e6d8faa9a13de3233e0
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131563343"
---
# <a name="plan-an-azure-arc-enabled-data-services-deployment"></a>Azure Arc 지원 데이터 서비스 배포 계획

이 문서에서는 Azure Arc 지원 데이터 서비스 배포를 계획하는 방법을 설명합니다.

Azure Arc 사용 데이터 서비스 배포 계획을 시작할 때 데이터베이스 작업 및 해당 워크 로드에 대 한 비즈니스 요구 사항을 적절히 이해 하는 것이 중요 합니다. 예를 들어 작업의 메모리, CPU 및 저장소에 대 한 가용성, 비즈니스 연속성 및 용량 요구 사항을 고려해 야 합니다. 그리고 비즈니스 요구 사항에 따라 데이터베이스 워크 로드를 지원 하도록 인프라를 신중 하 게 준비 해야 합니다.

## <a name="prerequisites"></a>사전 요구 사항

시작 하기 전에 특정 필수 구성 요소를 충족 하 고 필요한 배경 및 정보를 준비 해야 합니다. 성공적인 배포를 위해 인프라 환경은 적절 한 수준의 액세스와 저장소, CPU 및 메모리에 적절 한 용량으로 적절 하 게 구성 되어야 합니다. 

다음 문서를 검토합니다.

- [크기 조정 지침](sizing-guidance.md)
- [스토리지 구성](storage-configuration.md)
- [연결 모드 및 해당 요구 사항](connectivity.md)

다음 사항을 확인합니다.

- [ `arcdata` CLI 확장이](install-arcdata-extension.md) 설치 되어 있습니다.
- 다른 [클라이언트 도구가](install-client-tools.md) 설치 되어 있습니다.
- Kubernetes 클러스터에 대 한 액세스 권한이 있습니다.
- *Kubeconfig* 파일이 구성 되었습니다. 배포 하려는 Kubernetes 클러스터를 가리켜야 합니다. 클러스터의 현재 컨텍스트를 확인 하려면 다음 명령을 실행 합니다.
   ```console
   kubectl cluster-info
   ```  
- azure arc 데이터 컨트롤러, azure arc 사용 SQL 관리 되는 인스턴스 또는 azure arc 사용 PostgreSQL hyperscale server와 같은 리소스가 프로젝션 되 고 청구 됩니다.

인프라를 준비한 후에는 다음과 같은 방법으로 Azure Arc 사용 데이터 서비스를 배포 합니다.
1. Kubernetes 클러스터의 유효성을 검사 한 배포 중 하나에 Azure Arc 사용 데이터 컨트롤러를 만듭니다.
1. Azure Arc 지원 SQL Managed Instance 및/또는 Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹을 만듭니다.

> [!CAUTION]
> 일부 데이터 서비스 계층 및 모드는 [GA (일반 공급)](release-notes.md)이며 일부는 미리 보기 상태입니다. 동일한 데이터 컨트롤러에서 GA와 preview 서비스를 혼합 하지 않는 것이 좋습니다. 동일한 데이터 컨트롤러에서 GA 및 preview 서비스를 혼합 하는 경우 현재 상태에서 업그레이드할 수 없습니다. 이 시나리오에서를 업그레이드 하려는 경우 데이터 컨트롤러 및 데이터 서비스를 제거 하 고 다시 만들어야 합니다.

## <a name="overview-create-an-azure-arc-enabled-data-controller"></a>개요: Azure Arc 사용 데이터 컨트롤러 만들기

다양 한 유형의 Kubernetes 클러스터에서 Azure Arc 사용 데이터 서비스를 만들 수 있으며 다양 한 방법으로 관리 되는 Kubernetes 서비스를 만들 수 있습니다.

현재 검증된 Kubernetes 서비스 및 배포 목록은 다음과 같습니다.

- Amazon 탄력적 Kubernetes 서비스 (Amazon EKS)
- AKS(Azure Kubernetes Service)
- Azure Stack HCI에서 Azure Kubernetes Service
- Azure Red Hat OpenShift
- GKE(Google Kubernetes Engine)
- 오픈 소스, 업스트림 Kubernetes (일반적으로 kubeadm를 사용 하 여 배포 됨)
- OCP(OpenShift 컨테이너 플랫폼)

> [!IMPORTANT]
> * 지원되는 최소 Kubernetes 버전은 v1.19입니다. 자세한 내용은 [릴리스 정보 &nbsp; -Azure Arc 사용 데이터 서비스](./release-notes.md#known-issues)의 "알려진 문제" 섹션을 참조 하세요.
> * OCP는 최소 4.7 버전부터 지원됩니다.
> * Azure Kubernetes Service를 사용 하는 경우 클러스터의 작업자 노드 VM (가상 머신) 크기는 Standard_D8s_v3 이상 이어야 하 고 Premium 디스크를 사용 해야 합니다. 
> * 클러스터는 여러 가용성 영역에 걸쳐 있지 않아야 합니다. 
> * 자세한 내용은 [릴리스 정보 &nbsp; -Azure Arc 사용 데이터 서비스](./release-notes.md#known-issues)의 "알려진 문제" 섹션을 참조 하세요.

선택한 서비스 또는 배포 옵션에 관계 없이 Azure Arc 사용 데이터 서비스를 만들 때 다음 정보를 제공 해야 합니다.

- **데이터 컨트롤러 이름**: 데이터 컨트롤러에 대 한 설명이 포함 된 이름입니다 (예: *프로덕션-dc* 또는 *시애틀 dc*). 이름은 [Kubernetes 명명 표준](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/)을 충족해야 합니다.

- **Username**: Kibana/Grafana administrator 사용자의 사용자 이름입니다.
- **암호**: Kibana/Grafana 관리자 사용자에 대 한 암호입니다.
- **Kubernetes 네임 스페이스의 이름**: 데이터 컨트롤러를 만들려는 Kubernetes 네임 스페이스의 이름입니다.
- **연결 모드**: azure Arc 사용 데이터 서비스 환경에서 azure로의 연결 수준을 결정 합니다. *간접* 연결 모드는 일반적으로 사용할 수 있으며 *직접* 연결 모드는 미리 보기 상태입니다. 선택한 연결 모드에 따라 배포 방법에 대 한 옵션이 결정 됩니다. 자세한 내용은 [연결 모드 및 요구 사항](./connectivity.md)을 참조 하세요.
- **Azure 구독 ID**: azure에서 데이터 컨트롤러 리소스를 만들려는 AZURE 구독 GUID입니다. 모든 Azure Arc 사용 SQL 관리 되는 인스턴스 및 Azure Database for PostgreSQL 하이퍼 확장 서버 그룹도에서 만들어지고이 구독으로 청구 됩니다.
- **Azure 리소스 그룹 이름**: azure에서 데이터 컨트롤러 리소스를 만들려는 리소스 그룹의 이름입니다. 모든 Azure Arc 사용 SQL 관리 되는 인스턴스 및 Azure Database for PostgreSQL hyperscale 서버 그룹도이 리소스 그룹에 만들어집니다.
- **Azure 위치**: 데이터 컨트롤러 리소스 메타 데이터가 azure에 저장 되는 azure 위치입니다. 사용 가능한 지역 목록은 Azure 글로벌 인프라의 [지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc) 페이지를 참조 하세요. 배포 된 데이터 컨트롤러에서 관리 하는 Azure 리소스에 대 한 메타 데이터 및 청구 정보는 위치 매개 변수로 지정 하는 Azure의 위치에만 저장 됩니다. 직접 연결 모드에서 배포 하는 경우 데이터 컨트롤러에 대 한 위치 매개 변수는 대상 사용자 지정 위치 리소스의 위치와 동일 합니다.
- **서비스 사용자 정보**: 
   - **간접** 연결 모드로 배포 하는 경우에는 사용 현황 및 메트릭 데이터를 업로드 하기 위한 서비스 주체 정보가 필요 합니다. 자세한 내용은 [Azure에 대 한 업로드 사용 현황 데이터, 메트릭 및 로그](upload-metrics-and-logs-to-azure-monitor.md)의 "서비스 사용자에 게 역할 할당" 섹션을 참조 하세요. 

- **인프라**: 청구를 위해 Azure Arc 사용 데이터 서비스를 실행 하는 인프라를 지정 해야 합니다. 옵션은 다음과 같습니다.
- `alibaba`
- `aws`
- `azure`
- `gcp`
- `onpremises`
- `other`

## <a name="additional-concepts-for-direct-connectivity-mode"></a>직접 연결 모드에 대 한 추가 개념

[연결 모드 및 요구 사항](./connectivity.md)에 설명 된 대로 **직접** 또는 **간접** 연결 모드에서 Azure Arc 데이터 컨트롤러를 배포할 수 있습니다. 직접 연결 모드에서 Azure Arc data services를 배포 하려면 추가 개념 및 고려 사항이 필요 합니다.

* 먼저 Azure Arc 사용 데이터 서비스가 배포 될 Kubernetes 클러스터는 [Azure arc 사용 가능 Kubernetes 클러스터](../kubernetes/overview.md)여야 합니다. Kubernetes 클러스터를 Azure에 연결 하 여 Azure Portal에서 직접 클러스터에 Azure Arc 데이터 서비스를 배포 하 고 관리 하 고, 사용 현황, 로그 및 메트릭을 Azure에 자동으로 업로드 하 고, 다른 Azure 혜택을 얻을 수 있습니다. 방법을 알아보려면 [Azure에 클러스터 커넥트](../kubernetes/quickstart-connect-cluster.md)를 참조 하세요.

* Kubernetes 클러스터가 Azure Arc를 사용 하도록 설정 된 후 다음을 수행 하 여 Azure Arc 사용 데이터 서비스를 배포 합니다.
   1. Azure Arc data services 확장을 만듭니다. 방법을 알아보려면 [Azure Arc에서 클러스터 확장-사용 Kubernetes](../kubernetes/conceptual-extensions.md)을 참조 하세요.
   1. 사용자 지정 위치를 만듭니다. 방법에 대 한 자세한 내용은 [Azure Arc 사용 Kubernetes의 맨 위에 있는 사용자 지정 위치](../kubernetes/conceptual-custom-locations.md)를 참조 하세요.
   1. Azure Arc 데이터 컨트롤러를 만듭니다.

   Azure Portal에서 Azure Arc data controller 만들기 마법사를 사용 하 여이 세 단계를 한 번에 수행할 수 있습니다.

azure arc 데이터 컨트롤러를 설치한 후 azure arc 사용 SQL Managed Instance 또는 azure arc 사용 PostgreSQL hyperscale과 같은 데이터 서비스를 만들고 액세스할 수 있습니다.


## <a name="next-steps"></a>다음 단계

Azure Arc 데이터 컨트롤러를 만들기 위한 몇 가지 추가 옵션이 있습니다.

> **작업을 시도해보시겠습니까?**
> AKS, Amazon EKS 또는 GKE 또는 Azure VM에서 [Azure Arc Jumpstart](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) 를 빠르게 시작 하세요.
>
- [Azure Portal를 사용 하 여 직접 연결 모드에서 데이터 컨트롤러 만들기](create-data-controller-direct-prerequisites.md)
- [CLI를 사용 하 여 간접 연결 모드에서 데이터 컨트롤러 만들기](create-data-controller-indirect-cli.md)
- [Azure Data Studio를 사용 하 여 간접 연결 모드에서 데이터 컨트롤러 만들기](create-data-controller-indirect-azure-data-studio.md)
- [Azure Data Studio에서 Jupyter 노트북을 통해 Azure Portal에서 간접 연결 모드로 데이터 컨트롤러를 만듭니다.](create-data-controller-indirect-azure-portal.md)
- [Kubectl 또는 oc와 같은 Kubernetes 도구를 사용 하 여 간접 연결 모드에서 데이터 컨트롤러 만들기](create-data-controller-using-kubernetes-native-tools.md)
