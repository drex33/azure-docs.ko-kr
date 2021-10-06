---
title: Azure Face 서비스란?
titleSuffix: Azure Cognitive Services
description: Azure Face 서비스는 이미지에서 사람의 얼굴을 감지, 인식 및 분석하는 데 사용하는 AI 알고리즘을 제공합니다.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: overview
ms.date: 09/27/2021
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: 얼굴 인식, 얼굴 인식 소프트웨어, 얼굴 분석, 얼굴 일치, 얼굴 인식 앱, 이미지별 얼굴 검색, 얼굴 인식 검색
ms.openlocfilehash: 3cb63bb5fb98eddee84677bb8e2d8d11ed86c876
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129362755"
---
# <a name="what-is-the-azure-face-service"></a>Azure Face 서비스란?

> [!WARNING]
> 2020년 6월 11일, Microsoft는 인권에 기반한 강력한 규정이 적용될 때까지 미국 경찰 당국에 얼굴 인식 기술을 판매하지 않겠다고 발표했습니다. 따라서 미국 경찰 당국에 의해 또는 미국 경찰 당국을 위해 이 서비스를 사용하거나 허용하는 경우 고객은 얼굴 인식 기능 또는 Face나 Video Indexer와 같은 Azure 서비스에 포함된 기능을 사용하지 않을 수도 있습니다. 새 얼굴 리소스를 만들 때는 Azure Portal에서 이 서비스를 미국의 경찰서를 통해 또는 경찰서를 위해 사용하지 않을 것이며, [RAI(Responsible AI) 설명서](../cognitive-services-apis-create-account-cli.md#prerequisites)를 검토했으며 이 서비스를 그에 따라 사용할 것임을 인정하고 동의합니다.

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Azure Face 서비스는 이미지에서 사람의 얼굴을 감지, 인식 및 분석하는 AI 알고리즘을 제공합니다. 얼굴 인식 소프트웨어는 ID 검증, 터치리스 액세스 제어, 개인 정보 보호를 위한 얼굴 흐림 처리 등 다양한 시나리오에서 중요합니다.

ID 검증: 여권 또는 운전면허증이나 기타 등록 이미지와 같은 정부 발급 ID 카드를 기준으로 사람의 신원을 확인하여 디지털 또는 물리적 서비스나 계정 복구에 대한 액세스 권한을 부여합니다. 특정 액세스 시나리오에는 새 계정 개설, 작업자 확인 또는 온라인 평가 관리가 포함됩니다. ID 검증은 사용자가 온보딩할 때 한 번 수행될 수 있으며, 누군가 디지털 또는 물리적 서비스에 액세스할 때 반복적으로 수행될 수 있습니다.

터치리스 액세스 제어: 옵트인 얼굴 식별은 카드 또는 티켓과 같은 오늘날의 방법보다 향상된 액세스 제어 환경을 지원함과 동시에 카드 공유, 분실 또는 도난으로 인한 디지털 위생 및 보안 위험을 줄일 수 있습니다. 얼굴 인식은 사무실, 병원, 체육관, 클럽 또는 학교의 리셉션 키오스크뿐만 아니라 공항, 경기장, 테마파크 또는 빌딩의 체크 인 루프에서 사용자의 체크 인 프로세스를 지원합니다.

얼굴 편집: 개인 정보 보호를 위해 비디오에 녹화된 사용자의 얼굴을 편집하거나 흐리게 처리합니다.

이 설명서에는 다음과 같은 유형의 문서가 포함되어 있습니다.
* [빠른 시작](./Quickstarts/client-libraries.md)은 서비스를 호출하고 짧은 시간 내에 결과를 얻을 수 있는 단계별 지침입니다. 
* [방법 가이드](./Face-API-How-to-Topics/HowtoDetectFacesinImage.md)에는 보다 구체적이거나 사용자 지정된 방식으로 서비스를 사용하기 위한 지침이 포함되어 있습니다.
* [개념 문서](./concepts/face-detection.md)에서는 서비스의 기능 및 기능에 대한 자세한 설명을 제공합니다.
* [자습서](./enrollment-overview.md)는 보다 광범위한 비즈니스 솔루션에서 이 서비스를 구성 요소로 사용하는 방법을 보여주는 긴 가이드입니다.

## <a name="face-detection-and-analysis"></a>얼굴 감지 및 분석

얼굴 감지는 다른 모든 시나리오에서 첫 번째 단계로 필요합니다. Detect API는 이미지에서 사람의 얼굴을 감지하고 해당 위치의 사각형 좌표를 반환합니다. 또한 저장된 얼굴 데이터를 나타내는 고유한 ID를 반환합니다. 이는 이후 작업에서 얼굴을 식별하거나 확인하는 데 사용됩니다.

얼굴 감지는 필요에 따라 머리 포즈, 성별, 연령, 감정, 수염 및 안경과 같은 일련의 얼굴 관련 특성을 추출할 수 있습니다. 이러한 속성은 실제 분류가 아닌 일반적인 예측입니다. 일부 특성은 사용자가 얼굴 서비스에 자신을 추가할 때 애플리케이션에서 고품질의 얼굴 데이터를 얻도록 하는 데 유용합니다. 예를 들어 사용자가 선글라스를 착용하고 있는 경우 애플리케이션에서 사용자에게 선글라스를 벗도록 권고할 수 있습니다.

> [!NOTE]
> 얼굴 감지 기능은 [Computer Vision 서비스](../computer-vision/overview.md)를 통해 사용할 수도 있습니다. 그러나 식별, 확인, 유사 항목 찾기 또는 얼굴 그룹화와 같은 다른 얼굴 작업을 수행하려면 이 서비스를 대신 사용해야 합니다.

얼굴 감지 및 분석에 대한 자세한 내용은 [얼굴 감지](concepts/face-detection.md) 개념 문서를 참조하세요. [Detect API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) 참조 설명서도 참조하세요.


## <a name="identity-verification"></a>ID 확인

현대의 기업 및 앱은 얼굴 식별 및 얼굴 확인 작업을 사용하여 사용자의 본인 여부를 확인할 수 있습니다. 

### <a name="identification"></a>Identification(식별)

얼굴 식별은 이미지의 한 면을 보안 리포지토리의 얼굴 세트와 일치시키는 "일 대 다"를 처리할 수 있습니다. 얼굴 데이터가 쿼리 얼굴과 일치하는 정도에 따라 일치 후보가 반환됩니다. 이 시나리오는 특정 사용자 그룹에 대한 빌드 액세스 권한을 부여하거나 디바이스의 사용자를 확인하는 데 사용됩니다.

다음 이미지는 `"myfriends"`라는 데이터베이스의 예를 보여 줍니다. 각 그룹은 최대 1백만 개의 서로 다른 사람 개체를 포함할 수 있습니다. 각 사람 개체에 대해 최대 248개의 얼굴을 등록할 수 있습니다.

![서로 다른 사람에 대한 3개의 열 및 각 열에 3개의 얼굴 이미지 행이 있는 그리드](./Images/person.group.clare.jpg)

그룹을 만들고 학습시킨 후에 새로 감지된 얼굴과 그룹을 비교하여 식별할 수 있습니다. 얼굴이 그룹에 속한 사람의 것으로 식별되면 해당 사람 개체가 표시됩니다.

### <a name="verification"></a>확인

확인 작업은 "이 두 얼굴이 동일한 사람의 것인가요?"라는 질문에 답합니다. 

또한 이미지의 한 면을 보안 리포지토리 또는 사진의 한 면으로 "일 대 일"로 일치시킵니다.

ID 확인 또는 액세스 제어 시나리오에서 확인을 사용하여 사진이 이전에 캡처한 이미지(예: 정부 발급 ID 카드의 사진)와 일치하는지 확인할 수 있습니다.

ID 검증에 대한 자세한 내용은 [얼굴 인식](concepts/face-recognition.md) 개념 가이드 또는 [Identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) 및 [Verify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) API 참조 설명서를 확인하세요.


## <a name="find-similar-faces"></a>유사 얼굴 찾기

유사 얼굴 찾기 작업은 대상 얼굴과 일련의 후보 얼굴 간 얼굴 일치를 수행하고 대상 얼굴과 비슷해 보이는 몇 개의 얼굴을 찾습니다. 이는 이미지별 얼굴 검색을 수행하는 데 유용합니다. 

이 서비스는 **matchPerson** 및 **matchFace** 라는 두 가지 작업 모드를 지원합니다. **matchPerson** 모드는 [Verify API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a)를 사용하여 동일한 사람에 대해 필터링한 후 유사한 얼굴을 반환합니다. **matchFace** 모드는 동일한 사람 필터를 무시합니다. 이 모드는 동일한 사람에게 속하거나 속하지 않을 수 있는 유사한 후보 얼굴의 목록을 표시합니다.

다음 예제에서는 대상 얼굴을 보여 줍니다.

![웃는 여자](./Images/FaceFindSimilar.QueryFace.jpg)

그리고 다음 이미지는 후보 얼굴입니다.

![5개의 웃는 사람들의 이미지 a와 b 이미지는 동일한 사람을 보여 줍니다.](./Images/FaceFindSimilar.Candidates.jpg)

4개의 유사한 얼굴을 찾기 위해 **matchPerson** 모드는 a와 b를 표시하며 동일한 사람을 대상 얼굴로 나타냅니다. **matchFace** 모드는 일부가 대상과 동일한 사람이 아니거나 유사성이 낮은 경우에도 4개의 후보인 a, b, c, d&mdash;를 정확히 표시합니다. 자세한 내용은 [얼굴 인식](concepts/face-recognition.md) 개념 가이드 또는 [Find Similar API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) 참조 설명서를 참조하세요.

## <a name="group-faces"></a>얼굴 그룹화

그룹화 작업은 알 수 없는 얼굴을 유사성에 따라 여러 개의 소그룹으로 나눕니다. 각 그룹은 서로 공통점이 없는 여러 개의 고유한 원래 얼굴에 속합니다. 또한 유사성을 찾을 수 없는 얼굴 ID를 포함하는 단일 "messyGroup" 배열을 반환합니다.

반환된 그룹의 모든 얼굴은 동일한 사람일 가능성이 있지만 단일 사용자에 대해 여러 다른 그룹이 있을 수 있습니다. 이러한 그룹은 식과 같은 다른 요소로 구분됩니다. 자세한 내용은 [얼굴 인식](concepts/face-recognition.md) 개념 가이드 또는 [Group API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238) 참조 설명서를 참조하세요.

## <a name="sample-app"></a>샘플 앱

다음 샘플 애플리케이션에서는 Face 서비스를 사용하는 몇 가지 방법을 보여 줍니다.

- [FamilyNotes UWP 앱](https://github.com/Microsoft/Windows-appsample-familynotes)은 가족 메모 공유 시나리오에서 음성, Cortana, 잉크 및 카메라와 함께 얼굴 식별 기능을 사용하는 UWP(유니버설 Windows 플랫폼) 앱입니다.

## <a name="data-privacy-and-security"></a>데이터 개인 정보 보호 및 보안

모든 Cognitive Services 리소스와 마찬가지로 Face 서비스를 사용하는 개발자는 고객 데이터에 대한 Microsoft 정책을 알고 있어야 합니다. 자세한 내용은 Microsoft Trust Center의 [Cognitive Services 페이지](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices)를 참조하세요.

## <a name="next-steps"></a>다음 단계

빠른 시작을 따라 사용자가 선택한 언어로 얼굴 인식 앱의 기본 구성 요소를 코딩합니다.

- [클라이언트 라이브러리 빠른 시작](quickstarts/client-libraries.md)