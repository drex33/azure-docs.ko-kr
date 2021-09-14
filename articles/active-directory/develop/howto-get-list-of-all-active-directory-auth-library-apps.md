---
title: '방법: 테넌트에서 ADAL(Active Directory 인증 라이브러리)을 사용하여 모든 앱의 전체 목록 가져오기 | Azure'
titleSuffix: Microsoft identity platform
description: 이 방법 가이드에서는 테넌트에서 ADAL을 사용하는 모든 앱의 전체 목록을 확인합니다.
services: active-directory
author: SHERMANOUKO
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 07/22/2021
ms.author: shermanouko
ms.custom: aaddev, has-adal-ref
ms.reviewer: aiwang, marsma
ms.openlocfilehash: 07f6c7f481e815e788b22782f01ad9369bd2c9f6
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123039699"
---
# <a name="get-a-complete-list-of-apps-using-adal-in-your-tenant"></a>테넌트에서 ADAL을 사용하여 앱의 전체 목록 가져오기

ADAL(Active Directory 인증 라이브러리)에 대한 지원은 2022년 6월 30일에 종료됩니다. 기존 OS 버전에서 ADAL을 사용하는 앱은 계속 작동하지만 기술 지원 및 보안 업데이트는 종료됩니다. 지속적인 보안 업데이트가 없으면 ADAL을 사용하는 앱이 최신 보안 공격 패턴에 점점 더 취약해집니다. 이 문서에서는 Azure Monitor 통합 문서를 사용하여 테넌트에서 ADAL을 사용하는 모든 앱 목록을 가져오는 방법에 대한 지침을 제공합니다.

## <a name="sign-ins-workbook"></a>로그인 통합 문서

통합 문서는 Azure AD(Azure Active Directory) 로그에서 사용할 수 있는 정보를 수집하고 시각화하는 쿼리 세트입니다. [여기에서 로그인 로그 스키마에 대해 자세히 알아보세요](../reports-monitoring/reference-azure-monitor-sign-ins-log-schema.md). 이제 Azure AD 관리 포털의 로그인 통합 문서에는 ADAL을 사용하는 애플리케이션과 사용 빈도를 결정하는 데 도움이 되는 표가 있습니다. 먼저 애플리케이션 목록에 대한 시각화를 표시하기 전에 통합 문서에 액세스하는 방법을 자세히 설명합니다.

## <a name="step-1-integrate-audit-logs-with-azure-monitor"></a>1단계: Azure Monitor와 감사 로그 통합

통합 문서에 액세스하기 전에 [Azure Monitor와 Azure AD 로그인 및 감사 로그 통합](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)의 단계를 따르세요. Azure Monitor 통합 후에 생성된 로그인 및 감사 이벤트만 저장됩니다.

## <a name="step-2-access-sign-ins-workbook-in-azure-portal"></a>2단계: Azure Portal의 로그인 통합 문서에 액세스

Azure Monitor 통합에 지정된 대로 Azure AD 로그인 및 감사 로그를 Azure Monitor와 통합했으면 로그인 통합 문서에 액세스합니다.

   1. Azure Portal에 로그인합니다. 
   1.  **Azure Active Directory** > **모니터링** > **통합 문서** 로 이동합니다. 
   1. **사용** 섹션에서  **로그인** 통합 문서를 엽니다. 

   :::image type="content" source="media/howto-get-list-of-all-active-directory-auth-library-apps/sign-in-workbook.png" alt-text="로그인 통합 문서를 강조 표시하는 Azure Active Directory 포털 통합 문서 인터페이스의 스크린샷":::

## <a name="step-3-identify-apps-that-use-adal"></a>3단계: ADAL을 사용하는 앱 식별

로그인 통합 문서 페이지 아래쪽에 있는 표에는 최근에 ADAL을 사용한 앱이 나열됩니다. 앱 목록을 내보낼 수도 있습니다. MSAL을 사용하도록 해당 앱을 업데이트합니다.
    
:::image type="content" source="media/howto-get-list-of-all-active-directory-auth-library-apps/active-directory-auth-library-apps-present.png" alt-text="Active Directory 인증 라이브러리를 사용하는 앱을 표시하는 로그인 통합 문서의 스크린샷":::
    
ADAL을 사용하는 앱이 없으면 통합 문서에 아래와 같이 보기가 표시됩니다. 
    
:::image type="content" source="media/howto-get-list-of-all-active-directory-auth-library-apps/no-active-directory-auth-library-apps.png" alt-text="Active Directory 인증 라이브러리를 사용하는 앱이 없을 때의 로그인 통합 문서 스크린샷":::

## <a name="step-4-update-your-code"></a>4단계: 코드 업데이트

ADAL을 사용하는 앱을 식별한 후 아래 그림과 같이 애플리케이션 유형에 따라 MSAL로 마이그레이션합니다.

[!INCLUDE [application type](includes/adal-msal-migration.md)]

## <a name="next-steps"></a>다음 단계

사용 정보, 다양한 프로그래밍 언어와 애플리케이션 유형에 사용할 수 있는 라이브러리 등을 포함하여 MSAL에 관한 자세한 내용은 다음을 참조하세요.

- [MSAL을 사용하여 토큰 획득 및 캐시](msal-acquire-cache-tokens.md)
- [애플리케이션 구성 옵션](msal-client-application-configuration.md)
- [MSAL 인증 라이브러리 목록](reference-v2-libraries.md)
