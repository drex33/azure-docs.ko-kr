---
title: Azure Active Directory B2C로 Saviynt를 구성하기 위한 자습서
titleSuffix: Azure AD B2C
description: 애플리케이션 간 통합을 위해 Saviynt로 Azure Active Directory B2C를 구성하여 IT 현대화를 간소화하고 보안, 거버넌스 및 규정 준수를 강화하도록 하는 자습서입니다. 
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/20/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 2685570bb14cf177f0a658196aa1750f9d8e02a1
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128558351"
---
# <a name="tutorial-for-configuring-saviynt-with-azure-active-directory-b2c"></a>Azure Active Directory B2C로 Saviynt를 구성하기 위한 자습서

이 샘플 자습서에서는 AD(Azure Active Directory) B2C를 [Saviynt](https://saviynt.com/integrations/azure-ad/for-b2c/)와 통합하는 방법에 대한 지침을 제공합니다. Saviynt의 Security Manager 플랫폼은 단일 통합 플랫폼으로 오늘날의 비즈니스에서 요구하는 가시성, 보안 및 거버넌스를 제공합니다. Saviynt는 애플리케이션 리스크 및 거버넌스, 인프라 관리, 권한 계정 관리, 고객 위험 분석을 통합합니다.

이 샘플 자습서에서는 Azure AD B2C 사용자에게 위임 관리를 기반으로 세분화된 액세스 제어를 제공하도록 Saviynt를 설정합니다. Saviynt는 사용자에게 Azure AD B2C 사용자를 관리할 권한이 있는지 확인하기 위해 다음 검사를 수행합니다.

- 사용자가 특정 작업을 수행할 수 있는지 여부를 결정하는 기능 수준 보안입니다. 예를 들어 사용자 만들기, 사용자 업데이트, 사용자 암호 재설정 등이 있습니다.

- 사용자가 사용자 관리 작업 중에 다른 사용자의 특정 속성을 읽거나 쓸 수 있는지 여부를 결정하는 필드 수준 보안입니다. 예를 들어 지원 센터 에이전트는 전화 번호만 업데이트할 수 있으며 다른 모든 특성에 대해서는 읽기만 가능합니다.

- 사용자가 특정 사용자에 대해 특정 작업을 수행할 수 있는지 여부를 결정하는 데이터 수준 보안입니다. 예를 들어 영국 지역의 지원 센터 관리자는 영국 사용자만 관리할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

시작하려면 다음이 필요합니다.

- Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.

- [Azure AD B2C 테넌트](./tutorial-create-tenant.md). 테넌트는 Azure 구독에 연결됩니다.

- Saviynt [구독](https://saviynt.com/contact-us/)

## <a name="scenario-description"></a>시나리오 설명

Saviynt 통합에는 다음 구성 요소가 포함됩니다.

- [Azure AD B2C](https://azure.microsoft.com/services/active-directory/external-identities/b2c/) – 고객이 가입하고, 로그인하고, 프로필을 관리하는 방법을 사용자 지정 컨트롤할 수 있도록 하는 서비스형 비즈니스 대 고객 ID입니다.

- [Saviynt](https://saviynt.com/integrations/azure-ad/for-b2c/) – Azure AD B2C 사용자의 액세스 거버넌스 및 사용자 수명 주기 관리를 위해 세분화된 위임 관리를 제공하는 ID 거버넌스 플랫폼입니다.  

- [Microsoft Graph API](/graph/use-the-api) – 해당 API는 Azure AD B2C 사용자 및 Azure AD B2C에서 사용자의 액세스를 관리하기 위해 Saviynt용 인터페이스를 제공합니다.

다음 다이어그램은 아키텍처 구현을 보여 줍니다.

![Saviynt 아키텍처 다이어그램을 보여 주는 이미지](./media/partner-saviynt/saviynt-architecture-diagram.png)

|단계 | 설명 |
|:-----| :-----------|
| 1. | 위임된 관리자는 Saviynt를 통해 Azure AD B2C 사용자 관리 작업을 시작합니다.
| 2. | Saviynt는 권한 부여 엔진을 사용하여 위임된 관리자가 특정 작업을 수행할 수 있는지 확인합니다.
| 3. | Saviynt의 권한 부여 엔진은 권한 부여 성공/실패 응답을 전송합니다.
| 4. | Saviynt는 위임된 관리자가 필요한 작업을 수행하도록 합니다.
| 5. | Saviynt는 Azure AD B2C에서 사용자를 관리하기 위해 사용자 특성과 함께 Microsoft Graph API를 호출
| 6. | 그러면 Microsoft Graph API가 Azure AD B2C에서 사용자를 생성/업데이트/삭제합니다.
| 7. | Azure AD B2C는 성공/실패 응답을 보냅니다.
| 8. | 그러면 Microsoft Graph API가 Saviynt로 응답을 반환합니다.

## <a name="onboard-with-saviynt"></a>Saviynt으로 온보딩

1. Saviynt 계정을 만들려면 [Saviynt](https://saviynt.com/contact-us/)에 문의

2. 위임 관리 정책을 만들고 사용자에게 위임된 관리자의 다양한 역할을 할당합니다.

## <a name="configure-azure-ad-b2c-with-saviynt"></a>Saviynt로 Azure AD B2C 구성

### <a name="create-an-azure-ad-application-for-saviynt"></a>Saviynt용 Azure AD 애플리케이션 만들기

1. [Azure Portal](https://portal.azure.com/#home)에 로그인합니다.
1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
1. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 Azure AD B2C 디렉터리를 찾은 다음, **전환** 을 선택합니다.
1. Azure Portal에서 **Azure AD B2C** 를 검색하여 선택합니다.
1. **앱 등록** > **새 등록** 을 선택합니다.
1. 애플리케이션의 이름을 입력합니다. 예를 들어 Saviynt라고 입력하고 **만들기** 를 선택합니다.
1. **API 사용 권한** 으로 이동하여 **+ 사용 권한 추가** 를 선택합니다.
1. API 사용 권한 요청 페이지가 나타납니다. **Microsoft APIs** 탭을 선택하고 일반적으로 사용되는 Microsoft API인 **Microsoft Graph** 를 선택합니다.
1. 다음 페이지로 이동하여 **애플리케이션 사용 권한** 을 선택합니다.
1. **디렉터리** 를 선택하고 **Directory.Read.All** 및 **Directory.ReadWrite.All** 확인란을 선택합니다.
1. **사용 권한 추가** 를 선택합니다. 추가된 사용 권한을 검토합니다.
1. **기본 디렉터리에 대한 관리자 동의 부여** > **저장** 을 선택합니다.
1. **인증서 및 암호** 로 이동하고 **+ 클라이언트 암호 추가** 를 선택합니다. 클라이언트 암호 설명을 입력하고 만료 옵션을 선택한 다음 **추가** 를 선택합니다.
1. 비밀 키가 생성되고 클라이언트 암호 섹션에 표시됩니다. 나중에 사용해야 합니다.

1. **개요** 로 이동하여 **클라이언트 ID** 및 **테넌트 ID** 를 가져옵니다.
1. Saviynt의 설정을 완료하려면 테넌트 ID, 클라이언트 ID, 클라이언트 암호가 필요합니다.

### <a name="enable-saviynt-to-delete-users"></a>Saviynt를 통해 사용자 삭제

다음 단계에서는 Saviynt를 사용하여 Azure AD B2C에서 사용자 삭제 작업을 수행하는 방법을 설명합니다.

>[!NOTE]
>[서비스 주체에 관리자 역할 액세스 권한을 부여하기 전에 위험을 평가합니다.](../active-directory/develop/app-objects-and-service-principals.md)

1. Windows 워크스테이션/서버에 최신 버전의 MSOnline PowerShell 모듈을 설치합니다.

2. AzureAD PowerShell 모듈에 연결하고 다음 명령을 실행합니다.

```powershell
Connect-msolservice #Enter Admin credentials of the Azure portal
$webApp = Get-MsolServicePrincipal –AppPrincipalId “<ClientId of Azure AD Application>”
Add-MsolRoleMember -RoleName "Company Administrator" -RoleMemberType ServicePrincipal -RoleMemberObjectId $webApp.ObjectId
```

## <a name="test-the-solution"></a>솔루션 테스트

Saviynt 애플리케이션 테넌트를 찾아보고 사용자 수명 주기 관리 및 액세스 거버넌스 사용 사례를 테스트합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조하세요.

- [Azure AD B2C의 사용자 지정 정책](./custom-policy-overview.md)

- [Azure AD B2C에서 사용자 지정 정책 시작](tutorial-create-user-flows.md?pivots=b2c-custom-policy)

- [웹 API 애플리케이션 생성](./add-web-api-application.md)