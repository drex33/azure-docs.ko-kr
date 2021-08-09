---
title: Azure Stream Analytics 작업에서 Azure Functions 실행
description: 이 문서에서는 Azure Stream Analytics의 출력으로 Azure functions를 설명합니다.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/28/2021
ms.openlocfilehash: ccedab6284fd5dac5a3d9f8d221a22803a3571f8
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110666674"
---
# <a name="azure-functions-output-from-azure-stream-analytics"></a>Azure Stream Analytics 작업에서 Azure Functions 실행

Azure Functions는 인프라를 명시적으로 프로비저닝하거나 관리할 필요 없이 요청 시 코드를 실행하는 데 사용할 수 있는 서버리스 컴퓨팅 서비스입니다. Azure 또는 파트너 서비스에서 발생하는 이벤트에서 트리거되는 코드를 구현할 수 있습니다. 트리거에 응답하는 Azure Functions의 이 기능을 사용하면 Azure Stream Analytics 출력을 자연스럽게 제공할 수 있습니다. 이 출력 어댑터를 사용하면 사용자가 Stream Analytics를 Azure Functions에 연결하여 다양한 이벤트에 대한 응답으로 스크립트 또는 코드 조각을 실행할 수 있습니다.

Stream Analytics의 Azure Functions 출력은 현재 Azure 중국 21Vianet 및 Azure 독일(T-Systems International) 지역에서 사용할 수 없습니다. 다중 테넌트 클러스터에서 실행 중인 Stream Analytics 작업에서 가상 네트워크 (VNet) 내의 Azure Functions에 대한 연결은 지원되지 않습니다.

Azure Stream Analytics는 HTTP 트리거를 통해 Azure Functions를 호출합니다. Azure Functions 출력 어댑터는 다음과 같은 구성 가능한 속성을 통해 사용할 수 있습니다.

| 속성 이름 | Description |
| --- | --- |
| 함수 앱 |Azure Functions 앱의 이름입니다. |
| 함수 |Azure Functions 앱의 함수 이름입니다. |
| 키 |다른 구독에서 Azure 함수를 사용하려는 경우 이 작업은 함수에 액세스하기 위한 키를 제공하여 수행할 수 있습니다. |
| 최대 일괄 처리 크기 |Azure 함수로 보내는 각 출력 일괄 처리의 최대 크기를 설정할 수 있는 속성입니다. 입력 단위는 바이트입니다. 이 값은 기본적으로 262,144바이트(256KB)입니다. |
| 최대 일괄 처리 수  |Azure Functions로 보내는 각 일괄 처리의 최대 이벤트 수를 지정할 수 있는 속성입니다. 기본값은 100입니다. |

Azure Stream Analytics는 성공적으로 처리된 일괄 처리에 대해 Functions 앱의 200 HTTP 상태를 예상합니다.

Azure Stream Analytics는 Azure 함수에서 413("http 요청 엔터티가 너무 큼") 예외를 받으면 Azure Functions로 보내는 일괄 처리 크기를 줄입니다. Azure 함수 코드에서 이 예외를 사용하여 Azure Stream Analytics에서 너무 큰 일괄 처리를 보내지 않도록 합니다. 또한 함수에 사용되는 최대 일괄 처리 수 및 크기 값이 Stream Analytics 포털에 입력한 값과 일치해야 합니다.

> [!NOTE]
> Stream Analytics는 연결 테스트 중에 빈 일괄 처리를 Azure Functions로 보내 두 앱 간의 연결이 작동하는지 테스트합니다. Functions 앱에서 빈 일괄 처리 요청을 처리하여 테스트 연결이 통과하는지 확인합니다.

또한 시간 범위에서 이벤트가 발생하지 않는 경우 출력이 생성되지 않습니다. 따라서 **computeResult** 함수가 호출되지 않습니다. 이 동작은 기본 제공 기간 이동 집계 함수와 일치합니다.

## <a name="partitioning"></a>분할

partition 키 값은 쿼리의 PARTITION BY 절에 기반합니다. 출력 작성기의 수는 [완전히 병렬화된 쿼리](stream-analytics-scale-jobs.md)의 입력 분할을 따릅니다.

## <a name="output-batch-size"></a>출력 일괄 처리 크기

기본 일괄 처리 크기는 262,144바이트(256KB)입니다. 일괄 처리당 기본 이벤트 수는 100개입니다. 일괄 처리 크기는 구성 가능하며 Stream Analytics 출력 옵션에서 증가 또는 감소될 수 있습니다.

## <a name="limitation"></a>제한 사항

Azure Functions는 HTTP 클라이언트가 100초 후에 시간이 초과되기 때문에 100초 이내에 요청을 완료해야 합니다. Azure Functions가 데이터 일괄 처리를 처리하는 데 100초 이상 걸리는 경우에는 재시도를 트리거하는 시간 제한이 있습니다. Azure Functions가 이전 요청에서 부분적으로 출력되었을 수 있어 데이터를 다시 처리하고 동일한 출력을 잠재적으로 생성하기 때문에 이 재시도는 중복 데이터를 생성할 수 있습니다.


## <a name="next-steps"></a>다음 단계

* [빠른 시작: Azure Portal을 사용하여 Stream Analytics 작업 만들기](stream-analytics-quick-create-portal.md)
* [빠른 시작: Azure CLI를 사용하여 Azure Stream Analytics 작업 만들기](quick-create-azure-cli.md)
* [빠른 시작: ARM 템플릿을 사용하여 Azure Stream Analytics 작업 만들기](quick-create-azure-resource-manager.md)
* [빠른 시작: Azure PowerShell을 사용하여 Stream Analytics 작업 만들기](stream-analytics-quick-create-powershell.md)
* [빠른 시작: Visual Studio를 사용하여 Azure Stream Analytics 작업 만들기](stream-analytics-quick-create-vs.md)
* [빠른 시작: Visual Studio Code에서 Azure Stream Analytics 작업 만들기](quick-create-visual-studio-code.md)
