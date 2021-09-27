---
title: Azure Video Analyzer for Media(이전의 Video Indexer)에서 사용자 모델 사용자 지정 - Azure
description: 이 문서에서는 Azure Video Analyzer for Media(이전의 Video Indexer) 사용자 모델의 개념과 이를 사용자 지정하는 방법을 간략하게 설명합니다.
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: kumud
ms.openlocfilehash: 1d76c1124be542a22707b358ce1d050dfac380f8
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128614867"
---
# <a name="customize-a-person-model-in-video-analyzer-for-media"></a>Video Analyzer for Media에서 사용자 모델 사용자 지정

Azure Video Analyzer for Media(이전의 Video Indexer)는 비디오의 유명인 인식을 지원합니다. 유명인 인식 기능은 IMDB, Wikipedia, 상위 LinkedIn 영향력 행사자 등 일반적으로 요청되는 데이터 원본을 기준으로 하는 약 백만 개의 얼굴을 처리합니다. Video Analyzer for Media에서 인식되지 않는 얼굴은 감지되지만 명명되지 않은 상태로 남아 있습니다. 고객은 사용자 지정 개인 모델을 빌드하여 기본적으로는 인식되지 않는 얼굴을 Video Analyzer for Media가 인식하게 할 수 있습니다. 고객은 개인의 이름과 개인의 얼굴 이미지 파일을 쌍으로 연결하여 개인 모델을 빌드할 수 있습니다.  

계정이 다양한 사용 사례에 적합한 경우 계정에 대해 여러 개의 개인 모델을 만들 수 있는 이점이 있습니다. 예를 들어 계정의 콘텐츠를 다양한 채널로 분류하려는 경우 채널마다 별도의 개인 모델을 만들 수 있습니다. 

> [!NOTE]
> 각 개인 모델은 최대 100만 명의 개인을 지원하며 각 계정의 개인 모델은 50개로 제한됩니다. 

모델이 생성되면, 비디오를 업로드/인덱싱하거나 다시 인덱싱할 때 특정 개인 모델의 모델 ID를 제공하여 모델을 사용할 수 있습니다. 비디오의 새로운 얼굴을 학습하면 해당 비디오와 연결된 특정 사용자 지정 모델이 업데이트됩니다. 

다중 개인 모델 지원이 필요하지 않은 경우, 업로드/인덱싱하거나 다시 인덱싱할 때 비디오에 개인 모델 ID를 할당하지 마세요. 이 경우 Video Analyzer for Media는 계정의 기본 개인 모델을 사용합니다. 

[웹 사이트를 사용하여 개인 모델 사용자 지정](customize-person-model-with-website.md) 항목에 설명된 대로 Video Analyzer for Media 웹 사이트를 사용하여 비디오에서 감지된 얼굴을 편집하고 계정의 여러 사용자 지정 개인 모델을 관리할 수 있습니다.  [API를 사용하여 개인 모델 사용자 지정](customize-person-model-with-api.md)에 설명된 대로 API를 사용할 수도 있습니다.
