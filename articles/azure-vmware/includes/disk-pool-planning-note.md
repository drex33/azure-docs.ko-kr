---
title: vNet용 디스크 풀 계획 참고 사항
description: Azure VMware Solution 호스트에 더 가까운 vNet 배포의 중요성에 대한 중요 참고 사항입니다.
ms.topic: include
ms.date: 07/14/2021
author: suzizuber
ms.author: v-szuber
ms.service: azure-vmware
ms.openlocfilehash: 04bd1b8b4faf697084dad49fa927394deaad3572
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129638578"
---
[Azure 디스크 풀](../../virtual-machines/disks-pools.md)을 사용하여 Azure VMware Solution 호스트를 스케일링하려는 경우 ExpressRoute 가상 네트워크 게이트웨이를 사용하여 호스트와 가까운 vNet을 배포하는 것이 중요합니다. 스토리지가 호스트에 가까울수록 성능이 향상됩니다.
