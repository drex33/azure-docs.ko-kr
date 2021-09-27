---
title: FSLogix 프로필 컨테이너 NetApp Azure Virtual Desktop - Azure
description: Azure Virtual Desktop에서 Azure NetApp Files를 사용하여 FSLogix 프로필 컨테이너를 만드는 방법입니다.
author: Heidilohr
ms.topic: how-to
ms.date: 06/05/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: e4f4a7149f522d11a9a77bdeb9b16a0f848cba77
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124776865"
---
# <a name="create-a-profile-container-with-azure-netapp-files-and-ad-ds"></a>Azure NetApp Files 및 AD DS를 사용하여 프로필 컨테이너 만들기

[Azure Virtual Desktop 서비스](overview.md)에 대한 사용자 프로필 솔루션으로 FSLogix 프로필 컨테이너를 사용하는 것이 좋습니다. FSLogix 프로필 컨테이너는 전체 사용자 프로필을 단일 컨테이너에 저장하고 Azure Virtual Desktop과 같은 비영구적 원격 컴퓨팅 환경에서 프로필을 로밍하도록 설계되었습니다. 로그인하면 컨테이너가 로컬에서 지원되는 VHD(가상 하드 디스크) 및 VHDX(Hyper-V 가상 하드 디스크)를 사용하여 컴퓨팅 환경에 동적으로 연결됩니다. 이러한 고급 필터 드라이버 기술을 사용하면 사용자 프로필을 즉시 사용할 수 있고 로컬 사용자 프로필과 똑같이 시스템에 표시할 수 있습니다. FSLogix 프로필 컨테이너에 대한 자세한 내용은 [FSLogix 프로필 컨테이너 및 Azure 파일](fslogix-containers-azure-files.md)을 참조하세요.

고객이 Azure Virtual Desktop 환경을 위한 엔터프라이즈급 SMB 볼륨을 빠르고 안정적으로 프로비전하는 데 도움이 되는 사용하기 쉬운 Azure 네이티브 플랫폼 서비스인 [Azure NetApp Files](https://azure.microsoft.com/services/netapp/)를 사용하여 FSLogix 프로필 컨테이너를 만들 수 있습니다. Azure NetApp Files에 대한 자세한 내용은 [Azure NetApp Files란?](../azure-netapp-files/azure-netapp-files-introduction.md)을 참조하세요.

이 가이드는 Azure Virtual Desktop에서 Azure NetApp Files 계정을 설정하고 FSLogix 프로필 컨테이너를 만드는 방법을 보여줍니다.

이 문서에서는 Azure Virtual Desktop 환경에서 이미 [호스트 풀](create-host-pools-azure-marketplace.md)을 설정하고 하나 이상의 테넌트로 그룹화했다고 가정합니다. 테넌트를 설정하는 방법을 알아보려면 [ Virtual Desktop에서 테넌트 만들기](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md) 및 [기술 커뮤니티 블로그 게시물](https://techcommunity.microsoft.com/t5/Windows-IT-Pro-Blog/Getting-started-with-Windows-Virtual-Desktop/ba-p/391054)을 참조하세요.

이 가이드의 지침은 특히 Azure Virtual Desktop 사용자를 위한 것입니다. Azure Virtual Desktop 외부에서 Azure NetApp Files를 설정하고 FSLogix 프로필 컨테이너를 만드는 방법에 대한 일반적인 지침을 찾고 있다면 [Azure NetApp 파일 설정 및 NFS 볼륨 만들기 빠른 시작](../azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes.md)을 참조하세요.

>[!NOTE]
>이 문서에서는 Azure NetApp Files 공유에 대한 액세스 보안을 위한 모범 사례를 다루지 않습니다.

>[!NOTE]
>Azure의 다양한 FSLogix 프로필 컨테이너 스토리지 옵션에 대한 비교 자료를 찾고 있다면 [FSLogix 프로필 컨테이너의 스토리지 옵션](store-fslogix-profile.md)을 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

호스트 풀에 대한 FSLogix 프로필 컨테이너를 만들려면 먼저 다음을 수행해야 합니다.

- Azure Virtual Desktop 설정 및 구성
- Azure Virtual Desktop 호스트 풀 프로비전

## <a name="set-up-your-azure-netapp-files-account"></a>Azure NetApp Files 계정 설정

시작하려면 Azure NetApp Files 계정을 설정해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 계정에 기여자 또는 관리자 권한이 있는지 확인합니다.

2. 검색 창 오른쪽에 있는 **Azure Cloud Shell 아이콘** 을 선택하여 Azure Cloud Shell을 엽니다.

3. Azure Cloud Shell이 열리면 **PowerShell** 을 선택합니다.

4. Azure Cloud Shell을 처음 사용하는 경우 Azure NetApp Files 및 Azure Virtual Desktop을 유지하는 동일한 구독에서 스토리지 계정을 만듭니다.

   > [!div class="mx-imgBorder"]
   > ![창 아래쪽에 스토리지 만들기 단추가 있는 스토리지 계정 창이 빨간색으로 강조 표시됩니다.](media/create-storage-button.png)

5. Azure Cloud Shell이 로드되면 다음 두 cmdlet을 실행합니다.

   ```azurecli
   az account set --subscription <subscriptionID>
   ```

   ```azurecli
   az provider register --namespace Microsoft.NetApp --wait
   ```

6. 창 왼쪽에서 **모든 서비스** 를 선택합니다. 메뉴 맨 위에 표시되는 검색 상자에 **Azure NetApp Files** 를 입력합니다.

   > [!div class="mx-imgBorder"]
   > ![모든 서비스 검색 상자에 "Azure NetApp Files"를 입력하는 사용자의 스크린샷. 검색 결과에 Azure NetApp Files 리소스가 표시됩니다.](media/azure-netapp-files-search-box.png)


7. 검색 결과에서 **Azure NetApp 파일** 을 선택한 다음, **만들기** 를 선택합니다.

8. **추가** 단추를 선택합니다.
9. **새 NetApp 계정** 탭이 열리면 다음 값을 입력합니다.

    - **이름** 에 NetApp 계정 이름을 입력합니다.
    - **구독** 의 경우 드롭다운 메뉴에서 4단계에서 설정한 스토리지 계정에 대한 구독을 선택합니다.
    - **리소스 그룹** 의 경우 드롭다운 메뉴에서 기존 리소스 그룹을 선택하거나 **새로 만들기** 를 선택하여 새 리소스 그룹을 만듭니다.
    - **위치** 의 경우 드롭다운 메뉴에서 NetApp 계정의 지역을 선택합니다. 이 지역은 세션 호스트 VM과 동일한 지역이어야 합니다.

   >[!NOTE]
   >Azure NetApp Files는 현재 지역 간 볼륨 탑재를 지원하지 않습니다.

10. 완료되면 **만들기** 를 선택하여 NetApp 계정을 만듭니다.

## <a name="create-a-capacity-pool"></a>용량 풀 만들기

그 다음, 새 용량 풀을 만듭니다.

1. Azure NetApp 파일 메뉴로 이동하고 새 계정을 선택합니다.
2. 계정 메뉴에서 스토리지 서비스 아래의 **용량 풀** 을 선택합니다.
3. **풀 추가** 를 선택합니다.
4. **새 용량 풀** 탭이 열리면 다음 값을 입력합니다.

    - **이름** 에 새 용량 풀의 이름을 입력합니다.
    - **서비스 수준** 의 경우 드롭다운 메뉴에서 원하는 값을 선택합니다. 대부분의 환경에서 **프리미엄** 을 권장합니다.
       >[!NOTE]
       >프리미엄 설정은 프리미엄 서비스 수준에 사용할 수 있는 최소 처리량(256MBps)을 제공합니다. 프로덕션 환경에 맞게 이 처리량을 조정해야 할 수 있습니다. 최종 처리량은 [처리량 제한](../azure-netapp-files/azure-netapp-files-service-levels.md)에 설명된 관계를 기반으로 합니다.
    - **크기(TiB)** 에는 가장 적합한 용량 풀 크기를 입력합니다. 최소 크기는 4TiB입니다.

5. 작업을 마쳤으면 **확인** 을 선택합니다.

## <a name="join-an-active-directory-connection"></a>Active Directory 연결 참가

그런 다음, Active Directory 연결에 참가해야 합니다.

1. 페이지 왼쪽에 있는 메뉴에서 **Active Directory 연결** 을 선택한 다음, **참가** 단추를 선택하여 **Active Directory 참가** 페이지를 엽니다.

   > [!div class="mx-imgBorder"]
   > ![Active Directory 연결 참가 메뉴의 스크린샷.](media/active-directory-connections-menu.png)

2. **Active Directory 참가** 페이지에서 다음 값을 입력하여 연결을 참가합니다.

    - **주 DNS** 의 경우 도메인 이름을 확인할 수 있는 환경에 DNS 서버의 IP 주소를 입력합니다.
    - **도메인** 에는 FQDN(정규화된 도메인 이름)을 입력합니다.
    - **SMB 서버(컴퓨터 계정) 접두사** 에는 컴퓨터 계정 이름에 추가하려는 문자열을 입력합니다.
    - **사용자 이름** 에는 도메인 가입을 수행할 수 있는 권한이 있는 계정의 이름을 입력합니다.
    - **암호** 에는 계정의 암호를 입력합니다.

## <a name="create-a-new-volume"></a>새 볼륨 만들기

그 다음, 새 볼륨을 만들어야 합니다.

1. **볼륨** 을 선택한 다음, **볼륨 추가** 를 선택합니다.

2. **볼륨 만들기** 탭이 열리면 다음 값을 입력합니다.

    - **볼륨 이름** 에 새 볼륨의 이름을 입력합니다.
    - **용량 풀** 의 경우 드롭다운 메뉴에서 방금 만든 용량 풀을 선택합니다.
    - **할당량(GiB)** 에는 환경에 적합한 볼륨 크기를 입력합니다.
    - **가상 네트워크** 의 경우 드롭다운 메뉴에서 도메인 컨트롤러에 연결되어 있는 기존 가상 네트워크를 선택합니다.
    - **서브넷** 에서 **새로 만들기** 를 선택합니다. 이 서브넷은 Azure NetApp Files에 위임됩니다.

3.  **다음: 프로토콜 \>\>** 을 선택하여 프로토콜 탭을 열고 볼륨 액세스 매개 변수를 구성합니다.

## <a name="configure-volume-access-parameters"></a>볼륨 액세스 매개 변수 구성

볼륨을 만든 후에는 볼륨 액세스 매개 변수를 구성합니다.

1.  프로토콜 유형으로 **SMB** 를 선택합니다.
2.  **Active Directory** 드롭다운 메뉴의 구성에서, [Active Directory 연결 참가](create-fslogix-profile-container.md#join-an-active-directory-connection)에서 원래 연결한 것과 동일한 디렉터리를 선택합니다. 구독당 Active Directory는 1개로 제한됩니다.
3.  **공유 이름** 텍스트 상자에 세션 호스트 풀 및 해당 사용자가 사용하는 공유의 이름을 입력합니다.

4.  페이지 아래쪽에서 **검토 + 만들기** 를 선택합니다. 그러면 유효성 검사 페이지가 열립니다. 볼륨의 유효성 검사가 성공하면 **만들기** 를 선택합니다.

5.  이때 새 볼륨이 배포되기 시작합니다. 배포가 완료되면 Azure NetApp Files 공유를 사용할 수 있습니다.

6.  탑재 경로를 보려면 **리소스로 이동** 을 선택하고 개요 탭에서 찾습니다.

    > [!div class="mx-imgBorder"]
    > ![탑재 경로를 가리키는 빨간색 화살표가 있는 개요 화면의 스크린샷.](media/overview-mount-path.png)

## <a name="configure-fslogix-on-session-host-virtual-machines-vms"></a>세션 호스트 VM(가상 머신)에서 FSLogix 구성

이 섹션은 [파일 공유를 사용하여 호스트 풀에 대한 프로필 컨테이너 만들기](create-host-pools-user-profile.md)를 기반으로 합니다.

1. 세션 호스트 VM에서 원격 상태에 있는 동안 [FSLogix 에이전트 .zip 파일을 다운로드](https://go.microsoft.com/fwlink/?linkid=2084562&clcid=0x409)합니다.

2. 다운로드한 파일의 압축을 풉니다.

3. 파일에서 **x64** > **릴리스** 로 이동하여 **FSLogixAppsSetup.exe** 를 실행합니다. 설치 메뉴가 열립니다.

4.  제품 키가 있는 경우 제품 키 텍스트 상자에 입력합니다.

5. **사용 약관에 동의함** 옆의 확인란을 선택합니다.

6. **설치** 를 선택합니다.

7. **C:\\Program Files\\FSLogix\\Apps** 으로 이동하여 에이전트가 설치되었는지 확인합니다.

8. 시작 메뉴에서 관리자로 **RegEdit** 를 실행합니다.

9. **컴퓨터\\HKEY_LOCAL_MACHINE\\software\\FSLogix** 로 이동합니다.

10. **Profiles** 이라는 키를 만듭니다.

11.  **REG_DWORD** 유형이 **1** 데이터 값으로 설정된 **Enabled** 라는 값을 만듭니다.

12. **다중 문자열** 유형을 사용하여 **VHDLocations** 라는 값을 만들고 해당 데이터 값을 Azure NetApp Files 공유에 대한 URI로 설정합니다.

13. 로그인하기 전에 기존 로컬 프로필의 문제를 방지하려면 DWORD 값이 1인 **DeleteLocalProfileWhenVHDShouldApply** 라는 값을 만듭니다.

     >[!WARNING]
     >DeleteLocalProfileWhenVHDShouldApply 값을 만들 때는 주의해야 합니다. FSLogix 프로필 시스템이 사용자에게 FSLogix 프로필이 있어야 하는데 로컬 프로필이 있다고 판단하면, 프로필 컨테이너는 로컬 프로필을 영구적으로 삭제합니다. 그러면 사용자는 새 FSLogix 프로필로 로그인됩니다.

## <a name="assign-users-to-session-host"></a>세션 호스트에 사용자 할당

1. 관리자로 **PowerShell ISE** 를 열고 Azure Virtual Desktop에 로그인합니다.

2. 다음 cmdlet을 실행합니다.

   ```powershell
   Import-Module Microsoft.RdInfra.RdPowershell
   # (Optional) Install-Module Microsoft.RdInfra.RdPowershell
   $brokerurl = "https://rdbroker.wvd.microsoft.com"
   Add-RdsAccount -DeploymentUrl $brokerurl
   ```

3. 자격 증명을 입력하라는 메시지가 표시되면 Azure Virtual Desktop 테넌트에서 테넌트 작성자 또는 RDS 소유자/RDS 기여자 역할이 있는 사용자의 자격 증명을 입력합니다.

4. 다음 cmdlet을 실행하여 사용자를 원격 데스크톱 그룹에 할당합니다.

   ```powershell
   $wvdTenant = "<your-wvd-tenant>"
   $hostPool = "<wvd-pool>"
   $appGroup = "Desktop Application Group"
   $user = "<user-principal>"
   Add-RdsAppGroupUser $wvdTenant $hostPool $appGroup $user
   ```

## <a name="make-sure-users-can-access-the-azure-netapp-file-share"></a>사용자가 Azure NetApp 파일 공유에 액세스할 수 있는지 확인합니다.

1. 인터넷 브라우저를 열고 <https://rdweb.wvd.microsoft.com/arm/webclient>으로 이동합니다.

2. 원격 데스크톱 그룹에 할당된 사용자의 자격 증명으로 로그인합니다.

3. 사용자 세션을 설정했으면 관리 계정으로 Azure Portal에 로그인합니다.

4. **Azure NetApp Files** 를 열고 Azure NetApp Files 계정을 선택한 다음, **볼륨** 을 선택합니다. 볼륨 메뉴가 열리면 해당 볼륨을 선택합니다.

   > [!div class="mx-imgBorder"]
   > ![볼륨 단추를 선택하여 Azure Portal에서 이전에 설정한 NetApp 계정의 스크린샷.](media/netapp-account.png)

5. **개요** 탭으로 이동하여 FSLogix 프로필 컨테이너가 공간을 사용하고 있는지 확인합니다.

6. 원격 데스크톱을 사용하여 호스트 풀의 VM 부분에 직접 연결하고 **파일 탐색기** 를 엽니다. 그런 다음, **탑재 경로** 로 이동합니다(다음 예에서 탑재 경로는 \\\\anf-SMB-3863.gt1107.onmicrosoft.com\\anf-VOL임).

   이 폴더에는 다음 예와 같은 프로필 VHD(또는 VHDX)가 있어야 합니다.

   > [!div class="mx-imgBorder"]
   > ![탑재 경로에 있는 폴더 내용의 스크린샷. 내부는 "Profile_ssbb"이라는 단일 VHD 파일입니다.](media/mount-path-folder.png)

## <a name="next-steps"></a>다음 단계

FSLogix 프로필 컨테이너를 사용하여 사용자 프로필 공유를 설정할 수 있습니다. 새 컨테이너로 사용자 프로필 공유를 만드는 방법을 알아보려면 [파일 공유를 사용하여 호스트 풀에 대한 프로필 컨테이너 만들기](create-host-pools-user-profile.md)를 참조하세요.

FSLogix 프로필을 저장할 Azure Files 파일 공유를 만들 수도 있습니다. 자세한 내용은 [도메인 컨트롤러를 사용하여 Azure Files 파일 공유 만들기](create-file-share.md)를 참조하세요.