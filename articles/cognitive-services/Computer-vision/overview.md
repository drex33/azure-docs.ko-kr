---
title: Computer Vision이란?
titleSuffix: Azure Cognitive Services
description: Computer Vision 서비스는 이미지를 처리하고 정보를 반환하는 고급 알고리즘에 대한 액세스를 제공합니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 09/28/2021
ms.author: pafarley
ms.custom:
- seodec18
- cog-serv-seo-aug-2020
- contperf-fy21q2
keywords: 컴퓨터 비전, 컴퓨터 비전 애플리케이션, 컴퓨터 비전 서비스
ms.openlocfilehash: c5aad28fc8b2052f9a5af1233085a93b6a25c479
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129360154"
---
# <a name="what-is-computer-vision"></a>Computer Vision이란?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Azure의 Computer Vision 서비스는 관심 있는 시각적 기능을 기반으로 이미지를 처리하고 정보를 반환하는 고급 알고리즘에 대한 액세스 권한을 제공합니다. 

| 서비스|설명|
|---|---|
| [OCR(광학 인식)](overview-ocr.md)|OCR(광학 인식) 서비스는 이미지에서 텍스트를 추출합니다. 새 Read API를 사용하여 사진 및 문서에서 인쇄 및 필기 텍스트를 추출할 수 있습니다. 딥 러닝 기반 모델을 사용하고 다양한 표면 및 배경의 텍스트로 작업합니다. 여기에는 비즈니스 문서, 청구서, 영수증, 포스터, 명함, 문자 및 화이트보드가 포함됩니다. OCR API는 [여러 언어](./language-support.md)로 인쇄된 텍스트 추출을 지원합니다. [OCR 빠른 시작](quickstarts-sdk/client-library.md)을 따라 시작하세요.|
|[이미지 분석](overview-image-analysis.md)| 이미지 분석 서비스는 개체, 면, 성인 콘텐츠, 자동 생성된 텍스트 설명 등 이미지에서 많은 시각적 기능을 추출합니다. [이미지 분석 빠른 시작](quickstarts-sdk/image-analysis-client-library.md)을 따라 시작합니다.|
| [공간 분석](intro-to-spatial-analysis-public-preview.md)| 공간 분석 서비스는 비디오 피드에 대한 사용자의 현재 상태와 이동을 분석하고 다른 시스템에서 응답할 수 있는 이벤트를 생성합니다. [공간 분석 컨테이너](spatial-analysis-container.md)를 설치하여 시작합니다.|

## <a name="computer-vision-for-digital-asset-management"></a>디지털 자산 관리를 위한 Computer Vision

Computer Vision은 여러 DAM(디지털 자산 관리) 시나리오를 구현할 수 있습니다. DAM은 풍부한 미디어 자산을 구성, 저장, 검색하고 디지털 권한 및 사용 권한을 관리하는 비즈니스 프로세스입니다. 예를 들어 기업에서 시각적 로고, 면, 개체, 색 등을 기준으로 이미지를 그룹화하고 식별하기를 원할 수 있습니다. 또는 이미지를 검색할 수 있도록 [이미지에 대한 자막을 자동으로 생성](./Tutorials/storage-lab-tutorial.md)하고 키워드를 연결하기를 원할 수 있습니다. Cognitive Services, Azure Cognitive Search 및 인텔리전트 보고를 사용하는 올인원 DAM 솔루션은 GitHub의 [정보 마이닝 솔루션 가속기 가이드](https://github.com/Azure-Samples/azure-search-knowledge-mining)를 참조하세요. 다른 DAM 예제는 [Computer Vision 솔루션 템플릿](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates) 리포지토리를 참조하세요.

## <a name="image-requirements"></a>이미지 요구 사항

Computer Vision에서 이미지를 분석하는 데 충족되어야 하는 요구 사항은 다음과 같습니다.

- 이미지가 JPEG, PNG, GIF 또는 BMP 형식으로 제공되어야 합니다.
- 이미지의 파일 크기가 4MB보다 작아야 합니다.
- 이미지의 크기가 50 x 50 픽셀보다 커야 합니다.
  - 읽기 API의 경우 이미지의 크기는 50 x 50 픽셀 및 10000 x 10000 픽셀 사이여야 합니다.

## <a name="data-privacy-and-security"></a>데이터 개인 정보 보호 및 보안

모든 Cognitive Services와 마찬가지로 Computer Vision 서비스를 사용하는 개발자는 고객 데이터에 대한 Microsoft의 정책에 대해 알고 있어야 합니다. Microsoft Trust Center의 [Cognitive Services 페이지](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices)에서 자세히 알아보세요.

## <a name="next-steps"></a>다음 단계

빠른 시작을 따라 선호하는 개발 언어로 서비스를 구현하고 실행합니다.

* [빠른 시작: OCR(광학 인식)](quickstarts-sdk/client-library.md)
* [빠른 시작: 이미지 분석](quickstarts-sdk/image-analysis-client-library.md)
* [빠른 시작: 공간 분석 컨테이너](spatial-analysis-container.md)
