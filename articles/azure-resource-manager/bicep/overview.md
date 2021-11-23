---
title: Azure 리소스 배포를 위한 Bicep 언어
description: Azure에 인프라를 배포하기 위한 Bicep 언어를 설명합니다. JSON을 사용하여 템플릿을 개발하는 것보다 향상된 제작 환경을 제공합니다.
ms.topic: conceptual
ms.date: 11/22/2021
ms.openlocfilehash: 23c693038896dfbb1f2ac7fb47bf12f65b082458
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132937624"
---
# <a name="what-is-bicep"></a>Bicep이란?

Bicep은 선언적 구문을 사용하여 Azure 리소스를 배포하는 DSL(도메인 특정 언어)입니다. 간결한 구문, 신뢰할 수 있는 형식 안전성 및 코드 다시 사용에 대한 지원을 제공합니다. Bicep은 Azure에서 코드형 인프라 솔루션에 대한 최고의 제작 환경을 제공합니다.

ARM 템플릿(Azure Resource Manager 템플릿)을 개발하는 데 JSON 대신 Bicep을 사용할 수 있습니다. ARM 템플릿을 만드는 JSON 구문은 자세한 정보를 사용할 수 있으며 복잡한 식이 필요할 수 있습니다. Bicep 구문은 이러한 복잡성을 줄이고 개발 환경을 향상시킵니다. Bicep은 ARM 템플릿 JSON에 대한 투명한 추상화이며 JSON 템플릿 기능이 손실되지 않습니다. 배포 중에 Bicep CLI는 Bicep 파일을 ARM 템플릿 JSON으로 변환합니다.

Bicep은 애플리케이션을 작성하기 위한 일반적인 프로그래밍 언어가 아닙니다. Bicep 파일은 리소스를 만드는 일련의 프로그래밍 명령을 작성하지 않고 Azure 리소스 및 리소스 속성을 선언합니다.

ARM 템플릿에서 유효한 리소스 종류, API 버전, 속성은 Bicep 파일에서 유효합니다.

Bicep 작업 상태를 추적하려면 [Bicep 프로젝트 리포지토리](https://github.com/Azure/bicep)를 참조하세요.

Bicep에 대한 자세한 내용은 다음 비디오를 참조하세요.

> [!VIDEO https://www.youtube.com/embed/sc1kJfcRQgY]

## <a name="get-started"></a>시작

Bicep으로 시작하려면:

1. **도구를 설치** 합니다. [Bicep 개발 및 배포 환경 설정](./install.md)을 참조하세요. 또는 [VS Code Devcontainer/Codespaces 리포지토리](https://github.com/Azure/vscode-remote-try-bicep)를 사용하여 사전 구성된 작성 환경을 얻을 수 있습니다.
2. **[빠른 시작](./quickstart-create-bicep-use-visual-studio-code.md) 및 [Microsoft Learn Bicep 모듈](./learn-bicep.md)** 을 완료합니다.

기존 ARM 템플릿을 Bicep로 디컴파일 하려면 [디컴파일 ARM 템플릿 JSON To Bicep](./decompile.md)를 참조 하세요.

Bicep 파일에서 사용할 수 있는 리소스에 대해 알아보려면 [Bicep 리소스 참조](/azure/templates/)를 참조하세요.

Bicep 예는 [Bicep GitHub 리포지토리](https://github.com/Azure/bicep/tree/main/docs/examples)에서 찾을 수 있습니다.

## <a name="benefits-of-bicep-versus-other-tools"></a>Bicep 및 기타 도구의 이점

Bicep은 다른 옵션에 비해 다음과 같은 이점을 제공합니다.

- **모든 리소스 종류 및 API 버전 지원**: Bicep은 Azure 서비스에 대한 모든 미리 보기 및 GA 버전을 즉각적으로 지원합니다. 리소스 공급자가 새 리소스 종류 및 API 버전을 도입하는 즉시 Bicep 파일에서 사용할 수 있습니다. 새 서비스를 사용하기 전에 도구가 업데이트될 때까지 기다릴 필요가 없습니다.
- **단순 구문**: 동등한 JSON 템플릿과 비교할 때 Bicep 파일은 더 간결하고 읽기 쉽습니다. Bicep에는 프로그래밍 언어에 대한 이전 지식이 필요하지 않습니다. Bicep 구문은 선언적이며 배포하려는 리소스 및 리소스 속성을 지정합니다.
- **저작 경험**: VS Code를 사용하여 Bicep 파일을 만들 때 최고 수준의 작성 환경이 제공됩니다. 편집기는 풍부한 형식 안전성, Intellisense 및 구문 유효성 검사를 제공합니다.
- **모듈성**: [모듈](./modules.md)을 사용하여 Bicep 코드를 관리 가능한 부분으로 나눌 수 있습니다. 모듈은 관련 리소스 집합을 배포합니다. 모듈을 사용하면 코드를 재사용하고 개발을 간소화할 수 있습니다. 해당 리소스를 배포해야 할 때마다 모듈을 Bicep 파일에 추가합니다.
- **Azure 서비스와 통합**: Bicep은 Azure Policy, 템플릿 사양 및 청사진과 같은 Azure 서비스와 통합됩니다.
- **관리할 상태 또는 상태 파일 없음**: 모든 상태가 Azure에 저장됩니다. 사용자는 공동 작업을 수행할 수 있으며 업데이트가 예상대로 처리된다는 확신을 가질 수 있습니다. 템플릿을 배포하기 전에 변경 사항을 미리 보려면 [가상 작업](./deploy-what-if.md)을 사용합니다.
- **무료 및 오픈 소스**: Bicep은 완전히 무료입니다. 프리미엄 기능에 대한 비용을 지불할 필요가 없습니다. Microsoft 지원에서도 지원합니다.

## <a name="bicep-improvements"></a>Bicep 개선 사항

Bicep은 동등한 JSON에 비해 더 쉽고 간결한 구문을 제공합니다. 대괄호로 묶인 식 `[...]`을 사용하지 않습니다. 대신 함수를 직접 호출하고 매개 변수 및 변수에서 값을 가져옵니다. 배포된 각 리소스에 심볼 이름을 지정하여 템플릿에서 해당 리소스를 쉽게 참조할 수 있게 됩니다.

예를 들어 다음 JSON은 리소스 속성에서 출력 값을 반환합니다.

```json
"outputs": {
  "hostname": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.Network/publicIPAddresses', variables('publicIPAddressName'))).dnsSettings.fqdn]"
    },
}
```

Bicep에서는 동일한 출력 식을 더 쉽게 작성할 수 있습니다. 다음 예제에서는 템플릿 내에 정의된 리소스에 대해 심볼 이름 **publicIP** 를 사용하여 동일한 속성을 반환합니다.

```bicep
output hostname string = publicIP.properties.dnsSettings.fqdn
```

구문 전체 비교는 [템플릿에 대한 JSON 및 Bicep 비교](compare-template-syntax.md)를 참조하세요.

Bicep은 리소스 간의 종속성을 자동으로 관리합니다. 리소스의 심볼 이름이 다른 리소스 선언에서 사용되는 경우 `dependsOn`을 설정하지 않아도 됩니다.

Bicep 파일의 구조는 JSON 템플릿보다 더 유연합니다. 파일의 어디에서나 매개 변수, 변수, 출력을 선언할 수 있습니다. JSON에서는 템플릿의 해당 섹션 내에서 모든 매개 변수, 변수, 출력을 선언해야 합니다.

## <a name="faq"></a>FAQ

**기존 언어를 사용하지 않고 새 언어를 만드는 이유는 무엇인가요?**

Bicep은 새로운 언어가 아닌 기존 ARM 템플릿 언어에 대한 수정 버전으로 간주될 수 있습니다. 구문이 변경되었지만, 핵심 기능과 런타임은 동일하게 유지됩니다.

Bicep을 개발하기 전에는 기존 프로그래밍 언어를 사용하는 것을 고려했습니다. 대상 사용자가 다른 언어를 배우기 시작하는 것보다 Bicep을 배우기가 더 쉬우리라 판단했습니다.

**Terraform 또는 기타 타사 IaC(Infrastructure as Code) 제품에 에너지를 집중하지 않는 이유는 무엇인가요?**

사용자마다 선호하는 구성 언어와 도구가 다릅니다. 모든 도구가 Azure에서 뛰어난 경험을 제공하기를 원합니다. Bicep은 그 노력의 일부입니다.

Terraform을 사용하는 데 만족하는 경우 전환할 이유가 없습니다. Microsoft는 Azure에서 Terraform이 가장 적합한지 확인하기 위해 최선을 다하고 있습니다.

ARM 템플릿을 선택한 고객의 경우 Bicep이 제작 환경을 개선할 것이라 생각합니다. 또한 Bicep은 IaC를 채택하지 않은 고객의 전환에 도움이 됩니다.

**생산용으로 사용할 준비가 되었나요?**

예. 버전 0.3부터 Bicep은 Microsoft 지원 계획에서 지원됩니다. Bicep에는 ARM 템플릿을 사용하여 어떤 작업을 수행할 수 있는지에 대한 패리티가 있습니다. 현재 계획된 호환성이 손상되는 변경이 없지만 나중에 호환성이 손상되는 변경을 만들어야 할 수 있습니다.

**Azure에만 Bicep을 사용할 수 있나요?**

현재 Bicep이 Azure 이상으로 확장할 계획이 없습니다. Azure를 완벽하게 지원하고 배포 환경을 최적화하려고 합니다.

해당 목표를 충족하려면 Azure 외부에 있는 일부 API를 사용해야 합니다. 해당 시나리오에 대한 확장성 지점을 제공할 예정입니다.

**사용하고 있던 기존 ARM 템플릿은 어떻게 되나요?**

기존 ARM 템플릿은 계속해서 지금까지와 똑같이 작동합니다. ARM 템플릿을 변경할 필요가 없습니다. 기본 ARM 템플릿 JSON 언어를 계속 지원할 것입니다. Bicep 파일은 JSON으로 컴파일되고 해당 JSON은 배포를 위해 Azure에 전송됩니다.

준비가 되면 [JSON 파일을 Bicep으로 디컴파일](./decompile.md)할 수 있습니다.

**Bicep를 사용 하 여 Azure Stack 허브에 배포할 수 있나요?**

예, Azure Stack 허브 배포에 대해 Bicep를 사용할 수 있지만, Bicep에는 Azure Stack 허브에서 아직 사용할 수 없는 형식이 표시 될 수 있습니다. [Azure Stack Hub 빠른 시작 템플릿 GitHub 리포지토리](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/Bicep)에서 예제 집합을 볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

[빠른 시작](./quickstart-create-bicep-use-visual-studio-code.md)으로 시작
