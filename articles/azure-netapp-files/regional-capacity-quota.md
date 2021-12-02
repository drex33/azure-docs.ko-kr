---
title: Azure NetApp Files에 대 한 지역 용량 할당량 Microsoft Docs
description: Azure NetApp Files의 지역 용량 할당량을 설명 합니다.
services: azure-netapp-files
documentationcenter: ''
author: b-hchen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/11/2021
ms.author: b-hchen
ms.openlocfilehash: 51702b7028374a92b1e4233a024bf571e091be6a
ms.sourcegitcommit: 9ef0965834870700468c822ddcafc011881fc2d5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133481236"
---
# <a name="regional-capacity-quota-for-azure-netapp-files"></a>Azure NetApp Files에 대 한 지역 용량 할당량

이 문서에서는 Azure NetApp Files의 지역 용량 할당량을 설명 합니다.

## <a name="display-regional-capacity-quota"></a>지역별 용량 할당량 표시

Azure NetApp Files의 설정에서 **할당량** 을 클릭하여 지역의 현재 및 기본 할당량 크기를 표시할 수 있습니다. 

예를 들면 다음과 같습니다. 

![할당량 정보를 표시하는 방법을 보여주는 스크린샷.](../media/azure-netapp-files/quota-display.png) 

## <a name="request-regional-capacity-quota-increase"></a>지역별 용량 할당량 증가 요청

추가 비용 없이 지원 용량 할당량을 늘리기 위해 [지원 요청을 제출](azure-netapp-files-resource-limits.md#request-limit-increase)할 수 있습니다. 제출한 지원 요청은 처리를 위해 Azure 용량 관리 팀에 전송됩니다. 일반적으로 영업일 기준 2일 이내에 응답을 받게 됩니다. 대용량 요청이 있는 경우 Azure 용량 관리 팀에서 연락할 수 있습니다.  

지역 용량 할당량 증가는 청구 증가를 발생시키지 않습니다. 요금은 여전히 프로비저닝된 용량 풀을 기반으로 합니다.
예를 들어 현재 프로비저닝된 용량이 25TiB인 경우 35TiB로 할당량 증가를 요청할 수 있습니다.  영업일 2일 이내에 할당량 증가가 요청된 지역에 적용됩니다. 할당량 증가가 적용되면 현재 프로비저닝된 용량(25TiB)에 대해서만 비용을 지불합니다. 하지만 추가 10TiB를 실제로 프로비저닝하는 경우 35TiB에 대한 요금이 청구됩니다.

Azure NetApp Files에 대한 현재 [리소스 제한](azure-netapp-files-resource-limits.md#resource-limits)은 변경되지 않습니다. 여전히 500TiB 용량 풀을 프로비저닝할 수 있습니다. 하지만 그 전에 지역 용량 할당량을 500TiB로 늘려야 합니다.

## <a name="next-steps"></a>다음 단계  

- [Azure NetApp Files에 대한 리소스 제한](azure-netapp-files-resource-limits.md)
- [Azure NetApp Files의 스토리지 계층 구조 이해](azure-netapp-files-understand-storage-hierarchy.md)
- [Azure NetApp Files 비용 모델](azure-netapp-files-cost-model.md)
