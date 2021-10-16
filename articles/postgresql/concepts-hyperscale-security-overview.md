---
title: 보안 개요 - 하이퍼스케일(Citus) - Azure Database for PostgreSQL
description: Azure Database for PostgreSQL 대한 정보 보호 및 네트워크 보안 - 하이퍼스케일(Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 10/15/2021
ms.openlocfilehash: eedad96af5baa6fe588788247c4f8a3a44bee0a0
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130075857"
---
# <a name="security-in-azure-database-for-postgresql--hyperscale-citus"></a>Azure Database for PostgreSQL 보안 – 하이퍼스케일(Citus)

이 페이지에서는 하이퍼스케일(Citus) 서버 그룹의 데이터를 보호하는 데 사용할 수 있는 여러 보안 계층을 간략하게 설명합니다. 

## <a name="information-protection-and-encryption"></a>정보 보호 및 암호화

### <a name="in-transit"></a>전송 중

데이터가 노드에 수집될 때마다 하이퍼스케일(Citus) 전송 계층 보안 1.2를 사용하여 전송 중인 데이터를 암호화하여 데이터를 보호합니다. 암호화(SSL/TLS)는 항상 적용되며 사용하지 않도록 설정될 수 없습니다.

### <a name="at-rest"></a>미사용

하이퍼스케일(Citus) 서비스는 미사용 데이터의 스토리지 암호화에 FIPS 140-2 유효성이 검사된 암호화 모듈을 사용합니다. 백업을 포함한 데이터는 디스크에서 암호화되며, 쿼리를 실행하는 동안 만든 임시 파일은 제외됩니다.
이 서비스는 Azure Storage 암호화에 포함된 AES 256비트 암호화를 사용하며 키는 시스템 관리형입니다. Storage 암호화는 항상 설정되어 있으며 사용하지 않도록 설정될 수 없습니다.

## <a name="network-security"></a>네트워크 보안

[!INCLUDE [azure-postgresql-hyperscale-access](../../includes/azure-postgresql-hyperscale-access.md)]

## <a name="limits-and-limitations"></a>제한 및 제한 사항

하이퍼스케일(Citus) [제한 사항](concepts-hyperscale-limits.md) 페이지를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [프라이빗 액세스를 사용하도록 설정하고 관리하는](howto-hyperscale-private-access.md) 방법 알아보기(미리 보기)
* 프라이빗 [엔드포인트에](/azure/private-link/private-endpoint-overview) 대해 알아보기
* 가상 [네트워크에](/azure/virtual-network/concepts-and-best-practices) 대해 알아보기
* 프라이빗 DNS 영역에 대해 [알아보기](/azure/dns/private-dns-overview)
