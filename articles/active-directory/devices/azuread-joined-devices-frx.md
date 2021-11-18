---
title: 첫 번째 실행 동안 Azure AD에 새 Windows 10 디바이스 조인 | Microsoft Docs
description: 기본 제공 환경에서 사용자가 Azure AD 조인을 설정하는 방법입니다.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b76ade4b47417cb6c3d4f65daa335553c7a4b83
ms.sourcegitcommit: c434baa76153142256d17c3c51f04d902e29a92e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132179187"
---
# <a name="tutorial-join-a-new-windows-10-device-with-azure-ad-during-a-first-run"></a>자습서: 첫 번째 실행 동안 Azure AD에 새 Windows 10 디바이스 조인

Azure AD(Active Directory)의 디바이스 관리를 사용하면 보안 및 규정 준수에 대한 표준을 충족하는 디바이스에서 사용자 리소스에 액세스할 수 있습니다. 자세한 내용은 [Azure Active Directory의 디바이스 관리 소개](overview.md)를 참조하세요.

Windows 10을 사용할 경우 OOBE(첫 실행 즉시 사용 환경) 동안 Azure AD에 새 디바이스를 조인할 수 있습니다.  
이렇게 하면 축소 래핑된 디바이스를 직원이나 학생에게 배포할 수 있습니다.

Windows 10 Professional 또는 Windows 10 Enterprise가 디바이스에 설치된 경우 환경은 기본적으로 회사 소유의 디바이스에 대한 설정 프로세스로 지정됩니다.

Windows *기본 환경* 에서 온-프레미스 AD(Active Directory) 도메인 조인은 지원되지 않습니다. 컴퓨터를 AD 도메인에 조인하려는 경우 **로컬 계정을 사용하여 Windows 설정** 링크를 선택해야 합니다. 그런 다음 컴퓨터의 설정에서 도메인에 조인할 수 있습니다.
 
이 자습서에서는 FRX 중에 디바이스를 Azure AD에 조인하는 방법에 대해 알아봅니다.
 > [!div class="checklist"]
> * 필수 구성 요소
> * 디바이스 조인
> * 확인

## <a name="prerequisites"></a>필수 구성 요소

Windows 10 디바이스를 조인하려면 디바이스를 등록할 수 있도록 디바이스 등록 서비스를 구성해야 합니다. Azure AD 테넌트에 디바이스를 조인할 수 있는 권한이 필요한 것 외에도, 구성된 디바이스 최대 수보다 적은 수의 디바이스를 등록해야 합니다. 자세한 내용은 [디바이스 설정 구성](device-management-azure-portal.md#configure-device-settings)을 참조하세요.

또한 테넌트가 페더레이션된 경우 ID 공급자가 WS-Fed 및 WS-Trust 사용자 이름/암호 엔드포인트를 지원해야 합니다. 버전 1.3 또는 2005일 수 있습니다. 이 프로토콜 지원은 Azure AD에 디바이스를 조인하고 암호를 사용하여 디바이스에 로그인하는 데 필요합니다.

## <a name="joining-a-device"></a>디바이스 조인

**FRX 동안 Windows 10 디바이스를 Azure AD에 조인하려면**

1. 새 디바이스를 켜고 설정 프로세스를 시작하면 **준비 중** 메시지가 표시됩니다. 프롬프트에 따라 디바이스를 설정합니다.
1. 국가 및 언어를 사용자 지정하는 작업부터 시작합니다. 그런 다음 Microsoft 소프트웨어 사용 조건에 동의합니다.
 
    <!--![Customize for your region](./media/azuread-joined-devices-frx/01.png)-->

1. 인터넷에 연결하는 데 사용할 네트워크를 선택합니다.
1. **이 디바이스는 내 조직에서 소유한 디바이스입니다** 를 클릭합니다. 

    <!--![Who owns this PC screen](./media/azuread-joined-devices-frx/02.png)-->

1. 조직에서 제공한 자격 증명을 입력하고 **로그인** 을 클릭합니다.

    <!--![Sign-in screen](./media/azuread-joined-devices-frx/03.png)-->

1. 디바이스는 Azure AD에서 일치하는 테넌트를 찾습니다. 페더레이션된 도메인의 사용자인 경우 온-프레미스 STS(보안 토큰 서비스)가 서버(예: AD FS(Active Directory Federation Services))로 리디렉션됩니다.
1. 페더레이션되지 않은 도메인의 사용자의 경우 Azure AD 호스팅 페이지에 직접 자격 증명을 입력해야 합니다. 
1. 다단계 인증 질문에 대한 메시지가 표시됩니다. 
1. Azure AD는 모바일 디바이스 관리에 등록해야 하는지 여부를 확인합니다.
1. Windows에서 조직의 디렉터리에 있는 디바이스를 Azure AD에 등록하고 해당하는 경우 모바일 디바이스 관리에 등록합니다.
1. 다음 사용자인 경우 해당 작업이 수행됩니다.
   - 관리되는 사용자인 경우 자동 로그인 프로세스를 통해 데스크톱으로 이동됩니다.
   - 페더레이션 사용자인 경우 자격 증명을 입력하도록 Windows 로그인 화면으로 이동됩니다.

## <a name="verification"></a>확인

디바이스가 Azure AD에 조인되었는지 여부를 확인하려면 Windows 디바이스에서 **회사 또는 학교 액세스** 대화 상자를 검토합니다. 이 대화 상자에는 사용자가 Azure AD 디렉터리에 연결되어 있다고 표시됩니다.

![회사 또는 학교 액세스](./media/azuread-joined-devices-frx/13.png)

## <a name="next-steps"></a>다음 단계

- 자세한 내용은 [Azure Active Directory의 디바이스 관리 소개](overview.md)를 참조하세요.
- Azure AD 포털에서 디바이스를 관리하는 방법에 대한 자세한 내용은 [Azure Portal을 사용하여 디바이스 관리](device-management-azure-portal.md)를 참조하세요.
