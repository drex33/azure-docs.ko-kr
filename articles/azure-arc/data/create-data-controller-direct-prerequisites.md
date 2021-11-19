---
title: 필수 구성 요소 | 직접 연결 모드
description: 직접 연결 모드에서 데이터 컨트롤러를 배포하기 위한 필수 구성 요소.
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 11/03/2021
ms.topic: overview
ms.openlocfilehash: cd1c12eaf4c4d5df575c7774386da007c9124712
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132549834"
---
# <a name="prerequisites-to-deploy-the-data-controller-in-direct-connectivity-mode"></a>직접 연결 모드에서 데이터 컨트롤러를 배포하기 위한 필수 구성 요소.

이 문서에서는 직접 연결 모드에서 Azure Arc 지원 데이터 서비스에 대한 데이터 컨트롤러 배포를 준비하는 방법을 설명합니다. Azure Arc 데이터 컨트롤러를 배포하기 전에 [Azure Arc 지원 데이터 서비스 배포 계획](plan-azure-arc-data-services.md)에 설명된 개념을 이해하세요.

높은 수준에서 **직접** 연결 모드로 Azure Arc 데이터 컨트롤러를 만들기 위한 필수 조건은 다음과 같습니다.

1. Azure Arc 지원 Kubernetes를 사용하여 Kubernetes 클러스터를 Azure에 연결
2. Azure Arc 지원 데이터 서비스 데이터 컨트롤러를 만듭니다. 이 단계에는 만들기가 포함됩니다.
    - Azure Arc 데이터 서비스 확장
    - 사용자 지정 위치
    - Azure Arc 데이터 컨트롤러
3. Azure Log Analytics에 로그를 자동으로 업로드하려는 경우 배포의 일부로 Log Analytics 작업 영역 ID 및 공유 액세스 키가 필요합니다.

## <a name="1-connect-kubernetes-cluster-to-azure-using-azure-arc-enabled-kubernetes"></a>1. Azure Arc 지원 Kubernetes를 사용하여 Kubernetes 클러스터를 Azure에 연결

Kubernetes 클러스터를 Azure에 연결하려면 다음 확장 또는 Helm과 함께 Azure CLI `az`를 사용합니다.

### <a name="install-tools"></a>도구 설치

- Helm 버전 3.3 이상([설치](https://helm.sh/docs/intro/install/))
- 최신 버전의 Azure CLI 설치 또는 업그레이드([다운로드](https://aka.ms/installazurecliwindows))

### <a name="add-extensions-for-azure-cli"></a>Azure CLI에 대한 확장 추가

다음 az 확장의 최신 버전을 설치합니다.
- `k8s-extension`
- `connectedk8s`
- `k8s-configuration`
- `customlocation`

다음 명령을 실행하여 az CLI 확장을 설치합니다.

```azurecli
az extension add --name k8s-extension
az extension add --name connectedk8s
az extension add --name k8s-configuration
az extension add --name customlocation
```

`k8s-extension`, `connectedk8s`, `k8s-configuration`, `customlocation` 확장을 이미 설치한 경우 다음 명령을 사용하여 최신 버전으로 업데이트할 수 있습니다.

```azurecli
az extension update --name k8s-extension
az extension update --name connectedk8s
az extension update --name k8s-configuration
az extension update --name customlocation
```

### <a name="connect-your-cluster-to-azure"></a>Azure에 클러스터 연결

이 작업을 완료하려면 [Azure arc에 기존 Kubernetes 클러스터 연결](../kubernetes/quickstart-connect-cluster.md)의 단계를 따르세요.

## <a name="2-optionally-keep-the-log-analytics-workspace-id-and-shared-access-key-ready"></a>2. 필요에 따라 Log Analytics 작업 영역 ID 및 공유 액세스 키를 준비 상태로 유지

Azure Arc 지원 데이터 컨트롤러를 배포하는 경우 설치하는 동안 메트릭 및 로그의 자동 업로드를 사용하도록 설정할 수 있습니다. 메트릭 업로드는 시스템 할당 관리 ID를 사용합니다. 그러나 로그를 업로드하려면 작업 영역 ID와 작업 영역에 대한 액세스 키가 필요합니다. 

데이터 컨트롤러를 배포한 후 메트릭 및 로그의 자동 업로드를 사용하거나 사용하지 않도록 설정할 수도 있습니다. 

## <a name="3-create-azure-arc-data-services"></a>3. Azure Arc 데이터 서비스 만들기

이러한 필수 구성 요소를 완료한 후 [Azure Arc 데이터 컨트롤러 | 직접 연결 모드를 배포](create-data-controller-direct-azure-portal.md)할 수 있습니다.
