---
title: Azure Video Analyzer로 재생하기 위해 비디오 녹화
description: 이 문서에서는 Azure Video Analyzer로 재생하기 위해 비디오를 녹화하는 방법을 설명합니다.
ms.service: azure-video-analyzer
ms.topic: conceptual
ms.date: 09/30/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 94ac0847b4cb4783ad16044514960b88c67d2a03
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131073722"
---
# <a name="record-video-for-playback"></a>재생을 위해 비디오 녹화

CCTV 카메라용 비디오 관리 시스템 컨텍스트에서 비디오 녹화는 카메라에서 영상을 캡처해 모바일과 브라우저 앱을 통해 나중에 볼 수 있도록 기록하는 과정을 말합니다. 비디오 녹화는 연속 비디오 녹화 및 이벤트 기반 비디오 녹화로 분류할 수 있습니다.

## <a name="continuous-video-recording"></a>연속 비디오 녹화

CVR(연속 비디오 녹화)은 비디오 소스에서 캡처되는 모든 비디오를 지속적으로 녹화하는 프로세스를 말합니다. CVR은 나중에 분석 및/또는 감사하기 위해 원하는 비디오 **[기간(보존 정책에](#retention-policy)** 따라 결정)을 사용할 수 있도록 합니다.

## <a name="event-based-video-recording"></a>이벤트 기반 비디오 녹화

EVR(이벤트 기반 비디오 녹화)은 이벤트에 의해 트리거되는 비디오 녹화 프로세스를 말합니다. 문제의 이벤트는 비디오 신호 자체 처리(예: 동작 감지)로 인해 발생하거나 독립적인 소스(예: 도어 센서)에서 발생할 수 있습니다. EVR은 스토리지 절감 효과를 가져올 수 있지만, 이벤트를 생성하고 비디오 녹화를 트리거하는 추가 구성 요소가 필요합니다. 즉, EVR을 사용하려면 비디오 녹화를 트리거해야 하는 이벤트 및 비디오 녹화 기간과 관련된 추가 의사 결정을 수행해야 합니다. 이벤트 시간 30초 전부터 2분간 비디오를 녹화하는 경우를 예로 들 수 있습니다.

## <a name="choosing-recording-modes"></a>녹화 모드 선택

CVR 또는 EVR 중 무엇을 사용할지는 비즈니스 목표에 따라 달라집니다. AVA(Azure Video Analyzer)는 CVR 및 EVR 모두에 대한 플랫폼 기능을 제공합니다. **[CVR](continuous-video-recording.md)** 및 **[EVR](event-based-video-recording-concept.md)** 시나리오 문서에서 자세히 알아볼 수 있습니다.

## <a name="retention-policy"></a>보존 정책

보존 정책은 보존되는 비디오 녹화의 길이 또는 기간을 결정하는 정책을 나타냅니다. 보존 정책을 사용하면 스토리지 비용과 비즈니스 요구 사항의 균형을 맞출 수 있습니다. CVR 시나리오의 경우 보존 정책은 비디오를 저장해야 하는 기간(예: 지난 7일)을 정의합니다. 이에 대한 자세한 내용은 **[보존 정책 관리](manage-retention-policy.md)** 문서에서 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [EVR 시나리오에 대해 알아보기](event-based-video-recording-concept.md)
- [CVR 시나리오에 대해 알아보기](continuous-video-recording.md)
