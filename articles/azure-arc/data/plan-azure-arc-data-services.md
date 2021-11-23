---
title: Azure Arc 지원 데이터 서비스 배포 계획
description: 이 문서에서는 Azure Arc 지원 데이터 서비스 배포를 계획하기 위한 고려 사항을 설명합니다.
services: azure-arc
ms.service: azure-arc-data
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: 7a555407a337d5de2d5370d3676d52fe93250403
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132938706"
---
# <a name="plan-an-azure-arc-enabled-data-services-deployment"></a>Azure Arc 지원 데이터 서비스 배포 계획

이 문서에서는 Azure Arc 지원 데이터 서비스 배포를 계획하는 방법을 설명합니다.

Azure Arc 지원 데이터 서비스 배포 계획을 시작할 때 데이터베이스 워크로드 및 해당 워크로드에 대한 비즈니스 요구 사항을 제대로 이해하는 것이 중요합니다. 예를 들어 워크로드의 메모리, CPU 및 스토리지에 대한 가용성, 비즈니스 연속성 및 용량 요구 사항을 고려해야 합니다. 또한 비즈니스 요구 사항에 따라 데이터베이스 워크로드를 지원하기 위해 인프라를 신중하게 준비해야 합니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하기 전에 특정 필수 구성을 충족하고 필요한 배경 및 정보를 준비해야 합니다. 성공적인 배포를 보장하려면 적절한 수준의 액세스 권한과 스토리지, CPU 및 메모리에 적절한 용량을 사용하여 인프라 환경을 올바르게 구성해야 합니다. 

다음 문서를 검토합니다.

- [크기 조정 지침](sizing-guidance.md)
- [스토리지 구성](storage-configuration.md)
- [연결 모드 및 해당 요구 사항](connectivity.md)

다음 사항을 확인합니다.

- [ `arcdata` CLI 확장이](install-arcdata-extension.md) 설치됩니다.
- 다른 [클라이언트 도구가](install-client-tools.md) 설치됩니다.
- Kubernetes 클러스터에 액세스할 수 있습니다.
- *kubeconfig* 파일이 구성되어 있습니다. 배포하려는 Kubernetes 클러스터를 가리킵니다. 클러스터의 현재 컨텍스트를 확인하려면 다음 명령을 실행합니다.
   ```console
   kubectl cluster-info
   ```  
- Azure Arc 데이터 컨트롤러, Azure Arc 지원 SQL 관리형 인스턴스 또는 Azure Arc 지원 PostgreSQL 하이퍼스케일 서버와 같은 리소스가 프로젝션되고 요금이 청구되는 Azure 구독이 있습니다.

인프라를 준비한 후 다음과 같은 방식으로 Azure Arc 지원 데이터 서비스를 배포합니다.
1. Kubernetes 클러스터의 유효성이 검사된 배포판 중 하나에서 Azure Arc 지원 데이터 컨트롤러를 만듭니다.
1. Azure Arc 지원 SQL Managed Instance 및/또는 Azure Arc 지원 PostgreSQL 하이퍼스케일 서버 그룹을 만듭니다.

> [!CAUTION]
> 일부 데이터 서비스 계층 및 모드는 [GA(일반 공급)에](release-notes.md)있으며 일부는 미리 보기로 제공됩니다. 동일한 데이터 컨트롤러에서 GA 및 미리 보기 서비스를 혼합하지 않는 것이 좋습니다. 동일한 데이터 컨트롤러에서 GA 및 미리 보기 서비스를 혼합하는 경우 현재 위치로 업그레이드할 수 없습니다. 이 시나리오에서 업그레이드하려는 경우 데이터 컨트롤러 및 데이터 서비스를 제거하고 다시 만들어야 합니다.

## <a name="overview-create-an-azure-arc-enabled-data-controller"></a>개요: Azure Arc 사용 가능한 데이터 컨트롤러 만들기

다양한 유형의 Kubernetes 클러스터에서 Azure Arc 지원 데이터 서비스를 만들고 다양한 접근 방식을 통해 관리되는 Kubernetes 서비스를 만들 수 있습니다.

현재 검증된 Kubernetes 서비스 및 배포 목록은 다음과 같습니다.

- Amazon EKS(Amazon Elastic Kubernetes Service)
- AKS(Azure Kubernetes Service)
- Azure Stack HCI에서 Azure Kubernetes Service
- Azure Red Hat OpenShift
- GKE(Google Kubernetes Engine)
- 오픈 소스 업스트림 Kubernetes(일반적으로 kubeadm을 사용하여 배포)
- OCP(OpenShift 컨테이너 플랫폼)

> [!IMPORTANT]
> * 지원되는 최소 Kubernetes 버전은 v1.19입니다. 자세한 내용은 릴리스 정보 - Azure Arc 지원 데이터 서비스의 "알려진 문제" [ &nbsp; 섹션을 참조하세요.](./release-notes.md#known-issues)
> * OCP는 최소 4.7 버전부터 지원됩니다.
> * Azure Kubernetes Service 사용하는 경우 클러스터의 작업자 노드 VM(가상 머신) 크기는 Standard_D8s_v3 이상이어야 하며 Premium 디스크를 사용해야 합니다. 
> * 클러스터는 여러 가용성 영역에 걸쳐 있지 않아야 합니다. 
> * 자세한 내용은 릴리스 정보 - Azure Arc 지원 데이터 서비스의 "알려진 문제" [ &nbsp; 섹션을 참조하세요.](./release-notes.md#known-issues)

선택한 서비스 또는 배포 옵션에 관계없이 Azure Arc 지원 데이터 서비스를 만들 때 다음 정보를 제공해야 합니다.

- **데이터 컨트롤러 이름:** 데이터 컨트롤러에 대한 설명이 포함된 이름입니다(예: *production-dc* 또는 *seattle-dc).* 이름은 [Kubernetes 명명 표준](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/)을 충족해야 합니다.

- **사용자 이름:** Kibana/Grafana 관리자 사용자의 사용자 이름입니다.
- **암호:** Kibana/Grafana 관리자 사용자의 암호입니다.
- **Kubernetes 네임스페이스의 이름:** 데이터 컨트롤러를 만들 Kubernetes 네임스페이스의 이름입니다.
- **연결 모드:** Azure Arc 지원 데이터 서비스 환경에서 Azure로의 연결 정도를 결정합니다. 연결 모드를 선택하면 배포 방법에 대한 옵션이 결정됩니다. 자세한 내용은 [연결 모드 및 요구 사항을 참조하세요.](./connectivity.md)
- **Azure 구독 ID:** Azure에서 데이터 컨트롤러 리소스를 만들려는 Azure 구독 GUID입니다. 모든 Azure Arc 지원 SQL 관리되는 인스턴스 및 Azure Database for PostgreSQL 하이퍼스케일 서버 그룹도 에서 만들어지고 이 구독에 대한 요금이 청구됩니다.
- **Azure 리소스 그룹 이름: Azure에서** 데이터 컨트롤러 리소스를 만들려는 리소스 그룹의 이름입니다. 모든 Azure Arc 지원 SQL 관리되는 인스턴스 및 Azure Database for PostgreSQL 하이퍼스케일 서버 그룹도 이 리소스 그룹에 만들어집니다.
- **Azure 위치:** 데이터 컨트롤러 리소스 메타데이터가 Azure에 저장될 Azure 위치입니다. 사용 가능한 지역 목록은 Azure 글로벌 인프라에 대한 [지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc) 페이지를 참조하세요. 배포된 데이터 컨트롤러에서 관리하는 Azure 리소스에 대한 메타데이터 및 청구 정보는 위치 매개 변수로 지정한 Azure의 위치에만 저장됩니다. 직접 연결 모드로 배포하는 경우 데이터 컨트롤러의 위치 매개 변수는 대상 사용자 지정 위치 리소스의 위치와 동일합니다.
- **서비스 주체 정보:** 
   - **간접** 연결 모드로 배포하는 경우 사용량 및 메트릭 데이터를 업로드하려면 서비스 주체 정보가 필요합니다. 자세한 내용은 [사용량 현황 데이터, 메트릭 및 로그를 Azure에 업로드](upload-metrics-and-logs-to-azure-monitor.md)"서비스 주체에 역할 할당" 섹션을 참조하세요. 

- **인프라:** 대금 청구를 위해 Azure Arc 지원 데이터 서비스를 실행하는 인프라를 지정해야 합니다. 옵션은 다음과 같습니다.
- `alibaba`
- `aws`
- `azure`
- `gcp`
- `onpremises`
- `other`

## <a name="additional-concepts-for-direct-connectivity-mode"></a>직접 연결 모드에 대한 추가 개념

연결 모드 [및 요구 사항에](./connectivity.md)설명된 대로 **직접** 또는 **간접** 연결 모드에서 Azure Arc 데이터 컨트롤러를 배포할 수 있습니다. 직접 연결 모드에서 Azure Arc 데이터 서비스를 배포하려면 추가 개념 및 고려 사항이 필요합니다.

* 먼저 Azure Arc 지원 데이터 서비스를 배포할 Kubernetes 클러스터는 [Azure Arc 지원 Kubernetes 클러스터](../kubernetes/overview.md)여야 합니다. Kubernetes 클러스터를 Azure에 연결하면 Azure Portal 직접 클러스터에 Azure Arc 데이터 서비스를 배포 및 관리하고, 사용량, 로그 및 메트릭을 Azure에 자동으로 업로드하고, 몇 가지 다른 Azure 혜택을 얻을 수 있습니다. 방법을 알아보려면 [클러스터를 Azure에 커넥트 참조하세요.](../kubernetes/quickstart-connect-cluster.md)

* Kubernetes 클러스터를 Azure Arc 사용하도록 설정한 후 다음을 수행하여 Azure Arc 지원 데이터 서비스를 배포합니다.
   1. Azure Arc 데이터 서비스 확장을 만듭니다. 방법을 알아보려면 [Azure Arc 지원 Kubernetes의 클러스터 확장을 참조하세요.](../kubernetes/conceptual-extensions.md)
   1. 사용자 지정 위치를 만듭니다. 방법을 알아보려면 [Azure Arc 지원 Kubernetes 위에 있는 사용자 지정 위치를 참조하세요.](../kubernetes/conceptual-custom-locations.md)
   1. Azure Arc 데이터 컨트롤러를 만듭니다.

   Azure Portal Azure Arc 데이터 컨트롤러 만들기 마법사를 사용하여 이러한 세 단계를 한 단계로 모두 수행할 수 있습니다.

Azure Arc 데이터 컨트롤러를 설치한 후에는 Azure Arc 지원 SQL Managed Instance 또는 Azure Arc 지원 PostgreSQL 하이퍼스케일과 같은 데이터 서비스를 만들고 액세스할 수 있습니다.


## <a name="next-steps"></a>다음 단계

Azure Arc 데이터 컨트롤러를 만들기 위한 몇 가지 추가 옵션이 있습니다.

> **작업을 시도해보시겠습니까?**
> AKS, Amazon EKS 또는 GKE 또는 Azure VM에서 [Azure Arc Jumpstart를](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) 빠르게 시작하세요.
>
- [Azure Portal 사용하여 직접 연결 모드에서 데이터 컨트롤러 만들기](create-data-controller-direct-prerequisites.md)
- [CLI를 사용하여 간접 연결 모드에서 데이터 컨트롤러 만들기](create-data-controller-indirect-cli.md)
- [Azure Data Studio 사용하여 간접 연결 모드에서 데이터 컨트롤러 만들기](create-data-controller-indirect-azure-data-studio.md)
- [Azure Data Studio Jupyter Notebook을 통해 Azure Portal 간접 연결 모드로 데이터 컨트롤러를 만듭니다.](create-data-controller-indirect-azure-portal.md)
- [kubectl 또는 oc와 같은 Kubernetes 도구를 사용하여 간접 연결 모드에서 데이터 컨트롤러 만들기](create-data-controller-using-kubernetes-native-tools.md)
