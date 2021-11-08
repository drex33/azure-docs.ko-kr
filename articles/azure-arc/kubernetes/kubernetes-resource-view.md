---
title: Azure Portal에서 Kubernetes 리소스에 액세스
services: azure-arc
ms.service: azure-arc
ms.date: 10/31/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Kubernetes 리소스와 상호 작용 하 여 Azure Portal에서 Azure Arc 사용 Kubernetes 클러스터를 관리 하는 방법에 대해 알아봅니다.
ms.openlocfilehash: 5af26c853df67b213b1951224ae9603d15a9dd25
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2021
ms.locfileid: "132000808"
---
# <a name="access-kubernetes-resources-from-azure-portal"></a>Azure Portal에서 Kubernetes 리소스에 액세스

Azure Portal에는 Azure Arc 사용 Kubernetes 클러스터의 Kubernetes 리소스에 쉽게 액세스할 수 있는 Kubernetes 리소스 뷰가 포함 되어 있습니다. Azure Portal에서 Kubernetes 리소스를 보면 Azure Portal과 `kubectl` 명령줄 도구 사이의 컨텍스트 전환이 감소하여 Kubernetes 리소스를 보고 편집하는 환경이 간소화됩니다. 리소스 보기에는 현재 여러 리소스 종류(예: 배포, Pod 및 복제본 세트)가 포함되어 있습니다.

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>필수 조건

- Azure Arc 사용 Kubernetes 리소스로 Azure에 [연결](quickstart-connect-cluster.md) 된 기존 Kubernetes 클러스터

- Azure Arc 사용 Kubernetes 클러스터에서 [클러스터 커넥트 기능을 사용 하도록 설정 해야](cluster-connect.md#enable-cluster-connect-feature) 합니다.

- 클러스터에 대 한 인증을 위한 [서비스 계정 토큰](cluster-connect.md#service-account-token-authentication-option) 입니다.

## <a name="view-kubernetes-resources"></a>Kubernetes 리소스 보기

Kubernetes 리소스를 보려면 Azure Portal에서 AKS 클러스터로 이동합니다. 왼쪽의 탐색 창은 리소스에 액세스하는 데 사용됩니다. 리소스에는 다음이 포함됩니다.

- **네임스페이스** 에는 클러스터의 네임스페이스가 표시됩니다. 네임스페이스 목록의 맨 위에 있는 필터는 네임스페이스 리소스를 신속하게 필터링 및 표시하는 방법을 제공합니다.
- **워크로드** 에는 배포, Pod, 복제본 세트, 상태 저장 세트, 디먼 세트, 클러스터에 배포된 cron 작업 관련 정보가 표시됩니다.
- **서비스 및 수신** 은 클러스터의 서비스와 수신 리소스를 모두 표시합니다.
- **스토리지** 는 Azure 스토리지 클래스 및 영구 볼륨 정보를 표시합니다.
- **구성** 은 클러스터의 구성 맵 및 비밀을 표시합니다.

[![Azure Portal ](media/kubernetes-resource-view/workloads.png) 에 표시 되는 작업 정보 Kubernetes ](media/kubernetes-resource-view/workloads.png#lightbox)

## <a name="edit-yaml"></a>YAML 편집

Kubernetes 리소스 보기에는 YAML 편집기도 있습니다. 기본 제공 YAML 편집기는 포털 내에서 Kubernetes 개체를 업데이트 하 고 변경 내용을 즉시 적용할 수 있음을 의미 합니다.

YAML을 편집한 후에는 **검토 + 저장** 을 선택하고 변경 내용을 확인한 후 다시 저장하면 변경 내용이 적용됩니다.

[![Azure Portal ](media/kubernetes-resource-view/yaml-editor.png) 에 표시 되는 Kubernetes 개체에 대 한 yaml 편집기 ](media/kubernetes-resource-view/yaml-editor.png#lightbox)

>[!WARNING]
> UI 나 CLI를 통해 직접 프로덕션 변경을 수행 하는 것은 권장 되지 않으며 프로덕션 환경에 대해 [구성 (GitOps)](tutorial-use-gitops-connected-cluster.md) 사용을 고려해 야 합니다. Azure Portal Kubernetes 관리 기능 및 YAML 편집기는 개발 및 테스트 설정에서 새 배포를 학습 하 고이를 위한 새 배포를 위해 작성 되었습니다.

## <a name="next-steps"></a>다음 단계

컨테이너에 대 한 Azure Monitor는이 문서에 설명 된 Kubernetes 리소스를 사용 하 여 사용할 수 있는 Kubernetes 리소스 보기와 비교할 때 클러스터의 노드 및 컨테이너에 대 한 자세한 정보를 제공 합니다. 클러스터의 [컨테이너에 대 한 Azure Monitor를 배포](../../azure-monitor/containers/container-insights-enable-arc-enabled-clusters.md?toc=/azure/azure-arc/kubernetes/toc.json) 하는 방법을 알아봅니다.