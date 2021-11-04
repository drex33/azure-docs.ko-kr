---
title: Azure Arc 사용 Kubernetes 에이전트 아키텍처
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: 이 문서에서는 Azure Arc 사용 Kubernetes 에이전트의 아키텍처 개요를 제공 합니다.
keywords: Kubernetes, Arc, Azure, 컨테이너
ms.openlocfilehash: 6bb1b035e1c64e82f89804928854ca019ad22201
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131450840"
---
# <a name="azure-arc-enabled-kubernetes-agent-architecture"></a>Azure Arc 사용 Kubernetes 에이전트 아키텍처

단독으로 [Kubernetes](https://kubernetes.io/)는 하이브리드 및 다중 클라우드 환경에서 컨테이너화된 워크로드를 일관되게 배포할 수 있습니다. 그러나 Azure Arc 사용 Kubernetes은 다른 유형의 환경에서 정책, 거 버 넌 스 및 보안을 관리 하는 중앙 집중식으로 일관 된 제어 평면으로 작동 합니다. 이 문서에서는 다음을 제공합니다.

* 클러스터를 Azure Arc에 연결하는 작업의 아키텍처 개요.
* 에이전트가 수행하는 연결 패턴.
* 클러스터 환경과 Azure 간에 교환되는 데이터에 관한 설명.

## <a name="deploy-agents-to-your-cluster"></a>클러스터에 에이전트 배포

대부분 온-프레미스 데이터 센터는 네트워크 경계 방화벽에서 인바운드 통신을 차단하는 엄격한 네트워크 규칙을 적용합니다. Azure Arc 사용 Kubernetes는 방화벽에서 인바운드 포트를 요구 하지 않고 아웃 바운드 통신을 위해 선택적 송신 끝점만 사용 하도록 설정 하 여 이러한 제한에 따라 작동 합니다. Azure Arc 사용 Kubernetes 에이전트는이 아웃 바운드 통신을 시작 합니다. 

![아키텍처 개요](./media/architectural-overview.png)

### <a name="connect-a-cluster-to-azure-arc"></a>클러스터를 Azure Arc에 연결

1. 선택한 인프라(VMware vSphere, Amazon Web Services, Google Cloud Platform 등)에서 Kubernetes 클러스터를 만듭니다. 

    > [!NOTE]
    > Azure Arc 사용 Kubernetes 현재 Azure Arc에 기존 Kubernetes 클러스터를 연결 하는 것만 지원 하므로 고객은 Kubernetes 클러스터 자체의 수명 주기를 만들고 관리 해야 합니다.  

1. Azure CLI를 사용하여 클러스터에 대한 Azure Arc 등록을 시작합니다.
    * Azure CLI는 Helm을 사용하여 클러스터에 에이전트 Helm 차트를 배포합니다.
    * 클러스터 노드는 [Microsoft Container Registry](https://github.com/microsoft/containerregistry)에 대한 아웃바운드 통신을 시작하고 `azure-arc` 네임스페이스에 다음 에이전트를 만드는 데 필요한 이미지를 끌어옵니다.

        | 에이전트 | Description |
        | ----- | ----------- |
        | `deployment.apps/clusteridentityoperator` | Azure Arc 사용 Kubernetes 현재 [시스템 할당 id](../../active-directory/managed-identities-azure-resources/overview.md)만 지원 합니다. `clusteridentityoperator`는 첫 번째 아웃바운드 통신을 시작합니다. 첫 번째 통신은 다른 에이전트가 Azure와 통신하기 위해 사용하는 MSI(관리되는 서비스 ID) 인증서를 가져옵니다. |
        | `deployment.apps/config-agent` | 클러스터에 적용된 원본 제어 구성 리소스의 연결된 클러스터를 감시합니다. 준수 상태를 업데이트합니다. |
        | `deployment.apps/controller-manager` | Azure Arc 구성 요소 간에 상호 작용을 오케스트레이션하는 연산자의 연산자입니다. |    
        | `deployment.apps/metrics-agent` | 다른 Arc 에이전트의 메트릭을 수집하여 최적 성능을 확인합니다. |
        | `deployment.apps/cluster-metadata-operator` | 클러스터 버전, 노드 수 및 Azure Arc 에이전트 버전을 포함한 클러스터 메타데이터를 수집합니다. |
        | `deployment.apps/resource-sync-agent` | 위에서 언급한 클러스터 메타데이터를 Azure에 동기화합니다. |
        | `deployment.apps/flux-logs-agent` | 원본 제어 구성의 일부로 배포된 flux 연산자에서 로그를 수집합니다. |
        | `deployment.apps/extension-manager` | 확장 Helm 차트의 수명 주기를 설치 및 관리합니다. |  
        | `deployment.apps/clusterconnect-agent` | 클러스터 연결 기능을 사용하여 클러스터의 `apiserver`에 대한 액세스를 제공하는 역방향 프록시 에이전트입니다. 클러스터에서 `cluster-connect` 기능이 사용되는 경우에만 배포되는 선택적 구성 요소입니다.   |
        | `deployment.apps/guard` | AAD RBAC 기능에 사용되는 인증 및 권한 부여 웹후크 서버입니다. 클러스터에서 `azure-rbac` 기능이 사용되는 경우에만 배포되는 선택적 구성 요소입니다.   |

1. 모든 Azure Arc 사용 Kubernetes agent pod가 `Running` 상태 이면 클러스터가 Azure arc에 연결 되어 있는지 확인 합니다. 다음이 표시 됩니다.
    * [Azure Resource Manager](../../azure-resource-manager/management/overview.md)의 Azure Arc 사용 Kubernetes 리소스입니다. Azure는 실제 Kubernetes 클러스터 자체가 아닌 고객 관리형 Kubernetes 클러스터의 프로젝션으로 이 리소스를 추적합니다.
    * 클러스터 메타 데이터 (예: Kubernetes 버전, 에이전트 버전 및 노드 수)는 Azure Arc 사용 Kubernetes 리소스에 메타 데이터로 표시 됩니다.

## <a name="data-exchange-between-cluster-environment-and-azure"></a>클러스터 환경과 Azure 간 데이터 교환

| 데이터 형식 | 시나리오 | 통신 모드 |
| --------- | -------- | ------------------ |
| Kubernetes 클러스터 버전 | 클러스터 메타데이터 | Azure에 대한 에이전트 밀어넣기 |
| 클러스터의 총 노드 수 | 클러스터 메타데이터 | Azure에 대한 에이전트 밀어넣기 |
| 에이전트 버전 | 클러스터 메타데이터 | Azure에 대한 에이전트 밀어넣기 |
| Kubernetes 배포 유형 | 클러스터 메타데이터 | Azure에 대한 Azure CLI 밀어넣기 |
| 인프라 형식(AWS/GCP/vSphere/...) | 클러스터 메타데이터 | Azure에 대한 Azure CLI 밀어넣기 |
| 클러스터에 있는 노드의 vCPU 수 | 결제 | Azure에 대한 Azure CLI 밀어넣기 |
| 에이전트 하트비트 | 리소스 상태 | Azure에 대한 에이전트 밀어넣기 |
| 에이전트별 리소스 사용량(메모리/CPU) | 진단 및 지원 가능성 | Azure에 대한 에이전트 밀어넣기 |
| 모든 에이전트 컨테이너의 로그 | 진단 및 지원 가능성 | Azure에 대한 에이전트 밀어넣기 |
| 에이전트 업그레이드 가용성 | 에이전트 업그레이드 | Azure에서 에이전트 끌어오기 |
| 필요한 구성 상태: Git 리포지토리 URL, flux 연산자 매개 변수, 프라이빗 키, 알려진 호스트 콘텐츠, HTTPS 사용자 이름, 토큰 또는 암호 | 구성 | Azure에서 에이전트 끌어오기 |
| Flux 운영자 설치 상태 | 구성 | Azure에 대한 에이전트 밀어넣기 |
| 클러스터 내에서 Gatekeeper를 적용해야 하는 Azure Policy 할당 | Azure Policy | Azure에서 에이전트 끌어오기 |
| 클러스터 내 정책 적용의 감사 및 준수 상태 | Azure Policy | Azure에 대한 에이전트 밀어넣기 |
| 고객 워크로드의 메트릭 및 로그 | Azure Monitor | 고객의 테넌트 및 구독에서 Log Analytics 작업 영역 리소스에 대한 에이전트 밀어넣기 |

## <a name="connectivity-status"></a>연결 상태

| 상태 | 설명 |
| ------ | ----------- |
| Connecting | Azure Arc 사용 Kubernetes 리소스가 Azure Resource Manager에서 만들어지지만 서비스에서 에이전트 하트 비트를 아직 받지 못했습니다. |
| 연결됨 | Azure Arc 사용 Kubernetes 서비스가 지난 15 분 내에 에이전트 하트 비트를 받았습니다. |
| 오프라인 | Azure Arc 사용 Kubernetes 리소스가 이전에 연결 되었지만 서비스가 15 분 동안 에이전트 하트 비트를 받지 못했습니다. |
| 만료됨 | MSI 인증서가 발급된 후 90일의 만료 기간이 있습니다. 이 인증서가 만료되면 리소스가 `Expired`로 간주되며 모든 기능(예: 구성, 모니터링 및 정책)이 해당 클러스터에서 작동을 중지합니다. 만료된 Azure Arc 지원 Kubernetes 리소스를 해결하는 방법에 대한 자세한 내용은 [FAQ 문서에서](./faq.md#how-to-address-expired-azure-arc-enabled-kubernetes-resources)찾을 수 있습니다. |

## <a name="understand-connectivity-modes"></a>연결 모드 이해

| 연결 모드 | 설명 |
| ----------------- | ----------- |
| 완전히 연결됨 | 에이전트는 GitOps 구성 전파, Azure Policy와 Gatekeeper 정책 적용, Azure Monitor의 워크로드 메트릭 및 로그 수집에서 약간 지연을 사용하여 Azure와 일관되게 통신할 수 있습니다. |
| 반연결됨 | `clusteridentityoperator`가 끌어온 MSI 인증서는 인증서가 만료되기까지 최대 90일 동안 유효합니다. 만료되면 Azure Arc 지원 Kubernetes 리소스의 작동이 중지됩니다. 클러스터의 모든 Azure Arc 기능을 다시 활성화하려면 Azure Arc 지원 Kubernetes 리소스 및 에이전트를 삭제하고 다시 만듭니다. 90일 동안 30일마다 한 번 이상 클러스터를 연결합니다. |
| 연결 끊김 | 연결이 끊긴 환경에서 Azure에 액세스할 수 없는 Kubernetes 클러스터는 현재 Azure Arc 지원되는 Kubernetes에서 지원되지 않습니다. 이 기능에 관심이 있는 경우 [Azure Arc의 UserVoice 포럼](https://feedback.azure.com/d365community/forum/5c778dec-0625-ec11-b6e6-000d3a4f0858)에서 아이디어를 제출하거나 아이디어에 투표합니다.

## <a name="next-steps"></a>다음 단계

* 빠른 시작을 통해 [Kubernetes 클러스터를 Azure Arc에 연결](./quickstart-connect-cluster.md)합니다.
* [Azure Arc 사용하도록 설정된 Kubernetes를](./conceptual-configurations.md)사용하여 구성 리소스로 클러스터와 Git 리포지토리 간에 연결을 만드는 방법에 대해 자세히 알아봅니다.
