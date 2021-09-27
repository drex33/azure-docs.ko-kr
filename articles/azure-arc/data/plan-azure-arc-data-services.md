---
title: Azure Arc 지원 데이터 서비스 배포 계획
description: Azure Arc 지원 데이터 서비스 배포 계획에 대한 고려 사항을 설명합니다.
services: azure-arc
ms.service: azure-arc-data
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 0d9ae624ddc0a4e5a2f5d9ac38428f4f3c10a01e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124832399"
---
# <a name="plan-to-deploy-azure-arc-enabled-data-services"></a>Azure Arc 지원 데이터 서비스 배포 계획

이 문서에서는 Azure Arc 지원 데이터 서비스 배포를 계획하는 방법을 설명합니다.


첫째, Azure Arc 데이터 서비스 배포에는 데이터베이스 워크로드 및 해당 워크로드에 대한 비즈니스 요구 사항에 대한 적절한 이해가 포함됩니다. 예를 들어 이러한 워크로드에 대한 메모리, CPU 및 스토리지에 대한 가용성, 비즈니스 연속성 및 용량 요구 사항과 같은 사항을 고려합니다. 둘째, 이러한 데이터베이스 워크로드를 지원하기 위한 인프라는 비즈니스 요구 사항에 따라 준비되어야 합니다.

## <a name="prerequisites"></a>필수 구성 요소

Azure Arc 지원 데이터 서비스를 배포하기 전에 필수 조건을 이해하고 필요한 모든 정보를 준비하고 적절한 수준의 액세스, 적절한 스토리지, CPU 및 메모리 용량으로 인프라 환경을 적절하게 구성하는 것이 중요합니다. 마지막에 성공적인 배포를 할 수 있습니다.

다음 섹션을 검토합니다.
- [크기 조정 지침](sizing-guidance.md)
- [스토리지 구성](storage-configuration.md)
- [연결 모드 및 해당 요구 사항](connectivity.md)

다음이 완료되었는지 확인합니다.
- [`arcdata` CLI 확장](install-arcdata-extension.md) 설치
- 다른 [클라이언트 도구](install-client-tools.md) 설치
- Kubernetes 클러스터에 액세스
- `kubeconfig` 파일 구성 배포하려는 Kubernetes 클러스터를 가리켜야 합니다. 다음 명령을 실행하여 배포할 클러스터의 현재 컨텍스트를 확인합니다.
   ```console
   kubectl cluster-info
   ```
- Azure Arc 데이터 컨트롤러, Azure Arc 지원 SQL Managed Instance 또는 Azure Arc 지원 PostgreSQL 하이퍼스케일 서버와 같은 리소스가 예상되고 청구되는 Azure 구독입니다.

인프라가 준비되면 다음과 같은 방식으로 Azure Arc 지원 데이터 서비스를 배포합니다.
1. Kubernetes 클러스터의 유효성이 검사된 배포 중 하나에서 Azure Arc 지원 데이터 컨트롤러 만들기
1. Azure Arc 지원 SQL Managed Instance 및/또는 Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹을 만듭니다.

> [!CAUTION]
> 일부 데이터 서비스 계층 및 모드는 [일반적으로 사용할 수](release-notes.md) 있으며 일부는 미리 보기 상태입니다. 동일한 데이터 컨트롤러에서 GA와 preview 서비스를 혼합 하지 않는 것이 좋습니다. 동일한 데이터 컨트롤러에서 GA 및 preview 서비스를 혼합 하는 경우 현재 상태에서 업그레이드할 수 없습니다. 이 시나리오에서를 업그레이드 하려는 경우 데이터 컨트롤러 및 데이터 서비스를 제거 하 고 다시 만들어야 합니다.

## <a name="overview-create-the-azure-arc-enabled-data-controller"></a>개요: Azure Arc 지원 데이터 컨트롤러 만들기

다양한 방법을 사용하여 다양한 Kubernetes 클러스터와 관리되는 Kubernetes 서비스에 Azure Arc 지원 데이터 서비스를 만들 수 있습니다.

현재 검증된 Kubernetes 서비스 및 배포 목록은 다음과 같습니다.


- AWS EKS(Elastic Kubernetes Service)
- AKS(Azure Kubernetes Service)
- Azure Stack HCI에서 Azure Kubernetes Service
- ARO(Azure RedHat OpenShift)
- GKE(Google Cloud Kubernetes Engine)
- 오픈 소스, 일반적으로 kubeadm을 사용하여 배포되는 업스트림 Kubernetes
- OCP(OpenShift 컨테이너 플랫폼)

> [!IMPORTANT]
> * 지원되는 최소 Kubernetes 버전은 v1.19입니다. 자세한 내용은 [알려진 문제](./release-notes.md#known-issues)를 참조하세요.
> * OCP는 최소 4.7 버전부터 지원됩니다.
> * Azure Kubernetes Service를 사용하고 있다면, 클러스터의 작업자 노드 VM 크기는 최소 **Standard_D8s_v3** 이어야 하며 **프리미엄 디스크** 를 사용하여야 합니다. 
> * 클러스터는 여러 가용성 영역에 걸쳐 있지 않아야 합니다. 
> * 자세한 내용은 [알려진 문제](./release-notes.md#known-issues)를 참조하세요.

선택한 옵션에 상관 없이 제작 프로세스 중에 다음 정보를 제공하여야 합니다.

- **데이터 컨트롤러 이름** - 데이터 컨트롤러를 설명하는 이름 - 예: "production-dc", "seattle-dc". 이름은 [Kubernetes 명명 표준](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/)을 충족해야 합니다.
- **사용자 이름** - Kibana/Grafana 관리자 사용자의 사용자 이름입니다.
- **암호** - Kibana/Grafana 관리자의 암호입니다.
- **Kubernetes 네임스페이스 이름** - 데이터 컨트롤러를 만들고자 하는 Kubernetes 네임스페이스의 이름
- **연결 모드** - 연결 모드는 Azure Arc 지원 데이터 서비스 환경과 Azure의 연결 수준을 결정합니다. 간접 연결 모드가 일반적으로 사용 가능합니다. 직접 연결 모드는 미리 보기 중입니다.  연결 모드 선택에 따라 배포 방법에 대한 옵션이 결정됩니다.  자세한 내용은 [연결 모드](./connectivity.md)를 참조하세요.
- **Azure 구독 ID** - Azure에서 데이터 컨트롤러 리소스를 만들고자 하는 Azure 구독 GUID  모든 Azure Arc 지원 SQL Managed Instances 및 PostgreSQL 하이퍼스케일 서버 그룹도 이 구독에서 만들어지고 해당 구독에 청구됩니다.
- **Azure 리소스 그룹 이름** - Azure에서 데이터 컨트롤러 리소스를 만들고자 하는 리소스 그룹 이름  모든 Azure Arc 지원 SQL Managed Instances 및 PostgreSQL 하이퍼스케일 서버 그룹도 이 리소스 그룹에서 만들어집니다.
- **Azure 위치** - Azure에서 데이터 컨트롤러 리소스 메타데이터를 저장할 Azure 위치 사용 가능한 지역 목록은 [Azure 글로벌 인프라/지역별 제품](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc)을 참조하세요. 배포 중인 데이터 컨트롤러에서 관리하는 Azure 리소스에 대한 메타데이터 및 청구 정보는 위치 매개 변수로 지정하는 Azure의 위치에만 저장됩니다. 직접 연결 모드에서 배포하는 경우 데이터 컨트롤러에 대한 위치 매개 변수는 대상으로 지정하는 사용자 지정 위치 리소스의 위치와 동일합니다.
- **서비스 주체 정보** - [업로드 필수 조건](upload-metrics-and-logs-to-azure-monitor.md) 문서에 설명된 대로 *직접* 연결 모드로 배포할 때 Azure Arc 데이터 컨트롤러를 만드는 동안 서비스 주체 정보가 필요합니다. *간접* 연결 모드의 경우 서비스 주체는 여전히 수동으로 내보내고 업로드해야 하지만 Azure Arc 데이터 컨트롤러가 만들어진 후에 필요합니다.
- **인프라** -청구를 위해 Azure Arc 사용 데이터 서비스를 실행 하는 인프라를 나타내는 데 필요 합니다.  옵션은 `alibaba`, `aws`, `azure`, `gcp`, `onpremises` 또는 `other`입니다.

## <a name="additional-concepts-for-direct-connected-mode"></a>직접 연결 모드에 대한 추가 개념

[연결 모드](./connectivity.md)에 설명된 대로 Azure Arc 데이터 컨트롤러는 **직접** 또는 **간접** 연결 모드로 배포할 수 있습니다. Azure Arc 데이터 서비스를 **직접** 연결 모드로 배포하려면 몇 가지 추가 개념과 고려 사항을 이해해야 합니다.
먼저 Azure Arc 사용 데이터 서비스가 배포 될 Kubernetes 클러스터는 [Azure arc 사용 가능 Kubernetes 클러스터](../kubernetes/overview.md)여야 합니다. Kubernetes 클러스터를 Azure Arc에 온보딩하면 사용 정보, 로그, 메트릭 등의 자동 업로드와 같은 기능에 활용되는 Azure 연결이 제공됩니다. Kubernetes 클러스터를 Azure에 연결하면 Azure Portal에서 직접 클러스터에 Azure Arc 데이터 서비스를 배포하고 관리할 수도 있습니다.

Kubernetes 클러스터를 Azure에 연결하려면 다음 단계가 포함됩니다.
- [Azure에 클러스터 연결](../kubernetes/quickstart-connect-cluster.md)

둘째, Kubernetes 클러스터가 Azure Arc에 온보딩된 후 Azure Arc 지원 Kubernetes 클러스터에 Azure Arc 지원 데이터 서비스를 배포하려면 다음이 포함됩니다.
- Arc 데이터 서비스 확장 만들기, [클러스터 확장](../kubernetes/conceptual-extensions.md)에 대해 자세히 알아보기
- 사용자 지정 위치 만들기, [사용자 지정 위치](../kubernetes/conceptual-custom-locations.md)에 대해 자세히 알아보기
- Azure Arc 데이터 컨트롤러 만들기

이 세 단계는 모두 Azure Portal에서 Azure Arc 데이터 컨트롤러 만들기 마법사를 사용하여 단일 단계로 수행할 수 있습니다.

Azure Arc 데이터 컨트롤러가 설치된 후 Azure Arc 지원 SQL Managed Instance 또는 Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹과 같은 데이터 서비스를 만들 수 있습니다.


## <a name="next-steps"></a>다음 단계

Azure Arc 데이터 컨트롤러를 만들 때는 다음과 같이 다양한 옵션이 있습니다.

> **작업을 시도해보시겠습니까?**
> AKS(Azure Kubernetes Service), AWS EKS(Elastic Kubernetes Service), GKE(Google Cloud Kubernetes Engine) 또는 Azure VM에서 [Azure Arc Jumpstart](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/)를 사용하여 빨리 시작할 수 있습니다.
>
- [Azure Portal을 사용하여 직접 연결 모드에서 데이터 컨트롤러 만들기](create-data-controller-direct-prerequisites.md)
- [CLI를 사용하여 간접 연결 모드에서 데이터 컨트롤러 만들기](create-data-controller-indirect-cli.md)
- [Azure Data Studio를 사용하여 간접 연결 모드에서 데이터 컨트롤러 만들기](create-data-controller-indirect-azure-data-studio.md)
- [Azure Data Studio의 Jupyter Notebook을 통해 Azure Portal에서 간접 연결 모드로 데이터 컨트롤러 만들기](create-data-controller-indirect-azure-portal.md)
- [kubectl 또는 oc와 같은 Kubernetes 도구를 사용하여 간접 연결 모드에서 데이터 컨트롤러 만들기](create-data-controller-using-kubernetes-native-tools.md)
