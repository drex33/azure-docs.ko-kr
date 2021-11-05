---
title: 워크로드 ID 페더레이션
titleSuffix: Microsoft identity platform
description: 워크로드 ID 페더레이션을 사용하여 Azure 외부에서 실행되는 워크로드에 비밀 또는 인증서를 사용하지 않고 Azure AD 보호 리소스에 대한 액세스 권한을 부여합니다. 이렇게 하면 개발자가 Azure 외부에서 수명이 긴 비밀 또는 인증서를 저장하고 유지 관리하지 않아도 됩니다.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/20/2021
ms.author: ryanwi
ms.reviewer: keyam, udayh, vakarand
ms.custom: aaddev
ms.openlocfilehash: ff1161e41965c92b469372038c4ba0cd602ee074
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131102679"
---
# <a name="workload-identity-federation-preview"></a>워크로드 ID 페더레이션(미리 보기)
이 문서에서는 소프트웨어 워크로드에 대한 워크로드 ID 페더레이션의 개요를 제공합니다. 워크로드 ID 페더레이션을 사용하면 비밀(지원되는 시나리오의 경우)을 관리할 필요 없이 Azure AD(Azure Active Directory) 보호 리소스에 액세스할 수 있습니다.

GitHub Actions과 같은 시나리오에서 워크로드 ID 페더레이션을 사용할 수 있습니다.

## <a name="why-use-workload-identity-federation"></a>워크로드 ID 페더레이션을 사용하는 이유

일반적으로 소프트웨어 워크로드(예: 애플리케이션, 서비스, 스크립트 또는 컨테이너 기반 애플리케이션)는 리소스를 인증하고 액세스하거나 다른 서비스와 통신하기 위해 ID가 필요합니다.  이러한 워크로드가 Azure에서 실행되면 관리 ID를 사용할 수 있으며 Azure 플랫폼에서 자격 증명을 관리합니다.  Azure 외부에서 실행되는 소프트웨어 워크로드의 경우 애플리케이션 자격 증명(비밀 또는 인증서)을 사용하여 Azure AD 보호 리소스(예: Azure, Microsoft Graph, Microsoft 365 또는 타사 리소스)에 액세스해야 합니다.  이러한 자격 증명은 보안 위험을 초래하며 안전하게 저장되고 정기적으로 회전해야 합니다. 또한 자격 증명이 만료될 경우 서비스 가동 중지 시간이 발생할 위험이 있습니다.

워크로드 ID 페더레이션을 사용하여 GitHub 같은 IdP(외부 ID 공급자)의 토큰을 신뢰하도록 Azure AD 앱 등록을 구성합니다.  신뢰 관계가 만들어지면 소프트웨어 워크로드는 외부 IdP의 신뢰할 수 있는 토큰을 Microsoft ID 플랫폼의 액세스 토큰으로 교환할 수 있습니다.  그러면 소프트웨어 워크로드가 해당 액세스 토큰을 사용하여 워크로드에 액세스 권한이 부여된 Azure AD 보호 리소스에 액세스합니다. 이로써 자격 증명을 수동으로 관리하는 유지 관리 부담이 제거되고 비밀이 유출되거나 인증서가 만료될 위험이 없습니다.

## <a name="supported-scenarios"></a>지원되는 시나리오

워크로드 ID 페더레이션을 사용하여 Azure AD 보호 리소스에 액세스하기 위해 지원되는 시나리오는 다음과 같습니다.

- GitHub Actions. 먼저 Azure AD의 앱과 Azure Portal 또는 Microsoft Graph 사용하여 GitHub 리포지토리 간에 [트러스트 관계를 구성](workload-identity-federation-create-trust-github.md)합니다. Microsoft ID 공급자에서 액세스 토큰을 얻고 Azure 리소스에 액세스하도록 [GitHub Actions 워크플로를 구성](/azure/developer/github/connect-from-azure)합니다.
- Azure 외부의 컴퓨팅 플랫폼에서 실행되는 워크로드. Azure AD 애플리케이션 등록과 컴퓨팅 플랫폼의 외부 IdP 간에 [신뢰 관계를 구성](workload-identity-federation-create-trust.md)합니다. 해당 플랫폼에서 발급한 토큰을 사용하여 Microsoft 에코시스템에서 Microsoft ID 플랫폼을 인증하고 API를 호출할 수 있습니다. 클라이언트 [자격 증명 흐름](v2-oauth2-client-creds-grant-flow.md#third-case-access-token-request-with-a-federated-credential)을 사용하여 저장된 인증서를 사용하여 직접 만드는 대신 ID 공급자의 JWT를 전달하여 Microsoft ID 플랫폼 액세스 토큰을 얻습니다.

## <a name="how-it-works"></a>작동 방식
[페더레이션 ID 자격 증명](/graph/api/resources/federatedidentitycredentials-overview?view=graph-rest-beta&preserve-view=true)을 구성하여 외부 IdP와 Azure AD의 앱 간에 신뢰 관계를 만듭니다. 페더레이션 ID 자격 증명은 애플리케이션에서 신뢰할 수 있어야 하는 외부 IdP의 토큰을 나타내는 데 사용됩니다. Azure Portal 또는 Microsoft Graph를 통한 앱 등록에서 페더레이션 ID 자격 증명을 구성합니다.  신뢰 관계를 구성하는 단계는 시나리오 및 외부 IdP에 따라 달라집니다.

그러나 액세스 토큰에 대한 외부 토큰을 교환하는 워크플로는 모든 시나리오에서 동일합니다. 다음 다이어그램에서는 액세스 토큰에 대한 외부 토큰을 교환한 다음 Azure AD 보호 리소스에 액세스하는 워크로드의 일반적인 워크플로를 보여줍니다.

:::image type="content" source="media/workload-identity-federation/workflow.svg" alt-text="액세스 토큰 및 Azure에 액세스하기 위해 교환된 외부 토큰을 표시합니다" border="false":::

1. 외부 워크로드(예: GitHub Actions 워크플로)는 외부 IdP(예: GitHub)에서 토큰을 요청합니다.
1. 외부 IdP는 외부 워크로드에 토큰을 발행합니다.
1. 외부 워크로드(예: GitHub 워크플로의 로그인 작업)는 [Microsoft ID 플랫폼에 토큰을 보내고](v2-oauth2-client-creds-grant-flow.md#third-case-access-token-request-with-a-federated-credential) 액세스 토큰을 요청합니다.
1. Microsoft ID 플랫폼은 앱 등록에서 [신뢰 관계](workload-identity-federation-create-trust.md)를 확인하고 외부 IdP의 OIDC(Open ID Connect) 발급자 URL에 대해 외부 토큰의 유효성을 검사합니다.
1. 검사가 충족되면 Microsoft ID 플랫폼은 외부 워크로드에 액세스 토큰을 발행합니다.
1. 외부 워크로드는 Microsoft ID 플랫폼의 액세스 토큰을 사용하여 Azure AD 보호 리소스에 액세스합니다. 예를 들어 GitHub Actions 워크플로는 액세스 토큰을 사용하여 웹앱을 Azure App Service에 게시합니다.

## <a name="next-steps"></a>다음 단계
워크로드 ID 페더레이션의 작동 방식에 대해 자세히 알아보세요.
- Azure AD에서 [OAuth 2.0 클라이언트 자격 증명 부여](v2-oauth2-client-creds-grant-flow.md#third-case-access-token-request-with-a-federated-credential) 및 다른 IdP에서 발급한 클라이언트 어설션을 사용하여 토큰을 얻는 방법입니다.
- Microsoft Graph 사용하여 앱 등록에서 [페더레이션 ID 자격 증명](/graph/api/resources/federatedidentitycredentials-overview?view=graph-rest-beta&preserve-view=true)을 생성, 삭제, 가져오기 또는 업데이트하는 방법입니다.
- Microsoft ID 공급자에서 액세스 토큰을 얻고 Azure 리소스에 액세스하도록 GitHub Actions 워크플로를 구성하는 방법에 대한 자세한 내용은 [GitHub Actions 설명서](https://docs.github.com/actions/deployment/security-hardening-your-deployments/configuring-openid-connect-in-azure)를 참조하세요.
