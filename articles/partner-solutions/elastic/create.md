---
title: Elastic 애플리케이션 만들기 - Azure 파트너 솔루션
description: 이 문서에서는 Azure Portal을 사용하여 Elastic 인스턴스를 만드는 방법을 설명합니다.
ms.service: partner-services
ms.topic: quickstart
ms.date: 09/02/2021
author: tfitzmac
ms.author: tomfitz
ms.custom: mode-other
ms.openlocfilehash: 3b718c5b138ceac49c6e1c2b36fc846e6946056a
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133066523"
---
# <a name="quickstart-get-started-with-elastic"></a>빠른 시작: Elastic 시작

이 빠른 시작에서는 Azure Portal을 사용하여 Elastic 인스턴스를 Azure 솔루션과 통합합니다.

## <a name="prerequisites"></a>사전 요구 사항

- 구독 소유자 - Azure와의 Elastic 통합은 Azure 구독에 대한 _소유자_ 액세스 권한이 있는 사용자만 만들 수 있습니다. 설정을 시작하기 전에 [적절한 액세스 권한이 있는지 확인](../../role-based-access-control/check-access.md)하세요.
- Single Sign-On 앱 - Azure Portal과 Elastic Cloud 사이를 자동으로 탐색하는 기능은 SSO(Single Sign-On)를 통해 사용하도록 설정됩다. 이 옵션은 모든 Azure 사용자에 대해 자동으로 사용하도록 설정되고 켜집니다. 

## <a name="find-offer"></a>제안 찾기

Azure Portal을 사용하여 Elastic 애플리케이션을 찾습니다.

1. 웹 브라우저에서 [Azure Portal](https://portal.azure.com/)로 이동한 후 로그인합니다.

1. 최근 세션에서 **Marketplace** 를 방문한 경우 사용 가능한 옵션에서 아이콘을 선택합니다. 그렇지 않으면 _Marketplace_ 를 검색합니다.

    :::image type="content" source="media/create/marketplace.png" alt-text="Marketplace 아이콘":::

1. _Elastic_ 을 검색하고,사용 가능한 제안에서 **Elasticsearch(Elastic Cloud)** 를 선택합니다.

1. **설정 + 구독** 을 선택합니다.

   :::image type="content" source="media/create/set-up.png" alt-text="제안 선택":::

## <a name="create-resource"></a>리소스 만들기

Elastic에 대한 제안이 선택되면 애플리케이션을 설정할 준비가 된 것입니다.

1. **Elastic 리소스 만들기** 기본 사항 페이지에서 다음 값을 제공합니다.

    :::image type="content" source="media/create/create-resource.png" alt-text="Elastic 리소스를 설정하기 위한 양식":::

    | 속성 | 설명 |
    | ---- | ---- |
    | **구독** | 드롭다운에서 소유자 액세스 권한이 있는 Azure 구독을 선택합니다. |
    | **리소스 그룹** | 새 리소스 그룹을 만들지, 아니면 기존 리소스 그룹을 사용할지 여부를 지정합니다. 리소스 그룹은 Azure 솔루션에 관련된 리소스를 보유하는 컨테이너입니다. 자세한 내용은 [Azure Resource Manager 개요](../../azure-resource-manager/management/overview.md)를 참조하세요. |
    | **Elastic 계정 이름** | 만들려는 Elastic 계정의 이름을 제공합니다. |
    | **지역** | 배포할 지역을 선택합니다. |
    | **요금제** | **종량제** 입니다. |
    | **가격** | 선택한 Elastic 요금제에 따라 지정됩니다. |

   완료되면 **다음: 로그 및 메트릭** 을 선택합니다.

1. **로그 및 메트릭** 에서 Elastic에 보낼 로그를 지정합니다.

    :::image type="content" source="media/create/configure-logs.png" alt-text="보낼 로그 선택":::

   Azure에서 Elastic으로 내보낼 수 있는 두 가지 유형의 로그가 있습니다.

   **구독 로그** 는 [관리 평면](../../azure-resource-manager/management/control-plane-and-data-plane.md)에서 구독에 있는 각 Azure 리소스의 작업에 대한 인사이트를 제공합니다. 로그는 Service Health 이벤트에 대한 업데이트도 제공합니다. 활동 로그를 사용하여 구독에 있는 리소스의 쓰기 작업(PUT, POST, DELETE)에 대한 대상, 주체 및 시기를 결정합니다. Azure 구독마다 활동 로그가 하나씩 있습니다.

   **Azure 리소스 로그** 는 [데이터 평면](../../azure-resource-manager/management/control-plane-and-data-plane.md) 내에서 수행되는 작업에 대한 인사이트를 제공합니다. 예를 들어 키 자격 증명 모음에서 비밀을 가져오거나 데이터베이스에 요청하는 것은 데이터 평면 활동입니다. 이러한 로그의 내용은 Azure 서비스와 리소스 종류에 따라 달라집니다. Azure 리소스 로그의 유형은 [Azure Monitor 리소스 로그 범주](../../azure-monitor/essentials/resource-logs-categories.md)에 나열되어 있습니다.

   로그를 Elastic에 보내는 Azure 리소스를 필터링하려면 리소스 태그를 사용합니다. 로그 보내기에 대한 태그 규칙은 다음과 같습니다.

   * 기본적으로 모든 리소스에 대한 로그가 수집됩니다. 
   * *포함* 태그가 있는 리소스는 로그를 Elastic에 보냅니다. 
   * *제외* 태그가 있는 리소스는 로그를 Elastic에 보내지 않습니다. 
   * 포함 규칙과 제외 규칙이 충돌하는 경우 제외 규칙이 우선 적용됩니다.
 
   새 Elastic 리소스에 대한 태그를 설정하려면 **다음: 태그** 를 선택합니다.

1. **태그** 에서 새 Elastic 리소스에 대한 사용자 지정 태그를 추가합니다. 각 태그는 이름과 값으로 구성됩니다. 태그가 추가되면 **다음: 검토 + 만들기** 를 선택하여 리소스 만들기의 마지막 단계로 이동합니다. 

   :::image type="content" source="media/create/add-tags.png" alt-text="Elastic 리소스에 태그 추가":::

1. **검토 + 만들기** 에서 구성의 유효성이 검사됩니다. 이전 양식에서 선택한 항목을 검토할 수 있습니다. 이 제안에 대한 약관을 검토할 수도 있습니다.

   :::image type="content" source="media/create/review-validation.png" alt-text="검토 및 유효성 검사 선택 항목":::

   유효성 검사에 성공하고 약관이 검토되었으면 **만들기** 를 선택합니다.

1. Azure에서 배포를 시작합니다.

   :::image type="content" source="media/create/deployment-in-progress.png" alt-text="배포 상태":::

1. 배포가 완료되면 **리소스로 이동** 을 선택하여 배포된 리소스를 확인합니다.

    :::image type="content" source="media/create/deployment-complete.png" alt-text="배포 상태 보기":::


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Elastic 리소스 관리](manage.md)
