---
title: 게스트 구성에 대한 PowerShell Desired State Configuration의 동작 변경 사항
description: 이 문서에서는 Azure Policy를 통해 컴퓨터에 구성 변경 내용을 제공하는 데 사용되는 플랫폼의 개요를 제공합니다.
ms.date: 05/31/2021
ms.topic: how-to
ms.openlocfilehash: b501305513e99963ec9d00a49e6e7aa1c74b3683
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130045335"
---
# <a name="changes-to-behavior-in-powershell-desired-state-configuration-for-guest-configuration"></a>게스트 구성에 대한 PowerShell Desired State Configuration의 동작 변경 사항

시작하기 전에 [게스트 구성](./guest-configuration.md)의 개요를 읽어보는 것이 좋습니다.

[이 문서의 동영상 연습 사용 가능](https://youtu.be/nYd55FiKpgs).

게스트 구성은 [DSC(Desired State Configuration)](/powershell/scripting/dsc/overview/overview) 버전 3을 사용하여 머신을 감사하고 구성합니다. DSC 구성은 컴퓨터가 충족해야 하는 상태를 정의합니다. 게스트 구성에서 DSC를 구현하는 방법에는 중요한 차이점이 많이 있습니다.

## <a name="guest-configuration-uses-powershell-7-cross-platform"></a>게스트 구성은 PowerShell 7 교차 플랫폼을 사용합니다

게스트 구성은 Windows 및 Linux 관리 환경이 일관될 수 있도록 설계되었습니다. 두 운영 체제 환경에서 PowerShell DSC 지식이 있는 사용자는 스크립팅 기술을 사용하여 구성을 만들고 게시할 수 있습니다.

게스트 구성은 PowerShell DSC 버전 3만 사용하며 [Linux용 DSC](https://github.com/Microsoft/PowerShell-DSC-for-Linux) 또는 해당 리포지토리에 포함된 "nx" 공급자의 이전 구현에 의존하지 않습니다.

게스트 구성은 Windows용 PowerShell 7.1.3 및 Linux용 PowerShell 7.2 미리 보기 6에서 작동합니다. 버전 7.2부터, `PSDesiredStateConfiguration` 모듈은 PowerShell 설치에 포함되지 않고, 대신 [PowerShell Gallery 모듈](https://www.powershellgallery.com/packages/PSDesiredStateConfiguration)로 설치됩니다.

## <a name="multiple-configurations"></a>여러 구성

게스트 구성은 동일한 컴퓨터에 여러 구성 할당을 지원합니다. 게스트 구성 확장의 운영 체제 내에서 특별한 단계가 필요하지 않습니다. [부분 구성](/powershell/scripting/dsc/pull-server/partialConfigs) 을 구성할 필요가 없습니다.

## <a name="configuration-mode-is-set-in-the-package-artifact"></a>구성 모드는 패키지 아티팩트에서 설정됩니다

구성 패키지를 만들 때, 모드는 다음 옵션을 사용하여 설정됩니다.

- _감사_: 컴퓨터의 준수를 확인합니다. 변경이 발생하지 않습니다.
- _AuditandSet_:컴퓨터의 준수 상태를 확인하고 수정합니다.
  컴퓨터가 규정을 준수하지 않으면 변경이 발생합니다.

모드는 여러 구성이 할당될 때 구성별로 다를 수 있으므로 [로컬 구성 관리자](/powershell/scripting/dsc/managing-nodes/metaConfig#basic-settings) 서비스가 아닌 패키지에서 설정됩니다.

## <a name="parameter-support-through-azure-resource-manager"></a>Azure Resource Manager를 통한 매개 변수 지원

[게스트 구성 할당](guest-configuration-assignments.md)에서 `configurationParameter` 속성 배열에 의해 설정된 매개 변수는 파일이 컴퓨터에 저장되어 있을 때 구성 MOF 파일 내의 정적 텍스트를 덮어씁니다. 매개 변수를 사용하면 머신 내에서 명령을 실행할 필요 없이, 서비스 API에서 운영자가 사용자 지정 및 변경 내용을 제어할 수 있습니다.

게스트 구성 할당에 값을 전달하는 Azure Policy의 매개 변수는 _문자열_ 형식이어야 합니다. DSC 리소스가 배열을 지원해도 매개 변수를 통해 배열을 전달할 수 없습니다.

## <a name="sequence-of-events"></a>이벤트 시퀀스

게스트 구성이 컴퓨터를 감사하거나 구성하는 경우, Windows 및 Linux 모두 동일한 이벤트 시퀀스가 사용됩니다. 동작의 주목할 만한 변경은 `Get` 메서드가 머신 상태에 대한 세부 정보를 반환하기 위해 서비스에서 호출되는 것입니다.

1. 에이전트는 먼저 `Test`를 실행하여 구성이 올바른 상태인지 확인합니다.
1. 패키지가 `Audit`로 설정된 경우, 함수에서 반환하는 부울 값이 게스트 할당에 대한 Azure Resource Manager 상태가 규정 준수/비준수 여부를 결정합니다.
1. 패키지가 `AuditandSet`로 설정된 경우, 부울 값은 `Set` 메서드를 사용하여 구성을 적용하여 컴퓨터를 수정할지 여부를 결정합니다.
   `Test` 메서드에서 False가 반환되면, `Set`가 실행됩니다. `Test`에서 True를 반환하면, `Set`가 실행되지 않습니다.
1. 마지막으로, 공급자는 `Get`를 실행하여 각 설정의 현재 상태를 반환하므로 컴퓨터가 규정을 준수하지 않는 이유 및 현재 상태가 규정을 준수하는지 확인하기 위한 세부 정보를 사용할 수 있습니다.

## <a name="trigger-set-from-outside-machine"></a>외부 컴퓨터에서 트리거 설정

이전 버전의 DSC에서는 WinRM 원격 연결에 의존하지 않고 많은 사용자 지정 코드 없이 대규모로 드리프트를 수정해야 했습니다. 게스트 구성은 이 문제를 해결합니다. 게스트 구성 사용자는 [주문형 수정](./guest-configuration-policy-effects.md#remediation-on-demand-applyandmonitor)을 통해 드리프트 수정을 제어할 수 있습니다.

## <a name="maximum-size-of-custom-configuration-package"></a>사용자 지정 구성 패키지의 최대 크기

Azure Automation State Configuration에서 DSC 구성은 [크기가 제한](../../../automation/automation-dsc-compile.md#compile-your-dsc-configuration-in-windows-powershell)됩니다.
게스트 구성은 압축 전 100MB의 총 패키지 크기를 지원합니다. 패키지 내의 MOF 파일 크기에 대한 구체적 제한은 없습니다.

## <a name="special-requirements-for-get"></a>Get에 대한 특별 요구 사항

함수 `Get` 메서드에는 Windows PowerShell Desired State Configuration에 필요하지 않은 Azure Policy 게스트 구성에 대한 특별 요구 사항이 있습니다.

- 반환되는 해시 테이블에 **Reasons** 라는 속성이 포함되어야 합니다.
- Reasons 속성은 배열이어야 합니다.
- 배열의 각 항목은 **Code** 및 **Phrase** 라는 키가 있는 hashtable이어야 합니다.
- 해시 가능 이외의 다른 값은 반환할 수 없습니다.

서비스는 Reasons 속성을 사용하여 준수 정보를 표시하는 방식을 표준화합니다. Reasons의 각 항목은 리소스가 규정을 준수하거나 준수하지 않는 "이유"라고 간주할 수 있습니다. 두 가지 이상의 이유로 리소스가 규정을 준수하지 않을 수 있으므로 속성은 배열입니다.

**Code** 및 **Phrase** 속성이 서비스에 필요합니다. 사용자 지정 리소스를 작성하는 경우 리소스가 규정을 준수하지 않는 이유를 표시하려는 텍스트(일반적으로 stdout)를 **Phrase** 값으로 설정합니다.
**Code** 에는 특정 형식 요구 사항이 있으므로 감사를 수행하는 데 사용되는 리소스에 대한 정보를 명확하게 보고할 수 있습니다. 이 솔루션은 게스트 구성을 확장 가능하게 만듭니다. 출력을 **Phrase** 속성에 대한 문자열 값으로 반환할 수 있는 한 모든 명령을 실행할 수 있습니다.

- **Code**(문자열): 리소스의 이름은 반복되고 이유에 대한 식별자로 공백이 없는 짧은 이름입니다. 이 세 값은 공백 없이 콜론으로 구분되어야 합니다.
  - 예를 들어 `registry:registry:keynotpresent`입니다.
- **Phrase**(문자열): 설정이 규정을 준수하지 않는 이유를 설명하는 사람이 읽을 수 있는 텍스트입니다.
  - 예를 들어 `The registry key $key isn't present on the machine.`입니다.

```powershell
$reasons = @()
$reasons += @{
  Code = 'Name:Name:ReasonIdentifer'
  Phrase = 'Explain why the setting is not compliant'
}
return @{
    reasons = $reasons
}
```

명령줄 도구를 사용하여 Get에 반환되는 정보를 얻을 때 도구가 예상하지 못한 출력을 반환하는 것을 확인할 수 있습니다. PowerShell에서 출력을 캡처하더라도 출력이 표준 오류로 기록되었을 수도 있습니다. 이 문제를 방지하려면 출력을 null로 리디렉션하는 것이 좋습니다.

### <a name="the-reasons-property-embedded-class"></a>Reasons 속성 포함 클래스

스크립트 기반 리소스(Windows만 해당)에서는 Reasons 클래스가 다음과 같이 스키마 MOF 파일에 포함됩니다.

```mof
[ClassVersion("1.0.0.0")]
class Reason
{
  [Read] String Phrase;
  [Read] String Code;
};

[ClassVersion("1.0.0.0"), FriendlyName("ResourceName")]
class ResourceName : OMI_BaseResource
{
  [Key, Description("Example description")] String Example;
  [Read, EmbeddedInstance("Reason")] String Reasons[];
};
```

클래스 기반 리소스(Windows 및 Linux)에서 `Reason` 클래스는 다음과 같이 PowerShell 모듈에 포함됩니다. Linux는 대/소문자를 구분하므로, 코드의 "C"와 구의 "P"를 대문자로 사용해야 합니다.

```powershell
enum ensure {
  Absent
  Present
}

class Reason {
  [DscProperty()]
  [string] $Code

  [DscProperty()]
  [string] $Phrase
}

[DscResource()]
class Example {

  [DscProperty(Key)]
  [ensure] $ensure

  [DscProperty()]
  [Reason[]] $Reasons

  [Example] Get() {
    # return current current state
  }

  [void] Set() {
    # set the state
  }

  [bool] Test() {
    # check whether state is correct
  }
}

```

리소스에 필수 속성이 있는 경우, 해당 속성도 `Get`에 의해 `Reason` 클래스와 병렬로 반환되어야 합니다. `Reason`가 포함되지 않으면 서비스는 `Get`에 대한 값 입력과 `Get`에서 반환된 값을 비교하는 "catch-all" 동작을 포함하며 `Reason`와 상세 비교를 제공합니다.

## <a name="configuration-names"></a>구성 이름

사용자 지정 구성의 이름은 모든 위치에서 일관되어야 합니다. 콘텐츠 패키지용 `.zip` 파일의 이름, MOF 파일의 구성 이름 및 Azure Resource Manager 템플릿에 있는 게스트 할당 이름은 동일해야 합니다.

## <a name="common-dsc-features-not-available-during-guest-configuration-public-preview"></a>게스트 구성 공개 미리 보기 중에는 사용할 수 없는 일반적인 DSC 기능

공개 미리 보기 중에 게스트 구성은 "WaitFor*" 리소스를 사용하여 [컴퓨터 간 의존성 지정](/powershell/scripting/dsc/configurations/crossnodedependencies)을 지원하지 않습니다. 한 컴퓨터가 진행되기 전에 다른 컴퓨터가 상태에 도달할 때까지 모니터링하고 기다릴 수는 없습니다.

[재부팅 처리](/powershell/scripting/dsc/configurations/reboot-a-node)는 게스트 구성의 공개 미리 보기 릴리스에서 사용할 수 없으며 `$global:DSCMachineStatus`에서 사용할 수 없습니다. 구성은 구성 중에 또는 구성이 끝날 때 노드를 재부팅할 수 없습니다.

## <a name="known-compatibility-issues-with-supported-modules"></a>지원되는 모듈의 알려진 호환성 문제

Microsoft는 PowerShell Gallery의 `PsDscResources` 모듈 및 Windows 함께 제공되는 `PSDesiredStateConfiguration` 모듈을 지원하며, 이는 DSC에 일반적으로 사용되는 리소스 집합입니다. DSCv3용 `PSDscResources` 모듈이 업데이트될 때까지, 다음과 같은 알려진 호환성 문제에 유의해야 합니다.

- Windows와 함께 제공되는 `PSDesiredStateConfiguration` 모듈의 리소스를 사용하지 마세요. 대신 `PSDscResources`로 전환합니다.
- `PsDscResources`에 있는 `WindowsFeature` 및 `WindowsFeatureSet` 리소스를 사용하지 마세요. 대신 `WindowsOptionalFeature` 및 `WindowsOptionalFeatureSet` 리소스로 전환합니다.
  
Linux용 DSC 리포지션에 포함된 [Linux용](https://github.com/microsoft/PowerShell-DSC-for-Linux/tree/master/Providers) "nx" 리소스는 C 및 Python 언어의 조합으로 작성되었습니다. Linux에서 DSC에 대한 경로는 PowerShell을 사용하는 것이므로 기존 "nx" 리소스는 DSCv3과 호환되지 않습니다. Linux에 대해 지원되는 리소스를 포함하는 새 모듈을 사용할 수 있게 될 때까지 사용자 지정 리소스를 작성해야 합니다.

## <a name="coexistance-with-dsc-version-3-and-previous-versions"></a>DSC 버전 3 및 이전 버전과 공존

게스트 구성의 DSC 버전 3은 [Windows](/powershell/scripting/dsc/getting-started/wingettingstarted) 및 [Linux](/powershell/scripting/dsc/getting-started/lnxgettingstarted)에 설치된 이전 버전과 공존할 수 있습니다.
구현은 별개입니다. 그러나 DSC 버전 간에 충돌 검색이 없으므로 동일한 설정을 관리하지 마세요.

## <a name="next-steps"></a>다음 단계

- [게스트 구성 개요](./guest-configuration.md)를 참조하세요.
- 사용자 지정 게스트 구성 패키지 [개발 환경](../how-to/guest-configuration-create-setup.md)을 설치합니다.
- 게스트 구성을 위한 [패키지 아티팩트를 만듭니다](../how-to/guest-configuration-create.md).
- 사용자 개발 환경에서 [패키지 아티팩트 테스트](../how-to/guest-configuration-create-test.md)합니다.
- `GuestConfiguration` 모듈을 사용하여 사용자 환경의 대규모 관리를 위한 [Azure Policy 정의를 생성](../how-to/guest-configuration-create-definition.md)합니다.
- Azure Portal을 사용하여 [사용자 지정 정책 정의를 할당](../assign-policy-portal.md)합니다.
- [게스트 구성 정책 할당에 대한 규정 준수 세부 정보](../how-to/determine-non-compliance.md#compliance-details-for-guest-configuration)를 보는 방법을 알아봅니다.
