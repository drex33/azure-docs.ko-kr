---
title: Connect Health를 사용하여 Azure AD에서 AD FS 로그인 | Microsoft Docs
description: 이 문서에서는 Azure AD Connect Health 로그인 보고서와 AD FS 로그인을 통합하는 방법을 설명합니다.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/16/2021
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 890bf61eb98218df53a6aa66c9e8562881b8d2df
ms.sourcegitcommit: 5be51a11c63f21e8d9a4d70663303104253ef19a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/25/2021
ms.locfileid: "112895601"
---
# <a name="ad-fs-sign-ins-in-azure-ad-with-connect-health---preview"></a>Connect Health를 사용하여 Azure AD에서 AD FS 로그인 - 미리 보기

이제 Connect Health를 사용하여 AD FS 로그인을 Azure Active Directory 로그인 보고서에 통합할 수 있습니다. [Azure AD 로그인 보고서](../reports-monitoring/concept-all-sign-ins.md)에는 사용자, 애플리케이션 및 관리되는 리소스가 Azure AD에 로그인하고 리소스에 액세스하는 시기에 대한 정보가 포함됩니다. 

AD FS용 Connect Health 에이전트는 서버 버전에 따라 AD FS의 여러 이벤트 ID를 상호 연결하여 요청에 대한 정보, 그리고 요청이 실패할 경우 오류 세부 정보를 제공합니다. 이 정보는 Azure AD 로그인 보고서 스키마와 관련되어 있으며 Azure AD 로그인 보고서 UX에 표시됩니다. 보고서와 함께 새 Log Analytics 스트림을 AD FS 데이터 및 새 Azure Monitor 통합 문서 템플릿과 함께 사용할 수 있습니다. 템플릿은 AD FS 계정 잠금, 잘못된 암호 시도 및 예기치 않은 로그인 시도 급증과 같은 시나리오에 대한 심층 분석을 위해 사용 및 수정할 수 있습니다.

## <a name="prerequisites"></a>필수 조건
* AD FS용 Azure AD Connect Health가 설치되고 최신 버전(3.1.95.0 이상)으로 업그레이드되었습니다.
* Azure AD 로그인을 볼 수 있는 전역 관리자 또는 보고서 읽기 권한자 역할

## <a name="what-data-is-displayed-in-the-report"></a>보고서에는 무슨 데이터가 표시되나요?
제공되는 데이터는 Azure AD 로그인에 사용할 수 있는 동일한 데이터를 미러링합니다. 로그인 유형(Azure AD 또는 AD FS)에 따라 정보가 포함된 5개의 탭을 사용할 수 있습니다. Connect Health는 서버 버전에 따라 AD FS의 이벤트를 상호 연결하고 이를 AD FS 스키마와 일치시킵니다. 



#### <a name="user-sign-ins"></a>사용자 로그인 
로그인 블레이드의 각 탭은 아래와 같은 기본값을 보여줍니다.
* 로그인 날짜
* 요청 ID
* 사용자 이름 또는 사용자 ID
* 로그인의 상태
* 로그인에 사용되는 디바이스의 IP 주소
* 로그인 식별자

#### <a name="authentication-method-information"></a>인증 방법 정보
다음 값이 인증 탭에 표시될 수 있습니다. 인증 방법은 AD FS 감사 로그에서 가져옵니다.

|인증 방법|설명|
|-----|-----|
|양식|사용자 이름/암호 인증|
|Windows|Windows 통합 인증|
|인증서|스마트 카드/VirtualSmart 인증서를 사용하는 인증|
|WindowsHelloForBusiness|이 필드는 비즈니스용 Windows Hello를 사용하는 인증을 위한 것입니다. (Microsoft Passport 인증)|
|디바이스 | 디바이스 인증을 인트라넷/엑스트라넷의 "기본" 인증으로 선택하고 디바이스 인증을 수행하는 경우에 표시됩니다.  이 시나리오에는 별도의 사용자 인증이 없습니다.| 
|페더레이션|AD FS에서 인증을 수행하지 않았지만 타사 ID 공급자에게 보냈습니다.|
|SSO |Single Sign-On 토큰을 사용한 경우 이 필드가 표시됩니다. SSO에 MFA가 있는 경우 다단계로 표시됩니다.|
|다단계|Single Sign-On 토큰에 MFA가 있고 인증에 사용된 경우 이 필드는 다단계로 표시됩니다.|
|Azure MFA|Azure MFA는 AD FS에서 추가 인증 공급자로 선택되며 인증에 사용되었습니다.|
|ADFSExternalAuthenticationProvider|이 필드는 타사 인증 공급자를 등록하여 인증에 사용한 경우에 해당합니다.|


#### <a name="ad-fs-additional-details"></a>AD FS 추가 세부 정보
AD FS 로그인에 사용할 수 있는 세부 정보는 다음과 같습니다.
* 서버 이름
* IP 체인
* 프로토콜

### <a name="enabling-log-analytics-and-azure-monitor"></a>Log Analytics 및 Azure Monitor 사용
Log Analytics는 AD FS 로그인에 사용할 수 있으며 Sentinel과 같은 다른 Log Analytics 통합 구성 요소와 함께 사용할 수 있습니다.

> [!NOTE] 
> AD FS 로그인은 조직의 AD FS 로그인 횟수에 따라 Log Analytics 비용이 크게 증가할 수 있습니다. Log Analytics를 사용하거나 사용하지 않도록 설정하려면 해당 스트림의 확인란을 선택합니다.

기능에 대해 Log Analytics를 사용하도록 설정하려면 Log Analytics 블레이드로 이동하여 "ADFSSignIns" 스트림을 선택합니다. 이 옵션을 선택하면 AD FS 로그인이 Log Analytics로 이동될 수 있습니다.

업데이트된 Azure Monitor 통합 문서 템플릿에 액세스하려면 "Azure Monitor 템플릿"으로 이동하여 "로그인" 통합 문서를 선택합니다.
통합 문서에 대한 자세한 내용을 보려면 [Azure Monitor 통합 문서](https://aka.ms/adfssigninspreview)를 방문하세요.




### <a name="frequently-asked-questions"></a>질문과 대답
***볼 수 있는 로그인 유형은 무엇인가요?***
로그인 보고서는 O Auth, WS-Fed, SAML 및 WS-Trust 프로토콜을 통한 로그인을 지원합니다. 

***로그인 보고서에는 여러 유형의 로그인이 어떻게 표시되나요?***
Seamless SSO 로그인이 수행되는 경우 하나의 상관 관계 ID가 있는 로그인에 대한 행이 하나 있습니다.
단일 단계 인증이 수행되면 두 개의 행이 동일한 상관 관계 ID로 채워지지만 두 가지 다른 인증 방법(예: 양식, SSO)으로 채워집니다.
다단계 인증의 경우 공유 상관 관계 ID가 있는 3개의 행과 해당하는 3개의 인증 방법(예: 양식, AzureMFA, Multifactor)이 있습니다. 이 예제의 다단계에서는 이 경우에 SSO에 MFA가 있음을 보여 줍니다.

***보고서에서 볼 수 있는 오류는 무엇인가요?***
로그인 보고서 및 설명에 채워지는 AD FS 관련 오류에 대한 전체 목록을 보려면 [AD FS 도움말 오류 코드 참조](https://adfshelp.microsoft.com/References/ConnectHealthErrorCodeReference)를 방문하세요.

***로그인의 "사용자" 섹션에 "00000000-0000-0000-0000-000000000000"이 표시됩니다. 무엇을 의미하나요?***
로그인에 실패하고 시도한 UPN이 기존 UPN과 일치하지 않는 경우 "사용자", "사용자 이름" 및 "사용자 ID" 필드는 "00000000-0000-0000-0000-000000000000"으로 채워지고 "로그인 식별자"는 사용자가 입력한 시도 값으로 채워집니다. 이러한 경우 로그인을 시도하는 사용자가 없습니다.

***온-프레미스 이벤트를 Azure AD 로그인 보고서와 상호 연결하려면 어떻게 해야 하나요?***
AD FS에 대한 Azure AD Connect Health 에이전트는 서버 버전에 따라 AD FS에서 이벤트 ID를 연관시킵니다. 이 이벤트는 AD FS 서버의 보안 로그에서 사용할 수 있습니다. 

***일부 AD FS 로그인에 대한 애플리케이션 ID/이름에 NotSet 또는 NotApplicable가 표시되는 이유는 무엇인가요?***
AD FS Sign-In 보고서는 OAuth 로그인을 위한 애플리케이션 ID 필드에 OAuth ID를 표시합니다. WS-Fed, WS-Trust 로그인 시나리오에서는 애플리케이션 ID가 NotSet 또는 NotApplicable이고 리소스 ID 및 신뢰 당사자 식별자가 리소스 ID 필드에 표시됩니다.

***리소스 ID 및 리소스 이름 필드가 "설정되지 않음"으로 표시되는 이유는 무엇인가요?***
"사용자 이름 및 암호가 잘못됨" 및 WSTrust 기반의 실패한 로그인과 같은 일부 오류 발생 상황에서 ResourceId/Name 필드에 "NotSet"이 표시됩니다.

***미리 보기에서 보고서에 대해 알려진 문제가 더 있나요?***
이 보고서에는 알려진 문제가 있습니다. 여기에는 "기본 정보" 탭의 "인증 요구 사항" 필드가 로그인 여부에 관계 없이 AD FS 로그인에 대한 단일 요소 인증 값으로 채워집니다. 또한 인증 세부 정보 탭에는 요구 사항 필드 아래에 "기본 또는 보조"가 표시되고, 기본 또는 보조 인증 유형을 구분하기 위해 진행 중인 수정이 표시됩니다.


## <a name="related-links"></a>관련 링크
* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Azure AD Connect Health Agent 설치](how-to-connect-health-agent-install.md)
* [위험 IP 보고서](how-to-connect-health-adfs-risky-ip.md)
