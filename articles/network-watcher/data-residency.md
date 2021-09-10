---
title: Azure Network Watcher에 대한 데이터 보존 | Microsoft Docs
description: 이 문서는 Azure Network Watcher 서비스에 대한 데이터 보존을 이해하는 데 도움이 됩니다.
services: network-watcher
documentationcenter: na
author: damendo
manager: ''
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: 638c466e0d8573ce0b23f31188c5e952a28f74b2
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/16/2021
ms.locfileid: "112240460"
---
# <a name="data-residency-for-azure-network-watcher"></a>Azure Network Watcher에 대한 데이터 보존
연결 모니터 서비스를 제외하고 Azure Network Watcher는 고객 데이터를 저장하지 않습니다.


## <a name="connection-monitor-data-residency"></a>연결 모니터 데이터 보존
연결 모니터 서비스는 고객 데이터를 저장합니다. 이 데이터는 Network Watcher에 의해 단일 지역에 자동으로 저장됩니다. 따라서 연결 모니터는 [보안 센터](https://azuredatacentermap.azurewebsites.net/)에 지정된 요구 사항을 포함하여 지역 데이터 보존 요구 사항을 자동으로 충족합니다.

## <a name="data-residency"></a>데이터 상주
Azure에서 단일 지역에 고객 데이터를 저장할 수 있는 기능은 현재 아시아 태평양 지역의 동남 아시아 지역(싱가포르) 및 브라질 지역의 브라질 남부(상파울루주)에서만 사용할 수 있습니다. 다른 모든 지역의 경우 고객 데이터는 지역에 저장됩니다. 자세한 내용은 [보안 센터](https://azuredatacentermap.azurewebsites.net/)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Network Watcher](./network-watcher-monitoring-overview.md) 개요를 읽어 보세요.
