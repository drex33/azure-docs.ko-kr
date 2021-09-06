---
title: SCIM 지원 앱으로의 Azure AD 온-프레미스 앱 프로비저닝
description: 이 문서에서는 Azure AD 프로비저닝 서비스를 사용하여 SCIM을 사용하는 온-프레미스 앱에 사용자를 프로비전하는 방법을 설명합니다.
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2021
ms.author: billmath
ms.reviewer: arvinh
ms.openlocfilehash: 11bd751ebc95b1a9486534b34c771fde64eced21
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114437352"
---
# <a name="azure-ad-on-premises-application-provisioning-to-scim-enabled-apps"></a>SCIM 지원 앱으로의 Azure AD 온-프레미스 애플리케이션 프로비저닝

>[!IMPORTANT]
> 온-프레미스 프로비저닝 미리 보기는 현재 초대 전용 미리 보기로 제공됩니다. 기능에 대한 액세스를 요청하려면 [액세스 요청 양식](https://aka.ms/onpremprovisioningpublicpreviewaccess)을 사용하세요. 일반 공급을 준비하면서 향후 몇 개월 동안 더 많은 고객 및 커넥터에 대한 미리 보기를 공개할 예정입니다.

Azure AD(Azure Active Directory) 프로비저닝 서비스는 사용자를 클라우드 또는 온-프레미스 애플리케이션에 자동으로 프로비전하는 데 사용할 수 있는 [SCIM 2.0](https://techcommunity.microsoft.com/t5/identity-standards-blog/provisioning-with-scim-getting-started/ba-p/880010) 클라이언트를 지원합니다. 이 문서에서는 Azure AD 프로비저닝 서비스를 사용하여 SCIM을 사용하는 온-프레미스 애플리케이션에 사용자를 프로비전하는 방법을 간략하게 설명합니다. SQL 데이터 저장소로 사용하는 SCIM이 아닌 온-프레미스 애플리케이션에 사용자를 프로비전하려면 [Azure AD ECMA 커넥터 호스트 일반 SQL 커넥터 자습서](tutorial-ecma-sql-connector.md)를 참조하세요. DropBox 및 Atlassian과 같은 클라우드 앱에 사용자를 프로비전하려는 경우 앱별 [자습서](../../active-directory/saas-apps/tutorial-list.md)를 검토합니다.

![SCIM 아키텍처를 보여 주는 다이어그램](./media/on-premises-scim-provisioning/scim-4.png)

## <a name="prerequisites"></a>필수 구성 요소
- Azure AD Premium P1 또는 Premium P2(또는 EMS E3 또는 E5)를 사용하는 Azure AD 테넌트 [!INCLUDE [active-directory-p1-license.md](../../../includes/active-directory-p1-license.md)]
- 에이전트를 설치하기 위한 관리자 역할. 이 작업은 일회성 작업이며 하이브리드 관리자 또는 전역 관리자인 Azure 계정이어야 합니다. 
- 클라우드에서 애플리케이션을 구성하기 위한 관리자 역할(애플리케이션 관리자, 클라우드 애플리케이션 관리자, 전역 관리자 또는 권한이 있는 사용자 지정 역할)

## <a name="on-premises-app-provisioning-to-scim-enabled-apps"></a>SCIM 사용 앱으로의 온-프레미스 앱 프로비저닝
SCIM 사용 앱에 사용자를 프로비전하려면 다음을 수행합니다.

 1. [갤러리](../../active-directory/manage-apps/add-application-portal.md)에서 **온-프레미스 SCIM 앱** 을 추가합니다.
 1. 앱으로 이동하고 **프로비저닝** > **프로비저닝 에이전트 다운로드** 를 선택합니다.
 1. **온-프레미스 연결** 을 선택하고 프로비저닝 에이전트를 다운로드합니다.
 1. SCIM 엔드포인트가 호스트되는 가상 머신 또는 서버에 에이전트를 복사합니다.
 1. 프로비저닝 에이전트 설치 관리자를 열고 서비스 약관에 동의하고, **설치** 를 선택합니다.
 1. 프로비저닝 에이전트 마법사를 열고 사용할 확장을 묻는 메시지가 표시되면 **온-프레미스 프로비저닝** 을 선택합니다.
 1. 권한을 부여하라는 메시지가 표시되면 Azure AD 관리자에게 자격 증명을 제공합니다. 하이브리드 관리자 또는 전역 관리자가 필요합니다.
 1. **확인** 을 선택하여 설치에 성공했음을 확인합니다.
 1. 애플리케이션으로 다시 돌아가서 **온-프레미스 연결** 을 선택합니다.
 1. 설치한 에이전트를 드롭다운 목록에서 선택하고 **에이전트 할당** 을 선택합니다.
 1. 10분 정도 기다리거나 서버 또는 VM에서 Azure AD Connect 프로비저닝 에이전트 서비스를 다시 시작합니다.
 1. **테넌트 URL** 상자에서 SCIM 엔드포인트의 URL을 제공합니다. 예제는 https://localhost:8585/scim입니다.
 
     ![에이전트 할당을 보여 주는 스크린샷](./media/on-premises-scim-provisioning/scim-2.png)
 1. **연결 테스트** 를 선택하고 자격 증명을 저장합니다.
 1. 애플리케이션에 필요한 [특성 매핑](customize-application-attributes.md) 또는 [범위 지정](define-conditional-rules-for-provisioning-user-accounts.md) 규칙을 구성합니다.
 1. 애플리케이션에 [사용자 및 그룹을 할당](../../active-directory/manage-apps/add-application-portal-assign-users.md)하여 범위에 사용자를 추가합니다.
 1. 몇 명의 사용자를 대상으로 [주문형](provision-on-demand.md) 프로비저닝을 테스트합니다.
 1. 애플리케이션에 사용자를 할당하여 범위에 사용자를 더 추가합니다.
 1. **프로비저닝** 창으로 이동하고 **프로비저닝 시작** 을 선택합니다.
 1. [프로비저닝 로그](../../active-directory/reports-monitoring/concept-provisioning-logs.md)를 사용하여 모니터링합니다.

## <a name="additional-requirements"></a>추가 요구 사항
* [SCIM](https://techcommunity.microsoft.com/t5/identity-standards-blog/provisioning-with-scim-getting-started/ba-p/880010) 구현이 Azure [AD SCIM 요구 사항](use-scim-to-provision-users-and-groups.md)을 충족하는지 확인합니다.
  
  Azure AD는 개발자가 SCIM 구현을 부트스트랩하는 데 사용할 수 있는 오픈 소스 [참조 코드](https://github.com/AzureAD/SCIMReferenceCode/wiki)를 제공합니다. 코드는 있는 그대로 사용합니다.
* /schemaDiscovery 엔드포인트를 지원하여 Azure Portal에 필요한 구성을 줄입니다. 

## <a name="next-steps"></a>다음 단계

- [앱 프로비저닝](user-provisioning.md)
- [Azure AD ECMA 커넥터 호스트 설치](on-premises-ecma-install.md)
- [Azure AD ECMA 커넥터 호스트 구성](on-premises-ecma-configure.md)
- [일반 SQL 커넥터](on-premises-sql-connector-configure.md)
- [자습서: ECMA 커넥터 호스트 일반 SQL 커넥터](tutorial-ecma-sql-connector.md)
