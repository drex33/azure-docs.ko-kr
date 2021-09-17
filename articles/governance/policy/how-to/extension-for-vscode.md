---
title: Visual Studio Code용 Azure Policy 확장
description: Visual Studio Code용 Azure Policy 확장을 사용하여 Azure Resource Manager 별칭을 조회하는 방법에 대해 알아봅니다.
ms.date: 09/01/2021
ms.topic: how-to
ms.openlocfilehash: 93b59114c6a89e9219389341d541d7850a90ccc7
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123433695"
---
# <a name="use-azure-policy-extension-for-visual-studio-code"></a>Visual Studio Code용 Azure Policy 확장 사용

> Azure Policy 확장 버전 **0.1.2** 이상에 적용됩니다.

Visual Studio Code용 Azure Policy 확장을 사용하여 [별칭](../concepts/definition-structure.md#aliases)을 조회하고, 리소스 및 정책 정의를 검토하며, 개체를 내보내고, 정책 정의를 평가하는 방법을 알아봅니다. 먼저 Visual Studio Code에서 Azure Policy 확장을 설치하는 방법을 설명합니다. 그런 다음 별칭을 조회하는 방법을 안내합니다.

Visual Studio Code용 Azure Policy 확장은 Linux, Mac 및 Windows에 설치할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 문서의 단계를 완료하려면 다음 항목이 필요합니다.

- Azure 구독 Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
- [Visual Studio Code](https://code.visualstudio.com)

## <a name="install-and-configure-the-azure-policy-extension"></a>Azure Policy 확장 설치 및 구성

사전 요구 사항을 충족하면 다음 단계에 따라 Visual Studio Code용 Azure Policy 확장을 설치할 수 있습니다.

1. Visual Studio Code를 엽니다.
1. 메뉴 모음에서 **보기** > **확장** 으로 이동합니다.
1. 검색 상자에 **Azure Policy** 를 입력합니다.
1. 검색 결과에서 **Azure Policy** 를 선택한 다음, **설치** 를 선택합니다.
1. 필요한 경우 **다시 로드** 를 선택합니다.

국가 클라우드 사용자의 경우, 다음 단계에 따라 Azure 환경을 먼저 설정합니다.

1. **파일\기본 설정\설정** 을 선택합니다.
1. 문자열 _Azure: Cloud_ 을 검색합니다.
1. 다음 목록에서 국가 클라우드를 선택합니다.

   :::image type="content" source="../media/extension-for-vscode/set-default-azure-cloud-sign-in.png" alt-text="Visual Studio Code용 국가 Azure 클라우드 로그인을 선택하는 스크린샷" border="false":::

## <a name="using-the-policy-extension"></a>Policy 확장 사용

> [!NOTE]
> Visual Studio Code용 Azure Policy 확장에 표시된 정책 정의에서 로컬로 변경한 내용은 Azure와 동기화되지 않습니다.

### <a name="connect-to-an-azure-account"></a>Azure 계정에 연결

리소스 및 조회 별칭을 평가하려면 Azure 계정에 연결해야 합니다. Visual Studio Code에서 Azure에 연결하려면 다음 단계를 따르세요.

1. Azure Policy 확장 또는 명령 팔레트에서 Azure에 로그인합니다.

   - Azure Policy 확장

     Azure Policy 확장에서 **Azure에 로그인** 을 선택합니다.

     :::image type="content" source="../media/extension-for-vscode/azure-cloud-sign-in-policy-extension.png" alt-text="Visual Studio Code 및 Azure Policy 확장의 아이콘 스크린샷." border="false":::

   - 명령 팔레트

     메뉴 모음에서 **보기** > **명령 팔레트** 로 이동한 다음, **Azure: 로그인** 을 입력합니다.

     :::image type="content" source="../media/extension-for-vscode/azure-cloud-sign-in-command-palette.png" alt-text="명령 팔레트의 Visual Studio Code에 대한 Azure cloud 로그인 옵션의 스크린샷" border="false":::.

1. 로그인 지침에 따라 Azure에 로그인합니다. 연결되면 Visual Studio Code 창 아래의 상태 표시줄에 Azure 계정 이름이 표시됩니다.

### <a name="select-subscriptions"></a>구독 선택

처음 로그인하면 Azure Policy 확장에 의해 기본 구독 리소스 및 정책 정의만 로드됩니다. 리소스 및 정책 정의 표시에서 구독을 추가하거나 제거하려면 다음 단계를 수행합니다.

1. 명령 팔레트 또는 창 바닥글에서 구독 명령을 시작합니다.

   - 명령 팔레트:

     메뉴 모음에서 **보기** > **명령 팔레트** 로 이동하고, **Azure: 구독 선택** 을 입력합니다.

   - 창 바닥글

     화면 하단의 창 바닥글에서 **Azure: \<your account\>** 와 일치하는 세그먼트를 선택합니다.

1. 필터 상자를 사용하여 이름을 기준으로 신속 하 게 구독을 찾습니다. 그런 다음 각 구독에서 확인란의 표시 여부로 Azure Policy 확장에 표시된 구독을 설정합니다. 표시할 구독을 추가하거나 제거하는 작업이 완료되면, **확인** 을 선택합니다.

### <a name="search-for-and-view-resources"></a>리소스 검색 및 보기

Azure Policy 확장에는 리소스 공급자 및 리소스 그룹에서 선택한 구독의 리소스가 **리소스** 창에 나열됩니다. 트리뷰에는 선택한 구독 내에서 또는 구독 수준에서 다음과 같은 리소스 그룹이 포함됩니다.

- **리소스 공급자**
  - 정책 별칭이 있는 리소스 및 관련 하위 리소스를 사용하여 등록된 각 리소스 공급자
- **리소스 그룹**
  - 소속된 리소스 그룹의 모든 리소스

기본값으로 확장은 기존 리소스와 정책 별칭이 있는 리소스를 기준으로 '리소스 공급자' 부분을 필터링합니다. 필터링하지 않고 모든 리소스 공급자를 보려면,**Settings** > **Extensions** > **Azure Policy** 에서 이 동작을 변경합니다.

단일 구독에서 수백 또는 수천 개의 리소스를 사용하는 고객은 리소스를 찾기 위해 검색 가능한 방법을 선호할 수 있습니다. Azure Policy 확장을 사용하면 다음 단계를 통해 특정 리소스를 검색할 수 있습니다.

1. Azure Policy 확장 또는 명령 팔레트에서 검색 인터페이스를 시작합니다.

   - Azure Policy 확장

     Azure Policy 확장에서 **리소스** 패널 위로 마우스를 이동하여 줄임표를 선택한 다음, **리소스 검색** 을 선택합니다.

   - 명령 팔레트:

     메뉴 모음에서 **보기** > **명령 팔레트** 로 이동하고, **Azure Policy: 리소스 검색** 을 입력합니다.

1. 표시하기 위해 둘 이상의 구독을 선택하는 경우, 필터를 사용하여 검색할 구독을 선택합니다.

1. 필터를 사용하여 이전에 선택한 구독의 일부인 검색할 리소스 그룹을 선택합니다.

1. 필터를 사용하여 표시할 리소스를 선택합니다. 필터는 리소스 이름과 리소스 유형 모두에 대해 작동합니다.

### <a name="discover-aliases-for-resource-properties"></a>별칭의 리소스 속성 검색

검색 인터페이스를 통하거나 트리뷰에서 직접 리소스를 선택하는 경우, Azure Policy 확장은 해당 리소스 및 그 모든 Azure Resource Manager 속성 값을 나타내는 JSON 파일을 엽니다.

리소스가 열린 후, Resource Manager 속성 이름 또는 값을 마우스로 가리키면 Azure Policy 별칭이 표시됩니다(있는 경우). 이 예제의 리소스는 `Microsoft.Compute/virtualMachines` 리소스 형식이 고, **properties.storageProfile.imageReference.offer** 속성이 가리켜집니다. 가리키기는 일치하는 별칭을 표시합니다.

:::image type="content" source="../media/extension-for-vscode/extension-hover-shows-property-alias.png" alt-text="별칭 이름을 표시하는 속성을 가리키는 Visual Studio Code용 Azure Policy 확장의 스크린샷" border="false":::

> [!NOTE]
> VS Code 확장은 Resource Manager 모드 속성의 평가만 지원합니다. 이 모드에 대한 자세한 내용은 [모드 정의](../concepts/definition-structure.md#mode)를 참조하세요.

### <a name="search-for-and-view-policy-definitions-and-assignments"></a>정책 정의와 할당 검색 및 보기

Azure Policy 확장에는 **정책** 창에 표시하기 위해 선택한 구독에 대한 트리뷰로 정책 형식 및 정책 할당이 나열됩니다. 단일 구독에서 수백 또는 수천 개의 정책 정의나 할당을 사용하는 고객은 해당 정책 정의 또는 할당을 찾기 위해 검색 가능한 방법을 선호할 수 있습니다. Azure Policy 확장을 사용하면 다음 단계를 통해 특정 정책 또는 할당을 검색할 수 있습니다.

1. Azure Policy 확장 또는 명령 팔레트에서 검색 인터페이스를 시작합니다.

   - Azure Policy 확장

     Azure Policy 확장에서 **정책** 패널을 마우스로 가리켜서 줄임표를 선택한 다음, **정책 검색** 을 선택합니다.

   - 명령 팔레트:

     메뉴 모음에서 **보기** > **명령 팔레트** 로 이동하고, **Azure Policy: 정책 검색** 을 입력합니다.

1. 표시하기 위해 둘 이상의 구독을 선택하는 경우, 필터를 사용하여 검색할 구독을 선택합니다.

1. 필터를 사용하여 이전에 선택한 구독의 일부인 검색할 정책 유형 또는 할당을 선택합니다.

1. 필터를 사용하여 표시할 정책을 선택합니다. 필터는 정책 정의 또는 정책 할당의 _displayName_ 에 대해 작동합니다.

검색 인터페이스를 통해 또는 트리뷰에서 이를 선택하여 정책 또는 할당을 선택하는 경우, Azure Policy 확장은 정책 또는 할당과 그 모든 Resource Manager 속성 값을 나타내는 JSON을 엽니다. 확장은 열린 Azure Policy JSON 스키마의 유효성을 검사할 수 있습니다.

### <a name="export-objects"></a>개체 내보내기

구독의 개체를 로컬 JSON 파일로 내보낼 수 있습니다. **리소스** 또는 **정책** 창에서 내보낼 수 있는 개체를 가리키거나 그 개체를 선택합니다. 강조 표시된 행의 끝에서 저장 아이콘을 선택하고 해당 리소스 JSON을 저장할 폴더를 선택합니다.

다음 개체는 로컬로 내보낼 수 있습니다.

- 리소스 창
  - 리소스 그룹
  - 개별 리소스(리소스 그룹 소속 또는 리소스 공급자 아래에 있는)
- 정책 창
  - 정책 할당
  - 기본 제공 정책 정의
  - 사용자 지정 정책 정의
  - Initiatives

### <a name="on-demand-evaluation-scan"></a>주문형 평가 검사

평가 검사는 Visual Studio Code용 Azure Policy 확장을 사용하여 시작할 수 있습니다. 평가를 시작하려면, 리소스, 정책 정의, 정책 할당 등의 각 개체를 선택하고 고정합니다.

1. 각 개체를 고정하려면 **리소스** 창이나 **정책** 창에서 찾아서 편집 탭 아이콘의 핀을 선택합니다. 개체를 고정하면 확장의 **평가** 창에 추가됩니다.
1. **평가** 창에서 각 개체 중 하나를 선택하고, 평가 아이콘 선택을 사용하여 그것을 평가에 포함된 것으로 표시합니다.
1. **평가** 창 상단에서 실행 평가 아이콘을 선택합니다. 평가 결과 세부 정보가 JSON 형식으로 Visual Studio Code 새 창에서 열립니다.

> [!NOTE]
> [AuditIfNotExists](../concepts/effects.md#auditifnotexists) 또는 [DeployIfNotExists](../concepts/effects.md#deployifnotexists) 정책 정의의 경우, **평가 창** 의 더하기 아이콘이나 명령 팔레트에서 **Azure Policy: 존재 확인을 위한 리소스 선택(없는 정책에 대해서만 사용)** 을 통해 존재 검사를 위한 _관련_ 리소스를 선택합니다.

평가 결과에는 정책 정의 및 정책 할당에 대한 정보와 함께 **policyEvaluations.evaluationResult** 속성도 제공됩니다. 출력은 다음 예제와 같이 표시됩니다.

```json
{
    "policyEvaluations": [
        {
            "policyInfo": {
                ...
            },
            "evaluationResult": "Compliant",
            "effectDetails": {
                "policyEffect": "Audit",
                "existenceScope": "None"
            }
        }
    ]
}
```

> [!NOTE]
> VS Code 확장은 Resource Manager 모드 속성의 평가만 지원합니다. 이 모드에 대한 자세한 내용은 [모드 정의](../concepts/definition-structure.md#mode)를 참조하세요.
>
> 평가 기능은 macOS 및 Linux 확장 설치에서 작동하지 않습니다.

### <a name="create-policy-definition-from-constraint-template"></a>제약 조건 템플릿에서 정책 정의 만들기

VS Code 확장은 기존 opa ( [Open policy Agent](https://www.openpolicyagent.org/) ) 게이트 키퍼 v3 [제약 조건 템플릿에서](https://open-policy-agent.github.io/gatekeeper/website/docs/howto/#constraint-templates)정책 정의를 만들 수 있습니다. 명령 팔레트를 옵션으로 사용할 VS Code에서 yaml 파일을 열어야 합니다.

1. 유효한 OPA 게이트 키퍼 v3 제약 조건 템플릿 YAML 파일을 엽니다.

1. 메뉴 모음에서 **보기** > **명령 팔레트** 로 이동 하 고 **Kubernetes: 제약 조건 템플릿에서 정책 정의 만들기에 Azure Policy** 을 입력 합니다.

1. 적절 한 _sourceType_ 값을 선택 합니다.

1. `/* EDIT HERE */`정책 정의 JSON의 부분을 입력 합니다.

확장은 정책 정의의 JSON을 생성 하지만 Azure에서 정의를 만들지는 않습니다. 적절 한 "여기에서 편집" 필드를 입력 한 후에는 완료 된 정책 정의 JSON 및 Azure Portal 또는 지원 되는 SDK를 사용 하 여 Azure 환경 내에서 정책 정의를 만듭니다.

### <a name="sign-out"></a>로그아웃

메뉴 모음에서 **보기** > **명령 팔레트** 로 이동한 다음, **Azure: 로그아웃** 을 입력합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Policy 샘플](../samples/index.md)에서 예제를 검토합니다.
- [Azure Policy 정의 구조](../concepts/definition-structure.md)를 검토합니다.
- [정책 효과 이해](../concepts/effects.md)를 검토합니다.
- [프로그래밍 방식으로 정책 정의를 만드는](programmatically-create.md) 방법을 이해합니다.
- [규정 비준수 리소스를 수정](remediate-resources.md)하는 방법을 알아봅니다.
- [Azure 관리 그룹으로 리소스 구성](../../management-groups/overview.md)을 포함하는 관리 그룹을 검토합니다.
