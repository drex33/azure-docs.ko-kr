---
title: '애플리케이션 관리: 모범 사례 및 권장 사항'
titleSuffix: Azure AD
description: Azure Active Directory에서 애플리케이션을 관리하기 위한 모범 사례와 권장 사항을 알아봅니다. 애플리케이션 프록시를 사용하여 온-프레미스 앱을 자동으로 프로비전 및 게시하는 방법을 알아봅니다.
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/13/2019
ms.subservice: app-mgmt
ms.author: davidmu
ms.collection: M365-identity-device-management
ms.reviewer: napuri
ms.openlocfilehash: 0dc4edc86065a0569f933f92990cec86d7dada2b
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129615014"
---
# <a name="application-management-best-practices-in-azure-active-directory"></a>Azure Active Directory의 애플리케이션 관리 모범 사례

이 문서에는 Azure AD(Azure Active Directory)에서의 애플리케이션 관리, 자동 프로비전 사용, 애플리케이션 프록시를 사용한 온-프레미스 앱 게시에 대한 권장 사항과 모범 사례가 포함되어 있습니다.

## <a name="cloud-app-and-single-sign-on-recommendations"></a>클라우드 앱 및 Single Sign-On 권장 사항

| 권장 | 주석 |
| --- | --- |
| 앱에 대한 Azure AD 애플리케이션 갤러리 확인  | Azure AD에는 Enterprise SSO(Single Sign-On)가 설정된 수천 개의 사전 통합 애플리케이션이 들어 있는 갤러리가 있습니다. 앱별 설정 지침은 [SaaS 앱 자습서 목록](../saas-apps/tutorial-list.md)을 참조하세요.  |
| 페더레이션된 SAML 기반 SSO 사용  | 애플리케이션에서 지원하는 경우 암호 기반 SSO 및 ADFS 대신 Azure AD에서 페더레이션된 SAML 기반 SSO를 사용합니다.  |
| 인증서 서명에 SHA-256 사용  | Azure AD에서는 기본적으로 SHA-256 알고리즘을 사용하여 SAML 응답에 서명합니다. 애플리케이션에 SHA(Secure Hash Algorithm) 1이 필요하지 않은 경우 SHA-256을 사용합니다([인증서 서명 옵션](certificate-signing-options.md) 및 [애플리케이션 로그인 문제](application-sign-in-problem-application-error.md) 참조).  |
| 사용자 할당 필요  | 기본적으로 사용자는 엔터프라이즈 애플리케이션에 할당되지 않아도 해당 애플리케이션에 액세스할 수 있습니다. 그러나 애플리케이션이 역할을 노출하거나 애플리케이션을 사용자의 내 앱에 표시하려는 경우 사용자 할당이 필요합니다.  |
| 내 앱을 사용자에게 배포 | `https://myapps.microsoft.com`의 [내 앱](end-user-experiences.md)은 사용자에게 본인의 할당 클라우드 기반 애플리케이션에 대한 단일 진입점을 제공하는 웹 기반 포털입니다. 그룹 관리 및 셀프 서비스 암호 재설정과 같은 추가 기능이 추가되면 사용자는 내 앱에서 해당 기능을 찾을 수 있습니다. [내 앱 배포 계획](my-apps-deployment-plan.md)을 참조하세요.
| 그룹 할당 사용  | 구독에 포함된 경우 그룹 소유자에게 지속적 액세스 관리를 위임할 수 있도록 애플리케이션에 그룹을 할당합니다.  |
| 인증서 관리 프로세스를 설정합니다. | 서명 인증서의 최대 수명은 3년입니다. 인증서 만료에 따른 중단 문제를 예방하거나 최소화하려면 역할 및 이메일 배포 목록을 사용하여 인증서 관련 변경 알림이 면밀히 모니터링되는지 확인합니다. |

## <a name="provisioning-recommendations"></a>프로비전 권장 사항

| 권장 | 주석 |
| --- | --- |
| 자습서를 사용하여 클라우드 앱으로 프로비전 설정 | 추가할 갤러리 앱의 프로비전 구성에 대한 단계별 지침은 [SaaS 앱 자습서 목록](../saas-apps/tutorial-list.md)을 참조하세요. |
| 프로비전 로그(미리 보기)를 사용하여 상태 모니터링 | [프로비전 로그](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)는 개별 사용자의 상태를 포함하여 프로비전 서비스에서 수행되는 모든 작업에 대한 세부 정보를 제공합니다. |
| 프로비전 알림 이메일에 배포 목록 할당 | 프로비전 서비스에서 보내는 중요한 알림의 가시성을 높이려면 배포 목록을 알림 이메일 설정에 할당합니다. |

## <a name="application-proxy-recommendations"></a>애플리케이션 프록시 권장 사항

| 권장 | 주석 |
| --- | --- |
| 내부 리소스에 대한 원격 액세스에 애플리케이션 프록시 사용 | 애플리케이션 프록시는 원격 사용자에게 내부 리소스에 대한 액세스 권한을 부여하여 VPN 또는 역방향 프록시에 대한 필요성을 대체하는 데 권장됩니다. 대기 시간이 길어질 수 있으므로 회사 네트워크 내에서 리소스에 액세스하는 용도는 아닙니다.
| 사용자 지정 도메인 사용 | 사용자의 URL과 애플리케이션 간의 URL이 네트워크 내부 또는 외부에서 작동하도록 애플리케이션의 사용자 지정 도메인을 설정합니다([사용자 지정 도메인 구성](../app-proxy/application-proxy-configure-custom-domain.md) 참조). 또한 브랜딩을 제어하고 URL을 사용자 지정할 수도 있습니다.  사용자 지정 도메인 이름을 사용하는 경우 Microsoft 외의 신뢰할 수 있는 인증 기관에서 공용 인증서를 획득하도록 계획합니다. Azure 애플리케이션 프록시는 표준, ([와일드카드](../app-proxy/application-proxy-wildcard.md)) 또는 SAN 기반 인증서를 지원합니다. ([애플리케이션 프록시 계획](../app-proxy/application-proxy-deployment-plan.md)을 참조하세요.) |
| 애플리케이션 프록시 배포 전 사용자 동기화 | 애플리케이션 프록시를 배포하기 전에 온-프레미스 디렉터리에서 사용자 ID를 동기화하거나 Azure AD에서 직접 만들어야 합니다. ID 동기화를 사용하면 Azure AD가 사용자에게 App Proxy 게시 애플리케이션에 대한 액세스 권한을 부여하기 전에 미리 인증할 수 있습니다. 또한 SSO(Single Sign-On)를 수행하는 데 필요한 사용자 ID 정보를 확인할 수도 있습니다. ([애플리케이션 프록시 계획](../app-proxy/application-proxy-deployment-plan.md)을 참조하세요.) |
| 고가용성 및 부하 분산을 위한 팁 따르기 | 사용자, 애플리케이션 프록시 커넥터, 백 엔드 앱 서버 간에 트래픽이 흐르는 방식을 알아보고 성능과 부하 분산의 최적화 팁을 확인하려면 [애플리케이션 프록시 커넥터와 애플리케이션의 고가용성 및 부하 분산](../app-proxy/application-proxy-high-availability-load-balancing.md)을 참조하세요. |
| 여러 커넥터 사용 | 복원력, 가용성, 규모를 키울 수 있도록 애플리케이션 프록시 커넥터를 2개 이상 사용합니다([애플리케이션 프록시 커넥터](../app-proxy/application-proxy-connectors.md) 참조). 커넥터 그룹을 만들고 각 커넥터 그룹에 커넥터가 2개 이상 있는지 확인합니다(커넥터 3개가 최적임). |
| 애플리케이션 서버에 가까운 커넥터 서버를 찾아 동일한 도메인에 있는지 확인합니다. | 성능을 최적화하려면 애플리케이션 서버와 가까운 커넥터 서버를 물리적으로 찾습니다([네트워크 토폴로지 고려 사항](../app-proxy/application-proxy-network-topology.md) 참조). 또한 커넥터 서버와 웹 애플리케이션 서버는 동일한 Active Directory 도메인에 속하거나 트러스팅 도메인에 걸쳐 있어야 합니다. 이 구성은 IWA(Windows 통합 인증) 및 KCD(Kerberos 제한 위임)를 사용하는 SSO에 필요합니다. 서버가 서로 다른 도메인에 있는 경우 SSO에 대한 리소스 기반 위임을 사용해야 합니다([애플리케이션 프록시를 사용하는 Single Sign-On의 KCD](../app-proxy/application-proxy-configure-single-sign-on-with-kcd.md) 참조). |
| 커넥터에 대한 자동 업데이트 사용 | 최신 기능 및 버그 수정을 위해 커넥터에 대해 자동 업데이트를 사용하도록 설정합니다. Microsoft에서는 최신 커넥터 버전 및 이전 버전을 직접 지원합니다. [애플리케이션 프록시 릴리스 버전 기록](../app-proxy/application-proxy-release-version-history.md)을 참조하세요. |
| 온-프레미스 프록시 바이패스 | 더 쉽게 유지 관리하려면 커넥터가 온-프레미스 프록시를 바이패스하여 Azure 서비스에 직접 연결하도록 구성합니다. ([애플리케이션 프록시 커넥터 및 프록시 서버](../app-proxy/application-proxy-configure-connectors-with-proxy-servers.md) 참조) |
