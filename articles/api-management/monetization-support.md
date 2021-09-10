---
title: 수익 창출에 대한 Azure API Management 지원
description: Azure API Management에서 API 제품에 대한 수익 창출 전략을 지원하는 방법을 알아봅니다.
author: v-hhunter
ms.author: v-hhunter
ms.date: 08/23/2021
ms.topic: article
ms.service: api-management
ms.openlocfilehash: 88323145b9b6bb66babe77b19d6b834afc2831ab
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122825302"
---
# <a name="how-api-management-supports-monetization"></a>API Management에서 수익 창출을 지원하는 방법

[Azure API Management](./api-management-key-concepts.md) 서비스 플랫폼을 통해 할 수 있는 작업은 다음과 같습니다.
* 소비자가 구독하는 API를 게시합니다.
* 구현의 위험성을 제거합니다. 
* 프로젝트에 드는 시간을 단축합니다.
* 자신 있게 API를 스케일링합니다.

이 문서에서는 수익 창출 전략을 구현하는 데 사용할 수 있는 API Management 기능을 중점적으로 다룹니다. 예를 들어 다음 작업을 위한 원활한 환경을 제공하는 기능이 있습니다.
* 퍼블릭 API를 발견합니다.
* 결제 정보를 입력합니다.
* 구독을 활성화합니다.
* API를 사용합니다.
* 사용량을 모니터링합니다.
* API 사용량에 대해 자동으로 지불합니다.

아래 다이어그램은 이러한 주요 API Management 기능을 소개합니다.

:::image type="content" source="media/monetization-support/architecture-overview.png" alt-text="주요 API Management 수익 창출 기능의 다이어그램":::

## <a name="api-discovery"></a>API 검색

API Management의 기본 제공 개발자 포털을 사용하여 API를 시작하고 API 소비자를 온보딩합니다. API 소비자가 API를 원활하게 살펴보고 사용할 수 있도록 개발자 포털의 우수한 품질 개발 콘텐츠를 강조합니다. 제공되는 콘텐츠와 정보의 접근성, 완전성, 사용성을 테스트합니다.

개발자 포털의 콘텐츠를 추가하고 브랜딩을 제어하는 방법에 대한 자세한 내용은 [개발자 포털 개요](./api-management-howto-developer-portal.md)를 참조하세요.

## <a name="api-packaging"></a>API 패키징

API Management는 ‘제품’ 및 ‘정책’의 개념을 사용하여 API가 패키지되고 제공되는 방법을 관리합니다. 

### <a name="products"></a>제품

API는 [제품을 통해](./api-management-howto-add-products.md) 게시됩니다. 제품은 다음과 같이 정의할 수 있습니다.
* 구독자가 액세스할 수 있는 API.
* 특정 구독을 월별 호출 할당량으로 제한하는 것과 같은 특정 제한 [정책](./api-management-howto-policies.md)입니다.

API 소비자가 제품을 구독하면 API 키를 수신하게 되며, 소비자는 이 키를 통해 호출합니다. 처음에 구독은 `submitted` 상태로 설정되어 있습니다. 구독자가 API를 사용할 수 있도록 구독을 활성화합니다.

API Management 제품을 구성하여 다음을 통해 수익 모델을 미러링하도록 기본 API를 패키지합니다.
* 수익 모델의 각 계층 간 일대일 관계입니다.
* 해당 API Management 제품입니다.

예제 프로젝트에서는 API Management 제품을 수익 창출 전략 코드화의 최상위 수단으로 사용합니다. API Management 제품은 수익 모델 계층을 미러링하고 각 계층에 대한 특정 가격 책정 모델을 인덱싱합니다. 이 설정은 수익 창출 전략을 준비하는 유연한 구성 기반 접근 방식을 제공합니다.

### <a name="policies"></a>정책

각 제품의 서비스 품질을 제어하는 API Management 정책을 적용합니다. 예제 프로젝트는 두 가지 특정 정책 기능을 사용하여 수익 모델에 맞게 서비스 품질을 제어합니다.

| 정책 기능 | Description |
| ----- | ----- |
| **할당량** | 지정된 기간 사용자가 API에 수행할 수 있는 총 호출 수를 정의합니다. 예: “월 100건의 호출” 사용자가 할당량을 채우면 API에 대한 호출이 실패하고 호출자는 `403 Forbidden` 응답 상태 코드를 받게 됩니다. |
| **속도 제한** | 슬라이딩 시간대에 API에 수행할 수 있는 호출 수를 정의합니다. 예를 들어 “분당 200건의 호출”이 있습니다. API 사용량이 선택한 제품에 대한 유료 서비스 품질 이상으로 급증하지 않도록 설계되었습니다. 호출 속도를 초과할 경우 호출자는 `429 Too Many Requests` 응답 상태 코드를 수신합니다. |

정책에 대한 자세한 내용은 [Azure API Management의 정책](./api-management-howto-policies.md) 설명서를 참조하세요.

## <a name="api-consumption"></a>API 사용량

API 구독을 사용하여 API에 API 소비자에 대한 액세스 권한을 부여합니다.

1. API 소비자는 특정 API Management 제품에 가입할 때 API 구독을 설정합니다. 
1. API Management 위임을 사용하여 구독 프로세스를 지불 공급자와 통합합니다. 
1. 지불 정보를 입력하면 사용자가 구독에 대해 생성된 고유 보안 키로 API에 액세스할 수 있습니다.

구독에 대한 자세한 내용은 [Azure API Management의 구독](./api-management-subscriptions.md) 설명서를 참조하세요.

## <a name="api-usage-monitoring"></a>API 사용량 모니터링:

API Management의 기본 제공 분석을 사용하여 API 사용량 및 성능에 대한 인사이트를 얻습니다. 이러한 분석은 다음 주제별 보고서를 제공합니다.
* API
* Geography
* API 작업
* 제품
* 요청
* 구독
* 시간 
* 사용자

분석 보고서를 정기적으로 검토하여 API 소비자가 수익 창출 전략을 채택하는 방식을 파악합니다.

자세한 내용은 [Azure API Management API 분석 사용](./howto-use-analytics.md)을 참조하세요.

## <a name="security"></a>보안

API Management 제품, API 정책 및 구독을 사용하여 각 사용자의 각 제품에 대한 액세스 수준을 제어합니다. 특정 API 제품이 무료인 경우에도 사용자가 지불 공급자를 사용하여 인증된 경우 구독 수준 API 액세스를 부여하여 오남용을 방지합니다.

## <a name="integration"></a>통합

API Management와 선택한 결제 공급자 간의 프런트 엔드 및 백 엔드 통합을 통해 원활한 수익 창출 환경을 만듭니다.  프런트 엔드 통합을 위해 API Management 위임을 사용하고 백 엔드 통합을 위해 REST API를 사용합니다.

### <a name="delegation"></a>위임

예제 프로젝트에서는 [API Management 위임](./api-management-howto-setup-delegation.md)을 사용하여 타사 지불 공급자와 사용자 지정 통합할 수 있습니다. 데모는 등록/로그인과 제품 구독 환경 모두에 위임을 사용합니다.

#### <a name="sign-upsign-in-workflow"></a>등록/로그인 워크플로

1. 개발자가 API Management 개발자 포털에서 로그인 또는 가입 링크를 클릭합니다.
1. 브라우저가 위임 엔드포인트(사용자 지정 청구 포털 앱의 페이지로 구성됨)으로 리디렉션됩니다.
1. 사용자 지정 청구 포털 앱은 로그인/등록 UI를 제공합니다.
1. 로그인/등록에 성공하면 사용자가 인증되고 처음의 API Management 개발자 포털 페이지로 다시 리디렉션됩니다.

#### <a name="product-subscription-workflow"></a>제품 구독 워크플로

1. 개발자는 API Management 개발자 포털에서 제품을 선택하고 **구독** 단추를 클릭합니다.
1. 브라우저가 위임 엔드포인트(사용자 지정 청구 포털 앱의 페이지로 구성됨)으로 리디렉션됩니다.
1. 사용자 지정 청구 포털 앱:
    * 청구 공급자(Stripe 또는 Adyen)를 기반으로 구성된 UI를 제공합니다.
    * 사용자에게 관련 체크 아웃 프로세스를 안내합니다.
1. 사용자가 처음 API Management 제품 페이지로 다시 리디렉션됩니다. 
    * 제품이 활성화되고 API 키를 사용할 수 있습니다.

### <a name="rest-api"></a>REST API

API Management에 대한 REST API를 사용하여 수익 창출 전략의 작업을 자동화합니다.

샘플 프로젝트는 API를 사용하여 다음을 프로그래밍 방식으로 수행합니다.

- Stripe와 같은 결제 공급자에서 유사한 개념의 동기화 된 구성을 사용할 수 있도록 API Management 제품 및 정책을 검색합니다.
- 정기적으로 API Management를 폴링하여 각 구독에 대한 API 사용 현황 메트릭을 검색하고 청구 프로세스를 진행합니다.

자세한 내용은 [REST API Azure API Management](https://docs.microsoft.com/rest/api/apimanagement/#rest-operation-groups)를 참조하세요.

## <a name="devops"></a>DevOps

Azure Resource Manager를 사용하여 버전 제어 및 API Management에 대한 배포 변경 자동화. 그 예로는 수익 창출 전략을 구현하는 기능을 구성하는 것 외에도 다음이 포함됩니다.
* 제품
* 정책
* 개발자 포털

예제 프로젝트에서 Azure Resource Manager 스크립트는 각 API Management 제품의 가격 책정 모델을 정의하는 JSON 파일에 의해 확대됩니다. 이러한 확대를 통해 API Management와 선택한 결제 공급자 간의 구성을 동기화할 수 있습니다. 전체 솔루션은 다음과 같은 목적을 위해 단일 소스 제어 리포지토리 아래에서 관리됩니다.
* 지속적인 수익 창출 전략 진화와 관련된 모든 변경 사항을 단일 릴리스로 조정합니다.
* 거버넌스 및 감사 요구 사항에 따라 변경 사항을 수행합니다.

## <a name="initialization-and-deployment"></a>초기화 및 배포

API Management는 다음을 통해 배포할 수 있습니다.
* Azure Portal UI 또는
* [Azure Resource Manager 템플릿](https://azure.microsoft.com/services/arm-templates)을 통한 “코드로서의 인프라” 접근 방식. 

## <a name="next-steps"></a>다음 단계

* [API Management 수익 창출 전략에 대해 자세히 알아보세요](monetization-overview.md).
* 연결된 [Git 리포지토리](https://github.com/microsoft/azure-api-management-monetization)를 통해 데모 Adyen 또는 Stripe 통합을 배포합니다.
