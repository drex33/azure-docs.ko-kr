---
title: Azure Active Directory의 로그인 로그 | Microsoft Docs
description: Azure Active Directory의 로그인 로그 개요
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: karenhoran
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 10/25/2021
ms.author: markvi
ms.reviewer: besiler
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad322032237d8e65fd82fea74bf359fcd65c3840
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131997232"
---
# <a name="sign-in-logs-in-azure-active-directory"></a>Azure Active Directory 로그인 로그

IT 관리자가 IT 환경의 운영 상태를 파악하려고 합니다. 시스템 상태 정보를 사용하여 잠재적 이슈에 대응해야 하는지 여부와 방법을 평가할 수 있습니다. 

이 목표를 지원하기 위해 Azure Active Directory 포털은 다음 세 가지 활동 로그에 대한 액세스를 제공합니다.

- **[로그인](concept-sign-ins.md)** – 로그인 및 사용자가 리소스를 사용하는 방법에 대한 정보입니다.
- **[감사](concept-audit-logs.md)** – 사용자 및 그룹 관리 또는 테넌트 리소스에 적용된 업데이트와 같이 테넌트에 적용된 변경 사항에 대한 정보입니다.
- **[프로비저닝](concept-provisioning-logs.md)** – ServiceNow의 그룹 생성 또는 Workday에서 가져온 사용자와 같이 프로비저닝 서비스에서 수행하는 활동입니다.

이 항목에서는 로그인 보고서에 대한 개요를 제공합니다.


## <a name="what-can-you-do-with-it"></a>로그인 보고서로 무엇을 할 수 있나요?

로그인 로그를 사용하여 다음과 같은 질문의 답변을 찾을 수 있습니다.

- 사용자의 로그인 패턴이란?

- 한 주 동안 얼마나 많은 사용자가 로그인했나요?

- 이러한 로그인의 상태란?


## <a name="who-can-access-it"></a>액세스할 수 있는 사용자는 누구인가요?

언제든지 다음 링크를 사용하여 사용자 고유의 로그인 기록에 액세스할 수 있습니다. [https://mysignins.microsoft.com](https://mysignins.microsoft.com)

로그인 로그에 액세스하려면 다음에 해당해야 합니다.

- 전역 관리자

- 다음 역할 중 하나가 지정된 사용자:
    - 보안 관리자

    - 보안 판독기

    - 전역 독자

    - 보고서 읽기 권한자



## <a name="what-azure-ad-license-do-you-need"></a>필요한 Azure AD 라이선스는 무엇인가요?

로그인 활동 보고서는 [Azure AD의 모든 버전](reference-reports-data-retention.md#how-long-does-azure-ad-store-the-data)에서 제공됩니다. Azure Active Directory P1 또는 P2 라이선스가 있는 경우 Microsoft Graph API를 통해 로그인 활동 보고서에 액세스할 수도 있습니다.


## <a name="where-can-you-find-it-in-the-azure-portal"></a>Azure Portal에서 로그를 찾을 수 있는 위치는 어디인가요?

Azure Portal은 로그에 액세스하는 몇 가지 옵션을 제공합니다. 예를 들어 Azure Active Directory 메뉴의 **모니터링** 섹션에서 로그를 열 수 있습니다.  

![로그인 로그 열기](./media/concept-sign-ins/sign-ins-logs-menu.png)

다음 링크를 사용하여 로그인 로그에 직접 액세스할 수도 있습니다. [https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns)


## <a name="what-is-the-default-view"></a>기본 보기는 무엇인가요?

로그인 로그에는 다음 항목을 보여주는 기본 목록 보기가 있습니다.

- 로그인 날짜
- 관련된 사용자
- 사용자가 로그인한 애플리케이션
- 로그인 상태
- 위험 검색 상태
- MFA(Multi-Factor Authentication) 요구 사항 상태

![Office 365 SharePoint Online 로그인을 보여주는 스크린샷.](./media/concept-sign-ins/sign-in-activity.png "로그인 작업")

도구 모음에서 **열** 을 클릭하여 목록 보기를 사용자 지정할 수 있습니다.

![로그인 페이지의 열 옵션을 보여주는 스크린샷.](./media/concept-sign-ins/19.png "로그인 작업")

**열** 대화 상자를 사용하여 선택 가능한 특성에 액세스할 수 있습니다. 로그인 보고서에는 지정된 로그인 요청에 대해 둘 이상의 값이 있는 필드를 열로 사용할 수 없습니다. 해당 사항은 예를 들어 인증 정보, 조건부 액세스 데이터, 네트워크 위치 등에 적용됩니다.   

![특성을 선택할 수 있는 열 대화 상자를 보여주는 스크린샷.](./media/concept-sign-ins/columns.png "로그인 작업")

자세한 정보를 가져오려면 목록 보기에서 항목을 선택합니다.

![자세한 정보 보기를 보여주는 스크린샷.](./media/concept-sign-ins/basic-sign-in.png "로그인 작업")



## <a name="sign-in-error-code"></a>로그인 오류 코드

로그인에 실패한 경우 관련 로그 항목의 **기본 정보** 섹션에서 이유에 대한 자세한 정보를 얻을 수 있습니다. 

![로그인 오류 코드](./media/concept-all-sign-ins/error-code.png)
 
로그 항목에서 실패 이유를 제공하지만 [로그인 오류 조회 도구](https://login.microsoftonline.com/error)를 사용하여 자세한 정보를 얻을 수 있는 경우가 있습니다. 예를 들어 이 도구는 사용 가능한 경우 수정 단계를 제공합니다.  

![오류 코드 조회 도구](./media/concept-all-sign-ins/error-code-lookup-tool.png)



## <a name="filter-sign-in-activities"></a>로그인 활동 필터링


로그의 데이터를 필터링하여 사용자에게 적합한 수준까지 범위를 좁힐 수 있습니다.

![필터 추가 옵션을 보여주는 스크린샷.](./media/concept-sign-ins/04.png "로그인 작업")

**요청 ID** - 관심 있는 요청의 ID입니다.

**사용자** - 관심 있는 사용자의 이름 또는 UPN(사용자 계정 이름)입니다.

**애플리케이션** - 대상 애플리케이션의 이름입니다.
 
**상태** - 관심 있는 로그인 상태입니다.

- Success

- 실패

- 중단됨


**IP 주소** - 테넌트에 연결하는 데 사용되는 디바이스의 IP 주소입니다.

**위치** - 연결이 시작된 위치입니다.

- City

- 주/도

- 국가/지역


**리소스** - 로그인에 사용되는 서비스의 이름입니다.


**리소스 ID** - 로그인에 사용되는 서비스의 ID입니다.


**클라이언트 앱** - 테넌트에 연결하는 데 사용되는 클라이언트 앱의 형식입니다.

![클라이언트 앱 필터](./media/concept-sign-ins/client-app-filter.png)


> [!NOTE]
> 개인 정보 보호 약정으로 인해 Azure AD는 테넌트 간 시나리오의 경우 이 필드를 홈 테넌트에 채우지 않습니다.


|이름|최신 인증|설명|
|---|:-:|---|
|인증된 SMTP| |POP 및 IMAP 클라이언트에서 메일 메시지를 보낼 때 사용합니다.|
|Autodiscover| |Outlook 및 EAS 클라이언트에서 Exchange Online의 사서함을 찾아 연결할 때 사용합니다.|
|Exchange ActiveSync| |이 필터는 EAS 프로토콜을 시도한 모든 로그인 시도를 표시합니다.|
|브라우저|![파란색 확인 표시](./media/concept-sign-ins/check.png)|웹 브라우저를 사용하는 사용자의 로그인 시도를 모두 표시합니다.|
|Exchange ActiveSync| | Exchange ActiveSync를 사용하여 Exchange Online에 연결하는 클라이언트 앱이 있는 사용자의 모든 로그인 시도를 표시합니다.|
|Exchange Online PowerShell| |원격 PowerShell을 사용하여 Exchange Online에 연결하는 데 사용됩니다. Exchange Online PowerShell에 대한 기본 인증을 차단하는 경우 Exchange Online PowerShell 모듈을 사용하여 연결해야 합니다. 자세한 내용은 [다단계 인증을 사용하여 Exchange Online PowerShell에 연결](/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell)을 참조하세요.|
|Exchange 웹 서비스| |Outlook, Outlook for Mac 및 제3자 앱에서 사용하는 프로그래밍 인터페이스입니다.|
|IMAP4| |메일을 검색하는 데 IMAP를 사용하는 레거시 메일 클라이언트입니다.|
|HTTP를 통한 MAPI| |Outlook 2010 이상에서 사용됩니다.|
|모바일 앱 및 데스크톱 클라이언트|![파란색 확인 표시](./media/concept-sign-ins/check.png)|모바일 앱 및 데스크톱 클라이언트를 사용하는 사용자의 로그인 시도를 모두 표시합니다.|
|오프라인 주소록| |Outlook에서 다운로드하여 사용하는 주소 목록 컬렉션의 복사본입니다.|
|외부에서 Outlook 사용(RPC over HTTP)| |Outlook 2016 이하에서 사용됩니다.|
|Outlook 서비스| |Windows 10용 메일 및 일정 앱에서 사용합니다.|
|POP3| |POP3를 사용하여 메일을 검색하는 레거시 메일 클라이언트입니다.|
|보고 웹 서비스| |Exchange Online에서 보고서 데이터를 검색할 때 사용합니다.|
|기타 클라이언트| |클라이언트 앱이 포함되어 있지 않거나 알 수 없는 사용자의 로그인 시도를 모두 표시합니다.|







**운영 체제** - 테넌트에 로그인하는 데 사용되는 디바이스에서 실행 중인 운영 체제입니다. 


**디바이스 브라우저** - 브라우저에서 연결이 시작된 경우 해당 필드를 사용하여 브라우저 이름을 기준으로 필터링할 수 있습니다.


**상관 관계 ID** - 활동의 상관 관계 ID입니다.




**조건부 액세스** - 적용된 조건부 액세스 규칙의 상태입니다.

- **적용되지 않음**: 로그인하는 동안 사용자 및 애플리케이션에 정책이 적용되지 않습니다.

- **성공**: 로그인하는 동안 사용자 및 애플리케이션에 적용되는 하나 이상의 조건부 액세스 정책(반드시 다른 조건을 충족하지는 않음)입니다. 

- **실패**: 로그인에서 하나 이상의 조건부 액세스 정책에 대한 사용자 및 애플리케이션 조건을 충족했고, 권한 부여 컨트롤이 충족되지 않거나 액세스를 차단하도록 설정되었습니다.









## <a name="download-sign-in-activities"></a>로그인 활동 다운로드

**다운로드** 옵션을 클릭하여 가장 최근 25만 레코드의 CSV 또는 JSON 파일을 생성합니다. Azure Portal 외부에서 작업하려는 경우 [로그인 데이터 다운로드](./howto-download-logs.md)로 시작합니다.  

![다운로드](./media/concept-sign-ins/71.png "다운로드")

> [!IMPORTANT]
> 다운로드할 수 있는 레코드의 수는 [Azure Active Directory 보고서 보존 정책](reference-reports-data-retention.md)에 의해 제한됩니다.  


## <a name="sign-ins-data-shortcuts"></a>로그인 데이터 바로 가기

Azure AD와 Azure Portal은 모두 로그인 데이터를 위한 추가 진입점을 제공합니다.

- ID 보안 보호 개요
- 사용자
- 그룹
- Enterprise 애플리케이션

### <a name="users-sign-ins-data-in-identity-security-protection"></a>ID 보안 보호에서 사용자 로그인 데이터

**ID 보안 보호**  개요 페이지의 사용자 로그인 그래프는 주간 로그인 집계를 보여줍니다. 기간에 대한 기본값은 30일입니다.

![한 달 동안의 로그인 그래프를 보여주는 스크린샷.](./media/concept-sign-ins/06.png "로그인 작업")

로그인 그래프에서 한 날짜를 클릭하면 해당 날짜의 로그인 활동에 대한 개요가 표시됩니다.

로그인 활동 목록의 각 행에는 다음 내용이 표시됩니다.

* 누가 로그인했나요?
* 어떤 애플리케이션이 로그인할 대상이었나요?
* 로그인의 상태는 어떻습니까?
* 로그인의 MFA 상태는 어떻습니까?

항목을 클릭하면 로그인 작업에 대한 세부 정보가 표시됩니다.

- 사용자 ID
- 사용자
- 사용자 이름
- 애플리케이션 ID
- 애플리케이션
- 클라이언트
- 위치
- IP 주소
- Date
- 필요한 MFA
- 로그인 상태

> [!NOTE]
> IP 주소는 IP 주소와 해당 주소가 실제로 연결된 컴퓨터 간에 확실한 연결이 없는 경우와 같은 방법으로 발급됩니다. IP 주소 매핑은 클라이언트 디바이스가 실제로 사용되는 위치에서 종종 매우 먼 중앙 풀에서 모바일 공급자 또는 VPN이 IP 주소를 발급한다는 사실로 인해 복잡해집니다. 현재 IP 주소를 실제 위치로 변환하는 작업은 추적, 레지스트리 데이터, 역방향 조회 및 기타 정보를 기반으로 한 최선의 노력입니다.

**사용자** 페이지에서 **활동** 섹션의 **로그인** 을 클릭하면 모든 사용자 로그인에 대한 전체 개요가 표시됩니다.

![로그인을 선택할 수 있는 활동 섹션을 보여주는 스크린샷.](./media/concept-sign-ins/08.png "로그인 작업")

## <a name="authentication-details"></a>인증 세부 정보

로그인 보고서에 있는 **인증 세부 정보** 탭에서는 각 인증 시도에 대한 다음 정보를 제공합니다.

- 적용된 인증 정책 목록(예: 조건부 액세스, 사용자별 MFA, 보안 기본값)
- 적용된 세션 수명 정책 목록(예: 로그인 빈도, MFA 기억, 구성 가능한 토큰 수명)
- 로그인에 사용되는 인증 방법 시퀀스
- 인증 시도 성공 여부
- 인증 시도 성공 또는 실패 이유에 대한 세부 정보

관리자는 이 정보를 사용하여 사용자 로그인의 각 단계와 관련된 문제를 해결하고 다음을 추적할 수 있습니다.

- 다단계 인증으로 보호된 로그인 볼륨 
- 세션 수명 정책에 따른 인증 프롬프트 이유
- 각 인증 방법의 사용량 및 성공률 
- 암호 없는 인증 방법(예: 암호 없는 휴대폰 로그인, FIDO2, 비즈니스용 Windows Hello) 사용량 
- 토큰 클레임이 인증 요구 사항을 충족하는 빈도(사용자에게 암호, SMS OTP 등을 입력하라는 메시지를 대화형으로 표시하지 않는 경우)

로그인 보고서를 보는 동안 **인증 세부 정보** 탭을 선택합니다. 

![인증 세부 정보 탭 스크린샷](media/concept-sign-ins/auth-details-tab.png)

>[!NOTE]
>**OATH 확인 코드** 가 OATH 하드웨어 및 소프트웨어 토큰 둘 다의 인증 방법으로 로그됩니다(예: Microsoft Authenticator 앱).

>[!IMPORTANT]
>로그 정보가 완전히 집계될 때까지 처음에는 **인증 세부 정보** 탭에 불완전하거나 부정확한 데이터가 표시될 수 있습니다. 알려진 예는 다음과 같습니다. 
>- 로그인 이벤트가 처음 로그될 때 **토큰 클레임으로 충족** 메시지가 잘못 표시됩니다. 
>- 처음에는 **기본 인증** 행이 로그되지 않습니다. 


## <a name="usage-of-managed-applications"></a>관리되는 애플리케이션의 사용량

로그인 데이터의 애플리케이션 중심 보기를 사용하여 다음과 같은 질문에 대답할 수 있습니다.

* 누가 내 애플리케이션을 사용하나요?
* 조직의 상위 3개의 애플리케이션은 무엇인가요?
* 최신 애플리케이션이 어떻게 작동하나요?

이 데이터에 대한 진입점은 조직에서 상위 3개의 애플리케이션입니다. 데이터는 **엔터프라이즈 애플리케이션** 의 **개요** 섹션에 있는 지난 30일간의 보고서에 포함되어 있습니다.

![개요를 선택할 수 있는 곳을 보여주는 스크린샷.](./media/concept-sign-ins/10.png "로그인 작업")

앱 사용 그래프에는 지정된 기간 동안 상위 3개 애플리케이션에 대한 로그인의 주간 집계가 있습니다. 시간에 대한 기본값은 30일입니다.

![1개월 동안의 앱 사용량을 보여주는 스크린샷.](./media/concept-sign-ins/graph-chart.png "로그인 작업")

원하면 특정 애플리케이션에 포커스를 설정할 수 있습니다.

![보고](./media/concept-sign-ins/single-app-usage-graph.png "보고")

앱 사용량 그래프에서 날짜를 클릭하면 로그인 활동의 자세한 목록이 표시됩니다.

**로그인** 옵션을 선택하면 애플리케이션에 대한 모든 로그인 이벤트의 전체적인 개요를 보여 줍니다.

## <a name="microsoft-365-activity-logs"></a>Microsoft 365 활동 로그

[Microsoft 365 관리 센터](/office365/admin/admin-overview/about-the-admin-center)에서 Microsoft 365 활동 로그를 볼 수 있습니다. Microsoft 365 활동 및 Azure AD 활동 로그는 상당 수의 디렉터리 리소스를 공유한다는 점을 고려하세요. Microsoft 365 관리 센터에서만 Microsoft 365 활동 로그에 대한 전체 보기를 제공합니다. 

[Office 365 관리 API](/office/office-365-management-api/office-365-management-apis-overview)를 사용하여 프로그래밍 방식으로 Microsoft 365 활동 로그에 액세스할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure AD 데이터 보존 정책](reference-reports-data-retention.md)
* [Azure AD 보고서 대기 시간](reference-reports-latencies.md)
* [로그인 보고서의 자사 Microsoft 애플리케이션](/troubleshoot/azure/active-directory/verify-first-party-apps-sign-in#application-ids-for-commonly-used-microsoft-applications)
