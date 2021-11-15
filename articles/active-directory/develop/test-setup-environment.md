---
title: 앱에 대한 테스트 환경 설정
titleSuffix: Microsoft identity platform
description: Microsoft ID 플랫폼과 통합된 애플리케이션을 테스트할 수 있도록 Azure Active Directory 테스트 환경을 설정하는 방법을 알아봅니다.  테스트를 위해 별도의 테넌트가 필요한지 또는 프로덕션 테넌트를 사용할 수 있는지를 평가합니다.
services: active-directory
author: arcrowe
manager: dastrock
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 09/28/2021
ms.author: arcrowe
ms.reviewer: ''
ms.custom: aaddev
ms.openlocfilehash: e695a7028432b1044c0c1896447d474b2716097f
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129372806"
---
# <a name="set-up-your-applications-azure-ad-test-environment"></a>애플리케이션의 Azure AD 테스트 환경 설정

개발자는 소프트웨어 개발 수명 주기를 사용하고, 앱을 개발, 테스트 및 프로덕션 환경 간에 이동합니다.  이 프로세스가 Microsoft ID 플랫폼에서 보호하는 애플리케이션에 사용되는 경우 테스트에 사용할 Azure AD(Azure Active Directory) 환경을 설정해야 합니다.  이 환경은 개발 수명 주기의 초기 테스트 단계와 장기적이고 영구적인 테스트 환경에서 사용할 수 있습니다.  리소스 격리 요구 사항에 따라 조직의 Azure AD 프로덕션 테넌트 또는 테스트를 위해 완전히 별도의 테넌트를 사용할 수 있습니다.  

이 문서에서는 Microsoft ID 플랫폼과 통합된 애플리케이션을 테스트할 수 있도록 Azure AD 테스트 환경을 설정하는 방법을 알아봅니다.  필요한 격리 수준과 테스트를 위해 별도의 테넌트가 필요한지 또는 프로덕션 테넌트를 사용할 수 있는지를 평가합니다.

## <a name="decide-the-level-of-isolation-needed"></a>필요한 격리 수준 결정
일반적으로 프로덕션 테넌트를 테스트 환경으로 사용하는 것이 더 쉽고 오버헤드가 적습니다. 그러나 이는 테스트 리소스와 프로덕션 리소스 간에 적절한 수준의 격리를 달성할 수 있는 경우에만 실행 가능한 옵션입니다.  격리는 높은 권한 시나리오에서 특히 중요합니다.

다음과 같은 경우 별도의 테넌트(조직의 프로덕션 테넌트가 아님)에서 테스트 환경을 설정합니다.
- 고유한 테넌트 전체 설정이 필요한 리소스 세트가 있습니다. 예를 들어 앱에서 사용자를 대신하여 액세스하지 않고 테넌트 리소스 자체에 액세스해야 할 수 있습니다(앱 전용 권한 사용).  앱 전용 액세스에는 전체 테넌트에서 적용되는 관리자 동의가 필요하며, 이러한 권한은 범위를 테넌트 경계 내에서 안전하게 좁히기 어렵습니다.
- 테스트 리소스에 대한 테넌트 멤버의 무단 액세스에 대한 최소 위험 허용 범위가 있습니다.
- 구성 변경은 프로덕션 환경에 중요한 영향을 줄 수 있습니다.
- 테넌트에서 테스트 사용자 및 연결된 테스트 데이터를 만들 수 없습니다.
- 테스트를 위해 애플리케이션에 대한 자동화된 로그인을 수행할 계획이며, 프로덕션 테넌트에서 일부 사용자 상호 작용이 필요한 인증 방법 정책을 구성했습니다.  예를 들어 모든 사용자에게 다단계 인증이 필요한 경우 통합 테스트를 위해 자동화된 로그인을 수행할 수 없습니다.
- 전역 관리자가 특정 테스트 리소스를 관리하거나 액세스할 수 없도록 해야 합니다. 별도의 전역 관리자를 사용하여 별도의 테넌트에서 해당 리소스를 격리해야 합니다.
- 비 프로덕션 리소스 및/또는 워크로드를 프로덕션 테넌트에 추가하면 해당 테넌트에 대한 [서비스 또는 제한 한도를 초과](test-throttle-service-limits.md)합니다.

이러한 조건 중 어느 것도 적용되지 않는 경우 다음 단계에 따라 [프로덕션 테넌트에서 테스트 환경을 설정](#set-up-a-test-environment-in-your-production-tenant)합니다.  그러나 이러한 조건 중 하나라도 적용되는 경우 [별도의 테넌트에서 테스트 환경을 설정](#set-up-a-test-environment-in-a-separate-tenant)해야 합니다.

## <a name="set-up-a-test-environment-in-a-separate-tenant"></a>별도의 테넌트에서 테스트 환경 설정
프로덕션 테넌트에서 테스트 앱을 안전하게 제한할 수 없는 경우 개발 및 테스트 목적으로 별도의 테넌트를 만들 수 있습니다. 

### <a name="get-a-test-tenant"></a>테스트 테넌트 얻기
전용 테스트 테넌트가 아직 없는 경우 Microsoft 365 개발자 프로그램을 사용하여 무료로 만들거나 수동으로 직접 만들 수 있습니다. 

#### <a name="join-the-microsoft-365-developer-program-recommended"></a>Microsoft 365 개발자 프로그램에 참가(권장) 
[Microsoft 365 개발자 프로그램](https://developer.microsoft.com/microsoft-365/dev-program)은 무료이며, 테스트 사용자 계정과 샘플 데이터 팩을 테넌트에 자동으로 추가할 수 있습니다.
1. 화면에서 **지금 참가** 단추를 클릭합니다.
2. 새 Microsoft 계정으로 로그인하거나 이미 있는 기존(작업) 계정을 사용하세요.
3. 등록 페이지에서 해당 지역을 선택하고 회사 이름을 입력하고 **다음** 을 클릭하기 전에 프로그램의 사용 약관에 동의합니다.
4. **구독 설정** 을 클릭합니다. 새 테넌트를 만들 지역을 지정하고, 사용자 이름과 도메인을 만들고, 암호를 입력합니다. 그러면 새 테넌트와 테넌트의 첫 번째 관리자가 생성됩니다.
5. 새 테넌트의 관리자 계정을 보호하는 데 필요한 보안 정보를 입력합니다. 그러면 계정에 대한 다단계 인증이 설정됩니다.

#### <a name="manually-create-a-tenant"></a>수동으로 테넌트 만들기  
[테넌트는 수동으로 만들](quickstart-create-new-tenant.md) 수 있습니다. 이 테넌트는 만들 때 비어 있으며 테스트 데이터로 구성해야 합니다.

### <a name="populate-your-tenant-with-users"></a>테넌트를 사용자로 채우기  
편의를 위해 자신과 개발 팀의 다른 멤버를 테넌트의 게스트 사용자로 초대할 수 있습니다.  이렇게 하면 테스트 테넌트에서 별도의 게스트 개체가 만들어지지만 회사 계정과 테스트 계정에 대해 하나의 자격 증명 세트만 관리하면 됩니다.
1. [Azure Portal](https://portal.azure.com)에서 **Azure Active Directory** 를 클릭합니다.
2. **사용자** 로 이동하십시오.
3. **새 게스트 사용자** 를 클릭하고 회사 계정 이메일 주소를 초대합니다.
4. 애플리케이션 개발 및/또는 테스트 팀의 다른 멤버에 대해 반복합니다.

또한 테스트 테넌트에서 테스트 사용자를 만들 수 있습니다.  Microsoft 365 샘플 팩 중 하나를 사용한 경우 테넌트에서 일부 테스트 사용자가 이미 있을 수 있습니다.  그렇지 않은 경우 일부 사용자를 테넌트 관리자로 직접 만들 수 있어야 합니다.
1. [Azure Portal](https://portal.azure.com)에서 **Azure Active Directory** 를 클릭합니다.
2. **사용자** 로 이동하십시오.
3. **새 사용자** 를 클릭하고, 새 테스트 사용자를 디렉터리에 만듭니다.

### <a name="get-an-azure-ad-subscription-optional"></a>Azure AD 구독 얻기(선택 사항)
애플리케이션에서 Azure AD Premium 기능을 완전히 테스트하려면 [Premium P1 또는 Premium P2 라이선스](https://azure.microsoft.com/pricing/details/active-directory/)를 테넌트에 가입해야 합니다.

Microsoft 365 개발자 프로그램을 사용하여 가입한 경우 Azure AD P2 라이선스가 테스트 테넌트에 제공됩니다.  그렇지 않은 경우 [Azure AD Premium 1개월 평가판](https://azure.microsoft.com/trial/get-started-active-directory/)을 계속 사용할 수 있습니다.

### <a name="create-and-configure-an-app-registration"></a>앱 등록 만들기 및 구성
테스트 환경에서 사용할 앱 등록을 만들어야 합니다.  테스트 환경과 프로덕션 환경 간의 보안 격리를 유지하려면 최종 프로덕션 앱 등록과는 별도의 등록이어야 합니다.  애플리케이션을 구성하는 방법은 빌드 중인 앱의 유형에 따라 달라집니다.  자세한 내용은 이 [웹 애플리케이션 등록](scenario-web-app-sign-user-app-registration.md) 문서와 같이 왼쪽 탐색 창에서 앱 시나리오에 대한 앱 등록 단계를 확인하세요.
   
### <a name="populate-your-tenant-with-policies"></a>테넌트를 정책으로 채우기
앱이 주로 단일 조직(일반적으로 단일 테넌트라고 함)에서 사용되고 해당 프로덕션 테넌트에 액세스할 수 있는 경우 앱 동작에 영향을 줄 수 있는 프로덕션 테넌트의 설정을 복제해야 합니다.  그러면 프로덕션 환경에서 작동할 때 예기치 않은 오류가 발생할 가능성이 낮아집니다.

#### <a name="conditional-access-policies"></a>조건부 액세스 정책 
조건부 액세스 정책을 복제하면 프로덕션으로 이동할 때 예기치 않게 차단되는 액세스가 발생하지 않고, 애플리케이션에서 받을 수 있는 오류를 적절하게 처리할 수 있습니다.

회사 관리자가 프로덕션 테넌트 조건부 액세스 정책을 확인해야 할 수 있습니다.  
1. 프로덕션 테넌트 계정을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Azure Active Directory** > **엔터프라이즈 애플리케이션** > **조건부 액세스** 로 차례로 이동합니다.
1. 테넌트의 정책 목록을 봅니다.  첫 번째 항목을 클릭합니다.
1. **클라우드 앱 또는 작업** 으로 이동합니다.  
1. 정책이 선택한 앱 그룹에만 적용되는 경우 다음 정책으로 이동합니다.  그렇지 않은 경우 프로덕션으로 이동할 때 앱에도 적용될 수 있습니다.  정책을 테스트 테넌트에 복사해야 합니다.

새 탭 또는 브라우저 세션에서 [Azure Portal](https://portal.azure.com)로 이동하여 테스트 테넌트에 로그인합니다.
1. **Azure Active Directory** > **엔터프라이즈 애플리케이션** > **조건부 액세스** 로 차례로 이동합니다.
1. **새 정책** 을 클릭합니다.
1. 이전 단계를 통해 식별된 프로덕션 테넌트 정책에서 설정을 복사합니다.

#### <a name="permission-grant-policies"></a>권한 부여 정책
권한 부여 정책을 복제하면 프로덕션으로 이동할 때 관리자 동의를 요청하는 예기치 않은 메시지가 표시되지 않습니다. 

1. 프로덕션 테넌트 계정을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Azure Active Directory** 를 클릭합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동합니다. 
1. 프로덕션 테넌트에서 **Azure Active Directory** > **엔터프라이즈 애플리케이션** > **동의 및 권한** > **사용자 동의** 로 차례로 이동합니다.  여기에 있는 설정을 테스트 테넌트에 복사합니다.  
    
#### <a name="token-lifetime-policies"></a>토큰 수명 정책
토큰 수명 정책을 복제하면 애플리케이션에 발급된 토큰이 프로덕션에서 예기치 않게 만료되지 않습니다.  
 
토큰 수명 정책은 현재 PowerShell을 통해서만 관리할 수 있습니다. 전체 프로덕션 조직에 적용되는 토큰 수명 정책을 식별하는 방법에 대해 알아보려면 [구성 가능한 토큰 수명](active-directory-configurable-token-lifetimes.md)을 참조하세요.  해당 정책을 테스트 테넌트에 복사합니다.
 
## <a name="set-up-a-test-environment-in-your-production-tenant"></a>프로덕션 테넌트에서 테스트 환경 설정
프로덕션 테넌트에서 테스트 앱을 안전하게 제한할 수 있는 경우 계속 진행하여 테스트 목적으로 테넌트를 설정합니다.

### <a name="create-and-configure-an-app-registration"></a>앱 등록 만들기 및 구성
테스트 환경에서 사용할 앱 등록을 만들어야 합니다.  테스트 환경과 프로덕션 환경 간의 보안 격리를 유지하려면 최종 프로덕션 앱 등록과는 별도의 등록이어야 합니다.  애플리케이션을 구성하는 방법은 빌드 중인 앱의 유형에 따라 달라집니다.  자세한 내용은 왼쪽 탐색 창에서 [앱 시나리오에 대한 앱 등록 단계](scenario-web-app-sign-user-app-registration.md)를 확인하세요.

### <a name="create-some-test-users"></a>일부 테스트 사용자 만들기
시나리오를 테스트하는 동안 사용할 관련 테스트 데이터를 사용하여 일부 테스트 사용자를 만들어야 합니다.  이 단계는 관리자가 수행해야 할 수 있습니다.
1. [Azure Portal](https://portal.azure.com)에서 **Azure Active Directory** 를 클릭합니다.
2. **사용자** 로 이동하십시오.
3. **새 사용자** 를 클릭하고, 새 테스트 사용자를 디렉터리에 만듭니다.

### <a name="add-the-test-users-to-a-group-optional"></a>그룹에 테스트 사용자 추가(선택 사항)
편의를 위해 이러한 모든 사용자를 그룹에 할당하여 다른 할당 작업을 더 쉽게 수행할 수 있습니다.  
1. [Azure Portal](https://portal.azure.com)에서 **Azure Active Directory** 를 클릭합니다.
2. **그룹** 으로 이동합니다.
3. **새 그룹** 을 클릭합니다.
4. 그룹 유형에 대해 **보안** 또는 **Microsoft 365** 를 선택합니다.
5. 그룹 이름을 지정합니다.
6. 이전 단계에서 만든 테스트 사용자를 추가합니다.

### <a name="restrict-your-test-application-to-specific-users"></a>테스트 애플리케이션을 특정 사용자로 제한
사용자 할당을 통해 테스트 애플리케이션을 사용할 수 있는 테넌트의 사용자를 특정 사용자 또는 그룹으로 제한할 수 있습니다.  [앱 등록을 통해 앱을 만들](#create-and-configure-an-app-registration) 때 **엔터프라이즈 애플리케이션** 에서도 앱의 표현이 만들어졌습니다.  **엔터프라이즈 애플리케이션** 설정을 사용하여 테넌트에서 애플리케이션을 사용할 수 있는 사용자를 제한합니다.

> [!IMPORTANT]
> 앱이 [다중 테넌트 앱](v2-supported-account-types.md)인 경우 이 작업은 다른 테넌트의 사용자가 앱에 로그인하고 사용하는 것을 제한하지 않습니다.  사용자 할당이 구성된 테넌트의 사용자만 제한합니다. 

앱을 테넌트의 특정 사용자로 제한하는 방법에 대한 자세한 지침은 [앱을 사용자 세트로 제한](howto-restrict-your-app-to-a-set-of-users.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계
 
테스트 환경을 설정하는 동안 발생할 수 있는 [제한 및 서비스 제한](test-throttle-service-limits.md)에 대해 알아봅니다.

테스트 환경에 대한 자세한 내용은 [Azure Active Directory를 사용하여 Azure 환경 보호](https://azure.microsoft.com/resources/securing-azure-environments-with-azure-active-directory/)를 참조하세요.
  
