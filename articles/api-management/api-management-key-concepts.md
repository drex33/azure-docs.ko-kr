---
title: Azure API Management 개요 및 키 개념 | Microsoft Docs
description: API, 제품, 역할, 그룹 및 기타 API Management의 주요 개념에 대해 알아봅니다.
services: api-management
documentationcenter: ''
author: dlepow
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: overview
ms.date: 11/15/2017
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 9e4df568696ee4bc1cf2ec2d11b951b53425f5a8
ms.sourcegitcommit: cae9bf0cad514c974c0c0185e24fd4b4b3132432
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133407440"
---
# <a name="about-api-management"></a>API Management 정보

APIM(API Management)은 기존 백 엔드 서비스를 위해 일관된 최신 API 게이트웨이를 빠르게 만드는 방법입니다.

API Management를 사용하여 조직은 외부, 파트너 및 내부의 개발자에게 API를 게시하여 데이터 및 서비스의 잠재성을 활용할 수 있습니다. 모든 곳의 비즈니스는 디지털 플랫폼으로 운영을 확장함으로써 새로운 채널을 생성하고, 새로운 고객을 찾고, 기존 고객과 더 깊은 관계를 구축하고자 합니다. API Management는 개발자 참여, 비즈니스 통찰력, 분석, 보안과 보호 등을 통해 성공적인 API 프로그램을 보장하는 핵심적인 역량을 제공합니다. Azure API Management를 통해 원하는 백 엔드를 사용하고 해당 백 엔드에 따라 모든 기능을 갖춘 API 프로그램을 시작할 수 있습니다.

이 문서에는 APIM과 관련된 일반적인 시나리오에 대한 개요가 제공됩니다.  APIM 시스템의 주요 구성 요소에 대한 간략한 개요도 제공됩니다. 그런 다음 각 구성 요소에 대해 보다 자세한 개요가 제공됩니다.

## <a name="overview"></a>개요

API Management를 사용하려면 관리자가 API를 만듭니다. 각 API는 하나 이상의 작업으로 구성되며, 각 API는 하나 이상의 제품에 추가할 수 있습니다. API를 사용하려면 개발자는 해당 API가 포함된 제품을 구독합니다. 그런 다음 적용 중인 사용 정책에 따라 API의 작업을 호출할 수 있습니다. 일반적인 시나리오는 다음과 같습니다.

* **모바일 인프라 보안**
* **ISV 파트너 시스템 사용**
* **내부 API 프로그램 실행**

시스템은 다음 구성 요소로 구성되어 있습니다.

* **API 게이트웨이** 는 다음 작업을 수행하는 엔드포인트입니다.
  
  * API 호출 수락 후 백 엔드로 라우팅합니다.
  * API 키, JWT 토큰, 인증서 및 기타 자격 증명을 확인합니다.
  * 사용 할당량 및 요금 제한을 적용합니다.
  * 코드를 수정하지 않고 즉시 API를 변환합니다.
  * 설정된 백 엔드 응답을 캐시합니다.
  * 분석용으로 호출 메타데이터를 기록합니다.
* **Azure Portal** 은 API 프로그램이 설치되는 관리 인터페이스입니다. 다음 작업을 수행하는 데 사용합니다.
  
  * API 스키마를 정의하거나 가져옵니다.
  * 제품에 API를 패키징합니다.
  * API에서 할당량 또는 변환 등의 정책을 설정합니다.
  * 분석의 정보를 활용합니다.
  * 사용자를 관리합니다.
* **개발자 포털** 은 개발자를 위한 기본 웹 서비스 역할을 하며, 여기서 개발자는 다음 작업을 수행할 수 있습니다.
  
  * API 설명서를 읽습니다.
  * 대화형 콘솔을 통해 API를 사용해 보세요.
  * 계정을 만들고 구독하여 API 키를 가져옵니다.
  * 자신의 사용량에 대한 분석에 액세스합니다.
 
## <a name="apis-and-operations"></a><a name="apis"> </a>API 및 작업
API는 API Management 서비스 인스턴스의 기반입니다. 각 API는 개발자가 사용할 수 있는 작업 집합을 나타냅니다. 각 API에는 API를 구현하는 백 엔드 서비스에 대한 참조가 포함되어 있으며, 해당 작업은 백 엔드 서비스에 의해 구현되는 작업에 매핑됩니다. API Management의 작업은 매우 다양하게 구성할 수 있으며 URL 매핑, 쿼리 및 경로 매개 변수, 요청 및 응답 콘텐츠, 작업 응답 캐싱 등을 더 효율적으로 제어할 수 있습니다. 속도 제한, 할당량 및 IP 제한 정책 또한 API 또는 개별 작업 수준에서 구현할 수 있습니다.

자세한 내용은 [API를 만드는 방법][How to create APIs] 및 [API에 작업을 추가하는 방법][How to add operations to an API]을 참조하세요.

## <a name="products"></a><a name="products"> </a> 제품
제품은 API가 개발자에게 표시되는 방식입니다. API Management에서 제품은 하나 이상의 API를 가지며 제목, 설명, 사용 약관 등으로 구성됩니다. 제품은 **개방형** 또는 **보호된** 제품일 수 있습니다. 보호된 제품은 사용하기 전에 구독해야 하는 반면, 개방형 제품은 구독하지 않고 사용할 수 있습니다. 제품을 개발자가 사용할 수 있게 되면 제품을 게시할 수 있습니다. 게시되고 나면 개발자가 볼 수 있습니다(보호된 제품의 경우 구독할 수 있음). 구독 승인은 제품 수준에서 구성되며 관리자 승인을 요구하거나 자동 승인될 수 있습니다.

그룹은 제품을 개발자에게 표시하는 옵션을 관리하는 데 사용됩니다. 제품은 그룹에 대한 표시 여부를 부여하고, 개발자는 자신이 속한 그룹에게 표시되는 제품을 보고 구독할 수 있습니다. 

## <a name="groups"></a><a name="groups"> </a> 그룹
그룹은 제품을 개발자에게 표시하는 옵션을 관리하는 데 사용됩니다. API Management에는 다음과 같은 변경할 수 없는 시스템 그룹이 있습니다.

* **관리자** - Azure 구독 관리자는 이 그룹의 구성원입니다. 관리자는 API Management 서비스 인스턴스를 관리하며 개발자가 사용하는 API, 작업 및 제품을 만듭니다.
* **개발자** - 인증된 개발자 포털 사용자는 이 그룹에 속합니다. 개발자는 API를 사용하여 애플리케이션을 빌드하는 고객입니다. 개발자는 개발자 포털에 액세스할 수 있는 권한을 받으며 API의 작업을 호출하는 애플리케이션을 빌드합니다.
* **게스트** - API Management 인스턴스의 개발자 포털을 방문하는 인증되지 않은 개발자 포털 사용자(예: 잠재 고객)가 이 그룹에 속합니다. 예를 들어 API를 볼 수 있지만 호출할 수는 없는 기능과 같이 특정 읽기 전용 액세스 권한을 받을 수 있습니다.

이러한 시스템 그룹 외에도 관리자는 사용자 지정 그룹을 만들거나 [연관된 Azure Active Directory 테넌트에서 외부 그룹을 가져올 수 있습니다](api-management-howto-aad.md). 사용자 지정 및 외부 그룹은 시스템 그룹과 함께 사용되어 개발자에게 API 제품에 대한 표시 여부 및 액세스를 제공합니다. 예를 들어, 특정 파트너 조직과 관련된 개발자를 위한 하나의 사용자 지정 그룹을 만들고 관련 API만을 포함한 제품에서 API에 대한 액세스를 허용합니다. 사용자는 두 그룹 이상의 구성원이 될 수 있습니다.

자세한 내용은 [그룹을 만들고 사용하는 방법][How to create and use groups]을 참조하세요.

## <a name="developers"></a><a name="developers"> </a> 개발자
개발자는 API Management 서비스 인스턴스의 사용자 계정을 나타냅니다. 개발자는 개발자(administrator)가 만들거나 참여하도록 초대할 수 있으며 [개발자 포털][Developer portal]에서 등록할 수도 있습니다. 각 개발자는 하나 이상의 그룹의 구성원이며 해당 그룹에 대한 가시성을 부여하는 제품을 구독할 수 있습니다.

개발자가 제품을 구독하면 제품의 기본 키 및 보조 키를 부여받습니다. 이 키는 제품의 API를 호출할 때 사용됩니다.

자세한 내용은 [개발자를 만들거나 초대하는 방법][How to create or invite developers](영문) 및 [그룹을 개발자와 연결하는 방법][How to associate groups with developers](영문)을 참조하세요.

## <a name="policies"></a><a name="policies"> </a> 정책
정책은 Azure Portal이 구성을 통해 API의 동작을 변경할 수 있게 하는 API Management의 강력한 기능입니다. 정책은 API의 요청이나 응답에 따라 순차적으로 실행되는 명령문의 컬렉션입니다. 많이 사용되는 명령문에는 XML에서 JSON으로 형식 변환, 개발자로부터 들어오는 호출 수를 제한하는 호출 속도 한도 등이 포함되며 다양한 다른 정책도 사용할 수 있습니다.

정책이 다르게 지정하지 않는 한 정책 식은 어떤 API Management 정책에서든 특성 값 또는 텍스트 값으로 사용될 수 있습니다. [제어 흐름](./api-management-advanced-policies.md#choose) 및 [변수 설정](./api-management-advanced-policies.md#set-variable) 정책 등의 일부 정책은 정책 식을 기반으로 합니다. 자세한 내용은 [고급 정책](./api-management-advanced-policies.md#AdvancedPolicies) 및 [정책 식](./api-management-policy-expressions.md)을 참조하세요.


API Management 정책의 전체 목록을 보려면 [정책 참조][Policy reference](영문)를 참조하세요. 정책 사용 및 구성에 대한 자세한 내용은 [API Management 정책][API Management policies]을 참조하세요. 속도 제한 및 할당량 정책을 사용하여 제품을 만드는 방법에 대한 자습서는 [고급 제품 설정을 만들고 구성하는 방법][How to create and configure advanced product settings]을 참조하세요.


## <a name="developer-portal"></a><a name="developer-portal"> </a> 개발자 포털
개발자 포털은 개발자가 API에 대해 알아보고, 작업을 보고 호출하고, 제품을 구독할 수 있는 곳입니다. 잠재 고객은 개발자 포털을 방문하고 API 및 작업을 보고 등록할 수 있습니다. 개발자 포털의 URL은 Azure Portal에서 해당 API Management 서비스 인스턴스에 대한 대시보드에서 찾을 수 있습니다.

사용자 지정 콘텐츠를 추가하고, 스타일을 사용자 지정하고, 브랜딩을 추가하여 개발자 포털의 모양을 사용자 지정할 수 있습니다.


## <a name="next-steps"></a>다음 단계

다음 빠른 시작을 완료하고 Azure API Management 사용을 시작합니다.

> [!div class="nextstepaction"]
> [Azure API Management 인스턴스 만들기](get-started-create-service-instance.md)

[APIs and operations]: #apis
[Products]: #products
[Groups]: #groups
[Developers]: #developers
[Policies]: #policies
[Developer portal]: #developer-portal

[How to create APIs]: ./import-and-publish.md
[How to add operations to an API]: ./mock-api-responses.md
[How to create and publish a product]: api-management-howto-add-products.md
[How to create and use groups]: api-management-howto-create-groups.md
[How to associate groups with developers]: api-management-howto-create-groups.md#associate-group-developer
[How to create and configure advanced product settings]: transform-api.md
[How to create or invite developers]: api-management-howto-create-or-invite-developers.md
[Policy reference]: ./api-management-policies.md
[API Management policies]: api-management-howto-policies.md
[Create an API Management service instance]: get-started-create-service-instance.md
