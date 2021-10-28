---
title: 애플리케이션 프록시를 사용하여 온-프레미스 앱과 Cloud App Security 통합 - Azure Active Directory
description: MCAS(Microsoft Cloud App Security)를 사용하여 작동하도록 Azure Active Directory에서 온-프레미스 애플리케이션을 구성합니다. MCAS 조건부 액세스 앱 제어를 사용하여 조건부 액세스 정책에 따라 실시간으로 세션을 모니터링 및 제어합니다. Azure AD(Azure Active Directory)에서 애플리케이션 프록시를 사용하는 온-프레미스 애플리케이션에 이러한 정책을 적용할 수 있습니다.
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-proxy
ms.workload: identity
ms.topic: how-to
ms.date: 04/28/2021
ms.author: kenwith
ms.reviewer: ashishj
ms.openlocfilehash: 2a626d7c26f165d554ec7dcc72e5e5f34c5b58c4
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129989326"
---
# <a name="configure-real-time-application-access-monitoring-with-microsoft-cloud-app-security-and-azure-active-directory"></a>Microsoft Cloud App Security와 Azure Active Directory를 사용하여 실시간 애플리케이션 액세스 모니터링 구성
실시간 모니터링을 위해 MCAS(Microsoft Cloud App Security)를 사용하도록 Azure AD(Azure Active Directory)에서 온-프레미스 애플리케이션을 구성합니다. MCAS는 조건부 액세스 앱 제어를 사용하여 조건부 액세스 정책에 따라 실시간으로 세션을 모니터링 및 제어합니다. Azure AD(Azure Active Directory)에서 애플리케이션 프록시를 사용하는 온-프레미스 애플리케이션에 이러한 정책을 적용할 수 있습니다.

MCAS를 사용하여 만들 수 있는 몇 가지 정책 유형의 예제는 다음과 같습니다.

- 관리되지 않는 디바이스에서 중요한 문서의 다운로드를 차단 또는 보호합니다.
- 위험 수준이 높은 사용자가 애플리케이션에 로그온하는 경우를 모니터링한 다음, 세션 내에서 해당 사용자의 작업을 기록합니다. 이 정보를 사용하여 세션 정책을 적용하는 방법을 결정하는 사용자 동작을 분석할 수 있습니다.
- 클라이언트 인증서 또는 디바이스의 규정 준수를 사용하여 관리되지 않는 디바이스에서 특정 애플리케이션에 대한 액세스를 차단합니다.
- 비 회사 네트워크에서 사용자 세션을 제한합니다. 회사 네트워크 외부에서 애플리케이션에 액세스하는 사용자에게 제한된 액세스를 제공할 수 있습니다. 예를 들어 이 제한된 액세스는 사용자가 중요한 문서를 다운로드하는 것을 차단할 수 있습니다.

자세한 내용은 [Microsoft Cloud App Security 조건부 액세스 앱 제어를 사용하여 앱 보호](/cloud-app-security/proxy-intro-aad)를 참조하세요.

## <a name="requirements"></a>요구 사항

라이선스:

- EMS E5 라이선스 또는 
- Azure Active Directory Premium P1 및 MCAS Standalone

온-프레미스 애플리케이션:

- 온-프레미스 애플리케이션에서 KCD(Kerberos 제한된 위임)를 사용해야 합니다.

애플리케이션 프록시 구성:

- 환경 준비 및 애플리케이션 프록시 커넥터 설치를 포함하여 애플리케이션 프록시를 사용하도록 Azure AD를 구성합니다. 자습서의 경우 [Azure AD에서 애플리케이션 프록시를 통한 원격 액세스를 위해 온-프레미스 애플리케이션 추가](../app-proxy/application-proxy-add-on-premises-application.md)를 참조하세요. 

## <a name="add-on-premises-application-to-azure-ad"></a>Azure AD에 온-프레미스 애플리케이션 추가

Azure AD에 온-프레미스 애플리케이션을 추가합니다. 빠른 시작은 [Azure AD에 온-프레미스 앱 추가](../app-proxy/application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad)를 참조하세요. 애플리케이션을 추가할 때 **온-프레미스 애플리케이션 추가** 블레이드에서 다음 두 설정을 설정해야 합니다.

- **사전 인증**: **Azure Active Directory** 를 입력합니다.
- **애플리케이션 본문에서 URL 변환**: **예** 를 선택합니다.

MCAS를 사용하려면 애플리케이션에 대해 이러한 두 설정이 필요합니다.

## <a name="test-the-on-premises-application"></a>온-프레미스 애플리케이션 테스트

Azure AD에 애플리케이션을 추가한 후 [애플리케이션 테스트](../app-proxy/application-proxy-add-on-premises-application.md#test-the-application)의 단계를 사용하여 테스트를 위해 사용자를 추가하고, 로그온을 테스트합니다. 

## <a name="deploy-conditional-access-app-control"></a>조건부 액세스 앱 제어 배포

조건부 액세스 애플리케이션 제어를 사용하여 애플리케이션을 구성하려면 [Azure AD 앱용 조건부 액세스 애플리케이션 제어 배포](/cloud-app-security/proxy-deployment-aad)의 지침을 따릅니다.


## <a name="test-conditional-access-app-control"></a>조건부 액세스 앱 제어 테스트

조건부 액세스 애플리케이션 제어를 사용하여 Azure AD 애플리케이션의 배포를 테스트하려면 [Azure AD 앱에 대한 배포 테스트](/cloud-app-security/proxy-deployment-aad)의 지침을 따릅니다.





