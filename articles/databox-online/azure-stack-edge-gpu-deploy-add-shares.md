---
title: Azure Stack Edge Pro GPU와 공유할 데이터를 전송하는 방법에 대한 자습서 | Microsoft Docs
description: Azure Stack Edge Pro GPU 디바이스에 공유를 추가하고 연결하는 방법을 알아봅니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: f6818d0f937cc58a6884a36195c0d77db7366d2b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121747370"
---
# <a name="tutorial-transfer-data-via-shares-with-azure-stack-edge-pro-gpu"></a>자습서: Azure Stack Edge Pro GPU와 공유를 통해 데이터 전송

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

이 자습서에서는 Azure Stack Edge Pro 디바이스의 공유를 추가하고 연결하는 방법에 대해 설명합니다. 공유가 추가되면 Azure Stack Edge Pro에서 Azure로 데이터를 전송할 수 있습니다.

이 절차를 완료하는 데 약 10분이 걸릴 수 있습니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
>
> * 공유 추가
> * 공유에 연결

## <a name="prerequisites"></a>사전 요구 사항

Azure Stack Edge Pro에 공유를 추가하기 전에 다음 사항을 확인합니다.

* [Azure Stack Edge Pro 설치](azure-stack-edge-gpu-deploy-install.md)에서 설명한 대로 물리적 디바이스를 설치했습니다.

* [Azure Stack Edge Pro 활성화](azure-stack-edge-gpu-deploy-activate.md)에서 설명한 대로 물리적 디바이스를 활성화했습니다.

## <a name="add-a-share"></a>공유 추가

공유를 만들려면 다음 절차를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)에서 Azure Stack Edge 리소스를 선택한 다음, **개요** 로 이동합니다. 디바이스는 온라인 상태여야 합니다. **클라우드 스토리지 게이트웨이** 를 선택합니다.

   ![디바이스 온라인 상태](./media/azure-stack-edge-gpu-deploy-add-shares/device-online-1.png)

2. 디바이스 명령 모음에서 **+ 공유 추가** 를 선택합니다.

   ![공유 추가](./media/azure-stack-edge-gpu-deploy-add-shares/select-add-share-1.png)

3. **공유 추가** 창에서 다음 단계를 수행합니다.

    a. **이름** 상자에서 공유에 대한 고유한 이름을 제공합니다.  
    공유 이름에는 문자, 숫자 및 하이픈만 있을 수 있습니다. 3-63자여야 하고 문자 또는 숫자로 시작해야 합니다. 하이픈 앞에는 문자 또는 숫자가 나와야 합니다.
    
    b. 공유에 대한 **유형** 을 선택합니다.  
    유형은 **SMB** 또는 **NFS** 이며, SMB가 기본값입니다. SMB는 Windows 클라이언트에 대한 표준이며, NFS는 Linux 클라이언트에 사용됩니다.  
    SMB 또는 NFS 공유를 선택하는지 여부에 따라 나머지 옵션이 약간 다릅니다. 

    다. 공유가 상주할 스토리지 계정을 입력합니다.

    d. **스토리지 서비스** 드롭다운 목록에서 **블록 Blob**, **페이지 Blob** 또는 **파일** 을 선택합니다.  
    선택하는 서비스 유형은 Azure에서 사용하려는 데이터 형식에 따라 달라집니다. 이 예제에서는 데이터를 블록 Blo 으로 Azure에 저장하므로 **블록 Blob** 을 선택합니다. **페이지 Blob** 을 선택하는 경우 데이터가 512바이트로 정렬되어 있는지 확인합니다. 예를 들어 VHDX는 항상 512바이트로 정렬됩니다.

   > [!IMPORTANT]
   > Azure Stack Edge Pro 또는 Data Box Gateway 디바이스에서 사용하는 경우 사용하는 Azure Storage 계정에 불변성 정책을 설정하지 않았는지 확인합니다. 자세한 내용은 [Blob 스토리지에 대한 불변성 정책 설정 및 관리](../storage/blobs/immutable-policy-configure-version-scope.md)를 참조하세요.

    e. 새 Blob 컨테이너를 만들거나 드롭다운 목록의 기존 항목을 사용합니다. Blob 컨테이너를 만드는 경우 컨테이너 이름을 제공합니다. 컨테이너가 아직 없으면 스토리지 계정에 새로 만든 공유 이름으로 만들어집니다.
   
    f. SMB 공유 또는 NFS 공유를 만들었는지 여부에 따라 다음 단계 중 하나를 수행합니다. 
     
    - **SMB 공유**: **모든 권한 로컬 사용자**  아래에서 **새로 만들기** 또는 **기존 항목 사용** 을 선택합니다. 새 로컬 사용자를 만드는 경우 사용자 이름과 암호를 입력한 다음, 해당 암호를 확인합니다. 이 작업은 로컬 사용자에게 권한을 할당합니다. 공유 수준 권한 수정은 현재 지원되지 않습니다. 이 공유 데이터에 대해 **읽기 작업만 허용** 확인란을 선택하면 읽기 전용 사용자를 지정할 수 있습니다.
    
        ![SMB 공유 추가](./media/azure-stack-edge-gpu-deploy-add-shares/add-share-smb-1.png)
   
    - **NFS 공유**: 공유에 액세스할 수 있도록 허용된 클라이언트의 IP 주소를 입력합니다.

        ![NFS 공유 추가](./media/azure-stack-edge-gpu-deploy-add-shares/add-share-nfs-1.png)
   
4. **만들기** 를 선택하여 공유를 만듭니다.
    
    공유 만들기가 진행 중이라는 알림이 표시됩니다. 지정한 설정으로 공유가 만들어지면 새 공유를 반영하도록 **공유** 타일이 업데이트됩니다.
    

## <a name="connect-to-the-share"></a>공유에 연결

이제 마지막 단계에서 만든 하나 이상의 공유에 연결할 수 있습니다. SMB 공유 또는 NFS 공유인지 여부에 따라 단계가 다를 수 있습니다.

첫 번째 단계는 SMB 또는 NFS 공유를 사용하는 경우 디바이스 이름을 확인할 수 있는지 확인하는 것입니다.

### <a name="modify-host-file-for-name-resolution"></a>이름 확인을 위해 호스트 파일 수정

이제 디바이스의 IP 및 디바이스의 로컬 웹 UI에서 정의한 디바이스 식별 이름을 다음에 추가합니다.

- 클라이언트의 호스트 파일 또는
- DNS 서버의 호스트 파일

> [!IMPORTANT]
> 디바이스 이름 확인을 위해 DNS 서버에서 호스트 파일을 수정하는 것이 좋습니다.

디바이스에 연결하는 데 사용하는 Windows 클라이언트에서 다음 단계를 수행합니다.

1. 관리자 권한으로 **메모장** 을 시작하고 `C:\Windows\System32\Drivers\etc`에 있는 **호스트** 파일을 엽니다.

    ![Windows 탐색기 호스트 파일](media/azure-stack-edge-gpu-deploy-add-shares/client-hosts-file-1.png)


2. 다음 항목을 디바이스에 적합한 값으로 대체하고 **hosts** 파일에 추가합니다. 

    ```
    <Device IP>   <device friendly name>
    ``` 
    **네트워크** 에서 디바이스 IP를 가져올 수 있으며 로컬 웹 UI의 **디바이스** 페이지에서 디바이스 식별 이름을 가져올 수 있습니다. 호스트 파일의 다음 스크린샷은 항목을 보여 줍니다.

    ![Windows 탐색기 호스트 파일 2](media/azure-stack-edge-gpu-deploy-add-shares/client-hosts-file-2.png)

### <a name="connect-to-an-smb-share"></a>SMB 공유에 연결

Azure Stack Edge Pro 디바이스에 연결된 Windows Server 클라이언트에서 다음 명령을 입력하여 SMB 공유에 연결합니다.


1. 명령 창에서 다음을 입력합니다.

    `net use \\<Device name>\<share name>  /u:<user name for the share>`

    > [!NOTE]
    > 디바이스 이름으로만 SMB 공유에 연결할 수 있으며 디바이스 IP 주소를 통해서는 연결할 수 없습니다.

2. 이 작업을 요청하는 메시지가 표시되면 공유에 대한 암호를 입력합니다.  
   이 명령의 출력 샘플은 다음과 같습니다.

    ```powershell
    Microsoft Windows [Version 10.0.18363.476)
    (c) 2017 Microsoft Corporation. All rights reserved.
    
    C: \Users\AzureStackEdgeUser>net use \\myasetest1\myasesmbshare1 /u:aseuser
    Enter the password for 'aseuser' to connect to 'myasetest1':
    The command completed successfully.
    
    C: \Users\AzureStackEdgeUser>
    ```   

3. 키보드에서 Windows+R을 선택합니다.

4. **실행** 창에서 `\\<device name>`를 지정한 다음, **확인** 을 선택합니다.  

    ![Windows Run 대화 상자](media/azure-stack-edge-gpu-deploy-add-shares/run-window-1.png)

   파일 탐색기가 열립니다. 이제 폴더로 만든 공유가 표시됩니다. 파일 탐색기에서 공유(폴더)를 두 번 클릭하여 콘텐츠를 봅니다.
 
    ![SMB 공유에 연결](./media/azure-stack-edge-gpu-deploy-add-shares/file-explorer-smbshare-1.png)

    공유가 생성되면 데이터가 이러한 공유에 기록되고, 디바이스에서 해당 데이터를 클라우드로 푸시합니다.

### <a name="connect-to-an-nfs-share"></a>NFS 공유에 연결

Azure Stack Edge Pro 디바이스에 연결된 Linux 클라이언트에서 다음 절차를 수행합니다.

1. 클라이언트에 NFSv4 클라이언트가 설치되어 있는지 확인합니다. NFS 클라이언트를 설치하려면 다음 명령을 사용합니다.

   `sudo apt-get install nfs-common`

    자세한 내용을 알아보려면 [NFSv4 클라이언트 설치](https://help.ubuntu.com/community/NFSv4Howto)로 이동하세요.

2. NFS 클라이언트가 설치되면 다음 명령을 사용하여 Azure Stack Edge Pro 디바이스에서 만든 NFS 공유를 탑재합니다.

   `sudo mount -t nfs -o sec=sys,resvport <device IP>:/<NFS share on device> /home/username/<Folder on local Linux computer>`

    로컬 웹 UI의 **네트워크** 페이지에서 디바이스 IP를 가져올 수 있습니다.

    > [!IMPORTANT]
    > 공유 탑재 시 대규모 파일의 전송 속도가 개선될 경우 `sync` 옵션을 사용합니다.
    > 공유를 탑재하기 전에 로컬 컴퓨터에서 탑재 지점으로 작동할 디렉터리가 이미 만들어져 있는지 확인합니다. 이러한 디렉터리에는 파일 또는 하위 폴더가 없어야 합니다.

    다음 예제에서는 NFS를 통해 Azure Stack Edge Pro 디바이스의 공유에 연결하는 방법을 보여줍니다. 디바이스 IP는 `10.10.10.60`입니다. `mylinuxshare2` 공유는 ubuntuVM에 탑재됩니다. 공유 탑재 지점은 `/home/azurestackedgeubuntuhost/edge`입니다.

    `sudo mount -t nfs -o sec=sys,resvport 10.10.10.60:/mylinuxshare2 /home/azurestackedgeubuntuhost/Edge`

> [!NOTE] 
> 다음은 이 릴리스에 적용되는 주의 사항입니다.
> - 공유에 파일이 만들어지면 파일 이름 바꾸기가 지원되지 않습니다. 
> - 공유에서 파일을 삭제해도 Azure Storage 계정의 항목은 삭제되지 않습니다.
> - `rsync`를 사용하여 NFS를 통해 복사하는 경우 `--inplace` 플래그를 사용합니다. 

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음과 같은 Azure Stack Edge Pro 항목에 대해 알아보았습니다.

> [!div class="checklist"]
> * 공유 추가
> * 공유에 연결

Azure Stack Edge Pro를 사용하여 데이터를 변환하는 방법을 알아보려면 다음 자습서를 계속 진행하세요.

> [!div class="nextstepaction"]
> [Azure Stack Edge Pro를 사용하여 데이터 변환](./azure-stack-edge-j-series-deploy-configure-compute.md)