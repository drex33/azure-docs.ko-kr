---
title: 데이터 컨트롤러 만들기
description: 이미 배포한 일반 다중 노드 Kubernetes 클러스터에 Azure Arc 데이터 컨트롤러를 만듭니다.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 05/05/2021
ms.topic: how-to
ms.openlocfilehash: 85a5c9f08304c98b544fef2ccb7818d96613b3d4
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108748136"
---
# <a name="create-the-azure-arc-data-controller"></a>Azure Arc 데이터 컨트롤러 만들기

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="overview-of-creating-the-azure-arc-data-controller"></a>Azure Arc 데이터 컨트롤러 만들기 개요

다양한 방법을 사용하여 다양한 Kubernetes 클러스터와 관리되는 Kubernetes 서비스에 Azure Arc 지원 데이터 서비스를 만들 수 있습니다.

현재는 다음과 같은 Kubernetes 서비스 및 배포가 지원되고 있습니다.

- AKS(Azure Kubernetes Service)
- Azure Stack의 AKS 엔진(Azure Kubernetes Service 엔진)
- Azure Stack HCI에서 Azure Kubernetes Service
- ARO(Azure RedHat OpenShift)
- OCP(OpenShift 컨테이너 플랫폼)
- AWS EKS(Elastic Kubernetes Service)
- GKE(Google Cloud Kubernetes Engine)
- 오픈 소스, 일반적으로 kubeadm을 사용하여 배포되는 업스트림 Kubernetes

> [!IMPORTANT]
> * 지원되는 최소 Kubernetes 버전은 v1.17입니다. 자세한 내용은 [알려진 문제](./release-notes.md#known-issues)를 참조하세요. 
> * OCP는 최소 4.5 버전부터 지원됩니다.
> * 환경과 Azure 사이에 필요한 연결에 대하여 이해하려면 [연결 요구 사항](connectivity.md)을 참조하세요.
> * 영구 저장소 구성 방법에 대하여 자세히 이해하려면 [스토리지 구성 참고 자료](storage-configuration.md)를 참조하세요.
> * Azure Kubernetes Service를 사용하고 있다면, 클러스터의 작업자 노드 VM 크기는 최소 **Standard_D8s_v3** 이어야 하며 **프리미엄 디스크** 를 사용하여야 합니다. 클러스터는 여러 가용성 영역에 걸쳐 있지 않아야 합니다. 
> * 다른 Kubernetes 배포나 서비스 사용 중인 경우, 최소 노드 크기는 8 GB RAM, 4 코어에 전체 Kubernetes 노드에서 사용 가능한 총 용량은 32 GB RAM이어야 합니다. 예를 들어 32 GB RAM, 4 코어에서는 노드 하나를 갖게 되거나 각각 16GB RAM, 4 코어인 노드 두 개를 갖게 됩니다.

> [!NOTE]
> Azure에서 Red Hat OpenShift Container Platform을 사용하는 경우, 사용 가능한 최신 버전을 사용하는 것이 좋습니다.

선택한 옵션에 따라 특정 도구가 _필요하지만_, Azure Arc 데이터 컨트롤러를 만들기 전에 [클라이언트 도구를 모두 설치](./install-client-tools.md)하는 것이 좋습니다.

선택한 옵션에 상관 없이 제작 프로세스 중에 다음 정보를 제공하여야 합니다.

- **데이터 컨트롤러 이름** - 데이터 컨드롤러를 설명하는 이름 - 예: "프로덕션 데이터 컨트롤러", "시애틀 데이터 컨트롤러".
- **데이터 컨트롤러 사용자 이름** - 데이터 컨트롤러 관리 사용자의 사용자 이름
- **데이터 컨트롤러 암호** - 데이터 컨트롤러 관리 사용자용 암호
- **Kubernetes 네임스페이스 이름** - 데이터 컨트롤러를 만들고자 하는 Kubernetes 네임스페이스의 이름
- **연결 모드** - 연결 모드는 Azure Arc 지원 데이터 서비스 환경과 Azure의 연결 수준을 결정합니다. 현재, 미리 보기는 간접 연결 및 직접 연결 모드만 지원합니다.  자세한 내용은 [연결 모드](./connectivity.md)를 참조하세요. 
- **Azure 구독 ID** - Azure에서 데이터 컨트롤러 리소스를 만들고자 하는 Azure 구독 GUID
- **Azure 리소스 그룹 이름** - Azure에서 데이터 컨트롤러 리소스를 만들고자 하는 리소스 그룹 이름
- **Azure 위치** - Azure에서 데이터 컨트롤러 리소스 메타데이터를 저장할 Azure 위치 사용 가능한 지역 목록은 [Azure 글로벌 인프라/지역별 제품](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc)을 참조하세요. 배포 중인 데이터 컨트롤러에서 관리하는 Azure 리소스에 대한 메타데이터 및 청구 정보는 위치 매개 변수로 지정하는 Azure의 위치에만 저장됩니다. 직접 연결 모드에서 배포하는 경우 데이터 컨트롤러에 대한 위치 매개 변수는 대상으로 지정하는 사용자 지정 위치 리소스의 위치와 동일합니다.

## <a name="next-steps"></a>다음 단계

Azure Arc 데이터 컨트롤러를 만들 때는 다음과 같이 다양한 옵션이 있습니다.

> **작업을 시도해보시겠습니까?**  
> AKS(Azure Kubernetes Service), AWS EKS(Elastic Kubernetes Service), GKE(Google Cloud Kubernetes Engine) 또는 Azure VM에서 [Azure Arc Jumpstart](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/)를 사용하여 빨리 시작할 수 있습니다.
> 
- [[!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]](create-data-controller-using-azdata.md)로 데이터 컨트롤러 만들기
- [Azure Data Studio로 데이터 컨트롤러 만들기](create-data-controller-azure-data-studio.md)
- [Azure Data Studio의 Jupyter Notebook을 통하여 Azure Portal에서 데이터 컨트롤러 만들기](create-data-controller-resource-in-azure-portal.md)
- [kubectl 또는 oc 등의 Kubernetes 도구로 데이터 컨트롤러 만들기](create-data-controller-using-kubernetes-native-tools.md)
- [테스트 배포를 가속화하는 환경을 위하여 Azure Arc Jumpstart로 데이터 컨트롤러 만들기](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/)
