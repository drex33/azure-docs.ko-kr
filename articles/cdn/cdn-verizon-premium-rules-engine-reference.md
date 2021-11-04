---
title: Azure CDN 규칙 엔진 참조 | Microsoft Docs
description: Azure CDN 규칙 엔진 일치 조건 및 기능에 대한 참조 설명서
services: cdn
author: duongau
ms.service: azure-cdn
ms.topic: article
ms.date: 05/26/2020
ms.author: duau
ms.openlocfilehash: 928e3fb96cd1acdbbfe7aceea5f5ab9ceca97909
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131461417"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-reference"></a>Verizon Premium의 Azure CDN 규칙 엔진 참조

이 아티클에서는 Azure CDN(Content Delivery Network) [규칙 엔진](cdn-verizon-premium-rules-engine.md)에 제공되는 일치 조건 및 기능에 대해 자세히 설명합니다.

규칙 엔진은 특정 형식의 요청이 CDN에서 처리되는 방식을 최종적으로 결정하도록 설계되었습니다.

**일반적인 사용**:

- 사용자 지정 캐시 정책을 재정의하거나 정의합니다.
- 중요한 콘텐츠에 대한 요청에 대해 보안을 유지하거나 거부합니다.
- 요청을 리디렉션합니다.
- 사용자 지정 로그 데이터를 저장합니다.
## <a name="key-concepts"></a>주요 개념
규칙 엔진 설정에 대한 주요 개념이 아래에 설명되어 있습니다.
### <a name="draft"></a>초안
정책의 초안은 요청과 이 요청에 적용되는 작업 집합을 식별하는 하나 이상의 규칙으로 구성됩니다. 초안은 사이트 트래픽에 영향을 주지 않고 구성을 자주 업데이트할 수 있는 진행 중인 작업입니다. 초안을 완성할 준비가 되면 읽기 전용 정책으로 변환해야 합니다.

### <a name="rule"></a>규칙
규칙은 하나 이상의 요청 유형과 이 요청에 적용되는 작업 집합을 식별합니다.

ASE는 다음으로 구성됩니다. 

- 요청을 식별하는 논리를 정의하는 조건식 집합
- 요청을 식별하는 데 사용되는 조건을 정의하는 일치 조건 집합
- CDN이 위의 요청을 처리하는 방법을 정의하는 기능 집합
이러한 요소는 다음 그림에서 확인할 수 있습니다.

![규칙의 조건식, 일치, 기능이 표시된, 레이블이 있는 스크린샷](./media/cdn-verizon-premium-rules-engine-reference/verizon-rules-engine-reference.png)

### <a name="policy"></a>정책
읽기 전용 규칙 집합으로 구성되는 정책은 다음을 수행하는 방법을 제공합니다.

- 규칙의 여러 변형 만들기, 저장, 관리
- 이전에 배포된 버전으로 롤백
- 이벤트 관련 규칙을 미리 준비(예: 고객 원본 유지 관리의 결과로 트래픽을 리디렉션하는 규칙)

> [!NOTE]
> 환경별로 하나의 단일 정책만 허용되지만 필요에 따라 여러 정책을 배포할 수 있습니다.

### <a name="deploy-request"></a>배포 요청
배포 요청은 정책이 스테이징 환경 또는 프로덕션 환경에 신속하게 적용될 수 있는 간단하고 간소화된 절차를 제공합니다. 이러한 환경에 적용되는 변경 내용에 대한 추적을 용이하게 하기 위해 배포 요청 기록이 제공됩니다.

> [!NOTE]
> 자동 유효성 검사 및 오류 검색 시스템을 통과하지 못하는 요청만 수동 검토 및 승인이 필요합니다.

### <a name="rule-precedence"></a>규칙 우선 순위
정책에 포함된 규칙은 일반적으로 목록에 있는 순서대로 처리됩니다(즉, 위에서 아래로). 요청에 충돌하는 규칙이 있는 경우 마지막에 처리되는 규칙이 적용됩니다.

### <a name="policy-deployment-workflow"></a>정책 배포 워크플로
정책이 프로덕션 환경 또는 스테이징 환경에 적용될 수 있는 워크플로가 아래에 나와 있습니다.

![정책 배포 워크플로](./media/cdn-verizon-premium-rules-engine-reference/policy-deployment-workflow.png)

|단계 |Description |
|---------|---------|
|[초안 만들기](https://docs.vdms.com/cdn/index.html#HRE/AdministeringDraftsandRules.htm#Create)    |    초안은 CDN에서 콘텐츠에 대한 요청을 처리하는 방법을 정의하는 규칙 집합으로 구성됩니다.     |
|초안 잠금   |     초안이 완성되면 이를 잠그고 읽기 전용 정책으로 변환해야 합니다.    |
|[배포 요청 제출](https://docs.vdms.com/cdn/index.html#HRE/DeployRequest.htm)   |   <br> 배포 요청을 통해 테스트 또는 프로덕션 트래픽에 정책을 적용할 수 있습니다.</br> <br>스테이징 또는 프로덕션 환경에 배포 요청을 제출합니다.</br>     |
|배포 요청 검토   |    <br>배포 요청에서는 자동화된 유효성 검사 및 오류 검색이 수행됩니다.</br><br>대부분의 배포 요청이 자동으로 승인되기는 하지만 좀 더 복잡한 정책에는 수동 검토가 필요합니다.</br>   |
|정책 배포([준비](https://docs.vdms.com/cdn/index.html#HRE/Environment.htm#Staging))   |  <br> 스테이징 환경에 대한 배포 요청이 승인되면 정책이 스테이징 환경에 적용됩니다. 이 환경에서는 모의 사이트 트래픽에 대해 정책을 테스트할 수 있습니다.</br><br>정책을 라이브 사이트 트래픽에 적용할 준비가 되면 프로덕션 환경에 대한 새 배포 요청을 제출해야 합니다.</br>      |
|정책 배포([프로덕션](https://docs.vdms.com/cdn/index.html#HRE/Environment.htm#Producti))   |  프로덕션 환경에 대한 배포 요청이 승인되면 정책이 프로덕션 환경에 적용됩니다. 이 환경에서는 정책이 CDN에서 라이브 트래픽을 처리하는 방법을 결정하는 최종 인증 기관 역할을 할 수 있습니다.     |
## <a name="syntax"></a>구문

특수 문자가 처리되는 방식은 일치 조건 또는 기능이 텍스트 값을 처리하는 방식에 따라 다릅니다. 일치 조건 또는 기능은 다음 방법 중 하나로 텍스트를 해석할 수 있습니다.

- [**리터럴 값**](#literal-values)
- [**와일드카드 값**](#wildcard-values)
- [**정규식**](#regular-expressions)

### <a name="literal-values"></a>리터럴 값

리터럴 값으로 해석되는 텍스트는 % 기호를 제외한 모든 특수 문자를 일치되어야 하는 값의 일부로 취급합니다. 즉, `\'*'\`로 설정된 리터럴 일치 조건은 정확한 값(예: `\'*'\`)을 찾은 경우에만 충족됩니다.

백분율 기호는 URL 인코딩을 나타내는 데 사용됩니다(예: `%20`).

### <a name="wildcard-values"></a>와일드카드 값

와일드카드 값으로 해석되는 텍스트는 특수 문자에 추가적인 의미를 할당합니다. 다음 표에서는 다음 문자 집합이 해석되는 방식을 설명합니다.

문자 | Description
----------|------------
\ | 백슬래시는 이 테이블에 지정된 문자를 이스케이프하는 데 사용됩니다. 백슬래시는 이스케이프해야 하는 특수 문자 바로 앞에 지정되어야 합니다.<br/>예를 들어 다음 구문은 별표를 이스케이프합니다.`\*`
% | 백분율 기호는 URL 인코딩을 나타내는 데 사용됩니다(예: `%20`).
\* | 별표는 하나 이상의 문자를 나타내는 와일드카드입니다.
Space | 공백 문자는 지정된 값 또는 패턴에 의해 일치 조건이 충족될 수 있음을 나타냅니다.
'value' | 작은따옴표는 특별한 의미가 없습니다. 그러나 값을 리터럴 값으로 취급해야 함을 나타내기 위해 작은따옴표 쌍을 사용합니다. 다음과 같은 방법으로 사용할 수 있습니다.<br><br/>- 지정된 값이 비교 값의 일부와 일치할 때마다 일치 조건이 충족될 수 있습니다.  예를 들어 `'ma'`는 다음 문자열 중 하나와 일치합니다. <br/><br/>/business/**ma** rathon/asset.htm<br/>**ma** p.gif<br/>/business/template.**ma** p<br /><br />- 특수 문자를 리터럴 문자로 지정할 수 있습니다. 예를 들어 공백 문자를 작은따옴표 쌍으로 묶어 리터럴 공백 문자를 지정할 수 있습니다(즉, `' '` 또는 `'sample value'`).<br/>- 빈 값을 지정할 수 있습니다. 작은따옴표 쌍(예: '')을 지정하여 빈 값을 지정합니다.<br /><br/>**중요:**<br/>- 지정된 값이 와일드 카드를 포함하지 않으면 자동으로 리터럴 값으로 간주됩니다. 즉, 작은따옴표 집합을 지정할 필요가 없습니다.<br/>- 백슬래시가 이 표의 다른 문자를 이스케이프하지 않으면 작은따옴표 집합으로 묶어서 지정할 때 무시됩니다.<br/>- 특수 문자를 리터럴 문자로 지정하는 또 다른 방법은 백슬래시를 사용하여 이스케이프하는 것입니다(즉, `\`).

### <a name="regular-expressions"></a>정규식

정규식은 텍스트 값 내에서 검색될 패턴을 정의합니다. 정규식 표기법은 다양한 기호에 대한 특정 의미를 정의합니다. 다음 표에서는 특수 문자가 정규식을 지원하는 일치 조건 및 기능에 의해 처리되는 방식을 나타냅니다.

특수 문자 | Description
------------------|------------
\ | 백슬래시는 뒤에 오는 문자를 이스케이프합니다. 그러면 해당 문자가 정규식 의미를 갖지 않고 리터럴 값으로 처리됩니다. 예를 들어 다음 구문은 별표를 이스케이프합니다.`\*`
% | 백분율 기호의 의미는 사용법에 따라 달라집니다.<br/><br/> `%{HTTPVariable}`: 이 구문은 HTTP 변수를 식별합니다.<br/>`%{HTTPVariable%Pattern}`: 이 구문은 백분율 기호를 사용하여 HTTP 변수를 식별하고 구분 기호로 지정합니다.<br />`\%`: 백분율 기호를 이스케이프하면 리터럴 값으로 사용되거나 URL 인코딩을 나타낼 수 있습니다(예: `\%20`).
\* | 별표를 사용하면 앞에 오는 문자의 일치 여부가 0번 이상 확인될 수 있습니다.
Space | 공백 문자는 일반적으로 리터럴 문자로 취급됩니다.
'value' | 작은따옴표는 리터럴 문자로 처리됩니다. 작은따옴표 쌍은 특별한 의미가 없습니다.

정규식을 지원하는 일치 조건 및 기능은 PCRE(Perl 호환 정규식)에서 정의한 패턴을 허용합니다.



## <a name="next-steps"></a>다음 단계

- [규칙 엔진 일치 조건](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [규칙 엔진 조건식](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [규칙 엔진 기능](cdn-verizon-premium-rules-engine-reference-features.md)
- [규칙 엔진을 사용하여 HTTP 동작 재정의](cdn-verizon-premium-rules-engine.md)
- [Azure CDN 개요](cdn-overview.md)
