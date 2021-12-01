---
title: 템플릿 개요
description: ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 리소스 배포를 수행하는 경우의 이점에 대해 설명합니다.
ms.topic: conceptual
ms.date: 12/01/2021
ms.openlocfilehash: b0678282578630d39e18c58bcadc128861b2eff9
ms.sourcegitcommit: 9567c42d1e5270af16a1a8090f11a3b12131010d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133426140"
---
# <a name="what-are-arm-templates"></a>ARM 템플릿이란?

클라우드로 전환하며 많은 팀이 Agile 개발 방법을 채택했습니다. 이들 팀은 반복 작업을 빠르게 수행합니다. 클라우드에 솔루션을 반복적으로 배포해야 하고, 인프라가 신뢰할 수 있는 상태에 있는지 파악해야 합니다. 인프라가 반복 프로세스의 일부가 되면서 작업과 개발 간의 구분이 사라졌습니다. 팀은 통합 프로세스를 통해 인프라 및 애플리케이션 코드를 관리해야 합니다.

해당 문제를 해결하기 위해 배포를 자동화하고 코드형 인프라를 실행할 수 있습니다. 배포해야 할 인프라를 코드에서 정의합니다. 인프라 코드가 프로젝트의 일부가 됩니다. 애플리케이션 코드처럼 소스 리포지토리에 인프라 코드를 저장하고 버전을 지정할 수 있습니다. 모든 팀 구성원이 코드를 실행하고 유사한 환경을 배포할 수 있습니다.

Azure 솔루션의 코드형 인프라를 구현하려면 ARM 템플릿(Azure Resource Manager 템플릿)을 사용하세요. 해당 템플릿은 프로젝트에 대한 인프라 및 구성을 정의하는 JSON(JavaScript Object Notation) 파일입니다. 이 템플릿은 대상을 만들기 위한 프로그래밍 명령 시퀀스를 작성하지 않고도 배포하려는 대상을 설명할 수 있는 선언적 구문입니다. 배포할 리소스와 해당 리소스의 속성을 템플릿에서 지정합니다.

> [!TIP]
> ARM 템플릿과 동일한 기능을 제공 하지만 사용 하기 쉬운 구문을 사용 하는 [Bicep](../bicep/overview.md) 라는 새로운 언어가 도입 되었습니다. 각 Bicep 파일은 배포 중에 자동으로 ARM 템플릿으로 변환 됩니다. 인프라를 코드 옵션으로 고려 하는 경우 Bicep을 살펴보는 것이 좋습니다. 자세한 내용은 [Bicep이란 무엇인가요?](../bicep/overview.md)를 참조하세요.

ARM 템플릿을 사용하는 방법에 대한 자세한 내용은 다음 비디오를 참조하세요.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Enablement/How-and-why-to-learn-about-ARM-templates/player]

## <a name="why-choose-arm-templates"></a>ARM 템플릿을 선택해야 하는 이유

ARM 템플릿과 다른 코드형 인프라 서비스 중 무엇을 사용할지 고민한다면, 템플릿 사용 시 얻을 수 있는 다음과 같은 이점을 고려해 보세요.

* **선언적 구문**: ARM 템플릿을 사용하면 전체 Azure 인프라를 선언적으로 생성하고 배포할 수 있습니다. 예를 들면 가상 머신뿐만 아니라 네트워크 인프라, 스토리지 시스템 및 필요한 기타 리소스도 배포할 수 있습니다.

* **반복 가능한 결과**: 개발 수명 주기 내내 인프라를 반복적으로 배포하며, 안정적이고 일관된 리소스 배포를 보장할 수 있습니다. 템플릿은 idempotent이며, 이는 동일한 템플릿을 여러 번 배포하고 동일한 상태에 있는 동일한 유형의 리소스를 가져올 수 있음을 의미합니다. 업데이트를 나타내는 여러 개의 개별 템플릿을 개발하는 대신 원하는 상태를 나타내는 하나의 템플릿을 개발할 수 있습니다.

* **오케스트레이션**: 복잡한 작업 순서 지정 작업에 대해 우려할 필요가 없습니다. Resource Manager는 리소스가 올바른 순서로 생성되도록 상호 의존적인 리소스의 배포를 오케스트레이션합니다. 가능한 경우 Resource Manager는 리소스를 병렬로 배포하여, 직렬로 배포될 때보다 배포가 빠르게 완료되도록 합니다. 다수의 명령어가 아닌 하나의 명령으로 템플릿을 배포합니다.

   ![템플릿 배포 비교](./media/overview/template-processing.png)

* **모듈식 파일**: 템플릿을 재사용 가능한 소규모 구성 요소로 분할하고, 배포 시 서로 연결할 수 있습니다. 하나의 템플릿을 다른 템플릿 내에 중첩할 수도 있습니다.

* **Azure 리소스 만들기**: 새로운 Azure 서비스 및 기능을 템플릿에서 즉시 사용할 수 있습니다. 리소스 공급자가 새 리소스를 도입하면, 템플릿을 통해 해당 리소스를 즉시 배포할 수 있습니다. 새 서비스를 사용하기 전에 도구나 모듈이 업데이트되기를 기다릴 필요가 없습니다.

* **확장성**: [배포 스크립트](deployment-script-template.md)를 사용하여 템플릿에 PowerShell 또는 Bash 스크립트를 추가할 수 있습니다. 배포 스크립트는 배포 중에 리소스를 설정하는 능력을 확장합니다. 스크립트는 템플릿에 포함될 수도 있고, 외부 소스에 저장되어 템플릿이 해당 스크립트를 참조할 수도 있습니다. 배포 스크립트를 사용하면 단일 ARM 템플릿에서 엔드투엔드 환경 설정을 완료할 수 있습니다.

* **테스트**: ARM 템플릿 도구 키트(arm-ttk)로 템플릿을 테스트하여 권장 지침을 따르는지 확인할 수 있습니다. 이 테스트 키트는 [GitHub](https://github.com/Azure/arm-ttk)에서 다운로드할 수 있는 PowerShell 스크립트입니다. 도구 키트를 사용하면 템플릿 언어를 사용하여 보다 쉽게 전문성을 계발할 수 있습니다.

* **변경 내용 미리 보기**: [가상 작업](./deploy-what-if.md)을 사용하여 템플릿을 배포하기 전에 변경 내용 미리 보기를 가져올 수 있습니다. 가상 작업으로 어떤 리소스가 생성되고, 업데이트되고, 삭제될 것인지 확인하고, 변경될 모든 리소스 속성을 볼 수 있습니다. 가상 작업은 사용자 환경의 현재 상태를 확인하고 상태를 관리할 필요가 없도록 해 줍니다.

* **기본 제공 유효성 검사**: 템플릿이 배포되려면 먼저 유효성 검사를 통과해야 합니다. 또한 Resource Manager는 배포의 성공 여부를 확인하기 위해 배포를 시작하기 전에 템플릿을 확인합니다. 배포가 반 정도 완료된 상태에서는 중단될 가능성이 낮습니다.

* **배포 추적**: Azure Portal에서 배포 기록을 검토하고 템플릿 배포에 대한 정보를 가져올 수 있습니다. 배포된 템플릿, 전달된 매개 변수 값, 모든 출력 값을 확인할 수 있습니다. 다른 코드형 인프라는 포털을 통해 추적되지 않습니다.

   ![배포 기록](./media/overview/deployment-history.png)

* **코드형 정책**: [Azure Policy](../../governance/policy/overview.md)는 거버넌스를 자동화하는 코드형 정책 프레임워크입니다. Azure 정책을 사용하면 템플릿을 통해 리소스를 배포할 때 규격에 맞지 않는 리소스에 대해 정책 수정이 수행됩니다.

* **배포 청사진**: 규정 및 규정 준수 표준을 충족하기 위해 Microsoft에서 제공하는 [청사진](../../governance/blueprints/overview.md)을 활용할 수 있습니다. 청사진에는 다양한 아키텍처를 위해 미리 빌드된 템플릿이 포함되어 있습니다.

* **CI/CD 통합**: CI/CD(연속 통합 및 지속적인 배포) 도구에 템플릿을 통합하여 빠르고 안정적인 애플리케이션 및 인프라 업데이트를 위해 릴리스 파이프라인을 자동화할 수 있습니다. Azure DevOps 및 Resource Manager 템플릿 작업을 통해 Azure Pipelines를 사용해서 ARM 템플릿 프로젝트를 지속적으로 빌드하고 배포할 수 있습니다. 자세한 내용은 [파이프라인을 사용한 VS 프로젝트](add-template-to-azure-pipelines.md) 및 [자습서: Azure Resource Manager 템플릿과 Azure Pipelines의 연속 통합](./deployment-tutorial-pipeline.md)을 참조하세요.

* **내보내기 가능한 코드**: 리소스 그룹의 현재 상태를 내보내거나 특정 배포에 사용된 템플릿을 검토하여 기존 리소스 그룹에 대한 템플릿을 가져올 수 있습니다. [내보낸 템플릿](export-template-portal.md)을 살펴보면 템플릿 구문에 대해 알아보는 데 도움이 됩니다.

* **제작 도구**: [Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md) 및 템플릿 도구 확장을 통해 템플릿을 작성할 수 있습니다. Intellisense, 구문 강조, 인라인 도움말 및 기타 많은 언어 함수를 사용할 수 있습니다. Visual Studio Code 외에도 [Visual Studio](create-visual-studio-deployment-project.md)를 사용할 수 있습니다.

## <a name="template-file"></a>템플릿 파일

템플릿 내에서 JSON 기능을 확장하는 [템플릿 식](template-expressions.md)을 작성할 수 있습니다. 해당 식은 Resource Manager에서 제공하는 [함수](template-functions.md)를 사용합니다.

템플릿은 다음과 같은 섹션으로 구성됩니다.

* [매개 변수](./parameters.md) - 배포 중에 다양한 환경에서 동일한 템플릿을 사용할 수 있도록 허용하는 값을 제공합니다.

* [변수](./variables.md) - 템플릿에서 재사용되는 값을 정의합니다. 변수는 매개 변수 값에서 생성될 수 있습니다.

* [사용자 정의 함수](./user-defined-functions.md) - 템플릿을 간소화하는 사용자 지정 함수를 만듭니다.

* [리소스](resource-declaration.md) - 배포할 리소스를 지정합니다.

* [출력](./outputs.md) - 배포된 리소스의 값을 반환합니다.

## <a name="template-deployment-process"></a>템플릿 배포 프로세스

템플릿을 배포할 때 Resource Manager가 템플릿을 REST API 작업으로 변환합니다. 예를 들어 리소스 관리자가 다음 리소스 정의로 템플릿을 받는 경우:

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2019-04-01",
    "name": "mystorageaccount",
    "location": "westus",
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "StorageV2",
    "properties": {}
  }
]
```

Microsoft.Storage 리소스 공급자에게 전송되는 다음 REST API 작업으로 정의를 변환합니다.

```HTTP
PUT
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/mystorageaccount?api-version=2019-04-01
REQUEST BODY
{
  "location": "westus",
  "sku": {
    "name": "Standard_LRS"
  },
  "kind": "StorageV2",
  "properties": {}
}
```

리소스에 대해 템플릿에 설정한 **apiVersion** 이 REST 작업의 API 버전으로 사용됩니다. 템플릿을 반복적으로 배포할 수 있으며 배포는 안정적으로 지속 가능합니다. 동일한 API 버전을 사용하면 후속 버전에서 호환성이 손상되는 변경이 생길 것을 걱정할 필요가 없습니다.

템플릿을 배포하려면 다음 옵션 중 하나를 사용합니다.

* [Azure Portal](deploy-portal.md)
* [Azure CLI](deploy-cli.md)
* [PowerShell](deploy-powershell.md)
* [REST API](deploy-rest.md)
* [GitHub 리포지토리의 단추](deploy-to-azure-button.md)
* [Azure Cloud Shell](deploy-cloud-shell.md)

## <a name="template-design"></a>템플릿 디자인

템플릿 및 리소스 그룹을 정의하는 방법은 사용자 및 솔루션을 관리하려는 방법에 전적으로 달려 있습니다. 예를 들어 단일 템플릿을 통해 3계층 애플리케이션을 단일 리소스 그룹에 배포할 수 있습니다.

![3계층 템플릿](./media/overview/3-tier-template.png)

그러나 단일 템플릿에서 전체 인프라를 정의할 필요가 없습니다. 대부분 배포 요구 사항을 대상, 목적에 특정 템플릿 집합으로 나누는 것이 좋습니다. 서로 다른 솔루션에 이러한 템플릿을 쉽게 다시 사용할 수 있습니다. 특정 솔루션을 배포하려면 모든 필수 템플릿에 연결하는 메인 템플릿을 만듭니다. 다음 이미지는 세 개의 중첩된 템플릿을 포함하는 부모 템플릿을 통해 3계층 솔루션을 배포하는 방법을 보여 줍니다.

![중첩된 계층 템플릿](./media/overview/nested-tiers-template.png)

계층이 별도 수명 주기를 갖도록 계획하는 경우 3계층을 별도 리소스 그룹에 배포할 수 있습니다. 리소스는 다른 리소스 그룹의 리소스에 계속해서 연결될 수 있습니다.

![계층 템플릿](./media/overview/tier-templates.png)

중첩된 템플릿에 대한 자세한 내용은 [Azure Resource Manager에서 연결된 템플릿 사용](linked-templates.md)을 참조하세요.

## <a name="share-templates"></a>템플릿 공유

템플릿을 만든 후 조직의 다른 사용자와 공유할 수 있습니다. [템플릿 사양](template-specs.md)을 사용하면 템플릿을 리소스 종류로 저장할 수 있습니다. 역할 기반 액세스 제어를 사용하여 템플릿 사양에 대한 액세스를 관리할 수 있습니다. 템플릿 사양에 대한 읽기 권한이 있는 사용자는 템플릿을 배포할 수 있지만 템플릿을 변경할 수는 없습니다.

이 접근 방식을 사용하면 조직의 표준에 맞는 템플릿을 안전하게 공유할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* 템플릿 만들기 프로세스를 안내하는 단계별 자습서는 [자습서: 첫 번째 ARM 템플릿 만들기 및 배포](template-tutorial-create-first-template.md)를 참조하세요.
* Microsoft Learn의 단계별 모듈 세트를 통한 ARM 템플릿에 대한 자세한 내용은 [ARM 템플릿을 사용하여 Azure에서 리소스 배포 및 관리](/learn/paths/deploy-manage-resource-manager-templates/)를 참조하세요.
* 템플릿 파일의 속성에 대한 자세한 내용은 [ARM 템플릿의 구조 및 구문 이해](./syntax.md)를 참조하세요.
* 템플릿 내보내기에 대한 자세한 내용은 [빠른 시작: Azure Portal을 사용하여 ARM 템플릿 만들기 및 배포](quickstart-create-templates-use-the-portal.md)를 참조하세요.
* 일반적인 문의 사항에 대한 답변은 [ARM 템플릿에 대해 자주 묻는 질문](./frequently-asked-questions.yml)을 참조하세요.
