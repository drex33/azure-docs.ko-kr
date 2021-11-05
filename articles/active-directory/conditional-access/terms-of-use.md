---
title: 사용 약관 - Azure Active Directory | Microsoft Docs
description: 액세스 권한을 얻기 전에 Azure Active Directory 사용 약관을 사용하여 직원 또는 게스트에게 정보를 제공하는 것을 시작하세요.
services: active-directory
ms.service: active-directory
ms.subservice: compliance
ms.topic: how-to
ms.date: 07/12/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: 754520be35cbac4321fb7fbb65135016642a7ee1
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131018322"
---
# <a name="azure-active-directory-terms-of-use"></a>Azure Active Directory 사용 약관

Azure AD 사용 약관 정책은 조직이 최종 사용자에게 정보를 제공하는 데 사용할 수 있는 간단한 방법을 제공합니다. 이 프레젠테이션은 사용자가 법률 또는 규정 준수 요구 사항에 대한 관련 고지 사항을 볼 수 있게 해줍니다. 이 문서에서는 ToU(사용 약관) 정책을 시작하는 방법을 설명합니다.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview-videos"></a>개요 비디오

다음 비디오에서는 ToU 정책의 간략한 개요를 제공합니다.

>[!VIDEO https://www.youtube.com/embed/tj-LK0abNao]

비디오에 대한 자세한 내용은 다음을 참조하세요.
- [Azure Active Directory의 사용 약관 정책 배포 방법](https://www.youtube.com/embed/N4vgqHO2tgY)
- [Azure Active Directory의 사용 약관 정책 롤아웃 방법](https://www.youtube.com/embed/t_hA4y9luCY)

## <a name="what-can-i-do-with-terms-of-use"></a>사용 약관으로 무엇을 할 수 있나요?

Azure AD 사용 약관 정책에는 다음과 같은 기능이 있습니다.

- 직원 또는 게스트가 서비스에 액세스하려면 사용 약관 정책에 동의하도록 요구.
- 직원 또는 게스트가 서비스에 액세스하려면 모든 디바이스에서 사용 약관 정책에 동의하도록 요구.
- 직원 또는 게스트가 되풀이 일정에서 사용 약관 정책에 동의하도록 요구.
- Azure AD MFA(Multi-Factor Authentication)에 보안 정보를 등록하려면 먼저 직원 또는 게스트가 사용 약관 정책에 동의하도록 요구.
- Azure AD SSPR(셀프 서비스 암호 재설정)에 보안 정보를 등록하기 전에 직원이 사용 약관 정책에 동의하도록 요구.
- 조직의 모든 사용자에 대한 일반 사용 약관 정책 제공.
- [동적 그룹](../enterprise-users/groups-dynamic-membership.md)을 사용하여 사용자 특성(예: 의사 대 간호사, 국내 대 국제 직원)을 기반으로 사용 정책의 특정 약관을 제공합니다.
- Salesforce 같은 비즈니스 영향력이 높은 애플리케이션에 액세스할 때 특정 사용 약관 정책 제공.
- 여러 언어로 사용 약관 정책 제공.
- 사용 약관 정책에 동의한 또는 동의하지 않은 사용자 나열.
- 개인 정보 보호 규정을 준수하도록 지원
- 준수 및 감사를 위해 사용 약관 정책 활동 로그 표시.
- [Microsoft Graph API](/graph/api/resources/agreement)를 사용하여 사용 약관 정책을 만들고 관리(현재 미리 보기로 제공).

## <a name="prerequisites"></a>필수 요건

Azure AD 사용 약관 정책을 사용하고 구성하려면 다음이 필요합니다.

- Azure AD Premium P1, P2, EMS E3 또는 EMS E5 구독.
   - 해당 구독이 없는 경우 [Azure AD Premium을 구매](../fundamentals/active-directory-get-started-premium.md)하거나 [Azure AD Premium 평가판을 사용](https://azure.microsoft.com/trial/get-started-active-directory/)할 수 있습니다.
- 구성하려는 디렉터리에 대한 다음 관리자 계정 중 하나:
   - 전역 관리자
   - 보안 관리자
   - 조건부 액세스 관리자

## <a name="terms-of-use-document"></a>사용 약관 문서

Azure AD 사용 약관 정책은 PDF 형식을 사용하여 콘텐츠를 제공합니다. PDF 파일은 기존 계약서와 같이 사용자가 로그인하는 동안 최종 사용자 계약을 수집할 수 있는 모든 콘텐츠가 될 수 있습니다. 모바일 디바이스에서 사용자를 지원하기 위한 PDF의 권장 글꼴 크기는 24pt입니다.

## <a name="add-terms-of-use"></a>사용 약관 추가

사용 약관 정책 문서를 완성했으면 다음 절차에 따라 추가합니다.

1. Azure에 전역 관리자, 보안 관리자 또는 조건부 액세스 관리자로 로그인합니다.
1. [https://aka.ms/catou](https://aka.ms/catou)에서 **사용 약관** 으로 이동합니다.

    ![조건부 액세스 - 사용 약관 블레이드](./media/terms-of-use/tou-blade.png)

1. **새 사용 약관** 을 클릭합니다.

    ![사용 약관 설정을 지정하기 위한 새로운 사용 약관 창](./media/terms-of-use/new-tou.png)

1. **이름** 상자에 Azure Portal에서 사용할 사용 약관 정책의 이름을 입력합니다.
1. **표시 이름** 상자에 사용자가 로그인할 때 표시되는 제목을 입력합니다.
1. **사용 약관 문서** 를 보려면 완료된 사용 약관 정책 PDF로 이동하여 이를 선택합니다.
1. 사용 약관 정책 문서의 언어를 선택합니다. 언어 옵션을 사용하면 사용 약관 정책을 다양한 언어로 여러 개 업로드할 수 있습니다. 최종 사용자에게 표시되는 사용 약관 정책의 버전은 브라우저 기본 설정에 기반합니다.
1. 최종 사용자에게 동의하기 전에 사용 약관 정책을 보도록 요구하려면 **사용자가 사용 약관을 확장해야 함** 을 **켜기** 로 설정합니다.
1. 최종 사용자에게 액세스할 모든 디바이스에서 사용 약관 정책에 동의하도록 요구하려면 **사용자가 모든 디바이스에 동의해야 함** 을 **켜기** 로 설정합니다. 해당 옵션을 사용하도록 설정하면 사용자가 추가 애플리케이션을 설치해야 할 수 있습니다. 자세한 내용은 [디바이스별 사용 약관](#per-device-terms-of-use)을 참조하세요.
1. 일정에서 사용 약관 정책 동의를 만료하려면 **동의 만료** 를 **켜기** 로 설정합니다. 켜기로 설정되면 두 개의 추가 일정 설정이 표시됩니다.

    ![시작 날짜, 빈도, 기간을 설정하는 만료 동의 설정](./media/terms-of-use/expire-consents.png)

1. **만료 시작 날짜** 및 **빈도** 설정을 사용하여 사용 약관 정책 만료의 일정을 지정합니다. 다음 표는 몇 가지 예제 설정의 결과를 보여 줍니다.

   | 만료 시작 날짜 | 빈도 | 결과 |
   | --- | --- | --- |
   | 오늘 날짜  | 매월 | 오늘부터 사용자는 사용 약관 정책에 동의한 후 매월 다시 동의해야 합니다. |
   | 미래 날짜  | 매월 | 오늘부터 사용자는 사용 약관 정책에 동의해야 합니다. 미래 날짜가 발생하면 동의가 만료되고 이후 사용자는 매월 다시 동의해야 합니다.  |

   예를 들어 만료 시작 날짜를 **1월 1일** 로 설정하고 빈도를 **매월** 로 설정하는 경우 사용자 두 명에 대한 만료는 다음과 같은 방식으로 발생합니다.

   | 사용자 | 첫 번째 동의 날짜 | 첫 번째 만료 날짜 | 두 번째 만료 날짜 | 세 번째 만료 날짜 |
   | --- | --- | --- | --- | --- |
   | Alice | 1월 1일 | 2월 1일 | 3월 1일 | 4월 1일 |
   | Bob | 1월 15일 | 2월 1일 | 3월 1일 | 4월 1일 |

1. **다시 동의가 필요하기 전 기간(일)** 설정을 사용하여 사용자가 사용 약관 정책에 다시 동의해야 하기 전 일 수를 지정합니다. 이를 통해 사용자는 자신의 일정을 따를 수 있습니다. 예를 들어 기간을 **30** 일로 설정하면 두 사용자에 대한 만료가 다음과 같은 방식으로 발생합니다.

   | 사용자 | 첫 번째 동의 날짜 | 첫 번째 만료 날짜 | 두 번째 만료 날짜 | 세 번째 만료 날짜 |
   | --- | --- | --- | --- | --- |
   | Alice | 1월 1일 | 1월 31일 | 3월 2일 | 4월 1일 |
   | Bob | 1월 15일 | 2월 14일 | 3월 16일 | 4월 15일 |

   **동의 만료** 및 **다시 동의가 필요하기 전 기간(일)** 설정을 함께 사용할 수 있지만, 일반적으로 두 설정 중 하나만 사용합니다.

1. **조건부 액세스** 에서 **조건부 액세스 정책 템플릿을 사용하여 적용** 목록을 사용하여 사용 약관 정책을 적용할 템플릿을 선택합니다.

    ![정책 템플릿을 선택하는 조건부 액세스 드롭다운 목록](./media/terms-of-use/conditional-access-templates.png)

   | 템플릿 | Description |
   | --- | --- |
   | **모든 게스트에 대한 클라우드 앱 액세스** | 모든 게스트 및 모든 클라우드 앱에 대한 조건부 액세스 정책이 생성됩니다. 이 정책은 Azure Portal에 영향을 줍니다. 이 항목이 생성되면 로그아웃했다 로그인해야 할 수 있습니다. |
   | **모든 사용자에 대한 클라우드 앱 액세스** | 모든 사용자 및 모든 클라우드 앱에 대한 조건부 액세스 정책이 생성됩니다. 이 정책은 Azure Portal에 영향을 줍니다. 이 항목이 생성되면 로그아웃했다 로그인해야 할 수 있습니다. |
   | **사용자 지정 정책** | 이 사용 약관 정책이 적용될 사용자, 그룹 및 앱을 선택합니다. |
   | **나중에 조건부 액세스 정책 생성** | 이 사용 약관 정책은 조건부 액세스 정책을 만들 때 권한 부여 컨트롤 목록에 표시됩니다. |

   >[!IMPORTANT]
   >조건부 액세스 정책 제어(사용 약관 정책 포함)는 서비스 계정에 적용을 지원하지 않습니다. 조건부 액세스 정책에서 모든 서비스 계정을 제외하는 것을 추천합니다.

    사용자 지정 조건부 액세스 정책을 통해 특정 클라우드 애플리케이션 또는 사용자 그룹에까지 세분화된 사용 약관 정책을 설정할 수 있습니다. 자세한 내용은 [빠른 시작: 클라우드 앱에 액세스하기 전에 사용 약관에 동의하도록 요구](require-tou.md)를 참조하세요.

1. **만들기** 를 클릭합니다.

    사용자 지정 조건부 액세스 템플릿을 선택한 경우 사용자 지정 조건부 액세스 정책을 만들 수 있는 새 화면이 나타납니다.

   ![사용자 지정 조건부 액세스 정책 템플릿을 선택한 경우의 새 조건부 액세스 창](./media/terms-of-use/custom-policy.png)

   이제 새 사용 약관 정책이 표시됩니다.

   ![사용 약관 블레이드에 나열된 새로운 사용 약관](./media/terms-of-use/create-tou.png)

## <a name="view-report-of-who-has-accepted-and-declined"></a>사용 약관에 동의/거부한 사람에 대한 보고서 보기

[사용 약관] 블레이드에는 사용 약관에 동의한 사용자 및 거부한 사용자 수가 표시됩니다. 이러한 사용자 수 및 동의/거부한 사용자 명단은 사용 약관 정책의 수명 기간 동안 저장됩니다.

1. [https://aka.ms/catou](https://aka.ms/catou)에서 Azure에 로그인하여 **사용 약관** 으로 이동합니다.

    ![사용자 표시 수를 나열하는 사용 약관 블레이드가 동의되거나 거부됨](./media/terms-of-use/view-tou.png)

1. 사용 약관 정책의 경우 **동의** 또는 **거부** 아래의 숫자를 클릭하면 사용자의 현재 상태를 볼 수 있습니다.

    ![동의한 사용자를 나열하는 사용 약관 동의 창](./media/terms-of-use/accepted-tou.png)

1. 개별 사용자의 기록을 보려면 줄임표(**...**)를 클릭한 후 **기록 보기** 를 클릭합니다.

    ![사용자 기록 보기 바로 가기 메뉴](./media/terms-of-use/view-history-menu.png)

   [기록 보기] 창에서 모든 승인, 거부 및 만료 기록을 확인합니다.

   ![기록 보기 창에는 사용자의 동의, 거부, 만료 기록이 나열됩니다.](./media/terms-of-use/view-history-pane.png)

## <a name="view-azure-ad-audit-logs"></a>Azure AD 감사 로그 보기

추가 활동을 보려는 경우 Azure AD 사용 약관 정책의 감사 로그를 확인합니다. 각 사용자 동의는 **30일** 동안 저장된 감사 로그에서 이벤트를 트리거합니다. 포털에서 이러한 로그를 살펴보거나 .csv 파일로 다운로드할 수 있습니다.

Azure AD 감사 로그를 시작하려면 다음 절차를 따릅니다.

1. [https://aka.ms/catou](https://aka.ms/catou)에서 Azure에 로그인하여 **사용 약관** 으로 이동합니다.
1. 사용 약관 정책을 선택합니다.
1. **감사 로그 보기** 를 클릭합니다.

    ![감사 로그 보기 옵션이 강조 표시된 사용 약관 블레이드](./media/terms-of-use/audit-tou.png)

1. Azure AD 감사 로그 화면에서 제공된 목록을 사용하여 특정 감사 로그 정보를 대상으로 하도록 정보를 필터링할 수 있습니다.

    **다운로드** 를 클릭하여 로컬에서 사용하도록 정보를 .csv 파일로 다운로드할 수도 있습니다.

   ![Azure AD 감사 로그 화면 목록 날짜, 대상 정책, 초기자, 작업](./media/terms-of-use/audit-logs-tou.png)

   로그를 클릭하면 추가 활동 세부 정보가 포함된 창이 나타납니다.

   ![작업, 작업 상태, 초기자, 대상 정책을 보여 주는 로그의 작업 세부 정보](./media/terms-of-use/audit-log-activity-details.png)

## <a name="what-terms-of-use-looks-like-for-users"></a>사용자에게 표시되는 사용 약관

ToU 정책을 만들고 적용한 후에는 로그인하는 동안 범위 내에 있는 사용자에게 다음 화면이 표시됩니다.

![사용자가 로그인할 때 표시되는 사용 약관의 예](./media/terms-of-use/user-tou.png)

사용자는 사용 약관 정책을 볼 수 있으며, 필요한 경우 단추를 사용하여 확대 및 축소할 수 있습니다.

![확대/축소 단추를 사용하여 사용 약관 보기](./media/terms-of-use/zoom-buttons.png)

다음 화면은 모바일 디바이스에서 사용 약관 정책이 어떻게 표시되는지 보여 줍니다.

![사용자가 모바일 디바이스에 로그인할 때 표시되는 사용 약관의 예](./media/terms-of-use/mobile-tou.png)

사용자는 한 번만 사용 약관 정책에 동의해야 하며 후속 로그인 시 다시 사용 약관 정책이 표시되지 않습니다.

### <a name="how-users-can-review-their-terms-of-use"></a>사용자가 사용 약관을 검토하는 방법

사용자는 다음 절차를 사용하여 동의한 사용 정책 사용 약관 정책을 검토하고 확인할 수 있습니다.

1. [https://myaccount.microsoft.com/](https://myaccount.microsoft.com/)에 로그인합니다.
1. **설정 및 개인 정보** 를 선택합니다.
1. **개인 정보** 를 선택합니다.
1. **조직의 알림** 에서 검토할 사용 약관 옆에 있는 **보기** 를 선택합니다.

## <a name="edit-terms-of-use-details"></a>사용 약관 세부 정보 편집

사용 약관 정책 세부 정보 중 일부는 편집할 수 있지만 기존 문서는 수정할 수 없습니다. 다음 절차에서는 세부 정보를 편집하는 방법에 대해 설명합니다.

1. [https://aka.ms/catou](https://aka.ms/catou)에서 Azure에 로그인하여 **사용 약관** 으로 이동합니다.
1. 편집하려는 사용 약관 정책을 선택합니다.
1. **사용 약관 편집** 을 클릭합니다.
1. 사용 약관 편집 창에서 다음을 변경할 수 있습니다.
    - **이름** – 최종 사용자와 공유되지 않는 사용 약관의 내부 이름입니다.
    - **표시 이름** – 최종 사용자가 볼 수 있는 사용 약관의 이름입니다.
    - **사용자에게 사용 약관을 확장하도록 요구** – **켜기** 로 설정하면 최종 사용자가 동의하기 전에 사용 약관 정책 문서를 확장하도록 합니다.
    - (미리 보기) **기존 사용 약관 문서를 업데이트** 할 수 있습니다.
    - 기존 사용 약관에 언어를 추가할 수 있습니다.

   PDF 문서, 사용자가 모든 디바이스에 동의해야 함, 동의 만료, 다시 동의 전 기간 또는 조건부 액세스 정책과 같이 변경할 다른 설정이 있는 경우 새 사용 약관 정책을 만들어야 합니다.

    ![다른 언어 표시 편집 옵션 ](./media/terms-of-use/edit-terms-use.png)

1. 완료되면 **저장** 을 클릭하여 변경 내용을 저장합니다.

## <a name="update-the-version-or-pdf-of-an-existing-terms-of-use"></a>기존 사용 약관의 버전 또는 PDF 업데이트

1.  Azure에 로그인하고 [사용 약관](https://aka.ms/catou)으로 이동합니다.
2.  편집하려는 사용 약관 정책을 선택합니다.
3.  **사용 약관 편집** 을 클릭합니다.
4.  새 버전으로 업데이트하려는 언어에 대해 작업 열 아래의 **업데이트** 를 클릭합니다.

    ![이름 및 확장 옵션을 보여 주는 사용 약관 창 편집](./media/terms-of-use/edit-terms-use.png)

5.  오른쪽 창에서 새 버전에 대한 PDF를 업로드합니다.
6.  또한 사용자가 다음 번에 로그인할 때 이 새 버전에 동의하도록 요구하려는 경우에는 이 옵션을 다시 **동의해야 합니다**. 사용자가 다음에 다시 동의해야 하는 경우 사용자가 조건부 액세스 정책에 정의된 리소스에 액세스하려고 할 때 이 새 버전을 동의하라는 메시지가 표시됩니다. 사용자가 다시 동의하지 않아도 되는 경우 이전의 동의는 현재 상태를 유지하고 이전에 동의하지 않은 새로운 사용자 또는 동의가 만료된 사용자만 새 버전을 보게 됩니다. 세션이 만료될 때까지 **재승인 필요** 는 사용자가 새 TOU를 수락할 필요가 없습니다. 이 경우에 삭제 및 재생성 또는 새 TOU를 만들려면 다시 동의해야 합니다.

    ![사용 약관 다시 동의 편집 옵션 강조 표시](./media/terms-of-use/re-accept.png)

7.  새 PDF를 업로드하고 다시 동의하기로 결정한 후에는 창 맨 아래에 있는 추가를 클릭합니다.
8.  이제 문서 열 아래에 최신 버전이 표시됩니다.

## <a name="view-previous-versions-of-a-tou"></a>이전 버전의 ToU 보기

1.  https://aka.ms/catou에서 Azure에 로그인하여 **사용 약관** 으로 이동합니다.
2.  버전 기록을 보려는 사용 약관 정책을 선택합니다.
3.  **언어 및 버전 기록** 클릭합니다.
4.  **이전 버전 보기** 를 클릭합니다.

    ![언어 버전을 비롯한 문서 세부 정보](./media/terms-of-use/document-details.png)

5.  문서 이름을 클릭하여 해당 버전을 다운로드할 수 있습니다.

## <a name="see-who-has-accepted-each-version"></a>각 버전을 동의한 사용자를 확인합니다.

1.  https://aka.ms/catou에서 Azure에 로그인하여 **사용 약관** 으로 이동합니다.
2.  현재 어떤 사용자가 ToU를 동의했는지 확인하려면 원하는 ToU에 대한 **동의** 열 아래의 숫자를 클릭합니다.
3.  기본적으로 다음 페이지에는 사용자에 대한 각 사용자의 현재 상태가 표시됩니다.
4.  이전 동의 이벤트를 보려면 **현재 상태** 드롭다운에서 **모두** 를 선택할 수 있습니다. 이제 각 사용자 이벤트에서 각 버전에 대한 세부 정보 및 발생한 상황을 볼 수 있습니다.
5.  또는 **버전** 드롭다운에서 특정 버전을 선택하여 특정 버전을 동의한 사용자를 확인할 수 있습니다.


## <a name="add-a-tou-language"></a>ToU 언어 추가

다음 절차에서는 ToU 언어를 추가하는 방법에 대해 설명합니다.

1. [https://aka.ms/catou](https://aka.ms/catou)에서 Azure에 로그인하여 **사용 약관** 으로 이동합니다.
1. 편집하려는 사용 약관 정책을 선택합니다.
1. **사용 약관 편집** 을 클릭합니다.
1. 페이지 맨 아래에 있는 **추가** 를 클릭합니다.
1. [사용 약관 언어 추가] 창에서 지역화된 PDF를 업로드하고 언어를 선택합니다.

    ![사용 약관을 선택하고 세부 정보 창에서 언어 탭을 표시합니다.](./media/terms-of-use/select-language.png)

1. **언어 추가** 를 클릭합니다.
1. **저장** 클릭

1. **추가** 를 클릭하여 언어를 추가합니다.

## <a name="per-device-terms-of-use"></a>디바이스별 사용 약관

**사용자가 모든 디바이스에 동의해야 함** 설정을 사용하면 최종 사용자에게 액세스할 모든 디바이스에서 사용 약관 정책에 동의하도록 요구할 수 있습니다. 최종 사용자는 Azure AD에서 해당 디바이스에 등록해야 합니다. 디바이스가 등록되면 디바이스 ID를 사용하여 각 디바이스에서 사용 약관 정책을 적용합니다.

지원되는 플랫폼 및 소프트웨어 목록은 다음과 같습니다.

> [!div class="mx-tableFixed"]
> |  | iOS | Android | Windows 10 | 기타 |
> | --- | --- | --- | --- | --- |
> | **네이티브 앱** | 예 | 예 | 예 |  |
> | **Microsoft Edge** | 예 | 예 | 예 |  |
> | **Internet Explorer** | 예 | 예 | 예 |  |
> | **Chrome(확장 포함)** | 예 | 예 | 예 |  |

디바이스별 사용 약관에는 다음 제약 조건이 있습니다.

- 디바이스는 하나의 테넌트에만 연결될 수 있습니다.
- 사용자는 해당 디바이스에 연결할 권한이 있어야 합니다.
- Intune 등록 앱이 지원되지 않습니다. 사용 약관 정책을 필요로 하는 조건부 액세스 정책에서 제외되는지 확인합니다.
- Azure AD B2B 사용자는 지원되지 않습니다.

사용자의 디바이스가 연결되지 않으면 해당 디바이스에 연결해야 한다는 메시지가 표시됩니다. 해당 환경은 플랫폼 및 소프트웨어에 따라 다릅니다.

### <a name="join-a-windows-10-device"></a>Windows 10 디바이스 연결

사용자가 Windows 10 및 Microsoft Edge를 사용하는 경우 [해당 디바이스에 연결](https://support.microsoft.com/account-billing/join-your-work-device-to-your-work-or-school-network-ef4d6adb-5095-4e51-829e-5457430f3973#to-join-an-already-configured-windows-10-device)하기 위해 다음과 같은 메시지가 표시됩니다.

![디바이스를 등록해야 함을 나타내는 Windows 10 및 Microsoft Edge 메시지](./media/terms-of-use/per-device-win10-edge.png)

Chrome을 사용하는 경우 [Windows 10 계정 확장](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji)을 설치할지 묻는 메시지가 표시됩니다.

### <a name="register-an-ios-device"></a>iOS 디바이스 등록

사용자가 iOS 디바이스를 사용하는 경우 [Microsoft Authenticator 앱](https://apps.apple.com/us/app/microsoft-authenticator/id983156458)을 설치하라는 메시지가 표시됩니다.

### <a name="register-an-android-device"></a>Android 디바이스 등록

사용자가 Android 디바이스를 사용하는 경우 [Microsoft Authenticator 앱](https://play.google.com/store/apps/details?id=com.azure.authenticator)을 설치하라는 메시지가 표시됩니다.

### <a name="browsers"></a>브라우저

사용자가 지원되지 않는 브라우저를 사용하는 경우에는 다른 브라우저를 사용하도록 요청하는 메시지가 표시됩니다.

![디바이스를 등록해야 하지만 브라우저가 지원되지 않음을 나타내는 메시지입니다.](./media/terms-of-use/per-device-browser-unsupported.png)

## <a name="delete-terms-of-use"></a>사용 약관 삭제

다음 절차에 따라 이전 사용 약관 정책을 삭제할 수 있습니다.

1. [https://aka.ms/catou](https://aka.ms/catou)에서 Azure에 로그인하여 **사용 약관** 으로 이동합니다.
1. 제거하려는 사용 약관 정책을 선택합니다.
1. **사용 약관 삭제** 를 클릭합니다.
1. 계속 진행할 것인지 묻는 메시지가 표시되면 **예** 를 클릭합니다.

    ![사용 약관 삭제에 대한 확인을 요청하는 메시지](./media/terms-of-use/delete-tou.png)

   사용 약관 정책이 더 이상 표시되지 않습니다.

## <a name="user-acceptance-record-deletion"></a>사용자 동의 레코드 삭제

사용자 동의 레코드가 다음과 같은 경우에 삭제됩니다.

- 관리자가 해당 ToU를 명시적으로 삭제하는 경우 이 경우 해당 특정 ToU에 연결된 모든 동의 레코드도 삭제됩니다.
- 테넌트가 Azure Active Directory Premium 라이선스를 손실한 경우.
- 테넌트가 삭제된 경우.

## <a name="policy-changes"></a>정책 변경 내용

조건부 액세스 정책은 즉시 적용됩니다. 이 경우 관리자에게 "sad 클라우드" 또는 "Azure AD 토큰 문제"가 표시되기 시작합니다. 관리자는 새 정책을 충족하기 위해 로그아웃했다가 다시 로그인해야 합니다.

> [!IMPORTANT]
> 범위에 있는 사용자는 다음과 같은 경우 새 정책을 충족하기 위해 로그아웃 및 로그인해야 합니다.
>
> - 조건부 액세스 정책은 사용 약관 정책에서 사용하도록 설정된 경우
> - 또는 두 번째 사용 약관 정책이 만들어진 경우

## <a name="b2b-guests"></a>B2B 게스트

대부분의 조직에는 직원이 조직의 사용 약관 정책 및 개인 정보 취급 방침에 동의할 수 있는 프로세스가 있습니다. 그러나 SharePoint 또는 Teams를 통해 추가될 경우 Azure AD B2B 게스트에게 동일한 동의를 어떻게 적용할 수 있나요? 조건부 액세스 및 사용 약관 정책을 사용하여 B2B 게스트 사용자에게 직접 정책을 적용할 수 있습니다. 초대 사용 흐름 중에 사용자에게 사용 약관 정책이 표시됩니다. 이 지원은 현재 미리 보기로 제공되고 있습니다.

사용 약관 정책은 사용자가 Azure AD에서 게스트 계정을 가지고 있는 경우에만 표시됩니다. 현재 SharePoint Online에는 사용자에게 게스트 계정이 없어도 사용할 수 있는 문서 또는 폴더를 공유하는 [임시 외부 공유 수신자 환경](/sharepoint/what-s-new-in-sharing-in-targeted-release)이 있습니다. 이 경우 사용 약관 정책이 표시되지 않습니다.

![사용자 및 그룹 창 - 모든 게스트 사용자 옵션을 선택한 탭 포함](./media/terms-of-use/b2b-guests.png)

## <a name="support-for-cloud-apps"></a>클라우드 앱 지원

사용 약관 정책은 Azure Information Protection 및 Microsoft Intune과 같은 여러 클라우드 앱에 사용할 수 있습니다. 이 지원은 현재 미리 보기로 제공되고 있습니다.

### <a name="azure-information-protection"></a>Azure Information Protection

Azure Information Protection 앱에 대한 조건부 액세스 정책을 구성하고 사용자가 보호된 문서에 액세스할 때 사용 약관 정책을 요구할 수 있습니다. 이렇게 하면 사용자가 처음으로 보호된 문서에 액세스하기 전에 사용 약관 정책이 트리거됩니다.

![Microsoft Azure Information Protection 앱이 선택된 클라우드 앱 창](./media/terms-of-use/cloud-app-info-protection.png)

### <a name="microsoft-intune-enrollment"></a>Microsoft Intune 등록

Microsoft Intune 등록 앱에 대한 조건부 액세스 정책을 구성하고 Intune에 디바이스를 등록하기 전에 사용 약관 정책을 요구할 수 있습니다. 자세한 내용은 [조직에 적합한 약관 솔루션 선택 블로그 게시물](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409) 읽기를 참조하세요.

![Microsoft Intune 앱이 선택된 클라우드 앱 창](./media/terms-of-use/cloud-app-intune.png)

> [!NOTE]
> [디바이스별 사용 약관](#per-device-terms-of-use)에 대해 Intune 등록 앱이 지원되지 않습니다.

## <a name="frequently-asked-questions"></a>질문과 대답

**Q: 사용 약관을 사용하는 경우 PowerShell을 사용하여 로그인할 수 없습니다.**<br />
A: 사용 약관은 대화형으로 인증하는 경우에만 동의할 수 있습니다.

**Q: 사용자가 사용 약관에 동의했는지 어떻게 볼 수 있나요?**<br />
A: 사용 약관 블레이드에서 **수락** 아래에 있는 숫자를 클릭합니다. Azure AD 감사 로그에서 동의 작업을 보거나 검색할 수도 있습니다. 자세한 내용은 사용 약관에 동의/거부한 사람에 대한 보고서 보기 및 [Azure AD 감사 로그 보기](#view-azure-ad-audit-logs)를 참조하세요.

**Q: 정보가 얼마나 오래 저장되나요?**<br />
A: 사용 약관 보고서에 있는, 사용 약관에 동의/거부한 사용자 수는 사용 약관의 수명 기간 동안 저장됩니다. Azure AD 감사 로그는 30일 동안 저장됩니다.

**Q: 사용 약관 보고서와 Azure AD 감사 로그에 표시되는 동의 수가 다른 이유는 무엇인가요?**<br />
A: 사용 약관 보고서는 사용 약관 정책의 수명 기간 동안 저장되는 반면, Azure AD 감사 로그는 30일 동안 저장됩니다. 또한 사용 약관 보고서에는 사용자의 현재 동의 상태만 표시됩니다. 예를 들어, 사용자가 거부한 후 동의할 경우, 사용 약관 보고서에는 해당 사용자의 동의만 표시됩니다. 기록을 확인해야 하는 경우, Azure AD 감사 로그를 사용할 수 있습니다.

**Q: 사용 약관 정책 PDF 문서에 하이퍼링크가 있는 경우, 최종 사용자가 하이퍼링크를 클릭할 수 있나요?**<br />
A: 예, 최종 사용자는 추가 페이지에 대한 하이퍼링크를 선택할 수 있지만 문서 내의 섹션에 대한 링크는 지원되지 않습니다. 또한 Azure AD MyApps/MyAccount 포털에서 액세스할 때 사용 약관 정책 PDF의 하이퍼링크가 작동하지 않습니다.

**Q: 사용 약관 정책에서 다중 언어를 지원할 수 있나요?**<br />
A: 예. 현재 관리자가 단일 사용 약관 정책에 대해 구성할 수는 108개의 다른 언어가 있습니다. 관리자는 여러 PDF 문서를 업로드하고 해당 언어를 사용하는 문서에 태그를 지정할 수 있습니다(최대 108개). 최종 사용자가 로그인할 때 브라우저 언어 기본 설정이 확인되고 일치하는 문서가 표시됩니다. 일치 항목이 없으면 업로드된 첫 번째 문서인 기본 문서가 표시됩니다.

**Q: 사용 약관 정책은 언제 트리거되나요?**<br />
A: 사용 약관 정책은 로그인 환경 중에 트리거됩니다.

**Q: 사용 약관 정책을 대상으로 지정할 수 있는 애플리케이션은 무엇인가요?**<br />
A: 최신 인증을 사용하는 엔터프라이즈 애플리케이션에 조건부 액세스 정책을 만들 수 있습니다. 자세한 내용은 [엔터프라이즈 애플리케이션](./../manage-apps/view-applications-portal.md)을 참조하세요.

**Q: 특정 사용자 또는 앱에 여러 사용 약관 정책을 추가할 수 있나요?**<br />
A: 예, 해당 그룹 또는 애플리케이션을 대상으로 하는 조건부 액세스 정책을 여러 개 작성하면 가능합니다. 사용자가 여러 사용 약관 정책 범위에 속하는 경우 한 번에 하나의 사용 약관 정책에 동의합니다.

**Q: 사용자가 사용 약관 정책을 거부하는 경우 어떻게 되나요?**<br />
A: 사용자는 애플리케이션에 액세스할 수 없습니다. 액세스하려면 다시 로그인하여 약관에 동의해야 합니다.

**Q: 이전에 동의한 사용 약관 정책의 동의를 취소할 수 있나요?**<br />
A: [이전에 동의한 사용 약관 정책을 검토](#how-users-can-review-their-terms-of-use)할 수 있지만 현재 동의를 취소할 방법이 없습니다.

**Q: Intune 사용 약관도 사용하면 어떻게 되나요?**<br />
A: Azure AD 사용 약관과 [Intune 약관](/intune/terms-and-conditions-create)을 모두 구성한 경우 사용자는 두 약관에 모두 동의해야 합니다. 자세한 내용은 [조직에 적합한 약관 솔루션 선택 블로그 게시물](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409)을 참조하세요.

**Q: 사용 약관 서비스에서 인증에 사용하는 엔드포인트는 무엇인가요?**<br />
A: 사용 약관은 인증을 위해 엔드포인트 https://tokenprovider.termsofuse.identitygovernance.azure.com 및 https://account.activedirectory.windowsazure.com 을 활용합니다. 조직에서 등록을 위한 URL의 허용 목록을 사용하는 경우 로그인에 대한 Azure AD 엔드포인트와 함께 이러한 엔드포인트를 허용 목록에 추가해야 합니다.

## <a name="next-steps"></a>다음 단계

- [빠른 시작: 클라우드 앱에 액세스하기 전에 사용 약관에 동의하도록 요구](require-tou.md)