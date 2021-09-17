---
title: Azure SignalR Service 제한 테이블
description: Azure SignalR Service를 위한 시스템 제한을 설명합니다.
services: signalr
documentationcenter: signalr
author: sffamily
manager: cfowler
editor: ''
ms.service: signalr
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/11/2020
ms.author: zhshang
ms.openlocfilehash: f5b197448529e953d5d7d188b00a88d60e87e900
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123454156"
---
| 리소스 | 기본 제한 | 최대 제한 | 
| --- | --- | --- |
| 무료 계층의 인스턴스당 Azure SignalR Service 단위 |1 |1 |
| 표준 계층의 인스턴스당 Azure SignalR Service 단위 |100 |100 |
| 무료 계층의 지역별 구독당 Azure SignalR Service 단위|5 |5 |
| 지역별 구독당 총 Azure SignalR Service 단위 수 |150 |제한 없음 |
| 무료 계층의 단위당 일일 연결 수 |20 |20 |
| 표준 계층의 단위당 일일 연결 수 |1,000 |1,000|
| 무료 계층의 단위당 일일 포함 메시지 수|20,000 |20,000 |
| 무료 계층의 일일 단일당 추가 메시지 수|0 |0 |
| 표준 계층의 단위당 일일 포함 메시지 수|1,000,000 |1,000,000 |
| 표준 계층의 일일 단일당 추가 메시지 수|제한 없음 |제한 없음 |

구독의 기본 한도에 대한 업데이트를 요청하려면 지원 티켓을 엽니다.

연결 및 메시지 계산 방법에 대한 자세한 내용은 [Azure SignalR Service의 메시지 및 연결](../articles/azure-signalr/signalr-concept-messages-and-connections.md)을 참조하세요.

요구 사항이 제한을 초과 하는 경우 무료 계층에서 표준 계층으로 전환 하 고 단위를 추가 합니다. 자세한 내용은 [Azure SignalR 서비스 인스턴스 크기를 조정 하는 방법](../articles/azure-signalr/signalr-howto-scale-signalr.md)을 참조 하세요. 

요구 사항이 단일 인스턴스의 제한을 초과 하는 경우 인스턴스를 추가 합니다. 자세한 내용은 [여러 인스턴스를 사용 하 여 SignalR 서비스를 확장 하는 방법](../articles/azure-signalr/signalr-howto-scale-multi-instances.md)을 참조 하세요.
