---
ms.openlocfilehash: 45bca4d03bf2b05040b62a5fd24577a10355e762
ms.sourcegitcommit: 1aeff9f012cfd868104ef0159c5204e402d75696
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133034853"
---
## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/dotnet/).
- [Azure Az PowerShell 모듈](/powershell/azure/) 설치

전화번호를 사용하려는 경우에는 평가판 계정을 사용할 수 없습니다. 리소스를 만들기 전에 전화번호를 구매하려는 경우 구독이 모든 [요구 사항](../../concepts/telephony-sms/plan-solution.md)을 충족하는지 확인합니다. 

## <a name="create-azure-communication-services-resource"></a>Azure Communication Services 리소스 만들기

Azure Communication Services 리소스를 만들려면 [Azure CLI에 로그인합니다](/cli/azure/authenticate-azure-cli). ```Connect-AzAccount``` 명령을 사용하고 자격 증명을 제공하여 터미널을 통해 이 작업을 수행할 수 있습니다.

먼저 다음 명령을 사용하여 Azure Communication Services 모듈 ```Az.Communication```을 설치해야 합니다.

```PowerShell
PS C:\> Install-Module Az.Communication
```

다음 명령을 실행하여 리소스를 만듭니다.

```PowerShell
PS C:\> New-AzCommunicationService -ResourceGroupName ContosoResourceProvider1 -Name ContosoAcsResource1 -DataLocation UnitedStates -Location Global
```

특정 구독을 선택하려는 경우 ```--subscription``` 플래그를 지정하고 구독 ID를 제공할 수도 있습니다.
```PowerShell
PS C:\> New-AzCommunicationService -ResourceGroupName ContosoResourceProvider1 -Name ContosoAcsResource1 -DataLocation UnitedStates -Location Global -SubscriptionId SubscriptionID
```

다음 옵션을 사용하여 Communication Services 리소스를 구성할 수 있습니다.

* 리소스 그룹
* Communication Services 리소스의 이름
* 리소스가 연결되는 지리적 위치

다음 단계에서는 리소스에 태그를 할당할 수 있습니다. 태그를 사용하여 Azure 리소스를 구성할 수 있습니다. 태그에 대한 자세한 내용은 [리소스 태그 지정 설명서](../../../azure-resource-manager/management/tag-resources.md)를 참조하세요.

## <a name="manage-your-communication-services-resource"></a>Communication Services 리소스 관리

Communication Services 리소스에 태그를 추가하려면 다음 명령을 실행합니다. 특정 구독을 대상으로 지정할 수도 있습니다.

```PowerShell
PS C:\> Update-AzCommunicationService -Name ContosoAcsResource1 -ResourceGroupName ContosoResourceProvider1 -Tag @{ExampleKey1="ExampleValue1"}

PS C:\> Update-AzCommunicationService -Name ContosoAcsResource1 -ResourceGroupName ContosoResourceProvider1 -Tag @{ExampleKey1="ExampleValue1"} -SubscriptionId SubscriptionID
```

지정된 구독의 모든 Azure Communication Services 리소스를 나열하려면 다음 명령을 사용합니다.

```PowerShell
PS C:\> Get-AzCommunicationService -SubscriptionId SubscriptionID
```

지정된 리소스에 대한 모든 정보를 나열하려면 다음 명령을 사용합니다.

```PowerShell
PS C:\> Get-AzCommunicationService -Name ContosoAcsResource1 -ResourceGroupName ContosoResourceProvider1
```
