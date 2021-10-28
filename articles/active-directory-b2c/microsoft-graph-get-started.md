---
title: Microsoft Graph 애플리케이션 등록
titleSuffix: Azure AD B2C
description: 필요한 Graph API 권한이 부여된 애플리케이션을 등록하여 Microsoft Graph를 통해 Azure AD B2C 리소스를 관리할 준비를 합니다.
services: B2C
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/20/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: 48f60c6769812e28a0981329885c60995d1d0e1b
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130036436"
---
# <a name="register-a-microsoft-graph-application"></a>Microsoft Graph 애플리케이션 등록

[Microsoft Graph][ms-graph]를 사용하면 고객 사용자 계정 및 사용자 지정 정책을 포함하여 Azure AD B2C 테넌트 내의 많은 리소스를 관리할 수 있습니다. [Microsoft Graph API][ms-graph-api]를 호출하는 스크립트 또는 애플리케이션을 작성하여 다음과 같은 테넌트 관리 작업을 자동화할 수 있습니다.

* 기존 사용자 저장소를 Azure AD B2C 테넌트로 마이그레이션합니다.
* Azure DevOps에서 Azure Pipeline을 사용하여 사용자 지정 정책 배포 및 사용자 지정 정책 키 관리
* 고유한 페이지에서 사용자 등록을 호스팅하고 백그라운드에서 Azure AD B2C 디렉터리에 사용자 계정을 만듭니다.
* 애플리케이션 등록 자동화
* 감사 로그 가져오기

다음 섹션에서는 Microsoft Graph API를 사용하여 Azure AD B2C 디렉터리의 리소스 관리를 자동화하기 위해 준비하는 데 도움이 됩니다.

## <a name="microsoft-graph-api-interaction-modes"></a>Microsoft Graph API 상호 작용 모드

Azure AD B2C 테넌트에서 리소스를 관리하기 위해 Microsoft Graph API를 사용할 때 사용할 수 있는 두 가지 통신 모드가 있습니다.

* **대화형** - 1회 실행 작업에 적합하며 B2C 테넌트의 관리자 계정을 사용하여 관리 작업을 수행합니다. 이 모드에서는 관리자가 Microsoft Graph API를 호출하기 전에 자격 증명을 사용하여 로그인해야 합니다.

* **자동화** - 예약되거나 지속적으로 실행되는 작업에 적합한 이 방법은 관리 작업을 수행하는 데 필요한 권한으로 구성한 서비스 계정을 사용합니다. 애플리케이션 및 스크립트가 *애플리케이션(클라이언트) ID* 및 **OAuth 2.0 클라이언트 자격 증명** 부여를 사용하여 인증에 사용하는 애플리케이션을 등록하여 Azure AD B2C에서 "서비스 계정"을 만듭니다. 이 경우 애플리케이션은 앞에서 설명한 대화형 방법의 관리자 사용자가 아니라 Microsoft Graph API를 호출하는 자체 역할을 합니다.

다음 섹션에 표시된 애플리케이션 등록을 만들어 **자동화된** 상호 작용 시나리오를 사용하도록 설정합니다.

OAuth 2.0 클라이언트 자격 증명 권한 부여 흐름은 현재 Azure AD B2C 인증 서비스를 통해 직접 지원되지 않지만, Azure AD B2C 테넌트에서 애플리케이션에 대해 Azure AD 및 Microsoft ID 플랫폼/토큰 엔드포인트를 사용하여 클라이언트 자격 증명 흐름을 설정할 수 있습니다. Azure AD B2C 테넌트는 Azure AD Enterprise 테넌트와 일부 기능을 공유합니다.

## <a name="register-management-application"></a>관리 애플리케이션 등록

스크립트 및 애플리케이션이 [Microsoft Graph API][ms-graph-api]와 상호 작용하여 Azure AD B2C 리소스를 관리하려면 먼저 필요한 API 권한을 부여하는 애플리케이션 등록을 Azure AD B2C 테넌트에 만들어야 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
1. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 Azure AD B2C 디렉터리를 찾은 다음, **전환** 을 선택합니다.
1. Azure Portal에서 **Azure AD B2C** 를 검색하고 선택합니다.
1. **앱 등록** 을 선택한 다음, **새 등록** 을 선택합니다.
1. 애플리케이션의 **이름** 을 입력합니다. 예: *managementapp1*.
1. **이 조직 디렉터리의 계정만** 을 선택합니다.
1. **사용 권한** 에서 *openid 및 offline_access 권한에 대한 관리자 동의 부여* 확인란을 선택 해제합니다.
1. **등록** 을 선택합니다.
1. 애플리케이션 개요 페이지에 표시된 **애플리케이션(클라이언트) ID** 를 기록합니다. 이후 단계에서 이 값을 사용합니다.

## <a name="grant-api-access"></a>API 액세스 권한 부여

애플리케이션이 Microsoft Graph의 데이터에 액세스할 수 있도록 등록된 애플리케이션에 관련 [애플리케이션 권한](/graph/permissions-reference)을 부여합니다. 애플리케이션의 유효 권한은 권한이 암시하는 전체 수준의 권한입니다. 예를 들어 Azure AD B2C 테넌트의 모든 사용자를 *만들기*, *읽기*, *업데이트* 및 *삭제* 하려면 **User.ReadWrite.All** 권한을 추가합니다. 

> [!NOTE]
> **User.ReadWrite.All** 권한에는 사용자 계정 암호 업데이트 기능이 포함되어 있지 않습니다. 애플리케이션에서 사용자 계정 암호를 업데이트해야 하는 경우 [사용자 관리자 역할을 부여](#optional-grant-user-administrator-role)합니다. [사용자 관리자](../active-directory/roles/permissions-reference.md#user-administrator) 역할을 부여할 때 **User.ReadWrite.All** 은 필요하지 않습니다. 사용자 관리자 역할에는 사용자를 관리하는 데 필요한 모든 항목이 포함됩니다.

애플리케이션에 여러 애플리케이션 권한을 부여할 수 있습니다. 예를 들어 애플리케이션에서 Azure AD B2C 테넌트의 그룹 또한 관리해야 하는 경우 **Group.ReadWrite.All** 권한도 추가합니다. 

[!INCLUDE [active-directory-b2c-permissions-directory](../../includes/active-directory-b2c-permissions-directory.md)]


## <a name="optional-grant-user-administrator-role"></a>[선택 사항] 사용자 관리자 역할 부여

애플리케이션 또는 스크립트에서 사용자 암호를 업데이트해야 하는 경우 애플리케이션에 *사용자 관리자* 역할을 할당해야 합니다. [사용자 관리자](../active-directory/roles/permissions-reference.md#user-administrator) 역할에는 애플리케이션에 부여하는 고정된 권한 집합이 있습니다. 

*사용자 관리자* 역할을 추가하려면 다음 단계를 따르세요.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
1. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 Azure AD B2C 디렉터리를 찾은 다음, **전환** 을 선택합니다.
1. **Azure AD B2C** 를 검색하고 선택합니다.
1. **관리** 아래에서 **역할 및 관리자** 를 선택합니다.
1. **사용자 관리자** 역할을 선택합니다. 
1. **할당 추가** 를 선택합니다.
1. **선택** 텍스트 상자에 이전에 등록한 애플리케이션의 이름 또는 ID를 입력합니다(예: *managementapp1*). 검색 결과에 표시되면 애플리케이션을 선택합니다.
1. **추가** 를 선택합니다. 권한이 완전히 전파되는 데 몇 분 정도 걸릴 수 있습니다.

## <a name="create-client-secret"></a>클라이언트 비밀 생성

애플리케이션은 토큰을 요청할 때 ID를 증명하기 위해 클라이언트 비밀을 필요로 합니다. 클라이언트 비밀을 추가하려면 다음 단계를 수행합니다.

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]


## <a name="next-steps"></a>다음 단계

이제 관리 애플리케이션을 등록하고 필요한 권한을 부여했으므로 애플리케이션 및 서비스(예: Azure Pipelines)에서 자격 증명 및 사용 권한을 사용하여 Microsoft Graph API와 상호 작용할 수 있습니다. 

* [Azure AD에서 액세스 토큰 가져오기](/graph/auth-v2-service#4-get-an-access-token)
* [액세스 토큰을 사용하여 Microsoft Graph 호출](/graph/auth-v2-service#4-get-an-access-token)
* [Microsoft Graph에서 지원하는 B2C 작업](microsoft-graph-operations.md)
* [Microsoft Graph로 Azure AD B2C 사용자 계정 관리](microsoft-graph-operations.md)
* [Azure AD Reporting API를 사용하여 감사 로그 가져오기](view-audit-logs.md#get-audit-logs-with-the-azure-ad-reporting-api)

<!-- LINKS -->
[ms-graph]: /graph/
[ms-graph-api]: /graph/api/overview