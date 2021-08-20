---
title: Azure Portal에서 GPU가 있는 Azure Stack Edge Pro 디바이스에 대한 인증서를 구성하기 위한 자습서 | Microsoft Docs
description: GPU가 있는 Azure Stack Edge Pro 배포에 대한 자습서에서는 물리적 디바이스에서 인증서를 구성하는 방법을 안내합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 06/30/2020
ms.author: alkohli
ms.openlocfilehash: 2b63b496fb06c2987161661ddbbfcfe74580e3bf
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2021
ms.locfileid: "113356620"
---
# <a name="tutorial-configure-certificates-for-your-azure-stack-edge-pro-with-gpu"></a>자습서: GPU가 있는 Azure Stack Edge Pro를 위한 인증서 구성

이 자습서에서는 로컬 웹 UI를 사용하여 온보드 GPU가 있는 Azure Stack Edge Pro 디바이스에 대한 인증서를 구성하는 방법을 설명합니다.

이 단계에 소요되는 시간은 선택한 특정 옵션 및 사용자 환경에서 인증서 흐름이 설정된 방법에 따라 달라질 수 있습니다.

이 자습서에서는 다음에 대해 알아봅니다.

> [!div class="checklist"]
>
> * 사전 요구 사항
> * 물리적 디바이스에 대한 인증서 구성

## <a name="prerequisites"></a>사전 요구 사항

Azure Stack Edge Pro device with GPU 디바이스를 구성하고 설정하기 전에 다음 사항을 확인합니다.

* [Azure Stack Edge Pro GPU 설치](azure-stack-edge-gpu-deploy-install.md)에서 설명한 대로 물리적 디바이스를 설치했습니다.
* 사용자 고유의 인증서를 가져오려는 경우:
    - 서명 체인 인증서를 포함하는 적절한 형식으로 인증서를 준비해야 합니다. 인증서에 대한 자세한 내용은 [인증서 관리](azure-stack-edge-gpu-manage-certificates.md)를 참조하세요.

    - 디바이스가 Azure Government에 배포되고 Azure 퍼블릭 클라우드에 배포되지 않은 경우 디바이스를 활성화하려면 서명 체인 인증서가 필요합니다. 
    인증서에 대한 자세한 내용은 [인증서 관리](azure-stack-edge-gpu-manage-certificates.md)를 참조하세요.


## <a name="configure-certificates-for-device"></a>디바이스에 대한 인증서 구성

1. **인증서** 페이지에서 인증서를 구성합니다. **디바이스** 페이지에서 디바이스 이름 또는 DNS 도메인을 변경했는지 여부에 따라 다음 인증서 옵션 중 하나를 선택할 수 있습니다.

    - 이전 단계에서 디바이스 이름 또는 DNS 도메인을 변경하지 않았고 자체 인증서를 가져오지 않으려면 이 단계를 건너뛰고 다음 단계를 진행할 수 있습니다. 디바이스에서 자체 서명된 인증서를 자동으로 생성하여 시작합니다. 

        ![로컬 웹 UI “인증서” 페이지](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-2.png)

    - 디바이스 이름 또는 DNS 도메인을 변경한 경우 인증서 상태가 **유효하지 않음** 으로 표시됩니다. 

        ![로컬 웹 UI "인증서" 페이지 2](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-1.png)    

        상태 세부 정보를 보고 싶은 인증서를 선택합니다.

        ![로컬 웹 UI "인증서" 페이지 3](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-1a.png)  

        인증서가 업데이트된 디바이스 이름 및 DNS 도메인(주체 이름 및 주체 대체 이름에 사용됨)을 반영하지 않기 때문입니다. 디바이스를 성공적으로 활성화하려면 다음 옵션 중 하나를 선택합니다. 
    
        - **모든 디바이스 인증서를 생성합니다**. 이러한 디바이스 인증서는 테스트 용도로만 사용해야 하고 프로덕션 워크로드에 사용하면 안 됩니다. 자세한 내용은 [Azure Stack Edge Pro GPU에서 디바이스 인증서 생성](#generate-device-certificates)을 참조하세요.

        - **사용자 고유의 인증서를 가져옵니다**. 사용자 고유의 서명된 엔드포인트 인증서와 해당 서명 체인을 가져올 수 있습니다. 먼저 서명 체인을 추가한 다음, 엔드포인트 인증서를 업로드합니다. **프로덕션 워크로드에 대한 자체 인증서를 항상 가져오는 것이 좋습니다.** 자세한 내용은 [Azure Stack Edge Pro GPU 디바이스에서 사용자 고유의 인증서 가져오기](#bring-your-own-certificates)를 참조하세요.
    
        - 사용자 고유의 인증서를 가져와 일부 디바이스 인증서를 생성할 수 있습니다. **인증서 생성** 옵션은 디바이스 인증서만 다시 생성합니다.

    - 디바이스 이름 또는 DNS 도메인을 변경한 후 인증서를 생성하지 않거나 자체 인증서를 가져오지 않은 경우에는 **활성화가 차단** 됩니다.


### <a name="generate-device-certificates"></a>디바이스 인증서 생성

디바이스 인증서를 생성하려면 다음 단계를 수행합니다.

다음 단계를 사용하여 Azure Stack Edge Pro GPU 디바이스 인증서를 다시 생성하고 다운로드합니다.

1. 디바이스의 로컬 UI에서 **구성 > 인증서** 로 이동합니다. **인증서 생성** 을 선택합니다.

    ![인증서 생성 및 다운로드 1](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-3.png)

2. **디바이스 인증서 생성** 에서 **생성** 을 선택합니다. 

    ![인증서 생성 및 다운로드 2](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-4.png)

    이제 디바이스 인증서가 생성되어 적용됩니다. 인증서를 생성하고 적용하는 데 몇 분 정도 걸립니다.
    
    > [!IMPORTANT]
    > 인증서 생성 작업이 진행되는 동안에는 사용자 고유의 인증서를 가져오거나 **+ 인증서 추가** 옵션을 통해 인증서를 추가하려고 시도하지 마십시오.

    작업이 성공적으로 완료되면 알림이 표시됩니다. **잠재적 캐시 문제를 방지하려면 브라우저를 다시 시작합니다.**
    
    ![인증서 생성 및 다운로드 4](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-5.png)

3. 인증서가 생성된 후: 

    - 모든 인증서의 상태가 **유효함** 으로 표시됩니다. 

        ![인증서 생성 및 다운로드 5](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-6.png)

    - 특정 인증서 이름을 선택하고 인증서 세부 정보를 볼 수 있습니다. 

        ![인증서 생성 및 다운로드 6](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-6a.png)

    - 이제 **다운로드** 열이 채워집니다. 이 열에는 다시 생성된 인증서를 다운로드할 수 있는 링크가 있습니다. 

        ![인증서 생성 및 다운로드 7](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-6b.png)


4. 인증서에 대한 다운로드 링크를 선택하고 메시지가 표시되면 인증서를 저장합니다. 

    ![인증서 생성 및 다운로드 8](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-7.png)

5. 다운로드하려는 모든 인증서에 대해 이 프로세스를 반복합니다. 
    
    ![인증서 생성 및 다운로드 9](./media/azure-stack-edge-gpu-deploy-configure-certificates/generate-certificate-8.png)

    디바이스에서 생성된 인증서는 다음과 같은 이름 형식의 DER 인증서로 저장됩니다. 

    `<Device name>_<Endpoint name>.cer`. 이러한 인증서에는 디바이스에 설치된 해당 인증서에 대한 공개 키가 포함되어 있습니다. 

Azure Stack Edge 디바이스의 엔드포인트에 액세스하는 데 사용하는 클라이언트 시스템에 이러한 인증서를 설치해야 합니다. 이러한 인증서는 클라이언트와 디바이스 간에 트러스트를 설정합니다.

디바이스에 액세스하는 데 사용하는 클라이언트에서 이러한 인증서를 가져오고 설치하려면 [Azure Stack Edge Pro GPU 디바이스에 액세스하는 클라이언트에서 인증서 가져오기](azure-stack-edge-gpu-manage-certificates.md#import-certificates-on-the-client-accessing-the-device)의 단계를 수행합니다. 

Azure Storage Explorer를 사용하는 경우 클라이언트에 PEM 형식으로 인증서를 설치하고 디바이스에서 생성된 인증서를 PEM 형식으로 변환해야 합니다. 

> [!IMPORTANT]
> - 다운로드 링크는 디바이스에서 생성된 인증서에 대해서만 사용할 수 있으며 사용자 고유의 인증서를 가져오는 경우에는 사용할 수 없습니다.
> - 다른 인증서 요구 사항이 충족되는 한 디바이스에서 생성된 인증서를 혼합하고 사용자 고유의 인증서를 가져올 수 있습니다. 자세한 내용은 [인증서 요구 사항](azure-stack-edge-gpu-certificate-requirements.md)으로 이동하세요.
    

### <a name="bring-your-own-certificates"></a>사용자 고유의 인증서 가져오기

사용자 고유의 인증서를 가져올 수 있습니다. 

- 먼저 [Azure Stack Edge 디바이스에서 사용할 수 있는 인증서 유형](azure-stack-edge-gpu-certificates-overview.md)을 이해합니다.
- 다음으로 [각 인증서 유형에 대한 인증서 요구 사항](azure-stack-edge-gpu-certificate-requirements.md)을 검토합니다.
- 그런 다음, [Azure PowerShell 통해 인증서를 만들](azure-stack-edge-gpu-create-certificates-powershell.md)거나 [준비 검사기 도구를 통해 인증서를 만들](azure-stack-edge-gpu-create-certificates-tool.md) 수 있습니다.
- 마지막으로 [인증서를 적절한 형식으로 변환](azure-stack-edge-gpu-prepare-certificates-device-upload.md)하여 디바이스에 업로드할 수 있도록 합니다.

서명 체인을 포함한 사용자 고유의 인증서를 업로드하려면 다음 단계를 수행합니다.

1. 인증서를 업로드하려면 **인증서** 페이지에서 **+ 인증서 추가** 를 선택합니다.

    ![로컬 웹 UI "인증서" 페이지 4](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-1.png)

2. 먼저 서명 체인을 업로드하고 **유효성 검사 및 추가** 를 선택합니다.

    ![로컬 웹 UI "인증서" 페이지 5](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-2.png)

3. 이제 다른 인증서를 업로드할 수 있습니다. 예를 들어 Azure Resource Manager 및 Blob Storage 엔드포인트 인증서를 업로드할 수 있습니다.

    ![로컬 웹 UI "인증서" 페이지 6](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-3.png)

    로컬 웹 UI 인증서를 업로드할 수도 있습니다. 이 인증서를 업로드한 후에는 브라우저를 시작하고 캐시를 지워야 합니다. 그런 다음, 디바이스 로컬 웹 UI에 연결해야 합니다.  

    ![로컬 웹 UI "인증서" 페이지 7](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-5.png)

    노드 인증서를 업로드할 수도 있습니다.

    ![로컬 웹 UI "인증서" 페이지 8](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-4.png)

    언제든지 인증서를 선택하고 세부 정보를 검토하여 업로드한 인증서와 일치하는지 확인할 수 있습니다.

    ![로컬 웹 UI "인증서" 페이지 9](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-6.png)

    인증서 페이지가 새로 추가된 인증서를 반영하도록 업데이트됩니다.

    ![로컬 웹 UI "인증서" 페이지 10](./media/azure-stack-edge-gpu-deploy-configure-certificates/add-certificate-7.png)  

    > [!NOTE]
    > Azure 퍼블릭 클라우드를 제외하고, 모든 클라우드 구성(Azure Government 또는 Azure Stack)을 활성화하기 전에 서명 체인 인증서를 가져와야 합니다.


이제 디바이스가 활성화될 준비가 되었습니다. **< 시작으로 돌아가기** 를 선택합니다.


## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음에 대해 알아봅니다.

> [!div class="checklist"]
>
> * 사전 요구 사항
> * 물리적 디바이스에 대한 인증서 구성

Azure Stack Edge Pro GPU 디바이스를 활성화하는 방법을 알아보려면 다음을 참조하세요.

> [!div class="nextstepaction"]
> [Azure Stack Edge Pro GPU 디바이스 활성화](./azure-stack-edge-gpu-deploy-activate.md)
