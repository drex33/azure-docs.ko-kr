---
title: Azure Chaos Studio의 Chaos 실험
description: Azure Chaos Studio의 비정상 상황에서의 실험 개념을 이해합니다. 비정형 실험의 일부는 무엇인가요? 비정실용성 실험을 만들려면 어떻게 해야 합니까?
author: johnkemnetz
ms.author: johnkem
ms.service: chaos-studio
ms.topic: conceptual
ms.date: 11/01/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: ef71d6d09d1965eefd7008dd8c3d020c0a8ae1ae
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132371546"
---
# <a name="chaos-experiments"></a>Chaos 실험

Chaos Studio에서 chaos 실험을 만들고 실행합니다. 카오스 실험은 실행해야 하는 오류와 해당 오류를 실행해야 하는 리소스를 설명하는 Azure 리소스입니다. 실험은 다음 두 섹션으로 나뉩니다.
- **선택기: 선택기는** 오류가 있거나 다른 작업이 실행되는 대상 리소스 그룹입니다. 선택기를 사용하면 여러 작업에서 다시 사용할 리소스를 논리적으로 그룹화할 수 있습니다. 예를 들어 미국 동부에 모든 비프로덕션 가상 머신을 추가한 "AllNonProdEastUSVMs"라는 선택기가 있을 수 있습니다. 그런 다음 선택기를 참조하기만 하면 CPU 압력과 가상 메모리 압력을 가상 머신에 적용할 수 있습니다.
- **논리:** 나머지 실험에서는 오류를 실행하는 방법과 시기를 설명합니다. 실험은 하나씩 실행되는 **단계로** 구성됩니다. 각 단계에는 동시에 실행되는 하나 이상의 **분기가** 있습니다. 단계 및 분기를 사용하면 환경의 리소스에 여러 오류를 병렬로 삽입할 수 있습니다. 각 분기에는 실행하려는 오류 또는 시간 지연인 하나 이상의 **작업이** 있습니다. **오류는** 일부 중단을 일으키는 작업입니다. 대부분의 오류는 오류 실행 기간 또는 적용할 스트레스 양과 같은 하나 이상의 **매개 변수를** 사용합니다.

![카오스 실험의 레이아웃을 보여 주는 다이어그램.](images/chaos-experiment.png)

## <a name="cross-subscription-and-cross-tenant-experiments"></a>구독 간 및 테넌트 간 실험

비정상 사고 실험은 구독, 리소스 그룹 및 지역에 배포된 Azure 리소스입니다. Azure Portal 또는 Chaos Studio REST API를 사용하여 실험 상태를 생성, 업데이트, 시작, 취소, 조회할 수 있습니다.

비정상 상황에서는 구독이 동일한 Azure 테넌트 내에 있는 한 실험과 다른 구독의 리소스를 대상으로 지정할 수 있습니다. Chaos 실험은 지역이 Chaos Studio에 대해 지원되는 지역인 한 실험과 다른 지역의 리소스를 대상으로 할 수 있습니다.

## <a name="next-steps"></a>다음 단계
이제 비정실 실험이 무엇인지 이해했습니다. 다음을 수행할 준비가 되었습니다.
- [오류 및 작업에 대해 알아보기](chaos-studio-faults-actions.md)
- [첫 번째 실험 만들기 및 실행](chaos-studio-tutorial-service-direct-portal.md)
