---
title: Azure Arc 지원 Kubernetes 유효성 검사
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Kubernetes 배포에 대한 Arc 유효성 검사 프로그램을 설명합니다.
keywords: Kubernetes, Arc, Azure, K8s, 유효성 검사
ms.openlocfilehash: f5057a7785ec77109d8fe8efb44e1015976786c8
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128672859"
---
# <a name="azure-arc-enabled-kubernetes-validation"></a>Azure Arc 지원 Kubernetes 유효성 검사

Azure Arc 지원 Kubernetes는 모든 CNCF(Cloud Native Computing Foundation) 인증 Kubernetes 클러스터에서 작동합니다. 또한 Azure Arc 팀은 Kubernetes 배포를 통해 Azure Arc 사용 Kubernetes의 유효성을 검사 하는 핵심 업계 Kubernetes 제공 공급자와 함께 작업 했습니다. 이러한 공급자가 릴리스한 Kubernetes 배포판의 이후 주 버전과 부 버전은 Azure Arc 사용 Kubernetes와의 호환성을 확인 합니다.

## <a name="validated-distributions"></a>유효성을 검사한 배포

다음 Microsoft에서 제공 하는 Kubernetes 배포판 및 인프라 공급자는 Azure Arc 사용 Kubernetes에 대 한 규칙 테스트를 성공적으로 통과 했습니다.

| 배포 및 인프라 공급자 | 버전 |
| ---------------------------------------- | ------- |
| Azure의 클러스터 API 공급자            | 릴리스 버전: [0.4.12](https://github.com/kubernetes-sigs/cluster-api-provider-azure/releases/tag/v0.4.12), Kubernetes 버전: [1.18.2](https://github.com/kubernetes/kubernetes/releases/tag/v1.18.2) |
| Azure Stack HCI의 AKS                   | 릴리스 버전: [2020년 12월 업데이트](https://github.com/Azure/aks-hci/releases/tag/AKS-HCI-2012), Kubernetes 버전: [1.18.8](https://github.com/kubernetes/kubernetes/releases/tag/v1.18.8) |

다음 공급자와 해당 Kubernetes 배포판에서 Azure Arc 사용 Kubernetes에 대 한 규칙 테스트를 성공적으로 통과 했습니다.

| 공급자 이름 | 배포 이름 | 버전 |
| ------------ | ----------------- | ------- |
| RedHat       | [OpenShift Container Platform](https://www.openshift.com/products/container-platform) | [4.5.41 이상](https://docs.openshift.com/container-platform/4.5/release_notes/ocp-4-5-release-notes.html), [4.6.35 이상](https://docs.openshift.com/container-platform/4.6/release_notes/ocp-4-6-release-notes.html), [4.7.18 이상](https://docs.openshift.com/container-platform/4.7/release_notes/ocp-4-7-release-notes.html) |
| VMware       | [Tanzu Kubernetes Grid](https://tanzu.vmware.com/kubernetes-grid) | TKGm 1.4.0; 업스트림 K8s v 1.21.2 + vmware. 1 <br>TKGm 1.3.1; 업스트림 K8s v1.20.5_vmware. 2 <br>TKGm 1.2.1; 업스트림 K8s v 1.19.3 + vmware. 1 |
| Canonical    | [Charmed Kubernetes](https://ubuntu.com/kubernetes) | [1.19](https://ubuntu.com/kubernetes/docs/1.19/components) |
| SUSE Rancher      | [Rancher Kubernetes Engine](https://rancher.com/products/rke/) | RKE CLI 버전: [v1.2.4](https://github.com/rancher/rke/releases/tag/v1.2.4), Kubernetes 버전: [1.19.6](https://github.com/kubernetes/kubernetes/releases/tag/v1.19.6)), [1.18.14](https://github.com/kubernetes/kubernetes/releases/tag/v1.18.14)), [1.17.16](https://github.com/kubernetes/kubernetes/releases/tag/v1.17.16))  |
| Nutanix      | [Karbon](https://www.nutanix.com/products/karbon)    | 버전 2.2.1 |
| Platform9      | [PMK(Platform9 Managed Kubernetes)](https://platform9.com/managed-kubernetes/)    | PMK 버전 [5.3.0](https://platform9.com/docs/kubernetes/release-notes#platform9-managed-kubernetes-version-53-release-notes), Kubernetes 버전: v1.20.5, v1.19.6, v1.18.10 |

또한 Azure Arc 팀은 규칙 테스트를 실행 하 고 다음과 같은 공용 클라우드 공급자에서 Azure Arc 사용 Kubernetes 시나리오의 유효성을 검사 했습니다.

| 퍼블릭 클라우드 공급자 이름 | 배포 이름 | 버전 |
| -------------------------- | ----------------- | ------- |
| Amazon Web Services        | EKS(Elastic Kubernetes Service) | v1.18.9  |
| Google Cloud Platform      | GKE(Google Kubernetes Engine) | v1.17.15 |

## <a name="scenarios-validated"></a>시나리오 유효성 검사

Azure Arc 사용 Kubernetes 유효성 검사의 일부로 준수 테스트를 실행 하는 시나리오는 다음과 같습니다.

1. Kubernetes 클러스터를 Azure Arc에 연결: 
    * 클러스터에 Azure Arc 사용 Kubernetes 에이전트 투구 차트를 배포 합니다.
    * 클러스터에서 MSI(관리형 시스템 ID) 인증서를 설정합니다.
    * 에이전트는 클러스터 메타데이터를 Azure에 보냅니다.

2. 구성: 
    * Azure Arc 사용 Kubernetes 리소스 위에 구성을 만듭니다.
    * GitOps 워크플로를 설정하는 데 필요한 [Flux](https://docs.fluxcd.io/)가 클러스터에 배포됩니다.
    * Flux는 데모 Git 리포지토리에서 매니페스트 및 Helm 차트를 풀(pull)하여 클러스터에 배포합니다.

## <a name="next-steps"></a>다음 단계

클러스터를 Azure Arc에 연결하는 방법을 알아봅니다.
> [!div class="nextstepaction"]
> [클러스터를 Azure Arc에 연결](./quickstart-connect-cluster.md)
