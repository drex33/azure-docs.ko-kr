---
title: Azure Defender for Kubernetes - 이점 및 기능
description: Microsoft Defender for Kubernetes의 이점 및 기능에 대해 알아봅니다.
author: memildin
ms.author: memildin
ms.date: 11/23/2021
ms.topic: overview
ms.service: defender-for-cloud
manager: rkarlin
ms.openlocfilehash: 8f9199f263f2244fed19efe49a3dea89500b3005
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133061771"
---
# <a name="introduction-to-microsoft-defender-for-kubernetes"></a>Microsoft Defender for Kubernetes 소개

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Defender for Cloud는 [Defender for Cloud의 컨테이너 보안](container-security.md)에 설명된 대로 환경 강화, 워크로드 보호 및 런타임 보호를 제공합니다.

Defender for Kubernetes는 다음에서 실행되는지 여부에 관계없이 Kubernetes 클러스터를 보호합니다.

- **AKS(Azure Kubernetes Service)** - 컨테이너화된 애플리케이션 개발, 배포 및 관리를 위한 Microsoft의 관리형 서비스입니다.

- **연결된 AWS(Amazon Web Services) 계정의 Amazon EKS(Elastic Kubernetes Service)** (미리 보기) - 자체 Kubernetes 컨트롤 플레인 또는 노드를 설치, 운영 및 유지 관리하지 않고도 AWS에서 Kubernetes를 실행하기 위한 Amazon의 관리형 서비스입니다.

- **관리되지 않는 Kubernetes 배포** - 온-프레미스 또는 IaaS에 있는 CNCF(Cloud Native Computing Foundation) 인증 Kubernetes 클러스터입니다. [온-프레미스 및 다중 클라우드 환경에서 실행되는 Azure Arc 지원 Kubernetes 클러스터 방어](defender-for-kubernetes-azure-arc.md)에서 자세히 알아보세요.

[서버용 Microsoft Defender](defender-for-servers-introduction.md) 및 Log Analytics 에이전트를 사용하도록 설정하면 Linux AKS 노드에 호스트 수준 위협 탐지를 사용할 수 있습니다. 그러나 현재는 클러스터가 Azure Kubernetes Service 가상 머신 확장 집합에 배포된 경우에는 Log Analytics 에이전트가 지원되지 않습니다.


## <a name="availability"></a>가용성

|양상|세부 정보|
|----|:----|
|릴리스 상태:|GA(일반 공급)<br>EKS 클러스터에 대한 보호는 미리 보기로 제공됩니다. [!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)]|
|가격 책정:|**Microsoft Defender for Kubernetes** 는 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/security-center/)에 표시된 대로 요금이 청구됩니다.<br>연결된 AWS 계정의 EKS 클러스터에 대한 **컨테이너 계획** 은 미리 보기 상태인 동안 무료입니다.|
|필요한 역할 및 권한:|**보안 관리자** 는 경고를 해제할 수 있습니다.<br>**보안 읽기 권한자** 는 발견 사항을 볼 수 있습니다.|
|클라우드:|:::image type="icon" source="./media/icons/yes-icon.png"::: 상용 클라우드<br>:::image type="icon" source="./media/icons/yes-icon.png"::: 국가(Azure Government, Azure 중국 21Vianet)<br>:::image type="icon" source="./media/icons/yes-icon.png"::: 연결된 AWS 계정(미리 보기)|
|||

## <a name="what-are-the-benefits-of-microsoft-defender-for-kubernetes"></a>Microsoft Defender for Kubernetes의 이점은 무엇인가요?

글로벌 보안 연구 팀에서는 위협 환경을 지속적으로 모니터링합니다. 컨테이너별 경고 및 취약성이 발견되면 이러한 연구원이 위협 인텔리전스 피드에 추가하고 Defender for Cloud는 사용자 환경과 관련된 모든 경고를 알려줍니다.

또한 Microsoft Defender for Kubernetes는 클러스터의 로그를 모니터링하여 **클러스터 수준 위협 보호** 를 제공합니다. 즉, 구독에서 Defender for Kubernetes를 사용하도록 설정한 *후* 에 발생하는 작업 및 배포에 대해서만 보안 경고가 트리거됩니다.

> [!TIP]
> EKS 기반 클러스터의 경우 컨트롤 플레인 감사 로그를 모니터링합니다. 컨테이너 계획 구성에서 사용하도록 설정됩니다. :::image type="content" source="media/defender-for-kubernetes-intro/eks-audit-logs-enabled.png" alt-text="감사 로그가 활성화된 AWS 커넥터의 컨테이너 계획 스크린샷.":::

Microsoft Defender for Kubernetes에서 모니터링하는 보안 이벤트의 예는 다음과 같습니다.

- 노출된 Kubernetes 대시보드
- 높은 권한 있는 역할 만들기
- 중요한 탑재 만들기.

클러스터 수준 경고의 전체 목록은 [경고 참조 표](alerts-reference.md#alerts-k8scluster)에서 확인할 수 있습니다.


## <a name="protect-azure-kubernetes-service-aks-clusters"></a>AKS(Azure Kubernetes Service) 클러스터 보호

AKS 클러스터를 보호하려면 관련 구독에서 Defender 계획을 사용하도록 설정합니다.

1. Defender for Cloud 메뉴에서 **환경 설정** 을 엽니다.
1. 관련 구독을 선택합니다.
1. **Defender 계획** 페이지에서 Microsoft Defender for Kubernetes의 상태를 **켜기** 로 설정합니다.

    :::image type="content" source="media/defender-for-kubernetes-intro/enable-defender-for-kubernetes.png" alt-text="사용 중인 Microsoft Defender for Kubernetes 계획의 스크린샷.":::

1. **저장** 을 선택합니다.

## <a name="protect-amazon-elastic-kubernetes-service-clusters"></a>Amazon Elastic Kubernetes Service 클러스터 보호

> [!IMPORTANT]
> AWS 계정을 아직 연결하지 않은 경우 이제 [AWS 계정을 Microsoft Defender for Cloud에 연결](quickstart-onboard-aws.md)의 지침을 사용하여 다음 3단계로 건너뜁니다.

EKS 클러스터를 보호하려면 관련 계정 커넥터에서 컨테이너 계획을 사용하도록 설정합니다.

1. Defender for Cloud 메뉴에서 **환경 설정** 을 엽니다.
1. AWS 커넥터를 선택합니다.

    :::image type="content" source="media/defender-for-kubernetes-intro/select-aws-connector.png" alt-text="AWS 커넥터를 보여주는 Defender for Cloud의 환경 설정 페이지 스크린샷.":::

1. **컨테이너** 계획의 토글을 **켜기** 로 설정합니다.

    :::image type="content" source="media/defender-for-kubernetes-intro/enable-containers-plan-on-aws-connector.png" alt-text="AWS 커넥터에 Defender for Containers를 사용하도록 설정하는 스크린샷.":::

1. 필요에 따라 감사 로그의 보존 기간을 변경하려면 **구성** 을 선택하고 원하는 기간을 입력한 다음, **저장** 을 선택합니다.

    :::image type="content" source="media/defender-for-kubernetes-intro/adjust-eks-logs-retention.png" alt-text="EKS 제어 창 로그의 보존 기간 조정의 스크린샷." lightbox="./media/defender-for-kubernetes-intro/adjust-eks-logs-retention.png":::

1. 커넥터 마법사의 나머지 페이지를 계속 진행합니다.

1. Azure Arc 지원 Kubernetes 및 Defender 확장은 EKS 클러스터에 설치되고 실행되어야 합니다. 전용 Defender for Cloud 권장 사항은 확장을 배포하고 필요한 경우 Arc를 배포합니다.

    1. Defender for Cloud의 **권장 사항** 페이지에서 **EKS 클러스터에 Azure Arc에 대한 Azure Defender의 확장이 설치되어 있어야 함** 을 검색합니다.
    1. 비정상 클러스터를 선택합니다.

        > [!IMPORTANT]
        > 클러스터를 한 번에 하나씩 선택해야 합니다.
        >
        > 하이퍼링크된 이름으로 클러스터를 선택하지 마세요. 관련 행의 다른 곳을 선택합니다.

    1. **수정** 을 선택합니다.
    1. Defender for Cloud는 원하는 언어로 스크립트를 생성합니다. Bash(Linux의 경우) 또는 PowerShell(Windows의 경우)을 선택합니다.
    1. **수정 논리 다운로드** 를 선택합니다.
    1. 클러스터에서 생성된 스크립트를 실행합니다. 

    :::image type="content" source="media/defender-for-kubernetes-intro/generate-script-defender-extension-kubernetes.gif" alt-text="Defender for Cloud 권장 사항을 사용하여 Azure Arc 확장을 사용하도록 설정하는 EKS 클러스터에 대한 스크립트를 생성하는 방법에 대한 비디오입니다.":::

### <a name="view-recommendations-and-alerts-for-your-eks-clusters"></a>EKS 클러스터에 대한 권장 사항 및 경고 보기

> [!TIP]
> [이 블로그 게시물](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270)의 지침에 따라 컨테이너 경고를 시뮬레이션할 수 있습니다.

EKS 클러스터에 대한 경고 및 권장 사항을 보려면 경고, 권장 사항 및 인벤토리 페이지에 대한 필터를 사용하여 리소스 유형 **AWS EKS 클러스터** 를 기준으로 필터링합니다.

:::image type="content" source="media/defender-for-kubernetes-intro/view-alerts-for-aws-eks-clusters.png" alt-text="Microsoft Defender for Cloud의 경고 페이지에서 필터를 사용하여 AWS EKS 클러스터와 관련된 경고를 보는 방법의 스크린샷." lightbox="./media/defender-for-kubernetes-intro/view-alerts-for-aws-eks-clusters.png":::

## <a name="faq---microsoft-defender-for-kubernetes"></a>FAQ - Microsoft Defender for Kubernetes

- [Log Analytics 에이전트 없이도 여전히 클러스터 보호를 받을 수 있나요?](#can-i-still-get-cluster-protections-without-the-log-analytics-agent)
- [AKS를 사용하면 사용자 지정 VM 확장을 내 AKS 노드에 설치할 수 있나요?](#does-aks-allow-me-to-install-custom-vm-extensions-on-my-aks-nodes)
- [내 클러스터에서 컨테이너 에이전트용 Azure Monitor를 이미 실행하고 있는 경우 Log Analytics 에이전트도 필요한가요?](#if-my-cluster-is-already-running-an-azure-monitor-for-containers-agent-do-i-need-the-log-analytics-agent-too)
- [Microsoft Defender for Kubernetes는 가상 머신 확장 집합 노드의 AKS을 지원하나요?](#does-microsoft-defender-for-kubernetes-support-aks-with-virtual-machine-scale-set-nodes)

### <a name="can-i-still-get-cluster-protections-without-the-log-analytics-agent"></a>Log Analytics 에이전트 없이도 여전히 클러스터 보호를 받을 수 있나요?

**Microsoft Defender for Kubernetes** 는 클러스터 수준에서 보호를 제공합니다. **서버용 Microsoft Defender** 의 Log Analytics 에이전트까지 배포하면 노드에 대한 위협 방지가 해당 플랜과 함께 제공됩니다. [서버용 Microsoft Defender 소개](defender-for-servers-introduction.md)에서 자세히 알아보세요.

가능하면 가장 완전한 보호를 위해 둘 다 배포하는 것이 좋습니다.

에이전트를 호스트에 설치하지 않도록 선택하는 경우 위협 방지 혜택 및 보안 경고를 일부만 받게 됩니다. 네트워크 분석 및 악의적인 서버와의 통신과 관련된 경고는 계속 받을 수 있습니다.

### <a name="does-aks-allow-me-to-install-custom-vm-extensions-on-my-aks-nodes"></a>AKS를 사용하면 사용자 지정 VM 확장을 내 AKS 노드에 설치할 수 있나요?

Defender for Cloud에서 AKS 노드를 모니터링하려면 Log Analytics 에이전트를 실행해야 합니다.

AKS는 관리형 서비스이며, Log Analytics 에이전트가 Microsoft에서 관리하는 확장이므로 AKS 클러스터에서도 지원됩니다. 그러나 현재는 클러스터가 Azure Kubernetes Service 가상 머신 확장 집합에 배포된 경우에는 Log Analytics 에이전트가 지원되지 않습니다.

### <a name="if-my-cluster-is-already-running-an-azure-monitor-for-containers-agent-do-i-need-the-log-analytics-agent-too"></a>내 클러스터에서 컨테이너 에이전트용 Azure Monitor를 이미 실행하고 있는 경우 Log Analytics 에이전트도 필요한가요?

Defender for Cloud에서 노드를 모니터링하려면 Log Analytics 에이전트를 실행해야 합니다.

클러스터에서 컨테이너용 Azure Monitor 에이전트를 이미 실행하고 있는 경우 Log Analytics 에이전트도 설치할 수 있으며 두 에이전트가 문제없이 서로 간에 함께 작업할 수 있습니다.

[컨테이너 에이전트용 Azure Monitor에 대해 자세히 알아보세요](../azure-monitor/containers/container-insights-manage-agent.md).

### <a name="does-microsoft-defender-for-kubernetes-support-aks-with-virtual-machine-scale-set-nodes"></a>Microsoft Defender for Kubernetes는 가상 머신 확장 집합 노드의 AKS을 지원하나요?

현재는 클러스터가 Azure Kubernetes Service 가상 머신 확장 집합에 배포된 경우에는 Log Analytics 에이전트가 지원되지 않습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Microsoft Defender for Kubernetes를 포함하여 Defender for Cloud의 Kubernetes 보호에 대해 알아보았습니다.

> [!div class="nextstepaction"]
> [향상된 보호 사용](enable-enhanced-security.md)

관련 자료는 다음 문서를 참조하세요.

- [SIEM, SOAR 또는 IT 서비스 관리 솔루션에 대한 경고 스트리밍](export-to-siem.md)
- [경고 참조 테이블](alerts-reference.md)
