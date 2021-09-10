---
title: Azure AD Connect 프로비저닝 에이전트 설치
description: Azure AD Connect 프로비전 에이전트를 설치하는 방법과 Azure Portal에서 그것을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/16/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 660d233c4fc0690799e8ec25e6a07e516035a90b
ms.sourcegitcommit: 0ab53a984dcd23b0a264e9148f837c12bb27dac0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2021
ms.locfileid: "113506334"
---
# <a name="install-the-azure-ad-connect-provisioning-agent"></a>Azure AD Connect 프로비저닝 에이전트 설치
이 문서에서는 Azure AD(Azure Active Directory) Connect 프로비저닝 에이전트의 설치 프로세스와 Azure Portal에 이를 처음으로 구성하는 방법을 안내합니다.

>[!IMPORTANT]
>다음 설치 지침에서는 모든 [필수 구성 요소](how-to-prerequisites.md)가 충족되었다고 가정합니다.

>[!NOTE]
>이 문서에서는 마법사를 사용하여 프로비저닝 에이전트를 설치하는 방법을 다룹니다. CLI(명령줄 인터페이스)를 사용하여 Azure AD Connect 프로비저닝 에이전트를 설치하는 방법에 대한 자세한 내용은 [CLI 및 PowerShell을 사용하여 Azure AD Connect 프로비저닝 에이전트 설치](how-to-install-pshell.md)를 참조하세요.

## <a name="group-managed-service-accounts"></a>Group Managed Service Accounts
gMSA(그룹 관리 서비스 계정)는 자동 암호 관리, 간소화된 SPN(서비스 사용자 이름) 관리 및 다른 관리자에게 관리를 위임하는 기능을 제공하는 관리되는 도메인 계정입니다. 또한 이 기능을 여러 서버로 확장합니다. Azure AD Connect 클라우드 동기화는 에이전트를 실행하는 그룹 관리 서비스 계정 사용을 지원하고 권장합니다. 그룹 관리 서비스 계정에 대한 자세한 내용은 [그룹 관리 서비스 계정](/windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview)을 참조하세요.


### <a name="upgrade-an-existing-agent-to-use-the-gmsa"></a>gMSA를 사용하도록 기존 에이전트 업그레이드
설치 중에 만든 그룹 관리 서비스 계정을 사용하도록 기존 에이전트를 업그레이드하려면 AADConnectProvisioningAgent.msi를 실행하여 에이전트 서비스를 최신 버전으로 업데이트합니다. 이제 설치 마법사를 다시 실행하고, 메시지가 표시되면 계정을 만들 수 있는 자격 증명을 제공합니다.

## <a name="install-the-agent"></a>에이전트 설치

에이전트를 설치하려면 다음을 수행합니다.

 1. 사용할 서버에 엔터프라이즈 관리자 권한으로 로그인합니다.
 1. Azure Portal에 로그인한 다음 **Azure Active Directory** 로 이동합니다.
 1. 왼쪽 메뉴에서 **Azure AD Connect** 를 선택합니다.
 1. **클라우드 동기화 관리** > **모든 에이전트 검토** 를 선택합니다.
 1. Azure Portal에서 Azure AD Connect 프로비저닝 에이전트를 다운로드합니다.
 
    ![온-프레미스 에이전트 다운로드를 보여주는 스크린샷.](media/how-to-install/install-9.png)</br>
 1. 사용 약관에 동의하고, **다운로드** 를 선택합니다.
 1. Azure AD Connect 프로비전 설치 프로그램 AADConnectProvisioningAgentSetup.msi를 실행합니다.
 1. **Microsoft Azure AD Provisioning Agent Package** 화면에서 사용 조건에 동의하고 **설치** 를 선택합니다.
 
    ![Microsoft Azure AD Connect 프로비전 에이전트 패키지 화면을 보여 주는 스크린샷](media/how-to-install/install-1.png)</br>
 1. 이 작업이 완료되면 구성 마법사가 시작됩니다. Azure AD 글로벌 관리자 계정으로 로그인합니다.
 1. **서비스 계정 구성** 화면에서 **gMSA 만들기** 또는 **사용자 지정 gMSA 사용** 을 선택합니다. 에이전트가 계정을 만들도록 허용하면 이름이 provAgentgMSA$로 지정됩니다. **사용자 지정 gMSA 사용** 을 지정하면 이 계정을 제공하라는 메시지가 표시됩니다.
 1. 도메인 관리자 자격 증명을 입력하여, 에이전트 서비스를 실행하는 데 사용되는 그룹 관리 서비스 계정을 만듭니다. **다음** 을 선택합니다.
  
    ![gMSA 만들기 옵션을 보여주는 스크린샷.](media/how-to-install/install-12.png)</br>
 1. **Active Directory 연결** 화면에서 **디렉터리 추가** 를 선택합니다. 그런 다음, Active Directory 관리자 계정으로 로그인합니다. 이 작업을 수행하면 온-프레미스 디렉터리가 추가됩니다. 
 1. 필요에 따라 **도메인 컨트롤러 우선 순위 선택** 확인란 및 도메인 컨트롤러 목록 순서 지정을 선택하여, 에이전트가 사용하는 도메인 컨트롤러의 기본 설정을 관리할 수 있습니다. **확인** 을 선택합니다.
 
    ![도메인 컨트롤러의 순서 지정을 보여주는 스크린샷.](media/how-to-install/install-2a.png)</br>
 1. **다음** 을 선택합니다.
 
    ![Active Directory 연결 화면을 보여주는 스크린샷.](media/how-to-install/install-3a.png)</br>
 1. **에이전트 설치** 화면에서 설정 및 생성될 계정을 확인하고, **확인** 을 선택합니다.
 
    ![설정 확인을 보여주는 스크린샷.](media/how-to-install/install-11.png)</br>
 1. 이 작업이 완료되면, **에이전트 설치가 완료** 된 것을 확인해야 합니다. **끝내기** 를 선택합니다.
 
    ![구성 완료 화면을 보여주는 스크린샷.](media/how-to-install/install-4a.png)</br>
 1. 초기 **Microsoft Azure AD Connect Provisioning Agent Package** 화면이 계속 표시되는 경우 **닫기** 를 선택합니다.

## <a name="verify-agent-installation"></a>에이전트 설치 확인
에이전트 확인은 Azure Portal 및 에이전트를 실행하는 로컬 서버에서 수행됩니다.

### <a name="azure-portal-agent-verification"></a>Azure Portal에서 에이전트 확인
Azure에서 에이전트가 표시되는지 확인하려면 다음을 수행합니다.

 1. Azure Portal에 로그인합니다.
 1. 왼쪽에서 **Azure Active Directory** > **Azure AD Connect** 를 선택합니다. 중앙에서 **클라우드 동기화 관리** 를 선택합니다.

    ![Azure Portal을 보여 주는 스크린샷.](media/how-to-install/install-6.png)</br>

 1. **Azure AD Connect 클라우드 동기화** 화면에서 **모든 에이전트 검토** 를 클릭합니다.

    ![모든 에이전트 검토 옵션을 보여주는 스크린샷.](media/how-to-install/install-7.png)</br>
 
 1. **온-프레미스 프로비저닝 에이전트** 화면에 설치한 에이전트가 표시됩니다. 해당 에이전트가 있고 *활성* 으로 표시되어 있는지 확인합니다.

    ![온-프레미스 프로비저닝 에이전트 화면을 보여주는 스크린샷.](media/how-to-install/verify-1.png)</br>

### <a name="on-the-local-server"></a>로컬 서버에서 에이전트 확인
에이전트가 실행 중인지 확인하려면 다음을 수행합니다.

1. 관리자 계정으로 서버에 로그인합니다.
1. **서비스** 로 이동하거나 **시작** > **실행** > **Services.msc** 를 선택하여 서비스를 엽니다.
1. **서비스** 아래에서 **Microsoft Azure AD Connect 에이전트 업데이트 프로그램** 및 **Microsoft Azure AD Connect Provisioning Agent** 가 있고 상태가 *실행 중* 인지 확인합니다.

    ![서비스 화면을 보여주는 스크린샷.](media/how-to-install/troubleshoot-1.png)

>[!IMPORTANT]
>에이전트가 설치되었지만 에이전트가 사용자 동기화를 시작하기 전에 에이전트를 구성하고 사용하도록 설정해야 합니다. 새 에이전트를 구성하려면 [Azure AD Connect 클라우드 동기화에 대한 새 구성 만들기](how-to-configure.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계 

- [프로비저닝이란?](what-is-provisioning.md)
- [Azure AD Connect 클라우드 동기화란?](what-is-cloud-sync.md)
- [Azure AD Connect 클라우드 동기화에 대한 새 구성 만들기](how-to-configure.md)

