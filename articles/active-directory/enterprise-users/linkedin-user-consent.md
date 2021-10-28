---
title: LinkedIn 데이터 공유 및 동의 - Azure Active Directory | Microsoft Docs
description: LinkedIn 통합이 Azure Active Directory에서 Microsoft 앱을 통해 데이터를 공유하는 방법을 설명합니다.
services: active-directory
author: curtand
manager: KarenH444
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9fa37d30ffd96fff41d1d1145238cae16aa5f973
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129985678"
---
# <a name="linkedin-account-connections-data-sharing-and-consent"></a>LinkedIn 계정 연결 데이터 공유 및 동의

Azure AD(Azure Active Directory) 조직의 사용자가 해당 Microsoft 회사 또는 학교 계정을 LinkedIn 계정과 연결하는 데 동의하도록 설정할 수 있습니다. 사용자가 해당 계정을 연결하면 LinkedIn의 정보 및 주요 내용을 일부 Microsoft 앱 및 서비스에서 사용할 수 있습니다. 또한 사용자는 Microsoft의 정보를 사용하여 LinkedIn의 해당 네트워킹 환경이 개선되고 보강되기를 기대할 수 있습니다.

Microsoft 앱 및 서비스에서 LinkedIn 정보를 보려면 사용자가 자신의 Microsoft 및 LinkedIn 계정을 연결하는 데 동의해야 합니다. 사용자가 Outlook, OneDrive 또는 SharePoint Online의 프로필 카드에 대한 다른 사용자의 LinkedIn 정보를 보기 위해 처음으로 클릭할 때 해당 계정에 연결하라는 메시지가 표시됩니다. 사용자가 해당 환경 및 해당 계정에 연결하도록 동의하기 전까지는 LinkedIn 계정 연결을 완전히 사용하지 않습니다.

[!INCLUDE [active-directory-gdpr-note](../../../includes/gdpr-hybrid-note.md)]

## <a name="benefits-of-sharing-linkedin-information"></a>LinkedIn 정보를 공유하는 이점

Microsoft 앱 및 서비스 내에서 LinkedIn 정보에 대한 액세스 권한을 통해 사용자는 조직 내외의 동료, 고객 및 파트너와 전문적인 관계를 쉽게 연결하고, 관여하고, 빌드할 수 있습니다. 새 사용자는 동료와 연결되고, 자세히 알아보고, 자세한 정보에 쉽게 액세스하여 속도를 높일 수 있습니다. Microsoft 앱의 프로필 카드에서 LinkedIn 정보가 표시되는 방법의 예제는 다음과 같습니다.

![조직에서 LinkedIn 통합 사용](./media/linkedin-user-consent/display-example.png)

## <a name="enable-and-announce-linkedin-integration"></a>LinkedIn 통합 사용 및 알림

조직에 대한 설정을 관리하려면 Azure Active Directory 관리자여야 합니다. 모든 사용자 또는 사용자의 특정 집합에 대해 사용할 수 있습니다.

1. 통합을 사용하거나 사용하지 않도록 설정하려면 [Azure AD 조직에 대한 LinkedIn 통합에 동의](linkedin-integration.md)하는 단계를 수행합니다.
2. 조직에서 LinkedIn을 통합한다고 공지하면 사용자에게 [Microsoft 앱 및 서비스의 LinkedIn 정보](https://support.office.com/article/about-linkedin-information-and-features-in-microsoft-apps-and-services-dc81cc70-4d64-4755-9f1c-b9536e34d381)에 대한 FAQ를 알려줍니다. 이 문서에서는 LinkedIn 정보가 표시되는 위치, [데이터 공유 및 개인 정보](https://support.microsoft.com/office/your-data-ae9c08a7-4d06-45b5-a065-320a97bc1400), [계정을 연결하는 방법](https://support.microsoft.com/office/connect-your-linkedin-and-work-or-school-accounts-c7c245f2-fa56-4c9b-ba20-3fceb23c5772) 등에 대한 정보를 제공합니다.

사용자에게 [Linkedin 통합을 통한 데이터 공유 및 개인 정보](https://support.microsoft.com/office/your-data-ae9c08a7-4d06-45b5-a065-320a97bc1400)와 관련된 모든 정보를 제공하는 Linkedin 통합을 공지해야 합니다. 

## <a name="user-consent-for-data-access-in-microsoft-and-linkedin"></a>Microsoft 및 LinkedIn의 데이터 액세스에 대한 사용자 동의

LinkedIn에서 액세스되는 데이터는 Microsoft 서비스에 영구적으로 저장되지 않습니다. 연락처를 제외하고 Microsoft에서 액세스되는 데이터는 LinkedIn에서 영구적으로 저장되지 않습니다. [LinkedIn에서 가져온 연락처 삭제](https://www.linkedin.com/help/linkedin/answer/43377)에 설명된 대로 사용자가 제거할 때까지 Microsoft 연락처를 LinkedIn에 저장합니다.

사용자가 해당 계정을 연결하면 LinkedIn의 정보 및 인사이트는 프로필 카드와 같은 일부 Microsoft 앱에서 사용할 수 있습니다. 또한 사용자는 Microsoft의 정보를 사용하여 LinkedIn의 해당 네트워킹 환경이 개선되고 보강되기를 기대할 수 있습니다.
조직 내 사용자가 자신의 LinkedIn 및 Microsoft 회사 또는 학교 계정을 연결하는 경우 두 가지 옵션이 있습니다.

* 데이터에 두 계정 모두에서 액세스할 수 있는 사용 권한을 제공합니다. 즉, LinkedIn 계정의 데이터에 액세스하도록 Microsoft 또는 회사 계정에 사용 권한을 부여하고 [해당 Microsoft 회사 또는 학교 계정의 데이터에 액세스하도록 해당 LinkedIn 계정에도 사용 권한을 부여합니다](https://www.linkedin.com/help/linkedin/answer/84077).
* Microsoft 회사 및 학교 계정에서 액세스하도록 LinkedIn 데이터에 대해서만 사용 권한을 부여합니다.

사용자는 언제든지 계정의 연결을 끊고, 데이터 액세스 권한을 제거하고, Microsoft 앱에 자신의 프로필 표시 여부를 비롯하여 [고유한 LinkedIn 프로필이 표시되는 방식을 제어할 수 있습니다](https://www.linkedin.com/help/linkedin/answer/83).

### <a name="linkedin-account-data"></a>LinkedIn 계정 데이터

Microsoft 및 LinkedIn 계정을 연결하면 LinkedIn에서 Microsoft에 다음 데이터를 제공할 수 있습니다.

* 프로필 데이터 - LinkedIn ID, 연락처 정보 및 [LinkedIn 프로필](https://www.linkedin.com/help/linkedin/answer/15493)에서 다른 사용자와 공유하는 정보가 포함됩니다.
* 관심 데이터 - 팔로우한 사람 및 항목, 과정 그룹 및 좋아요 및 공유를 표시한 콘텐츠 등 LinkedIn에 대한 관심 사항이 포함됩니다.
* 구독 데이터 - 연결된 데이터와 함께 LinkedIn 애플리케이션 및 서비스에 대한 구독이 포함됩니다. 
* 연결 데이터 - 첫 번째 연결의 프로필 및 연락처 정보를 비롯한 [LinkedIn 네트워크](https://www.linkedin.com/help/linkedin/answer/110)가 포함됩니다.

LinkedIn에서 액세스되는 데이터는 Microsoft 서비스에 영구적으로 저장되지 않습니다. Microsoft에서 사용하는 개인 데이터에 대한 자세한 내용은 [Microsoft 개인정보처리방침](https://privacy.microsoft.com/privacystatement/)을 참조하세요.

### <a name="microsoft-work-or-school-account-data"></a>Microsoft 회사 또는 학교 계정 데이터

Microsoft 및 LinkedIn 계정을 연결하면 Microsoft에서 LinkedIn에 다음 데이터를 제공할 수 있습니다.

* 프로필 데이터 - 이름, 성, 프로필 사진, 이메일 주소, 관리자 및 관리하는 사용자와 같은 정보가 포함됩니다.
* 일정 데이터 - 일정에서 회의, 해당 시간, 위치 및 참석자의 연락처 정보가 포함됩니다. 어젠더, 콘텐츠 또는 회의 제목 등 회의에 대한 정보는 일정 데이터에 포함되지 않습니다.
* 관심 데이터 - 비지니스용 Cortana 및 Bing와 같이 사용하는 Microsoft 서비스에 따라 계정과 연결된 관심 사항이 포함됩니다.
* 구독 데이터 - Microsoft 365와 같은 Microsoft 앱 및 서비스에 대해 조직에서 제공하는 구독이 포함됩니다.
* 연락처 데이터 - 자주 통신하거나 공동 작업하는 사용자의 연락처 정보 등 Outlook, Skype 및 다른 Microsoft 계정 서비스의 연락처 목록이 포함됩니다. 예를 들어 연락처는 연결을 제안하고, 연락처를 구성하고, 연락처에 대한 업데이트를 보여주기 위해 LinkedIn에서 주기적으로 가져오고, 저장되고, 사용됩니다.

연락처를 제외하고 Microsoft에서 액세스되는 데이터는 LinkedIn에서 영구적으로 저장되지 않습니다. 사용자가 제거할 때까지 Microsoft 연락처는 LinkedIn에 저장됩니다. [LinkedIn에서 가져온 연락처 삭제](https://www.linkedin.com/help/linkedin/answer/43377)에 대해 자세히 알아봅니다.

LinkedIn에서 개인 데이터를 사용하는 자세한 내용은 [LinkedIn 개인 정보 취급 방침](https://www.linkedin.com/legal/privacy-policy)을 참조하세요. LinkedIn 서비스, 데이터 전송 및 스토리지의 경우 데이터는 유럽 연합과 미국 간에 이동하고, 개인 정보는 [유럽 연합 데이터 전송](https://www.linkedin.com/help/linkedin/answer/62533)에 설명된 대로 보호됩니다.

## <a name="next-steps"></a>다음 단계

* [회사 또는 학교 계정을 사용한 Microsoft 애플리케이션의 LinkedIn](https://www.linkedin.com/help/linkedin/answer/84077)
