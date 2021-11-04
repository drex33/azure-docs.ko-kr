---
title: 할당량 및 제한 Form Recognizer
titleSuffix: Azure Applied AI Services
description: Azure Form Recognizer 서비스 할당량 및 제한에 대한 빠른 참조, 자세한 설명 및 모범 사례
services: cognitive-services
author: vkurpad
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 09/30/2021
ms.author: vikurpad
ms.openlocfilehash: 8a1b57571acdbfd8dce29e45218cfd096b0906f6
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131482897"
---
# <a name="form-recognizer-service-quotas-and-limits"></a>Form Recognizer 서비스 할당량 및 제한

이 문서에는 모든 가격 책정 계층에 대한 Azure Form Recognizer 서비스 할당량 및 제한에 대한 빠른 참조 및 **자세한 설명이** [포함되어 있습니다.](https://azure.microsoft.com/pricing/details/form-recognizer/) 또한 요청 제한을 방지하기 위한 몇 가지 모범 사례가 포함되어 있습니다. 

[Form Recognizer SDK를](quickstarts/try-v3-csharp-sdk.md)사용하는 경우 [Form Recognizer REST API](quickstarts/try-v3-rest-api.md), [Form Recognizer Studio](quickstarts/try-v3-form-recognizer-studio.md) 및 샘플 레이블 지정 [도구.](https://fott-2-1.azurewebsites.net/)

| 할당량 | 무료(F0)<sup>1</sup> | Standard(S0) |
|--|--|--|
| **동시 요청 제한** | 1 | 15(기본값) |
| 조정 가능 | 아니요<sup>2</sup> | 예<sup>2</sup> |
| **모델 작성 제한** | 5 | 100(기본값) |


<sup>1</sup> **무료(F0)** 가격 책정 계층의 경우 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/form-recognizer/)의 월간 허용량도 참조하세요.
<sup>2</sup> [모범 사례 및](#example-of-a-workload-pattern-best-practice) [조정 지침을 참조하세요.](#create-and-submit-support-request)

## <a name="detailed-description-quota-adjustment-and-best-practices"></a>자세한 설명, 할당량 조정 및 모범 사례
할당량 증가를 요청하기 전에(해당하는 경우) 필요한지 확인합니다. Form Recognizer 서비스는 자동 크기 조정을 사용하여 필요한 계산 리소스를 "주문형"으로 가져오는 동시에 과도한 양의 하드웨어 용량을 유지하지 않고 사용하지 않는 리소스의 프로비저닝을 해제하여 고객 비용을 낮게 유지합니다. 워크로드가 정의된 한도 내에 있는 동안 애플리케이션이 응답 코드 429("너무 많은 요청")를 받을 [때마다(할당량 및 제한 빠른 참조 참조](#form-recognizer-service-quotas-and-limits)참조) 가장 가능성이 높은 설명은 서비스가 수요에 맞게 확장되고 아직 필요한 규모에 도달하지 않았기 때문에 요청을 제공할 충분한 리소스가 즉시 없는 것입니다. 이 상태는 일반적으로 일시적이며 오래 지속되지 않아야 합니다.

### <a name="general-best-practices-to-mitigate-throttling-during-autoscaling"></a>자동 크기 조정 중 제한을 완화하기 위한 일반적인 모범 사례
제한과 관련된 문제(응답 코드 429)를 최소화하려면 다음 방법을 사용하는 것이 좋습니다.
- 응용 프로그램에서 다시 시도 논리를 구현하세요.
- 워크로드가 급격히 변경되지 않도록 합니다. 워크로드를 점진적으로 늘립니다. <br/>
*예제.* 애플리케이션은 Form Recognizer 사용하고 현재 워크로드는 10 TPS(초당 트랜잭션 수)입니다. 다음 1초는 부하를 40 TPS(4배 이상)로 늘림합니다. 서비스는 새 로드를 충족하기 위해 즉시 확장을 시작하지만 1초 이내에 수행할 수 없으므로 일부 요청에 응답 코드 429가 수신됩니다.

다음 섹션에서는 할당량 조정의 특정 사례에 대해 설명합니다.
Form Recognizer [이동: 동시 요청 제한 늘리기](#create-and-submit-support-request)

### <a name="increasing-transactions-per-second-request-limit"></a>초당 트랜잭션 수 요청 제한 증가
기본적으로 동시 요청 수는 Form Recognizer 리소스에 대해 초당 15개의 트랜잭션으로 제한됩니다. 표준 가격 책정 계층의 경우 이 크기를 늘릴 수 있습니다. 요청을 제출하기 전에 [이 섹션](#detailed-description-quota-adjustment-and-best-practices)의 자료를 숙지하고 [모범 사례](#example-of-a-workload-pattern-best-practice)에 대해 알고 있는지 확인하세요.

동시 요청 제한을 늘려도 비용에 직접적인 영향을 주지 **않습니다**. Form Recognizer 서비스는 "사용한 것에 대해서만 지불" 모델을 사용합니다. 이 제한은 서비스에서 요청을 제한하기 전에 확장할 수 있는 한도를 정의합니다.

동시 요청 제한 매개 변수의 기존 값은 Azure Portal, 명령줄 도구 또는 API 요청을 통해 표시되지 **않습니다**. 기존 값을 확인하려면 Azure 지원 요청을 만듭니다.

#### <a name="have-the-required-information-ready"></a>필요한 정보를 준비합니다.
- Form Recognizer 리소스 ID
- 지역

- **정보를 가져오는 방법(기본 모델)** :
  - [Azure Portal](https://portal.azure.com/)로 이동합니다.
  - 트랜잭션 제한을 늘리려는 Form Recognizer 리소스를 선택합니다.
  - *속성* 선택(*리소스 관리* 그룹)
  - 다음 필드의 값을 복사하고 저장합니다.
    - **리소스 ID**
    - **위치**(엔드포인트 영역)

#### <a name="create-and-submit-support-request"></a>지원 요청 만들기 및 제출
지원 요청을 제출하여 리소스에 대한 TPS(초당 트랜잭션 수) 제한을 늘리기 시작합니다.

- [필요한 정보](#have-the-required-information-ready)가 있는지 확인
- [Azure Portal](https://portal.azure.com/)로 이동합니다.
- TPS 제한을 늘리려는 Form Recognizer 리소스를 선택합니다.
- *새 지원 요청*(*지원 + 문제 해결* 그룹) 선택
- Azure 구독 및 Azure 리소스에 대한 정보가 자동으로 채워진 새 창이 표시됩니다.
- *요약* 입력(예: "Form Recognizer TPS 제한 증가")
- *문제 유형에서* "할당량 또는 사용 유효성 검사"를 선택합니다.
- *다음: 솔루션* 클릭
- 요청 만들기 계속 진행
- *세부 정보* 탭에서 *설명* 필드에 다음을 입력합니다.
  - 참고: 요청은 약 **Form Recognizer** 할당량입니다.
  - 크기를 조정하려는 TPS 기대치를 제공합니다.    
  - [이전에 수집한](#have-the-required-information-ready) Azure 리소스 정보
  - 필요한 정보 입력을 완료하고 *검토 + 만들기* 탭에서 *만들기* 단추를 클릭합니다.
  - Azure Portal 알림에서 지원 요청 번호를 확인합니다. 추가 처리를 위해 곧 연락을 드릴 것입니다.

## <a name="example-of-a-workload-pattern-best-practice"></a>워크로드 패턴 모범 사례의 예
이 예에서는 [진행 중인 자동 크기 조정](#detailed-description-quota-adjustment-and-best-practices)으로 인해 가능한 요청 제한을 완화하기 위해 다음과 같은 권장되는 접근 방식을 제시합니다. 이는 "정확한 해결 방법"이 아니라 우리가 필요에 맞게 따르고 조정하도록 초대하는 템플릿일 뿐입니다.

Form Recognizer 리소스에 기본 제한 집합이 있다고 가정해 보겠습니다. 워크로드를 시작하여 분석 요청을 제출합니다. 응답 코드 429에서 자주 제한되는 경우 GET 분석 응답 요청에서 백업하여 시작하고 2-3-5-8 패턴을 사용하여 다시 시도합니다. 일반적으로 해당 POST 요청에 대해 2초마다 두 번 이상 get analyze 응답을 호출하지 않는 것이 좋습니다. 

제출되는 문서에 대한 POST 요청 수가 제한되는 경우 요청 사이에 지연을 추가하는 것이 좋습니다. 워크로드에 더 높은 수준의 동시 처리가 필요한 경우 초당 트랜잭션에 대한 서비스 제한을 늘리기 위한 지원 요청을 만들어야 합니다.

일반적으로 프로덕션으로 이동하기 전에 워크로드 및 워크로드 패턴을 테스트하는 것이 좋습니다.

