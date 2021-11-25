---
title: Load Balancer에 대한 Azure CLI 샘플
titleSuffix: Azure Load Balancer
description: Azure CLI 샘플
services: load-balancer
documentationcenter: load-balancer
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18, devx-track-azurecli
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 06/14/2018
ms.author: allensu
ms.openlocfilehash: fcc2a579f2fe9048dd58cd8b52c8c704c894936b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "87499350"
---
# <a name="azure-cli-samples-for-load-balancer"></a>Load Balancer에 대한 Azure CLI 샘플

다음 테이블은 Azure CLI를 사용하여 빌드된 bash 셸에 대한 링크를 포함합니다.

* [고가용성을 위해 VM에 트래픽 부하 분산](./scripts/load-balancer-linux-cli-sample-nlb.md)

  고가용성의 부하가 분산된 구성으로 여러 가상 머신을 만듭니다.

* [가용성 영역에 VM 부하 분산](./scripts/load-balancer-linux-cli-sample-zone-redundant-frontend.md)

  한 영역 내의 서로 다른 가용성 영역에 VM 3대를 만들고 영역 중복 프런트 엔드 IP 주소를 사용하는 표준 Load Balancer를 만듭니다. 이 부하 분산 장치 구성은 가능성은 낮지만 전체 데이터 센터가 중지되는 사고로부터 앱과 데이터를 보호합니다.

* [특정 가용성 영역 내에 VM 부하 분산](./scripts/load-balancer-linux-cli-sample-zonal-frontend.md)

  VM 3대를 만들고, 영역별 프런트 엔드 IP 주소를 사용하여 지정된 지역의 단일 영역에서 데이터 경고와 리소스를 정렬하는 표준 Load Balancer를 만듭니다.

* [VM에서 여러 웹 사이트에 부하 분산](./scripts/load-balancer-linux-cli-load-balance-multiple-websites-vm.md)

  Azure 가용성 집합에 연결되고 Azure Load Balancer를 통해 액세스할 수 있는 2개의 VM을 여러 IP 구성을 사용해서 만듭니다.
