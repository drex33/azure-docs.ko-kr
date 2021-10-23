---
title: Azure Kinect 신체 추적 관절
description: Azure Kinect DK에서 신체 프레임, 관절, 관절 좌표, 관절 계층 구조를 이해합니다.
author: qm13
ms.author: quentinm
ms.reviewer: cedmonds, abalan
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect, 이식, 신체, 추적, 관절, 계층 구조, 뼈, 연결
ms.openlocfilehash: 2616573d0474addb14abe5f6e932181b097bed46
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130223458"
---
# <a name="azure-kinect-body-tracking-joints"></a>Azure Kinect 신체 추적 관절

Azure Kinect 신체 추적은 동시에 여러 인체를 추적할 수 있습니다. 각 신체에는 프레임과 운동학적 골격 간의 시간에 따른 상관 관계에 대한 ID가 포함됩니다. 각 프레임에서 검색된 신체 수는 `k4abt_frame_get_num_bodies()`를 사용하여 얻을 수 있습니다.

## <a name="joints"></a>관절

관절 위치와 방향은 참조의 전체 깊이 센서 프레임에 상대적인 예상치입니다. 위치는 밀리미터 단위로 지정됩니다. 방향은 정규화된 쿼터니언으로 표현됩니다.

## <a name="joint-coordinates"></a>관절 좌표

각 조인의 위치와 방향은 고유한 오른쪽 좌표계를 형성합니다. 모든 조인 좌표계는 깊이 카메라 3D 좌표계의 절대 좌표계입니다.

> [!NOTE]
> 본문의 양쪽에 걸쳐 있는 해당 조인에 대해 대칭 이동 축 방향을 선택하는 것은 미러 이동을 간소화하기 위한 것입니다. 예를 들어 상용 아바타, 게임 엔진 및 렌더링 소프트웨어에서 공통적으로 두 개의 암을 +20도 높이도록 합니다.

![관절 좌표](./media/concepts/joint-coordinates.png)

범례: | x축 = 빨간색 | y축 = 녹색 | z축 = 파란색 |

## <a name="joint-hierarchy"></a>관절 계층 구조

골격에는 신체의 중앙에서 말단으로 흐르는 관절 계층 구조와 함께 32개의 관절이 포함됩니다. 각 연결(뼈)은 부모 관절을 자식 관절과 연결합니다. 그림에서는 인체와 관련된 관절 위치 및 연결을 보여 줍니다.

![관절 계층 구조](./media/concepts/joint-hierarchy.png)

다음 표에는 표준 관절 연결이 열거되어 있습니다.

|인덱스 |관절 이름     | 부모 관절   |
|------|---------------|----------------|
| 0    |PELVIS         | -              |
| 1    |SPINE_NAVAL    | PELVIS         |
| 2    |SPINE_CHEST    | SPINE_NAVAL    |
| 3    |NECK           | SPINE_CHEST    |
| 4    |CLAVICLE_LEFT  | SPINE_CHEST    |
| 5    |SHOULDER_LEFT  | CLAVICLE_LEFT  |
| 6    |ELBOW_LEFT     | SHOULDER_LEFT  |
| 7    |WRIST_LEFT     | ELBOW_LEFT     |
| 8    |HAND_LEFT      | WRIST_LEFT     |
| 9    |HANDTIP_LEFT   | HAND_LEFT      |
| 10   |THUMB_LEFT     | WRIST_LEFT     |
| 11   |CLAVICLE_RIGHT | SPINE_CHEST    |
| 12   |SHOULDER_RIGHT | CLAVICLE_RIGHT |
| 13   |ELBOW_RIGHT    | SHOULDER_RIGHT |
| 14   |WRIST_RIGHT    | ELBOW_RIGHT    |
| 15   |HAND_RIGHT     | WRIST_RIGHT    |
| 16   |HANDTIP_RIGHT  | HAND_RIGHT     |
| 17   |THUMB_RIGHT    | WRIST_RIGHT    |
| 18   |HIP_LEFT       | PELVIS         |
| 19   |KNEE_LEFT      | HIP_LEFT       |
| 20   |ANKLE_LEFT     | KNEE_LEFT      |
| 21   |FOOT_LEFT      | ANKLE_LEFT     |
| 22   |HIP_RIGHT      | PELVIS         |
| 23   |KNEE_RIGHT     | HIP_RIGHT      |
| 24   |ANKLE_RIGHT    | KNEE_RIGHT     |
| 25   |FOOT_RIGHT     | ANKLE_RIGHT    |
| 26   |HEAD           | NECK           |
| 27   |NOSE           | HEAD           |
| 28   |EYE_LEFT       | HEAD           |
| 29   |EAR_LEFT       | HEAD           |
| 30   |EYE_RIGHT      | HEAD           |
| 31   |EAR_RIGHT      | HEAD           |

## <a name="next-steps"></a>다음 단계

[신체 추적 인덱스 맵](body-index-map.md)
