---
title: 최소 초기 호스트 배포
description: 최소 초기 배포는 세 개의 호스트입니다.
ms.topic: include
ms.date: 09/29/2021
author: suzizuber
ms.author: v-szuber
ms.service: azure-vmware
ms.openlocfilehash: 76b24de732f9668580d5a42f4279d9882a9ae702
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129638544"
---
<!-- Used in plan-private-cloud-deployment.md and concepts-private-clouds-clusters.md -->

기본적으로 생성된 프라이빗 클라우드마다 vSAN 클러스터가 하나 있습니다. 클러스터를 추가, 삭제 및 스케일링할 수 있습니다.  클러스터당 최소 호스트 수 및 초기 배포 수는 3개입니다. 

vSphere와 NSX-T Manager를 사용하여 클러스터 구성 또는 작업의 다른 대부분의 측면을 관리합니다. 클러스터의 각 호스트에 있는 모든 로컬 스토리지는 vSAN이 제어합니다.

>[!TIP]
>초기 배포 수를 초과해야 하는 경우 언제든지 나중에 클러스터를 확장하고 클러스터를 추가할 수 있습니다.
