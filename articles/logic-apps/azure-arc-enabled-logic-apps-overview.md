---
title: 개요 - Azure Arc 지원 Logic Apps
description: Kubernetes 실행이 가능한 모든 위치에서 실행할 수 있는 단일 테넌트 Logic Apps 워크플로에 대해 알아봅니다.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, ladolan, reylons, archidda, sopai, azla
ms.topic: overview
ms.date: 05/25/2021
ms.openlocfilehash: 2a8d3187eecb684f7c0c2b89c135745dbcc9d8e9
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129713360"
---
# <a name="what-is-azure-arc-enabled-logic-apps-preview"></a>Azure Arc 지원 Logic Apps란? (미리 보기)

> [!NOTE]
> 이 기능은 미리 보기로 제공되고 [Microsoft Azure 미리 보기의 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)이 적용됩니다.

Azure Arc 지원 Logic Apps를 사용하면 Kubernetes 실행이 가능한 모든 위치에서 단일 테넌트 기반 논리 앱을 개발하고 실행할 수 있습니다. 예를 들어 Azure, Azure Kubernetes Service, 온-프레미스 및 다른 클라우드 공급자를 통해서도 논리 앱 워크플로를 실행할 수 있습니다. 이 제품은 다음 기능에 대해 Azure Arc와 Azure Portal을 통해 중앙 집중식 단일 창 관리 플랫폼을 제공합니다.

- Azure Logic Apps를 통합 플랫폼으로 사용합니다.
- 워크플로가 호스트되는 위치와 관계없이 모든 서비스에 워크플로를 연결합니다.
- 서비스와 함께 통합 솔루션을 직접 실행합니다.
- Visual Studio Code를 사용하여 워크플로를 만들고 편집합니다.
- DevOps에 대해 선택된 파이프라인을 사용하여 배포합니다.
- Azure, 비Azure, 여러 클라우드, 온-프레미스 및 에지 환경에서 인프라와 리소스를 제어합니다.

자세한 내용은 다음 설명서를 검토하세요.

- [Azure Logic Apps란?](../logic-apps/logic-apps-overview.md)
- [단일 테넌트 및 기타 Logic Apps 환경](../logic-apps/single-tenant-overview-compare.md)
- [Azure Arc 개요](../azure-arc/overview.md)
- [Azure Kubernetes Service 개요](../aks/intro-kubernetes.md)
- [Azure Arc 지원 Kubernetes란?](../azure-arc/kubernetes/overview.md)
- [Kubernetes란?](https://kubernetes.io/docs/concepts/overview/what-is-kubernetes/)

<a name="why-use"></a>

## <a name="why-use-azure-arc-enabled-logic-apps"></a>Azure Arc 지원 Logic Apps를 사용하는 이유

Azure Arc 지원 Logic Apps를 사용하면 Azure Logic Apps의 단일 테넌트 환경과 동일한 방식으로 논리 앱 워크플로를 만들고 배포할 수 있습니다. 또한 사용자가 운영 및 관리하는 Kubernetes 인프라에서 실행되는 논리 앱이 있는 경우 제어와 유연성 확보가 더 용이해집니다.

Azure Arc와 단일 테넌트 Logic Apps 환경 사이에는 논리 앱을 만들고 디자인하고 배포하는 데 있어서 약간의 차이가 있습니다. Azure Arc 지원 Logic Apps를 사용하는 경우, 주요 차이점은 논리 앱이 *사용자 지정 위치* 에서 실행된다는 것입니다. 이 위치는 사용자가 Azure App Service 플랫폼 확장 번들을 설치하고 사용하도록 설정한 Azure Arc 지원 Kubernetes 클러스터에 매핑됩니다.

예를 들어 이 클러스터는 Azure Kubernetes Service, 미설치 Kubernetes 또는 다른 설정일 수 있습니다. 확장 번들을 사용하면 Kubernetes 클러스터에서 Azure Logic Apps, Azure Functions 및 Azure App Service와 같은 플랫폼 서비스를 실행할 수 있습니다.

자세한 내용은 다음 설명서를 검토하세요.

- [단일 테넌트 및 기타 Azure Logic Apps 환경](../logic-apps/single-tenant-overview-compare.md)
- [Azure Kubernetes Service 개요](../aks/intro-kubernetes.md)
- [Azure Arc 지원 Kubernetes란?](../azure-arc/kubernetes/overview.md)
- [Azure Arc 지원 Kubernetes의 사용자 지정 위치](../azure-arc/kubernetes/conceptual-custom-locations.md)
- [Azure Arc의 App Service, Functions 및 Logic Apps(미리 보기)](../app-service/overview-arc-integration.md)
- [Azure Arc 지원 Kubernetes 클러스터를 설정하여 App Service, Functions 및 Logic Apps 실행(미리 보기)](../app-service/manage-create-arc-environment.md)

<a name="when-to-use"></a>

## <a name="when-to-use-azure-arc-enabled-logic-apps"></a>Azure Arc 지원 Logic Apps를 사용하는 경우

Kubernetes를 통해 제어와 유연성이 증진되지만 운영 오버헤드가 있습니다. Logic Apps 서비스가 요구 사항을 충족하는 경우, 이 서비스를 계속 사용하는 것이 좋습니다. 그러나 다음 시나리오에서는 Azure Arc 지원 Logic Apps를 사용하는 것이 좋습니다.

- Kubernetes에서 모든 앱과 서비스를 이미 실행하고 있습니다. 사용자가 이러한 프로세스 및 컨트롤을 다른 모든 PaaS 서비스로 확장하려고 합니다.

- 사용자가 Azure Logic Apps를 통합 플랫폼으로 사용하려고 합니다. 그러나 세분화되어 컴퓨팅 제어와 유연성이 확보된 네트워킹이 필요합니다. 사용자가 ISE(통합 서비스 환경) 또는 ASE(App Service Environment)를 사용하지 않으려고 합니다.

- 보안상의 이유로 논리 앱이 실행되는 위치(예: 사용자의 지역이나 데이터 센터)를 제어해야 합니다. 

- 사용자가 다중 클라우드 시나리오에서 논리 앱을 실행하고, 실행되는 모든 애플리케이션의 유일한 통합 플랫폼으로 Logic Apps 서비스를 사용하려고 합니다.

<a name="compare"></a>

## <a name="compare-offerings"></a>제품 비교

이 테이블에서는 현재 Azure Logic Apps 제품의 기능을 개괄적으로 비교합니다.

:::row:::
   :::column:::
      **기능**
   :::column-end:::
   :::column:::
      **다중 테넌트 Logic Apps(소비)**
   :::column-end:::
   :::column:::
      **단일 테넌트 Logic Apps(표준)**
   :::column-end:::
   :::column:::
      **독립 실행형 컨테이너**
   :::column-end:::
   :::column:::
      **Azure Arc**
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
      로컬 개발
   :::column-end:::
   :::column:::
      Visual Studio Code, Visual Studio
   :::column-end:::
   :::column:::
      Visual Studio Code(실행 기록 및 중단점 디버깅이 포함된 개요 포함)
   :::column-end:::
   :::column:::
      Visual Studio Code
   :::column-end:::
   :::column:::
      Visual Studio Code(실행 기록 및 중단점 디버깅이 포함된 개요 포함)
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
      Hosting
   :::column-end:::
   :::column:::
      Azure에서만 실행
   :::column-end:::
   :::column:::
      Azure에서만 실행
   :::column-end:::
   :::column:::
      컨테이너가 실행되는 모든 곳에서 실행
   :::column-end:::
   :::column:::
      Azure Arc 지원 Kubernetes 클러스터를 사용하여 어디서나 실행
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
      관리
   :::column-end:::
   :::column:::
      완전 관리형 Logic Apps 환경
   :::column-end:::
   :::column:::
      완전 관리형 Logic Apps 환경
   :::column-end:::
   :::column:::
      관리되지 않음
   :::column-end:::
   :::column:::
      Kubernetes 수준에서 운영 제어를 할 수 있는 관리형 Logic Apps 환경
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
      모니터링
   :::column-end:::
   :::column:::
      필요한 경우 실행 기록, 다시 제출 실행 및 Application Insights 기능을 포함하여 Azure Portal에서 모니터링
   :::column-end:::
   :::column:::
      필요한 경우 실행 기록, 다시 제출 실행 및 Application Insights 기능을 포함하여 Azure Portal에서 모니터링
   :::column-end:::
   :::column:::
      Application Insights 또는 기타 컨테이너 모니터링 도구로만 모니터링
   :::column-end:::
   :::column:::
      필요한 경우 실행 기록, 다시 제출 실행 및 Application Insights 기능을 포함하여 Azure Portal에서 모니터링
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
      크기 조정
   :::column-end:::
   :::column:::
      소비 계획을 사용하여 크기 조정 제어
   :::column-end:::
   :::column:::
      표준 계획을 사용하여 크기 조정 제어
   :::column-end:::
   :::column:::
      사용할 수 없음
   :::column-end:::
   :::column:::
      [Kubernetes 기반 KEDA(이벤트 기반 자동 크기 조정)](https://keda.sh/)를 사용하여 크기 조정을 제어합니다. 큐 길이에 따라 크기 조정 이벤트를 구성합니다.
   :::column-end:::
:::row-end:::

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Arc 지원 Logic Apps를 사용하여 워크플로 만들기 및 배포](azure-arc-enabled-logic-apps-create-deploy-workflows.md)
