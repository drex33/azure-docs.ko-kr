---
title: 새 세션 호스트를 사용하여 기존 호스트 풀 확장 - Azure
description: Azure Virtual Desktop에서 새 세션 호스트를 사용하여 기존 호스트 풀을 확장하는 방법입니다.
author: Heidilohr
ms.topic: how-to
ms.date: 07/14/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: f37e7e18fd32c3ad0b06f1189c57f44d72dced7a
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/14/2021
ms.locfileid: "113760861"
---
# <a name="expand-an-existing-host-pool-with-new-session-hosts"></a>새 세션 호스트를 사용하여 기존 호스트 풀 확장

>[!IMPORTANT]
>이 내용은 Azure Resource Manager Azure Virtual Desktop 개체를 통해 Azure Virtual Desktop에 적용됩니다. Azure Resource Manager 개체 없이 Azure Virtual Desktop(클래식)을 사용하는 경우 [이 문서](./virtual-desktop-fall-2019/expand-existing-host-pool-2019.md)를 참조하세요.

호스트 풀 내에서 사용량을 늘릴 때 새 부하를 처리하기 위해 새 세션 호스트를 사용하여 기존 호스트 풀을 확장해야 할 수 있습니다.

이 문서에서는 새 세션 호스트를 사용하여 기존 호스트 풀을 확장하는 방법을 설명합니다.

## <a name="what-you-need-to-expand-the-host-pool"></a>호스트 풀을 확장하는 데 필요한 항목

시작하기 전에 다음 방법 중 하나를 사용하여 호스트 풀 및 세션 호스트 VM(가상 머신)을 만들었는지 확인합니다.

- [Azure Portal](./create-host-pools-azure-marketplace.md)
- [PowerShell을 사용한 호스트 풀 만들기](./create-host-pools-powershell.md)

또한 호스트 풀 및 세션 호스트 VM을 처음 만들 때 사용한 다음 정보가 필요합니다.

- VM 크기, 이미지 및 이름 접두사
- 도메인 가입 관리자 자격 증명
- 가상 네트워크 이름 및 서브넷 이름

## <a name="add-virtual-machines-with-the-azure-portal"></a>Azure Portal로 가상 머신 추가

가상 머신을 추가하여 호스트 풀을 확장하려면:

1. Azure Portal에 로그인합니다.

2. **Azure Virtual Desktop** 을 검색하여 선택합니다.

3. 화면 왼쪽의 메뉴에서 **호스트 풀** 을 선택한 다음, 가상 머신을 추가할 호스트 풀의 이름을 선택합니다.

4. 화면 왼쪽의 메뉴에서 **세션 호스트** 를 선택합니다.

5. **+추가** 를 선택하여 호스트 풀 만들기를 시작합니다.

6. 기본 탭을 무시하고 대신 **VM 세부 정보** 탭을 선택합니다. 여기에서 호스트 풀에 추가하려는 VM(가상 머신)의 세부 정보를 확인하고 편집할 수 있습니다.

7. VM을 만들 리소스 그룹을 선택한 다음 지역을 선택합니다. 현재 사용 중인 지역 또는 새 지역을 선택할 수 있습니다.

8. 호스트 풀에 추가할 세션 호스트의 수를 **VM 수** 에 입력합니다. 예를 들어 호스트 풀을 5개 호스트로 확장할 경우 **5** 를 입력합니다.

    >[!NOTE]
    >VM의 이미지 및 접두사를 편집할 수 있더라도 동일한 호스트 풀에 다른 이미지를 사용하는 VM이 있는 경우에는 이를 편집하지 않는 것이 좋습니다. 영향을 받는 호스트 풀에서 이전 이미지를 사용하는 VM을 제거할 계획인 경우에만 이미지와 접두사를 편집하세요.

9. **가상 네트워크 정보** 를 보려면 가상 머신을 조인할 가상 네트워크 및 서브넷을 선택합니다. 현재 기존 머신에서 사용하는 것과 동일한 가상 네트워크를 선택하거나 7단계에서 선택한 지역에 더 적합한 다른 가상 네트워크를 선택할 수 있습니다.

10. **가입할 도메인** 에서 가상 머신을 Active Directory에 가입할지 또는 [Azure Active Directory](deploy-azure-ad-joined-vm.md)에 가입할지 선택합니다. **Intune에 VM 등록** 을 선택하면 가상 머신이 Intune에 자동으로 등록됩니다. 호스트 풀의 모든 가상 머신은 동일한 도메인 또는 Azure AD 테넌트에 가입되어야 합니다.

11. **AD 도메인 가입 UPN** 에는 선택한 도메인과 연결된 Active Directory 도메인 사용자 이름과 암호를 입력합니다. 해당 자격 증명은 가상 머신을 Active Directory 도메인에 가입하는 데 사용됩니다.

      >[!NOTE]
      >관리자 이름이 여기에 지정된 정보를 준수하는지 확인합니다. 그리고 계정에 MFA를 사용하도록 설정되어 있지 않은지도 확인합니다.

12. **Virtual Machine 관리자 계정** 의 경우 모든 가상 머신에 사용할 로컬 관리자 계정 정보를 입력합니다.

13. 가상 머신을 그룹화할 태그가 있는 경우 **태그** 탭을 선택합니다. 그렇지 않은 경우, 이 탭을 건너뜁니다.

14. **검토 + 만들기** 탭을 선택합니다. 선택 항목을 검토하고, 모든 것이 제대로 표시되면 **만들기** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

이제 기존 호스트 풀을 확장했으므로 Azure Virtual Desktop 클라이언트에 로그인하여 사용자 세션의 일부로 테스트할 수 있습니다. 다음 클라이언트 중 하나를 사용하여 세션에 연결할 수 있습니다.

- [Windows Desktop 클라이언트를 사용하여 연결](./user-documentation/connect-windows-7-10.md)
- [웹 클라이언트를 사용하여 연결](./user-documentation/connect-web.md)
- [Android 클라이언트와 연결](./user-documentation/connect-android.md)
- [macOS 클라이언트와 연결](./user-documentation/connect-macos.md)
- [iOS 클라이언트와 연결](./user-documentation/connect-ios.md)
