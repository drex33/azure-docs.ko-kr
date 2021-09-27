---
title: 랩의 할당량 및 제한 크기 조정
description: 이 문서에서는 Azure DevTest Labs에서 랩을 스케일링할 수 있는 방법을 설명합니다. 사용 할당량 및 제한을 확인하고 증가를 요청합니다.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 357e8b4746aa5be368ecf87d12c86014cc525704
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128666555"
---
# <a name="scale-quotas-and-limits-in-devtest-labs"></a>DevTest Labs의 할당량 및 한도 규모 조정
DevTest Labs에서 작업할 때 일부 Azure 리소스에 대한 특정 기본 한도가 있으며 이 한도가 DevTest Labs 서비스에 영향을 미칠 수 있습니다. 이러한 한도를 **할당량** 이라고 합니다.

> [!NOTE]
> DevTest Labs 서비스는 할당량을 적용하지 않습니다. 발생할 수 있는 모든 할당량은 전체 Azure 구독의 기본 제약 조건입니다.

할당량에 도달할 때까지 각 Azure 리소스를 사용할 수 있습니다. 각 구독에는 별도의 할당량이 있으며 구독마다 사용량이 추적됩니다.

예를 들어 각 구독의 기본 할당량은 20 코어입니다. 따라서 랩에서 코어가 4개인 VM을 만드는 경우 총 5개의 VM을 만들 수 있습니다.

[Azure 구독 및 서비스 한도](../azure-resource-manager/management/azure-subscription-service-limits.md)에는 Azure 리소스에 대 한 가장 일반적인 할당량 중 일부가 나열되어 있습니다. 랩에서 가장 일반적으로 사용되며 할당량이 지정될 수 있는 리소스로는 VM 코어, 공용 IP 주소, 네트워크 인터페이스, 관리 디스크, Azure 역할 할당, ExpressRoute 회로가 있습니다.

## <a name="view-your-usage-and-quotas"></a>사용량 및 할당량 보기
다음 단계에서는 특정 Azure 리소스에 대한 구독의 현재 할당량 그리고 사용한 각 할당량의 비율을 보는 방법을 보여 줍니다.

1. [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)에 로그인합니다.
1. **추가 서비스** 를 선택한 후 목록에서 **청구** 를 선택합니다.
1. 청구 블레이드에서 구독을 선택합니다.
4. **사용량 + 할당량** 을 선택합니다.

   ![사용량 및 할당량 단추](./media/devtest-lab-scale-lab/devtestlab-usage-and-quotas-new.png)

   해당 구독에서 사용 가능한 여러 리소스 및 리소스당 사용되고 있는 할당량 비율을 나열하는 사용량 + 할당량 블레이드가 나타납니다.

   ![할당량 및 사용량](./media/devtest-lab-scale-lab/devtestlab-view-quotas-new.png)

## <a name="requesting-more-resources-in-your-subscription"></a>구독에 더 많은 리소스 요청
할당량 제한에 도달하는 경우 [Azure 구독 및 서비스 제한](../azure-resource-manager/management/azure-subscription-service-limits.md)의 설명에 따라 구독의 기본 리소스 한도를 최대 한도까지 늘릴 수 있습니다.

다음 단계에서는 [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)을 통해 할당량 증가를 요청하는 방법을 보여 줍니다.

1. **추가 서비스**, **청구**, **사용량 + 할당량** 을 차례로 선택합니다.
1. 사용량 + 할당량 블레이드에서 **증가 요청** 단추를 선택합니다.

   ![증가 요청 단추](./media/devtest-lab-scale-lab/devtestlab-request-increase-new.png)

1. 요청을 완료하고 제출하려면 **새 지원 요청** 양식의 세 탭에 필수 정보를 모두 입력합니다.

   ![증가 요청 양식](./media/devtest-lab-scale-lab/devtestlab-support-form-new.png)

Azure 지원에 할당량 증가를 요청하는 자세한 방법은 [Azure 한도 및 증가의 이해](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/)를 참조하세요.



[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

### <a name="next-steps"></a>다음 단계
* [DevTest Labs Azure Resource Manager 빠른 시작 템플릿 갤러리](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates)를 탐색합니다.
