---
title: 클라우드용 Microsoft Defender 및 Microsoft Defender를 사용하여 컨테이너 보안
description: 클라우드용 Microsoft Defender의 컨테이너 보안 기능에 대해 알아보기
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 04/06/2021
ms.author: memildin
ms.custom: ignite-fall-2021
ms.openlocfilehash: e3b839d572437e3fd9677ebabb111de68ef4d005
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131010090"
---
# <a name="container-security-in-microsoft-defender-for-cloud"></a>클라우드용 Microsoft Defender의 컨테이너 보안

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

클라우드용 Microsoft Defender는 컨테이너를 보호하기 위한 클라우드 네이티브 솔루션입니다.

Defender for Cloud는 다음과 같은 컨테이너 리소스 종류를 보호할 수 있습니다.

| 리소스 종류 | Defender for Cloud에서 제공하는 보호 |
|:--------------------:|-----------|
| ![Kubernetes 서비스.](./media/container-security/icon-kubernetes-service-rec.png)<br>**Kubernetes 클러스터** | 클러스터를 지속적으로 평가하여 잘못된 구성에 대한 가시성과 식별된 위협을 완화할 수 있는 지침을 제공합니다. [보안 권장 사항을 통한 환경 강화](#environment-hardening)에 대해 자세히 알아보세요.<br><br>클러스터 및 Linux 노드에 대한 위협 방지. 의심스러운 활동에 대한 경고는 [Microsoft Defender for Kubernetes](defender-for-kubernetes-introduction.md)에서 제공됩니다. 이 계획은 AKS(Azure Kubernetes Service), 온-프레미스 또는 다른 클라우드 공급자에서 호스트되는 Kubernetes 클러스터를 보호합니다. 클러스터. <br>[Kubernetes 노드 및 클러스터의 런타임 보호](#run-time-protection-for-kubernetes-nodes-and-clusters)에 대해 자세히 알아보세요.|
| ![컨테이너 호스트.](./media/container-security/icon-container-host-rec.png)<br>**컨테이너 호스트**<br>(Docker를 실행하는 VM) | Docker 환경을 지속적으로 평가하여 잘못된 구성에 대한 가시성과 선택 사항인 [서버용 Microsoft Defender](defender-for-servers-introduction.md)를 통해 식별된 위협을 완화하는 데 도움이 되는 지침을 제공합니다.<br>[보안 권장 사항을 통한 환경 강화](#environment-hardening)에 대해 자세히 알아보세요.|
| ![컨테이너 레지스트리.](./media/container-security/icon-container-registry-rec.png)<br>**ACR(Azure Container Registry) 레지스트리** | 선택 사항인 [컨테이너 레지스트리용 Microsoft Defender](defender-for-container-registries-introduction.md)를 통해 Azure Resource Manager 기반 ACR 레지스트리에 포함된 이미지의 취약성을 평가하고 관리하는 도구입니다.<br>[컨테이너 이미지의 취약성 검사](#vulnerability-management---scanning-container-images)에 대해 자세히 알아보세요. |
|||

이 문서에서는 컨테이너 레지스트리, 서버 및 Kubernetes에 사용되는 선택 사항인 향상된 보호와 함께 Defender for Cloud를 사용하여 컨테이너 및 컨테이너에 포함된 앱의 보안을 개선하고 모니터링하고 유지 관리하는 방법을 설명합니다.

Defender for Cloud가 컨테이너 보안의 이러한 주요 측면에 어떤 도움이 되는지 알아봅니다.

- [취약성 관리 - 컨테이너 이미지 검사](#vulnerability-management---scanning-container-images)
- [환경 강화](#environment-hardening)
- [Kubernetes 노드 및 클러스터의 런타임 보호](#run-time-protection-for-kubernetes-nodes-and-clusters)

다음 스크린샷에서는 자산 인벤토리 페이지와 Defender for Cloud로 보호되는 다양한 컨테이너 리소스 종류를 보여줍니다.

:::image type="content" source="./media/container-security/inventory-container-resources.png" alt-text="Defender for Cloud의 자산 인벤토리에서 컨테이너 관련 리소스 페이지" lightbox="./media/container-security/inventory-container-resources.png":::

## <a name="vulnerability-management---scanning-container-images"></a>취약성 관리 - 컨테이너 이미지 검사

Azure Resource Manager 기반 Azure 컨테이너 레지스트리의 이미지를 모니터링하려면 [컨테이너 레지스트리용 Microsoft Defender](defender-for-container-registries-introduction.md)를 사용하도록 설정합니다. Defender for Cloud는 지난 30일 이내에 끌어온 이미지, 레지스트리에 푸시한 이미지 또는 가져온 이미지를 검사합니다. 통합 검사기는 업계 최고의 취약성 검사 공급업체인 Qualys에서 제공합니다.

Qualys 또는 Defender for Cloud에서 문제가 발견되면 [워크로드 보호 대시보드](workload-protections-dashboard.md)에 알림이 표시됩니다. Defender for Cloud는 모든 취약성에 대해 심각도 분류 및 문제 해결 방법에 대한 지침과 실행 가능한 권장 사항을 제공합니다. Defender for Cloud의 컨테이너 관련 권장 사항에 대한 자세한 내용은 [권장 사항 참조 목록](recommendations-reference.md#recs-compute)을 참조하세요.

Defender for Cloud는 검사기의 검사 결과를 필터링하고 분류합니다. 이미지가 정상인 경우 Defender for Cloud는 이를 정상으로 표시합니다. Defender for Cloud는 해결해야 할 문제가 있는 이미지에 대해서만 보안 권장 사항을 생성합니다. Defender for Cloud는 문제가 있는 경우에만 알리는 방식으로 원치 않는 정보 알림이 발생할 가능성을 줄입니다.

## <a name="environment-hardening"></a>환경 강화

### <a name="continuous-monitoring-of-your-docker-configuration"></a>Docker 구성을 지속적으로 모니터링

클라우드용 Microsoft Defender는 IaaS Linux VM 또는 Docker 컨테이너를 실행하는 다른 Linux 머신에 호스트되는 관리되지 않는 컨테이너를 식별합니다. Defender for Cloud는 이러한 컨테이너의 구성을 지속적으로 평가합니다. 그런 다음, 이러한 항목을 [CIS(Center for Internet Security) Docker 벤치마크](https://www.cisecurity.org/benchmark/docker/)와 비교합니다.

Defender for Cloud는 CIS Docker 벤치마크의 전체 규칙 세트를 포함하며 컨테이너가 규칙을 위반할 경우 경고를 표시합니다. Defender for Cloud는 잘못된 구성을 발견하면 보안 권장 사항을 생성합니다. Defender for Cloud의 **권장 사항 페이지** 를 사용하여 권장 사항을 살펴보고 문제를 해결하세요. CIS 벤치마크 검사는 AKS 관리형 인스턴스 또는 Databricks 관리형 VM에서 실행되지 않습니다.

이 기능에 대해 표시될 수 있는 관련 Defender for Cloud 권장 사항에 대한 자세한 내용은 권장 사항 참조 표의 [컴퓨팅 섹션](recommendations-reference.md#recs-compute)을 참조하세요.

VM의 보안 문제를 살펴보는 동안 Defender for Cloud는 컴퓨터의 컨테이너에 대한 추가 정보를 제공합니다. 이러한 정보에는 호스트에서 실행되는 Docker 버전 및 이미지 수가 포함됩니다. 

IaaS Linux VM에 호스트되는 관리되지 않는 컨테이너를 모니터링하려면 선택 사항인 [서버용 Microsoft Defender](defender-for-servers-introduction.md)를 사용하도록 설정합니다.


### <a name="continuous-monitoring-of-your-kubernetes-clusters"></a>Kubernetes 클러스터를 지속적으로 모니터링
Defender for Cloud는 컨테이너화된 애플리케이션을 개발, 배포 및 관리하는 데 사용되는 Microsoft의 관리형 컨테이너 오케스트레이션 서비스인 AKS(Azure Kubernetes Service)와 함께 작동합니다.

AKS는 클러스터의 보안 태세에 대한 보안 제어 및 가시성을 제공합니다. Defender for Cloud는 이러한 기능을 사용하여 AKS 클러스터의 구성을 지속적으로 모니터링하면서 업계 표준에 맞는 보안 권장 사항을 생성합니다.

다음은 클라우드용 Microsoft Defender, Azure Kubernetes Service 및 Azure Policy 간의 상호 작용에 대한 개략적인 다이어그램입니다.

:::image type="content" source="./media/defender-for-kubernetes-intro/kubernetes-service-security-center-integration-detailed.png" alt-text="클라우드용 Microsoft Defender, Azure Kubernetes Service 및 Azure Policy 간의 상호 작용에 대한 개략적인 아키텍처" lightbox="./media/defender-for-kubernetes-intro/kubernetes-service-security-center-integration-detailed.png":::

Defender for Cloud에서 수신하고 분석한 항목에 다음이 포함된 것을 알 수 있습니다.

- API 서버의 감사 로그
- Log Analytics 에이전트의 원시 보안 이벤트

    > [!NOTE]
    > 현재는 가상 머신 확장 집합에서 실행되는 Azure Kubernetes Service 클러스터에 Log Analytics 에이전트를 설치할 수 없습니다.

- AKS 클러스터의 클러스터 구성 정보
- **Kubernetes용 Azure Policy 추가 항목** 을 통해 Azure Policy에서 워크로드 구성

이 기능에 대해 표시될 수 있는 관련 Defender for Cloud 권장 사항에 대한 자세한 내용은 권장 사항 참조 표의 [컴퓨팅 섹션](recommendations-reference.md#recs-compute)을 참조하세요.


###  <a name="workload-protection-best-practices-using-kubernetes-admission-control"></a>Kubernetes 허용 제어를 사용하여 워크로드 보호 모범 사례

Kubernetes 컨테이너의 워크로드를 보호하기 위한 권장 사항 번들을 보려면 **Kubernetes용 Azure Policy 추가 기능** 을 설치합니다. [Log Analytics 에이전트 및 확장의 자동 프로비저닝 사용](enable-data-collection.md#auto-provision-mma)에 설명된 대로 이 추가 기능을 자동 배포할 수도 있습니다. 추가 기능에 대한 자동 프로비저닝이 "켜기"로 설정하면 모든 기존 및 향후 클러스터(추가 기능 설치 요구 사항 충족)에서 확장이 기본적으로 사용하도록 설정됩니다.

[Kubernetes용 Azure Policy 페이지](../governance/policy/concepts/policy-for-kubernetes.md)에 설명된 것처럼, 이 추가 기능은  [Open Policy Agent](https://www.openpolicyagent.org/)에 대한 오픈 소스 [Gatekeeper v3](https://github.com/open-policy-agent/gatekeeper) 허용 컨트롤러 웹후크를 확장합니다. Kubernetes 허용 컨트롤러는 클러스터 사용 방법을 강제 적용하는 플러그 인입니다. 추가 기능은 Kubernetes 허용 제어의 webhook로 등록되며, 일관적인 중앙 집중식 방식으로 클러스터에 대규모 규약 및 세이프가드를 적용할 수 있습니다. 

AKS 클러스터에 추가 기능을 사용하면 Kubernetes API 서버에 대한 모든 요청은 클러스터에 유지되기 전에 미리 정의된 모범 사례 세트에 대해 모니터링됩니다. 그런 다음, 모범 사례를 **적용** 하고 향후 워크로드에 대해 위임하도록 구성할 수 있습니다. 

예를 들어 권한 있는 컨테이너를 만들지 않도록 위임할 수 있습니다. 그러면 이러한 작업에 대한 이후의 모든 요청이 차단됩니다.

[Kubernetes 워크로드 보호](kubernetes-workload-protections.md)에 대해 자세히 알아보세요.


## <a name="run-time-protection-for-kubernetes-nodes-and-clusters"></a>Kubernetes 노드 및 클러스터의 런타임 보호

[!INCLUDE [AKS in ASC threat protection](../../includes/security-center-azure-kubernetes-threat-protection.md)]



## <a name="next-steps"></a>다음 단계

이 개요에서는 클라우드용 Microsoft Defender의 컨테이너 보안 핵심 요소에 대해 알아보았습니다. 관련 자료는 다음을 참조하세요.

- [Microsoft Defender for Kubernetes 소개](defender-for-kubernetes-introduction.md)
- [컨테이너 레지스트리용 Microsoft Defender 소개](defender-for-container-registries-introduction.md)
