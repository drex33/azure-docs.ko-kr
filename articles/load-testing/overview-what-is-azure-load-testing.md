---
title: Azure 부하 테스트란?
description: Azure Load Testing은 개발자가 대규모 부하를 생성하여 앱 성능을 최적화할 수 있는 완전 관리형 부하 테스트 서비스입니다.
services: load-testing
ms.service: load-testing
ms.topic: overview
ms.author: nicktrog
author: ntrogh
ms.date: 11/30/2021
adobe-target: true
ms.openlocfilehash: 61109c8af817086350e4929fa338860b6fa4c939
ms.sourcegitcommit: 845eb7b0ed05ef2f0dfa6f054eaf5f32c780567c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133304177"
---
# <a name="what-is-azure-load-testing-preview"></a>Azure 부하 테스트 미리 보기란?

Azure 부하 테스트 미리 보기는 대규모 부하를 생성할 수 있는 완전 관리형 부하 테스트 서비스입니다. 서비스는 호스트되는 위치에 관계없이 애플리케이션에 대한 트래픽을 시뮬레이션합니다. 개발자, 테스터 및 QA(품질 보증) 엔지니어는 이를 사용하여 애플리케이션 성능, 확장성 또는 용량을 최적화할 수 있습니다. 

인기 있는 오픈 소스 부하 및 성능 도구인 Apache JMeter를 기반으로 기존 테스트 스크립트를 사용하여 부하 테스트를 만들 수 있습니다. Azure 기반 애플리케이션의 경우 자세한 리소스 메트릭을 통해 성능 병목 현상을 식별할 수 있습니다. CI/CD(연속 통합 및 지속적인 배포) 워크플로를 사용하면 회귀 테스트를 자동화할 수 있습니다.

> [!IMPORTANT]
> Azure 부하 테스트는 현재 미리 보기로 제공됩니다.
> 베타, 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="how-does-azure-load-testing-work"></a>Azure 부하 테스트는 어떻게 작동하나요?

Azure 부하 테스트 테스트 엔진은 대규모 부하 테스트를 실행하는 데 필요한 인프라를 추상화합니다. 테스트 엔진은 Apache JMeter 스크립트를 실행하여 애플리케이션 엔드포인트에 동시에 액세스하는 많은 수의 가상 사용자를 시뮬레이션합니다. 부하 테스트를 확장하려면 테스트 엔진 수를 구성할 수 있습니다.

애플리케이션은 Azure, 온-프레미스 또는 다른 클라우드의 어디에서나 호스트할 수 있습니다. 부하 테스트 실행 중에 자세한 리소스 메트릭이 수집되어 대시보드에 표시됩니다.

- *클라이언트 쪽 메트릭은* 가상 사용자 수, 요청 응답 시간 또는 초당 요청 수와 같이 테스트 엔진에서 보고한 세부 정보를 제공합니다.

- *서버 쪽 메트릭은* Azure 애플리케이션 구성 요소에 대한 정보를 제공합니다. Azure Load Testing은 애플리케이션 Insights 및 컨테이너 인사이트를 비롯한 Azure Monitor 통합하여 Azure 서비스에서 세부 정보를 캡처합니다. 서비스 유형에 따라 다양한 메트릭을 사용할 수 있습니다. 예를 들어 데이터베이스 읽기 수, HTTP 응답 유형 또는 컨테이너 리소스 사용량이 있습니다.

Azure Load Testing은 테스트가 안전하고 안정적으로 실행되도록 Azure 네트워킹 모범 사례를 자동으로 통합합니다. 애플리케이션 엔드포인트 또는 Azure 구성 요소가 제한 요청을 시작하면 부하 테스트가 자동으로 중단됩니다.

Azure Load Testing 리소스에 저장된 데이터는 Microsoft에서 관리하는 키(서비스 관리형 키)로 자동으로 원활하게 암호화됩니다. 이 데이터에는 예를 들어 Apache JMeter 스크립트가 포함됩니다.

:::image type="content" source="./media/overview-what-is-azure-load-testing/azure-load-testing-architecture.svg" alt-text="Azure Load Testing 아키텍처 개요를 보여 주는 다이어그램":::

> [!NOTE]
> 이 이미지는 Azure Load Testing에서 Azure Monitor 사용하여 앱 구성 요소에 대한 메트릭을 캡처하는 방법을 보여 하며, 지원되는 Azure 리소스의 포괄적인 목록이 아닙니다.

## <a name="how-to-identify-performance-bottlenecks-by-using-high-scale-load-tests"></a>대규모 부하 테스트를 사용하여 성능 병목 현상을 식별하는 방법

성능 문제는 종종 애플리케이션이 로드될 때까지 감지되지 않은 상태로 유지됩니다. Azure Portal 대규모 부하 테스트를 시작하여 애플리케이션이 스트레스에서 동작하는 방식을 더 빨리 알아볼 수 있습니다. 테스트가 실행되는 동안 Azure Load Testing 대시보드는 클라이언트 및 서버 쪽 메트릭의 라이브 업데이트를 제공합니다.

부하 테스트가 완료되면 대시보드를 사용하여 테스트 결과를 분석하고 성능 병목 현상을 식별할 수 있습니다. Azure 호스팅 애플리케이션의 경우 대시보드에 Azure 애플리케이션 구성 요소의 자세한 리소스 메트릭이 표시됩니다.

Azure Load Testing은 테스트 실행 기록을 유지하며 여러 실행을 시각적으로 비교하여 성능 회귀를 검색할 수 있습니다.

타사 도구에서 분석을 위해 테스트 결과를 다운로드할 수도 있습니다.

## <a name="how-to-enable-automated-load-testing"></a>자동화된 부하 테스트를 사용하도록 설정하는 방법

개발 수명 주기 동안 의미 있는 지점에서 CI/CD(연속 통합 및 지속적인 배포) 파이프라인에 Azure Load Testing을 통합할 수 있습니다. 예를 들어 각 스프린트 또는 스테이징 환경에서 부하 테스트를 자동으로 실행하여 릴리스 후보 빌드의 유효성을 검사할 수 있습니다.

테스트 구성에서는 개발 주기 초기에 성능 회귀를 catch하는 통과/실패 규칙을 지정합니다. 예를 들어 평균 응답 시간이 지정된 임계값을 초과하면 테스트가 실패합니다.

Azure Load Testing은 특정 오류 조건에 대한 응답으로 자동화된 부하 테스트를 자동으로 중단합니다. Apache JMeter 스크립트에서 AutoStop 수신기를 사용할 수도 있습니다. 자동 중단은 잘못 구성된 엔드포인트 URL 등으로 인해 추가 비용이 발생하는 테스트 실패를 방지합니다.

Azure Pipelines 또는 GitHub Actions 워크플로에서 Azure Load Testing을 트리거할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure 부하 테스트 사용 시작:
- [자습서: 부하 테스트를 사용하여 성능 병목 현상 식별](./tutorial-identify-bottlenecks-azure-portal.md)
- [자습서: 자동화된 부하 테스트 설정](./tutorial-cicd-azure-pipelines.md)
