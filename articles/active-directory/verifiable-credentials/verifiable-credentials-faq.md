---
title: 질문과 대답 - Azure가 확인 가능한 자격 증명(미리 보기)
description: 확인 가능한 자격 증명에 대한 일반적인 질문에 대한 답변을 확인합니다
author: barclayn
manager: karenh444
ms.service: active-directory
ms.subservice: verifiable-credentials
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: 760f90a766eca28e3d6cc009e3370192a9bd5285
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129994806"
---
# <a name="frequently-asked-questions-faq-preview"></a>FAQ(질문과 대답)(미리 보기)

이 페이지에는 확인 가능한 자격 증명 및 탈중앙화 ID에 대한 일반적인 질문과 대답이 포함되어 있습니다. 질문은 다음 섹션으로 구성됩니다.

- [용어 모음 및 기본 사항](#the-basics)
- [탈중앙화 ID에 대한 개념적 질문](#conceptual-questions)
- [확인 가능한 자격 증명 미리 보기 사용에 대한 질문](#using-the-preview)

> [!IMPORTANT]
> Azure Active Directory 확인 가능한 자격 증명은 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="the-basics"></a>기본 사항

### <a name="what-is-a-did"></a>DID란? 

DID(탈중앙화 식별자)는 리소스에 대한 액세스를 보호하고, 자격 증명을 서명 및 확인하고, 애플리케이션 데이터 교환을 용이하게 하는데 사용할 수 있는 식별자입니다. 기존 사용자 이름 및 이메일 주소와 달리 DID는 엔터티 자체(개인, 디바이스, 회사)가 소유하고 제어합니다. DID는 외부 조직 또는 신뢰할 수 있는 매개체와는 독립적으로 존재합니다. [W3C 탈중앙화 식별자 사양](https://www.w3.org/TR/did-core/)에서 이를 자세히 설명합니다.

### <a name="why-do-we-need-a-did"></a>DID가 필요한 이유는 무엇인가요?

디지털 신뢰를 사용하려면 기본적으로 참가자가 자신의 ID를 소유하고 제어해야 하며 ID는 식별자에서 시작됩니다.
일별 대규모 시스템 위반 및 중앙 집중식 식별자 허니팟에 대한 공격이 만연한 시대에서 ID의 탈중앙화는 소비자와 비즈니스에게 중요한 보안 니즈로 자리잡고 있습니다.
ID를 소유하고 제어하는 개인은 확인 가능한 데이터와 증명을 교환할 수 있습니다. 분산 자격 증명 환경을 사용하면 현재 수동 및 인건비 집약적인 많은 비즈니스 프로세스를 자동화할 수 있습니다.

### <a name="what-is-a-verifiable-credential"></a>확인 가능한 자격 증명이란? 

자격 증명은 일상의 일부입니다. 운전 면허증은 자동차를 운전할 수 있음을 입증하는 데 사용되고, 대학 학위를 이용하여 교육 수준을 입증할 수 있으며, 정부에서 발급한 여권을 통해 국가 간을 여행할 수 있습니다. 확인 가능한 자격 증명은 암호화된 보안, 개인 정보 보호, 컴퓨터 확인 가능한 방식으로 웹에서 이러한 종류의 자격 증명을 표현하는 메커니즘을 제공합니다. [W3C가 확인 가능한 자격 증명 사양](https://www.w3.org/TR/vc-data-model//)에서 이를 자세히 설명합니다.


## <a name="conceptual-questions"></a>개념적 질문

### <a name="what-happens-when-a-user-loses-their-phone-can-they-recover-their-identity"></a>사용자가 휴대폰을 분실하면 어떻게 되나요? ID를 복구할 수 있나요?

여러 가지 방법으로 사용자에게 복구 메커니즘을 제공할 수 있으며, 각 방법에는 고유한 장단점이 있습니다. 현재 사용자의 개인 정보 및 자체 독립성을 존중하면서 편의성과 보안을 제공하는 옵션을 평가하고 복구 방법을 디자인하고 있습니다.

### <a name="how-can-a-user-trust-a-request-from-an-issuer-or-verifier-how-do-they-know-a-did-is-the-real-did-for-an-organization"></a>사용자가 발급자 또는 검증 도구에서 요청을 신뢰하려면 어떻게 해야 하나요? DID가 진짜 조직의 DID인지 어떻게 알 수 있나요?

이미 알려진 기존 시스템인 도메인 이름에 DID를 연결하기 위해 [Decentralized Identity Foundation의 잘 알려진 DID 구성 사양](https://identity.foundation/.well-known/resources/did-configuration/)을 구현했습니다. Azure Active Directory가 확인 가능한 자격 증명을 사용하여 만든 DID에는 DID 문서에서 인코딩할 루트 도메인 이름을 포함하는 옵션이 있습니다. 자세한 내용은 [분산 식별자에 도메인 연결](how-to-dnsbind.md) 문서를 참조하세요.  

### <a name="why-does-the-verifiable-credential-preview-use-ion-as-its-did-method-and-therefore-bitcoin-to-provide-decentralized-public-key-infrastructure"></a>확인 가능한 자격 증명 미리 보기에서 DID 메서드로 ION를 사용하고 탈중앙화 공개 키 인프라를 제공하기 위해 Bitcoin을 사용하는 이유는 무엇인가요?

ION은 Bitcoin에서 실행되는 탈중앙화되고, 허가가 불필요한, 확장성 있는 탈중앙화 식별자 계층 2 네트워크입니다. 특수 암호자산 토큰, 신뢰할 수 있는 유효성 검사기, 중앙 집중식 합의 메커니즘을 포함하지 않고 확장성을 달성합니다. 탈중앙화 네트워크의 장점으로 인해 시간순 이벤트 레코드 시스템에 대한 높은 수준의 불변성을 제공하기 때문에 기본 계층 1의 기저에 Bitcoin을 사용합니다.

## <a name="using-the-preview"></a>미리 보기 사용

### <a name="why-must-i-use-nodejs-for-the-verifiable-credentials-preview-any-plans-for-other-programming-languages"></a>확인 가능한 자격 증명 미리 보기에 NodeJS를 사용해야 하는 이유는 무엇인가요? 다른 프로그래밍 언어에 대한 계획은 무엇인가요? 

NodeJS가 애플리케이션 개발자를 위한 매우 인기 있는 플랫폼이기 때문에 NodeJS를 선택했습니다. 개발자가 자격 증명을 발급하고 확인할 수 있도록 하는 Rest API를 출시할 예정입니다. 

### <a name="is-any-of-the-code-used-in-the-preview-open-source"></a>미리 보기 오픈 소스에서 사용되는 코드가 있나요?

예 다음 리포지토리는 서비스의 오픈 소스 구성 요소입니다.

1. [GitHub에서 SideTree](https://github.com/decentralized-identity/sidetree)
2. [GitHub에서 Node용 VC SDK](https://github.com/microsoft/VerifiableCredentials-Verification-SDK-Typescript)
3. [GitHub에서 탈중앙화 ID 전자지갑을 빌드하기 위한 Android SDK](https://github.com/microsoft/VerifiableCredential-SDK-Android)
4. [GitHub에서 탈중앙화 ID 전자지갑을 빌드하기 위한 iOS SDK](https://github.com/microsoft/VerifiableCredential-SDK-iOS)


## <a name="what-are-the-licensing-requirements"></a>라이선싱 요구 사항은 무엇인가요?

확인 가능한 자격 증명의 미리 보기를 사용하려면 Azure AD P2 라이선스가 필요합니다. 이 서비스에 대한 가격 책정은 사용량에 따라 청구될 것으로 예상하기 때문에 이는 일시적인 요구 사항입니다. 


## <a name="next-steps"></a>다음 단계

- [Azure Active Directory 확인 가능한 자격 증명을 사용자 지정하는 방법](credential-design.md)
