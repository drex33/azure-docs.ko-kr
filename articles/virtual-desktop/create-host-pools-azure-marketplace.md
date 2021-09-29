---
title: Azure Virtual Desktop 호스트 풀 - Azure Portal - Azure
description: Azure Portal을 사용하여 Azure Virtual Desktop 호스트 풀을 만드는 방법입니다.
author: Heidilohr
ms.topic: tutorial
ms.custom: references_regions
ms.date: 08/06/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 8e33c165cd249b578bd4cba9ef4f25446287d7ec
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124766448"
---
# <a name="tutorial-create-a-host-pool"></a>자습서: 호스트 풀 만들기

>[!IMPORTANT]
>이 콘텐츠는 Azure Resource Manager Azure Virtual Desktop 개체를 통해 Azure Virtual Desktop에 적용됩니다. Azure Resource Manager 개체 없이 Azure Virtual Desktop(클래식)을 사용하는 경우 [이 문서](./virtual-desktop-fall-2019/create-host-pools-azure-marketplace-2019.md)를 참조하세요. Azure Virtual Desktop(클래식)으로 만든 개체는 Azure Portal로 관리할 수 없습니다.

호스트 풀은 Azure Virtual Desktop 환경 내에서 ‘세션 호스트’라고도 하는 하나 이상의 동일한 VM(가상 머신) 컬렉션입니다. 각 호스트 풀은 사용자가 물리적 데스크톱에서처럼 상호 작용할 수 있는 앱 그룹을 포함할 수 있습니다. 배포 아키텍처에 대해 자세히 알아보려면 [Azure Virtual Desktop 환경](environment-setup.md)을 확인하세요. Azure Virtual Desktop에 대해 원격 앱 스트리밍을 사용하는 앱 개발자인 경우 고객 또는 사용자는 물리적 디바이스의 로컬 앱처럼 앱을 사용할 수 있습니다. 앱 개발자로 Azure Virtual Desktop을 사용하는 방법에 관한 자세한 내용은 [Azure Virtual Desktop 원격 앱 스트리밍](./remote-app-streaming/custom-apps.md) 설명서를 확인하세요.

>[!NOTE]
>Azure Virtual Desktop에 대해 원격 앱 스트리밍을 사용하는 앱 개발자이고 앱 사용자가 배포와 동일한 조직에 있는 경우 기존 Azure 테넌트를 사용하여 호스트 풀을 만들 수 있습니다. 사용자가 조직 외부에 있는 경우에는 보안상의 이유로 각 조직에 대해 하나 이상의 호스트 풀을 사용하여 별도의 Azure 테넌트를 만들어야 합니다. 배포를 안전하게 유지하기 위해 따르도록 권장하는 방법에 관한 자세한 내용은 [아키텍처 권장 사항](./remote-app-streaming/architecture-recs.md)에서 알아보세요.

이 문서에서는 Azure Portal을 통해 Azure Virtual Desktop 환경에 대한 호스트 풀을 만들기 위한 설정 프로세스를 안내합니다. 이 방법은 Azure Virtual Desktop에서 호스트 풀을 만들고, Azure 구독에서 VM으로 구성된 리소스 그룹을 만들며, 해당 VM을 Active Directory(AD) 도메인 또는 Azure Active Directory(Azure AD) 테넌트에 조인하고, VM을 Azure Virtual Desktop에 등록하기 위한 브라우저 기반 사용자 인터페이스를 제공합니다.

## <a name="prerequisites"></a>필수 조건

조직의 배포를 설정하는 IT 전문가인지 아니면 고객에게 애플리케이션을 제공하는 앱 개발자인지 여부에 따라 크게 두 가지 요구 사항이 있습니다.

### <a name="requirements-for-it-professionals"></a>IT 전문가를 위한 요구 사항

호스트 풀을 만들려면 다음 매개 변수를 입력해야 합니다.

- VM 이미지 이름
- VM 구성
- 도메인 및 네트워크 속성
- Azure Virtual Desktop 호스트 풀 속성

또한 다음 사항을 알고 있어야 합니다.

- 사용하려는 이미지의 원본이 있는 위치. Azure 갤러리에서 가져온 이미지인가요, 아니면 사용자 지정 이미지인가요?
- 도메인 조인 자격 증명

### <a name="requirements-for-app-developers"></a>앱 개발자를 위한 요구 사항

Azure Virtual Desktop용 원격 앱 스트리밍을 사용하여 고객에게 앱을 제공하는 앱 개발자인 경우 시작하는 데 필요한 사항은 다음과 같습니다.

- 최종 사용자에게 조직의 앱을 제공할 계획이라면 실제로 해당 앱이 준비되어 있는지 확인하세요. 자세한 내용은 [Azure Virtual Desktop을 사용하여 사용자 지정 앱을 호스트하는 방법](./remote-app-streaming/custom-apps.md)을 참조하세요.
- 기존 Azure 갤러리 이미지 옵션이 요구 사항을 충족하지 않는 경우 세션 호스트 VM에 대한 고유한 사용자 지정 이미지도 만들어야 합니다. VM 이미지를 만드는 방법에 대한 자세한 내용은 [Azure에 업로드할 Windows VHD 또는 VHDX 준비](../virtual-machines/windows/prepare-for-upload-vhd-image.md) 및 [Azure에서 일반화된 VM의 관리 이미지 만들기](../virtual-machines/windows/capture-image-resource.md)를 참조하세요.
- 도메인 조인 자격 증명 Azure Virtual Desktop과 호환되는 ID 관리 시스템이 아직 없는 경우 호스트 풀에 대한 ID 관리를 설정해야 합니다. 자세한 내용은 [관리 ID 설정](./remote-app-streaming/identities.md)을 참조하세요.

### <a name="final-requirements"></a>최종 요구 사항

마지막으로 Microsoft.DesktopVirtualization 리소스 공급자를 등록했는지 확인합니다. 아직 수행하지 않은 경우 **구독** 으로 이동하여 구독 이름을 선택한 다음, **리소스 공급자** 를 선택합니다. **DesktopVirtualization** 을 검색하고 **Microsoft.DesktopVirtualization** 을 선택한 다음, **등록** 을 선택합니다.

네트워크를 만드는 IT 전문가라면 Azure Resource Manager 템플릿을 사용하여 Azure Virtual Desktop 호스트 풀을 만드는 경우 Azure 갤러리, 관리형 이미지 또는 관리되지 않는 이미지에서 가상 머신을 만들 수 있습니다. VM 이미지를 만드는 방법에 대한 자세한 내용은 [Azure에 업로드할 Windows VHD 또는 VHDX 준비](../virtual-machines/windows/prepare-for-upload-vhd-image.md) 및 [Azure에서 일반화된 VM의 관리 이미지 만들기](../virtual-machines/windows/capture-image-resource.md)를 참조하세요. (앱 개발자라면 이 부분은 걱정하지 않아도 됩니다.)

마지막으로 Azure 구독이 아직 없다면 이 지침을 따르기 전에 먼저 [계정 만들기](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)를 해야 합니다.

## <a name="begin-the-host-pool-setup-process"></a>호스트 풀 설정 프로세스 시작

### <a name="portal"></a>[포털](#tab/azure-portal)

새 호스트 풀 만들기를 시작하려면 다음을 수행합니다.

1. [https://portal.azure.com](https://portal.azure.com/)에서 Azure Portal에 로그인합니다.
   
   >[!NOTE]
   > US Gov 포털에 로그인하는 경우 대신 [https://portal.azure.us/](https://portal.azure.us/)로 이동합니다.
   > 
   >Azure 중국 포털에 액세스하는 경우 [https://portal.azure.cn/](https://portal.azure.cn/)으로 이동합니다.

2. 검색 창에서 **Azure Virtual Desktop** 을 입력한 다음, 서비스 아래에서 **Azure Virtual Desktop** 을 찾아서 선택합니다.

3. **Azure Virtual Desktop** 개요 페이지에서 **호스트 풀 만들기** 를 선택합니다.

4. **기본** 탭의 [프로젝트 세부 정보] 아래에서 올바른 구독을 선택합니다.

5. **새로 만들기** 를 선택하여 새 리소스 그룹을 만들거나 드롭다운 메뉴에서 기존 리소스 그룹을 선택합니다.

6. 호스트 풀에 대한 고유한 이름을 입력합니다.

7. [위치] 필드의 드롭다운 메뉴에서 호스트 풀을 만들려는 지역을 선택합니다.

   선택한 지역과 연결된 Azure 지역은 이 호스트 풀 및 관련 개체에 대한 메타데이터가 저장되는 위치입니다. 서비스 메타데이터를 저장하려는 지리적 위치 내의 지역을 선택해야 합니다.

     > [!div class="mx-imgBorder"]
     > ![미국 동부 위치가 선택된 위치 필드를 보여주는 Azure Portal의 스크린샷. 필드 옆에는 "메타데이터가 미국 동부에 저장됩니다."라는 텍스트가 있습니다.](media/portal-location-field.png)
  
   >[!NOTE]
   > 미국 이외의 [지원되는 지역](data-locations.md)에서 호스트 풀을 만들려면 리소스 공급자를 다시 등록해야 합니다. 다시 등록한 후 위치를 선택할 수 있는 드롭다운에 다른 지역이 표시됩니다. [호스트 풀 만들기](troubleshoot-set-up-issues.md#i-only-see-us-when-setting-the-location-for-my-service-objects) 문제 해결 문서에서 다시 등록하는 방법에 대해 알아봅니다.

8. [호스트 풀 유형] 아래에서 호스트 풀이 **개인** 또는 **풀링됨** 인지를 선택합니다.

    - **개인** 을 선택하는 경우 [할당 유형] 필드에서 **자동** 또는 **직접** 을 선택합니다.

      > [!div class="mx-imgBorder"]
      > ![할당 유형 필드 드롭다운 메뉴의 스크린샷. 사용자가 자동을 선택했습니다.](media/assignment-type-field.png)

9.  **풀링됨** 을 선택하는 경우 다음 정보를 입력합니다.

     - **최대 세션 제한** 에 대해 단일 세션 호스트로 부하가 분산되도록 하려는 최대 사용자 수를 입력합니다.
     - **부하 분산 알고리즘** 에 대해 사용 패턴에 따라 [너비 우선] 또는 [깊이 우선] 중 하나를 선택합니다. 이러한 각 옵션의 의미에 관한 자세한 내용은 [호스트 풀 부하 분산 방법](host-pool-load-balancing.md)에서 확인하세요.

       > [!div class="mx-imgBorder"]
       > !["풀링됨"이 선택된 할당 유형 필드의 스크린샷. 사용자가 부하 분산 드롭다운 메뉴에서 커서로 너비 우선 위를 가리키고 있습니다.](media/pooled-assignment-type.png)

10. **다음: Virtual Machines >** 을 선택합니다.

11. 가상 머신을 이미 만들어 새 호스트 풀에서 사용하려는 경우 **아니요** 를 선택하고, **다음: 작업 영역 >** 을 선택한 다음, [작업 영역 정보](#workspace-information) 섹션으로 이동합니다. 새 가상 머신을 만들어 새 호스트 풀에 등록하려면 **예** 를 선택합니다.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI에 대한 환경 준비하는 것으로 시작합니다.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

로그인한 후 [az desktopvirtualization hostpool create](/cli/azure/desktopvirtualization#az_desktopvirtualization_hostpool_create) 명령을 사용하여 새 호스트 풀을 만들고 선택적으로 세션 호스트가 호스트 풀에 조인할 수 있는 등록 토큰을 만듭니다.

```azurecli
az desktopvirtualization hostpool create --name "MyHostPool" \
    --resource-group "MyResourceGroup" \ 
    --location "MyLocation" \
    --host-pool-type "Pooled" \
    --load-balancer-type "BreadthFirst" \
    --max-session-limit 999 \
    --personal-desktop-assignment-type "Automatic"  \
    --registration-info expiration-time="2022-03-22T14:01:54.9571247Z" registration-token-operation="Update" \
    --sso-context "KeyVaultPath" \
    --description "Description of this host pool" \
    --friendly-name "Friendly name of this host pool" \
    --tags tag1="value1" tag2="value2" 
```

새 가상 머신을 만들고 새 호스트 풀에 등록하려면 다음 섹션을 계속합니다. 이미 만든 가상 머신을 새 호스트 풀과 함께 사용하려면 [작업 영역 정보](#workspace-information) 섹션으로 이동하세요. 

---

이제 호스트 풀을 만들었으므로 VM을 만드는 설정 프로세스의 다음 부분으로 이동하겠습니다.

## <a name="virtual-machine-details"></a>가상 머신 세부 정보

이제 첫 번째 단계가 완료되었으므로 VM을 설정해야 합니다.

### <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal 호스트 풀 설정 프로세스 내에서 가상 머신을 설정하려면 다음을 수행합니다.

1. **리소스 그룹** 아래에서 가상 머신을 만들려는 리소스 그룹을 선택합니다. 이는 호스트 풀에 사용한 것과 다른 리소스 그룹일 수 있습니다.

2. 그런 다음, **이름 접두사** 를 제공하여 설정 프로세스에서 만드는 가상 머신의 이름을 지정합니다. 접미사는 0부터 시작하는 숫자가 포함된 `-`입니다.

3. 가상 머신을 만들려는 **가상 머신 위치** 를 선택합니다. 호스트 풀에 대해 선택한 지역과 같거나 다를 수 있습니다. VM 가격은 지역에 따라 달라지며 성능을 극대화하려면 VM 위치가 가능한 한 사용자와 가까운 곳에 있어야 합니다. 자세한 내용은 [Azure Virtual Desktop의 데이터 위치](data-locations.md)에서 확인하세요.
   
4. 다음으로, 요구 사항에 가장 적합한 가용성 옵션을 선택합니다. 사용자에게 적합한 옵션에 대해 자세히 알아보려면 [Azure의 가상 머신에 대한 가용성 옵션](../virtual-machines/availability.md) 및 [FAQ](/azure/virtual-desktop/faq#which-availability-option-is-best-for-me)를 참조하세요.
   
   > [!div class="mx-imgBorder"]
   > ![가용성 영역 드롭다운 메뉴의 스크린샷. "가용성 영역" 옵션이 강조 표시됩니다.](media/availability-zone.png)

5. 다음으로, 가상 머신을 만드는 데 사용해야 하는 이미지를 선택합니다. **갤러리** 또는 **스토리지 Blob** 중 하나를 선택할 수 있습니다.

    - **갤러리** 를 선택하는 경우 드롭다운 메뉴에서 다음과 같은 추천 이미지 중 하나를 선택합니다.

      - Windows 10 Enterprise 다중 세션 버전 1909
      - Windows 10 Enterprise 다중 세션 버전 1909 + Microsoft 365 Apps
      - Windows Server 2019 Datacenter
      - Windows 10 Enterprise 다중 세션 버전 2004
      - Windows 10 Enterprise 다중 세션 버전 2004 + Microsoft 365 Apps

      원하는 이미지가 표시되지 않으면 **모든 이미지 보기** 를 선택합니다. 그러면 갤러리의 다른 이미지 또는 Microsoft 및 다른 게시자가 제공한 이미지를 선택할 수 있습니다. 선택한 이미지가 [지원되는 OS 이미지](overview.md#supported-virtual-machine-os-images) 중 하나인지 확인합니다.

      > [!div class="mx-imgBorder"]
      > ![Microsoft의 이미지 목록이 표시된 Azure Portal의 스크린샷.](media/marketplace-images.png)

      **내 항목** 으로 이동하여 이미 업로드한 사용자 지정 이미지를 선택할 수도 있습니다.

      > [!div class="mx-imgBorder"]
      > ![내 항목 탭의 스크린샷.](media/my-items.png)

    - **Storage Blob** 을 선택하면 Hyper-V 또는 Azure VM을 통해 고유한 이미지 빌드를 사용할 수 있습니다. 스토리지 Blob에 있는 이미지의 위치를 URI로 입력하면 됩니다.
   
   이미지의 위치는 가용성 옵션과 독립적이지만 이미지의 영역 복원력에 따라 해당 이미지를 가용성 영역과 함께 사용할 수 있는지 여부가 결정됩니다. 이미지를 만드는 동안 가용성 영역을 선택하는 경우 영역 복원력이 활성화된 갤러리에서 이미지를 사용하고 있는지 확인합니다. 사용해야 하는 영역 복원력 옵션에 대한 자세한 내용은 [FAQ](/azure/virtual-desktop/faq#which-availability-option-is-best-for-me)를 참조하세요.

6. 그런 다음, 사용할 **가상 머신 크기** 를 선택합니다. 기본 크기를 그대로 유지하거나 **크기 변경** 을 선택하여 크기를 변경할 수 있습니다. 표시되는 창에서 **크기 변경** 을 선택하는 경우 워크로드에 적합한 가상 머신의 크기를 선택합니다. 가상 머신 크기 및 선택해야 하는 크기에 관한 자세한 내용은 [가상 머신 크기 조정 지침](/windows-server/remote/remote-desktop-services/virtual-machine-recs?context=/azure/virtual-desktop/context/context)을 참조하세요.

7. **VM 수** 아래에서 호스트 풀에 대해 만들려는 VM 수를 제공합니다.

    >[!NOTE]
    >설정 프로세스는 호스트 풀을 설정하는 동안 최대 400개의 VM을 만들 수 있으며, 각 VM 설정 프로세스에서 4개의 개체를 리소스 그룹에 만듭니다. 만들기 프로세스는 구독 할당량을 확인하지 않으므로 입력하는 VM 수가 리소스 그룹 및 구독에 대한 Azure VM 및 API 제한 내에 있는지 확인합니다. 호스트 풀 만들기가 완료되면 VM을 더 추가할 수 있습니다.

8. VM에서 사용할 OS 디스크의 종류(표준 SSD, 프리미엄 SSD 또는 표준 HDD)를 선택합니다.

9. 네트워크 및 보안 아래에서 사용자가 만든 가상 머신을 배치하려는 **가상 네트워크** 및 **서브넷** 을 선택합니다. 가상 네트워크 내의 가상 머신을 도메인에 조인해야 하므로 가상 네트워크에서 도메인 컨트롤러에 연결할 수 있는지 확인합니다. 선택한 가상 네트워크의 DNS 서버는 도메인 컨트롤러의 IP를 사용하도록 구성해야 합니다.

10. 원하는 보안 그룹의 종류(**기본** , **고급**  또는 **없음**)를 선택합니다.

    **기본** 을 선택하는 경우 인바운드 포트를 열지 여부를 선택해야 합니다. **예** 를 선택하는 경우 인바운드 연결을 허용하는 표준 포트의 목록에서 선택합니다.

    >[!NOTE]
    >보안을 강화하려면 퍼블릭 인바운드 포트를 열지 않는 것이 좋습니다.

    > [!div class="mx-imgBorder"]
    > ![드롭다운 메뉴의 사용 가능한 포트 목록을 보여주는 보안 그룹 페이지의 스크린샷.](media/available-ports.png)

    **고급** 을 선택하는 경우 이미 구성한 기존 네트워크 보안 그룹을 선택합니다.

11. 그런 다음, 가상 머신을 **Active Directory** 또는 **Azure Active Directory**(미리 보기)에 조인할지 여부를 선택합니다.

    - Active Directory의 경우 도메인에 조인할 계정을 제공하고 특정 도메인 및 조직 구성 단위에 조인할지 여부를 선택합니다.

        - AD 도메인 조인 UPN의 경우 선택한 가상 네트워크의 Active Directory 도메인 관리자에 대한 자격 증명을 입력합니다. 사용하는 계정에는 MFA(다단계 인증)를 사용할 수 없습니다. Azure AD DS(Azure Active Directory Domain Services) 도메인에 조인할 때 사용 중인 계정은 Azure AD DC 관리자 그룹의 일부여야 하며 계정 암호는 Azure AD DS에서 작동해야 합니다.

        - 도메인을 지정하려면 **예** 를 선택한 다음, 조인하려는 도메인의 이름을 입력합니다. 원한다면 전체 경로(고유 이름)를 따옴표 없이 입력하여 가상 머신을 사용할 특정 조직 구성 단위를 추가할 수도 있습니다. 도메인을 지정하지 않으려면 **아니요** 를 선택합니다. VM은 **AD 도메인 조인 UPN** 의 접미사와 일치하는 도메인에 자동으로 조인합니다.
  
    - Azure Active Directory의 경우 **Intune을 사용하여 VM 등록** 을 선택하여 VM을 배포한 후 자동으로 관리에 사용할 수 있도록 설정할 수 있습니다.

12. **가상 머신 관리자 계정** 에서 VM을 생성하는 동안 추가할 로컬 관리자 계정의 자격 증명을 입력합니다. AD 및 Azure AD 조인 VM 모두에서 이 계정을 관리 목적으로 사용할 수 있습니다.

13. **다음: 작업 영역 >** 을 선택합니다.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[az vm create](/cli/azure/vm#az_vm_create) 명령을 사용하여 새 Azure 가상 머신을 만듭니다.

```azurecli
az vm create --name "MyVMName" \
    --resource-group "MyResourceGroup" \
    --image "MyImage" \
    --generate-ssh-keys
```

Azure CLI를 사용하여 Azure 가상 머신을 만드는 방법에 대한 자세한 내용은 다음을 참조하세요.
- Windows
    - [Azure CLI를 사용하여 Windows VM 만들기]( /azure/virtual-machines/windows/quick-create-cli)
    - [자습서 - Azure CLI를 사용하여 Windows VM 만들기 및 관리](/cli/azure/azure-cli-vm-tutorial)
- Linux
    - [Azure CLI를 사용하여 Linux VM 만들기](../virtual-machines/linux/quick-create-cli.md)
    - [자습서: Azure CLI로 Linux VM 만들기 및 관리]( /azure/virtual-machines/linux/tutorial-manage-vm) 
---

이제 호스트 풀을 설정하는 다음 단계로서 앱 그룹을 작업 영역에 등록할 준비가 되었습니다.

## <a name="workspace-information"></a>작업 영역 정보

호스트 풀 설정 프로세스는 기본적으로 데스크톱 애플리케이션 그룹을 만듭니다. 호스트 풀이 의도한 대로 작동하려면 이 앱 그룹을 사용자 또는 사용자 그룹에 게시해야 하고 해당 앱 그룹을 작업 영역에 등록해야 합니다.

>[!NOTE]
>조직의 앱을 게시하려는 앱 개발자인 경우 사용자 세션에 동적으로 MSIX 앱을 연결하거나 사용자 지정 VM 이미지에 앱 패키지를 추가할 수 있습니다. 자세한 내용은 Azure Virtual Desktop을 사용하여 사용자 지정 앱을 제공하는 방법을 참조하세요.

### <a name="portal"></a>[포털](#tab/azure-portal)

데스크톱 앱 그룹을 작업 영역에 등록하려면 다음을 수행합니다.

1. **예** 를 선택합니다.

   **아니요** 를 선택하는 경우 나중에 앱 그룹을 등록할 수 있지만 호스트 풀이 제대로 작동하도록 가능한 한 빨리 작업 영역을 등록하는 것이 좋습니다.

2. 다음으로, 새 작업 영역을 만들지 아니면 기존 작업 영역에서 선택할지를 선택합니다. 호스트 풀과 동일한 위치에 만든 작업 영역에만 앱 그룹을 등록할 수 있습니다.

3. 필요에 따라 **다음: 태그 >** 를 선택할 수 있습니다.

    여기서는 관리자가 작업을 더 쉽게 수행할 수 있도록 태그를 추가하여 개체를 메타데이터로 그룹화할 수 있습니다.

4. 완료되면 **검토 + 만들기** 를 선택합니다.

     >[!NOTE]
     >검토 + 만들기 유효성 검사 프로세스에서는 암호가 보안 표준을 충족하는지 또는 아키텍처가 올바른지 확인하지 않으므로 이러한 항목 중 하나에 문제가 있는지 직접 확인해야 합니다.

5. 배포 정보를 검토하여 모든 것이 올바르게 표시되는지 확인합니다. 완료되면 **만들기** 를 선택합니다. 

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[az desktopvirtualization workspace create](/cli/azure/desktopvirtualization#az_desktopvirtualization_workspace_create) 명령을 사용하여 새 작업 영역을 만듭니다.

```azurecli
az desktopvirtualization workspace create --name "MyWorkspace" \
    --resource-group "MyResourceGroup" \
    --location "MyLocation" \
    --tags tag1="value1" tag2="value2" \
    --friendly-name "Friendly name of this workspace" \
    --description "Description of this workspace" 
```
---

그러면 배포 프로세스가 시작되어 다음과 같은 개체가 만들어집니다.

- 새 호스트 풀
- 데스크톱 앱 그룹
- 작업 영역(만들도록 선택한 경우)
- 데스크톱 앱 그룹을 등록하도록 선택한 경우 등록이 완료됩니다.
- 가상 머신(가상 머신을 만들도록 선택한 경우 도메인에 조인되어 새 호스트 풀에 등록됨)
- 구성에 기반한 Azure 리소스 관리 템플릿에 대한 다운로드 링크

그러면 모든 작업이 완료되었습니다!

## <a name="run-the-azure-resource-manager-template-to-provision-a-new-host-pool"></a>Azure Resource Manager 템플릿을 실행하여 새 호스트 풀 프로비전

자동화된 프로세스를 사용하는 경우 대신 [Azure Resource Manager 템플릿을 다운로드](https://github.com/Azure/RDS-Templates/tree/master/ARM-wvd-templates)하여 새 호스트 풀을 프로비저닝합니다.

>[!NOTE]
>자동화된 프로세스를 사용하여 환경을 빌드하는 경우 최신 버전의 구성 JSON 파일이 필요합니다. [여기](https://wvdportalstorageblob.blob.core.windows.net/galleryartifacts?restype=container&comp=list)에서 JSON 파일을 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

이제 호스트 풀이 만들어졌으므로 이 풀을 RemoteApp 프로그램으로 채울 수 있습니다. Azure Virtual Desktop에서 앱을 관리하는 방법에 관해 자세히 알아보려면 다음 자습서로 진행하세요.

> [!div class="nextstepaction"]
> [앱 그룹 관리 자습서](./manage-app-groups.md)
