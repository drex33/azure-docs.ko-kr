---
title: Custom Vision의 새로운 기능
titleSuffix: Azure Cognitive Services
description: 이 문서에는 Custom Vision에 대한 뉴스가 포함되어 있습니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: overview
ms.date: 09/27/2021
ms.author: pafarley
ms.openlocfilehash: 677a5ff8214c8f43ec5dfe1d6d6e73b1d03b2650
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129356958"
---
# <a name="whats-new-in-custom-vision"></a>Custom Vision의 새로운 기능

서비스의 새로운 기능에 대해 알아봅니다. 이러한 항목은 릴리스 정보, 비디오, 블로그 게시물 및 기타 유형의 정보입니다. 서비스를 최신 상태로 유지하려면 이 페이지에 대한 책갈피를 지정하세요.


## <a name="october-2020"></a>2020년 10월 

### <a name="custom-base-model"></a>사용자 지정 기본 모델

- 일부 애플리케이션은 모델을 별도로 미세 조정해야 한다는 조건이 붙지만 대량의 공동 학습 데이터를 제공합니다. 따라서 사소한 차이가 있는 여러 원본의 이미지 성능이 향상됩니다. 이 예제에서는 평소와 같이 대량의 학습 데이터를 사용하여 첫 번째 모델을 학습시킬 수 있습니다. 그런 다음, 요청 본문에서 _CustomBaseModelInfo_ 를 통해 3.4 공개 미리 보기 API에서 **TrainProject** 를 호출하여 첫 번째 학습 단계를 마친 모델을 다운스트림 프로젝트의 기본 모델로 사용합니다. 원본 프로젝트와 다운스트림 대상 프로젝트의 이미지 특징이 비슷한 경우 보다 나은 성능을 기대할 수 있습니다. 

### <a name="new-domain-information"></a>새 도메인 정보

- 이제 Custom Vision 3.4 공개 미리 보기 API의 **GetDomains** 에서 반환된 도메인 정보에는 지원되는 내보내기 가능한 플랫폼, 모델 아키텍처에 대한 간단한 설명 및 컴팩트 도메인의 모델 크기가 포함됩니다.

### <a name="training-divergence-feedback"></a>학습 차이 피드백

- Custom Vision 3.4 공개 미리 보기 API는 이제 **GetIteration** 호출에서 **TrainingErrorDetails** 를 반환합니다. 반복이 실패할 경우 이를 통해 실패의 원인이 학습 차이인지 알려주며, 보다 높은 품질의 학습 데이터를 더 많이 제공하면 이 문제를 해결할 수 있습니다.

## <a name="july-2020"></a>2020년 7월

### <a name="azure-role-based-access-control"></a>Azure 역할 기반 액세스 제어

* Custom Vision은 Azure 리소스에 대한 개별 액세스를 관리하는 권한 부여 시스템인 Azure RBAC(Azure 역할 기반 액세스 제어)를 지원합니다. Custom Vision 프로젝트에 대한 액세스를 관리하는 방법을 알아보려면 [Azure 역할 기반 액세스 제어](./role-based-access-control.md)를 참조하세요.

### <a name="subset-training"></a>하위 집합 학습

* 개체 감지 프로젝트를 학습하는 경우 적용된 태그의 하위 집합에 대해서만 선택적으로 학습을 수행할 수 있습니다. 특정 태그는 아직 충분히 적용하지 않았지만 다른 태그는 충분히 적용한 경우 이 작업을 수행할 수 있습니다. 자세히 알아보려면 C# 또는 Python의 [클라이언트 라이브러리 빠른 시작](./quickstarts/object-detection.md)을 참조하세요.

### <a name="azure-storage-notifications"></a>Azure 스토리지 알림

* Custom Vision 프로젝트를 Azure Blob Storage 큐와 통합하여 프로젝트 학습/내보내기 작업 및 게시된 모델의 백업 복사본에 대한 푸시 알림을 받을 수 있습니다. 이 기능은 작업이 오래 실행될 때 결과를 위해 서비스를 지속적으로 폴링하는 것을 방지하는 데 유용합니다. 대신 스토리지 큐 알림을 워크플로에 통합할 수 있습니다. 자세히 알아보려면 [스토리지 통합](./storage-integration.md) 가이드를 참조하세요.

### <a name="copy-and-move-projects"></a>프로젝트 복사 및 이동

* 이제 한 Custom Vision 계정에서 다른 계정으로 프로젝트를 복사할 수 있습니다. 개발 환경에서 프로덕션 환경으로 프로젝트를 이동하거나 데이터 보안을 강화하기 위해 프로젝트를 다른 Azure 지역의 계정으로 백업할 수 있습니다. 자세한 내용은 [프로젝트 복사 및 이동](./copy-move-projects.md) 가이드를 참조하세요.

## <a name="september-2019"></a>2019년 9월

### <a name="suggested-tags"></a>제안된 태그

* [Custom Vision 웹 사이트](https://www.customvision.ai/)의 Smart Labeler 도구는 학습 이미지에 대한 제안된 태그를 생성합니다. 이렇게 하면 Custom Vision 모델을 학습할 때 많은 수의 이미지에 더 빠르게 레이블을 지정할 수 있습니다. 이 기능을 사용하는 방법에 대한 지침은 [제안된 태그](./suggested-tags.md)를 참조하세요.

## <a name="cognitive-service-updates"></a>Cognitive Service 업데이트

[Cognitive Services에 대한 Azure 업데이트 공지](https://azure.microsoft.com/updates/?product=cognitive-services)