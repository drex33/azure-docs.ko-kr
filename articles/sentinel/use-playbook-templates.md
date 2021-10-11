---
title: 기본 제공 템플릿에서 Azure 센티널 플레이 북 만들기 및 사용자 지정 | Microsoft Docs
description: 이 문서에서는 플레이 북 템플릿을 사용 하 여 플레이 북을 만들고 필요에 맞게 사용자 지정 하는 방법을 보여 줍니다.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/11/2021
ms.author: yelevin
ms.openlocfilehash: a2d9bdce1294a42487529c959f26eeee497d5046
ms.sourcegitcommit: af303268d0396c0887a21ec34c9f49106bb0c9c2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2021
ms.locfileid: "129760549"
---
# <a name="create-and-customize-azure-sentinel-playbooks-from-built-in-templates"></a>기본 제공 템플릿에서 Azure 센티널 플레이 북 만들기 및 사용자 지정

> [!IMPORTANT]
>
> **플레이 북 템플릿은** 현재 **미리 보기** 상태입니다. 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

플레이 북 템플릿은 요구 사항에 맞게 사용자 지정할 수 있는 미리 작성 되 고 테스트 되 고 사용이 가능한 워크플로입니다. 템플릿은 처음부터 플레이 북을 개발할 때 모범 사례에 대 한 참조 나 새로운 자동화 시나리오의 역할을 할 수도 있습니다.

플레이 북 템플릿은 플레이 북 (템플릿의 편집 가능한 복사본)를 만들 때까지 활성 플레이 북이 아닙니다.

전 세계의 보안 운영 센터에서 사용 되는 인기 있는 자동화 시나리오를 기반으로 하는 Azure 센티널 커뮤니티, Isv (독립 소프트웨어 공급 업체) 및 Microsoft 고유의 전문가 들이 많은 플레이 북 템플릿을 개발 했습니다.

다음 원본에서 플레이 북 템플릿을 가져올 수 있습니다.

- **플레이 북 템플릿** 탭 ( **Automation** 아래)은 Azure 센티널 커뮤니티에서 제공 하는 주요 시나리오를 제공 합니다. 동일한 템플릿에서 여러 활성 플레이 북을 만들 수 있습니다.

    새 버전의 템플릿이 게시 되 면 해당 템플릿에서 만든 활성 플레이 북 (플레이 **북** 탭)에 업데이트를 사용할 수 있다는 알림이 표시 됩니다.

- 플레이 북 템플릿은 특정 제품의 컨텍스트에서 [**Azure 센티널 솔루션**](sentinel-solutions.md) 의 일부로 가져올 수도 있습니다. 솔루션을 배포 하면 활성 플레이 북이 생성 됩니다.

- [**Azure 센티널 GitHub 리포지토리에**](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks) 는 많은 플레이 북 템플릿이 포함 되어 있습니다. Azure **에 배포** 단추를 선택 하 여 azure 구독에 배포할 수 있습니다. 

기술적으로 플레이 북 템플릿은 관련 된 각 연결에 대 한 Azure Logic Apps 워크플로 및 API 연결의 여러 리소스로 구성 된 [ARM (Azure Resource Manager) 템플릿입니다](../azure-resource-manager/templates/index.yml) . 

이 문서에서는 **Automation** 의 **플레이 북 템플릿** 탭에서 플레이 북 템플릿 배포에 대해 집중적으로 설명 합니다.

이 문서는 다음을 수행 하는 방법을 이해 하는 데 도움이 됩니다.

> [!div class="checklist"]
> * 기본 플레이 북 템플릿 탐색
> * 플레이 북 템플릿 배포

## <a name="explore-playbook-templates"></a>플레이 북 템플릿 탐색

Azure 센티널 탐색 메뉴에서 **자동화** 를 선택 하 고 **playbooks 템플릿** 탭을 선택 합니다.

여기에 표시 된 플레이 북 템플릿은 Soc가 사용 하거나 아이디어를 얻는 데 일반적으로 사용 되는 선행 자동화 시나리오를 보여줍니다. 이러한 플레이 북의 대부분은 azure 센티널 커뮤니티에서 제공 했으며 원래 azure 센티널 GitHub 리포지토리에 있습니다. 이러한 일부는 Azure 센티널 솔루션에 통합 되었습니다.

:::image type="content" source="media/use-playbook-templates/gallery.png" alt-text="플레이 북 갤러리의 스크린샷" lightbox="media/use-playbook-templates/gallery.png":::

요구 사항에 맞는 플레이 북 템플릿을 찾으려면 다음 기준으로 목록을 필터링 하면 됩니다.

- **트리거** 는 플레이 북가 인시던트 생성에 의해 트리거되고 (따라서 자동화 규칙에 연결 될 수 있음) 경고를 만든 후 (따라서 분석 규칙에 연결할 수 있음) 또는 다른 작업을 수행 함을 나타냅니다. [자세히 알아보기](playbook-triggers-actions.md#azure-sentinel-triggers-summary)

- **Logic Apps 커넥터** 는이 플레이 북가 상호 작용할 외부 서비스를 표시 합니다. 배포 프로세스 중에 각 커넥터는 외부 서비스를 인증 하는 id를 가정 해야 합니다.

- **엔터티에** 는 인시던트에 의해 명시적으로 필터링 되 고 구문 분석 되는 엔터티 형식이 플레이 북에서 표시 됩니다. 예를 들어 방화벽에 IP 주소를 차단 하도록 지시 하는 플레이 북는 무차별 암호 대입 공격 검색 규칙과 같은 IP 주소가 포함 된 경고를 생성 하는 분석 규칙에 의해 생성 된 인시던트에 대해 작동 합니다.

- **태그** 는 특정 시나리오와 관련 하 여 플레이 북에 적용 되는 레이블을 표시 하거나 특수 한 특성을 나타냅니다.
 
    예제:

    - **보강** -플레이 북는 다른 서비스에서 정보를 가져와 인시던트에 정보를 추가 합니다. 이 정보는 일반적으로 인시던트에 대 한 주석으로 추가 되거나 SOC에 전송 됩니다.

    - **수정** -플레이 북은 영향을 받는 엔터티에 대해 작업을 수행 하 여 잠재적인 위협을 제거 합니다.

    - **동기화** -플레이 북은 인시던트 관리 서비스와 같은 외부 서비스를 인시던트의 속성으로 업데이트 하는 데 도움이 됩니다.

    - **알림** -플레이 북에서 전자 메일 또는 메시지를 보냅니다.

    - **Teams 응답** -분석가가 대화형 카드를 사용 하 여 Teams에서 수동 작업을 수행할 수 있도록 합니다.

:::image type="content" source="media/use-playbook-templates/filters.png" alt-text="플레이 북 템플릿 목록 필터링":::

## <a name="customize-a-playbook-from-a-template"></a>템플릿에서 플레이 북 사용자 지정

이 절차에서는 플레이 북 템플릿을 배포 하는 방법을 설명 합니다.

이 프로세스를 반복 하 여 동일한 템플릿에서 여러 플레이 북을 만들 수 있습니다. 

1. **플레이 북 템플릿** 탭에서 플레이 북 이름을 선택 합니다.

1. 플레이 북에 필수 구성 요소가 있는 경우 지침을 따라야 합니다.

    - 일부 플레이 북은 다른 플레이 북를 작업으로 호출 합니다. 이 두 번째 플레이 북를 **중첩 된 플레이 북** 라고 합니다. 이 경우 필수 구성 요소 중 하나는 먼저 중첩 된 플레이 북를 배포 하는 것입니다.

    - 일부 플레이 북은 사용자 지정 Logic Apps 커넥터 또는 Azure 함수를 배포 해야 합니다. 이러한 경우 **Azure에 배포** 링크가 있습니다. <!-- WHERE? --> 그러면 일반 ARM 템플릿 배포 프로세스로 이동 합니다.

1. **플레이 북 만들기** 를 선택 하 여 선택한 템플릿에 따라 플레이 북 만들기 마법사를 엽니다. 마법사에는 네 개의 탭이 있습니다.

    - **기본 사항:** 새 플레이 북 (Logic Apps 리소스)를 찾고 이름을 지정 합니다 (기본값을 사용할 수 있음).
        :::image type="content" source="media/use-playbook-templates/basics.png" alt-text="플레이 북 만들기 마법사, 기본 사항 탭":::

    - **매개 변수:** 플레이 북에서 사용할 고객 관련 값을 입력 합니다. 예를 들어이 플레이 북가 SOC에 전자 메일을 보내는 경우 여기에서 받는 사람 주소를 정의할 수 있습니다. 플레이 북에 매개 변수가 있는 경우에만이 탭이 표시 됩니다.
    
        > [!NOTE]
        > 이 플레이 북에 사용 되는 사용자 지정 커넥터가 있는 경우 동일한 리소스 그룹에 배포 해야 하며,이 탭에서 이름을 삽입할 수 있습니다.

        :::image type="content" source="media/use-playbook-templates/parameters.png" alt-text="플레이 북 만들기 마법사, 매개 변수 탭":::

    - **연결:** 각 작업을 확장 하 여 이전 플레이 북에 대해 만든 기존 연결을 확인 합니다. [플레이 북에 대 한 연결 만들기](authenticate-playbooks-to-sentinel.md)에 대해 자세히 알아보세요.
    
        > [!NOTE]
        > 사용자 지정 커넥터의 경우 **매개 변수** 탭에 입력 한 사용자 지정 커넥터의 이름으로 연결이 표시 됩니다.

        :::image type="content" source="media/use-playbook-templates/connections.png" alt-text="플레이 북 만들기 마법사. 연결 탭":::

        항목이 없거나 새 항목을 만들려면 **배포 후에 새 연결 만들기** 를 선택 합니다. 이렇게 하면 배포 프로세스가 완료 된 후 Logic Apps 디자이너로 이동 합니다.

        **Azure 센티널** 와 같이 [관리 되는 id로 연결](authenticate-playbooks-to-sentinel.md#authenticate-with-managed-identity)을 지 원하는 커넥터의 경우 기본적으로 선택 되는 연결 방법입니다.

    - **검토 및 만들기:** 플레이 북을 만들기 전에 프로세스에 대 한 요약을 보고 입력 유효성 검사를 기다립니다.


1. 플레이 북 만들기 마법사의 단계를 완료 한 후에는 Logic Apps designer에서 새로운 플레이 북의 워크플로 디자인으로 이동 됩니다.

    :::image type="content" source="media/use-playbook-templates/designer.png" alt-text="Logic Apps 디자이너의 플레이 북를 참조 하세요.":::

1. 배포 후에에 대 한 새 연결을 만들도록 선택한 각 커넥터에 대해 다음을 수행 합니다.
    1. 탐색 메뉴에서 **API 연결** 을 선택 합니다.
    1. 연결 이름을 선택 합니다.
    1. 탐색 메뉴에서 **API 연결 편집** 을 선택 합니다.
    1. 필요한 매개 변수를 입력 하 고 **저장** 을 클릭 합니다.
        :::image type="content" source="media/use-playbook-templates/edit-api-connection.png" alt-text="P I 연결을 편집 하는 방법을 보여 주는 스크린샷":::
    
    또는 Logic Apps 디자이너의 관련 단계에서 새 연결을 만들 수 있습니다.
    1. 오류 기호와 함께 표시 되는 각 단계에 대해 선택 하 여 확장 합니다.
    1. **새로 추가** 를 선택합니다.
    1. 관련 지침에 따라 인증 합니다.
    1. 동일한 커넥터를 사용 하는 다른 단계가 있으면 해당 상자를 확장 합니다. 표시 되는 연결 목록에서 방금 만든 연결을 선택 합니다.

1. Azure 센티널 (또는 지원 되는 기타 연결)에 대해 관리 id 연결을 사용 하도록 선택한 경우 Azure 센티널 작업 영역에서 새 플레이 북에 대 한 사용 권한을 부여 하거나 다른 커넥터에 대 한 관련 대상 리소스에 권한을 부여 합니다.

1. 플레이 북을 저장 합니다. 이제 **활성 플레이 북** 탭에서 볼 수 있습니다.

1. 이 플레이 북를 실행 하려면  [자동화 된 응답을 설정](automate-responses-with-playbooks.md#set-an-automated-response) 하거나 [수동으로 실행](automate-responses-with-playbooks.md#run-a-playbook-manually-on-an-alert) 합니다 (경고 트리거만 해당).

1. 대부분의 템플릿은 그대로 사용할 수 있지만 SOC 요구에 맞게 새로운 플레이 북를 조정 하는 데 필요한 조정을 권장 합니다.

## <a name="troubleshooting"></a>문제 해결

### <a name="issue-found-a-bug-in-the-playbook"></a>문제: 플레이 북에서 버그가 발견 되었습니다.

버그를 보고 하거나 플레이 북에 대 한 개선을 요청 하려면 플레이 북의 세부 정보 창에서 **지원 되** 는 대상 링크를 선택 합니다. 커뮤니티 지원 플레이 북 경우 링크를 사용 하 여 GitHub 문제를 열 수 있습니다. 그렇지 않으면 서포터의 페이지로 이동 합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 플레이 북 템플릿을 사용 하 여 사용자의 요구에 맞게 플레이 북를 만들고 사용자 지정 하는 방법을 배웠습니다. Azure 센티널의 플레이 북 및 automation에 대해 자세히 알아보세요.

- [Azure Sentinel의 SOAR(보안 오케스트레이션, 자동화, 응답)](automation-in-azure-sentinel.md)
- [Azure Sentinel의 플레이북을 사용하여 위협 대응 자동화](automate-responses-with-playbooks.md)
- [자습서: Azure Sentinel에서 자동화 규칙으로 플레이북 사용](tutorial-respond-threats-playbook.md)
- [Azure Sentinel로 플레이북 인증](authenticate-playbooks-to-sentinel.md)
- [Azure Sentinel 플레이북에서 트리거 및 작업 사용](playbook-triggers-actions.md)
