---
title: Azure Event Grid의 이벤트 도메인 사용 사례
description: 이 문서에서는 Azure Event Grid에서 이벤트 도메인을 사용하는 몇 가지 사용 사례를 설명합니다.
ms.topic: conceptual
ms.date: 03/04/2021
ms.openlocfilehash: 24a338717b44567bad0ec1575d98ddaeada71113
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/21/2021
ms.locfileid: "112413624"
---
# <a name="use-cases-for-event-domains-in-azure-event-grid"></a>Azure Event Grid의 이벤트 도메인 사용 사례
이 문서에서는 Azure Event Grid에서 이벤트 도메인을 사용하는 몇 가지 사용 사례를 설명합니다. 

## <a name="use-case-1"></a>사용 사례 1 
[!INCLUDE [event-grid-domain-example-use-case.md](./includes/event-grid-domain-example-use-case.md)]

## <a name="use-case-2"></a>사용 사례 2
시스템 토픽을 사용할 때 이벤트 구독은 500개로 제한됩니다. 시스템 토픽에 대해 500개보다 많은 이벤트 구독이 필요한 경우 도메인을 사용할 수 있습니다. 

Azure Blob Storage에 대한 시스템 토픽을 만들고 토픽에 대해 500개보다 많은 구독을 만들어야 하지만 제한(토픽당 구독 500개) 때문에 불가능하다고 가정합니다. 이 경우 다음 솔루션을 통해 이벤트 도메인을 사용할 수 있습니다. 

1. 최대 100,000개의 토픽을 지원할 수 있는 도메인을 만들고 각 도메인 토픽은 500개의 이벤트 구독을 가질 수 있습니다. 이 모델은 500 * 100,000 이벤트 구독을 제공합니다. 
1. Azure Storage 시스템 토픽에 대한 Azure 함수 구독을 만듭니다. 함수가 Azure Storage에서 이벤트를 수신하면 이벤트를 보강하고 적절한 도메인 토픽에 게시할 수 있습니다. 예를 들어 함수는 Blob 파일 이름을 구문 분석하여 대상 도메인 토픽을 결정하고 이벤트를 도메인 토픽에 게시할 수 있습니다. 

:::image type="content" source="./media/event-domains-use-cases/use-case-2.jpg" alt-text="AzureEvent Grid 도메인 - 사용 사례 2":::


## <a name="next-steps"></a>다음 단계
이벤트 도메인을 설정하고, 토픽을 만들고, 이벤트 구독을 만들고, 이벤트를 게시하는 방법에 대한 자세한 내용은 [이벤트 도메인 관리](./how-to-event-domains.md)를 참조하세요.
