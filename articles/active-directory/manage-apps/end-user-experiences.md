---
title: 애플리케이션에 대한 최종 사용자 환경
titleSuffix: Azure AD
description: Azure AD(Azure Active Directory)는 조직의 최종 사용자에게 애플리케이션을 배포하는 몇 가지 사용자 지정 가능한 방법을 제공합니다.
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: davidmu
ms.reviewer: lenalepa
ms.openlocfilehash: 9480370a263c58984277d652965f099ac5951bd3
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129617761"
---
# <a name="end-user-experiences-for-applications-in-azure-active-directory"></a>Azure Active Directory에서 애플리케이션에 대한 최종 사용자 환경

Azure AD(Azure Active Directory)는 조직의 최종 사용자에게 애플리케이션을 배포하는 몇 가지 사용자 지정 가능한 방법을 제공합니다.

* Azure AD 내 앱
* Microsoft 365 애플리케이션 시작 관리자
* 페더레이션된 앱에 직접 로그온
* 페더레이션된 앱, 암호로 보호된 앱 또는 기존 앱에 대한 딥 링크

조직에 배포할 방법을 선택하는 것은 여러분의 몫입니다.

## <a name="azure-ad-my-apps"></a>Azure AD 내 앱

<https://myapps.microsoft.com> 의 내 앱은 최종 사용자가 Azure Active Directory의 조직 계정을 사용하여 Azure AD 관리자가 액세스 권한을 부여한 애플리케이션을 보고 시작할 수 있도록 하는 웹 기반 포털입니다. [Azure Active Directory Premium](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing)의 최종 사용자는 내 앱을 통해 셀프 서비스 그룹 관리 기능을 활용할 수도 있습니다.

기본적으로 모든 애플리케이션이 단일 페이지에 함께 나열됩니다. 그러나 컬렉션을 사용하여 관련 애플리케이션을 그룹화하고 별도의 탭에 표시하여 더 쉽게 찾을 수 있습니다. 예를 들어 컬렉션을 사용하여 특정 작업 역할, 작업, 프로젝트 등에 대해 애플리케이션의 논리적 그룹을 만들 수 있습니다. 자세한 내용은 [내 앱 포털에서 컬렉션 만들기](access-panel-collections.md)를 참조하세요.

내 앱은 Azure Portal과 별개이며, 사용자에게 Azure 구독이나 Microsoft 365 구독이 필요하지 않습니다.

Azure AD 내 앱에 대한 자세한 내용은 [내 앱 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.

## <a name="microsoft-365-application-launcher"></a>Microsoft 365 애플리케이션 시작 관리자

Microsoft 365를 배포한 조직에서 Azure AD를 통해 사용자에게 할당된 애플리케이션은 Office 365 포털([https://portal.office.com/myapps](https://portal.office.com/myapps))에도 표시됩니다. 이렇게 하면 조직의 사용자가 두 번째 포털을 사용하지 않고도 해당 앱을 쉽고 편리하게 시작할 수 있으므로 Microsoft 365를 사용하는 조직에 권장되는 앱 시작 솔루션입니다.

Office 365 애플리케이션 실행 프로그램에 대한 자세한 내용은 [Office 365 앱 실행 프로그램에 해당 앱 표시](/previous-versions/office/office-365-api/)(영문)를 참조하세요.

## <a name="direct-sign-on-to-federated-apps"></a>페더레이션된 앱에 직접 로그온

SAML 2.0, WS-Federation 또는 OpenID Connect를 지원하는 대부분의 페더레이션된 애플리케이션은 사용자가 애플리케이션에서 시작한 다음, 자동 리디렉션을 통하거나 로그인 링크를 클릭하여 Azure AD로 로그인하는 기능을 지원합니다. 이것을 서비스 공급자에서 시작한 로그온이라고 하며, Azure AD 애플리케이션 갤러리에서 페더레이션된 대부분의 애플리케이션이 이 기능을 지원합니다(자세한 내용은 Azure Portal에 있는 앱의 Single Sign-On 구성 마법사에서 연결된 설명서 참조).

## <a name="direct-sign-on-links"></a>직접 로그온 링크

Azure AD는 암호 기반 Single Sign-On, 연결된 Single Sign-On 및 모든 형태의 페더레이션된 Single Sign-On을 지원하는 개별 애플리케이션에 대한 직접 Single Sign-On 링크도 지원합니다.

이러한 링크는 사용자가 Azure AD 내 앱이나 Microsoft 365에서 시작하지 않아도 Azure AD 로그인 프로세스를 통해 사용자를 특정 애플리케이션으로 보내는, 특별히 제작된 URL입니다. 이러한 **사용자 액세스 URL** 은 사용 가능한 엔터프라이즈 애플리케이션의 속성에서 찾을 수 있습니다. Azure Portal에서 **Azure Active Directory** > **엔터프라이즈 애플리케이션** 을 차례로 선택합니다. 애플리케이션을 선택한 다음, **속성** 을 선택합니다.

![Twitter 속성의 사용자 액세스 URL 예](media/end-user-experiences/direct-sign-on-link.png)

이러한 링크는 복사하여 선택한 애플리케이션에 대한 로그인 링크를 제공할 위치에 붙여 넣을 수 있습니다. 이 링크는 전자 메일 또는 사용자 애플리케이션 액세스에 대해 설정한 사용자 지정 웹 기반 포털에 있을 수 있습니다. 다음은 Twitter에 대한 Azure AD Single Sign-On URL의 예제입니다.

`https://myapps.microsoft.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

내 앱에 대한 조직 관련 URL과 마찬가지로 *myapps.microsoft.com* 도메인 뒤의 디렉터리에 대해 활성 또는 확인된 도메인 중 하나를 추가하여 이 URL을 추가로 사용자 지정할 수 있습니다. 이렇게 하면 사용자가 자신의 ID를 먼저 입력하지 않아도 조직 브랜딩이 로그인 페이지에 즉시 로드됩니다.

`https://myapps.microsoft.com/contosobuild.com/signin/Twitter/230848d52c8745d4b05a60d29a40fced`

권한이 있는 사용자가 이러한 애플리케이션 관련 링크 중 하나를 클릭하면 조직의 로그인 페이지가 먼저 표시되며(아직 로그인하지 않았다고 가정), 로그인 과정이 내 앱에서 먼저 중지되지 않고 해당 앱으로 리디렉션됩니다. 사용자에게 암호 기반 Single Sign-On 브라우저 확장과 같은, 애플리케이션에 액세스하기 위한 필수 구성 요소가 없는 경우 확장을 설치할 것인지 묻는 링크가 표시됩니다. 애플리케이션에 대한 Single Sign-On 구성이 변경되는 경우에도 링크 URL은 그대로 유지됩니다.

이러한 링크는 내 앱 및 Microsoft 365와 동일한 액세스 제어 메커니즘을 사용하며, Azure Portal에서 애플리케이션에 할당된 해당 사용자나 그룹만 성공적으로 인증할 수 있습니다. 그러나 권한이 없는 사용자에게는 액세스 권한이 부여되지 않았음을 설명하는 메시지가 표시되고, 액세스할 수 있는 사용 가능한 애플리케이션을 볼 수 있는 내 앱 로드에 대한 링크가 제공됩니다.

## <a name="next-steps"></a>다음 단계

* [애플리케이션 관리에 대한 빠른 시작 시리즈](view-applications-portal.md)
* [Single Sign-On이란?](what-is-single-sign-on.md)
* [애플리케이션과 Azure Active Directory 통합 시작 가이드](plan-an-application-integration.md)