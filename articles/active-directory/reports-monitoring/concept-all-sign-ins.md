---
title: Azure Active Directory 로그인 로그 - 미리 보기 | Microsoft Docs
description: 미리 보기 상태인 새 기능을 포함한 Azure Active Directory 로그인 로그의 개요입니다.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 06/23/2021
ms.author: markvi
ms.reviewer: besiler
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9fbd65204534e978446109c99ca7286c0af00d68
ms.sourcegitcommit: 54d8b979b7de84aa979327bdf251daf9a3b72964
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2021
ms.locfileid: "112580389"
---
# <a name="sign-in-logs-in-azure-active-directory---preview"></a>Azure Active Directory 로그인 로그 - 미리 보기

IT 관리자가 IT 환경의 운영 상태를 파악하려고 합니다. 시스템 상태 정보를 사용하여 잠재적 이슈에 대응해야 하는지 여부와 방법을 평가할 수 있습니다. 

이 목표를 지원하기 위해 Azure Active Directory 포털은 다음 세 가지 활동 로그에 대한 액세스를 제공합니다.

- **[로그인](concept-sign-ins.md)** – 로그인 및 사용자가 리소스를 사용하는 방법에 대한 정보입니다.
- **[감사](concept-audit-logs.md)** – 사용자 및 그룹 관리 또는 테넌트 리소스에 적용된 업데이트와 같이 테넌트에 적용된 변경 사항에 대한 정보입니다.
- **[프로비저닝](concept-provisioning-logs.md)** – ServiceNow의 그룹 생성 또는 Workday에서 가져온 사용자와 같이 프로비저닝 서비스에서 수행하는 활동입니다.


Azure Active Directory의 클래식 로그인 로그는 대화형 사용자 로그인에 대한 개요를 제공합니다. 또한 이제 미리 보기로 제공되는 세 가지 추가 로그인 로그에 액세스할 수 있습니다.

- 비 대화형 사용자 로그인

- 서비스 주체 로그인

- Azure 리소스 로그인에 대한 관리 ID

이 문서에서는 Azure 리소스 로그인에 대한 비대화형, 애플리케이션 및 관리 ID의 미리 보기가 포함된 로그인 로그의 개요를 제공합니다. 미리 보기 기능이 없는 로그인 보고서에 대한 자세한 내용은 [Azure Active Directory 포털의 로그인 활동 보고서](concept-sign-ins.md)를 참조하세요.



## <a name="what-can-you-do-with-it"></a>로그인 로그로 무엇을 할 수 있나요?

로그인 로그는 다음과 같은 질문에 대한 답변을 제공합니다.

- 사용자, 애플리케이션 또는 서비스의 로그인 패턴은 무엇인가요?

- 일주일 동안 얼마나 많은 사용자, 앱 또는 서비스가 로그인했나요?

- 이러한 로그인의 상태란?


## <a name="who-can-access-the-data"></a>데이터에 액세스할 수 있는 사용자는 누구인가요?

- 보안 관리, 보안 읽기 권한자 및 보고서 읽기 권한자 역할의 사용자

- 글로벌 관리자

- 모든 사용자(비관리자)가 자신의 로그인에 액세스할 수 있습니다. 

## <a name="what-azure-ad-license-do-you-need"></a>필요한 Azure AD 라이선스는 무엇인가요?

로그인 활동을 보려면 테넌트에 이와 관련된 Azure AD Premium 라이선스가 있어야 합니다. [Azure Active Directory Premium 시작하기](../fundamentals/active-directory-get-started-premium.md)를 참조하여 Azure Active Directory 버전을 업그레이드하세요. 업그레이드 전에 데이터 활동이 없는 프리미엄 라이선스로 업그레이드한 후 로그에 데이터가 표시되려면 며칠이 걸립니다.




## <a name="where-can-you-find-it-in-the-azure-portal"></a>Azure Portal에서 로그를 찾을 수 있는 위치는 어디인가요?

Azure Portal은 로그에 액세스하는 몇 가지 옵션을 제공합니다. 예를 들어 Azure Active Directory 메뉴의 **모니터링** 섹션에서 로그를 열 수 있습니다.  

![로그인 로그 열기](./media/concept-sign-ins/sign-ins-logs-menu.png)

[https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns) 링크를 사용하여 로그인 로그에 직접 액세스할 수도 있습니다.

로그인 페이지에서 다음 중 하나를 전환할 수 있습니다.

- **대화형 사용자 로그인** - 사용자가 암호, MFA 앱을 통한 응답, 생체 인식 요소 또는 QR 코드와 같은 인증 요소를 제공하는 로그인입니다.

- **비 대화형 사용자 로그인** - 클라이언트가 사용자를 대신하여 수행하는 로그인입니다. 이러한 로그인에는 사용자의 상호 작용이나 인증 요소가 필요하지 않습니다. 예를 들어, 사용자가 자격 증명을 입력할 필요가 없는 새로 고침 및 액세스 토큰을 사용한 인증 및 권한 부여가 있습니다.

- **서비스 주체 로그인** - 사용자와 관련이 없는 앱 및 서비스 주체에 의한 로그인입니다. 이러한 로그인에서 앱 또는 서비스는 리소스를 인증하거나 액세스할 수 있는 자격 증명을 자체적으로 제공합니다.

- **Azure 리소스에 대한 관리 ID 로그인** - Azure에서 관리하는 비밀이 있는 Azure 리소스에 의한 로그인입니다. 자세한 내용은 [Azure 리소스에 대한 관리 ID란?](../managed-identities-azure-resources/overview.md)을 참조하세요. 


![로그인 로그 유형](./media/concept-all-sign-ins/sign-ins-report-types.png)



로그인 페이지의 각 탭은 아래와 같은 기본 열을 보여줍니다. 일부 탭에는 다음과 같은 추가 열이 있습니다.

- 로그인 날짜

- 요청 ID

- 사용자 이름 또는 사용자 ID

- 애플리케이션 이름 또는 애플리케이션 ID

- 로그인의 상태

- 로그인에 사용되는 디바이스의 IP 주소



### <a name="interactive-user-sign-ins&quot;></a>대화형 사용자 로그인


대화형 사용자 로그인은 사용자가 Azure AD에 인증 요소를 제공하거나 Azure AD 또는 Microsoft Authenticator 앱과 같은 도우미 앱과 직접 상호 작용하는 로그인입니다. 사용자가 제공하는 요인에는 사용자가 Azure AD 또는 도우미 앱에 제공하는 암호, MFA 문제에 대한 응답, 생체 인식 요인 또는 QR 코드가 포함됩니다.

> [!NOTE]
> 이 로그에는 Azure AD에 페더레이션된 ID 공급자의 페더레이션된 로그인도 포함됩니다.  



> [!NOTE] 
> 대화형 사용자 로그인 로그는 Microsoft Exchange 클라이언트의 일부 비 대화형 로그인을 포함하는 데 사용됩니다. 이러한 로그인은 대화형이 아니었지만 추가 가시성을 위해 대화형 사용자 로그인 로그에 포함되었습니다. 2020년 11월 비 대화형 사용자 로그인 로그가 공개 미리 보기에 들어가면 이러한 비 대화형 로그인 로그는 정확성을 높이기 위해 비 대화형 사용자 로그인 로그로 이동되었습니다. 


**보고서 크기:** 작음 <br> 
**예:**

- 사용자는 Azure AD 로그인 화면에서 사용자 이름과 암호를 제공합니다.

- 사용자가 SMS MFA 문제를 전달합니다.

- 사용자는 생체 인식 제스처를 제공하여 비즈니스용 Windows Hello가 설치된 Windows PC의 잠금을 해제합니다.

- 사용자는 AD FS SAML 어설션을 사용하여 Azure AD에 페더레이션됩니다.


기본 필드 외에도 대화형 로그인 로그에는 다음이 표시됩니다. 

- 로그인 위치

- 조건부 액세스 적용 여부



도구 모음에서 **열** 을 클릭하여 목록 보기를 사용자 지정할 수 있습니다.

![대화형 사용자 로그인 열](./media/concept-all-sign-ins/columns-interactive.png &quot;대화형 사용자 로그인 열")





보기를 사용자 지정하면 추가 필드를 표시하거나 이미 표시된 필드를 제거할 수 있습니다.

![모든 대화형 열](./media/concept-all-sign-ins/all-interactive-columns.png)


관련 로그인에 대한 자세한 정보를 보려면 목록 보기에서 항목을 선택합니다.

![로그인 작업](./media/concept-all-sign-ins/interactive-user-sign-in-details.png "대화형 사용자 로그인")



### <a name="non-interactive-user-sign-ins"></a>비 대화형 사용자 로그인

비 대화형 사용자 로그인은 사용자를 대신하여 클라이언트 앱 또는 OS 구성 요소에서 수행된 로그인입니다. 대화형 사용자 로그인과 마찬가지로 이러한 로그인은 사용자를 대신하여 수행됩니다. 대화형 사용자 로그인과 달리 이러한 로그인에는 사용자가 인증 요소를 제공할 필요가 없습니다. 대신 디바이스 또는 클라이언트 앱은 토큰 또는 코드를 사용하여 사용자를 대신하여 리소스를 인증하거나 액세스합니다. 일반적으로 사용자는 이러한 로그인이 사용자 활동의 백그라운드에서 발생하는 것으로 인식합니다.


**보고서 크기:** 큼 <br>
**예:** 

- 클라이언트 앱은 OAuth 2.0 새로 고침 토큰을 사용하여 액세스 토큰을 가져옵니다.

- 클라이언트는 OAuth 2.0 인증 코드를 사용하여 액세스 토큰을 얻고 토큰을 새로 고칩니다.

- 사용자는 Azure AD 조인 PC에서 웹 또는 Windows 앱에 대한 SSO(Single Sign-On)를 수행합니다.

- 사용자가 FOCI(클라이언트 ID 제품군)를 사용하여 모바일 디바이스에서 세션을 수행하는 동안 두 번째 Microsoft Office 앱에 로그인합니다.




기본 필드 외에도 비 대화형 로그인 로그에는 다음이 표시됩니다. 

- 리소스 ID

- 그룹화된 로그인 수




이 보고서에 표시된 필드는 사용자 지정할 수 없습니다.


![사용하지 않는 열](./media/concept-all-sign-ins/disabled-columns.png "사용하지 않는 열")

데이터를 보다 쉽게 요약하기 위해 비 대화형 로그인 이벤트가 그룹화됩니다. 클라이언트는 종종 짧은 기간에 동일한 사용자를 대신하여 많은 비 대화형 로그인을 생성하며, 이는 로그인 시도 시간을 제외하고는 모든 동일한 특성을 공유합니다. 예를 들어, 클라이언트는 사용자를 대신하여 시간당 한 번씩 액세스 토큰을 받을 수 있습니다. 사용자 또는 클라이언트가 상태를 변경하지 않으면 각 액세스 토큰 요청에 대해 IP 주소, 리소스 및 기타 모든 정보가 동일합니다. Azure AD가 시간과 날짜를 제외하고 동일한 여러 로그인을 기록할 경우 동일한 엔티티의 로그인이 단일 행으로 집계됩니다. 동일한 로그인이 여러 개 있는 행(발행 날짜 및 시간은 제외)은 #로그인 열에서 1보다 큰 값을 갖습니다. 행을 확장하여 모든 다른 로그인과 다른 타임스탬프를 볼 수 있습니다. 로그인은 다음 데이터가 일치하는 경우 비 대화형 사용자로 집계됩니다.


- 애플리케이션

- 사용자

- IP 주소

- 상태

- 리소스 ID


다음과 같습니다.

- 노드를 확장하여 그룹의 개별 항목을 볼 수 있습니다.  

- 개별 항목을 클릭하여 모든 세부 사항 보기 


![비 대화형 사용자 로그인 세부 정보](./media/concept-all-sign-ins/non-interactive-sign-ins-details.png)




## <a name="service-principal-sign-ins"></a>서비스 주체 로그인

대화형 및 비 대화형 사용자 로그인과 달리 서비스 주체 로그인은 사용자를 포함하지 않습니다. 대신 앱 또는 서비스 주체와 같은 비 사용자 계정에 의한 로그인입니다(관리 ID 로그인 로그에만 포함되는 관리 ID 로그인 제외). 이러한 로그인에서 앱 또는 서비스는 리소스를 인증하거나 액세스하기 위한 인증서 또는 앱 비밀과 같은 자체 자격 증명을 제공합니다.


**보고서 크기:** 큼 <br>
**예:**

- 서비스 주체는 인증서를 사용하여 Microsoft Graph를 인증하고 액세스합니다. 

- 애플리케이션은 클라이언트 암호를 사용하여 OAuth 클라이언트 자격 증명 흐름에서 인증합니다. 


이 보고서에는 다음 항목을 보여주는 기본 목록 보기가 있습니다.

- 로그인 날짜

- 요청 ID

- 서비스 주체 이름 또는 ID

- 상태

- IP 주소

- 리소스 이름

- 리소스 ID

- 로그인 수

이 보고서에 표시된 필드는 사용자 지정할 수 없습니다.

![사용하지 않는 열](./media/concept-all-sign-ins/disabled-columns.png "사용하지 않는 열")

서비스 주체 로그인 로그의 데이터를 보다 쉽게 요약하기 위해 서비스 주체 로그인 이벤트가 그룹화됩니다. 동일한 조건에서 동일한 엔터티의 로그인은 단일 행으로 집계됩니다. 행을 확장하여 모든 다른 로그인과 다른 타임스탬프를 볼 수 있습니다. 로그인은 다음 데이터가 일치할 때 서비스 주체 보고서에 집계됩니다.

- 서비스 주체 이름 또는 ID

- 상태

- IP 주소

- 리소스 이름 또는 ID

다음과 같습니다.

- 노드를 확장하여 그룹의 개별 항목을 볼 수 있습니다.  

- 개별 항목을 클릭하여 모든 세부 사항 보기 


![열 세부 정보](./media/concept-all-sign-ins/service-principals-sign-ins-view.png "열 세부 정보")




## <a name="managed-identity-for-azure-resources-sign-ins"></a>Azure 리소스에 대한 관리 ID 로그인 

Azure 리소스에 대한 관리 ID 로그인은 자격 증명 관리를 단순화하기 위해 Azure에서 관리하는 비밀이 있는 리소스에 의해 수행된 로그인입니다.

**보고서 크기:** 작음 <br> 
**예:**

관리 자격 증명이 있는 VM은 Azure AD를 사용하여 액세스 토큰을 가져옵니다.   


이 보고서에는 다음 항목을 보여주는 기본 목록 보기가 있습니다.


- 관리 ID

- 관리 ID 이름

- 리소스

- 리소스 ID

- 그룹화된 로그인 수

이 보고서에 표시된 필드는 사용자 지정할 수 없습니다.

데이터를 쉽게 요약할 수 있도록 Azure 리소스에 대한 관리 ID 로그인 로그, 비 대화형 로그인 이벤트가 그룹화됩니다. 동일한 엔터티의 로그인은 단일 행으로 집계됩니다. 행을 확장하여 모든 다른 로그인과 다른 타임스탬프를 볼 수 있습니다. 다음 데이터가 모두 일치하면 로그인은 관리 ID 보고서에 집계됩니다.

- 관리 ID 이름 또는 ID

- 상태

- 리소스 이름 또는 ID

목록 보기에서 항목을 선택하여 노드 아래에 그룹화된 모든 로그인을 표시합니다.

로그인에 대한 모든 세부 정보를 보려면 그룹화된 항목을 선택합니다. 


## <a name="sign-in-error-code"></a>로그인 오류 코드

로그인에 실패한 경우 관련 로그 항목의 **기본 정보** 섹션에서 이유에 대한 자세한 정보를 얻을 수 있습니다. 

![자세한 정보 보기를 보여주는 스크린샷.](./media/concept-all-sign-ins/error-code.png)
 
로그 항목에서 실패 이유를 제공하지만 [로그인 오류 조회 도구](https://login.microsoftonline.com/error)를 사용하여 자세한 정보를 얻을 수 있는 경우가 있습니다. 예를 들어 이 도구는 사용 가능한 경우 수정 단계를 제공합니다.  

![오류 코드 조회 도구](./media/concept-all-sign-ins/error-code-lookup-tool.png)



## <a name="filter-sign-in-activities"></a>로그인 활동 필터링

필터를 설정하여 반환된 로그인 데이터의 범위를 좁힐 수 있습니다. Azure AD는 설정할 수 있는 광범위한 추가 필터를 제공합니다. 필터를 설정할 때 항상 구성된 **날짜** 범위 필터에 특별한 주의를 기울여야 합니다. 적절한 날짜 범위 필터를 사용하면 Azure AD는 사용자가 정말로 관심 있는 데이터만 반환합니다.     

**날짜** 범위 필터를 사용하면 반환되는 데이터의 시간 범위를 정의할 수 있습니다.
가능한 값은 다음과 같습니다.

- 1달

- 7일

- 24시간

- 사용자 지정

![시간 범위 필터](./media/concept-all-sign-ins/date-range-filter.png)





### <a name="filter-user-sign-ins"></a>사용자 로그인 필터링

대화형 및 비 대화형 로그인에 대한 필터는 동일합니다. 따라서 대화형 로그인에 대해 구성한 필터는 비 대화형 로그인에 대해 유지되고, 그 반대의 경우도 마찬가지입니다. 






## <a name="access-the-new-sign-in-activity-logs"></a>새 로그인 활동 로그 액세스 

Azure Portal의 로그인 활동 보고서는 미리 보기 보고서를 켜고 끄는 간단한 방법을 제공합니다. 미리 보기 보고서를 사용하는 경우 모든 로그인 활동 로그 유형에 액세스할 수 있는 새 메뉴가 표시됩니다.     


비 대화형 및 애플리케이션 로그인을 사용하여 새 로그인 로그에 액세스하려면 다음을 수행합니다. 

1. [Azure Portal](https://portal.azure.com)에서 **Azure Active Directory** 를 선택합니다.

    ![Azure AD 선택](./media/concept-all-sign-ins/azure-services.png)

2. **모니터링** 섹션에서 **로그인** 을 클릭합니다.

    ![로그인 선택](./media/concept-all-sign-ins/sign-ins.png)

3. **미리 보기** 표시줄을 클릭합니다.

    ![새 보기 사용](./media/concept-all-sign-ins/enable-new-preview.png)

4. 기본 보기로 다시 전환하려면 **미리 보기** 표시줄을 다시 클릭합니다. 

    ![클래식 보기 복원](./media/concept-all-sign-ins/switch-back.png)







## <a name="download-sign-in-activity-logs"></a>로그인 활동 로그 다운로드

로그인 활동 보고서를 다운로드하는 경우 다음 사항이 적용됩니다.

- 로그인 보고서를 CSV 또는 JSON 파일로 다운로드할 수 있습니다.

- 최대 100-K개의 레코드를 다운로드할 수 있습니다. 더 많은 데이터를 다운로드하려면 보고 API를 사용하세요.

- 다운로드는 필터 선택에 따라 결정됩니다.

- 다운로드할 수 있는 레코드의 수는 [Azure Active Directory 보고서 보존 정책](reference-reports-data-retention.md)에 의해 제한됩니다. 


![로그 다운로드](./media/concept-all-sign-ins/download-reports.png "로그 다운로드")


각 CSV 다운로드는 여섯 개의 서로 다른 파일로 구성됩니다.

- 대화형 로그인

- 대화형 로그인의 인증 세부 정보

- 비 대화형 로그인

- 비 대화형 로그인의 인증 세부 정보

- 서비스 주체 로그인

- Azure 리소스에 대한 관리 ID 로그인

각 JSON 다운로드는 네 개의 서로 다른 파일로 구성 됩니다.

- 대화형 로그인(인증 세부 정보 포함)

- 비 대화형 로그인(인증 세부 정보 포함)

- 서비스 주체 로그인

- Azure 리소스에 대한 관리 ID 로그인

![파일 다운로드](./media/concept-all-sign-ins/download-files.png "파일 다운로드")


## <a name="return-log-data-with-microsoft-graph"></a>Microsoft Graph를 사용하여 로그 데이터 반환

Azure Portal을 사용하는 것 외에도 Microsoft Graph API를 사용하여 로그인 로그를 쿼리하여 여러 유형의 로그인 정보를 반환할 수 있습니다. 잠재적 성능 문제를 방지하려면 쿼리를 관심 있는 데이터에만 범위를 지정합니다. 

다음 예제에서는 레코드 수, 특정 기간 및 로그인 이벤트 유형별로 쿼리 범위를 지정합니다.

```msgraph-interactive
GET https://graph.microsoft.com/beta/auditLogs/signIns?$top=100&$filter=createdDateTime ge 2020-09-10T06:00:00Z and createdDateTime le 2020-09-17T06:00:00Z and signInEventTypes/any(t: t eq 'nonInteractiveUser')
```

예제의 쿼리 매개 변수는 다음과 같은 결과를 제공합니다.

- [$top](/graph/query-parameters#top-parameter) 매개 변수는 상위 100개 결과를 반환합니다.
- [$filter](/graph/query-parameters#filter-parameter) 매개 변수는 결과가 반환되는 시간 프레임을 제한하고 signInEventTypes 속성을 사용하여 비 대화형 사용자 로그인만 반환합니다.

다음 값은 여러 로그인 유형별로 필터링하는 데 사용할 수 있습니다. 

- interactiveUser
- nonInteractiveUser
- servicePrincipal 
- managedIdentity

## <a name="next-steps"></a>다음 단계

* [로그인 활동 보고서 오류 코드](./concept-sign-ins.md)
* [Azure AD 데이터 보존 정책](reference-reports-data-retention.md)
* [Azure AD 보고서 대기 시간](reference-reports-latencies.md)
