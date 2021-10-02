---
title: Azure Stack Edge Pro GPU에서 인증서 사용 | Microsoft Docs
description: 인증서를 사용하는 이유, 인증서 유형 및 인증서를 디바이스에 업로드하는 방법을 포함하여 Azure Stack Edge Pro GPU 디바이스에서 인증서를 사용하는 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 06/01/2021
ms.author: alkohli
ms.openlocfilehash: 15cfd2b7188f84e14aa12824f8d5fab06d226330
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129363955"
---
# <a name="upload-import-and-export-certificates-on-azure-stack-edge-pro-gpu"></a>Azure Stack Edge Pro GPU에서 인증서 업로드, 가져오기, 내보내기

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure Stack Edge 디바이스와 여기에 연결하는 클라이언트 간에 신뢰할 수 있는 보안 통신을 보장하기 위해 자체 서명된 인증서를 사용하거나 고유한 인증서를 사용할 수 있습니다. 이 문서에서는 해당 인증서의 업로드, 가져오기, 내보내기를 수행하거나 만료 날짜를 보는 방법을 포함하여 해당 인증서를 관리하는 방법을 설명합니다.

해당 인증서를 만드는 방법에 관한 자세한 내용은 [Azure PowerShell을 사용하여 인증서 만들기](azure-stack-edge-gpu-create-certificates-powershell.md)를 참조하세요.


## <a name="upload-certificates-on-your-device"></a>디바이스에 인증서 업로드

고유한 인증서를 사용하는 경우 기본적으로 디바이스용으로 만든 인증서는 클라이언트의 **개인 저장소** 에 있습니다. 이러한 인증서는 클라이언트에서 적절한 형식 파일로 내보내야 합니다. 그러면 디바이스에 업로드할 수 있습니다.


### <a name="prerequisites"></a>필수 구성 요소

디바이스에 루트 인증서와 엔드포인트 인증서를 업로드하기 전에 인증서를 적절한 형식으로 내보내야 합니다.

- 루트 인증서는 `.cer` 파일 확장명의 DER 형식 파일로 내보내야 합니다. 자세한 단계는 [DER 형식으로 인증서 내보내기](azure-stack-edge-gpu-prepare-certificates-device-upload.md#export-certificates-as-der-format)를 참조하세요.
- 엔드포인트 인증서는 프라이빗 키와 함께 *.pfx* 파일로 내보내야 합니다. 자세한 단계는 [프라이빗 키와 함께 *.pfx* 파일로 인증서 내보내기](azure-stack-edge-gpu-prepare-certificates-device-upload.md#export-certificates-as-pfx-format-with-private-key)를 참조하세요. 

### <a name="upload-certificates"></a>인증서 업로드 

디바이스에 루트 및 엔드포인트 인증서를 업로드하려면 로컬 웹 UI의 **인증서** 페이지에서 **+ 인증서 추가** 옵션을 사용합니다. 다음 단계를 수행합니다.

1. 먼저 루트 인증서를 업로드합니다. 로컬 웹 UI에서 **인증서** 로 이동 합니다.
1. **+ 인증서 추가** 를 선택합니다.

    ![서명 체인 인증서 추가 1](media/azure-stack-edge-gpu-manage-certificates/add-cert-1.png)

1. 인증서를 저장 합니다.

#### <a name="upload-endpoint-certificate"></a>업로드 끝점 인증서

1. 다음으로 엔드포인트 인증서를 업로드합니다. 

    ![서명 체인 인증서 추가 2](media/azure-stack-edge-gpu-manage-certificates/add-cert-2.png)

    *.pfx* 형식의 인증서 파일을 선택하고, 인증서를 내보낼 때 제공한 암호를 입력합니다. Azure Resource Manager 인증서를 적용하는 데 몇 분 정도 걸릴 수 있습니다.

    서명 체인이 먼저 업데이트되지 않고 엔드포인트 인증서를 업로드하려고 하면 오류가 발생합니다.

    ![인증서 적용 오류](media/azure-stack-edge-gpu-manage-certificates/apply-cert-error-1.png)

    돌아가서 서명 체인 인증서를 업로드한 다음, 엔드포인트 인증서를 업로드하고 적용합니다.

> [!IMPORTANT]
> 디바이스 이름 또는 DNS 도메인이 변경되면 새 인증서를 만들어야 합니다. 그런 다음, 클라이언트 인증서와 디바이스 인증서를 새 디바이스 이름 및 DNS 도메인으로 업데이트해야 합니다. 

#### <a name="upload-kubernetes-certificates"></a>업로드 인증서 Kubernetes

Kubernetes 인증서는 Edge Container Registry 또는 Kubernetes 대시보드의 경우에만 가능 합니다. 각각의 경우 인증서와 키 파일을 업로드 해야 합니다. 다음 단계를 수행 하 여 Kubernetes 인증서를 만들고 업로드 합니다.


1. `openssl`를 사용 하 여 Kubernetes 대시보드 인증서 또는에 지 Container Registry를 만듭니다. 인증서를 만드는 데 사용 하는 시스템에 openssl를 설치 해야 합니다. Windows 시스템에서 Chocolatey를 사용 하 여를 설치할 수 있습니다 `openssl` . Chocolatey를 설치한 후 PowerShell을 열고 다음을 입력 합니다.
    
    ```powershell
    choco install openssl
    ```
1. `openssl`를 사용 하 여 이러한 인증서를 만듭니다. `cert.pem`인증서 파일 및 `key.pem` 키 파일이 만들어집니다.  

    - Edge Container Registry의 경우 다음 명령을 사용 합니다.
    
        ```powershell
        openssl req -newkey rsa:4096 -nodes -sha256 -keyout key.pem -x509 -days 365 -out cert.pem -subj "/CN=<ecr.endpoint-suffix>"
        ``` 
        출력의 예제는 다음과 같습니다. 

        ```powershell
        PS C:\WINDOWS\system32> openssl req -newkey rsa:4096 -nodes -sha256 -keyout key.pem -x509 -days 365 -out cert.pem -subj "/CN=ecr.dbe-1d6phq2.microsoftdatabox.com"
        Generating a RSA private key
        .....................++++....++++
        writing new private key to 'key.pem'
        -----
        PS C:\WINDOWS\system32>
        ```    
    - Kubernetes 대시보드 인증서의 경우 다음 명령을 사용 합니다.  
     
        ```powershell
        openssl req -newkey rsa:4096 -nodes -sha256 -keyout key.pem -x509 -days 365 -out cert.pem -subj "/CN=<<kubernetes-dashboard.endpoint-suffix> OR <endpoint-suffix>>"
        ```
        출력의 예제는 다음과 같습니다. 

        ```powershell
        PS C:\WINDOWS\system32> openssl req -newkey rsa:4096 -nodes -sha256 -keyout key.pem -x509 -days 365 -out cert.pem -subj "/CN=kubernetes-dashboard.dbe-1d8phq2.microsoftdatabox.com"
        Generating a RSA private key
        .....................++++....++++
        writing new private key to 'key.pem'
        -----
        PS C:\WINDOWS\system32>
        ```          
1. Kubernetes 인증서와 앞에서 생성 한 해당 키 파일을 업로드 합니다.
    
    - Edge Container Registry의 경우
    
        ![Edge Container Registry 인증서 및 키 파일을 추가 하기 위한 스크린샷](media/azure-stack-edge-gpu-manage-certificates/add-cert-3.png)      

    - Kubernetes 대시보드     

        ![Kubernetes 대시보드 인증서 및 키 파일을 추가 하기 위한 스크린샷](media/azure-stack-edge-gpu-manage-certificates/add-cert-4.png) 

## <a name="import-certificates-on-the-client-accessing-the-device"></a>디바이스에 액세스하는 클라이언트에서 인증서 가져오기

디바이스에서 생성한 인증서를 사용하거나 고유한 인증서를 사용할 수 있습니다. 디바이스에서 생성한 인증서를 사용하는 경우 해당 인증서를 적절한 인증서 저장소로 가져오려면 먼저 클라이언트에 인증서를 다운로드해야 합니다. [디바이스에 액세스하는 클라이언트에 인증서 다운로드](azure-stack-edge-gpu-deploy-configure-certificates.md#generate-device-certificates)를 참조하세요.

두 경우에 모두 만들고 디바이스에 업로드한 인증서는 Windows 클라이언트(디바이스에 액세스)에서 적절한 인증서 저장소로 가져와야 합니다. 

- 이제 DER 형식으로 내보낸 루트 인증서를 클라이언트 시스템의 **신뢰할 수 있는 루트 인증 기관** 으로 가져와야 합니다. 자세한 단계는 [신뢰할 수 있는 루트 인증 기관 저장소로 인증서 가져오기](#import-certificates-as-der-format)를 참조하세요.

- `.pfx`로 내보낸 엔드포인트 인증서를 `.cer` 확장명의 DER로 내보내야 합니다. 그런 다음, 이 `.cer`을 시스템의 **개인 인증서 저장소** 로 가져옵니다. 자세한 단계는 [개인 인증서 저장소로 인증서 가져오기](#import-certificates-as-der-format)를 참조하세요.

### <a name="import-certificates-as-der-format"></a>DER 형식으로 인증서 가져오기 

Windows 클라이언트에서 인증서를 가져오려면 다음 단계를 수행합니다.

1. 마우스 오른쪽 단추로 파일을 클릭하고, **인증서 설치** 를 선택합니다. 그러면 인증서 가져오기 마법사가 시작됩니다.

    ![인증서 가져오기 1](media/azure-stack-edge-gpu-manage-certificates/import-cert-1.png)

2. **저장소 위치** 에 대해 **로컬 컴퓨터** 를 선택하고, **다음** 을 선택합니다.

    ![인증서 가져오기 2](media/azure-stack-edge-gpu-manage-certificates/import-cert-2.png)

3. **모든 인증서를 다음 저장소에 저장** 을 선택한 다음, **찾아보기** 를 선택합니다. 

    - 개인 저장소로 가져오려면 원격 호스트의 개인 저장소로 이동하고, **다음** 을 선택합니다.

        ![인증서 가져오기 4](media/azure-stack-edge-gpu-manage-certificates/import-cert-4.png)


    - 신뢰할 수 있는 저장소로 가져오려면 신뢰할 수 있는 루트 인증 기관으로 이동하고, **다음** 을 선택합니다.

        ![인증서 가져오기 3](media/azure-stack-edge-gpu-manage-certificates/import-cert-3.png)

 
4. **마침** 을 선택합니다. "가져오기를 완료했습니다."라는 메시지가 표시됩니다.


## <a name="view-certificate-expiry"></a>인증서 만료 보기

사용자 고유의 인증서를 가져오는 경우 인증서는 일반적으로 1년 또는 6개월 후에 만료됩니다. 인증서의 만료 날짜를 확인하려면 디바이스의 로컬 웹 UI에서 **인증서** 페이지로 이동합니다. 특정 인증서를 선택하면 인증서의 만료 날짜가 표시됩니다.


## <a name="next-steps"></a>다음 단계

[인증서 문제 해결](azure-stack-edge-gpu-certificate-troubleshooting.md) 방법 알아보기
