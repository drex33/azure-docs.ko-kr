---
title: 자체 호스팅 통합 런타임 자동 업데이트 및 만료 알림
description: 자체 호스팅 통합 런타임 자동 업데이트 및 만료 알림에 대해 알아봅니다.
ms.service: data-factory
ms.subservice: integration-runtime
ms.topic: conceptual
author: lrtoyou1223
ms.author: lle
ms.custom: seo-lt-2019
ms.date: 06/16/2021
ms.openlocfilehash: f59948204af76ce5e2d940c2910601b848bb4605
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122642995"
---
# <a name="self-hosted-integration-runtime-auto-update-and-expire-notification"></a>자체 호스팅 통합 런타임 자동 업데이트 및 만료 알림

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 자체 호스팅 통합 런타임을 최신 버전으로 자동 업데이트하는 방법과 ADF에서 자체 호스팅 통합 런타임의 버전을 관리하는 방법을 설명합니다.

## <a name="self-hosted-integration-runtime-auto-update"></a>자체 호스팅 통합 런타임 자동 업데이트
일반적으로 자체 호스팅 통합 런타임을 로컬 컴퓨터 또는 Azure VM에 설치하는 경우 자체 호스팅 통합 런타임의 버전을 관리하는 두 가지 옵션인 자동 업데이트 또는 수동 관리가 있습니다. 일반적으로 ADF는 새로운 기능 릴리스, 버그 수정 또는 향상된 기능을 포함하여 매월 자체 호스팅 통합 런타임의 두 가지 새 버전을 릴리스합니다. 따라서 최신 기능과 향상된 기능을 사용하려면 최신 버전으로 업데이트하는 것이 좋습니다.

가장 편리한 방법은 자체 호스팅 통합 런타임을 만들거나 편집할 때 자동 업데이트를 사용하도록 설정하는 것입니다. 자체 호스팅 통합 런타임은 최신 버전으로 자동 업그레이드됩니다. 원하는 시간 슬롯에 업데이트를 예약할 수도 있습니다.

:::image type="content" source="media/create-self-hosted-integration-runtime/shir-auto-update.png" alt-text="자동 업데이트 사용":::

자체 호스팅 통합 런타임 클라이언트에서 마지막 업데이트 날짜/시간을 확인할 수 있습니다.

:::image type="content" source="media/create-self-hosted-integration-runtime/shir-auto-update-2.png" alt-text="업데이트 시간을 확인하는 스크린샷":::

이 [PowerShell 명령](/powershell/module/az.datafactory/get-azdatafactoryv2integrationruntime?view=azps-6.1.0&preserve-view=true#example-5--get-self-hosted-integration-runtime-with-detail-status)을 사용하여 자동 업데이트 버전을 다운로드할 수 있습니다. 

> [!NOTE]
> 자체 호스팅 통합 런타임 노드가 여러 개 있는 경우 자동 업데이트 중에 가동이 중지되지 않습니다. 다른 노드에서 작업을 진행하는 동안 한 노드에서 먼저 자동 업데이트가 수행됩니다. 첫 번째 노드에서 업데이트가 완료되면 다른 노드가 업데이트될 때 나머지 작업을 인수합니다. 자체 호스팅 통합 런타임이 하나만 있는 경우 자동 업데이트 중에 약간 가동이 중지됩니다.

## <a name="auto-update-version-vs-latest-version"></a>자동 업데이트 버전과 최신 버전
자체 호스팅 통합 런타임의 안정성을 보장하기 위해 두 가지 버전이 릴리스되더라도 매달 한 가지 버전만 푸시됩니다. 따라서 경우에 따라 자동 업데이트 버전이 실제 최신 버전보다 이전 버전인 것을 확인할 수 있습니다. 최신 버전을 가져오려면 [다운로드 센터](https://www.microsoft.com/download/details.aspx?id=39717)로 이동하면 됩니다.

현재 버전이 오래된 경우 ADF 포털의 자체 호스팅 통합 런타임 **자동 업데이트** 페이지에 최신 버전이 표시됩니다. 자체 호스팅 통합 런타임이 온라인 상태인 경우 이 버전이 자동 업데이트 버전이며, 예약된 시간에 자체 호스팅 통합 런타임을 자동으로 업데이트합니다. 그러나 자체 호스팅 통합 런타임이 오프라인인 경우 이 페이지에 최신 버전만 표시됩니다.

## <a name="self-hosted-integration-runtime-expire-notification"></a>자체 호스팅 통합 런타임 만료 알림
자체 호스팅 통합 런타임의 버전을 수동으로 제어하려는 경우 자동 업데이트 설정을 해제하고 수동으로 설치할 수 있습니다. 자체 호스팅 통합 런타임의 각 버전은 1년 후에 만료됩니다. 만료 메시지는 ADF 포털 및 자체 호스팅 통합 런타임 클라이언트에 만료 **90일** 전부터 표시됩니다.

## <a name="next-steps"></a>다음 단계

- [Azure Data Factory의 통합 런타임 개념](./concepts-integration-runtime.md)을 검토합니다.

- [Azure Portal에서 자체 호스팅 통합 런타임을 만드는](./create-self-hosted-integration-runtime.md) 방법을 알아봅니다.
