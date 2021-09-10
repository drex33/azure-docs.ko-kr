---
title: vNet용 디스크 풀 계획 참고 사항
description: Azure VMware Solution 호스트에 더 가까운 vNet 배포의 중요성에 대한 중요 참고 사항입니다.
ms.topic: include
ms.date: 07/14/2021
author: shortpatti
ms.author: v-patsho
ms.service: azure-vmware
ms.openlocfilehash: ddee39189fd93d5651e8e879375b977a4222e9fc
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121729115"
---
[Azure 디스크 풀](../../virtual-machines/disks-pools.md)을 사용하여 Azure VMware Solution 호스트를 스케일링하려는 경우 ExpressRoute 가상 네트워크 게이트웨이를 사용하여 호스트와 가까운 vNet을 배포하는 것이 중요합니다. 스토리지가 호스트에 가까울수록 성능이 향상됩니다.
