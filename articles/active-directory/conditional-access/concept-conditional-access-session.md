---
title: 조건부 액세스 정책의 세션 제어 - Azure Active Directory
description: Azure AD 조건부 액세스 정책의 세션 제어란 무엇인가요?
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 81819fa99db4ff0171e96e5b2ba0ce3fc4604769
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114459542"
---
# <a name="conditional-access-session"></a>조건부 액세스: 세션

관리자는 조건부 액세스 정책 내 세션 제어를 활용하여 특정 클라우드 애플리케이션 내에서 제한된 환경을 사용하도록 설정할 수 있습니다.

![다단계 인증이 필요한 권한 부여 제어를 사용하는 조건부 액세스 정책](./media/concept-conditional-access-session/conditional-access-session.png)

## <a name="application-enforced-restrictions"></a>애플리케이션 적용 제한

조직은 이 제어를 사용하여 Azure AD가 디바이스 정보를 선택한 클라우드 앱에 전달하도록 요구할 수 있습니다. 디바이스 정보를 사용하면 클라우드 앱이 규정 준수 또는 도메인 조인 디바이스에서 연결이 초기화되는지 여부를 알 수 있습니다. 이 컨트롤은 선택한 클라우드 앱으로 SharePoint Online 및 Exchange Online만 지원합니다. 선택하면 클라우드 앱에서는 디바이스 정보를 사용하여 사용자에게 디바이스 상태에 따라 제한된 환경이나 전체 환경을 제공합니다.

앱 적용 제한 사용 및 구성에 대한 자세한 내용은 다음 문서를 참조하세요.

- [SharePoint Online을 사용하여 제한된 액세스를 사용하도록 설정](/sharepoint/control-access-from-unmanaged-devices)
- [Exchange Online을 사용하여 제한된 액세스를 사용하도록 설정](https://aka.ms/owalimitedaccess)

## <a name="conditional-access-application-control"></a>조건부 액세스 애플리케이션 제어

조건부 액세스 앱 제어는 역방향 프록시 아키텍처를 사용하며 Azure AD 조건부 액세스와 고유하게 통합됩니다. Azure AD 조건부 액세스를 사용하면 특정 조건에 따라 조직의 앱에 대한 액세스 제어를 적용할 수 있습니다. 조건은 조건부 액세스 정책이 누구에게(사용자 또는 사용자 그룹), 무엇에(클라우드 앱), 어디에(위치 및 네트워크) 적용되는지 정의합니다. 조건을 확인한 후 액세스 및 세션 제어를 적용하여 조건부 액세스 앱 제어로 데이터를 보호할 수 있는 [Microsoft Cloud App Security](/cloud-app-security/what-is-cloud-app-security)로 사용자를 라우팅할 수 있습니다.

조건부 액세스 앱 제어를 통해 사용자는 액세스 및 세션 정책에 따라 실시간으로 앱 액세스 및 세션을 모니터링하고 제어할 수 있습니다. 액세스 및 세션 정책은 Cloud App Security 포털에서 사용되어 필터를 자세히 구체화하고 사용자에 대해 수행할 작업을 설정합니다. 액세스 및 세션 정책을 사용하면 다음을 수행할 수 있습니다.

- 데이터 반출 방지: 비관리형 디바이스 등에서 중요한 문서의 다운로드, 잘라내기, 복사, 인쇄를 차단할 수 있습니다.
- 다운로드 시 보호: 중요한 문서의 다운로드를 차단하는 대신 문서에 레이블을 지정하고 Azure Information Protection으로 문서를 보호하도록 요구할 수 있습니다. 이 작업을 수행하면 문서가 보호되고 잠재적으로 위험한 세션에서 사용자 액세스가 제한됩니다.
- 레이블이 지정되지 않은 파일 업로드 방지: 다른 사용자가 중요한 파일을 업로드, 배포, 사용하기 전에 파일에 올바른 레이블과 보호가 적용되었는지 확인하는 것이 중요합니다. 사용자가 콘텐츠를 분류할 때까지 중요한 콘텐츠가 포함되고 레이블이 지정되지 않은 파일이 업로드되지 않도록 할 수 있습니다.
- 사용자 세션에서 준수 모니터링(미리 보기): 위험 사용자가 앱에 로그인하면 모니터링되고 해당 작업이 세션 내에서 로깅됩니다. 사용자 동작을 조사하고 분석하여 나중에 세션 정책을 적용해야 하는 위치와 조건을 이해할 수 있습니다.
- 액세스 차단(미리 보기): 몇 가지 위험 요소에 따라 특정 앱과 사용자의 액세스를 세부적으로 차단할 수 있습니다. 예를 들어 디바이스 관리의 형태로 클라이언트 인증서를 사용하는 경우 차단할 수 있습니다.
- 사용자 지정 활동 차단: 일부 앱에는 Microsoft Teams, Slack 등의 앱에서 중요한 콘텐츠가 포함된 메시지를 보내는 경우와 같이 위험을 수반하는 고유한 시나리오가 있습니다. 이 종류의 시나리오에서는 메시지에서 중요한 콘텐츠를 검사하고 실시간으로 메시지를 차단할 수 있습니다.

자세한 내용은 [추천 앱에 대한 조건부 액세스 앱 제어 배포](/cloud-app-security/proxy-deployment-aad) 문서를 참조하세요.

## <a name="sign-in-frequency"></a>로그인 빈도

로그인 빈도는 리소스에 액세스하려고 할 때 사용자에게 다시 로그인하라는 메시지가 표시되는 기간을 정의합니다.

로그인 빈도 설정은 표준에 따라 OAUTH2 또는 OIDC 프로토콜을 구현한 앱에서 적용됩니다. 다음 웹 애플리케이션을 포함하여 Windows, Mac, 모바일용 Microsoft 네이티브 앱은 대부분 이 설정을 준수합니다.

- Word, Excel, PowerPoint Online
- OneNote Online
- Office.com
- Microsoft 365 관리 포털
- Exchange Online
- SharePoint 및 OneDrive
- 팀 웹 클라이언트
- Dynamics CRM Online
- Azure portal

자세한 내용은 [조건부 액세스를 사용하여 인증 세션 관리 구성](howto-conditional-access-session-lifetime.md#user-sign-in-frequency) 문서를 참조하세요.

## <a name="persistent-browser-session"></a>영구적 브라우저 세션

영구 브라우저 세션을 사용하면 사용자가 브라우저 창을 닫았다가 다시 연 후 로그인 상태를 유지할 수 있습니다.

자세한 내용은 [조건부 액세스를 사용하여 인증 세션 관리 구성](howto-conditional-access-session-lifetime.md#persistence-of-browsing-sessions) 문서를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [조건부 액세스 일반 정책](concept-conditional-access-policy-common.md)

- [보고서 전용 모드](concept-conditional-access-report-only.md)
