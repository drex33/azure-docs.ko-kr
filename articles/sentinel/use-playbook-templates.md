---
title: 기본 제공 템플릿 | Microsoft Sentinel 플레이북 만들기 및 사용자 지정 Microsoft Docs
description: 이 문서에서는 플레이북을 만들고 플레이북 템플릿을 사용하여 요구에 맞게 사용자 지정하는 방법을 보여 줍니다.
author: yelevin
ms.topic: how-to
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: f38cb4fa485a8ef5d976a4b61e786d832120ccbb
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132751537"
---
# <a name="create-and-customize-microsoft-sentinel-playbooks-from-built-in-templates"></a>기본 제공 템플릿에서 Microsoft Sentinel 플레이북 만들기 및 사용자 지정

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
>
> **플레이북 템플릿은** 현재 **미리 보기로** 제공됩니다. 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

플레이북 템플릿은 요구 사항에 맞게 사용자 지정할 수 있는 미리 빌드되고 테스트되고 즉시 사용할 수 있는 워크플로입니다. 템플릿은 플레이북을 처음부터 개발할 때 모범 사례에 대한 참조로 사용하거나 새로운 자동화 시나리오에 대한 영감을 제공할 수도 있습니다.

플레이북 템플릿은 플레이북(템플릿의 편집 가능한 복사본)을 만들 때까지 활성 플레이북 자체가 아닙니다.

많은 플레이북 템플릿은 전 세계 보안 운영 센터에서 사용되는 인기 있는 자동화 시나리오를 기반으로 Microsoft Sentinel 커뮤니티, ISV(독립 소프트웨어 공급업체) 및 Microsoft 자체 전문가에 의해 개발되었습니다.

다음 원본에서 플레이북 템플릿을 얻을 수 있습니다.

- **플레이북 템플릿** 탭(Automation 아래)은 Microsoft Sentinel 커뮤니티에서 제공하는 주요 시나리오를 제공합니다. 동일한 템플릿에서 여러 활성 플레이북을 만들 수 있습니다.

    새 버전의 템플릿이 게시되면 해당 템플릿에서 만든 활성 **플레이북(플레이북** 탭)에 업데이트를 사용할 수 있다는 알림이 레이블로 지정됩니다.

- 플레이북 템플릿은 특정 제품의 컨텍스트에서 [Microsoft Sentinel 솔루션의](sentinel-solutions.md) 일부로 가져올 수도 있습니다. 솔루션을 배포하면 활성 플레이북이 생성됩니다.

- [Microsoft Sentinel GitHub 리포지토리에는](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks) 많은 플레이북 템플릿이 포함되어 있습니다. Azure에 배포 단추를 선택하여 Azure 구독에 **배포할** 수 있습니다.

기술적으로 플레이북 템플릿은 관련된 각 연결에 대한 Azure Logic Apps 워크플로 및 API 연결과 같은 여러 리소스로 구성된 [ARM(Azure Resource Manager) 템플릿입니다.](../azure-resource-manager/templates/index.yml)

이 문서에서는 **Automation** 아래의 플레이북 템플릿 탭에서 **플레이북 템플릿을 배포하는** 데 중점을 둡니다.

이 문서는 다음 방법을 이해하는 데 도움이 됩니다.

> [!div class="checklist"]
> - 첫 번째 플레이북 템플릿 살펴보기
> - 플레이북 템플릿 배포

## <a name="explore-playbook-templates"></a>플레이북 템플릿 살펴보기

Microsoft Sentinel 탐색 메뉴에서 **Automation을** 선택한 **다음, 플레이북 템플릿 탭을** 선택합니다.

여기에 표시된 플레이북 템플릿은 SOC가 사용하거나 아이디어를 얻는 경향이 있는 주요 자동화 시나리오를 보여줍니다. 이러한 플레이북의 대부분은 Microsoft Sentinel 커뮤니티에서 제공했으며 원래 Microsoft Sentinel GitHub 리포지토리에 있었습니다. 이러한 솔루션 중 일부는 Microsoft Sentinel 솔루션에 통합되었습니다.

:::image type="content" source="media/use-playbook-templates/gallery.png" alt-text="플레이북 갤러리의 스크린샷." lightbox="media/use-playbook-templates/gallery.png":::

요구 사항에 맞는 플레이북 템플릿을 찾으려면 다음 기준에 따라 목록을 필터링할 수 있습니다.

- **트리거는** 플레이북이 인시던트 생성(따라서 자동화 규칙에 연결될 수 있음), 경고 생성(따라서 분석 규칙에 연결할 수 있음) 또는 다른 것에 의해 트리거되었음을 나타냅니다. [자세한 정보](playbook-triggers-actions.md#microsoft-sentinel-triggers-summary)

- **Logic Apps 커넥터는** 이 플레이북이 상호 작용할 외부 서비스를 보여줍니다. 배포 프로세스 중에 각 커넥터는 외부 서비스에 인증할 ID를 가정해야 합니다.

- **엔터티는** 인시던트에서 해당 엔터티 형식을 찾을 것으로 예상되는 플레이북에 의해 명시적으로 필터링되고 구문 분석된 엔터티 형식을 표시합니다. 예를 들어 IP 주소를 차단하도록 방화벽에 지시하는 플레이북은 무차별 암호 대입 공격 검색 규칙과 같이 IP 주소를 포함하는 경고를 생성하는 분석 규칙에서 만든 인시던트에 대해 작동할 것으로 예상됩니다.

- **태그는** 플레이북에 적용된 레이블을 표시하여 특정 시나리오와 관련되거나 특수한 특징을 나타냅니다.

  예제:

  - **보강** - 플레이북은 다른 서비스에서 정보를 가져와 인시던트 정보를 추가합니다. 이 정보는 일반적으로 인시던트 주석으로 추가되거나 SOC로 전송됩니다.

  - **수정** - 플레이북은 잠재적인 위협을 제거하기 위해 영향을 받는 엔터티에 대한 작업을 수행합니다.

  - **동기화** - 플레이북은 인시던트 관리 서비스와 같은 외부 서비스를 인시던트의 속성으로 업데이트하는 데 도움이 됩니다.

  - **알림** - 플레이북이 이메일 또는 메시지를 보냅니다.

  - **Teams 응답** - 플레이북을 사용하면 분석가가 대화형 카드를 사용하여 Teams 수동 작업을 할 수 있습니다.

:::image type="content" source="media/use-playbook-templates/filters.png" alt-text="플레이북 템플릿 목록 필터링":::

## <a name="customize-a-playbook-from-a-template"></a>템플릿에서 플레이북 사용자 지정

이 절차에서는 플레이북 템플릿을 배포하는 방법을 설명합니다.

이 프로세스를 반복하여 동일한 템플릿에 여러 플레이북을 만들 수 있습니다.

1. **플레이북 템플릿** 탭에서 플레이북 이름을 선택합니다.

1. 플레이북에 필수 구성 조건이 있는 경우 지침을 따라야 합니다.

    - 일부 플레이북은 다른 플레이북을 작업으로 호출합니다. 이 두 번째 플레이북을 **중첩 플레이북이라고** 합니다. 이러한 경우 먼저 중첩된 플레이북을 배포해야 합니다.

    - 일부 플레이북에서는 사용자 지정 Logic Apps 커넥터 또는 Azure Function을 배포해야 합니다. 이러한 경우 **Azure에 배포** 링크가 있습니다. <!-- WHERE? --> 일반 ARM 템플릿 배포 프로세스로 가는 입니다.

1. **플레이북 만들기를** 선택하여 선택한 템플릿에 따라 플레이북 만들기 마법사를 엽니다. 마법사에는 다음과 같은 4개의 탭이 있습니다.

    - **기초:** 새 플레이북(Logic Apps 리소스)을 찾아 이름을 지정합니다(기본값 사용 가능).
        :::image type="content" source="media/use-playbook-templates/basics.png" alt-text="플레이북 만들기 마법사, 기본 사항 탭":::

    - **매개 변수:** 플레이북에서 사용할 고객별 값을 입력합니다. 예를 들어 이 플레이북이 SOC에 이메일을 보내는 경우 여기에서 수신자 주소를 정의할 수 있습니다. 이 탭은 플레이북에 매개 변수가 있는 경우에만 표시됩니다.

        > [!NOTE]
        > 이 플레이북에 사용 중 사용자 지정 커넥터가 있는 경우 동일한 리소스 그룹에 배포해야 하며, 이 탭에 해당 이름을 삽입할 수 있습니다.

        :::image type="content" source="media/use-playbook-templates/parameters.png" alt-text="플레이북 만들기 마법사, 매개 변수 탭":::

    - **연결:** 각 작업을 확장하여 이전 플레이북에 대해 만든 기존 연결을 확인합니다. [플레이북에 대한 연결을 만드는](authenticate-playbooks-to-sentinel.md)방법에 대해 자세히 알아보세요.

        > [!NOTE]
        > 사용자 지정 커넥터의 경우 **매개 변수** 탭에 입력한 사용자 지정 커넥터의 이름으로 연결이 표시됩니다.

        :::image type="content" source="media/use-playbook-templates/connections.png" alt-text="플레이북 만들기 마법사. 연결 탭":::

        없는 경우 또는 새 연결을 만들려면 배포 후 **새 연결 만들기를** 선택합니다. 그러면 배포 프로세스가 완료된 후 Logic Apps 디자이너로 이동됩니다.

        **Microsoft Sentinel과** 같이 [관리 ID와의 연결을](authenticate-playbooks-to-sentinel.md#authenticate-with-managed-identity)지원하는 커넥터의 경우 기본적으로 선택되는 연결 방법이 됩니다.

    - **검토 및 만들기:** 플레이북을 만들기 전에 프로세스의 요약을 보고 입력의 유효성 검사를 기다립니다.

1. 플레이북 만들기 마법사의 단계를 끝으로 수행하면 Logic Apps 디자이너에서 새 플레이북의 워크플로 디자인으로 이동됩니다.

    :::image type="content" source="media/use-playbook-templates/designer.png" alt-text="Logic Apps 디자이너에서 플레이북을 참조하세요.":::

1. 배포 후 새 연결을 만들도록 선택한 각 커넥터에 대해 다음을 수행합니다.
    1. 탐색 메뉴에서 **API 연결 을** 선택합니다.
    1. 연결 이름을 선택합니다.
    1. 탐색 메뉴에서 **API 연결 편집을** 선택합니다.
    1. 필요한 매개 변수를 입력하고 **저장을** 클릭합니다.
        :::image type="content" source="media/use-playbook-templates/edit-api-connection.png" alt-text="A P I 연결을 편집하는 방법을 보여주는 스크린샷.":::

    또는 Logic Apps 디자이너의 관련 단계 내에서 새 연결을 만들 수 있습니다.
    1. 오류 기호와 함께 표시되는 각 단계에 대해 이를 선택하여 확장합니다.
    1. **새로 추가** 를 선택합니다.
    1. 관련 지침에 따라 인증합니다.
    1. 이 동일한 커넥터를 사용하는 다른 단계가 있는 경우 해당 상자를 확장합니다. 표시되는 연결 목록에서 방금 만든 연결을 선택합니다.

1. Microsoft Sentinel(또는 지원되는 다른 연결)에 관리 ID 연결을 사용하도록 선택한 경우 Microsoft Sentinel 작업 영역(또는 다른 커넥터의 관련 대상 리소스)에서 새 플레이북에 권한을 부여합니다.

1. 플레이북을 저장합니다. 이제 **활성 플레이북** 탭에서 볼 수 있습니다.

1. 이 플레이북을 실행하려면  [자동화된 응답을 설정하거나](automate-responses-with-playbooks.md#set-an-automated-response) [수동으로 실행합니다(경고](automate-responses-with-playbooks.md#run-a-playbook-manually-on-an-alert) 트리거에만 해당).

1. 대부분의 템플릿은 있는 그대로 사용할 수 있지만 SOC 요구 사항에 맞게 새 플레이북에 맞게 조정하는 것이 좋습니다.

## <a name="troubleshooting"></a>문제 해결

### <a name="issue-found-a-bug-in-the-playbook"></a>문제: 플레이북에서 버그가 발견되었습니다.

버그를 보고하거나 플레이북에 대한 개선 사항을 요청하려면 플레이북의 세부 정보 창에서 **지원됨** 링크를 선택합니다. 커뮤니티에서 지원하는 플레이북인 경우 링크를 통해 GitHub 문제를 열 수 있습니다. 그렇지 않은 경우, 다음 페이지로 안내됩니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 플레이북 템플릿을 사용하여 요구에 맞게 플레이북을 만들고 사용자 지정하는 방법을 배웠습니다. Microsoft Sentinel의 플레이북 및 자동화에 대해 자세히 알아보세요.

- [Microsoft Sentinel의 SOAR(보안 오케스트레이션, 자동화 및 응답)](automation.md)
- [Microsoft Sentinel에서 플레이북을 사용하여 위협 대응 자동화](automate-responses-with-playbooks.md)
- [자습서: Microsoft Sentinel에서 자동화 규칙으로 플레이북 사용](tutorial-respond-threats-playbook.md)
- [Microsoft Sentinel에 플레이북 인증](authenticate-playbooks-to-sentinel.md)
- [Microsoft Sentinel 플레이북에서 트리거 및 작업 사용](playbook-triggers-actions.md)
