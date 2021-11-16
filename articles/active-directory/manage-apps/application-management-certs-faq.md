---
title: 애플리케이션 관리 인증서 질문과 대답
titleSuffix: Azure AD
description: IdP(ID 공급자)로 Azure Active Directory를 사용하여 앱의 인증서 관리에 대해 FAQ(질문과 대답)에 대한 답변을 알아봅니다.
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: reference
ms.date: 03/19/2021
ms.author: davidmu
ms.reviewer: sureshja, saumadan
ms.openlocfilehash: 3f94c7eae439303f2954b8ab62f499b789902e79
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129619940"
---
# <a name="azure-active-directory-application-management-certificates-frequently-asked-questions"></a>Azure Active Directory 애플리케이션 관리 인증서 질문과 대답

이 페이지에서는 IdP(ID 공급자)로 Azure AD(Azure Active Directory)를 사용하여 앱의 인증서 관리에 대한 FAQ(질문과 대답)에 답변합니다.

## <a name="is-there-a-way-to-generate-a-list-of-expiring-saml-signing-certificates"></a>만료되는 SAML 서명 인증서 목록을 생성하는 방법이 있나요?

만료된 비밀, 인증서 및 지정된 앱에 대한 해당 소유자를 포함하는 모든 앱 등록을 [PowerShell 스크립트](app-management-powershell-samples.md)를 통해 CSV 파일의 디렉터리에서 내보낼 수 있습니다.

## <a name="where-can-i-find-the-information-about-soon-to-expire-certificates-renewal-steps"></a>인증서 갱신 단계를 곧 만료하기 위한 정보는 어디에서 확인할 수 있나요?

단계는 [여기](manage-certificates-for-federated-single-sign-on.md#renew-a-certificate-that-will-soon-expire)서 찾을 수 있습니다.

## <a name="how-can-i-customize-the-expiration-date-for-the-certificates-issued-by-azure-ad"></a>Azure AD에서 발급한 인증서의 만료 날짜를 사용자 지정하려면 어떻게 해야 하나요?

기본적으로 Azure AD는 SAML Single Sign-On 구성 중에 자동으로 만들어지는 인증서로 3년 후에 만료되도록 구성합니다. 인증서를 저장한 후에는 인증서의 날짜를 변경할 수 없으므로 새 인증서를 만들어야 합니다. 이 작업을 수행하는 방법에 대한 단계는 [페더레이션 인증서의 만료 날짜 사용자 지정 및 새 인증서 롤오버](manage-certificates-for-federated-single-sign-on.md#customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate)를 참조하세요.

## <a name="how-can-i-automate-the-certificates-expiration-notifications"></a>인증서 만료 알림을 자동화하려면 어떻게 해야 하나요?

Azure AD에서는 SAML 인증서가 만료되기 60일, 30일 및 7일 전에 이메일 알림을 보냅니다. 알림을 받을 이메일 주소를 둘 이상 추가할 수 있습니다.

> [!NOTE]
> 알림 목록에 최대 5개의 이메일 주소를 추가할 수 있습니다(애플리케이션을 추가한 관리자의 이메일 주소 포함). 더 많은 사용자에게 알림이 필요한 경우 배포 목록 이메일을 사용합니다.

알림을 보낼 이메일을 지정하려면 [인증서 만료에 대한 이메일 알림 주소 추가](manage-certificates-for-federated-single-sign-on.md#add-email-notification-addresses-for-certificate-expiration)를 참조하세요.

`aadnotification@microsoft.com`에서 받은 이러한 이메일 알림을 편집하거나 사용자 지정하는 옵션은 없습니다. 그러나 [PowerShell 스크립트](app-management-powershell-samples.md)를 통해 암호 및 인증서가 만료된 앱 등록을 내보낼 수 있습니다.

## <a name="who-can-update-the-certificates"></a>인증서를 업데이트할 수 있는 사람은 누구인가요?

애플리케이션의 소유자 또는 전역 관리자 또는 애플리케이션 관리자는 Azure Portal UI, PowerShell 또는 Microsoft Graph를 통해 인증서를 업데이트할 수 있습니다.

## <a name="i-need-more-details-about-certificate-signing-options"></a>인증서 서명 옵션에 대한 자세한 내용이 필요합니다.

Azure AD에서 인증서 서명 옵션과 인증서 서명 알고리즘을 설정할 수 있습니다. 자세히 알아보려면 [Azure AD 앱에 대한 고급 SAML 토큰 인증서 서명 옵션](certificate-signing-options.md)을 참조하세요.

## <a name="i-need-to-replace-the-certificate-for-azure-ad-application-proxy-applications-and-need-more-instructions"></a>Azure AD 애플리케이션 프록시에 대한 인증서를 바꾸어야 하며 추가 지침이 필요합니다.

Azure AD 애플리케이션 프록시 애플리케이션에 대한 인증서를 바꾸려면 [PowerShell 샘플 - 애플리케이션 프록시 앱의 인증서 바꾸기](../app-proxy/scripts/powershell-get-custom-domain-replace-cert.md)를 참조하세요.

## <a name="how-do-i-manage-certificates-for-custom-domains-in-azure-ad-application-proxy"></a>Azure AD 애플리케이션 프록시의 사용자 지정 도메인에 대한 인증서를 관리하려면 어떻게 해야 하나요?

사용자 지정 도메인을 사용하도록 온-프레미스 앱을 구성하려면 확인된 Azure Active Directory 사용자 지정 도메인, 사용자 지정 도메인에 대한 PFX 인증서 및 구성할 온-프레미스 앱이 필요합니다. 자세히 알아보려면 [Azure AD 애플리케이션 프록시에서 사용자 지정 도메인 작업](../app-proxy/application-proxy-configure-custom-domain.md)을 참조하세요.

## <a name="i-need-to-update-the-token-signing-certificate-on-the-application-side-where-can-i-get-it-on-azure-ad-side"></a>애플리케이션 쪽에서 토큰 서명 인증서를 업데이트해야 합니다. Azure AD 쪽의 어디에서 얻을 수 있나요?

SAML X.509 인증서를 갱신할 수 있습니다. [SAML 서명 인증서](configure-saml-single-sign-on.md#saml-signing-certificate)를 참조하세요.

## <a name="what-is-azure-ad-signing-key-rollover"></a>Azure AD 서명 키 롤오버란?

자세한 내용은 [여기](../develop/active-directory-signing-key-rollover.md)에서 볼 수 있습니다.

## <a name="how-do-i-renew-application-token-encryption-certificate"></a>애플리케이션 토큰 암호화 인증서를 갱신하려면 어떻게 해야 하나요?

애플리케이션 토큰 암호화 인증서를 갱신하려면 [엔터프라이즈 애플리케이션에 대한 토큰 암호화 인증서를 갱신하는 방법](howto-saml-token-encryption.md)을 참조하세요.

## <a name="how-do-i-renew-application-token-signing-certificate"></a>애플리케이션 토큰 서명 인증서를 갱신하려면 어떻게 해야 하나요?

애플리케이션 토큰 서명 인증서를 갱신하려면 [엔터프라이즈 애플리케이션에 대한 토큰 서명 인증서를 갱신하는 방법](manage-certificates-for-federated-single-sign-on.md)을 참조하세요.

## <a name="how-do-i-update-azure-ad-after-changing-my-federation-certificates"></a>내 페더레이션 인증서를 변경한 후 Azure AD를 업데이트하려면 어떻게 해야 하나요?

페더레이션 인증서를 변경한 후 Azure AD를 업데이트하려면 [Microsoft 365 및 Azure Active Directory에 대한 페더레이션 인증서 갱신](../hybrid/how-to-connect-fed-o365-certs.md)을 참조하세요.
