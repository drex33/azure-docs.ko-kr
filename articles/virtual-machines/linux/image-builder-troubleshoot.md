---
title: Azure Image Builder Service 문제 해결
description: Azure VM Image Builder Service를 사용할 때 발생하는 일반적인 문제와 오류를 해결합니다.
author: kof-f
ms.author: kofiforson
ms.reviewer: cynthn
ms.date: 10/02/2020
ms.topic: troubleshooting
ms.service: virtual-machines
ms.subservice: image-builder
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4117926fe8de79a295fa3c0a52c1ca54816496ba
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131427991"
---
# <a name="troubleshoot-azure-image-builder-service"></a>Azure Image Builder Service 문제 해결

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: 유연한 확장 집합 

이 문서는 Azure Image Builder Service를 사용할 때 발생할 수 있는 일반적인 문제를 확인하고 해결하는 데 도움이 됩니다.

## <a name="prerequisites"></a>필수 구성 요소
빌드를 만들 때 빌드가 다음 필수 구성 요소를 충족하는지 확인합니다.
    
- 이미지 빌더 서비스는 WinRM 또는 SSH를 사용하여 빌드 VM과 통신합니다. 빌드의 일부로 이러한 설정을 사용하지 마세요.
- Image Builder는 빌드의 일부로 리소스를 만듭니다. Azure Policy가 AIB가 필요한 리소스를 만들거나 사용하지 못하도록 하는지 확인합니다.
  - IT_ 리소스 그룹 만들기
  - 방화벽 없이 스토리지 계정 만들기
- Azure Policy가 Azure Extensions와 같은 빌드 VM에 의도하지 않은 기능을 설치하지 않는지 확인합니다.
-   Image Builder에 이미지 읽기/쓰기 및 Azure Storage 연결에 대한 올바른 권한이 있는지 확인합니다. [CLI](./image-builder-permissions-cli.md) 또는 [PowerShell](./image-builder-permissions-powershell.md)에 대한 권한 문서를 검토하세요.
- 스크립트/인라인 명령이 오류(0이 아닌 종료 코드)와 함께 실패하면 Image Builder가 빌드에 실패합니다. 사용자 지정 스크립트가 오류(종료 코드 0) 없이 실행되는지 또는 사용자 입력을 필요로 하는지 테스트 및 확인해야 합니다. 자세한 내용은 다음 [문서](../windows/image-builder-virtual-desktop.md#tips-for-building-windows-images)를 참조하세요.

AIB 오류는 다음 두 영역에서 발생할 수 있습니다.
- 이미지 템플릿 전송
- 이미지 빌드

## <a name="troubleshoot-image-template-submission-errors"></a>이미지 템플릿 전송 오류 문제 해결

이미지 템플릿 전송 오류는 전송 시에만 반환됩니다. 이미지 템플릿 전송 오류에 대한 오류 로그가 없습니다. 전송 중에 오류가 발생한 경우 템플릿 상태를 확인하여, 특히 `ProvisioningState` 및 `ProvisioningErrorMessage`/`provisioningError`를 검토하여 오류를 반환할 수 있습니다.

```azurecli
az image builder show --name $imageTemplateName  --resource-group $imageResourceGroup
```

```azurepowershell-interactive
Get-AzImageBuilderTemplate -ImageTemplateName  <imageTemplateName> -ResourceGroupName <imageTemplateResourceGroup> | Select-Object ProvisioningState, ProvisioningErrorMessage
```
> [!NOTE]
> PowerShell의 경우 [Azure Image Builder PowerShell 모듈](../windows/image-builder-powershell.md#prerequisites)을 설치해야 합니다.

다음 섹션에는 일반적인 이미지 템플릿 전송 오류에 대한 문제 해결 지침을 포함되어 있습니다.

### <a name="updateupgrade-of-image-templates-is-currently-not-supported"></a>이미지 템플릿 업데이트/업그레이드가 현재 지원되지 않음

#### <a name="error"></a>오류

```text
'Conflict'. Details: Update/Upgrade of image templates is currently not supported
```

#### <a name="cause"></a>원인

템플릿이 이미 있습니다.

#### <a name="solution"></a>해결 방법

이미지 구성 템플릿을 전송했지만 전송이 실패하면 실패한 템플릿 아티팩트가 여전히 있습니다. 실패한 템플릿을 삭제합니다.

### <a name="the-resource-operation-completed-with-terminal-provisioning-state-failed"></a>리소스 작업이 '실패' 터미널 프로비저닝 상태로 완료되었음

#### <a name="error"></a>오류

```text
Microsoft.VirtualMachineImages/imageTemplates 'helloImageTemplateforSIG01' failed with message '{
  "status": "Failed",
  "error": {
    "code": "ResourceDeploymentFailure",
    "message": "The resource operation completed with terminal provisioning state 'Failed'.",
    "details": [
      {
        "code": "InternalOperationError",
        "message": "Internal error occurred."
```
#### <a name="cause"></a>원인

대부분의 경우 권한 누락으로 인해 리소스 배포 실패 오류가 발생합니다.

#### <a name="solution"></a>해결 방법

시나리오에 따라 Azure Image Builder에 다음 권한이 필요할 수 있습니다.
- 원본 이미지 또는 Azure 계산 갤러리 (이전의 공유 이미지 갤러리) 리소스 그룹
- 배포 이미지 또는 Azure Compute 갤러리 리소스
- 파일 사용자 지정자에서 액세스하는 스토리지 계정, 컨테이너 또는 Blob 

권한을 구성하는 방법에 대한 자세한 내용은 [Azure CLI를 사용하여 Azure Image Builder Service 권한 구성](image-builder-permissions-cli.md) 또는 [PowerShell을 사용하여 Azure Image Builder Service 권한 구성](image-builder-permissions-powershell.md)을 참조하세요.

### <a name="error-getting-managed-image"></a>관리되는 이미지를 가져오는 동안 오류 발생

#### <a name="error"></a>오류

```text
Build (Managed Image) step failed: Error getting Managed Image '/subscriptions/.../providers/Microsoft.Compute/images/mymanagedmg1': Error getting managed image (...): compute.
ImagesClient#Get: Failure responding to request: StatusCode=403 -- Original Error: autorest/azure: Service returned an error.
Status=403 Code="AuthorizationFailed" Message="The client '......' with object id '......' does not have authorization to perform action 'Microsoft.Compute/images/read' over scope 
```
#### <a name="cause"></a>원인

권한이 없습니다.

#### <a name="solution"></a>해결 방법

시나리오에 따라 Azure Image Builder에 다음 권한이 필요할 수 있습니다.
* 원본 이미지 또는 Azure Compute 갤러리 리소스 그룹
* 배포 이미지 또는 Azure Compute 갤러리 리소스
* 파일 사용자 지정자에서 액세스하는 스토리지 계정, 컨테이너 또는 Blob 

권한을 구성하는 방법에 대한 자세한 내용은 [Azure CLI를 사용하여 Azure Image Builder Service 권한 구성](image-builder-permissions-cli.md) 또는 [PowerShell을 사용하여 Azure Image Builder Service 권한 구성](image-builder-permissions-powershell.md)을 참조하세요.

### <a name="build--step-failed-for-image-version"></a>이미지 버전에 대한 빌드 단계가 실패함

#### <a name="error"></a>오류
```text
Build (Shared Image Version) step failed for Image Version '/subscriptions/.../providers/Microsoft.Compute/galleries/.../images/... /versions/0.23768.4001': Error getting Image Version '/subscriptions/.../resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/.../images/.../versions/0.23768.4001': Error getting image version '... :0.23768.4001': compute.GalleryImageVersionsClient#Get: Failure responding to request: StatusCode=404 -- Original Error: autorest/azure: Service returned an error. 
Status=404 Code="ResourceNotFound" Message="The Resource 'Microsoft.Compute/galleries/.../images/.../versions/0.23768.4001' under resource group '<rgName>' was not found."
```
#### <a name="cause"></a>원인

Azure Image Builder에서 원본 이미지를 찾을 수 없습니다.

#### <a name="solution"></a>해결 방법

원본 이미지가 올바르고 Azure Image Builder Service의 위치에 있는지 확인합니다.

### <a name="downloading-external-file-to-local-file"></a>외부 파일을 로컬 파일에 다운로드하는 중

#### <a name="error"></a>오류

```text
Downloading external file (<myFile>) to local file (xxxxx.0.customizer.fp) [attempt 1 of 10] failed: Error downloading '<myFile>' to 'xxxxx.0.customizer.fp'..
```

#### <a name="cause"></a>원인

파일 이름 또는 위치가 잘못되었거나 위치에 연결할 수 없습니다.

#### <a name="solution"></a>해결 방법

파일에 연결할 수 있는지 확인합니다. 이름 및 위치가 올바른지 확인합니다.

## <a name="troubleshoot-build-failures"></a>빌드 실패 문제 해결

이미지 빌드가 실패하는 경우 `lastrunstatus`에서 오류를 가져온 다음, customization.log에서 세부 정보를 검토할 수 있습니다.


```azurecli
az image builder show --name $imageTemplateName  --resource-group $imageResourceGroup
```

```azurepowershell-interactive
Get-AzImageBuilderTemplate -ImageTemplateName  <imageTemplateName> -ResourceGroupName <imageTemplateResourceGroup> | Select-Object LastRunStatus, LastRunStatusMessage
```

### <a name="customization-log"></a>사용자 지정 로그

이미지 빌드를 실행하면 로그가 만들어지고 스토리지 계정에 저장됩니다. Azure Image Builder는 이미지 템플릿 아티팩트를 만들 때 스토리지 계정을 임시 리소스 그룹에 만듭니다.

스토리지 계정 이름은 **IT_\<ImageResourceGroupName\> _\<TemplateName\>_ \<GUID\>** 패턴을 사용합니다.

예를 들어 *IT_aibmdi_helloImageTemplateLinux01* 입니다.

**스토리지 계정** > **Blob** > `packerlogs`울 차례로 선택하여 리소스 그룹의 스토리지 계정에서 customization.log를 볼 수 있습니다.  그런 다음 **디렉터리 > customization.log** 를 차례로 선택합니다.


### <a name="understanding-the-customization-log"></a>사용자 지정 로그 이해

로그는 자세한 정보입니다. 이미지 배포에 대 한 문제를 포함 하 여 Azure Compute 갤러리 복제와 같은 이미지 빌드를 다룹니다. 이러한 오류는 이미지 템플릿 상태의 오류 메시지에 표시됩니다.

customization.log에 포함되는 단계는 다음과 같습니다.

1. ARM 템플릿을 사용하여 빌드 VM 및 종속성을 IT_ staging 리소스 그룹 단계에 배포합니다. 이 단계에는 Azure Image Builder 리소스 공급자에 대한 여러 POST가 포함됩니다.
    ```text
    Azure request method="POST" request="https://management.azure.com/subscriptions/<subID>/resourceGroups/IT_aibImageRG200_window2019VnetTemplate01_dec33089-1cc3-cccc-cccc-ccccccc/providers/Microsoft.Storage/storageAccounts
    ..
    PACKER OUT ==> azure-arm: Deploying deployment template ...
    ..
    ```

2. 배포 단계의 상태입니다. 이 단계에는 각 리소스 배포의 상태가 포함됩니다.
    ```text
    PACKER ERR 2020/04/30 23:28:50 packer: 2020/04/30 23:28:50 Azure request method="GET" request="https://management.azure.com/subscriptions/<subID>/resourcegroups/IT_aibImageRG200_window2019VnetTemplate01_dec33089-1cc3-4505-ae28-6661e43fac48/providers/Microsoft.Resources/deployments/pkrdp51lc0339jg/operationStatuses/08586133176207523519?[REDACTED]" body=""
    ```

3. VM 빌드 단계에 연결합니다.

    Windows의 경우 Azure Image Builder Service는 WinRM을 사용하여 연결합니다.
    ```text
    PACKER ERR 2020/04/30 23:30:50 packer: 2020/04/30 23:30:50 Waiting for WinRM, up to timeout: 10m0s
    ..
    PACKER OUT     azure-arm: WinRM connected.
    ```

    Linux의 경우 Azure Image Builder Service는 SSH를 사용하여 연결합니다.
    ```text
    PACKER OUT ==> azure-arm: Waiting for SSH to become available...
    PACKER ERR 2019/12/10 17:20:51 packer: 2020/04/10 17:20:51 [INFO] Waiting for SSH, up to timeout: 20m0s
    PACKER OUT ==> azure-arm: Connected to SSH!
    ```

4. 사용자 지정 단계를 실행합니다. 사용자 지정이 실행되면 customization.log를 검토하여 해당 사용자 지정을 식별할 수 있습니다. *(telemetry)* 를 검색합니다.
    ```text
    (telemetry) Starting provisioner windows-update
    (telemetry) ending windows-update
    (telemetry) Starting provisioner powershell
    (telemetry) ending powershell
    (telemetry) Starting provisioner file
    (telemetry) ending file
    (telemetry) Starting provisioner windows-restart
    (telemetry) ending windows-restart
    
    (telemetry) Finalizing. - This means the build hasfinished
    ```
5. 프로비저닝 해제 단계입니다. Azure Image Builder는 숨겨진 사용자 지정자를 추가합니다. 이 프로비저닝 해제 단계는 프로비저닝 해제를 위해 VM을 준비하는 작업을 담당합니다. Windows의 경우 Sysprep(c:\DeprovisioningScript.ps1 사용)을 실행하거나 Linux의 경우 waagent deprovision(/tmp/DeprovisioningScript.sh 사용)을 실행합니다. 

    예:
    ```text
    PACKER ERR 2020/03/04 23:05:04 [INFO] (telemetry) Starting provisioner powershell
    PACKER ERR 2020/03/04 23:05:04 packer: 2020/03/04 23:05:04 Found command: if( TEST-PATH c:\DeprovisioningScript.ps1 ){cat c:\DeprovisioningScript.ps1} else {echo "Deprovisioning script [c:\DeprovisioningScript.ps1] could not be found. Image build may fail or the VM created from the Image may not boot. Please make sure the deprovisioning script is not accidentally deleted by a Customizer in the Template."}
    ```

6. 정리 단계입니다. 빌드가 완료되면 Azure Image Builder 리소스가 삭제됩니다.
    ```text
    PACKER ERR 2020/02/04 02:04:23 packer: 2020/02/04 02:04:23 Azure request method="DELETE" request="https://management.azure.com/subscriptions/<subId>/resourceGroups/IT_aibDevOpsImg_t_vvvvvvv_yyyyyy-de5f-4f7c-92f2-xxxxxxxx/providers/Microsoft.Network/networkInterfaces/pkrnijamvpo08eo?[REDACTED]" body=""
    ```
## <a name="tips-for-troubleshooting-scriptinline-customization"></a>스크립트/인라인 사용자 지정 문제 해결을 위한 팁
- Image Builder에 제공하기 전에 코드를 테스트합니다.
- Azure Policy 및 Firewall에서 원격 리소스에 대한 연결을 허용하는지 확인합니다.
- 주석을 콘솔에 출력합니다(예: `Write-Host` 또는 `echo` 사용). 그러면 customization.log를 검색할 수 있습니다.

## <a name="troubleshoot-common-build-errors"></a>일반적인 빌드 오류 문제 해결

### <a name="packer-build-command-failure"></a>packer 빌드 명령 실패

#### <a name="error"></a>오류

```text
  "provisioningState": "Succeeded",
  "lastRunStatus": {
   "startTime": "2020-04-30T23:24:06.756985789Z",
   "endTime": "2020-04-30T23:39:14.268729811Z",
   "runState": "Failed",
   "message": "Failed while waiting for packerizer: Microservice has failed: Failed while processing request: Error when executing packerizer: Packer build command has failed: exit status 1. During the image build, a failure has occurred, please review the build log to identify which build/customization step failed. For more troubleshooting steps go to https://aka.ms/azvmimagebuilderts. Image Build log location: https://xxxxxxxxxx.blob.core.windows.net/packerlogs/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/customization.log. OperationId: xxxxxx-5a8c-4379-xxxx-8d85493bc791. Use this operationId to search packer logs."
```

#### <a name="cause"></a>원인

사용자 지정 오류입니다.

#### <a name="solution"></a>해결 방법

로그를 검토하여 사용자 지정자 오류를 찾습니다. *(telemetry)* 를 검색합니다. 

예:
```text
(telemetry) Starting provisioner windows-update
(telemetry) ending windows-update
(telemetry) Starting provisioner powershell
(telemetry) ending powershell
(telemetry) Starting provisioner file
(telemetry) ending file
(telemetry) Starting provisioner windows-restart
(telemetry) ending windows-restart

(telemetry) Finalizing. - This means the build has finished
```

### <a name="timeout-exceeded"></a>시간 제한 초과

#### <a name="error"></a>오류

```text
Deployment failed. Correlation ID: xxxxx-xxxx-xxxx-xxxx-xxxxxxxxx. Failed in building/customizing image: Failed while waiting for packerizer: Timeout waiting for microservice to complete: 'context deadline exceeded'
```

#### <a name="cause"></a>원인

빌드에서 빌드 시간 제한을 초과했습니다. 이 오류는 'lastrunstatus'에 표시됩니다.

#### <a name="solution"></a>해결 방법

1. customization.log를 검토합니다. 실행할 마지막 사용자 지정자를 식별합니다. 로그 아래쪽부터 `(telemetry)`를 식별합니다. 

2. 스크립트 사용자 지정을 확인합니다. 사용자 지정은 `quiet` 옵션과 같은 명령에 대한 사용자 상호 작용을 억제하지 않을 수 있습니다. 예를 들어 `apt-get install -y`는 스크립트 실행에서 사용자 상호 작용을 기다리도록 합니다.

3. `File` 사용자 지정자를 사용하여 20MB를 초과하는 아티팩트를 다운로드하는 경우 해결 방법 섹션을 참조하세요.

4. 스크립트 대기가 발생할 수 있는 스크립트의 오류 및 종속성을 검토합니다.

5. 사용자 지정에 더 많은 시간이 필요한 경우 [buildTimeoutInMinutes](image-builder-json.md)를 늘립니다. 기본값은 4시간입니다.

6. 기가바이트 단위의 파일을 다운로드하는 것과 같이 리소스를 많이 사용하는 작업이 있는 경우 기본 빌드 VM 크기를 고려합니다. 이 서비스는 Standard_D1_v2 VM을 사용합니다. VM에는 1개의 vCPU 및 3.5GB의 메모리가 있습니다. 50GB를 다운로드하는 경우 이로 인해 VM 리소스가 고갈되고 Azure Image Builder Service와 빌드 VM 간에 통신 오류가 발생할 수 있습니다. [VM_Size](image-builder-json.md#vmprofile)를 설정하여 더 큰 메모리 VM을 사용하여 빌드를 다시 시도합니다.

### <a name="long-file-download-time"></a>긴 파일 다운로드 시간

#### <a name="error"></a>오류
```text
[086cf9c4-0457-4e8f-bfd4-908cfe3fe43c] PACKER OUT 
myBigFile.zip 826 B / 826000 B  1.00%
[086cf9c4-0457-4e8f-bfd4-908cfe3fe43c] PACKER OUT 
myBigFile.zip 1652 B / 826000 B  2.00%
[086cf9c4-0457-4e8f-bfd4-908cfe3fe43c] PACKER OUT 
..
hours later...
..
myBigFile.zip 826000 B / 826000 B  100.00%
[086cf9c4-0457-4e8f-bfd4-908cfe3fe43c] PACKER OUT 
```
#### <a name="cause"></a>원인 

파일 사용자 지정자에서 큰 파일을 다운로드하고 있습니다.

#### <a name="solution"></a>해결 방법

파일 사용자 지정자는 20MB 미만의 작은 파일을 다운로드하는 데만 적합합니다. 큰 파일을 다운로드하려면 스크립트 또는 인라인 명령을 사용합니다. 예를 들어 Linux에서 `wget` 또는 `curl`를 사용할 수 있습니다. Windows에서는 `Invoke-WebRequest`를 사용할 수 있습니다.

### <a name="error-waiting-on-azure-compute-gallery"></a>Azure 계산 갤러리에서 대기 하는 동안 오류 발생

#### <a name="error"></a>오류

```text
Deployment failed. Correlation ID: XXXXXX-XXXX-XXXXXX-XXXX-XXXXXX. Failed in distributing 1 images out of total 1: {[Error 0] [Distribute 0] Error publishing MDI to Azure Compute Gallery:/subscriptions/<subId>/resourceGroups/xxxxxx/providers/Microsoft.Compute/galleries/xxxxx/images/xxxxxx, Location:eastus. Error: Error returned from SIG client while publishing MDI to Azure Compute Gallery for dstImageLocation: eastus, dstSubscription: <subId>, dstResourceGroupName: XXXXXX, dstGalleryName: XXXXXX, dstGalleryImageName: XXXXXX. Error: Error waiting on Azure Compute Gallery future for resource group: XXXXXX, gallery name: XXXXXX, gallery image name: XXXXXX.Error: Future#WaitForCompletion: context has been cancelled: StatusCode=200 -- Original Error: context deadline exceeded}
```

#### <a name="cause"></a>원인

이미지를 추가 하 고 Azure Compute 갤러리에 복제할 때까지 기다리는 동안 이미지 작성기 시간이 초과 되었습니다. 이미지가 SIG에 삽입되는 경우 이미지 빌드에 성공했다고 가정할 수 있습니다. 그러나 이미지 작성기가 Azure 계산 갤러리에서 복제를 완료 하기를 기다리고 있으므로 전체 프로세스가 실패 했습니다. 빌드가 실패하더라도 복제는 계속됩니다. 배포 *runOutput* 을 확인하여 이미지 버전의 속성을 가져올 수 있습니다.

```bash
$runOutputName=<distributionRunOutput>
az resource show \
    --ids "/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.VirtualMachineImages/imageTemplates/$imageTemplateName/runOutputs/$runOutputName"  \
    --api-version=2020-02-14
```

#### <a name="solution"></a>해결 방법

**buildTimeoutInMinutes** 를 늘립니다.
 
### <a name="low-windows-resource-information-events"></a>낮은 Windows 리소스 정보 이벤트

#### <a name="error"></a>오류

```text
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for operation to complete (system performance: 1% cpu; 37% memory)...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for operation to complete (system performance: 51% cpu; 35% memory)...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for operation to complete (system performance: 21% cpu; 37% memory)...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for operation to complete (system performance: 21% cpu; 36% memory)...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for operation to complete (system performance: 90% cpu; 32% memory)...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT     azure-arm: Waiting for the Windows Modules Installer to exit...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer: 2020/04/30 23:38:58 [INFO] command 'PowerShell -ExecutionPolicy Bypass -OutputFormat Text -File C:/Windows/Temp/packer-windows-update-elevated.ps1' exited with code: 101
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT ==> azure-arm: Restarting the machine...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer: 2020/04/30 23:38:58 [INFO] RPC endpoint: Communicator ended with: 101
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] 1672 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] RPC client: Communicator ended with: 101
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] RPC endpoint: Communicator ended with: 101
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT ==> azure-arm: Waiting for machine to become available...
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] 1672 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] RPC client: Communicator ended with: 101
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer: 2020/04/30 23:38:58 [INFO] starting remote command: shutdown.exe -f -r -t 0 -c "packer restart"
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer: 2020/04/30 23:38:58 [INFO] command 'shutdown.exe -f -r -t 0 -c "packer restart"' exited with code: 0
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer: 2020/04/30 23:38:58 [INFO] RPC endpoint: Communicator ended with: 0
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT ==> azure-arm: A system shutdown is in progress.(1115)
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] RPC client: Communicator ended with: 0
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 [INFO] RPC endpoint: Communicator ended with: 0
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] 0 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:58 packer-provisioner-windows-update: 2020/04/30 23:38:58 [INFO] RPC client: Communicator ended with: 0
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer: 2020/04/30 23:38:59 [INFO] starting remote command: shutdown.exe -f -r -t 60 -c "packer restart test"
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer: 2020/04/30 23:38:59 [INFO] command 'shutdown.exe -f -r -t 60 -c "packer restart test"' exited with code: 1115
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer: 2020/04/30 23:38:59 [INFO] RPC endpoint: Communicator ended with: 1115
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 [INFO] 0 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 [INFO] 40 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 [INFO] RPC client: Communicator ended with: 1115
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 [INFO] RPC endpoint: Communicator ended with: 1115
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer-provisioner-windows-update: 2020/04/30 23:38:59 [INFO] 40 bytes written for 'stderr'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer-provisioner-windows-update: 2020/04/30 23:38:59 [INFO] 0 bytes written for 'stdout'
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer-provisioner-windows-update: 2020/04/30 23:38:59 [INFO] RPC client: Communicator ended with: 1115
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER ERR 2020/04/30 23:38:59 packer-provisioner-windows-update: 2020/04/30 23:38:59 Retryable error: Machine not yet available (exit status 1115)
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT Build 'azure-arm' errored: unexpected EOF
[45f485cf-5a8c-4379-9937-8d85493bc791] PACKER OUT 
```
#### <a name="cause"></a>원인

리소스가 고갈되었습니다. 이 문제는 일반적으로 D1_V2 기본 빌드 VM 크기를 사용하여 실행되는 Windows 업데이트에 표시됩니다.

#### <a name="solution"></a>해결 방법

빌드 VM 크기를 늘립니다.

### <a name="builds-finished-but-no-artifacts-were-created"></a>빌드가 완료되었지만 만들어진 아티팩트가 없음

#### <a name="error"></a>오류

```text
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT Build 'azure-arm' errored: Future#WaitForCompletion: context has been cancelled: StatusCode=200 -- Original Error: context deadline exceeded
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR ==> Some builds didn't complete successfully and had errors:
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT 
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR 2020/04/30 22:29:23 machine readable: azure-arm,error []string{"Future#WaitForCompletion: context has been cancelled: StatusCode=200 -- Original Error: context deadline exceeded"}
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT ==> Some builds didn't complete successfully and had errors:
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR ==> Builds finished but no artifacts were created.
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT --> azure-arm: Future#WaitForCompletion: context has been cancelled: StatusCode=200 -- Original Error: context deadline exceeded
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR 2020/04/30 22:29:23 Cancelling builder after context cancellation context canceled
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT 
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR 2020/04/30 22:29:23 [INFO] (telemetry) Finalizing.
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT ==> Builds finished but no artifacts were created.
[a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER ERR 2020/04/30 22:29:24 waiting for all plugin processes to complete...
Done exporting Packer logs to Azure for Packer prefix: [a170b40d-2d77-4ac3-8719-72cdc35cf889] PACKER OUT
```
#### <a name="cause"></a>원인

필요한 Azure 리소스가 만들어질 때까지 대기하여 시간 제한이 초과되었습니다.

#### <a name="solution"></a>해결 방법

빌드를 다시 실행하여 다시 시도합니다.

### <a name="resource-not-found"></a>리소스를 찾을 수 없음

#### <a name="error"></a>오류

```text
  "provisioningState": "Succeeded",
  "lastRunStatus": {
   "startTime": "2020-05-01T00:13:52.599326198Z",
   "endTime": "2020-05-01T00:15:13.62366898Z",
   "runState": "Failed",
   "message": "network.InterfacesClient#UpdateTags: Failure responding to request: StatusCode=404 -- Original Error: autorest/azure: Service returned an error. Status=404 Code=\"ResourceNotFound\" Message=\"The Resource 'Microsoft.Network/networkInterfaces/aibpls7lz2e.nic.4609d697-be0a-4cb0-86af-49b6fe877fe1' under resource group 'IT_aibImageRG200_window2019VnetTemplate01_9988723b-af56-413a-9006-84130af0e9df' was not found.\""
  },
```
#### <a name="cause"></a>원인

권한이 없습니다.

#### <a name="solution"></a>해결 방법

필요한 모든 권한이 Azure Image Builder에 있는지 다시 확인합니다. 

권한을 구성하는 방법에 대한 자세한 내용은 [Azure CLI를 사용하여 Azure Image Builder Service 권한 구성](image-builder-permissions-cli.md) 또는 [PowerShell을 사용하여 Azure Image Builder Service 권한 구성](image-builder-permissions-powershell.md)을 참조하세요.

### <a name="sysprep-timing"></a>Sysprep 타이밍

#### <a name="error"></a>오류

```text
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: Write-Output '>>> Waiting for GA Service (RdAgent) to start ...'
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: while ((Get-Service RdAgent) -and ((Get-Service RdAgent).Status -ne 'Running')) { Start-Sleep -s 5 }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: Write-Output '>>> Waiting for GA Service (WindowsAzureTelemetryService) to start ...'
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: while ((Get-Service WindowsAzureTelemetryService) -and ((Get-Service WindowsAzureTelemetryService).Status -ne 'Running')) { Start-Sleep -s 5 }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: Write-Output '>>> Waiting for GA Service (WindowsAzureGuestAgent) to start ...'
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: while ((Get-Service WindowsAzureGuestAgent) -and ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running')) { Start-Sleep -s 5 }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: Write-Output '>>> Sysprepping VM ...'
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: if( Test-Path $Env:SystemRoot\system32\Sysprep\unattend.xml ) {
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm:   Remove-Item $Env:SystemRoot\system32\Sysprep\unattend.xml -Force
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: & $Env:SystemRoot\System32\Sysprep\Sysprep.exe /oobe /generalize /quiet /quit
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: while($true) {
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm:   $imageState = (Get-ItemProperty HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Setup\State).ImageState
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm:   Write-Output $imageState
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm:   if ($imageState -eq 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { break }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm:   Start-Sleep -s 5
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: }
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: Write-Output '>>> Sysprep complete ...'
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: >>> Waiting for GA Service (RdAgent) to start ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: >>> Waiting for GA Service (WindowsAzureTelemetryService) to start ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: >>> Waiting for GA Service (WindowsAzureGuestAgent) to start ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: >>> Sysprepping VM ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_COMPLETE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: Get-Service : Cannot find any service with service name 'WindowsAzureGuestAgent'.
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: At C:\DeprovisioningScript.ps1:6 char:9
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: + while ((Get-Service WindowsAzureGuestAgent) -and ((Get-Service Window ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: +         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm:     + CategoryInfo          : ObjectNotFound: (WindowsAzureGuestAgent:String) [Get-Service], ServiceCommandException
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm:     + FullyQualifiedErrorId : NoServiceFoundForGivenName,Microsoft.PowerShell.Commands.GetServiceCommand
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm:
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT     azure-arm: IMAGE_STATE_UNDEPLOYABLE
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER ERR 2020/05/05 22:26:17 Cancelling builder after context cancellation context canceled
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT Cancelling build after receiving terminated
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER ERR 2020/05/05 22:26:17 packer: 2020/05/05 22:26:17 Cancelling provisioning due to context cancellation: context canceled
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: 
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER ERR 2020/05/05 22:26:17 packer: 2020/05/05 22:26:17 Cancelling hook after context cancellation context canceled
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER OUT ==> azure-arm: The resource group was not created by Packer, deleting individual resources ...
[922bdf36-b53c-4e78-9cd8-6b70b9674685] PACKER ERR ==> azure-arm: The resource group was not created by Packer, deleting individual resources ...
```
#### <a name="cause"></a>원인

D1_V2 VM 크기로 인해 타이밍 문제가 발생할 수 있습니다. 사용자 지정이 제한되고 3초 미만 내에 실행되는 경우 Azure Image Builder에서 sysprep 명령을 실행하여 프로비저닝을 해제합니다. Azure Image Builder에서 프로비저닝을 해제할 때 sysprep 명령은 완전히 설치되지 않아 타이밍 문제가 발생할 수 있는 *WindowsAzureGuestAgent* 를 확인합니다. 

#### <a name="solution"></a>해결 방법

VM 크기를 늘립니다. 또는 60초의 PowerShell 절전 모드 사용자 지정을 추가하여 타이밍 문제를 방지할 수 있습니다.

### <a name="cancelling-builder-after-context-cancellation-context-canceled"></a>컨텍스트 취소 컨텍스트가 취소된 후 작성기를 취소하는 중

#### <a name="error"></a>오류
```text
PACKER ERR 2020/03/26 22:11:23 Cancelling builder after context cancellation context canceled
PACKER OUT Cancelling build after receiving terminated
PACKER ERR 2020/03/26 22:11:23 packer-builder-azure-arm plugin: Cancelling hook after context cancellation context canceled
..
PACKER ERR 2020/03/26 22:11:23 packer-builder-azure-arm plugin: Cancelling provisioning due to context cancellation: context canceled
PACKER ERR 2020/03/26 22:11:25 packer-builder-azure-arm plugin: [ERROR] Remote command exited without exit status or exit signal.
PACKER ERR 2020/03/26 22:11:25 packer-builder-azure-arm plugin: [INFO] RPC endpoint: Communicator ended with: 2300218
PACKER ERR 2020/03/26 22:11:25 [INFO] 148974 bytes written for 'stdout'
PACKER ERR 2020/03/26 22:11:25 [INFO] 0 bytes written for 'stderr'
PACKER ERR 2020/03/26 22:11:25 [INFO] RPC client: Communicator ended with: 2300218
PACKER ERR 2020/03/26 22:11:25 [INFO] RPC endpoint: Communicator ended with: 2300218
```
#### <a name="cause"></a>원인
Image Builder Service는 22 포트(Linux) 또는 5986 포트(Windows)를 사용하여 빌드 VM에 연결합니다. 이는 이미지 빌드 중에 서비스의 연결이 빌드 VM에서 끊길 때 발생합니다. 연결이 끊기는 이유는 다양할 수 있지만 스크립트에서 방화벽을 사용하도록 설정하거나 구성하면 위의 포트를 차단할 수 있습니다.

#### <a name="solution"></a>해결 방법
스크립트를 검토하여 방화벽 변경/사용 또는 SSH 또는 WinRM에 대한 변경을 확인하고, 모든 변경이 위의 포트에서 서비스와 빌드 VM 간의 지속적인 연결을 허용하는지 확인합니다. Image Builder 네트워킹에 대한 자세한 내용은 [요구 사항](./image-builder-networking.md)을 검토하세요.

### <a name="jwt-errors-in-log-early-in-the-build"></a>빌드 초기에 로그의 JWT 오류

#### <a name="error"></a>오류
빌드 프로세스 초기에 빌드가 실패하고 로그에 JWT 오류가 표시됩니다.

```text
PACKER OUT Error: Failed to prepare build: "azure-arm"
PACKER ERR 
PACKER OUT 
PACKER ERR * client_jwt will expire within 5 minutes, please use a JWT that is valid for at least 5 minutes
PACKER OUT 1 error(s) occurred:
```

#### <a name="cause"></a>원인
템플릿의 `buildTimeoutInMinutes` 값은 1분에서 5분 사이로 설정됩니다.

#### <a name="solution"></a>솔루션
[Azure Image Builder 템플릿 만들기](./image-builder-json.md)에 설명된 대로 기본값을 사용하려면 시간 제한을 0으로 설정하고 기본값을 재정의하려면 5분 이상으로 설정해야 합니다.  템플릿의 시간 제한을 0으로 변경하여 기본값을 사용하거나 최소 6분으로 변경합니다.

## <a name="devops-task"></a>DevOps 작업 

### <a name="troubleshooting-the-task"></a>작업 문제 해결
사용자 지정 중에 오류가 발생하는 경우에만 작업이 실패합니다. 이 경우 작업에서 오류를 보고하고 로그와 함께 준비 리소스 그룹에서 나가므로 문제를 식별할 수 있습니다. 

로그를 찾으려면 템플릿 이름을 알고 있어야 합니다. [파이프라인 > 실패한 빌드 > AIB DevOps 작업으로 드릴다운]으로 차례로 이동합니다. 그러면 로그와 템플릿 이름이 표시됩니다.
```text
start reading task parameters...
found build at:  /home/vsts/work/r1/a/_ImageBuilding/webapp
end reading parameters
getting storage account details for aibstordot1556933914
created archive /home/vsts/work/_temp/temp_web_package_21475337782320203.zip
Source for image:  { type: 'SharedImageVersion',
  imageVersionId: '/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.Compute/galleries/<galleryName>/images/<imageDefName>/versions/<imgVersionNumber>' }
template name:  t_1556938436xxx
``` 

포털로 이동하여 리소스 그룹에서 템플릿 이름을 검색한 다음, IT_*를 사용하여 리소스 그룹을 찾습니다.
[스토리지 계정 > Blob > 컨테이너 > 로그]로 차례로 이동합니다.

### <a name="troubleshooting-successful-builds"></a>성공한 빌드 문제 해결
성공한 빌드를 조사하고 로그를 검토해야 하는 경우가 있을 수 있습니다. 앞에서 설명한 대로 이미지 빌드가 성공하면 정리 단계의 일환으로 로그가 포함된 준비 리소스 그룹이 삭제됩니다. 그러나 수행할 수 있는 작업은 인라인 명령 뒤에 절전 모드를 도입한 다음, 빌드가 일시 중지될 때 로그를 가져오는 것입니다. 이렇게 하려면 다음 단계를 수행합니다.
 
1. 인라인 명령을 업데이트하고, Write-Host / Echo "Sleep"을 추가합니다. 그러면 로그에서 검색할 수 있습니다.
2. 10분 이상의 절전 모드를 추가하고, [Start-Sleep](/powershell/module/microsoft.powershell.utility/start-sleep) 또는 `Sleep` Linux 명령을 사용할 수 있습니다.
3. 위의 방법을 사용하여 로그 위치를 확인한 다음, 절전 모드로 전환될 때까지 계속 다운로드/확인합니다.


### <a name="operation-was-canceled"></a>작업이 취소되었습니다.

#### <a name="error"></a>오류

```text
2020-05-05T18:28:24.9280196Z ##[section]Starting: Azure VM Image Builder Task
2020-05-05T18:28:24.9609966Z ==============================================================================
2020-05-05T18:28:24.9610739Z Task         : Azure VM Image Builder Test
2020-05-05T18:28:24.9611277Z Description  : Build images using Azure Image Builder resource provider.
2020-05-05T18:28:24.9611608Z Version      : 1.0.18
2020-05-05T18:28:24.9612003Z Author       : Microsoft Corporation
2020-05-05T18:28:24.9612718Z Help         : For documentation, and end to end example, please visit: http://aka.ms/azvmimagebuilderdevops
2020-05-05T18:28:24.9613390Z ==============================================================================
2020-05-05T18:28:26.0651512Z start reading task parameters...
2020-05-05T18:28:26.0673377Z found build at:  d:\a\r1\a\_AppsAndImageBuilder\webApp
2020-05-05T18:28:26.0708785Z end reading parameters
2020-05-05T18:28:26.0745447Z getting storage account details for aibstagstor1565047758
2020-05-05T18:28:29.8812270Z created archive d:\a\_temp\temp_web_package_09737279437949953.zip
2020-05-05T18:28:33.1568013Z Source for image:  { type: 'PlatformImage',
2020-05-05T18:28:33.1584131Z   publisher: 'MicrosoftWindowsServer',
2020-05-05T18:28:33.1585965Z   offer: 'WindowsServer',
2020-05-05T18:28:33.1592768Z   sku: '2016-Datacenter',
2020-05-05T18:28:33.1594191Z   version: '14393.3630.2004101604' }
2020-05-05T18:28:33.1595387Z template name:  t_1588703313152
2020-05-05T18:28:33.1597453Z starting put template...
2020-05-05T18:28:52.9278603Z put template:  Succeeded
2020-05-05T18:28:52.9281282Z starting run template...
2020-05-05T19:33:14.3923479Z ##[error]The operation was canceled.
2020-05-05T19:33:14.3939721Z ##[section]Finishing: Azure VM Image Builder Task
```
#### <a name="cause"></a>원인

사용자가 빌드를 취소하지 않은 경우 Azure DevOps 사용자 에이전트에서 빌드를 취소한 것입니다. Azure DevOps 기능으로 인해 1시간 제한이 발생했을 가능성이 높습니다. 프라이빗 프로젝트 및 에이전트를 사용하는 경우 60분의 빌드 시간이 제공됩니다. 빌드에서 시간 제한을 초과하면 DevOps는 실행 중인 작업을 취소합니다.

Azure DevOps 기능 및 제한 사항에 대한 자세한 내용은 [Microsoft 호스팅 에이전트](/azure/devops/pipelines/agents/hosted#capabilities-and-limitations)를 참조하세요.
 
#### <a name="solution"></a>해결 방법

사용자 고유의 DevOps 에이전트를 호스팅하거나 빌드 시간을 줄일 수 있습니다. 예를 들어 Azure Compute 갤러리에 배포하는 경우 한 지역에 복제합니다. 비동기적으로 복제하려는 경우입니다. 

### <a name="slow-windows-logon-please-wait-for-the-windows-modules-installer"></a>느린 Windows 로그온: 'Windows 모듈 설치 관리자를 기다려 주세요.'

#### <a name="error"></a>오류
Image Builder를 사용하여 Windows 10 이미지가 만들어지면 이미지에서 VM을 만들고, RDP를 수행하고, 첫 번째 로그온에서 몇 분 정도 기다려야 다음과 같은 메시지가 포함된 블루 스크린이 표시됩니다.
```text
Please wait for the Windows Modules Installer
```

#### <a name="solution"></a>해결 방법
먼저 이미지 빌드에서 Windows 다시 시작 사용자 지정자를 마지막 사용자 지정으로 추가하여 필요한 미해결 다시 부팅이 없는지 확인한 다음, 모든 소프트웨어 설치가 완료되었는지 확인합니다. 마지막으로 [/mode:vm](/windows-hardware/manufacture/desktop/sysprep-command-line-options) 옵션을 AIB에서 사용하는 기본 sysprep에 추가합니다. 아래의 'AIB 이미지에서 만든 VM이 성공적으로 만들어지지 않음' > '명령 재정의'를 차례로 참조하세요.  

 
## <a name="vms-created-from-aib-images-do-not-create-successfully"></a>AIB 이미지에서 만든 VM이 성공적으로 만들어지지 않음

기본적으로 Azure Image Builder는 이미지를 *일반화* 하기 위해 각 이미지 사용자 지정 단계가 끝날 때 *프로비저닝 해제* 코드를 실행합니다. 일반화는 이미지가 여러 VM을 만드는 데 다시 사용하도록 설정되고 호스트 이름, 사용자 이름 등과 같은 VM 설정을 전달할 수 있는 프로세스입니다. Windows의 경우 Azure Image Builder에서 *Sysprep* 을 실행하고, Linux Azure Image Builder에서는 `waagent -deprovision`을 실행합니다. 

Windows의 경우 Azure Image Builder는 일반 Sysprep 명령을 사용합니다. 그러나 이는 성공적인 모든 Windows 일반화에 적합하지 않을 수 있습니다. Azure Image Builder를 사용하면 Sysprep 명령을 사용자 지정할 수 있습니다. 참고: Azure Image Builder는 이미지 자동화 도구입니다. Sysprep 명령을 성공적으로 실행해야 합니다. 그러나 이미지를 다시 사용할 수 있도록 하려면 다른 Sysprep 명령이 필요할 수 있습니다. Linux의 경우 Azure Image Builder는 일반 `waagent -deprovision+user` 명령을 사용합니다. 자세한 내용은 [Microsoft Azure Linux 에이전트 설명서](https://github.com/Azure/WALinuxAgent#command-line-options)를 참조하세요.

기존 사용자 지정을 마이그레이션하고 다른 Sysprep/waagent 명령을 사용하는 경우 Image Builder 일반 명령을 시도할 수 있습니다. VM을 만들 때 오류가 발생하면 이전 Sysprep/waagent 명령을 사용합니다.

> [!NOTE]
> AIB에서 Windows 사용자 지정 이미지를 성공적으로 만들고, 이 이미지에서 VM을 만든 다음, VM이 성공적으로 만들어지지 않는 경우(예: VM 만들기 명령이 완료되지 않음/시간 제한 초과) Windows Server Sysprep 설명서를 검토해야 합니다. 또는 올바른 Sysprep 명령에 대한 문제를 해결하고 조언할 수 있는 Windows Server Sysprep 고객 서비스 지원 팀에 지원 요청을 제기할 수 있습니다.

### <a name="command-locations-and-filenames"></a>명령 위치 및 파일 이름

Windows:

```
c:\DeprovisioningScript.ps1
```

Linux:
```bash
/tmp/DeprovisioningScript.sh
```

### <a name="sysprep-command-windows"></a>Sysprep 명령: Windows

```PowerShell
Write-Output '>>> Waiting for GA Service (RdAgent) to start ...'
while ((Get-Service RdAgent).Status -ne 'Running') { Start-Sleep -s 5 }
Write-Output '>>> Waiting for GA Service (WindowsAzureTelemetryService) to start ...'
while ((Get-Service WindowsAzureTelemetryService) -and ((Get-Service WindowsAzureTelemetryService).Status -ne 'Running')) { Start-Sleep -s 5 }
Write-Output '>>> Waiting for GA Service (WindowsAzureGuestAgent) to start ...'
while ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running') { Start-Sleep -s 5 }
Write-Output '>>> Sysprepping VM ...'
if( Test-Path $Env:SystemRoot\system32\Sysprep\unattend.xml ) {
  Remove-Item $Env:SystemRoot\system32\Sysprep\unattend.xml -Force
}
& $Env:SystemRoot\System32\Sysprep\Sysprep.exe /oobe /generalize /quiet /quit
while($true) {
  $imageState = (Get-ItemProperty HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Setup\State).ImageState
  Write-Output $imageState
  if ($imageState -eq 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { break }
  Start-Sleep -s 5
}
Write-Output '>>> Sysprep complete ...'
```

### <a name="deprovision-command-linux"></a>프로비저닝 해제 명령: Linux

```bash
/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync
```

### <a name="overriding-the-commands"></a>명령 재정의

명령을 재정의하려면 PowerShell 또는 셸 스크립트 프로비저닝 프로그램에서 정확한 파일 이름을 사용하여 명령 파일을 만들고, 이전에 나열된 디렉터리에 배치합니다. Azure Image Builder는 이러한 명령을 읽고, 출력은 *customization.log* 에 기록됩니다.

## <a name="getting-support"></a>지원 받기
지침을 참조했지만 여전히 문제를 해결할 수 없는 경우 지원 사례를 열 수 있습니다. 이 경우 적절한 제품 및 지원 항목을 선택하세요. 이렇게 하면 Azure VM Image Builder 지원 팀이 참여하게 됩니다.

사례 제품 선택:
```bash
Product Family: Azure
Product: Virtual Machine Running (Window\Linux)
Support Topic: Azure Features
Support Subtopic: Azure Image Builder
```

## <a name="next-steps"></a>다음 단계

자세한 내용은 [Azure Image Builder 개요](../image-builder-overview.md)를 참조하세요.