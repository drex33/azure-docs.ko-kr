---
title: Microsoft Azure FXT Edge Filer 유닛을 종료하는 방법
description: 클러스터 제어판 소프트웨어를 사용하여 Azure FXT Edge Filer 노드의 시작 및 안전 종료 절차를 알아봅니다.
author: femila
ms.service: fxt-edge-filer
ms.topic: how-to
ms.date: 07/01/2019
ms.author: femila
ms.openlocfilehash: c3b9f9c317de7ff1bdf2801d1348769882cb777b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131024242"
---
# <a name="how-to-safely-power-off-azure-fxt-edge-filer-hardware"></a>Azure FXT Edge Filer 하드웨어 전원을 안전하게 끄는 방법

실제 전원 단추를 사용하여 개별 노드를 켤 수 있지만, 일반적인 상황에서 유닛을 종료하는 데 사용하면 안 됩니다.

Azure FXT Edge Filer 노드가 클러스터의 일부로 사용 중이면 클러스터 제어판 소프트웨어를 사용하여 하드웨어를 종료해야 합니다.

> [!NOTE]
> 데이터 손실이나 손상을 방지하려면, 항상 제어판 소프트웨어를 사용하여 Azure FXT Edge Filer를 종료합니다. Microsoft 고객 서비스 및 지원팀에서 이 작업을 수행 하도록 지시하지 않는 한 종료를 위해 물리적 전원 단추를 사용하지 마세요.
>
> 정전 비상 상황에서는 전원 코드를 분리하거나 데이터 센터의 전원 분리 메커니즘을 사용합니다.

## <a name="shut-down-a-node-from-the-control-panel"></a>제어판에서 노드 종료

다음 지침에 따라 Azure FXT Edge Filer 노드의 전원을 안전하게 끕니다.

1. 클러스터 제어판에 로그인합니다. ([설정 페이지 열기](cluster-create.md#open-the-settings-pages)의 지시)
1. **설정** 탭을 클릭한 다음 **클러스터** > **FXT Nodes** 페이지를 로드합니다.
1. 클러스터 노드 목록에서 종료하려는 클러스터 노드를 찾습니다. **작업** 열에서 **전원 끄기** 단추를 클릭합니다.
1. 잠시 기다립니다. 노드가 종료되고 전원이 꺼집니다.

## <a name="next-steps"></a>다음 단계

* [Azure FXT Edge Filer 하드웨어 상태 모니터링](monitor.md)의 상태 LED 및 기타 표시기에 대해 알아봅니다.
* [전원 케이블 연결](network-power.md#connect-power-cables)의 Azure FXT Edge Filer 전원 공급 장치에 대해 자세히 알아보세요.
