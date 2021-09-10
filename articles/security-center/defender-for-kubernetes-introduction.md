---
title: Azure Defender for Kubernetes - 이점 및 특징
description: Azure Defender for Kubernetes의 이점 및 특징에 대해 알아봅니다.
author: memildin
ms.author: memildin
ms.date: 07/20/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 53c5ef179a3df860d97cbe31287f2defcec2411b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121750711"
---
# <a name="introduction-to-azure-defender-for-kubernetes"></a>Azure Defender for Kubernetes 소개

Azure Defender for Kubernetes는 Kubernetes 클러스터를 실행하는 모든 곳에서 보호할 수 있는 Azure Defender 계획입니다. 

다음에서 클러스터를 방어할 수 있습니다.

- **AKS(Azure Kubernetes Service)** - 컨테이너화된 애플리케이션 개발, 배포 및 관리를 위한 Microsoft의 관리형 서비스

- **온-프레미스 및 다중 클라우드 환경** - [Arc 지원 Kubernetes에 대한 확장](defender-for-kubernetes-azure-arc.md) 사용

Azure Security Center 및 AKS는 [Security Center의 컨테이너 보안](container-security.md)에 설명된 대로 환경 강화, 워크로드 보호 및 런타임 보호를 제공하는 클라우드 네이티브 Kubernetes 보안 제품을 구성합니다.

[서버용 Azure Defender](defender-for-servers-introduction.md) 및 Log Analytics 에이전트를 사용하도록 설정하면 Linux AKS 노드에 호스트 수준 위협 탐지를 사용할 수 있습니다. 그러나 현재는 클러스터가 Azure Kubernetes Service VMSS(가상 머신 확장 집합)에 배포된 경우에는 Log Analytics 에이전트가 지원되지 않습니다.



## <a name="availability"></a>가용성

|양상|세부 정보|
|----|:----|
|릴리스 상태:|GA(일반 공급)|
|가격 책정:|**Azure Defender for Kubernetes** 는 [Security Center 가격 책정](https://azure.microsoft.com/pricing/details/security-center/)에 표시된 대로 요금이 청구됩니다.|
|필요한 역할 및 권한:|**보안 관리자** 는 경고를 해제할 수 있습니다.<br>**보안 읽기 권한자** 는 발견 사항을 볼 수 있습니다.|
|클라우드:|:::image type="icon" source="./media/icons/yes-icon.png"::: 상용 클라우드<br>:::image type="icon" source="./media/icons/yes-icon.png"::: 국가/소버린(Azure Government, Azure 중국 21Vianet)|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-kubernetes"></a>Azure Defender for Kubernetes의 이점은?

Azure Defender for Kubernetes는 클러스터의 로그를 모니터링하여 **클러스터 수준 위협 보호** 를 제공합니다.

Azure Defender for Kubernetes가 모니터링하는 보안 이벤트의 예로는 노출된 Kubernetes 대시보드, 높은 권한이 있는 역할 만들기, 중요한 탑재 만들기 등이 있습니다. 클러스터 수준 경고의 전체 목록은 [경고 참조 표](alerts-reference.md#alerts-k8scluster)에서 확인할 수 있습니다.

> [!TIP]
> [이 블로그 게시물](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270)의 지침에 따라 컨테이너 경고를 시뮬레이션할 수 있습니다.

또한 글로벌 보안 연구 팀에서는 위협 환경을 지속적으로 모니터링합니다. 검색된 컨테이너 관련 경고 및 취약성을 추가합니다.

>[!NOTE]
> Azure Defender는 구독에서 Defender for Kubernetes 계획을 사용하도록 설정한 후에 발생하는 작업 및 배포에 대한 보안 경고를 생성합니다.




## <a name="faq---azure-defender-for-kubernetes"></a>FAQ - Azure Defender for Kubernetes

- [Log Analytics 에이전트 없이도 여전히 클러스터 보호를 받을 수 있나요?](#can-i-still-get-cluster-protections-without-the-log-analytics-agent)
- [AKS를 사용하면 사용자 지정 VM 확장을 내 AKS 노드에 설치할 수 있나요?](#does-aks-allow-me-to-install-custom-vm-extensions-on-my-aks-nodes)
- [내 클러스터에서 컨테이너 에이전트용 Azure Monitor를 이미 실행하고 있는 경우 Log Analytics 에이전트도 필요한가요?](#if-my-cluster-is-already-running-an-azure-monitor-for-containers-agent-do-i-need-the-log-analytics-agent-too)
- [Azure Defender for Kubernetes는 VMSS 노드에서 AKS를 지원하나요?](#does-azure-defender-for-kubernetes-support-aks-with-vmss-nodes)

### <a name="can-i-still-get-cluster-protections-without-the-log-analytics-agent"></a>Log Analytics 에이전트 없이도 여전히 클러스터 보호를 받을 수 있나요?

**Azure Defender for Kubernetes** 플랜은 클러스터 수준에서 보호를 제공합니다. **서버용 Azure Defender** 의 Log Analytics 에이전트까지 배포하면 노드에 대한 위협 방지가 해당 플랜과 함께 제공됩니다. [서버용 Azure Defender 소개](defender-for-servers-introduction.md)에서 자세히 알아보세요.

가능하면 가장 완전한 보호를 위해 둘 다 배포하는 것이 좋습니다.

에이전트를 호스트에 설치하지 않도록 선택하는 경우 위협 방지 혜택 및 보안 경고를 일부만 받게 됩니다. 네트워크 분석 및 악의적인 서버와의 통신과 관련된 경고는 계속 받을 수 있습니다.

### <a name="does-aks-allow-me-to-install-custom-vm-extensions-on-my-aks-nodes"></a>AKS를 사용하면 사용자 지정 VM 확장을 내 AKS 노드에 설치할 수 있나요?
Azure Defender에서 AKS 노드를 모니터링하려면 Log Analytics 에이전트를 실행해야 합니다.

AKS는 관리형 서비스이며, Log Analytics 에이전트가 Microsoft에서 관리하는 확장이므로 AKS 클러스터에서도 지원됩니다.

### <a name="if-my-cluster-is-already-running-an-azure-monitor-for-containers-agent-do-i-need-the-log-analytics-agent-too"></a>내 클러스터에서 컨테이너 에이전트용 Azure Monitor를 이미 실행하고 있는 경우 Log Analytics 에이전트도 필요한가요?
Azure Defender에서 노드를 모니터링하려면 Log Analytics 에이전트를 실행해야 합니다.

클러스터에서 컨테이너용 Azure Monitor 에이전트를 이미 실행하고 있는 경우 Log Analytics 에이전트도 설치할 수 있으며 두 에이전트가 문제없이 서로 간에 함께 작업할 수 있습니다.

[컨테이너 에이전트용 Azure Monitor에 대해 자세히 알아보세요](../azure-monitor/containers/container-insights-manage-agent.md).


### <a name="does-azure-defender-for-kubernetes-support-aks-with-vmss-nodes"></a>Azure Defender for Kubernetes는 VMSS 노드에서 AKS를 지원하나요?
현재는 클러스터가 Azure Kubernetes Service VMSS(가상 머신 확장 집합)에 배포된 경우에는 Log Analytics 에이전트가 지원되지 않습니다.



## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Defender for Kubernetes를 비롯한 Security Center의 Kubernetes 보호에 대해 알아보았습니다. 

> [!div class="nextstepaction"]
> [Azure Defender 사용](enable-azure-defender.md)

관련 자료는 다음 문서를 참조하세요. 

- [SIEM, SOAR 또는 IT 서비스 관리 솔루션에 대한 경고 스트리밍](export-to-siem.md)
- [경고 참조 테이블](alerts-reference.md)
