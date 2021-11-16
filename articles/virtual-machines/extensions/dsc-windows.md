---
title: Azure Desired State Configuration 확장 처리기
description: DSC 확장을 사용하여 PowerShell DSC 구성을 Azure VM에 업로드하고 적용합니다.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: bobbytreed
ms.author: robreed
ms.collection: windows
ms.date: 03/26/2018
ms.openlocfilehash: 9bd77230e742f4874fde81fa0ab1e4c1a83ba6d5
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132488794"
---
# <a name="powershell-dsc-extension"></a>Powershell DSC 확장

## <a name="overview"></a>개요

Microsoft는 Windows용 Powershell DSC 확장을 게시하고 지원합니다. 확장은 PowerShell DSC 구성을 Azure VM에 업로드하고 적용합니다. DSC 확장은 PowerShell DSC를 호출하여 VM에서 받은 DSC 구성을 적용합니다. 이 문서에서는 Windows용 DSC 가상 머신 확장에 지원되는 플랫폼, 구성 및 배포 옵션에 대해 자세히 설명합니다.

> [!NOTE]
> DSC 확장을 사용 하도록 설정 하기 전에 최신 버전의 DSC를 미리 보기에서 사용할 수 있다는 것을 알고 Azure Policy [게스트 구성](../../governance/policy/concepts/guest-configuration.md)이라는 기능을 통해 관리 합니다. 게스트 구성 기능은 Desired State Configuration (DSC) 확장 처리기의 기능, Azure Automation 상태 구성 및 가장 일반적으로 요청 되는 기능을 고객 피드백에서 결합 합니다. 게스트 구성에는 [Arc 사용 서버](../../azure-arc/servers/overview.md)를 통한 하이브리드 컴퓨터 지원 기능도 포함 되어 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

### <a name="operating-system"></a>운영 체제

DSC 확장에서 지원하는 OS는 다음과 같습니다.

Windows Server 2019, Windows Server 2016, Windows Server 2012R2, Windows Server 2012, Windows Server 2008 R2 SP1, Windows Client 7/8.1/10

### <a name="internet-connectivity"></a>인터넷 연결

Windows용 DSC 확장을 사용하려면 대상 가상 머신이 Azure와 통신할 수 있어야 하고 Azure 외부의 위치에 저장되어 있는 경우에는 구성 패키지(.zip 파일)의 위치와 통신할 수 있어야 합니다.

## <a name="extension-schema"></a>확장 스키마

다음 JSON은 Azure Resource Manager 템플릿의 DSC 확장 설정 부분에 대한 스키마를 보여 줍니다.

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "name": "Microsoft.Powershell.DSC",
  "apiVersion": "2018-10-01",
  "location": "<location>",
  "properties": {
    "publisher": "Microsoft.Powershell",
    "type": "DSC",
    "typeHandlerVersion": "2.77",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "wmfVersion": "latest",
        "configuration": {
            "url": "http://validURLToConfigLocation",
            "script": "ConfigurationScript.ps1",
            "function": "ConfigurationFunction"
        },
        "configurationArguments": {
            "argument1": "Value1",
            "argument2": "Value2"
        },
        "configurationData": {
            "url": "https://foo.psd1"
        },
        "privacy": {
            "dataCollection": "enable"
        },
        "advancedOptions": {
            "forcePullAndApply": false,
            "downloadMappings": {
             "specificDependencyKey": "https://myCustomDependencyLocation"
            }
        }
    },
    "protectedSettings": {
        "configurationArguments": {
            "parameterOfTypePSCredential1": {
                "userName": "UsernameValue1",
                "password": "PasswordValue1"
            },
            "parameterOfTypePSCredential2": {
                "userName": "UsernameValue2",
                "password": "PasswordValue2"
            }
        },
        "configurationUrlSasToken": "?g!bber1sht0k3n",
        "configurationDataUrlSasToken": "?dataAcC355T0k3N"
    }
  }
}
```

### <a name="property-values"></a>속성 값

| Name | 값/예제 | 데이터 형식 |
| ---- | ---- | ---- |
| apiVersion | 2018-10-01 | date |
| publisher | Microsoft.Powershell.DSC | 문자열 |
| type | DSC | 문자열 |
| typeHandlerVersion | 2.77 | int |

### <a name="settings-property-values"></a>settings(설정) 속성 값

| Name | 데이터 형식 | Description
| ---- | ---- | ---- |
| settings.wmfVersion | 문자열 | VM에 설치해야 하는 Windows Management Framework의 버전을 지정합니다. 이 속성을 'latest'로 설정하면 최신 업데이트 버전의 WMF가 설치됩니다. 현재 이 속성에는 '4.0', '5.0' 및 'latest' 값만 사용할 수 있습니다. 가능한 값은 업데이트에 따라 달라집니다. 기본값은 'latest'입니다. |
| settings.configuration.url | 문자열 | DSC 구성 zip 파일을 다운로드할 URL 위치를 지정합니다. 제공된 URL에 액세스하기 위해 SAS 토큰이 필요한 경우 protectedSettings.configurationUrlSasToken 속성을 SAS 토큰 값으로 설정해야 합니다. settings.configuration.script 및/또는 settings.configuration.function이 정의된 경우 이 속성이 필요합니다.
| settings.configuration.script | 문자열 | DSC 구성의 정의를 포함하는 스크립트의 파일 이름을 지정합니다. 이 스크립트는 configuration.url 속성에 지정된 URL에서 다운로드된 zip 파일의 루트 폴더에 있어야 합니다. settings.configuration.url 및/또는 settings.configuration.script가 정의된 경우 이 속성이 필요합니다.
| settings.configuration.function | 문자열 | DSC 구성의 이름을 지정합니다. 명명된 구성은 configuration.script에 정의된 스크립트에 있어야 합니다. settings.configuration.url 및/또는 settings.configuration.function이 정의된 경우 이 속성이 필요합니다.
| settings.configurationArguments | 컬렉션 | DSC 구성을 전달하려는 매개 변수를 정의합니다. 이 속성은 암호화되지 않습니다.
| settings.configurationData.url | 문자열 | DSC 구성에 대한 입력으로 사용할 구성 데이터(.pds1) 파일을 다운로드할 URL을 지정합니다. 제공된 URL에 액세스하기 위해 SAS 토큰이 필요한 경우 protectedSettings.configurationDataUrlSasToken 속성을 SAS 토큰 값으로 설정해야 합니다.
| settings.privacy.dataEnabled | 문자열 | 원격 분석 수집을 사용하거나 사용하지 않도록 설정합니다. 이 속성에는 Enable', 'Disable', " 또는 $null 값만 사용할 수 있습니다. 이 속성을 비워 두거나 null로 설정하면 원격 분석이 사용됩니다.
| settings.advancedOptions.forcePullAndApply | Bool | 이 설정은 확장을 사용하여 Azure Automation DSC에 노드를 등록하는 환경을 개선하도록 설계되었습니다.  값이 `$true`이면 확장은 성공/실패를 반환하기 전에 서비스에서 가져온 구성의 첫 번째 실행을 기다립니다.  값이 $false로 설정된 경우 확장에 의해 반환되는 상태는 노드가 Azure Automation State Configuration에 성공적으로 등록되었는지 여부만을 나타내고 등록하는 동안 노드 구성이 실행되지 않습니다.
| settings.advancedOptions.downloadMappings | 컬렉션 | WMF 및 .NET과 같은 종속성을 다운로드할 대체 위치를 정의합니다.

### <a name="protected-settings-property-values"></a>protectedSettings(보호된 설정) 속성 값

| Name | 데이터 형식 | Description
| ---- | ---- | ---- |
| protectedSettings.configurationArguments | 문자열 | DSC 구성을 전달하려는 매개 변수를 정의합니다. 이 속성은 암호화됩니다. |
| protectedSettings.configurationUrlSasToken | 문자열 | configuration.url에서 정의한 URL에 액세스하기 위해 SAS 토큰을 지정합니다. 이 속성은 암호화됩니다. |
| protectedSettings.configurationDataUrlSasToken | 문자열 | configuration.url에서 정의한 URL에 액세스하기 위해 SAS 토큰을 지정합니다. 이 속성은 암호화됩니다. |

## <a name="template-deployment"></a>템플릿 배포

Azure Resource Manager 템플릿을 사용하여 Azure VM 확장을 배포할 수 있습니다.
배포 후 구성이 필요한 하나 이상의 가상 머신을 배포하는 경우 템플릿을 사용하는 것이 좋습니다.
Windows용 DSC 확장을 포함하는 Resource Manager 템플릿 샘플은 [Azure 빠른 시작 갤러리](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.automation/automation-configuration/nested/provisionServer.json#L91)에서 찾을 수 있습니다.

## <a name="troubleshoot-and-support"></a>문제 해결 및 지원

### <a name="troubleshoot"></a>문제 해결

확장 배포 상태에 대한 데이터는 Azure CLI를 사용하여 Azure Portal에서 검색할 수 있습니다. 지정된 VM에 대한 확장의 배포 상태를 보려면 Azure CLI를 사용하여 다음 명령을 실행합니다.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

확장 패키지가 Azure VM의 다음 위치에 다운로드되어 배포됩니다.
```
C:\Packages\Plugins\{Extension_Name}\{Extension_Version}
```

확장 상태 파일에는 각 확장 실행에 대한 자세한 오류 및 설명과 함께 하위 상태 및 상태 성공/오류 코드가 포함되어 있습니다.
```
C:\Packages\Plugins\{Extension_Name}\{Extension_Version}\Status\{0}.Status  -> {0} being the sequence number
```

확장 출력 로그는 다음 디렉터리에 기록됩니다.

```
C:\WindowsAzure\Logs\Plugins\{Extension_Name}\{Extension_Version}
```

### <a name="error-codes-and-their-meanings"></a>오류 코드 및 해당 의미

| 오류 코드 | 의미 | 가능한 작업 |
| :---: | --- | --- |
| 1000 | 일반 오류 | 이 오류 메시지는 확장 로그의 특정 예외에서 제공됩니다. |
| 52 | 확장 설치 오류 | 이 오류 메시지는 특정 예외에서 제공됩니다. |
| 1002 | WMF 설치 오류 | WMF를 설치하는 중에 오류가 발생했습니다. |
| 1004 | 잘못된 Zip 패키지 | 잘못된 Zip입니다. Zip 파일의 압축을 푸는 중에 오류가 발생했습니다. |
| 1100 | 인수 오류 | 사용자가 제공한 입력에 문제가 있음을 나타냅니다. 이 오류 메시지는 특정 예외에서 제공됩니다.|


### <a name="support"></a>지원

이 문서의 어디에서든 도움이 필요한 경우 [MSDN Azure 및 Stack Overflow 포럼](https://azure.microsoft.com/support/forums/)에서 Azure 전문가에게 문의할 수 있습니다. 또는 Azure 기술 지원 인시던트를 제출할 수 있습니다. [Azure 지원 사이트](https://azure.microsoft.com/support/options/)로 가서 지원 받기를 선택합니다. Azure 지원을 사용하는 방법에 대한 자세한 내용은 [Microsoft Azure 지원 FAQ](https://azure.microsoft.com/support/faq/)를 참조하세요.
