---
title: Azure Database for PostgreSQL - 유연한 서버 릴리스 정보
description: Azure Database for PostgreSQL 릴리스 정보 - 유연한 서버
author: sr-msft
ms.author: srranga
ms.custom: references_regions
ms.service: postgresql
ms.topic: overview
ms.date: 11/18/2021
ms.openlocfilehash: 672fa91d0fc6785332ace1d45f8e1c080895347e
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132721863"
---
# <a name="release-notes---azure-database-for-postgresql---flexible-server"></a>릴리스 정보 - Azure Database for PostgreSQL - 유연한 서버

이 페이지에서는 기능 추가, 엔진 버전 지원, 확장 및 유연한 서버인 PostgreSQL 관련 기타 공지 사항과 관련된 최신 뉴스와 업데이트를 제공합니다.

> [!IMPORTANT]
> Azure Database for PostgreSQL - 유연한 서버는 미리 보기로 제공됨

## <a name="release-november-2021"></a>릴리스: 2021년 11월

* 새 서버를 만들어 [최신 PostgreSQL 부](./concepts-supported-versions.md) 13.4, 12.8 및 11.13 지원<sup>$</sup>
* 선택된 쌍을 이루는(미국 동부 2, 미국 중부, 북유럽, 서유럽, 일본 동부 및 일본 서부)의 미리 보기에서 [지역 중복 백업 및 복원](concepts-backup-restore.md) 기능을 지원합니다.
*  높은 처리량 스트리밍 데이터를 기존 테이블에 수집하기 위해 [미리 보기에서 ASA(Azure Stream Analytics) 커넥터](https://techcommunity.microsoft.com/t5/analytics-on-azure/stream-analytics-updates-ignite-fall-2021-new-outputs-new/ba-p/2919170)를 지원합니다.
*  여러 버그 수정, 안정성 및 성능 향상<sup>$</sup>

<sup> **$**</sup> 새 서버는 이러한 기능을 자동으로 가져옵니다. 기존 서버에서 이러한 기능은 서버의 향후 유지 관리 기간 동안 사용하도록 설정됩니다.

## <a name="release-october-2021"></a>릴리스: 2021년 10월

*   [Ddsv4 및 Edsv4 SKU](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/flexible-server-now-supports-v4-compute-series-in-postgresql-on/ba-p/2815092)를 지원합니다. 
*   `azure.enable_temp_tablespaces_on_local_ssd` 서버 매개 변수를 사용하여 임시 테이블스페이스에 대한 로컬 디스크를 선택할 수 있습니다.
*   Azure Portal의 서버 매개 변수 페이지에는 측정 단위 및 대부분의 매개 변수에 대한 PostgreSQL 문서 링크가 표시됩니다.
*   여러 버그 수정, 안정성 및 성능 향상

## <a name="release-september-2021"></a>릴리스: 2021년 9월

* [Terraform](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/postgresql_flexible_server)을 지원합니다.
* [새 지역](overview.md#azure-regions)(인도 중부 및 일본 서부)을 지원합니다.
* 새 `require_secure_transport` 서버 매개 변수를 사용하여 비 SSL 연결 모드를 지원합니다.
* 각 로그 줄의 시작 부분에 문자열을 추가하는 `log_line_prefix` 서버 매개 변수를 지원합니다.
* 유연한 서버 상태 진단에 사용되는 [Azure Resource Health](../../service-health/resource-health-overview.md)를 지원하며 지원을 받을 수 있습니다.
* 여러 버그 수정, 안정성 및 성능 향상

## <a name="release-july-2021"></a>릴리스: 2021년 7월

* [새 지역](overview.md#azure-regions) 동아시아, 독일 중서부, 한국 남부, 미국 중남부, 영국 서부를 지원합니다.
* PostgreSQL 11,12 및 13에서 [pglogical 확장](concepts-logical.md) v2.3.2를 지원합니다.<sup>$</sup>
* 이제 PgBouncer에는 `ignore_startup_parameters`가 포함되어 `extra_float_digits` 및 `pgbouncer.query_wait_timeout` 매개 변수를 비롯한 특정 클라이언트 쪽 드라이버의 매개 변수를 무시합니다.  <sup>$</sup>.
* `pg_stat_bgwriter` 및 `pg_stat_archiver` 보기 <sup>$</sup>에 표시된 카운터를 다시 설정하는 `pg_stat_reset_shared('bgwriter');` 및 `pg_stat_reset_shared('archiver');`를 지원합니다.
* 여러 버그 수정, 안정성 및 성능 향상<sup>$</sup>

<sup> **$**</sup> 새 서버는 이러한 기능을 자동으로 가져옵니다. 기존 서버에서 이러한 기능은 서버의 향후 유지 관리 기간 동안 사용하도록 설정됩니다.

## <a name="release-june-2021"></a>릴리스: 2021년 6월

* 새 서버를 만들어 [최신 PostgreSQL 부](./concepts-supported-versions.md) 13.3, 12.7 및 11.12 지원<sup>$</sup>
* 오스트레일리아 남동부, 브라질 남부, 한국 중부, 노르웨이 동부, 남아프리카 공화국 북부, 스위스 북부, 아랍에미리트 북부, 미국 서부 등을 포함하여 [새 지역](overview.md#azure-regions) 지원
* 영역 중복 고가용성 배포를 위한 강제 장애 조치(failover) 및 계획된 장애 조치를 포함하여 [주문형 장애 조치](./concepts-high-availability.md#on-demand-failover) 기능 지원
* 새 서버를 만들어 모든 주 버전에 대한 [SCRAM 인증](how-to-connect-scram.md) 지원<sup>$</sup>
* 새 서버를 만들어 `shared_preload_libraries`를 통해 미리 로드할 `pg_prewarm` 지원<sup>$</sup>
* lo 확장 지원. 각 주 버전에서 지원되는 버전은 [확장 페이지](./concepts-extensions.md)를 참조하세요<sup>$</sup>.
* 여러 버그 수정, 안정성 및 성능 향상<sup>$</sup>
  
<sup> **$**</sup> 새 서버는 이러한 기능을 자동으로 가져옵니다.  기존 서버는 지원되는 최신 부 버전으로 자동으로 업그레이드되며 새로운 기능도 서버의 향후 유지 관리 기간 중에 사용하도록 설정됩니다.

## <a name="release-may-2021"></a>릴리스: 2021년 5월

* [PostgreSQL 주 버전 13](./concepts-supported-versions.md) 지원
* pg_partman, pg_cron, pgaudit를 포함한 확장 지원. 각 주 버전에서 지원되는 버전은 [확장 페이지](./concepts-extensions.md)를 참조하세요.
* 여러 버그 수정, 안정성 및 성능 향상

## <a name="release-april-2021"></a>릴리스: 2021년 4월

* 새 서버를 만들어 [최신 PostgreSQL 부](./concepts-supported-versions.md) 12.6 및 11.11 지원
* VNET(Virtual Network) [프라이빗 DNS 영역](./concepts-networking.md#private-access-vnet-integration) 지원
* 지정 시간 복구 작업 중에 가용성 영역 선택 지원
* 오스트레일리아 동부, 캐나다 중부 및 프랑스 중부 등 새 [지역](./overview.md#azure-regions) 지원
* [기본 제공 PgBouncer](./concepts-pgbouncer.md) 연결 풀러 지원 
<!--- * Support for [pglogical](https://github.com/2ndQuadrant/pglogical) extension version 2.3.2. -->
* 공개 미리 보기의 [지능형 성능](concepts-query-store.md)
* 여러 버그 수정, 안정성 및 성능 향상

## <a name="release-october-2020---march-2021"></a>릴리스: 2020년 10월 - 2021년 3월

*  `az postgres flexible- server connect` 명령으로 Azure CLI를 사용하여 유연한 서버에 [연결](connect-azure-cli.md)하는 환경이 개선되었습니다.
*  [새 지역](overview.md#azure-regions)을 지원합니다.
*  부 버전 표시, 개요 블레이드의 메트릭 요약 등 여러 가지 포털 기능이 향상되었습니다.
*  여러 버그 수정, 안정성 및 성능 향상

## <a name="contacts"></a>연락처

Azure Database for PostgreSQL 유연한 서버에 대한 질문이나 제안이 있으면 Azure Database for PostgreSQL 팀([@Ask Azure DB for PostgreSQL](mailto:AskAzureDBforPostgreSQL@service.microsoft.com))으로 이메일을 보내주세요. 이 이메일 주소는 기술 지원 별칭이 아닙니다.

또한 문의의 다음 사항을 적절히 고려해 주세요.

- Azure 고객 지원팀에 문의하려면 [Azure Portal에서 티켓을 제출](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하세요.
- 계정 관련 문제를 해결하려면 Azure Portal에서 [지원 요청](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)을 제출합니다.
- 피드백을 제공하거나 새 기능을 요청하려면 [UserVoice](https://feedback.azure.com/forums/597976-azure-database-for-postgresql)를 통해 항목을 만드세요.
  

## <a name="next-steps"></a>다음 단계

Azure Database for PostgreSQL 유연한 서버 배포 모드에 대한 소개를 읽어 보았습니다. 이제 첫 번째 서버를 만들 준비가 된 것입니다. [Azure Portal을 사용하여 Azure Database for PostgreSQL - 유연한 서버 만들기](./quickstart-create-server-portal.md)
