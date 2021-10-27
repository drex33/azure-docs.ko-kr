---
title: Azure Database for PostgreSQL - 하이퍼스케일(Citus)의 제품 업데이트
description: 미리 보기의 새로운 기능
author: jonels-msft
ms.author: jonels
ms.custom: mvc
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: overview
ms.date: 10/15/2021
ms.openlocfilehash: aed4439cf608c5080d5c429a4f3af957bd77ccdb
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130070131"
---
# <a name="product-updates-for-postgresql---hyperscale-citus"></a>PostgreSQL - 하이퍼스케일(Citus)의 제품 업데이트

## <a name="updates-feed"></a>업데이트 피드

Microsoft Azure 웹 사이트에는 제품별로 새롭게 사용할 수 있는 기능과 미리 보기 및 개발 기능이 나열되어 있습니다. 최신 [하이퍼스케일(Citus) 업데이트](https://azure.microsoft.com/updates/?category=databases&query=citus) 섹션을 확인합니다. 해당 페이지에서 RSS 피드도 사용할 수 있습니다.

## <a name="features-in-preview"></a>미리 보기의 기능

Azure Database for PostgreSQL - 하이퍼스케일(Citus)은 출시되지 않은 기능에 대한 미리 보기를 제공합니다. 미리 보기 버전은 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 권장되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.  자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

현재 미리 보기로 제공되는 기능은 다음과 같습니다.

* **[pgAudit](concepts-hyperscale-audit.md)** . 표준 PostgreSQL 로깅 기능을 통해 자세한 세션 및 개체 감사 로깅을 제공합니다. 특정 정부, 금융 또는 ISO 인증 감사를 전달하는 데 필요한 감사 로그를 생성합니다.
* **[프라이빗 액세스](concepts-hyperscale-private-access.md)**.
  VNet(가상 네트워크)의 호스트가 프라이빗 엔드포인트를 통해 하이퍼스케일(Citus) 서버 그룹에 안전하게 액세스할 수 있도록 허용합니다.

> [!NOTE]
>
> 프라이빗 액세스는 [특정 지역](concepts-hyperscale-limits.md#regions)에서만 미리 볼 수 있습니다.

## <a name="contact-us"></a>문의처

[Azure DB for PostgreSQL 문의](mailto:AskAzureDBforPostgreSQL@service.microsoft.com)에 이메일을 보내 미리 보기 기능 사용 경험을 알려주세요.
(이 이메일 주소는 기술 지원 채널이 아닙니다. 기술 문제를 문의하려면 [지원 요청](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)을 여세요.)
