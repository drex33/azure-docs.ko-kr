---
title: Azure Stream Analytics 미리 보기 기능
description: 이 문서에는 현재 미리 보기에 있는 Azure Stream Analytics 기능이 나열되어 있습니다.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/16/2021
ms.openlocfilehash: 3f6b46425954d8befaef396c66b023565310ec36
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528950"
---
# <a name="azure-stream-analytics-preview-features"></a>Azure Stream Analytics 미리 보기 기능

이 문서에는 Azure Stream Analytics에 현재 미리 보기로 제공되는 모든 기능이 요약되어 있습니다. 프로덕션 환경에서 미리 보기 기능을 사용하는 것은 권장되지 않습니다.

## <a name="authenticate-to-sql-database-output-with-managed-identities-preview"></a>관리 ID를 사용하여 SQL Database 출력에 인증(미리 보기)

Azure Stream Analytics는 Azure SQL Database 출력 싱크에 대한 [관리 ID 인증](../active-directory/managed-identities-azure-resources/overview.md)을 지원합니다. 관리 ID를 사용하면 암호를 변경할 때 재인증해야 하는 것과 같은 사용자 기반 인증 방법의 제한이 사라집니다. 

## <a name="real-time-high-performance-scoring-with-custom-ml-models-managed-by-azure-machine-learning"></a>Azure Machine Learning에서 관리하는 사용자 지정 ML 모델을 사용하는 실시간 고성능 점수 매기기

Azure Stream Analytics는 코드를 작성할 필요가 없는 워크플로를 사용하는 Azure Machine Learning에서 관리하며, AKS(Azure Kubernetes Service) 또는 ACI(Azure Container Instances)에서 호스팅되는 사용자 지정의 미리 학습된 Machine Learning 모델을 활용하여 고성능의 실시간 점수 매기기를 지원합니다. 미리 보기에 [등록](https://aka.ms/asapreview1)하세요.

## <a name="c-custom-de-serializers"></a>C# 사용자 지정 역직렬 변환기
개발자는 Azure Stream Analytics 기능을 활용하여 Protobuf, XML 또는 사용자 지정 형식의 데이터를 처리할 수 있습니다. C#에서 [사용자 지정 역직렬 변환기](custom-deserializer-examples.md)를 구현하여 Azure Stream Analytics에서 수신한 이벤트를 역직렬화하는 데 사용할 수 있습니다.

## <a name="extensibility-with-c-custom-code"></a>C# 사용자 지정 코드를 사용한 확장 가능 여부

클라우드 또는 IoT Edge에서 Stream Analytics 모듈을 만드는 개발자는 사용자 지정 C# 함수를 작성하거나 다시 사용할 수 있으며, [사용자 정의 함수](stream-analytics-edge-csharp-udf-methods.md)를 통해 쿼리에서 직접 호출할 수 있습니다.

## <a name="debug-queries-locally-using-job-diagram-in-visual-studio-code"></a>Visual Studio Code에서 작업 다이어그램을 사용하여 로컬로 쿼리 디버그

쿼리를 로컬로 테스트하는 동안 작업 다이어그램을 사용하여 각 단계에 대한 중간 결과 집합 및 메트릭을 검사할 수 있습니다.

## <a name="explore-jobs-in-visual-studio-code"></a>Visual Studio Code에서 작업 살펴보기

Visual Studio Code Extension의 Stream Analytics Explorer는 개발자에게 Stream Analytics 작업을 관리하기 위한 간단한 환경을 제공합니다. Stream Analytics Explorer에서 작업을 쉽게 관리하고, 작업 다이어그램을 보고, 작업 모니터에서 디버그할 수 있습니다.

## <a name="debug-query-steps-in-visual-studio"></a>Visual Studio에서의 쿼리 디버그 단계

Visual Studio용 Azure Stream Analytics 도구에서 로컬 테스트를 수행할 때 데이터 다이어그램의 중간 행 세트를 손쉽게 미리 볼 수 있습니다. 


## <a name="live-data-testing-in-visual-studio"></a>Visual Studio에서 실시간 데이터 테스트

Azure Stream Analytics용 Visual Studio 도구는 이벤트 허브 또는 IoT 허브 등의 클라우드 원본에서 실시간 이벤트 스트림에 대한 쿼리를 테스트할 수 있는 로컬 테스트 기능을 향상합니다. [Azure Stream Analytics Tools for Visual Studio를 사용하여 로컬로 라이브 데이터 테스트](stream-analytics-live-data-local-testing.md) 방법을 알아보세요.


