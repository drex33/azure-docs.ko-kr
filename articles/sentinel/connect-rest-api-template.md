---
title: 데이터 원본을 Microsoft Sentinel 데이터 수집기 API에 커넥트 데이터 | 수집 Microsoft Docs
description: 외부 시스템을 Microsoft Sentinel 데이터 수집기 API에 연결하여 작업 영역의 사용자 지정 로그에 로그 데이터를 수집하는 방법을 알아봅니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 733ce3565970a6f858edfe32386a21d7655345a6
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132721825"
---
# <a name="connect-your-data-source-to-the-microsoft-sentinel-data-collector-api-to-ingest-data"></a>데이터 원본을 Microsoft Sentinel 데이터 수집기 API에 커넥트 데이터를 수집합니다.

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

타사 공급업체에서 빌드한 API 통합은 제품의 데이터 원본에서 데이터를 끌어오고 Microsoft Sentinel의 [Azure Monitor 데이터 수집기 API에](../azure-monitor/logs/data-collector-api.md) 연결하여 Microsoft Sentinel 작업 영역의 사용자 지정 로그 테이블에 데이터를 푸시합니다.

대부분의 경우 각 공급업체의 설명서에서 Microsoft Sentinel에 연결하도록 이러한 데이터 원본을 구성하는 데 필요한 모든 정보를 찾을 수 있습니다.

[데이터 커넥터 참조](data-connectors-reference.md) 페이지에서 제품 섹션에 표시될 수 있는 추가 지침과 공급업체 지침에 대한 링크를 확인합니다.

> [!NOTE]
> 데이터는 Microsoft Sentinel을 실행하는 작업 영역의 지리적 위치에 저장됩니다.

## <a name="prerequisites"></a>필수 구성 요소

- Microsoft Sentinel 작업 영역에 대한 읽기 및 쓰기 권한이 있어야 합니다.

- 작업 영역 공유 키에 대한 읽기 권한이 있어야 합니다. [작업 영역 키에 대해 자세히 알아보세요](../azure-monitor/agents/agent-windows.md).

## <a name="configure-and-connect-your-data-source"></a>데이터 원본 구성 및 연결

1. Microsoft Sentinel 포털의 탐색 메뉴에서 **데이터 커넥터를** 선택합니다.

1. 데이터 커넥터 갤러리에서 제품 항목을 선택한 다음 **커넥터 페이지 열기** 단추를 선택합니다.

1. 커넥터 페이지에 표시되는 단계나 표시되는 공급업체 지침에 대한 링크를 따릅니다.

1. 작업 영역 ID와 기본 키를 묻는 메시지가 표시되면 데이터 커넥터 페이지에서 복사하여 공급업체의 지침에 따라 구성에 붙여넣습니다. 아래 예제를 참조하세요.

    :::image type="content" source="media/connect-rest-api-template/workspace-id-primary-key.png" alt-text="작업 영역 ID 및 기본 키":::

## <a name="find-your-data"></a>데이터 찾기

연결이 성공적으로 설정되면 **로그** 의 **CustomLogs** 섹션에 로그가 표시됩니다. 테이블 이름은 [데이터 커넥터 참조](data-connectors-reference.md) 페이지에서 제품 섹션을 참조하세요.

제품의 데이터를 쿼리하려면 쿼리에서 해당 테이블 이름을 사용합니다.

로그가 Log Analytics에 표시될 때까지 최대 20분가량 소요될 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 외부 데이터 원본을 Microsoft Sentinel 데이터 수집기 API에 연결하는 방법을 배웠습니다. 이러한 데이터 커넥터에 기본 제공되는 기능을 최대한 활용하려면 데이터 커넥터 페이지에서 **다음 단계** 탭을 선택합니다. 여기에서는 유용한 정보 찾기를 시작할 수 있도록 미리 만들어진 샘플 쿼리, 통합 문서 및 분석 규칙 템플릿을 찾을 수 있습니다.

Microsoft Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [데이터 및 잠재적 위협에 대한 가시성을 확보](get-visibility.md)하는 방법을 알아봅니다.
- [Microsoft Sentinel을 사용하여 위협 검색을](detect-threats-built-in.md)시작합니다.
- [통합 문서를 사용](monitor-your-data.md)하여 데이터를 모니터링합니다.
