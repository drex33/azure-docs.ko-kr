---
title: Azure AD 및 Silverfort를 사용하여 하이브리드 액세스 보호
description: 이 자습서에서는 보안 하이브리드 액세스를 위해 Azure AD와 Silverfort를 통합하는 방법을 알아봅니다.
titleSuffix: Azure AD
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 9/13/2021
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1515bfe65a9a680a9331a5a4d2b13e4d3633fda0
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129658256"
---
# <a name="tutorial-configure-silverfort-with-azure-active-directory-for-secure-hybrid-access"></a>자습서: 보안 하이브리드 액세스를 위해 Azure Active Directory를 사용하여 Silverfort 구성

이 자습서에서는 Azure AD(Azure Active Directory)와 Silverfort를 통합하는 방법에 대해 알아봅니다. [Silverfort](https://www.silverfort.com/)는 에이전트 및 프록시가 없는 혁신적인 기술을 사용하여 온-프레미스 및 클라우드의 모든 자산을 Azure AD에 연결합니다. 이 솔루션을 사용하면 조직에서 ID 보호, 가시성 및 사용자 환경을 Azure AD의 모든 환경에 적용할 수 있습니다. 이를 통해 온-프레미스 및 클라우드 환경에 대한 인증 작업에 대한 범용 위험 기반 모니터링 및 평가를 수행할 수 있으며 위협을 사전에 방지합니다.  

Silverfort는 모든 유형의 자산을 최신 웹 애플리케이션인 것처럼 Azure AD에 원활하게 연결할 수 있습니다. 다음은 그 예입니다.

- 레거시 및 자체 제작 애플리케이션

- 원격 데스크톱 및 SSH(Secure Shell)

- 명령줄 도구 및 기타 관리자 액세스

- 파일 공유 및 데이터베이스

- 인프라 및 산업 시스템

이러한 **브리지된** 자산은 Azure AD에서 일반 애플리케이션으로 표시되며, 조건부 액세스, SSO(Single Sign-On), 다단계 인증, 감사 등으로 보호할 수 있습니다.

이 솔루션은 모든 회사 자산과 타사 IAM(ID 및 액세스 관리) 플랫폼을 결합합니다. 예를 들어 하이브리드 및 다중 클라우드 환경을 포함하여 Azure AD의 Active Directory, ADFS(Active Directory Federation Services) 및 RADIUS(Remote Authentication Dial-In User Service)가 있습니다.

## <a name="scenario-description"></a>시나리오 설명

이 가이드에서는 Azure AD 테넌트에서 Silverfort Azure AD 브리지를 구성하고 테스트합니다.

구성되면 SSO를 위해 다양한 ID 원본의 인증 요청을 Azure AD에 브리지하는 Silverfort 인증 정책을 만들 수 있습니다. 애플리케이션이 브리지되면 Azure AD에서 이를 관리할 수 있습니다.

다음 다이어그램에서는 솔루션에 포함된 구성 요소와 Silverfort에서 오케스트레이션하는 인증 시퀀스를 보여 줍니다.

![아키텍처 다이어그램을 보여 주는 이미지](./media/silverfort-azure-ad-integration/silverfort-architecture-diagram.png)

| 단계 | 설명|
|:---------|:------------|
| 1. | 사용자가 Kerberos, SAML, NTLM, OIDC 및 LDAP와 같은 프로토콜을 통해 인증 요청을 원래 ID 공급자(IdP)에 보냅니다.|
| 2. | 유효성 검사를 통해 인증 상태를 확인하기 위해 응답이 있는 그대로 Silverfort로 라우팅됩니다.|
| 3. | Silverfort에서 Azure AD에 대한 가시성, 검색 및 브리징을 제공합니다.|
| 4. | 애플리케이션이 **브리지됨** 으로 구성된 경우 인증 결정이 Azure AD에 전달됩니다. Azure AD에서 조건부 액세스 정책을 평가하고 인증의 유효성을 검사합니다.|
| 5. | 그런 다음, 인증 상태 응답이 해제되고 Silverfort에서 있는 그대로 IdP에 보냅니다. |
| 6.| IdP에서 리소스에 대한 액세스를 승인하거나 거부합니다.|
| 7. | 액세스 요청이 승인되거나 거부되면 사용자에게 알립니다. |

## <a name="prerequisites"></a>사전 요구 사항

Azure AD 테넌트에 추가한 애플리케이션에 대해 SSO를 설정하려면 다음이 필요합니다.

- 활성 구독이 있는 Azure 계정. [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만들 수 있습니다.

- Azure 계정의 전역 관리자, 클라우드 애플리케이션 관리자, 애플리케이션 관리자 또는 서비스 주체 소유자 역할 중 하나

- SSO를 지원하고 이미 미리 구성되어 Azure AD 갤러리에 추가된 애플리케이션. Azure AD 갤러리의 Silverfort 애플리케이션은 이미 미리 구성되어 있습니다. 갤러리에서 이를 엔터프라이즈 애플리케이션으로 추가해야 합니다.

## <a name="onboard-with-silverfort"></a>Silverfort를 사용하여 온보딩

Silverfort를 테넌트 또는 인프라에 배포하려면 [Silverfort에 문의](https://www.silverfort.com/)하세요. Silverfort 데스크톱 앱을 관련 워크스테이션에 설치합니다.

## <a name="configure-silverfort-and-create-a-policy"></a>Silverfort 구성 및 정책 만들기

1. 브라우저에서 **Silverfort 관리 콘솔** 에 로그인합니다.

2. 주 메뉴에서 **Settings(설정)** 로 이동한 다음, [General(일반)] 섹션에서 **Azure AD Bridge Connector(Azure AD 브리지 커넥터)** 로 스크롤합니다. 테넌트 ID를 확인한 다음, **Authorize(권한 부여)** 를 선택합니다.

   ![Azure AD Bridge Connector를 보여 주는 이미지](./media/silverfort-azure-ad-integration/azure-ad-bridge-connector.png)

   ![등록 확인을 보여 주는 이미지](./media/silverfort-azure-ad-integration/grant-permission.png)

3. 새 탭에 등록 확인이 표시됩니다. 이 탭을 닫습니다.

   ![완료된 등록을 보여 주는 이미지](./media/silverfort-azure-ad-integration/registration-completed.png)

4. **Settings** 페이지에서 **Save changes(변경 내용 저장)** 를 선택합니다.

   ![Azure AD 어댑터를 보여 주는 이미지](./media/silverfort-azure-ad-integration/silverfort-azure-ad-adapter.png)

    Azure AD 콘솔에 로그인합니다. 엔터프라이즈 애플리케이션으로 등록된 **Silverfort Azure AD 어댑터** 애플리케이션이 표시됩니다.

   ![엔터프라이즈 애플리케이션을 보여 주는 이미지](./media/silverfort-azure-ad-integration/enterprise-application.png)

5. Silverfot 관리 콘솔에서 **Policies(정책)** 페이지로 이동하고, **Create Policy(정책 만들기)** 를 선택합니다.

6. **New Policy(새 정책)** 대화 상자가 표시됩니다. Azure에서 만들어질 애플리케이션 이름을 나타내는 **Policy Name(정책 이름)** 을 입력합니다. 예를 들어 이 정책에 따라 여러 서버 또는 애플리케이션을 추가하는 경우 정책이 적용되는 리소스를 반영하도록 이름을 지정합니다. 이 예에서는 *SL-APP1* 서버에 대한 정책을 만듭니다.

   ![정책 정의를 보여 주는 이미지](./media/silverfort-azure-ad-integration/define-policy.png)

7. 적절한 **Authentication(인증)** 유형과 **Protocol(프로토콜)** 을 선택합니다.

8. **Users and Groups(사용자 및 그룹)** 필드에서 편집 아이콘을 선택하여 정책의 영향을 받을 사용자를 구성합니다. 이러한 사용자의 인증은 Azure AD에 브리지됩니다.

   ![사용자 및 그룹을 보여 주는 이미지](./media/silverfort-azure-ad-integration/user-groups.png)

9. 사용자, 그룹 또는 OU(조직 구성 단위)를 검색하여 선택합니다.

   ![사용자 검색을 보여 주는 이미지](./media/silverfort-azure-ad-integration/search-users.png)

   선택한 사용자가 SELECTED(선택됨) 상자에 나열됩니다.

   ![선택된 사용자를 보여 주는 이미지](./media/silverfort-azure-ad-integration/select-user.png)

10. 정책을 적용할 **Source(원본)** 를 선택합니다. 이 예에서는 *All Devices(모든 디바이스)* 가 선택되었습니다.

    ![원본을 보여 주는 이미지](./media/silverfort-azure-ad-integration/source.png)

11. **Destination(대상)** 을 *SL-App1* 로 설정합니다. 편집 단추를 선택하여 더 많은 리소스 또는 리소스 그룹을 변경하거나 추가할 수 있습니다(선택 사항).

    ![대상을 보여 주는 이미지](./media/silverfort-azure-ad-integration/destination.png)

12. **AZURE AD BRIDGE(Azure AD 브리지)** 에 대한 작업을 선택합니다.

    ![Azure AD 브리지 저장을 보여 주는 이미지](./media/silverfort-azure-ad-integration/save-azure-ad-bridge.png)

13. **SAVE(저장)** 를 선택하여 새 정책을 저장합니다. 해당 정책을 사용하도록 설정하거나 활성화하라는 메시지가 표시됩니다.

    ![변경 상태를 보여 주는 이미지](./media/silverfort-azure-ad-integration/change-status.png)

    정책이 Azure AD Bridge((Azure AD 브리지)) 섹션의 Policies(정책) 페이지에 표시됩니다.

    ![정책 추가를 보여 주는 이미지](./media/silverfort-azure-ad-integration/add-policy.png)

14. Azure AD 콘솔로 돌아가서 **엔터프라이즈 애플리케이션** 으로 이동합니다. 이제 새 Silverfort 애플리케이션이 표시됩니다. 이제 이 애플리케이션을 [CA 정책](../authentication/tutorial-enable-azure-mfa.md?bc=/azure/active-directory/conditional-access/breadcrumb/toc.json&toc=/azure/active-directory/conditional-access/toc.json%23create-a-conditional-access-policy)에 포함할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Silverfort Azure AD 어댑터](https://azuremarketplace.microsoft.com/marketplace/apps/aad.silverfortazureadadapter?tab=overview)

- [Silverfort 리소스](https://www.silverfort.com/resources/)
