---
title: Azure Active Directory 비밀번호 쓰기 저장 사용
description: 이 자습서에서는 Azure AD Connect를 통해 Azure AD 셀프 서비스 암호 재설정 쓰기 저장을 사용하도록 설정하여 변경 내용을 온-프레미스 Active Directory Domain Services 환경으로 다시 동기화하는 방법을 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 08/25/2021
ms.author: justinha
author: justinha
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.custom: contperf-fy20q4
ms.openlocfilehash: a2b716d2b0187e90e71156f2259a380d6fde7db4
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130073262"
---
# <a name="tutorial-enable-azure-active-directory-self-service-password-reset-writeback-to-an-on-premises-environment"></a>자습서: 온-프레미스 환경에 Azure Active Directory 셀프 서비스 암호 재설정 쓰기 저장 사용

Azure AD(Azure Active Directory) SSPR(셀프 서비스 암호 재설정)을 사용하면 웹 브라우저를 사용하여 암호를 업데이트하거나 계정 잠금을 해제할 수 있습니다. [Azure AD에서 SSPR을 사용하도록 설정하고 구성하는 방법](https://www.youtube.com/watch?v=rA8TvhNcCvQ)에 대해 이 비디오를 권장합니다. Azure AD가 온-프레미스 AD DS(Active Directory Domain Services) 환경에 연결된 하이브리드 환경에서는 이 시나리오로 인해 두 디렉터리 간에 암호가 달라질 수 있습니다.

비밀번호 쓰기 저장은 Azure AD의 암호 변경 내용을 온-프레미스 AD DS 환경으로 다시 동기화하는 데 사용할 수 있습니다. Azure AD Connect는 이러한 암호 변경 내용을 Azure AD에서 기존 온-프레미스 디렉터리로 다시 보내는 보안 메커니즘을 제공합니다.

> [!IMPORTANT]
> 이 자습서에서는 셀프 서비스 암호 재설정을 온-프레미스 환경으로 다시 사용하도록 설정하는 방법을 관리자에게 보여줍니다. 셀프 서비스 암호 재설정에 이미 등록된 최종 사용자가 계정으로 다시 이동해야 하는 경우 https://aka.ms/sspr 로 이동합니다.
>
> IT 팀이 자신의 암호를 재설정 하는 기능을 사용하도록 설정하지 않은 경우, 추가 지원이 필요 하면 기술 지원팀에 문의하세요.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 비밀번호 쓰기 저장에 필요한 권한 구성
> * Azure AD Connect에서 비밀번호 쓰기 저장 옵션 사용
> * Azure AD SSPR에서 비밀번호 쓰기 저장 사용

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하는 데 필요한 리소스와 권한은 다음과 같습니다.

* Azure AD Premium P1 또는 평가판 이상의 라이선스를 사용하도록 설정되어 작동하는 Azure AD 테넌트.
    * 필요한 경우, [체험 계정을 만드세요](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
    * 자세한 내용은 [Azure AD SSPR에 대한 라이선스 요구 사항](concept-sspr-licensing.md)을 참조하세요.
* *글로벌 관리자* 권한이 있는 계정
* 셀프 서비스 암호 재설정으로 구성된 Azure AD
    * 필요한 경우 [이전 자습서를 완료하여 Azure AD SSPR을 사용하도록 설정](tutorial-enable-sspr.md)합니다.
* 현재 버전의 Azure AD Connect를 사용하여 구성된 기존 온-프레미스 AD DS 환경
    * 필요한 경우 [기본](../hybrid/how-to-connect-install-express.md) 또는 [사용자 지정](../hybrid/how-to-connect-install-custom.md) 설정을 사용하여 Azure AD Connect를 구성합니다.
    * 비밀번호 쓰기 저장을 사용하려면 도메인 컨트롤러가 Windows Server 2016 이상이어야 합니다.

## <a name="configure-account-permissions-for-azure-ad-connect"></a>Azure AD Connect에 대한 계정 권한 구성

Azure AD Connect를 사용하면 온-프레미스 AD DS 환경과 Azure AD 간에 사용자, 그룹 및 자격 증명을 동기화할 수 있습니다. 일반적으로 온-프레미스 AD DS 도메인에 조인된 Windows Server 2016 이상 컴퓨터에 Azure AD Connect를 설치합니다.

SSPR 쓰기 저장을 제대로 사용하려면 Azure AD Connect에 지정된 계정에 적절한 권한과 옵션이 설정되어 있어야 합니다. 현재 사용하는 계정을 잘 모르는 경우 Azure AD Connect를 열고 **현재 구성 보기** 옵션을 선택합니다. 권한을 추가해야 하는 계정은 **동기화된 디렉터리** 아래에 나열됩니다. 계정에 설정해야 하는 권한과 옵션은 다음과 같습니다.

* **암호 다시 설정**
* **쓰기 권한** 켜짐`lockoutTime`
* **쓰기 권한** 켜짐`pwdLastSet`
* 아직 설정하지 않은 경우 해당 포리스트에 있는 *각 도메인* 의 루트 개체에 "암호 만료"에 대한 **확장 권한** 이 있습니다.

이러한 권한을 할당하지 않으면 쓰기 저장이 올바르게 구성된 것처럼 표시되지만 사용자가 클라우드에서 온-프레미스 암호를 관리할 때 오류가 발생합니다. "만료되지 않은 암호"가 표시되도록 하려면 **이 개체 및 모든 하위 개체** 에 사용 권한을 적용해야 합니다.  

> [!TIP]
>
> 일부 사용자 계정의 암호가 온-프레미스 디렉터리에 다시 기록되지 않는 경우 온-프레미스 AD DS 환경의 계정에 대해 상속이 비활성화되어 있지 않은지 확인합니다. 기능이 제대로 작동하려면 암호에 대한 쓰기 권한을 하위 개체에 적용해야 합니다.

비밀번호 쓰기 저장이 발생하도록 적절한 사용 권한을 설정하려면 다음 단계를 완료하세요.

1. 온-프레미스 AD DS 환경에서 적절한 *도메인 관리자* 권한이 있는 계정으로 **Active Directory 사용자 및 컴퓨터** 를 엽니다.
1. **보기** 메뉴에서 **고급 기능** 이 켜져 있는지 확인합니다.
1. 왼쪽 패널에서 마우스 오른쪽 단추로 도메인의 루트를 나타내는 개체를 선택하고, **속성** > **보안** > **고급** 을 차례로 선택합니다.
1. **사용 권한** 탭에서 **추가** 를 선택합니다.
1. **보안 주체** 에 대해 권한을 적용할 계정(Azure AD Connect에서 사용하는 계정)을 선택합니다.
1. **적용 대상** 드롭다운 목록에서 **하위 사용자 개체** 를 선택합니다.
1. *권한* 아래에서 다음 옵션의 상자를 선택합니다.
    * **암호 다시 설정**
1. *속성* 아래에서 다음 옵션의 확인란을 선택합니다. 목록을 스크롤하여 이미 기본적으로 설정되어 있을 수 있는 옵션을 찾습니다.
    * **lockoutTime 쓰기**
    * **pwdLastSet 쓰기**

    [ ![Active Directory 사용자 및 컴퓨터를 통해 Azure AD Connect에서 사용하는 계정에 대한 적절한 권한 설정](media/tutorial-enable-sspr-writeback/set-ad-ds-permissions-cropped.png) ](media/tutorial-enable-sspr-writeback/set-ad-ds-permissions.png#lightbox)

1. 준비가 되면 **적용/확인** 을 선택하여 변경 내용을 적용하고, 열려 있는 대화 상자를 모두 종료합니다.

권한을 업데이트하는 경우 이러한 권한으로 디렉터리의 모든 개체를 복제하는 데 한 시간 이상 걸릴 수 있습니다.

온-프레미스 AD DS 환경의 암호 정책에 따라 암호 재설정이 올바르게 처리되지 않을 수 있습니다. 비밀번호 쓰기 저장이 가장 효율적으로 작동하려면 *최소 암호 사용 기간* 에 대한 그룹 정책을 0으로 설정해야 합니다. 이 설정은 `gpmc.msc`의 **컴퓨터 구성 > 정책 > Windows 설정 > 보안 설정 > 계정 정책** 에서 확인할 수 있습니다.

그룹 정책을 업데이트하는 경우 업데이트된 정책이 복제될 때까지 기다리거나 `gpupdate /force` 명령을 사용합니다.

> [!Note]
> 암호를 즉시 변경하려면 *최소 암호 사용 기간* 을 0으로 설정해야 합니다. 그러나 사용자가 온-프레미스 정책을 준수하고 *최소 암호 사용 기간* 을 0보다 큰 값으로 설정한 경우 온-프레미스 정책을 평가한 후에도 비밀번호 쓰기 저장이 계속 작동합니다.

## <a name="enable-password-writeback-in-azure-ad-connect"></a>Azure AD Connect에서 비밀번호 쓰기 저장 사용

Azure AD Connect의 구성 옵션 중 하나가 비밀번호 쓰기 저장입니다. 이 옵션을 사용하도록 설정되면 암호 변경 이벤트로 인해 Azure AD Connect에서 업데이트된 자격 증명을 온-프레미스 AD DS 환경으로 다시 동기화합니다.

SSPR 쓰기 저장을 사용하도록 설정하려면 먼저 Azure AD Connect에서 쓰기 저장 옵션을 사용하도록 설정합니다. Azure AD Connect 서버에서 다음 단계를 완료합니다.

1. Azure AD Connect 서버에 로그인하고, **Azure AD Connect** 구성 마법사를 시작합니다.
1. **시작** 페이지에서 **구성** 을 선택합니다.
1. **추가 작업** 페이지에서 **동기화 옵션 사용자 지정** 을 선택하고 **다음** 을 선택합니다.
1. **Azure AD에 연결** 페이지에서 Azure 테넌트에 대한 글로벌 관리자 자격 증명을 입력하고, **다음** 을 선택합니다.
1. **디렉터리 연결** 및 **도메인/OU** 필터링 페이지에서 **다음** 을 선택합니다.
1. **선택적 기능** 페이지에서 **비밀번호 쓰기 저장** 옆의 확인란을 선택한 후 **다음** 을 선택합니다.

    ![Azure AD Connect의 비밀번호 쓰기 저장 구성](media/tutorial-enable-sspr-writeback/enable-password-writeback.png)

1. **디렉터리 확장** 페이지에서 **다음** 을 선택합니다.
1. **구성 준비 완료** 페이지에서 **구성** 을 선택하고 프로세스가 완료될 때까지 기다립니다.
1. 구성이 완료된 것이 확인되면 **마침** 을 선택합니다.

## <a name="enable-password-writeback-for-sspr"></a>SSPR에 대한 비밀번호 쓰기 저장 사용

이제 Azure AD Connect에서 비밀번호 쓰기 저장을 사용하도록 설정한 상태에서 Azure AD SSPR을 쓰기 저장으로 구성합니다. SSPR에서 비밀번호 쓰기 저장을 사용하도록 설정되면 암호를 변경하거나 다시 설정하는 사용자는 업데이트된 암호를 온-프레미스 AD DS 환경으로도 다시 동기화해야 합니다.

SSPR에서 비밀번호 쓰기 저장을 사용하도록 설정하려면 다음 단계를 완료합니다.

1. 전역 관리자 계정을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Azure Active Directory** 를 찾아서 선택하고, **암호 재설정** 을 선택한 다음, **온-프레미스 통합** 을 선택합니다.
1. **온-프레미스 디렉터리에 비밀번호를 쓰기 저장하시겠습니까?** 옵션을 *예* 로 설정합니다.
1. **사용자가 암호를 재설정하지 않고 계정을 잠금 해제하도록 허용하시겠습니까?** 옵션을 *예* 로 설정합니다.

    ![Azure AD 셀프 서비스 암호 재설정에 비밀번호 쓰기 저장 사용](media/tutorial-enable-sspr-writeback/enable-sspr-writeback.png)

1. 준비되면 **저장** 을 선택합니다.

## <a name="clean-up-resources"></a>리소스 정리

이 자습서의 일부로 구성한 SSPR 쓰기 저장 기능을 더 이상 사용하지 않으려면 다음 단계를 완료합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Azure Active Directory** 를 찾아서 선택하고, **암호 재설정** 을 선택한 다음, **온-프레미스 통합** 을 선택합니다.
1. **온-프레미스 디렉터리에 비밀번호를 쓰기 저장하시겠습니까?** 옵션을 *아니요* 로 설정합니다.
1. **사용자가 암호를 재설정하지 않고 계정을 잠금 해제하도록 허용하시겠습니까?** 옵션을 *아니요* 로 설정합니다.

암호 기능을 더 이상 사용하지 않으려면 Azure AD Connect 서버에서 다음 단계를 완료합니다.

1. Azure AD Connect 서버에 로그인하고, **Azure AD Connect** 구성 마법사를 시작합니다.
1. **시작** 페이지에서 **구성** 을 선택합니다.
1. **추가 작업** 페이지에서 **동기화 옵션 사용자 지정** 을 선택하고 **다음** 을 선택합니다.
1. **Azure AD에 연결** 페이지에서 Azure 테넌트에 대한 글로벌 관리자 자격 증명을 입력하고, **다음** 을 선택합니다.
1. **디렉터리 연결** 및 **도메인/OU** 필터링 페이지에서 **다음** 을 선택합니다.
1. **선택적 기능** 페이지에서 **비밀번호 쓰기 저장** 옆의 확인란을 선택 취소하고, **다음** 을 선택합니다.
1. **구성 준비 완료** 페이지에서 **구성** 을 선택하고 프로세스가 완료될 때까지 기다립니다.
1. 구성이 완료된 것이 확인되면 **마침** 을 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 AD DS 환경에 온-프레미스 Azure AD SSPR 쓰기 저장을 사용하도록 설정했습니다. 구체적으로 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * 비밀번호 쓰기 저장에 필요한 권한 구성
> * Azure AD Connect에서 비밀번호 쓰기 저장 옵션 사용
> * Azure AD SSPR에서 비밀번호 쓰기 저장 사용

> [!div class="nextstepaction"]
> [로그인 시 위험 평가](tutorial-risk-based-sspr-mfa.md)
