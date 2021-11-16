---
title: Azure Virtual Desktop 시작 기능 배포 - Azure
description: Azure Portal의 시작 기능을 사용하여 Azure Virtual Desktop을 빠르게 설정하는 방법에 대한 빠른 시작 가이드입니다.
author: Heidilohr
ms.topic: quickstart
ms.date: 07/14/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 62ba9a8fa578ab12408160d9a6d19243474921fc
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132277223"
---
# <a name="deploy-azure-virtual-desktop-with-the-getting-started-feature"></a>시작 기능을 사용하여 Azure Virtual Desktop 배포

Azure Portal의 새로운 시작 기능은 배포에 Azure Virtual Desktop을 설치하고 구성하는 빠르고 쉬운 방법입니다.

## <a name="requirements"></a>요구 사항

시작을 사용하려면 다음 요구 사항을 충족해야 합니다.

- Azure AD(Active Directory) 테넌트.
- Azure AD에 대한 전역 관리자 권한이 있는 계정.

   >[!NOTE]
   >시작 기능은 현재 MSA, B2B 또는 게스트 계정을 지원하지 않습니다.

- 활성화된 Azure 구독.

   >[!NOTE]
   >시작 기능은 현재 다단계 인증을 사용하는 계정을 지원하지 않습니다.

- 구독에 대한 **소유자 권한** 이 있는 계정.

AD DS(Active Directory Domain Services)가 있는 환경에서 시작 기능을 사용하는 경우 다음 요구 사항도 충족해야 합니다.

- AD DS 도메인 관리자 자격 증명.
- 구독에서 Azure AD Connect를 구성하고 "USERS" 컨테이너가 Azure AD와 동기화되는지 확인해야 합니다.
- VM(가상 머신)의 도메인 컨트롤러에는 **Microsoft.Powershell.DSC** 유형의 DSC 확장이 없어야 합니다.

ID 공급자가 없는 환경에서 시작 기능을 사용하는 경우 따라야 하는 추가 요구 사항은 다음과 같습니다.

- AD 도메인 조인 UPN에는 [사용자 이름 지침 목록에서 허용하지 않는](../virtual-machines/windows/faq.yml#what-are-the-username-requirements-when-creating-a-vm-) 키워드가 포함되어서는 안 되며 Azure AD 구독에 없는 고유한 사용자 이름을 사용해야 합니다.
- 시작 기능으로 만든 세션 호스트를 추가하려면 새 호스트 풀을 만들어야 합니다. 기존 호스트 풀에서 세션 호스트를 만들려고 하면 작동하지 않습니다.

## <a name="for-subscriptions-with-azure-ad-ds-or-ad-ds"></a>Azure AD DS 또는 AD DS 구독의 경우

Azure AD DS 또는 AD DS가 이미 있는 구독에서 시작 기능을 사용하는 방법은 다음과 같습니다.

1. [Azure Portal](https://portal.azure.com)을 엽니다.

2. Azure에 로그인하고 **Azure Virtual Desktop 관리** 를 연 다음 **시작** 탭을 선택합니다. 그러면 시작 기능의 방문 페이지가 열립니다.

3. **만들기** 를 선택합니다.

4. **기본** 탭에서 다음 값을 선택합니다.

    - **구독** 의 경우 **구독 구성 방법** 으로 이동한 다음 **기존 설정** 을 선택합니다.

    - **위치** 에서 리소스를 배포할 위치를 선택합니다.

    - **Azure 관리자 UPN** 에 Azure AD의 관리자 권한과 사용하려는 구독의 소유자 권한이 있는 계정의 전체 UPN(사용자 계정 이름)을 입력합니다.

    - **AD 도메인 조인 UPN** 에 VM을 도메인에 조인하는 데 사용할 권한이 있는 계정의 전체 UPN을 입력합니다.

    - **ID** 에 대해 환경에 따라 **Azure AD DS** 또는 **AD DS** 를 선택합니다. 여기에서 선택하는 항목은 VM에 필요한 입력에 영향을 줍니다.

5. **가상 머신** 탭에서 다음 값을 선택합니다.

    - **사용자가 이 컴퓨터를 공유하도록 할까요?** 에서 필요에 따라 다음 옵션 중 하나를 선택합니다.
      - 단일 세션 또는 개인 호스트 풀을 만들려면 **아니요** 를 선택합니다.
      - 다중 세션 또는 풀링된 호스트 풀을 만들려면 **예(다중 세션)** 를 선택합니다. 그러면 Azure AD DS 또는 AD DS에 조인된 Azure Files 스토리지 계정도 만들어집니다.

    - **이미지 유형** 에서 Azure 이미지 갤러리의 이미지, 사용자 지정 이미지 또는 Storage Blob의 VHD를 선택합니다.

    - **VM 크기** 에서 배포할 VM에 대해 원하는 크기와 SKU를 선택합니다.

    - **VM 수** 에서 호스트 풀에 프로비저닝할 VM 수를 선택합니다.

    - AD DS와 함께 기존 설정을 사용하는 경우 다음 옵션이 나타납니다.

       - **서브넷** 에서 VNET의 서브넷을 선택합니다. 선택하는 서브넷은 ID(AD DS 또는 Azure AD DS)와 동일한 위치에 있거나 피어링되어야 합니다.

       - **도메인 컨트롤러 리소스 그룹** 에서 AD DS VM이 있거나 피어링된 리소스 그룹을 선택합니다. 도메인 컨트롤러가 있는 리소스 그룹은 동일한 구독에 있어야 합니다. 시작 기능은 현재 피어링된 구독을 지원하지 않습니다.

       - **도메인 컨트롤러 가상 머신** 에 배포의 AD DS를 실행하는 VM의 이름을 입력합니다.

    - Azure AD 사용자 또는 사용자 그룹 선택을 열려면 **기존 사용자 할당** 확인란을 선택합니다.

    - 배포를 테스트하기 위해 유효성 검사 사용자 계정을 만들려면 **유효성 검사 사용자 만들기** 확인란을 선택한 다음 표시되는 프롬프트에 사용자 이름과 암호를 입력합니다.

       >[!NOTE]
       >시작하면 "USERS" 컨테이너에 유효성 검사 사용자 그룹이 만들어집니다. 유효성 검사 그룹이 Azure AD에 동기화되었는지 확인해야 합니다. 동기화가 작동하지 않으면 Azure AD에 동기화되는 조직 단위에서 AVDValidationUsers 그룹을 미리 만듭니다.

## <a name="for-subscriptions-without-azure-ad-ds-or-ad-ds"></a>Azure AD DS 또는 AD DS가 없는 구독의 경우

이 섹션에서는 Azure AD DS 또는 AD DS 없이 구독을 시작하는 기능을 사용하는 방법을 보여 줍니다. 참고로 이러한 구독을 "빈" 구독이라고도 합니다.

Azure AD DS 또는 AD DS가 없는 구독에 Azure Virtual Desktop을 배포하려면:

1. [Azure Portal](https://portal.azure.com)을 엽니다.

2. Azure에 로그인하고 **Azure Virtual Desktop 관리** 를 연 다음 **시작** 탭을 선택합니다. 그러면 시작 기능의 방문 페이지가 열립니다.

3. **기본** 탭에서 다음 값을 선택합니다.

    - **구독** 에서 Azure Virtual Desktop을 배포할 구독을 선택합니다.

    - **구독 구성 방법** 에서 **빈 구독** 을 선택합니다. "빈" 구독은 Azure AD 또는 AD DS와 같은 ID 공급자가 필요하지 않은 구독입니다.

    - **리소스 그룹 접두사** 에 만들려는 리소스 그룹의 접두사( *-prerequisite*, *-deployment* 및 *-avd*)를 입력합니다.

    - **위치** 에 배포에 사용할 리소스 위치를 입력합니다.

    - **Azure 관리자 UPN** 에 Azure AD에 대한 관리자 권한과 구독에 대한 소유자 권한이 있는 계정의 전체 UPN을 입력합니다.

    - **AD 도메인 조인 UPN** 에 **AAD DC 관리자** 그룹에 추가될 계정의 전체 UPN을 입력합니다.

    >[!NOTE]
    >AD 도메인 조인 UPN의 사용자 이름은 Azure AD에 아직 존재하지 않는 고유한 이름이어야 합니다. 시작 기능은 현재 Azure AD 또는 AD DS가 없는 계정에 대한 기존 Azure AD 사용자 이름 사용을 지원하지 않습니다.

4. **가상 머신** 탭에서 다음 값을 선택합니다.

    - **사용자가 이 컴퓨터를 공유하도록 할까요?** 에서 필요에 따라 다음 옵션 중 하나를 선택합니다.
      - 단일 세션 또는 개인 호스트 풀을 만들려면 **아니요** 를 선택합니다.
      - 다중 세션 또는 풀링된 호스트 풀을 만들려면 **예(다중 세션)** 를 선택합니다. 그러면 Azure AD DS 또는 AD DS에 조인된 Azure Files 스토리지 계정도 만들어집니다.

    - **이미지 유형** 에서 Azure 이미지 갤러리의 이미지, 사용자 지정 이미지 또는 Storage Blob의 VHD를 선택합니다.

    - **VM 크기** 에서 배포할 VM에 대해 원하는 크기와 SKU를 선택합니다.

    - **VM 수** 에서 호스트 풀에 프로비저닝할 VM 수를 선택합니다.

5. **할당** 탭에서 **유효성 검사 사용자 만들기** 를 선택한 다음 **유효성 검사 사용자 이름** 및 **유효성 검사 사용자 암호** 필드에 사용자 이름과 암호를 입력합니다. 유효성 검사 사용자는 배포가 준비되면 테스트할 사용자입니다.

## <a name="clean-up-resources"></a>리소스 정리

배포 후 마음이 바뀌어 추가 청구 비용 없이 사용자 환경의 Azure Virtual Desktop 리소스를 제거하려는 경우 이 섹션의 지침에 따라 안전하게 제거할 수 있습니다.

Azure AD DS 또는 AD DS가 있는 구독에서 리소스를 만든 경우 이 기능은 접두사 " *-deployment*" 및 " *-avd*"를 사용하여 두 개의 리소스 그룹을 만듭니다. Azure Portal에서 **리소스 그룹** 으로 이동하고 해당 접두사가 있는 리소스 그룹을 삭제하여 배포를 제거합니다.

Azure AD DS 또는 AD DS 없이 구독에서 리소스를 만든 경우 이 기능은 *-prerequisite*, *-deployment* 및 *-avd* 접두사가 있는 세 개의 리소스 그룹을 만듭니다. Azure Portal에서 **리소스 그룹** 으로 이동하고 해당 접두사가 있는 리소스 그룹을 삭제하여 배포를 제거합니다.

## <a name="next-steps"></a>다음 단계

Azure Virtual Desktop을 더 심층적으로 배포하는 방법을 알아보려면 [Azure Portal로 호스트 풀 만들기](create-host-pools-azure-marketplace.md)부터 시작하여 수동으로 배포를 설정하는 자습서를 확인하세요.
