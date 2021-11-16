---
author: memildin
ms.author: memildin
manager: rkarlin
ms.date: 10/18/2021
ms.topic: include
ms.openlocfilehash: 74209403f26404435a58e76efffd69e54435d221
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132354100"
---
Defender for Cloud는 실시간 위협 방지를 컨테이너화된 환경에 제공하고 의심스러운 활동에 대한 경고를 생성합니다. 이 정보를 사용하여 보안 문제를 신속하게 수정하고 컨테이너의 보안을 강화할 수 있습니다.

Defender for Cloud에서 위협 방지를 제공하는 다양한 수준은 다음과 같습니다. 

* **호스트 수준(서버용 Microsoft Defender에서 제공)** - Defender for Cloud가 다른 VM에서 사용하는 것과 동일한 Log Analytics 에이전트를 사용하면 서버용 Microsoft Defender는 Linux Kubernetes 노드에서 웹 셸 검색 및 알려진 의심스러운 IP 주소와의 연결과 같은 의심스러운 활동을 모니터링합니다. 이 에이전트는 또한 권한 있는 컨테이너 만들기, API 서버에 대한 의심스러운 액세스 및 Docker 컨테이너 내에서 실행되는 SSH(Secure Shell) 서버와 같은 컨테이너 관련 분석을 모니터링합니다.

    에이전트를 호스트에 설치하지 않도록 선택하는 경우 위협 방지 혜택 및 보안 경고의 하위 집합만 받게 됩니다. 네트워크 분석 및 악의적인 서버와의 통신과 관련된 경고는 계속 받을 수 있습니다.

    >[!IMPORTANT]
    > 현재는 가상 머신 확장 집합에서 실행되는 Azure Kubernetes Service 클러스터에 Log Analytics 에이전트를 설치할 수 없습니다.

    호스트 수준 경고 목록은 [경고 참조 표](../articles/security-center/alerts-reference.md#alerts-containerhost)를 참조하세요.


* **클러스터 수준(Microsoft Defender for Kubernetes에서 제공)** - 클러스터 수준의 위협 보호는 Kubernetes의 감사 로그 분석을 기반으로 합니다. 이 **에이전트 없는** 모니터링을 사용하도록 설정하려면 향상된 보안 기능을 사용하도록 설정합니다. 클러스터가 온-프레미스 또는 다른 클라우드 공급자에 있는 경우 [Azure Arc 지원 Kubernetes 및 Defender 확장](../articles/security-center/defender-for-kubernetes-azure-arc.md)을 사용하도록 설정합니다.

    이 수준에서 경고를 생성하기 위해 Defender for Cloud는 클러스터의 로그를 모니터링합니다. 이 수준의 이벤트 예로 노출된 Kubernetes 대시보드, 높은 권한이 있는 역할 만들기 및 중요한 탑재 만들기가 있습니다.

    >[!NOTE]
    > Defender for Cloud는 구독에서 Defender for Kubernetes 계획을 사용하도록 설정한 후에 발생하는 작업 및 배포에 대한 보안 경고를 생성합니다. 

    클러스터 수준 경고 목록은 [경고 참조 표](../articles/security-center/alerts-reference.md#alerts-k8scluster)를 참조하세요.

또한 글로벌 보안 연구 팀에서는 위협 환경을 지속적으로 모니터링합니다. 검색된 컨테이너 관련 경고 및 취약성을 추가합니다.

> [!TIP]
> [이 블로그 게시물](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270)의 지침에 따라 컨테이너 경고를 시뮬레이션할 수 있습니다.
