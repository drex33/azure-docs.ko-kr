---
title: Azure Stream Analytics 기능 비교
description: 이 문서에서는 Azure Portal, Visual Studio 및 Visual Studio Code에서 Azure Stream Analytics 클라우드 및 IoT Edge 작업에 대해 지원되는 기능을 비교합니다.
author: an-emma
ms.author: raan
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 26ef280950d14f7bb3a833edd466912540ff1059
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130263195"
---
# <a name="azure-stream-analytics-feature-comparison"></a>Azure Stream Analytics 기능 비교

Azure Stream Analytics를 통해, [Azure Portal](stream-analytics-quick-create-portal.md), [Visual Studio](stream-analytics-quick-create-vs.md) 및 [Visual Studio Code](quick-create-visual-studio-code.md)를 사용하여 클라우드 및 IoT Edge에서 스트리밍 솔루션을 만들 수 있습니다. 이 문서의 표에서는 두 작업 유형에 대해 각 플랫폼에서 지원하는 기능을 보여줍니다.

> [!NOTE]
> Visual Studio 및 Visual Studio Code 도구는 중국 동부, 중국 북부, 독일 중부 및 독일 북동부 지역의 작업을 지원하지 않습니다.

## <a name="cloud-job-features"></a>클라우드 작업 기능


|기능  |포털  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|플랫폼 간     |Mac</br>Linux</br>Windows         |Windows        |Mac</br>Linux</br>Windows          |
|스크립트 작성     |예         |예         |예         |
|Intellisense 스크립팅     |구문 강조         |구문 강조</br>코드 완성</br>오류 표식         |구문 강조</br>코드 완성</br>오류 표식         |
|모든 종류의 입력, 출력 및 작업 구성 정의     |예         |예         |예         |
|원본 제어     |예         |예         |예         |
|CI/CD 지원     |부분         |예         |예         |
|여러 쿼리에서 입력 및 출력 공유     |예         |예         |예         |
|샘플 파일을 통한 쿼리 테스트     |예         |예        |예         |
|라이브 데이터 로컬 테스트     |예         |예       |예      |
|작업 나열 및 작업 엔터티 보기     |예         |예        |예         |
|로컬 프로젝트로 작업 내보내기     |예         |예         |예         |
|작업 제출, 시작 및 중지     |예         |예         |예         |
|작업 메트릭 및 다이어그램 보기     |예         |예         |예         |
|작업 런타임 오류 보기     |예         |예         |예         |
|리소스 로그     |예         |아니요         |예         |
|사용자 지정 메시지 속성     |예         |예         |예       |
|C# 사용자 지정 코드 함수 및 역직렬 변환기|읽기 전용 모드|예|예|
|JavaScript UDF 및 UDA     |예         |예         |Windows만         |
|Azure Machine Learning      |예        |예         |예         |
|호환성 수준     |1.0</br>1.1</br>1.2(기본값)         |1.0</br>1.1</br>1.2(기본값)           |1.0</br>1.1</br>1.2(기본값)           |
|기본 제공 ML 기반 변칙 검색 함수     |예         |예         |예         |
|기본 제공 지리 공간적 함수     |예         |예         |예         |



## <a name="iot-edge-job-features"></a>IoT Edge 작업 기능

|기능  |포털  |Visual Studio  |Visual Studio Code  |
|---------|---------|---------|---------|
|작업 작성     |예         |예         |예         |
|원본 제어     |예         |예         |예         |
|로컬 프로젝트로 작업 내보내기     |예         |예         |예         |
|샘플 파일을 통한 쿼리 테스트     |예         |예         |예         |
|여러 쿼리에서 입력 및 출력 공유     |예         |예         |예         |
|C# UDF     |예         |예         |예         |
|작업 제출     |예         |예         |예         |
|작업 나열 및 작업 엔터티 보기     |예         |예         |예         |
|작업 메트릭 및 다이어그램 보기     |예         |부분         |예         |
|작업 런타임 오류 보기     |예         |부분         |예         |
|CI/CD 지원     |예         |아니요         |아니요         |


## <a name="next-steps"></a>다음 단계

* [IoT Edge의 Azure Stream Analytics](stream-analytics-edge.md)
* [자습서: Azure Stream Analytics Edge 작업용 C# 사용자 정의 함수 작성(미리 보기)](stream-analytics-edge-csharp-udf.md)
* [Visual Studio 도구를 사용하여 Stream Analytics IoT Edge 작업 개발](stream-analytics-tools-for-visual-studio-edge-jobs.md)
* [Visual Studio를 사용하여 Azure Stream Analytics 작업 보기](stream-analytics-vs-tools.md)
* [Visual Studio Code를 사용하여 Azure Stream Analytics 탐색(미리 보기)](visual-studio-code-explore-jobs.md)


