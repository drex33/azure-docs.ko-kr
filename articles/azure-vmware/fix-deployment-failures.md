---
title: Azure VMware Solution 배포 또는 프로비저닝 실패에 대한 지원
description: Azure VMware Solution 프라이빗 클라우드에서 정보를 가져와서 Azure VMware Solution 배포 또는 프로비저닝 실패에 대한 서비스 요청을 제출합니다.
ms.topic: how-to
ms.date: 10/28/2020
ms.openlocfilehash: 58c7277d19a1b8a512eca0233548ebc388a2c459
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/27/2021
ms.locfileid: "114713157"
---
# <a name="open-a-support-request-for-an-azure-vmware-solution-deployment-or-provisioning-failure"></a>Azure VMware Solution 배포 또는 프로비저닝 실패에 대한 지원 요청 열기

이 문서에서는 [지원 요청](https://rc.portal.azure.com/#create/Microsoft.Support)을 열고 Azure VMware Solution 배포 또는 프로비저닝 실패에 대한 주요 정보를 제공하는 방법을 보여 줍니다. 

프라이빗 클라우드에서 실패가 발생한 경우 Azure Portal에서 지원 요청을 열어야 합니다. 지원 요청을 열려면 먼저 Azure Portal에서 몇 가지 주요 정보를 가져옵니다.

- 상관관계 ID
- Azure ExpressRoute 회로 ID
- 오류 메시지

## <a name="get-the-correlation-id"></a>상관 관계 ID 가져오기
 
Azure에서 프라이빗 클라우드 또는 리소스를 만드는 경우 리소스에 대한 상관 관계 ID가 리소스에 대해 자동으로 생성됩니다. 요청을 신속하게 열고 해결하려면 지원 요청에 프라이빗 클라우드 상관 관계 ID를 포함합니다.

Azure Portal에서 다음 두 가지 방법으로 리소스에 대한 상관 관계 ID를 가져올 수 있습니다.

* **개요** 창
* 배포 로그
 
 ### <a name="get-the-correlation-id-from-the-resource-overview"></a>리소스 개요에서 상관 관계 ID 가져오기

다음은 상관 관계 ID가 선택된 상태에서 실패한 프라이빗 클라우드 배포에 대한 작업 세부 정보의 예제입니다.

:::image type="content" source="media/fix-deployment-failures/failed-private-cloud-deployment.png" alt-text="상관 관계 ID가 선택된 실패한 프라이빗 클라우드 배포를 보여 주는 스크린샷":::

프라이빗 클라우드 **개요** 창에서 배포 결과에 액세스하려면 다음을 수행합니다.

1. Azure Portal에서 프라이빗 클라우드를 선택합니다.

1. 왼쪽 메뉴에서 **개요** 를 선택합니다.

배포가 시작된 후에는 배포 결과가 프라이빗 클라우드 **개요** 창에 표시됩니다.

서비스 요청에 포함할 프라이빗 클라우드 배포 상관 관계 ID를 복사하고 저장합니다.

### <a name="get-the-correlation-id-from-the-deployment-log"></a>배포 로그에서 상관 관계 ID 가져오기

Azure Portal에서 배포 활동 로그를 검색하여 실패한 배포에 대한 상관 관계 ID를 가져올 수 있습니다.

배포 로그에 액세스하려면 다음을 수행합니다.

1. Azure Portal에서 프라이빗 클라우드를 선택한 다음, 알림 아이콘을 선택합니다.

   :::image type="content" source="media/fix-deployment-failures/open-notifications.png" alt-text="Azure Portal에서 알림 아이콘을 보여 주는 스크린샷":::

1. **알림** 창의 **활동 로그의 더 많은 이벤트** 를 선택합니다.

    :::image type="content" source="media/fix-deployment-failures/more-events-in-activity-log.png" alt-text="알림 창에서 선택한 활동 로그 링크의 더 많은 이벤트를 보여 주는 스크린샷":::

1. 실패한 배포와 해당 상관 관계 ID를 찾으려면 리소스 이름 또는 리소스를 만드는 데 사용한 기타 정보를 검색합니다. 

    다음 예제에서는 pc03이라는 프라이빗 클라우드 리소스에 대한 검색 결과를 보여 줍니다.
 
    :::image type="content" source="media/fix-deployment-failures/find-past-deployments.png" alt-text="예제 프라이빗 클라우드 리소스 및 PrivateCloud 만들기 또는 업데이트 창에 대한 검색 결과를 보여 주는 스크린샷":::
 
1. **활동 로그** 창의 검색 결과에서 실패한 배포의 작업 이름을 선택합니다.

1. **PrivateCloud 만들기 또는 업데이트** 창에서 **JSON** 탭을 선택한 다음, 표시된 로그에서 `correlationId`를 찾습니다. `correlationId` 값을 복사하여 지원 요청에 포함합니다. 
 
## <a name="copy-error-messages"></a>오류 메시지 복사

배포 문제를 해결하려면 Azure Portal에 표시되는 모든 오류 메시지를 포함합니다. 오류 요약을 보려면 경고 메시지를 선택합니다.
 
:::image type="content" source="media/fix-deployment-failures/summary-of-errors.png" alt-text="복사 아이콘이 선택된 상태에서 오류 창의 요약 탭에 오류 세부 정보를 표시하는 스크린샷":::

오류 메시지를 복사하려면 복사 아이콘을 선택합니다. 복사된 메시지를 저장하여 지원 요청에 포함합니다.
 
## <a name="get-the-expressroute-id-uri"></a>ExpressRoute ID(URI) 가져오기
 
프라이빗 클라우드 ExpressRoute 회로를 사용하여 기존 프라이빗 클라우드를 스케일링하거나 피어링하려고 시도했지만 실패했을 수 있습니다. 이 시나리오에서는 지원 요청에 포함할 ExpressRoute ID가 필요합니다.

ExpressRoute ID를 복사하려면 다음을 수행합니다.

1. Azure Portal에서 프라이빗 클라우드를 선택합니다.
1. 왼쪽 메뉴의 **관리** 아래에서 **연결** 을 선택합니다. 
1. 오른쪽 창에서 **ExpressRoute** 탭을 선택합니다.
1. **ExpressRoute ID** 에 대한 복사 아이콘을 선택하고 지원 요청에 사용할 값을 저장합니다.
 
:::image type="content" source="media/expressroute-global-reach/expressroute-id.png" alt-text="클립보드에 복사할 ExpressRoute ID를 보여주는 스크린샷."::: 
 
## <a name="pre-validation-failures"></a>사전 유효성 검사 실패

배포 전에 프라이빗 클라우드 사전 유효성 검사에 실패하면 상관 관계 ID가 생성되지 않습니다. 이 시나리오에서는 지원 요청에 다음 정보를 제공할 수 있습니다.

- 오류 및 실패 메시지. 오류 및 실패 메시지는 할당량 관련 문제의 경우와 같이 많은 실패에 유용할 수 있습니다. 이 문서에 설명된 대로 오류 및 실패 메시지를 복사하여 지원 요청에 포함하는 것이 중요합니다.
- Azure VMware Solution 프라이빗 클라우드를 만드는 데 사용한 정보에는 다음이 포함됩니다.
  - 위치
  - Resource group
  - 리소스 이름

## <a name="create-your-support-request"></a>지원 요청 만들기

지원 요청을 만드는 방법에 대한 일반적인 내용은 [Azure 지원 요청을 만드는 방법](../azure-portal/supportability/how-to-create-azure-support-request.md)을 참조하세요. 

Azure VMware Solution 배포 또는 프로비저닝 실패에 대한 지원 요청을 만들려면 다음을 수행합니다.

1. Azure Portal에서 **도움말** 아이콘을 선택하고 **새 지원 요청** 을 선택합니다.

    :::image type="content" source="media/fix-deployment-failures/open-support-request.png" alt-text="Azure Portal의 새 지원 요청 창의 스크린샷":::

1. 필요한 정보를 입력하거나 선택합니다.

   1. **기본 사항** 탭에서 다음을 수행합니다.

      1. **문제 유형** 에서 **구성 및 설정 문제** 를 선택합니다.

      1. **문제 하위 유형** 에서 **프라이빗 클라우드 프로비저닝** 을 선택합니다.

   1. **세부 정보** 탭에서 다음을 수행합니다.

      1. 필요한 정보를 입력하거나 선택합니다.

      1. 이 정보가 요청된 위치에 상관 관계 ID 또는 ExpressRoute ID를 붙여넣습니다. 해당 값에 대한 특정 텍스트 상자가 표시되지 않는 경우 **문제에 대한 세부 정보 제공** 텍스트 상자에 붙여넣습니다.

    1. **문제에 대한 세부 정보 제공** 텍스트 상자에서 복사한 오류 또는 실패 메시지를 포함한 오류 정보를 붙여넣습니다.

1. 항목을 검토한 다음, **만들기** 를 선택하여 지원 요청을 만듭니다.
