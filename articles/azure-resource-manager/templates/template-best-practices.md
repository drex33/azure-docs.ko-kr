---
title: 템플릿 모범 사례
description: Azure Resource Manager 템플릿(ARM 템플릿) 작성에 대한 권장되는 방법을 설명합니다. 템플릿을 사용할 때 일반적인 문제를 방지하기 위한 제안을 제공합니다.
ms.topic: conceptual
ms.date: 04/23/2021
ms.openlocfilehash: a9b7530f7d9e3e86a3f2137cda5fcefa5e101c23
ms.sourcegitcommit: 5c136a01bddfccb2cc9f7e7e7741e2cf2651ddbe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/03/2021
ms.locfileid: "111353492"
---
# <a name="arm-template-best-practices"></a>ARM 템플릿 모범 사례

이 문서에서는 Azure Resource Manager 템플릿(ARM 템플릿)을 생성할 때 모범 사례를 사용하는 방법을 보여줍니다. 이러한 권장 사항은 ARM 템플릿을 사용하여 솔루션을 배포할 때 일반적인 문제를 방지하는 데 도움이 됩니다.

## <a name="template-limits"></a>템플릿 제한

템플릿 크기를 4MB로 제한합니다. 4MB의 제한은 반복적인 리소스 정의로 확장된 후 템플릿의 마지막 상태와 변수 및 매개 변수 값에 적용됩니다. 매개 변수 파일도 4MB로 제한됩니다. 총 요청 크기가 너무 크면 4 MB 미만의 템플릿이나 매개 변수 파일에 오류가 발생할 수 있습니다. 큰 요청이 방지되도록 템플릿을 간소화하는 방법에 대한 자세한 내용은 [작업 크기 초과에 대한 오류 해결](error-job-size-exceeded.md)을 참조하세요.

또한 다음으로 제한됩니다.

* 매개 변수 256개
* 변수 256개
* 리소스 800개(인쇄 매수 포함)
* 출력 값 64개
* 템플릿 식의 문자 24,576자

중첩된 템플릿을 사용하여 일부 템플릿 제한을 초과할 수 있습니다. 자세한 내용은 [Azure 리소스를 배포할 때 연결된 템플릿 및 중첩된 템플릿 사용](linked-templates.md)을 참조하세요. 매개 변수, 변수 또는 출력의 수를 줄이려면 개체에 여러 값을 결합할 수 있습니다. 자세한 내용은 [매개 변수로 개체 사용](/azure/architecture/guide/azure-resource-manager/advanced-templates/objects-as-parameters)을 참조하세요.

## <a name="resource-group"></a>리소스 그룹

리소스 그룹으로 리소스를 배포할 때 리소스 그룹은 리소스에 대한 메타데이터를 저장합니다. 메타데이터는 리소스 그룹의 위치에 저장됩니다.

리소스 그룹의 지역이 일시적으로 사용할 수 없는 경우 메타데이터를 사용할 수 없기 때문에 리소스 그룹의 리소스를 업데이트할 수 없습니다. 다른 지역에 있는 리소스는 여전히 예상대로 작동하지만 업데이트는 불가능합니다. 위험을 최소화하려면 동일한 지역에 있는 리소스 그룹 및 리소스를 찾습니다.

## <a name="parameters"></a>매개 변수

이 섹션의 정보는 [매개 변수](template-parameters.md)로 작업하는 경우 도움이 될 수 있습니다.

### <a name="general-recommendations-for-parameters"></a>매개 변수에 대한 일반 권장 사항

* 매개 변수 사용을 최소화합니다. 대신 배포하는 동안 지정할 필요가 없는 속성에 대한 변수 또는 리터럴 값을 사용합니다.

* 매개 변수 이름에 카멜식 대/소문자를 사용합니다.

* SKU, 크기 또는 용량과 같이 환경에 따라 달라지는 설정에는 매개 변수를 사용합니다.

* 쉽게 식별할 수 있도록 지정하려는 리소스 이름에 대해 매개 변수를 사용합니다.

* 매개 변수에 모든 메타데이터의 설명을 제공합니다.

    ```json
    "parameters": {
      "storageAccountType": {
        "type": "string",
        "metadata": {
          "description": "The type of the new storage account created to store the VM disks."
        }
      }
    }
    ```

* 중요하지 않은 매개 변수의 기본값을 정의합니다. 기본값을 지정하여 템플릿을 배포하는 것이 쉽습니다. 템플릿의 사용자에게 적절한 값의 예제가 표시됩니다. 매개 변수의 기본값은 기본 배포 구성에서 모든 사용자에 대해 유효해야 합니다.

    ```json
    "parameters": {
      "storageAccountType": {
        "type": "string",
        "defaultValue": "Standard_GRS",
        "metadata": {
          "description": "The type of the new storage account created to store the VM disks."
        }
      }
    }
    ```

* 선택적 매개 변수를 지정하려면 기본값으로 빈 문자열을 사용하지 마세요. 대신 리터럴 값 또는 언어 식을 사용하여 값을 생성합니다.

   ```json
   "storageAccountName": {
     "type": "string",
     "defaultValue": "[concat('storage', uniqueString(resourceGroup().id))]",
     "metadata": {
       "description": "Name of the storage account"
     }
   }
   ```

* `allowedValues`를 제한적으로 사용합니다. 일부 값이 허용되는 옵션에 포함되지 않도록 해야 하는 경우에만 사용합니다. `allowedValues`를 너무 광범위하게 사용하는 경우 목록을 최신 상태로 유지하지 않아 유효한 배포를 차단할 수 있습니다.

* 템플릿의 매개 변수 이름이 PowerShell 배포 명령의 매개 변수와 일치하는 경우 Resource Manager는 템플릿 매개 변수에 **FromTemplate** 이라는 접미사를 추가하여 이러한 이름 충돌을 해결합니다. 예를 들어 템플릿에 **ResourceGroupName** 이라는 매개 변수가 포함되면 [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) cmdlet의 **ResourceGroupName** 매개 변수와 충돌합니다. 배포하는 동안 **ResourceGroupNameFromTemplate** 에 대한 값을 제공하라는 메시지가 표시됩니다.

### <a name="security-recommendations-for-parameters"></a>매개 변수에 대한 보안 권장 사항

* 항상 사용자 이름 및 암호(또는 비밀)에 대한 매개 변수를 사용합니다.

* 모든 암호 및 비밀에 대해 `securestring`을 사용합니다. JSON 개체에 중요한 데이터를 전달하는 경우 `secureObject` 유형을 사용합니다. 리소스 배포 후에는 secure string 또는 secure object 형식의 템플릿 매개 변수를 읽을 수 없습니다.

    ```json
    "parameters": {
      "secretValue": {
        "type": "securestring",
        "metadata": {
          "description": "The value of the secret to store in the vault."
        }
      }
    }
    ```

* `secureString` 형식이 필요한 사용자 이름, 암호 또는 모든 값에 대한 기본값을 제공하지 마세요.

* 애플리케이션의 공격 노출 영역을 증가시키는 속성에 대한 기본값을 제공하지 마세요.

### <a name="location-recommendations-for-parameters"></a>매개 변수에 대한 위치 권장 사항

* 매개 변수를 사용하여 리소스에 대한 위치를 지정하고, 기본값을 `resourceGroup().location`으로 설정합니다. 위치 매개 변수를 제공하면 템플릿의 사용자는 리소스 배포 권한이 있는 위치를 지정할 수 있습니다.

   ```json
   "parameters": {
     "location": {
       "type": "string",
       "defaultValue": "[resourceGroup().location]",
       "metadata": {
         "description": "The location in which the resources should be deployed."
       }
     }
   }
   ```

* 위치 매개 변수에 대해 `allowedValues`를 지정하지 마세요. 지정하는 위치는 모든 클라우드에서 사용할 수 없을 수도 있습니다.

* 동일한 위치에 있을 수 있는 리소스에 대해 위치 매개 변수 값을 사용합니다. 이렇게 하면 사용자에게 위치 정보를 제공하라고 요청하는 횟수가 최소화됩니다.

* 모든 위치에서 사용할 수 없는 리소스의 경우 별도 매개 변수를 사용하거나 리터럴 위치 값을 지정합니다.

## <a name="variables"></a>변수

다음 정보는 [변수](template-variables.md)로 작업하는 경우 도움이 될 수 있습니다.

* 변수 이름에 카멜식 대/소문자를 사용합니다.

* 두 번 이상 사용해야 하는 값의 경우 템플릿에서 변수를 사용합니다. 값을 한 번만 사용할 경우에는 하드 코드된 값을 사용해야 템플릿을 더 쉽게 읽을 수 있습니다.

* 복잡한 배열의 템플릿 함수에서 생성하는 값에 대해 변수를 사용합니다. 복잡한 식이 변수에만 표시되는 경우 템플릿을 읽기 쉽습니다.

* 템플릿의 `variables` 섹션에서 [reference](template-functions-resource.md#reference) 함수를 사용할 수 없습니다. `reference` 함수는 리소스의 런타임 상태에서 해당 값을 파생합니다. 하지만 템플릿의 초기 구문 분석 중에 변수가 확인됩니다. 템플릿의 `resources` 또는 `outputs` 섹션에서 직접 `reference` 함수에 필요한 값을 생성합니다.

* 고유해야 하는 리소스 이름에 대한 변수를 포함합니다.

* [변수에서 루프 복사](copy-variables.md)를 사용하여 JSON 개체의 반복된 패턴을 생성합니다.

* 사용하지 않는 변수를 제거합니다.

## <a name="api-version"></a>API 버전

`apiVersion` 속성을 리소스 종류에 대한 하드 코딩된 API 버전으로 설정합니다. 새 템플릿을 만들 때 리소스 종류에 대한 최신 API 버전을 사용하는 것이 좋습니다. 사용 가능한 값을 확인하려면 [템플릿 참조](/azure/templates/)를 참조하세요.

템플릿이 예상대로 작동하는 경우 동일한 API 버전을 계속 사용하는 것이 좋습니다. 동일한 API 버전을 사용하면 후속 버전에서 호환성이 손상되는 변경이 생길 것을 걱정할 필요가 없습니다.

API 버전에 대해서는 매개 변수를 사용하지 마세요. 리소스 속성 및 값은 API 버전에 따라 달라질 수 있습니다. API 버전이 매개 변수로 설정되면 코드 편집기의 IntelliSense에서 올바른 스키마를 확인할 수 없습니다. 템플릿의 속성과 일치하지 않는 API 버전을 전달하는 경우 배포에 실패합니다.

API 버전에 대해 변수를 사용하지 마세요. 

## <a name="resource-dependencies"></a>리소스 종속성

설정할 [종속성](define-resource-dependency.md)을 결정하는 경우 다음 지침을 따르세요.

* `reference` 함수를 사용하고 리소스 이름을 전달하여 속성을 공유해야 하는 리소스 간에 암시적 종속성을 설정합니다. 암시적 종속성을 이미 정의한 경우에는 명시적 `dependsOn` 요소를 추가하지 마세요. 이러한 방법은 불필요한 종속성이 포함될 위험을 줄여줍니다. 암시적 종속성 설정에 대한 예시는 [reference 및 list 함수](define-resource-dependency.md#reference-and-list-functions)를 참조하세요.

* 자식 리소스가 부모 리소스에 종속되도록 설정합니다.

* false로 설정된 [condition 요소](conditional-resource-deployment.md)가 있는 리소스는 종속성 순서에서 자동으로 제거됩니다. 리소스를 항상 배포하는 경우에 따라 종속성을 설정합니다.

* 종속성을 명시적으로 설정하지 않고 계단식으로 배열되도록 합니다. 예를 들어 가상 머신은 가상 네트워크 인터페이스에 종속되고 가상 네트워크 인터페이스는 가상 네트워크 및 공용 IP 주소에 종속됩니다. 따라서 가상 머신은 세 가지 모든 리소스보다 나중에 배포되지만 가상 머신이 세 가지 모든 리소스에 종속된다고 명시적으로 설정하지 않습니다. 이러한 방법은 종속성 순서를 명확히 하고 나중에 템플릿을 쉽게 변경할 수 있도록 합니다.

* 배포하기 전에 값을 확인할 수 있으면 종속성 없이 리소스 배포를 시도해 봅니다. 예를 들어 구성 값에 다른 리소스의 이름이 필요하면 종속성이 불필요할 수 있습니다. 일부 리소스는 다른 리소스의 존재를 확인하기 때문에 이 지침이 항상 해당되는 것은 아닙니다. 오류가 표시되면 종속성을 추가합니다.

## <a name="resources"></a>리소스

다음 정보는 [리소스](template-syntax.md#resources)로 작업하는 경우 도움이 될 수 있습니다.

* 다른 참가자들이 리소스의 용도를 이해하도록 하려면 템플릿에 각 리소스에 대한 `comments`를 지정합니다.

    ```json
    "resources": [
      {
        "name": "[variables('storageAccountName')]",
        "type": "Microsoft.Storage/storageAccounts",
        "apiVersion": "2019-06-01",
        "location": "[resourceGroup().location]",
        "comments": "This storage account is used to store the VM disks.",
          ...
      }
    ]
    ```

* 템플릿에서 *공용 엔드포인트*(예: Azure Blob 스토리지 공용 엔드포인트)를 사용하는 경우 네임스페이스를 *하드 코딩하지 마세요*. `reference` 함수를 사용하여 네임스페이스를 동적으로 검색합니다. 이 방법을 사용하면 템플릿의 엔드포인트를 수동으로 변경하지 않고 다른 공용 네임스페이스 환경에 템플릿을 배포할 수 있습니다. 템플릿에서 스토리지 계정에 사용하는 것과 동일한 버전으로 API 버전을 설정합니다.

    ```json
    "diagnosticsProfile": {
      "bootDiagnostics": {
        "enabled": "true",
        "storageUri": "[reference(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').primaryEndpoints.blob]"
      }
    }
    ```

   스토리지 계정이 작성 중인 동일한 템플릿에 배포되고 스토리지 계정 이름이 템플릿의 다른 리소스와 공유되지 않는 경우 리소스를 참조할 때 공급자 네임스페이스 또는 `apiVersion`을 지정할 필요가 없습니다. 다음 예제에서는 간소화된 구문을 보여줍니다.

    ```json
    "diagnosticsProfile": {
      "bootDiagnostics": {
        "enabled": "true",
        "storageUri": "[reference(variables('storageAccountName')).primaryEndpoints.blob]"
      }
    }
    ```

   다른 리소스 그룹에서 기존 스토리지 계정을 참조할 수도 있습니다.

    ```json
    "diagnosticsProfile": {
      "bootDiagnostics": {
        "enabled": "true",
        "storageUri": "[reference(resourceId(parameters('existingResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('existingStorageAccountName')), '2019-06-01').primaryEndpoints.blob]"
      }
    }
    ```

* 애플리케이션에 필요한 경우에만 공용 IP 주소를 가상 머신에 할당합니다. 디버깅, 관리 또는 관리 목적으로 가상 컴퓨터(VM)에 연결하려면 인바운드 NAT 규칙, 가상 네트워크 게이트웨이 또는 Jumpbox를 사용합니다.

     가상 머신에 연결하는 방법에 대한 자세한 내용은 다음을 참조하세요.

   * [Azure에서 N 계층 아키텍처에 대한 VM 실행](/azure/architecture/reference-architectures/n-tier/n-tier-sql-server)
   * [Azure Resource Manager에서 VM에 대한 WinRM 액세스 설정](../../virtual-machines/windows/winrm.md)
   * [Azure Portal을 사용하여 VM에 대한 외부 액세스 허용](../../virtual-machines/windows/nsg-quickstart-portal.md)
   * [PowerShell을 사용하여 VM에 대한 외부 액세스 허용](../../virtual-machines/windows/nsg-quickstart-powershell.md)
   * [Azure CLI를 사용하여 Linux VM에 대한 외부 액세스 허용](../../virtual-machines/linux/nsg-quickstart.md)

* 공용 IP 주소에 대한 `domainNameLabel` 속성은 고유해야 합니다. `domainNameLabel` 값은 3~63자 사이여야 하고 `^[a-z][a-z0-9-]{1,61}[a-z0-9]$` 정규식으로 지정된 규칙을 따라야 합니다. `uniqueString` 함수는 13자 길이의 문자열을 생성하고, `dnsPrefixString` 매개 변수는 50자로 제한됩니다.

    ```json
    "parameters": {
      "dnsPrefixString": {
        "type": "string",
        "maxLength": 50,
        "metadata": {
          "description": "The DNS label for the public IP address. It must be lowercase. It should match the following regular expression, or it will raise an error: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$"
        }
      }
    },
    "variables": {
      "dnsPrefix": "[concat(parameters('dnsPrefixString'),uniquestring(resourceGroup().id))]"
    }
    ```

* 사용자 지정 스크립트 확장에 암호를 추가하는 경우 `protectedSettings` 속성에서 `commandToExecute` 속성을 사용합니다.

    ```json
    "properties": {
      "publisher": "Microsoft.Azure.Extensions",
      "type": "CustomScript",
      "typeHandlerVersion": "2.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "fileUris": [
          "[concat(variables('template').assets, '/lamp-app/install_lamp.sh')]"
        ]
      },
      "protectedSettings": {
        "commandToExecute": "[concat('sh install_lamp.sh ', parameters('mySqlPassword'))]"
      }
    }
    ```

   > [!NOTE]
   > VM 및 확장에 매개 변수로 전달되는 비밀이 암호화되도록 하려면 관련 확장의 `protectedSettings` 속성을 사용합니다.

* 시간이 지남에 따라 변경될 수 있는 기본값을 가진 속성에 대해 명시적 값을 지정합니다. 예를 들어 AKS 클러스터를 배포하는 경우 `kubernetesVersion` 속성을 지정하거나 생략할 수 있습니다. 지정하지 않는 경우 [클러스터는 기본적으로 N-1 부 버전 및 최신 패치로 설정됩니다](../../aks/supported-kubernetes-versions.md#azure-portal-and-cli-versions). ARM 템플릿을 사용하여 클러스터를 배포하는 경우 이 기본 동작이 예상과 다를 수 있습니다. 템플릿을 다시 배포하면 클러스터가 예기치 않게 새 Kubernetes 버전으로 업그레이드될 수 있습니다. 대신 명시적 버전 번호를 지정한 다음 클러스터를 업그레이드할 준비가 되면 수동으로 변경하는 것이 좋습니다.

## <a name="use-test-toolkit"></a>테스트 도구 키트 사용

ARM 템플릿 테스트 도구 키트는 템플릿에서 모범 사례를 사용하는지 여부를 확인하는 스크립트입니다. 템플릿이 모범 사례를 준수하지 않는 경우 변경 제안과 함께 경고 목록을 반환합니다. 테스트 도구 키트를 통해 템플릿에서 모범 사례를 구현하는 방법을 학습할 수 있습니다.

템플릿 작성을 완료한 후 테스트 도구 키트를 실행하여 구현을 향상시킬 수 있는 방법이 있는지 확인합니다. 자세한 내용은 [ARM 템플릿 테스트 도구 키트 사용](test-toolkit.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* 템플릿 파일의 구조에 대한 자세한 내용은 [ARM 템플릿의 구조 및 구문 이해](template-syntax.md)를 참조하세요.
* 모든 Azure 클라우드 환경에서 작동하는 템플릿을 빌드하는 방법에 대한 권장 사항은 [클라우드 일관성에 대한 ARM 템플릿 개발](templates-cloud-consistency.md)을 참조하세요.
