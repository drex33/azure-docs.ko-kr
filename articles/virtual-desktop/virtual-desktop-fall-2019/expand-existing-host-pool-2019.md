---
title: 새 세션 호스트를 사용하여 기존 Azure Virtual Desktop(클래식) 호스트 풀 확장 - Azure
description: Azure Virtual Desktop(클래식)에서 새 세션 호스트를 사용하여 기존 호스트 풀을 확장하는 방법입니다.
author: Heidilohr
ms.topic: how-to
ms.date: 03/31/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: e459737db621dfb55b5534e60989437b293e57a9
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111745082"
---
# <a name="expand-an-existing-host-pool-with-new-session-hosts-in-azure-virtual-desktop-classic"></a>Azure Virtual Desktop(클래식)에서 새 세션 호스트를 사용하여 기존 호스트 풀 확장

>[!IMPORTANT]
>이 콘텐츠는 Azure Resource Manager Azure Virtual Desktop 개체를 지원하지 않는 Azure Virtual Desktop(클래식)에 적용됩니다. Azure Resource Manager Azure Virtual Desktop 개체를 관리하려는 경우 [이 문서](../expand-existing-host-pool.md)를 참조하세요.

호스트 풀 내에서 사용량을 늘릴 때 새 부하를 처리하기 위해 새 세션 호스트를 사용하여 기존 호스트 풀을 확장해야 할 수 있습니다.

이 문서에서는 새 세션 호스트를 사용하여 기존 호스트 풀을 확장하는 방법을 설명합니다.

## <a name="what-you-need-to-expand-the-host-pool"></a>호스트 풀을 확장하는 데 필요한 항목

시작하기 전에 다음 방법 중 하나를 사용하여 호스트 풀 및 세션 호스트 VM(가상 머신)을 만들었는지 확인합니다.

- [Azure Marketplace 제품](create-host-pools-azure-marketplace-2019.md)
- [GitHub Azure Resource Manager 템플릿](create-host-pools-arm-template.md)
- [PowerShell을 사용한 호스트 풀 만들기](create-host-pools-powershell-2019.md)

또한 호스트 풀 및 세션 호스트 VM을 처음 만들 때 사용한 다음 정보가 필요합니다.

- VM 크기, 이미지 및 이름 접두사
- 도메인 가입 및 Azure Virtual Desktop 테넌트 관리자 자격 증명
- 가상 네트워크 이름 및 서브넷 이름

다음 세 개의 섹션에서는 호스트 풀을 확장하는 데 사용할 수 있는 세 가지 방법을 설명합니다. 익숙한 배포 도구 중 하나를 사용하여 확장할 수 있습니다.

>[!NOTE]
>배포 단계 중에 이전 세션 호스트 VM 리소스가 현재 종료된 경우 해당 리소스에 대한 오류 메시지가 표시됩니다. 이러한 오류는 Azure에서 PowerShell DSC 확장을 실행하여 세션 호스트 VM이 기존 호스트 풀에 올바르게 등록되었는지 확인할 수 없기 때문에 발생합니다. 이름이 "-0"으로 끝나는 세션 호스트가 실행 중이어야 하지만 다른 세션 호스트에서 이러한 오류를 무시해도 되고, 배포 프로세스를 시작하기 전에 기존 호스트 풀에서 모든 세션 호스트 VM을 시작하여 오류를 방지할 수 있습니다.

## <a name="redeploy-from-azure"></a>Azure에서 다시 배포

[Azure Marketplace 제품](create-host-pools-azure-marketplace-2019.md) 또는 [GitHub Azure Resource Manager 템플릿](create-host-pools-arm-template.md)을 사용하여 호스트 풀 및 세션 호스트 VM을 이미 만든 경우, Azure Portal에서 동일한 템플릿을 다시 배포할 수 있습니다. 템플릿을 다시 배포하면 암호를 제외하고 원래 템플릿에 입력한 모든 정보가 자동으로 다시 입력됩니다.

호스트 풀을 확장하기 위해 Azure Resource Manager 템플릿을 다시 배포하는 방법은 다음과 같습니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. Azure Portal 상단에 있는 검색 창에서 **리소스 그룹** 을 검색하고 **서비스** 아래에서 항목을 선택합니다.
3. 호스트 풀을 만들 때 만든 리소스 그룹을 찾아 선택합니다.
4. 브라우저 왼쪽에 있는 패널에서 **배포** 를 선택합니다.
5. 호스트 풀 만들기 프로세스에 적절한 배포를 선택합니다.
     - Azure Marketplace 제품을 사용하여 원래 호스트 풀을 만든 경우에는 **rds.wvd-provision-host-pool** 로 시작하는 배포를 선택합니다.
     - GitHub Azure Resource Manager 템플릿을 사용하여 원래 호스트 풀을 만든 경우에는 **Microsoft.Template** 이라는 배포를 선택합니다.
6. **재배포** 를 선택합니다.

     >[!NOTE]
     >**재배포** 를 선택했는데 템플릿이 자동으로 재배포되지 않으면 브라우저 왼쪽에 있는 패널에서 **템플릿** 을 선택한 다음, **배포** 를 선택합니다.

7. 기존 호스트 풀의 현재 세션 호스트 VM이 포함 된 리소스 그룹을 선택합니다.

     >[!NOTE]
     >입력한 리소스 그룹이 올바른 경우에도 다른 리소스 그룹을 선택하라는 오류가 표시되면 다른 리소스 그룹을 선택한 다음, 원래 리소스 그룹을 선택합니다.

8. *_artifactsLocation* 에 `https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/`라는 URL을 입력합니다.
9. 원하는 세션 호스트의 새 총 수를 *Rdsh 인스턴스 수* 에 입력합니다. 예를 들어 호스트 풀을 세션 호스트 5개에서 8개로 확장하는 경우 **8** 을 입력합니다.
10. 기존 도메인 UPN에 사용한 것과 동일한 기존 도메인 암호를 입력합니다. 사용자 이름은 변경하지 마세요. 그러면 템플릿을 실행할 때 오류가 발생합니다.
11. *테넌트 관리자 UPN 또는 애플리케이션 ID* 에 입력한 사용자 또는 애플리케이션 ID에 사용한 것과 동일한 테넌트 관리자 암호를 입력합니다. 다시 말하지만, 사용자 이름은 변경하지 마세요.
12. 제출을 완료하여 호스트 풀을 확장합니다.

## <a name="run-the-azure-marketplace-offering"></a>Azure Marketplace 제품 실행

[Azure Marketplace 제품을 실행하여 새 호스트 풀 프로비전](create-host-pools-azure-marketplace-2019.md#run-the-azure-marketplace-offering-to-provision-a-new-host-pool)에 도달할 때까지 [Azure Marketplace를 사용하여 호스트 풀 만들기](create-host-pools-azure-marketplace-2019.md)의 지침을 따릅니다. 이 시점에 도달하면 각 탭에 다음 정보를 입력해야 합니다.

### <a name="basics"></a>기본 사항

이 섹션에서 *기본 데스크톱 사용자* 를 제외한 모든 값은 호스트 풀 및 세션 호스트 VM을 처음 만들 때 입력한 값과 일치해야 합니다.

1.    *구독* 에서 호스트 풀을 처음 만든 구독을 선택합니다.
2.    *리소스 그룹* 에서 기존 호스트 풀 세션 호스트 VM이 있는 리소스 그룹을 선택합니다.
3.    *지역* 에서 기존 호스트 풀 세션 호스트 VM이 있는 지역을 선택합니다.
4.    *호스트 풀 이름* 에 기존 호스트 풀의 이름을 입력합니다.
5.    *데스크톱 유형* 에서 기존 호스트 풀과 일치하는 데스크톱 유형을 선택합니다.
6.    *기본 데스크톱 사용자* 에서 Azure Marketplace 제품이 완료된 후 Azure Virtual Desktop 클라이언트에 로그인하고 데스크톱에 액세스하도록 할 추가 사용자를 쉼표로 구분된 목록 형식으로 입력합니다. 예를 들어 user3@contoso.com 및 user4@contoso.com 액세스 권한을 할당하려면 user3@contoso.com, user4@contoso.com을 입력합니다.
7.    **다음: 가상 머신 구성** 을 선택합니다.

>[!NOTE]
>*기본 데스크톱 사용자* 를 제외한 모든 필드는 기존 호스트 풀에 구성된 것과 정확히 일치해야 합니다. 일치하지 않는 항목이 있으면 새 호스트 풀이 생성됩니다.

### <a name="configure-virtual-machines"></a>가상 머신 구성

이 섹션에서 총 VM 수를 제외한 모든 매개 변수 값은 호스트 풀 및 세션 호스트 VM을 처음 만들 때 입력한 값과 일치해야 합니다. 입력한 VM 수는 확장된 호스트 풀의 VM 수가 됩니다.

1. 기존 세션 호스트 VM과 일치하는 VM 크기를 선택합니다.

    >[!NOTE]
    >찾고 있는 특정 VM 크기가 VM 크기 선택기에 표시되지 않는 경우, 이는 Azure Marketplace 도구에 아직 등록되지 않았기 때문입니다.

2. *사용 프로필*, *총 사용자* 및 *가상 머신 수* 매개 변수를 사용자 지정하여 호스트 풀에 포함할 총 세션 호스트 수를 선택합니다. 예를 들어 호스트 풀을 세션 호스트 5개에서 8개로 확장하는 경우 가상 머신 8개를 가져오도록 이 옵션을 구성합니다.
3. 가상 머신의 이름에 사용할 접두사를 입력합니다. 예를 들어, “prefix”라는 이름을 입력하는 경우 가상 머신은 “prefix-0”, “prefix-1” 등으로 불립니다.
4. **다음: 가상 머신 설정** 을 선택합니다.

### <a name="virtual-machine-settings"></a>가상 머신 설정

이 섹션에서 모든 매개 변수 값은 호스트 풀 및 세션 호스트 VM을 처음 만들 때 입력한 값과 일치해야 합니다.

1. *이미지 원본* 및 *이미지 OS 버전* 에서 호스트 풀을 처음 만들 때 지정한 것과 동일한 정보를 입력합니다.
2. *AD 도메인 가입 UPN* 및 연결된 암호에서 VM을 Active Directory 도메인에 가입시키기 위해 호스트 풀을 처음 만들 때 지정한 것과 동일한 정보를 입력합니다. 이러한 자격 증명을 사용하여 가상 머신에 로컬 계정을 만들 수 있습니다. 나중에 이러한 로컬 계정을 다시 설정하여 자격 증명을 변경할 수 있습니다.
3. 가상 네트워크 정보에 기존 호스트 풀 세션 호스트 VM이 있는 위치와 동일한 가상 네트워크 및 서브넷을 선택합니다.
4. **다음: Azure Virtual Desktop 정보 구성** 을 선택합니다.

### <a name="azure-virtual-desktop-information"></a>Azure Virtual Desktop 정보

이 섹션에서 모든 매개 변수 값은 호스트 풀 및 세션 호스트 VM을 처음 만들 때 입력한 값과 일치해야 합니다.

1. *Azure Virtual Desktop 테넌트 그룹 이름* 의 경우, 테넌트를 포함하는 테넌트 그룹의 이름을 입력합니다. 특정 테넌트 그룹 이름이 제공되지 않은 경우 기본값을 그대로 유지합니다.
2. *Azure Virtual Desktop 테넌트 이름* 의 경우, 이 호스트 풀을 만들 테넌트의 이름을 입력합니다.
3. 호스트 풀 및 세션 호스트 VM을 처음 만들 때 사용한 것과 동일한 자격 증명을 지정합니다. 서비스 주체를 사용하는 경우 서비스 주체가 있는 Azure Active Directory 인스턴스의 ID를 입력합니다.
4. **다음: 검토 + 만들기** 를 선택합니다.

## <a name="run-the-github-azure-resource-manager-template"></a>GitHub Azure Resource Manager 템플릿 실행

[Azure Resource Manager 템플릿을 실행하여 새 호스트 풀 프로비저닝](create-host-pools-arm-template.md#run-the-azure-resource-manager-template-for-provisioning-a-new-host-pool)의 지침을 따르고, *Rdsh 인스턴스 수* 를 제외한 모든 매개 변수 값을 동일하게 지정합니다. 템플릿을 실행한 후 호스트 풀에서 사용하려는 세션 호스트 VM 수를 입력합니다. 예를 들어 호스트 풀을 세션 호스트 5개에서 8개로 확장하는 경우 **8** 을 입력합니다.

## <a name="next-steps"></a>다음 단계

이제 기존 호스트 풀을 확장했으므로 Azure Virtual Desktop 클라이언트에 로그인하여 사용자 세션의 일부로 테스트할 수 있습니다. 다음 클라이언트 중 하나를 사용하여 세션에 연결할 수 있습니다.

- [Windows Desktop 클라이언트를 사용하여 연결](connect-windows-7-10-2019.md)
- [웹 클라이언트를 사용하여 연결](connect-web-2019.md)
- [Android 클라이언트와 연결](connect-android-2019.md)
- [macOS 클라이언트와 연결](connect-macos-2019.md)
- [iOS 클라이언트와 연결](connect-ios-2019.md)
