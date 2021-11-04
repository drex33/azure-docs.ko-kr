---
title: Azure 비정상 스튜디오의 비정상 실험
description: Azure의 비정상 스튜디오에서 비정상 실험의 개념을 이해 합니다. 비정상 실험의 부분은 무엇입니까? 비정상 실험을 어떻게 만들 수 있나요?
author: johnkemnetz
ms.author: johnkem
ms.service: chaos-studio
ms.topic: conceptual
ms.date: 11/01/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: 2c84b9908eb361b4dfb57bb87d2d6fe2f828982d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131102772"
---
# <a name="chaos-experiments"></a>비정상 실험

비정상 스튜디오에서는 비정상 실험을 만들고 실행 합니다. 비정상 실험은 실행 해야 하는 오류와 이러한 오류를 실행 해야 하는 리소스를 설명 하는 Azure 리소스입니다. 실험은 다음 두 섹션으로 구분 됩니다.
- **선택기**: 선택기는 오류 또는 다른 작업을 실행 하는 대상 리소스의 그룹입니다. 선택기를 사용 하면 여러 작업에서 다시 사용 하기 위해 리소스를 논리적으로 그룹화 할 수 있습니다. 예를 들어 미국 동부에 모든 비프로덕션 가상 머신을 추가 하는 "AllNonProdEastUSVMs" 이라는 선택기를 사용할 수 있습니다. 그런 다음 선택기를 참조 하는 것과 같이 CPU 압력 다음에 가상 메모리 압력을 적용할 수 있습니다.
- **논리**: 실험의 나머지 부분에서는 오류를 실행 하는 방법과 시기에 대해 설명 합니다. 실험은 서로를 실행 하는 여러 **단계로** 구성 됩니다. 각 단계에는 동시에 실행 되는 하나 이상의 **분기가** 있습니다. 단계와 분기를 사용 하면 사용자 환경의 리소스에서 여러 오류를 병렬로 삽입할 수 있습니다. 각 분기에는 하나 이상의 **작업** (실행 하려는 오류 또는 시간 지연)이 있습니다. **오류** 는 약간의 중단을 야기 하는 작업입니다. 대부분의 오류는 오류를 실행 하는 기간 또는 적용할 스트레스의 양과 같은 하나 이상의 **매개 변수** 를 사용 합니다.

![비정상 실험의 레이아웃을 보여 주는 다이어그램입니다.](images/chaos-experiment.png)

## <a name="cross-subscription-and-cross-tenant-experiments"></a>구독 간 및 교차 테 넌 트 실험

비정상 실험은 구독, 리소스 그룹 및 지역에 배포 된 Azure 리소스입니다. Azure Portal 또는 비정상 스튜디오 REST API를 사용 하 여 실험의 상태를 만들고, 업데이트 하 고, 시작 하 고, 취소 하 고, 볼 수 있습니다.

비정상 실험은 구독이 동일한 Azure 테 넌 트 내에 있는 한 실험 이외의 다른 구독에 있는 리소스를 대상으로 할 수 있습니다. 비정상 실험은 해당 지역이 비정상 스튜디오에서 지원 되는 영역인 경우 실험과 다른 지역의 리소스를 대상으로 할 수 있습니다.

## <a name="next-steps"></a>다음 단계
이제 비정상 실험의 준비를 이해 했으므로 다음을 수행할 수 있습니다.
- [오류 및 작업에 대 한 자세한 정보](chaos-studio-faults-actions.md)
- [첫 번째 실험 만들기 및 실행](chaos-studio-tutorial-service-direct.md)
