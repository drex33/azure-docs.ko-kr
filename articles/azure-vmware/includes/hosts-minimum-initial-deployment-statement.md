---
title: 최소 초기 호스트 배포
description: 최소 초기 배포는 세 개의 호스트입니다.
ms.topic: include
ms.date: 04/28/2021
author: shortpatti
ms.author: v-patsho
ms.service: azure-vmware
ms.openlocfilehash: d0efae1a8f3bf30360d2649b9d638f5cc800179f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121747621"
---
<!-- Used in plan-private-cloud-deployment.md and concepts-private-clouds-clusters.md -->

만들어진 프라이빗 클라우드 하나마다 기본적으로 하나의 vSAN 클러스터가 있습니다. 클러스터를 추가, 삭제 및 스케일링할 수 있습니다.  클러스터당 최소 호스트 수 및 초기 배포 수는 3개입니다. 

평가판 클러스터는 평가용으로 제공되며 프라이빗 클라우드당 호스트 3개로 제한됩니다.

vSphere와 NSX-T Manager를 사용하여 클러스터 구성 또는 작업의 다른 대부분의 측면을 관리합니다. 클러스터의 각 호스트에 있는 모든 로컬 스토리지는 vSAN이 제어합니다.

>[!TIP]
>초기 배포 수를 초과해야 하는 경우 언제든지 나중에 클러스터를 확장하고 클러스터를 추가할 수 있습니다.
