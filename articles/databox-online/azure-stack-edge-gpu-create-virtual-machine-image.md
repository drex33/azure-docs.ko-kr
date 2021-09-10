---
title: Azure Stack Edge Pro GPU 디바이스용 사용자 지정 VM 이미지 만들기
description: Azure Stack Edge Pro GPU 디바이스에 가상 머신을 배포하기 위한 사용자 지정 Windows 및 Linux VM 이미지를 만드는 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 07/16/2021
ms.author: alkohli
ms.openlocfilehash: 94ffb38c71437c8f5902866620b5ac0c2467edfd
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114462918"
---
# <a name="create-custom-vm-images-for-your-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU 디바이스용 사용자 지정 VM 이미지 만들기

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure Stack Edge Pro GPU 디바이스에 VM을 배포하려면 Azure에서 VM을 만드는 데 사용할 수 있는 사용자 지정 VM 이미지를 만들 수 있어야 합니다. 이 문서에서는 Azure에서 Windows 및 Linux VM에 대한 사용자 지정 VM 이미지를 만들고 해당 이미지를 다운로드하거나 Azure Storage 계정에 복사하는 단계를 설명합니다.

사용자 지정 VM 이미지를 준비하는 데 필요한 워크플로가 있습니다. 이미지 원본의 경우 Azure에서 지원하는 모든 크기의 Gen1 VM에서 고정 VHD를 사용해야 합니다. VM 크기 옵션은 [지원되는 VM 크기](azure-stack-edge-gpu-virtual-machine-sizes.md#supported-vm-sizes)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

VM 이미지를 만들기 전에 다음 필수 구성 요소를 완료합니다.

- [AzCopy를 다운로드합니다](../storage/common/storage-use-azcopy-v10.md#download-azcopy). AzCopy는 OS 디스크를 Azure Storage 계정에 복사하는 빠른 방법을 제공합니다.

---

## <a name="create-a-custom-vm-image"></a>사용자 지정 VM 이미지 만들기

사용자 지정 VM 이미지를 준비하는 단계는 Windows 또는 Linux VM에 따라 다릅니다.


### <a name="windows"></a>[Windows](#tab/windows)

다음 단계를 수행하여 Windows VM 이미지를 만듭니다.

1. Azure에서 Windows 가상 머신을 만듭니다. 포털 지침은 [Azure Portal에서 Windows 가상 머신 만들기](../virtual-machines/windows/quick-create-portal.md)를 참조하세요. PowerShell 지침은 [자습서: Azure PowerShell을 사용하여 Windows VM 만들기 및 관리](../virtual-machines/windows/tutorial-manage-vm.md)를 참조하세요.  

   가상 머신은 1세대 VM여야 합니다. VM 이미지를 만드는 데 사용하는 OS 디스크는 Azure에서 지원하는 크기의 고정 크기 VHD여야 합니다. VM 크기 옵션은 [지원되는 VM 크기](azure-stack-edge-gpu-virtual-machine-sizes.md#supported-vm-sizes)를 참조하세요.

   Azure Marketplace에서는 고정 크기 VHD가 있는 모든 Windows Gen1 VM을 사용할 수 있습니다. 사용 가능한 Azure Marketplace 이미지 목록은 [Azure Stack Edge에 일반적으로 사용되는 Azure Marketplace 이미지](azure-stack-edge-gpu-create-virtual-machine-marketplace-image.md#commonly-used-marketplace-images)를 참조하세요.

2. 가상 머신을 일반화합니다. VM을 일반화하려면 [가상 머신에 연결하고](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#connect-to-a-windows-vm) 명령 프롬프트를 열고 다음 `sysprep` 명령을 실행합니다.

   ```dos
   c:\windows\system32\sysprep\sysprep.exe /oobe /generalize /shutdown /mode:vm
   ```

   > [!IMPORTANT]
   > 명령이 완료되면 VM이 종료됩니다. **VM을 다시 시작하지 마십시오.** VM을 다시 시작하면 방금 준비한 디스크가 손상됩니다.


### <a name="linux"></a>[Linux](#tab/linux)

다음 단계를 수행하여 Linux VM 이미지를 만듭니다.

1. Azure에서 Linux 가상 머신을 만듭니다. 포털 지침은 [빠른 시작: Azure Portal에서 Linux VM 만들기](../virtual-machines/linux/quick-create-portal.md)를 참조하세요.  PowerShell 지침은 [빠른 시작: PowerShell을 사용하여 Azure에서 Linux VM 만들기](../virtual-machines/linux/quick-create-powershell.md)를 참조하세요.

   Azure Marketplace에서 고정 크기 VHD로 Gen1 VM을 사용하여 추가 단계가 필요한 RHEL(Red Hat Enterprise Linux) 이미지를 제외하고 Linux 사용자 지정 이미지를 만들 수 있습니다. 사용 가능한 Azure Marketplace 이미지 목록은 [Azure Stack Edge에 일반적으로 사용되는 Azure Marketplace 이미지](azure-stack-edge-gpu-create-virtual-machine-marketplace-image.md#commonly-used-marketplace-images)를 참조하세요. RHEL 이미지에 대한 지침은 아래 [RHEL BYOS 이미지 사용](#using-rhel-byos-images)을 참조하세요.

1. VM의 프로비전을 해제합니다. Azure VM 에이전트를 사용하여 머신별 파일 및 데이터를 삭제합니다. 원본 Linux VM에서 `-deprovision+user` 매개 변수와 함께 `waagent` 명령을 사용합니다. 자세한 내용은 [Azure Linux 에이전트 이해 및 사용](../virtual-machines/extensions/agent-linux.md)을 참조하세요.

    1. [SSH 클라이언트를 사용하여 Linux VM에 연결합니다](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md#connect-to-a-linux-vm).
    2. SSH 창에서 다음 명령을 입력합니다.
       
        ```bash
        sudo waagent -deprovision+user
        ```
       > [!NOTE]
       > 이미지로 캡처하려는 VM에서만 이 명령을 실행합니다. 이 명령이 이미지에서 중요한 정보가 모두 지워졌다거나 재배포에 적합하다는 것을 보장하지는 않습니다. 또한 `+user` 매개 변수는 마지막 프로비전된 사용자 계정을 제거합니다. VM에 사용자 계정 자격 증명을 유지하려면 `-deprovision`만 사용합니다.
     
    3. 계속하려면 **y** 를 입력합니다. `-force` 매개 변수를 추가하여 이 확인 단계를 피할 수 있습니다.
    4. 명령이 완료되면 **종료** 를 입력하여 SSH 클라이언트를 닫습니다.  이 시점에서 VM은 계속 실행되고 있습니다.


### <a name="using-rhel-byos-images"></a>RHEL BYOS 이미지 사용

RHEL(Red Hat Enterprise Linux) 이미지를 사용하는 경우 Red Hat Gold 이미지라고도 하는 RHEL BYOS(Red Hat Enterprise Linux Bring Your Own Subscription) 이미지가 지원되며 VM 이미지를 만드는 데 사용할 수 있습니다. Azure Marketplace의 표준 종량제 RHEL 이미지는 Azure Stack Edge에서 지원되지 않습니다.

RHEL BYOS 이미지를 사용하여 VM 이미지를 만들려면 다음 단계를 수행합니다.

1. [Red Hat 구독 관리](https://access.redhat.com/management)에 로그인합니다. 상단 메뉴 모음에서 [클라우드 액세스 대시보드](https://access.redhat.com/management/cloud)로 이동합니다.
1. Azure 구독을 사용하도록 설정합니다. [자세한 지침](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/getting-started-with-ca_cloud-access)을 참조하세요. 구독을 사용하도록 설정하면 Red Hat Gold 이미지에 액세스할 수 있습니다.

1. Azure 사용 약관(Azure 구독당, 이미지당 한 번만)에 동의하고 VM을 프로비전합니다. [지침](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/understanding-gold-images_cloud-access)을 참조하세요. 

이제 프로비전한 VM을 사용하여 Linux에서 [VM 사용자 지정 이미지를 만들](#create-a-custom-vm-image) 수 있습니다.

---

## <a name="download-os-disk-to-storage-account"></a>OS 디스크를 스토리지 계정으로 다운로드

사용자 지정 VM 이미지를 사용하여 디바이스에서 VM을 배포하려면 OS 디스크를 Azure Storage 계정으로 다운로드해야 합니다. 디바이스에 사용한 것과 동일한 스토리지 계정을 사용하는 것이 좋습니다.

VM에 대한 OS 디스크를 Azure 스토리지 계정에 다운로드하려면 다음 단계를 수행합니다.

1. [포털에서 VM을 중지합니다](../virtual-machines/windows/download-vhd.md#stop-the-vm). 일반화하도록 `sysprep`을 실행한 후 Windows VM이 종료된 경우에도 이 작업을 수행하여 OS 디스크의 할당을 취소해야 합니다.

1. [OS 디스크에 대한 다운로드 URL을 생성](../virtual-machines/windows/download-vhd.md#generate-download-url)하고 URL을 기록합니다. 기본적으로 URL은 3600초(1시간) 후에 만료됩니다. 필요한 경우 이 시간을 늘릴 수 있습니다.
      
1. 다음 방법 중 하나를 사용하여 Azure Storage 계정에 VHD를 다운로드합니다.
   
   - 방법 1: 더 빠른 전송의 경우 AzCopy를 사용하여 VHD를 Azure Storage 계정에 복사합니다. 지침은 [AzCopy를 사용하여 VM 이미지를 스토리지 계정에 복사](#copy-vhd-to-storage-account-using-azcopy)를 참조하세요. 

   - 방법 2: 간단한 한 번 클릭 방법으로 다운로드 URL을 생성할 때(3b단계에서) **VHD 파일 다운로드** 를 선택하여 포털에서 디스크를 다운로드할 수 있습니다. **이 방법을 사용하는 경우 디스크 복사본의 시간이 상당히 길어질 수 있으며, 포털을 사용하여 VM을 만들려면 [Azure 스토리지 계정에 VHD를 업로드](azure-stack-edge-gpu-deploy-virtual-machine-templates.md#use-storage-explorer-for-upload)해야 합니다.**

이제 이 VHD를 사용하여 Azure Stack Edge Pro GPU 디바이스에서 VM을 만들고 배포할 수 있습니다.

## <a name="copy-vhd-to-storage-account-using-azcopy"></a>AzCopy를 사용하여 VHD를 스토리지 계정에 복사

다음 절차에서는 AzCopy를 사용하여 사용자 지정 VM 이미지를 Azure Storage 계정에 복사하는 방법을 설명합니다. 그러면 이미지를 사용하여 Azure Stack Edge Pro GPU 디바이스에 VM을 배포할 수 있습니다. Azure Stack Edge와 동일한 지역/구독에 있는 사용 중인 기존 스토리지 계정에 사용자 지정 VM 이미지를 저장하는 것이 좋습니다.


### <a name="create-target-uri-for-a-container"></a>컨테이너의 대상 URI 만들기

AzCopy에는 스토리지 계정에서 새 이미지를 복사할 위치를 알려 주는 *대상 URI* 가 필요합니다. AzCopy를 실행하기 전에 파일을 복사하려는 Blob 컨테이너에 대한 SAS(공유 액세스 서명) URL을 생성합니다. 대상 URI를 만들려면 SAS URL에 파일 이름을 추가합니다.

준비된 VHD에 대한 대상 URI를 만들려면 다음 단계를 수행합니다.

1. Azure Storage 계정의 컨테이너에 대한 SAS URL을 생성하고 다음 단계를 수행합니다.

   1. Azure Portal에서 스토리지 계정을 열고 **컨테이너** 를 선택합니다. 사용하려는 Blob 컨테이너를 선택한 다음, 마우스 오른쪽 단추로 클릭하고 **SAS 생성** 을 선택합니다.

      ![Azure Portal에서 Blob 컨테이너에 대한 SAS 생성 옵션의 스크린샷](./media/azure-stack-edge-gpu-create-virtual-machine-image/storage-container-generate-sas-option.png)

   1. **SAS 생성** 화면의 **권한** 에서 **읽기** 및 **쓰기** 를 선택합니다.  

      ![읽기 및 쓰기 권한이 선택된 SAS 생성 화면의 스크린샷](./media/azure-stack-edge-gpu-create-virtual-machine-image/generate-sas-screen-read-and-write-permissions.png)

   1. **SAS 토큰 및 URL 생성** 을 선택한 다음, **복사** 를 선택하여 **Blob SAS URL** 을 복사합니다.

      ![Blob SAS URL을 생성하고 복사하는 옵션이 있는 SAS 생성 화면의 스크린샷](./media/azure-stack-edge-gpu-create-virtual-machine-image/generate-sas-screen-copy-blob-sas-url.png)

1. `azcopy` 명령에 대한 대상 URI를 만들려면 원하는 파일 이름을 SAS URL에 추가합니다.

   Blob SAS URL의 형식은 다음과 같습니다. 

   ![레이블이 지정된 새 파일 이름을 삽입할 컨테이너 경로 및 위치가 있는 Blob SAS URL의 그래픽](./media/azure-stack-edge-gpu-create-virtual-machine-image/blob-sas-url-format.png)

   쿼리 문자열을 시작하는 물음표 앞에 `/<filename>.vhd` 형식으로 파일 이름을 삽입합니다. 파일 이름 확장명은 VHD여야 합니다. 

   예를 들어 다음 Blob SAS URL에서는 **osdisk.vhd** 파일을 **mystorageaccount** 의 **virtualmachines** Blob 컨테이너에 복사합니다.

   ![osdisk라는 VHD에 대한 Blob SAS URL 예제의 그래픽](./media/azure-stack-edge-gpu-create-virtual-machine-image/target-uri-example-based-on-blob-sas-url.png)


### <a name="copy-vhd-to-blob-container"></a>Blob 컨테이너에 VHD 복사

AzCopy를 사용하여 Blob 컨테이너에 VHD를 복사하려면 다음 단계를 수행합니다.

 1. 아직 AZCopy를 다운로드하지 않은 경우 [AZCopy를 다운로드합니다](../storage/common/storage-use-azcopy-v10.md#download-azcopy).
 
 1. PowerShell에서 azcopy.exe를 저장한 디렉터리로 이동하고 다음 명령을 실행합니다.

    `.\azcopy copy <source URI> <target URI> --recursive`

    여기서
    * `<source URI>`는 이전에 생성한 다운로드 URL입니다.
    * `<target URI>`는 Azure Storage 계정에서 새 이미지를 복사할 Blob 컨테이너를 알려줍니다. 지침은 [AzCopy를 사용하여 VM 이미지를 스토리지 계정에 복사](#copy-vhd-to-storage-account-using-azcopy)를 참조하세요.

    예를 들어 다음 URI는 **windowsosdisk.vhd** 라는 파일을 **mystorageaccount** 스토리지 계정의 **가상 머신** Blob 컨테이너에 복사합니다.

    ```azcopy 
    .\azcopy copy "https://md-h1rvdq3wwtdp.z24.blob.storage.azure.net/gxs3kpbgjhkr/abcd?sv=2018-03-28&sr=b&si=f86003fc-a231-43b0-baf2-61dd51e3a05a&sig=o5Rj%2BNZSook%2FVNMcuCcwEwsr0i7sy%2F7gIDzak6JhlKg%3D" "https://mystorageaccount.blob.core.windows.net/virtualmachines/osdisk.vhd?sp=rw&st=2021-05-21T16:52:24Z&se=2021-05-22T00:52:24Z&spr=https&sv=2020-02-10&sr=c&sig=PV3Q3zpaQ%2FOLidbQJDKlW9nK%2BJ7PkzYv2Eczxko5k%2Bg%3D" --recursive
    ```

#### <a name="sample-output"></a>샘플 출력

위의 예제 AzCopy 명령의 경우 다음 출력은 성공적인 복사가 완료되었음을 나타냅니다.

   ```output
   PS C:\azcopy\azcopy_windows_amd64_10.10.0> .\azcopy copy "https://md-h1rvdq3wwtdp.z24.blob.storage.azure.net/gxs3kpbgjhkr/abcd?sv=2018-03-28&sr=b&si=f86003fc-a231-43b0-baf2-61dd51e3a05a&sig=o5Rj%2BNZSook%2FVNMcuCcwEwsr0i7sy%2F7gIDzak6JhlKg%3D" "https://mystorageaccount.blob.core.windows.net/virtualmachines/osdisk.vhd?sp=rw&st=2021-05-21T16:52:24Z&se=2021-05-22T00:52:24Z&spr=https&sv=2020-02-10&sr=c&sig=PV3Q3zpaQ%2FOLidbQJDKlW9nK%2BJ7PkzYv2Eczxko5k%2Bg%3D" --recursive
   INFO: Scanning...
   INFO: Failed to create one or more destination container(s). Your transfers may still succeed if the container already exists.
   INFO: Any empty folders will not be processed, because source and/or destination doesn't have full folder support

   Job 783f2177-8317-3e4b-7d2f-697a8f1ab63c has started
   Log file is located at: C:\Users\aseuser\.azcopy\783f2177-8317-3e4b-7d2f-697a8f1ab63c.log

   INFO: Destination could not accommodate the tier P10. Going ahead with the default tier. In case of service to service transfer, consider setting the flag --s2s-preserve-access-tier=false.
   100.0 %, 0 Done, 0 Failed, 1 Pending, 0 Skipped, 1 Total,

   Job 783f2177-8317-3e4b-7d2f-697a8f1ab63c summary
   Elapsed Time (Minutes): 1.4671
   Number of File Transfers: 1
   Number of Folder Property Transfers: 0
   Total Number of Transfers: 1
   Number of Transfers Completed: 1
   Number of Transfers Failed: 0
   Number of Transfers Skipped: 0
   TotalBytesTransferred: 136367309312
   Final Job Status: Completed

   PS C:\azcopy\azcopy_windows_amd64_10.10.0>
   ```

## <a name="next-steps"></a>다음 단계

- [Azure Portal을 사용하여 디바이스에 VM 배포](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)
- [PowerShell을 통해 디바이스에 VM 배포](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md)