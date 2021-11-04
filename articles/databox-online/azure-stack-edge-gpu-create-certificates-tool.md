---
title: Azure Stack 허브 준비 검사기 도구를 통해 Azure Stack Edge Pro GPU용 인증서 만들기
description: Azure Stack Hub 준비 상태 검사기 도구를 사용하여 인증서 요청을 만든 다음, Azure Stack Edge Pro GPU 디바이스에 인증서를 가져오고 설치하는 방법을 설명합니다.
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 10/01/2021
ms.author: alkohli
ms.openlocfilehash: 5c6782c3b4644607f292587beafccb86fd7b8119
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131057802"
---
# <a name="create-certificates-for-your-azure-stack-edge-pro-gpu-using-azure-stack-hub-readiness-checker-tool"></a>Azure Stack 허브 준비 검사기 도구를 사용하여 Azure Stack Edge Pro GPU용 인증서 만들기 

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

이 문서에서는 Azure Stack Hub 준비 상태 검사기 도구를 사용하여 Azure Stack Edge Pro의 인증서를 만드는 방법을 설명합니다. 

## <a name="using-azure-stack-hub-readiness-checker-tool"></a>Azure Stack Hub 준비 상태 검사기 도구 사용

Azure Stack Hub 준비 상태 검사기 도구를 사용하여 Azure Stack Edge Pro 디바이스 배포의 CSR(인증서 서명 요청)을 만듭니다. Azure Stack Edge Pro 디바이스에 대한 주문을 제출하고 디바이스가 도착할 때까지 기다린 후 이 요청을 만들 수 있습니다.

> [!NOTE]
> 이 도구는 프로덕션 디바이스가 아닌 테스트 또는 개발 용도로만 사용합니다. 

Azure Stack Hub 준비 상태 검사기 도구(AzsReadinessChecker)를 사용하여 다음 인증서를 요청할 수 있습니다.

- Azure Resource Manager 인증서
- 로컬 UI 인증서
- 노드 인증서
- Blob 인증서
- VPN 인증서


## <a name="prerequisites"></a>필수 조건

Azure Stack Edge Pro 디바이스 배포의 CSR을 만들려면 다음을 확인합니다. 

- Windows 10 또는 Windows Server 2016 이상을 실행하는 클라이언트가 있습니다. 
- 이 시스템의 [PowerShell 갤러리에서](https://aka.ms/AzsReadinessChecker) Microsoft Azure Stack Hub 준비 상태 검사기 도구를 다운로드했습니다.
- 인증서에 관한 다음 정보가 있습니다.
  - 디바이스 이름
  - 노드 일련 번호
  - 외부 FQDN(정규화된 도메인 이름)

## <a name="generate-certificate-signing-requests"></a>인증서 서명 요청 생성

다음 단계를 사용하여 Azure Stack Edge Pro 디바이스 인증서를 준비합니다.

1. 관리자 권한으로 PowerShell을 실행합니다(5.1 이상).
2. Azure Stack Hub 준비 상태 검사기 도구를 설치합니다. PowerShell 프롬프트에서 다음을 입력합니다. 

    ```azurepowershell
    Install-Module -Name Microsoft.AzureStack.ReadinessChecker
    ```

    설치된 버전을 가져오려면 다음을 입력합니다.  

    ```azurepowershell
    Get-InstalledModule -Name Microsoft.AzureStack.ReadinessChecker  | ft Name, Version 
    ```

3. 인증서가 아직 없는 경우 모든 인증서에 사용할 디렉터리를 만듭니다. 유형: 
    
    ```azurepowershell
    New-Item "C:\certrequest" -ItemType Directory
    ``` 
    
4. 인증서 요청을 만들려면 다음 정보를 제공합니다. VPN 인증서를 생성하는 경우 이 입력 중 일부가 적용되지 않습니다.
    
    |입력 |Description  |
    |---------|---------|
    |`OutputRequestPath`|인증서 요청을 만들려는 로컬 클라이언트의 파일 경로입니다.        |
    |`DeviceName`|디바이스의 로컬 웹 UI에 있는 **디바이스** 페이지의 디바이스 이름입니다. <br> 이 필드는 VPN 인증서에는 필요하지 않습니다.         |
    |`NodeSerialNumber`|디바이스의 로컬 웹 UI에 있는 **네트워크** 페이지의 디바이스 노드 일련 번호입니다. <br> 이 필드는 VPN 인증서에는 필요하지 않습니다.       |
    |`ExternalFQDN`|디바이스의 로컬 웹 UI에 있는 **디바이스** 페이지의 DNSDomain 값입니다.         |
    |`RequestType`|요청 유형은 다양한 엔드포인트에 대해 서로 다른 인증서를 사용하는 경우 `MultipleCSR`이고 모든 엔드포인트에 대해 단일 인증서를 사용하는 경우 `SingleCSR`일 수 있습니다. <br> 이 필드는 VPN 인증서에는 필요하지 않습니다.     |

    VPN 인증서를 제외한 모든 인증서에 대해 다음을 입력합니다. 
    
    ```azurepowershell
    $edgeCSRparams = @{
        CertificateType = 'AzureStackEdgeDEVICE'
        DeviceName = 'myTEA1'
        NodeSerialNumber = 'VM1500-00025'
        externalFQDN = 'azurestackedge.contoso.com'
        requestType = 'MultipleCSR'
        OutputRequestPath = "C:\certrequest"
    }
    New-AzsCertificateSigningRequest @edgeCSRparams
    ```

    VPN 인증서를 만드는 경우 다음을 입력합니다. 

    ```azurepowershell
    $edgeCSRparams = @{
        CertificateType = 'AzureStackEdgeVPN'
        externalFQDN = 'azurestackedge.contoso.com'
        OutputRequestPath = "C:\certrequest"
    }
    New-AzsCertificateSigningRequest @edgeCSRparams
    ```

    
5. 위의 OutputRequestPath 매개 변수에 지정한 디렉터리에서 인증서 요청 파일을 찾을 수 있습니다. `MultipleCSR` 매개 변수를 사용하는 경우 확장명이 `.req`인 다음 네 개의 파일이 표시됩니다.

    
    |파일 이름  |인증서 요청 유형  |
    |---------|---------|
    |`DeviceName`으로 시작     |로컬 웹 UI 인증서 요청      |
    |`NodeSerialNumber`로 시작     |노드 인증서 요청         |
    |`login`로 시작     |Azure Resource Manager 엔드포인트 인증서 요청       |
    |`wildcard`로 시작     |Blob Storage 인증서 요청. 여기에는 디바이스에서 만들 수 있는 모든 스토리지 계정을 포함하기 때문에 와일드카드가 포함됩니다.          |
    |`AzureStackEdgeVPNCertificate`로 시작     |VPN 클라이언트 인증서 요청.         |

    INF 폴더도 표시됩니다. 여기에는 관리가 포함 됩니다.\<edge-devicename\> 인증서 세부 정보를 설명 하는 일반 텍스트로 된 정보 파일입니다.  


6. 이 파일을 인증 기관(내부 또는 퍼블릭)에 제출합니다. 해당 CA가 생성된 요청을 사용하여 [노드 인증서](azure-stack-edge-gpu-certificates-overview.md#node-certificates), [엔드포인트 인증서](azure-stack-edge-gpu-certificates-overview.md#endpoint-certificates) 및 [로컬 UI 인증서](azure-stack-edge-gpu-certificates-overview.md#local-ui-certificates)의 Azure Stack Edge Pro 인증서 요구 사항을 충족하는 인증서를 생성하는지 확인합니다.

## <a name="prepare-certificates-for-deployment"></a>인증서 배포 준비

CA(인증 기관)에서 가져오는 인증서 파일은 Azure Stack Edge Pro 디바이스의 인증서 요구 사항과 일치하는 속성을 사용하여 가져오고 내보내야 합니다. 인증서 서명 요청을 생성한 동일한 시스템에서 다음 단계를 완료합니다.

- 인증서를 가져오려면 [Azure Stack Edge Pro 디바이스에 액세스하는 클라이언트에서 인증서 가져오기](azure-stack-edge-gpu-manage-certificates.md#import-certificates-on-the-client-accessing-the-device)의 단계를 수행합니다.

- 인증서를 내보내려면 [Azure Stack Edge Pro 디바이스에 액세스하는 클라이언트에서 인증서 내보내기](azure-stack-edge-gpu-prepare-certificates-device-upload.md#export-certificates-as-pfx-format-with-private-key)의 단계를 수행합니다.


## <a name="validate-certificates"></a>인증서 유효성 검사

먼저 적절한 폴더 구조를 생성하고 해당 폴더에 인증서를 저장합니다. 그런 다음, 도구를 사용하여 인증서의 유효성을 검사합니다.

1. 관리자 권한으로 PowerShell을 실행합니다.

2. 적절한 폴더 구조를 생성하려면 프롬프트에 다음을 입력합니다.

    `New-AzsCertificateFolder -CertificateType AzureStackEdgeDevice -OutputPath "$ENV:USERPROFILE\Documents\AzureStackCSR"`

3. PFX 암호를 보안 문자열로 변환합니다. 유형:       

    `$pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString` 

4. 그런 다음, 인증서의 유효성을 검사합니다. 유형:

    `Invoke-AzsCertificateValidation -CertificateType AzureStackEdgeDevice -DeviceName mytea1 -NodeSerialNumber VM1500-00025 -externalFQDN azurestackedge.contoso.com -CertificatePath $ENV:USERPROFILE\Documents\AzureStackCSR\AzureStackEdge -pfxPassword $pfxPassword`

## <a name="next-steps"></a>다음 단계

[기기에 인증서를 업로드합니다](azure-stack-edge-gpu-manage-certificates.md).
