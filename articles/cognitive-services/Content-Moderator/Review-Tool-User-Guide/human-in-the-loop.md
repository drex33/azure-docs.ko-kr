---
title: 검토 도구 개념 알아보기 - Content Moderator
titleSuffix: Azure Cognitive Services
description: AI와 인간의 검토 조정 활동 결합을 관장하는 웹 사이트인 Content Moderator 검토 도구에 대해 알아봅니다.
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.date: 10/28/2021
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.author: pafarley
ms.openlocfilehash: 10caecde2eb28a73252ce8581d279c657223f1c9
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131469980"
---
# <a name="content-moderator-review-tool"></a>Content Moderator 검토 도구

[!INCLUDE [deprecation notice](../includes/tool-deprecation.md)]

Azure Content Moderator는 Machine Learning 콘텐츠 조정을 사용자 검토와 결합하는 서비스를 제공합니다. [검토 도구](https://contentmoderator.cognitive.microsoft.com) 웹 사이트는 이러한 서비스에 대한 자세한 액세스를 제공하는 사용자 친화적인 프런트 엔드입니다.

## <a name="what-it-does"></a>수행하는 작업

[검토 도구](https://contentmoderator.cognitive.microsoft.com)가 머신 지원 조정 API와 함께 사용될 때 콘텐츠 조정 프로세스의 다음과 같은 작업을 수행할 수 있습니다.

- 하나의 도구 집합을 사용하여 여러 형식(텍스트, 이미지, 비디오)의 콘텐츠를 조정합니다.
- 조정 API 결과가 나올 때 사용자 [검토](../review-api.md#reviews)를 자동화하여 만듭니다.
- 콘텐츠 검토를 콘텐츠 범주 또는 경험 수준에 따라 구성된 여러 검토 팀에 할당 또는 에스컬레이션합니다.
- 기본 또는 사용자 지정 논리 필터([워크플로](../review-api.md#workflows))를 사용하여 코드를 작성하지 않고 콘텐츠를 정렬하고 추적합니다.
- [커넥터](./configure.md#connectors) 를 사용 하 여 Content Moderator api 외에 Microsoft 사진 Dna, 언어 및 얼굴 서비스를 사용 하 여 콘텐츠를 처리 합니다.
- 콘텐츠 수정 프로세스에 대한 주요 성능 메트릭을 가져옵니다.

## <a name="review-tool-dashboard"></a>검토 도구 대시보드

**대시보드** 탭에서는 도구 내에서 수행된 콘텐츠 검토에 대한 주요 메트릭을 확인할 수 있습니다. 이미지, 텍스트, 비디오 콘텐츠에 대한 전체, 완료, 보류 중인 검토 수를 확인합니다. 

**보류 중인 검토** 표에는 보류 중이거나 완료된 검토가 있는 사용자 및 하위 팀의 분석과 남아 있는 SLA 시간이 함께 표시됩니다. 표에서 항목을 선택하여 해당 검토로 이동할 수 있습니다. 표 위의 검색 상자를 사용하면 팀 이름을 기준으로 결과를 필터링할 수 있고 **필터** 아이콘을 사용하면 다른 메트릭을 기준으로 필터링할 수 있습니다.

**완료된 검토** 탭으로 전환하면 사용자 및 하위 팀이 처리하거나 완료한 총 항목 수가 표시됩니다. 보류 중인 검토와 동일하게 이 데이터를 필터링할 수 있습니다.

대시보드의 오른쪽 위 모서리에 있는 텍스트를 클릭하면 각 콘텐츠 형식에 대해 완료된 검토 수를 보고하는 일일 개인 메트릭이 표시됩니다.

> [!div class="mx-imgBorder"]
> ![브라우저의 검토 도구 대시보드](images/0-dashboard.png)

## <a name="review-tool-credentials"></a>검토 도구 자격 증명

[검토 도구](https://contentmoderator.cognitive.microsoft.com)로 등록할 때 계정에 대한 Azure 지역을 선택하라는 메시지가 표시됩니다. 이는 [검토 도구](https://contentmoderator.cognitive.microsoft.com)에서 Azure Content Moderator 서비스용 평가판 키를 생성하기 때문입니다. REST 호출 또는 클라이언트 SDK에서 서비스에 액세스하려면 이 키가 필요합니다. **관리자** > **자격 증명** 을 선택하여 키 및 API 엔드포인트 URL을 볼 수 있습니다.

> [!div class="mx-imgBorder"]
> ![Content Moderator 자격 증명](images/settings-6-credentials.png)

## <a name="next-steps"></a>다음 단계

검토 도구 리소스에 액세스하고 설정을 변경하는 방법을 알아보려면 [검토 도구 구성](./configure.md)을 참조하세요.