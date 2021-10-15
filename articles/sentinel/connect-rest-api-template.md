---
title: 데이터 원본을 Azure 센티널 데이터 수집기 API에 커넥트 하 여 데이터 수집 | Microsoft Docs
description: 외부 시스템을 Azure 센티널 데이터 수집기 API에 연결 하 여 작업 영역의 사용자 지정 로그에 로그 데이터를 수집 하는 방법에 대해 알아봅니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/17/2021
ms.author: yelevin
ms.openlocfilehash: 3b268d95eed0456b858ab5d8d93b895faca1ee7b
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130038425"
---
# <a name="connect-your-data-source-to-the-azure-sentinel-data-collector-api-to-ingest-data"></a>데이터 원본을 Azure 센티널 데이터 수집기 API에 커넥트 하 여 데이터 수집

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

타사 공급업체에서 구축한 API 통합은 제품의 데이터 원본에서 데이터를 가져오고 Azure Sentinel의 [Azure Monitor Data Collector API](../azure-monitor/logs/data-collector-api.md)에 연결하여 Azure Sentinel 작업 영역의 사용자 지정 로그 테이블에 데이터를 푸시합니다.

대부분의 경우 각 공급업체의 설명서에서 Azure Sentinel에 연결하도록 이러한 데이터 원본을 구성하는 데 필요한 모든 정보를 찾을 수 있습니다.

[데이터 커넥터 참조](data-connectors-reference.md) 페이지에서 제품 섹션에 표시될 수 있는 추가 지침과 공급업체 지침에 대한 링크를 확인합니다.

> [!NOTE]
> 데이터는 Azure Sentinel을 실행하는 작업 영역의 지리적 위치에 저장됩니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure Sentinel 작업 영역에 대한 읽기 및 쓰기 권한이 있어야 합니다.

- 작업 영역 공유 키에 대한 읽기 권한이 있어야 합니다. [작업 영역 키에 대해 자세히 알아보세요](../azure-monitor/agents/agent-windows.md).

## <a name="configure-and-connect-your-data-source"></a>데이터 원본 구성 및 연결

1. Azure Sentinel 포털의 탐색 메뉴에서 **데이터 커넥터** 를 선택합니다.

1. 데이터 커넥터 갤러리에서 제품 항목을 선택한 다음 **커넥터 페이지 열기** 단추를 선택합니다.

1. 커넥터 페이지에 표시되는 단계나 표시되는 공급업체 지침에 대한 링크를 따릅니다.

1. 작업 영역 ID와 기본 키를 묻는 메시지가 표시되면 데이터 커넥터 페이지에서 복사하여 공급업체의 지침에 따라 구성에 붙여넣습니다. 아래 예제를 참조하세요.

    :::image type="content" source="media/connect-rest-api-template/workspace-id-primary-key.png" alt-text="작업 영역 ID 및 기본 키":::

## <a name="find-your-data"></a>데이터 찾기

연결이 성공적으로 설정되면 **로그** 의 **CustomLogs** 섹션에 로그가 표시됩니다. 테이블 이름은 [데이터 커넥터 참조](data-connectors-reference.md) 페이지에서 제품 섹션을 참조하세요.

제품의 데이터를 쿼리하려면 쿼리에서 해당 테이블 이름을 사용합니다.

로그가 Log Analytics에 표시될 때까지 최대 20분가량 소요될 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 외부 데이터 원본을 Azure 센티널 데이터 수집기 API에 연결 하는 방법을 알아보았습니다. 이러한 데이터 커넥터에 기본 제공되는 기능을 최대한 활용하려면 데이터 커넥터 페이지에서 **다음 단계** 탭을 선택합니다. 여기에서는 유용한 정보 찾기를 시작할 수 있도록 미리 만들어진 샘플 쿼리, 통합 문서 및 분석 규칙 템플릿을 찾을 수 있습니다.

Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [데이터 및 잠재적 위협에 대한 가시성을 확보](get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](monitor-your-data.md)하여 데이터를 모니터링합니다.