---
title: Azure AD ECMA 커넥터 호스트 설치
description: 이 문서에서는 Azure AD ECMA 커넥터 호스트를 설치하는 방법을 설명합니다.
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/28/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: dea990e1075e8a0714959f42fdef38286a120912
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114449593"
---
# <a name="installation-of-the-azure-ad-ecma-connector-host"></a>Azure AD ECMA 커넥터 호스트 설치

>[!IMPORTANT]
> 온-프레미스 프로비저닝 미리 보기는 현재 초대 전용 미리 보기로 제공됩니다. 기능에 대한 액세스를 요청하려면 [액세스 요청 양식](https://aka.ms/onpremprovisioningpublicpreviewaccess)을 사용하세요. 일반 공급을 준비하면서 향후 몇 개월 동안 더 많은 고객 및 커넥터에 대한 미리 보기를 공개할 예정입니다.

Azure AD(Azure Active Directory) ECMA 커넥터 호스트는 Azure AD Connect 프로비저닝 에이전트 패키지에 포함되어 있습니다. 프로비저닝 에이전트와 Azure AD ECMA 커넥터 호스트는 두 개의 개별 Windows 서비스입니다. 두 서비스는 동일한 머신에 배포된 하나의 설치 관리자를 사용하여 설치됩니다.

이 흐름은 Azure AD ECMA 커넥터 호스트를 설치하고 구성하는 프로세스를 안내합니다.

 ![설치 흐름을 보여 주는 다이어그램](./media/on-premises-ecma-install/flow-1.png)

설치 및 구성에 대한 자세한 내용은 다음을 참조하세요.

   - [Azure AD ECMA 커넥터 호스트의 필수 조건](on-premises-ecma-prerequisites.md)
   - [Azure AD ECMA 커넥터 호스트 및 프로비저닝 에이전트 구성](on-premises-ecma-configure.md)
   - [Azure AD ECMA 커넥터 호스트 일반 SQL 커넥터 구성](on-premises-sql-connector-configure.md)

## <a name="download-and-install-the-azure-ad-connect-provisioning-agent-package"></a>Azure AD Connect 프로비저닝 에이전트 패키지 다운로드 및 설치

 1. Azure Portal에 로그인합니다.
 1. **엔터프라이즈 애플리케이션** > **새 애플리케이션 추가** 로 이동합니다.
 1. **온-프레미스 프로비저닝** 애플리케이션을 검색하여 테넌트 이미지에 추가합니다.
 1. **프로비저닝** 창으로 이동합니다.
 1. **온-프레미스 연결** 을 선택합니다.
 1. 에이전트 설치 관리자를 다운로드합니다.
 1. Azure AD Connect 프로비저닝 설치 관리자 **AADConnectProvisioningAgentSetup.msi** 를 실행합니다.
 1. **Microsoft Azure AD Connect 프로비저닝 에이전트 패키지** 화면에서 사용 조건에 동의하고 **설치** 를 선택합니다.
 
    ![Microsoft Azure AD Connect 프로비저닝 에이전트 패키지 화면](media/on-premises-ecma-install/install-1.png)</br>
 1. 이 작업이 완료되면 구성 마법사가 시작됩니다. **다음** 을 선택합니다.
 
    ![시작 화면을 보여 주는 스크린샷](media/on-premises-ecma-install/install-2.png)</br>

 1. **확장 선택** 화면에서 **온-프레미스 애플리케이션 프로비저닝(Azure AD에서 애플리케이션으로)** 을 선택합니다. **다음** 을 선택합니다. 
 
    ![확장 선택 스크린샷](media/on-premises-ecma-install/install-3.png)</br>
 1. 전역 관리자 계정을 사용하여 Azure AD에 로그인합니다.
 
     ![Azure 로그인 스크린샷](media/on-premises-ecma-install/install-4.png)</br>
 1. **에이전트 구성** 화면에서 **확인** 을 선택합니다.
 
     ![설치 확인 스크린샷](media/on-premises-ecma-install/install-5.png)</br>
 1. 설치가 완료되면 마법사 아래쪽에 메시지가 표시됩니다. **끝내기** 를 선택합니다.
 
     ![완료 중 스크린샷](media/on-premises-ecma-install/install-6.png)</br>
 

에이전트 패키지를 성공적으로 설치했으므로 이제 Azure AD ECMA 커넥터 호스트를 구성하고 커넥터를 만들거나 가져와야 합니다.
 
## <a name="next-steps"></a>다음 단계

- [Azure AD ECMA 커넥터 호스트 필수 조건](on-premises-ecma-prerequisites.md)
- [Azure AD ECMA 커넥터 호스트 구성](on-premises-ecma-configure.md)
- [일반 SQL 커넥터](on-premises-sql-connector-configure.md)
