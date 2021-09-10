---
title: MySQL 온-프레미스에서 Azure Database for MySQL로 마이그레이션 소개
description: MySQL 온-프레미스에서 Azure Data Base for MySQL로 마이그레이션 가이드
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 06/21/2021
ms.openlocfilehash: 5023d53132985875fc0b927148a6efbfb0239cc3
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/30/2021
ms.locfileid: "113085131"
---
# <a name="migrate-mysql-on-premises-to-azure-database-for-mysql"></a>MySQL 온-프레미스에서 Azure Database for MySQL로 마이그레이션

[!INCLUDE[applies-to-mysql-single-flexible-server](../../includes/applies-to-mysql-single-flexible-server.md)]

이 마이그레이션 가이드는 MySQL 워크로드를 [Azure Database for MySQL](../../overview.md)로 마이그레이션하려는 MySQL 고객 및 소프트웨어 통합자에게 스택형 및 실행 가능한 정보를 제공하도록 설계되었습니다. 이 가이드에서는 대부분의 사례에 적용되는 관련 지식을 제공하고 Azure로의 MySQL 마이그레이션을 성공적으로 계획하고 실행하도록 안내하는 지침을 제공합니다.

기존 데이터베이스 및 MySQL 워크로드를 클라우드로 이동하는 프로세스는 워크로드 기능 및 기존 애플리케이션의 연결과 관련된 문제를 제기할 수 있습니다. 이 가이드 전체에서 제공하는 정보는 성공적인 마이그레이션에 초점을 맞춘 유용한 링크 및 권장 사항을 제공하며 워크로드 및 애플리케이션이 원래 의도한 대로 계속 작동하도록 합니다.

제공된 정보는 Microsoft [클라우드 채택 프레임워크](/azure/cloud-adoption-framework/get-started/)를 사용하여 Azure Database for MySQL 환경에 대한 평가, 마이그레이션 및 최적화 후 작업을 수행하는 고객 여정을 중심으로 합니다.

## <a name="mysql"></a>MySQL

MySQL은 오픈 소스 커뮤니티에서 풍부한 기록을 보유하고 있으며, 웹 사이트 및 기타 중요 비즈니스용 애플리케이션에 대해 전 세계 기업에서 인기를 끌고 있습니다. 이 가이드는 마이그레이션 범위를 지정하고, 계획하고, 실행하라는 요청을 받은 관리자를 지원합니다. MySQL을 처음으로 접하는 관리자는 [MySQL 설명서](https://dev.mysql.com/doc/)를 검토하여 MySQL의 내부 작업에 대한 자세한 정보를 확인할 수도 있습니다. 또한 이 가이드는 각 섹션을 통해 여러 참조 문서에 연결하여 유용한 정보 및 자습서를 안내합니다.

## <a name="azure-database-for-mysql"></a>Azure Database for MySQL

[Azure Database for MySQL](../../overview.md)은 MySQL 환경이 완전히 관리되는 Microsoft의 PaaS(Platform as a Service) 제품입니다. 이 완전 관리형 환경에서는 운영 체제 및 소프트웨어 업데이트가 자동으로 적용되고 고가용성 및 데이터 보호가 구현됩니다.

PaaS 제품 외에도 Azure VM에서 MySQL을 실행할 수 있습니다. 대상 데이터 워크로드에 가장 적합한 배포 유형을 결정하려면 [Azure에서 올바른 MySQL 서버 옵션 선택](../../select-right-deployment-type.md) 문서를 참조하세요.

![MySQL 환경 비교](./media/image3.jpg)

**MySQL 환경 비교**

이 가이드에서는 스케일 업 및 스케일 아웃, 종량제, 고가용성, 보안 및 관리 기능과 같은 IaaS(Infrastructure as a Service)에 대한 다양한 장점으로 인해 온-프레미스 MySQL 워크로드를 Platform as a Service Azure Database for MySQL 제품으로 마이그레이션하는 데 중점을 둡니다.  

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [대표 사용 사례](./02-representative-use-case.md)
