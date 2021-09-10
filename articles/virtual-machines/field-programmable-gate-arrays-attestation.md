---
title: Azure FPGFA Attestation 서비스
description: NP 시리즈 VM에 대한 증명 서비스입니다.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: vikancha
ms.openlocfilehash: 034acde1f99409ef7d541d7705bb995e0db00d3b
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122689105"
---
# <a name="fpga-attestation-for-azure-np-series-vms-preview"></a>Azure NP 시리즈 VM에 대한 FPGA 증명(미리 보기)

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 균일 확장 집합

FPGA Attestation 서비스는 Xilinx 도구 집합에서 생성한 디자인 검사점 파일("netlist"라고 함)에 대해 일련의 유효성 검사를 수행하고, NP 시리즈 VM의 Xilinx U250 FPGA 카드에 로드할 수 있는 유효성 검사가 완료된 이미지("비트스트림"이라고 함)를 포함하는 파일을 생성합니다.  

## <a name="prerequisites"></a>사전 요구 사항  

Azure 구독 및 Azure Storage 계정이 필요합니다. 구독은 Azure에 대한 액세스를 제공하고, 스토리지 계정은 증명 서비스의 netlist 및 출력 파일을 저장하는 데 사용됩니다.  

증명 요청을 제출하는 데 필요한 PowerShell 및 Bash 스크립트가 제공됩니다.   이 스크립트는 Windows 및 Linux에서 실행할 수 있는 Azure CLI를 사용합니다. PowerShell은 Windows, Linux 및 macOS에서 실행할 수 있습니다.  

[Azure CLI 다운로드(필수)](/cli/azure/install-azure-cli)

[Windows, Linux 및 macOS용 PowerShell 다운로드(PowerShell 스크립트에만 해당)](/powershell/scripting/install/installing-powershell)

증명 서비스에 제출할 권한이 있는 테넌트 및 구독 ID가 있어야 합니다. 액세스를 요청하려면 [https://aka.ms/AzureFPGAAttestationPreview](https://aka.ms/AzureFPGAAttestationPreview)를 방문하세요. 

## <a name="building-your-design-for-attestation"></a>증명을 위한 디자인 빌드  

디자인 빌드 시 권장되는 Xilinx 도구 집합은 Vitis 2020.2입니다. 이전 버전의 도구 집합을 사용하여 생성된 netlist 파일 및 2020.2와 여전히 호환되는 파일을 사용할 수 있습니다. 빌드 대상으로 사용할 올바른 셸을 로드했는지 확인합니다. 현재 지원되는 버전은 xilinx_u250_gen3x16_xdma_2_1_202010_1입니다. 지원되는 파일은 Xilinx Alveo 라운지에서 다운로드할 수 있습니다. 

비트스트림 대신 netlist를 포함하는 xclbin 파일을 빌드하려면 Vitis(v++ 명령줄)에 다음 인수를 포함해야 합니다.   

`--advanced.param compiler.acceleratorBinaryContent=dcp`

## <a name="logging-into-azure"></a>Azure에 로그인  

Azure를 사용하여 작업을 수행하기 전에 Azure에 로그인하여 서비스를 호출할 권한이 있는 구독을 설정해야 합니다. 이를 위해 `az login` 및 `az account set –s <Sub ID or Name>` 명령을 사용합니다. 이 프로세스에 대한 추가 정보는 [Azure CLI로 로그인](/cli/azure/authenticate-azure-cli)에 설명되어 있습니다. 명령줄에서 **대화형으로 로그인** 또는 **자격 증명을 사용하여 로그인** 옵션을 사용합니다.  

## <a name="creating-a-storage-account-and-blob-container"></a>스토리지 계정 및 Blob 컨테이너 만들기  

증명 서비스에서 액세스할 수 있도록 netlist 파일을 Azure Storage Blob 컨테이너에 업로드해야 합니다.  

계정, 컨테이너를 만들고 해당 컨테이너에 netlist를 Blob으로 업로드하는 방법에 대한 자세한 내용은 [https://docs.microsoft.com/en-us/azure/storage/blobs/storage-quickstart-blobs-cli](../storage/blobs/storage-quickstart-blobs-cli.md) 페이지를 참조하세요.  

이 경우 Azure Portal을 사용할 수도 있습니다.  

## <a name="upload-your-netlist-file-to-azure-blob-storage"></a>Azure Blob Storage에 netlist 파일 업로드  

파일을 복사하는 방법은 여러 가지가 있습니다. az storage upload cmdlet을 사용하는 예는 다음과 같습니다. Az 명령은 Linux와 Windows 모두에서 실행됩니다. "Blob" 이름에 임의의 이름을 선택할 수 있지만 xclbin 확장명을 유지해야 합니다. 

```az storage blob upload --account-name <storage account to receive netlist> --container-name <blob container name> --name <blob filename> --file <local file with netlist>  ```

## <a name="download-the-attestation-scripts"></a>증명 스크립트 다운로드  

다음 Azure Storage Blob 컨테이너에서 유효성 검사 스크립트를 다운로드할 수 있습니다.  

[https://fpgaattestation.blob.core.windows.net/validationscripts/validate.zip](https://fpgaattestation.blob.core.windows.net/validationscripts/validate.zip)

Zip 파일에는 두 개의 PowerShell 스크립트가 있습니다. 하나는 제출용이고 다른 하나는 모니터링용이며 세 번째 파일은 두 기능을 수행하는 bash 스크립트입니다.  

## <a name="running-the-attestation-scripts"></a>증명 스크립트 실행  

스크립트를 실행하려면 스토리지 계정의 이름, netlist 파일이 저장되는 Blob 컨테이너의 이름 및 netlist 파일의 이름을 제공해야 합니다. 또한 사용자는 netlist가 아닌 컨테이너에 대한 읽기/쓰기 액세스 권한을 부여하는 서비스 SAS(공유 액세스 서명)를 만들어야 합니다. 이 SAS는 증명 서비스에서 netlist 파일의 로컬 복사본을 만들고 유효성 검사 프로세스의 결과 출력 파일을 컨테이너에 다시 기록하는 데 사용됩니다.  

여기에서 공유 액세스 서명의 개요를 볼 수 있으며, 서비스 SAS에 대한 자세한 정보도 볼 수 있습니다. 서비스 SAS 페이지에는 생성된 SAS를 보호하는 방법에 대한 중요한 주의 사항이 포함되어 있습니다.  이 주의 사항을 읽고 악의적이거나 의도치 않은 사용으로부터 SAS를 보호해야 하는 이유를 파악하세요.  

az storage container generate-sas cmdlet을 사용하여 컨테이너의 SAS를 생성할 수 있습니다. 만료 시간은 제출 시간으로부터 최소 몇 시간 이후로, UTC 형식으로 지정합니다. 약 6시간이면 충분하고도 남습니다.  

가상 디렉터리를 사용하려는 경우에는 컨테이너 인수의 일부로 디렉터리 계층 구조를 포함해야 합니다. 예를 들어 "netlists" 라는 컨테이너가 있고 netlists blob을 포함하는 "image1"이라는 가상 디렉터리가 있는 경우 "netlists/image1"을 컨테이너 이름으로 지정합니다. 더 깊은 계층 구조를 지정하려면 디렉터리 이름을 추가합니다. 

### <a name="powershell"></a>PowerShell   

```powershell
$sas=$(az storage container generate-sas --account-name <storage acct name> --name <blob container name> --https-only --permissions rwc --expiry <e.g., 2021-01-07T17:00Z> --output tsv)

.\Validate-FPGAImage.ps1 -StorageAccountName <storage acct name> -Container <blob container name> -BlobContainerSAS $sas -NetlistName <netlist blob filename>
```

### <a name="bash"></a>Bash  

```bash
sas=az storage container generate-sas --account-name <storage acct name> --name <blob container name> --https-only --permissions rwc --expiry <2021-01-07T17:00Z> --output tsv  

validate-fpgaimage.sh --storage-account <storage acct name> --container <blob container name> --netlist-name <netlist blob filename> --blob-container-sas $sas
``` 

## <a name="checking-on-the-status-of-your-submission"></a>제출 상태 확인  

Attestation 서비스가 제출의 오케스트레이션 ID를 반환합니다. 제출 스크립트가 완료를 폴링하는 방식으로 제출을 자동으로 모니터링하기 시작합니다. 오케스트레이션 ID는 제출에 발생한 문제를 검토할 때 주로 사용되므로 문제가 발생할 경우를 대비해서 보관해 두세요. 참고로, 작은 netlist 파일(300MB 크기)에 대한 증명을 완료하는 데 소요되는 시간은 약 30분이지만 1.6GB 파일은 1시간이 걸립니다. 

Monitor-Validation.ps1 스크립트를 언제든지 호출하여 증명의 상태와 결과를 확인하고, 오케스트레이션 ID를 인수로 제공할 수 있습니다.  

`.\Monitor-Validation.ps1 -OrchestrationId <orchestration ID>`

또는 HTTP 게시 요청을 증명 서비스 엔드포인트에 제출할 수 있습니다.  

`https://fpga-attestation.azurewebsites.net/api/ComputeFPGA_HttpGetStatus`

요청 본문은 증명 요청의 구독 ID, 테넌트 ID 및 오케스트레이션 ID를 포함해야 합니다.  

```json
{  
  "OrchestrationId": "<orchestration ID>",  
  "ClientSubscriptionId": "<your subscription ID>",  
  "ClientTenantId": "<your tenant ID>"
}
```

## <a name="post-validation-steps"></a>게시 유효성 검사 단계

서비스가 해당 출력을 다시 컨테이너에 기록합니다. 유효성 검사에 성공하면 컨테이너에 원래 netlist 파일(abc.xclbin), 비트스트림이 포함된 파일(abc.bit.xclbin), 저장된 비트스트림의 프라이빗 위치를 식별하는 파일(abc.azure.xclbin), 4개의 로그 파일(시작 프로세스용 하나(abc-log.txt), 유효성 검사를 수행하는 3개의 병렬 단계별로 각각 하나)이 있습니다. 이러한 파일에는 *logPhaseX.txt라는 이름이 지정되며, 여기서 X는 단계를 가리키는 숫자입니다. azure.xclbin은 VM에서 유효성 검사가 완료된 이미지를 U250에 업로드하도록 신호를 보내는 데 사용됩니다. 

유효성 검사에 실패하면 실패한 단계를 나타내는 error-*.txt 파일이 기록됩니다. 또한 로그 파일을 통해 오류 로그에 증명이 실패했다는 내용이 있는지 확인하세요. 지원팀 문의하는 경우 지원 요청에 이러한 모든 파일을 오케스트레이션 ID와 함께 포함해야 합니다.  

Azure Portal을 사용하여 컨테이너를 만들 수 있을 뿐만 아니라 netlist를 업로드하고 비트스트림 및 로그 파일을 다운로드할 수도 있습니다. 증명 요청을 제출하고 포털을 통해 진행 상황을 모니터링하는 작업은 현재 지원되지 않으며 위에서 설명한 대로 스크립트를 통해 수행해야 합니다.
