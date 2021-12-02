---
title: Azure Video Analyzer for Media(이전의 Video Indexer) 장애 조치(failover) 및 재해 복구
titleSuffix: Azure Video Analyzer for Media
description: 지역 데이터 센터 오류 또는 재해가 발생한 경우 보조 Azure Video Analyzer for Media(이전의 Video Indexer) 계정으로 장애 조치하는 방법에 대해 알아보세요.
services: azure-video-analyzer
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.workload: ''
ms.topic: article
ms.subservice: azure-video-analyzer-media
ms.custom: ''
ms.date: 07/29/2019
ms.author: juliako
ms.openlocfilehash: e2aae925f6d9e4897f3ac6e7b5cdf64ce6f02030
ms.sourcegitcommit: 93c7420c00141af83ed3294923b4826dd4dc6ff2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133435093"
---
# <a name="video-analyzer-for-media-failover-and-disaster-recovery"></a>Video Analyzer for Media 장애 조치 및 재해 복구

Azure Video Analyzer for Media(이전의 Video Indexer)는 지역 데이터 센터에 중단 또는 오류가 발생한 경우 서비스의 즉각적인 장애 조치를 제공하지 않습니다. 이 문서에서는 재해 발생 시 앱에 대한 최적의 가용성을 보장하고 복구 시간을 최소화하도록 장애 조치(failover) 환경을 구성하는 방법을 설명합니다.

Azure의 격리 및 가용성 정책을 활용하려면 지역 쌍 간에 BCDR(비즈니스 연속성 및 재해 복구)을 구성하는 것이 좋습니다. 자세한 내용은 [Azure 쌍을 이루는 지역](../../availability-zones/cross-region-replication-azure.md)을 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

Azure 구독 Azure 구독이 없는 경우 [Azure 무료 평가판](https://azure.microsoft.com/free/)을 등록하세요.

## <a name="failover-to-a-secondary-account"></a>보조 계정에 장애 조치(failover)

BCDR을 구현하려면 중복도를 처리하기 위해 두 개의 Video Analyzer for Media 계정이 있어야 합니다.

1. Azure에 연결된 두 개의 Video Analyzer for Media 계정을 만듭니다([Video Analyzer for Media 계정 만들기](connect-to-azure.md) 참조). 주 지역에 대한 계정 및 Azure 쌍을 이루는 지역에 대한 계정을 생성하세요.
1. 주 지역에 오류가 있는 경우 보조 계정을 사용하여 인덱싱으로 전환하세요.

> [!TIP]
> [서비스 알림에 대한 활동 로그 경고 생성](../../service-health/alerts-activity-log-service-notifications-portal.md)에 따라 서비스 상태 알림에 대한 작업 로그 경고를 설정하여 BCDR을 자동화할 수 있습니다.

다중 테넌트 사용에 대한 자세한 내용은 [다중 테넌트 관리](manage-multiple-tenants.md)를 참조하세요. BCDR을 구현하려면 [테넌트당 Video Analyzer for Media 계정](./manage-multiple-tenants.md#video-analyzer-for-media-account-per-tenant) 또는 [테넌트당 Azure 구독](./manage-multiple-tenants.md#azure-subscription-per-tenant) 옵션 중 하나를 선택하세요.

## <a name="next-steps"></a>다음 단계

[Azure에 연결된 Video Analyzer for Media 계정 관리](manage-account-connected-to-azure.md).
