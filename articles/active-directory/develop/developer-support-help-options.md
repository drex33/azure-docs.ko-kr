---
title: Microsoft ID 플랫폼 개발자를 위한 지원 및 도움말 옵션 | Azure
description: Azure AD(Azure Active Directory) 및 Microsoft ID 플랫폼의 기타 구성 요소와 통합되는 IAM(ID 및 액세스 관리) 솔루션을 빌드할 때 어디에서 지원을 받고 질문에 대한 답변을 찾을 수 있는지에 대해 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/28/2021
ms.author: marsma
ms.reviewer: jmprieur, saeeda
ms.custom: has-adal-ref
ms.openlocfilehash: b99b69b403603d347dfd49fd97de580a3871e799
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131466170"
---
# <a name="support-and-help-options-for-developers"></a>개발자를 위한 지원 및 도움말 옵션

질문에 대한 답변이 필요하거나 설명서에 포함되지 않은 문제를 해결하는 데 도움이 필요하면 전문가에게 문의하여 도움을 받을 수 있습니다. Microsoft ID 플랫폼과 통합되는 애플리케이션을 개발할 때 질문에 대한 답을 얻을 수 있는 몇 가지 제안 사항은 다음과 같습니다.

## <a name="create-an-azure-support-request"></a>Azure 지원 요청 만들기

<div class='icon is-large'>
    <img alt='Azure support' src='./media/common/logo_azure.svg'>
</div>

다양한 [Azure 지원 옵션](https://azure.microsoft.com/support/plans)을 살펴보고 가장 적합한 계획을 선택합니다. Azure Portal에서 지원 요청을 만들고 관리하는 두 가지 옵션이 있습니다.

- Azure 지원 플랜이 이미 있는 경우 [여기에서 지원 요청을 엽니다](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

- Azure 고객이 아닌 경우에는 [비즈니스용 Microsoft 지원](https://support.serviceshub.microsoft.com/supportforbusiness)을 통해 지원 요청을 열 수 있습니다.

## <a name="post-a-question-to-microsoft-qa"></a>Microsoft Q&A에 질문 게시

<div class='icon is-large'>
    <img alt='Microsoft Q&A' src='./media/common/question-mark-icon.png'>
</div>

Microsoft 엔지니어, Azure MVP(Most Valuable Professional), 전문가 커뮤니티의 구성원으로부터 직접 ID 앱 개발과 관련된 질문에 대한 답변을 받을 수 있습니다.

[Microsoft Q&A](/answers/products/)는 Azure에서 권장하는 커뮤니티 지원 제공 장소입니다.

Microsoft Q&A에서 검색하여 문제에 대한 답변을 찾을 수 없는 경우 새 질문을 제출합니다. [높은 수준의 질문](/answers/articles/24951/how-to-write-a-quality-question.html)을 하기 위해 다음 태그를 사용할 수 있습니다.

| 구성 요소/영역                                                              | 태그들                                                                    |
| --------------------------------------------------------------------------- | ----------------------------------------------------------------------- |
| MSAL(Microsoft 인증 라이브러리)                                     | [[msal]](/answers/topics/azure-ad-msal.html)                            |
| OWIN(Open Web Interface for .NET) 미들웨어                               | [[azure-active-directory]](/answers/topics/azure-active-directory.html) |
| [Azure AD B2B/외부 ID](../external-identities/what-is-b2b.md) | [[azure-ad-b2b]](/answers/topics/azure-ad-b2b.html)                     |
| [Azure AD B2C](https://azure.microsoft.com/services/active-directory-b2c/)  | [[azure-ad-b2c]](/answers/topics/azure-ad-b2c.html)                     |
| [Microsoft Graph API](https://developer.microsoft.com/graph/)               | [[azure-ad-graph]](/answers/topics/azure-ad-graph.html)                 |
| 기타 모든 인증 및 권한 부여 영역                            | [[azure-active-directory]](/answers/topics/azure-active-directory.html) |

## <a name="create-a-github-issue"></a>GitHub 문제 만들기

<div class='icon is-large'>
    <img alt='GitHub-image' src='./media/common/github.svg'>
</div>

MSAL(Microsoft 인증 라이브러리)에 대한 도움이 필요한 경우 GitHub의 리포지토리에서 문제를 엽니다.

| MSAL                   | GitHub 문제 URL                                                              |
| ---------------------- | ------------------------------------------------------------------------------ |
| Android용 MSAL       | https://github.com/AzureAD/microsoft-authentication-library-for-android/issues |
| MSAL Angular           | https://github.com/AzureAD/microsoft-authentication-library-for-js/issues      |
| iOS 및 macOS용 MSAL | https://github.com/AzureAD/microsoft-authentication-library-for-objc/issues    |
| MSAL Java              | https://github.com/AzureAD/microsoft-authentication-library-for-java/issues    |
| MSAL.js                | https://github.com/AzureAD/microsoft-authentication-library-for-js/issues      |
| MSAL.NET               | https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues  |
| MSAL 노드              | https://github.com/AzureAD/microsoft-authentication-library-for-js/issues      |
| MSAL Python            | https://github.com/AzureAD/microsoft-authentication-library-for-python/issues  |
| MSAL 반응              | https://github.com/AzureAD/microsoft-authentication-library-for-js/issues      |

## <a name="stay-informed-of-updates-and-new-releases"></a>업데이트 및 새 릴리스의 최신 정보 수신

<div class='icon is-large'>
    <img alt='Stay informed' src='./media/common/i_blog.svg'>
</div>

- [Azure 업데이트](https://azure.microsoft.com/updates/?category=identity): 중요한 제품 업데이트, 로드맵, 공지에 대해 알아봅니다.

- [문서의 새로운 항목](./whats-new-docs.md): Microsoft ID 플랫폼 설명서의 새로운 항목에 대해 알아봅니다.

- [Azure Active Directory ID 블로그](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/bg-p/Identity): Azure AD에 대한 뉴스 및 정보를 확인합니다.

- [기술 커뮤니티](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/bg-p/Identity/): 경험을 공유하고 참여하여 전문가로부터 배웁니다.
