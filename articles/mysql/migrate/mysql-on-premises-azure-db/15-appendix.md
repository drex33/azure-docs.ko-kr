---
title: MySQL 온-프레미스에서 Azure Database for MySQL 샘플 애플리케이션으로
description: 이 마이그레이션 가이드에 대해 만든 추가 설명서를 다운로드하고 구성하는 방법을 알아봅니다.
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 06/21/2021
ms.openlocfilehash: 27f4ded8e1378ccceeb11409e9c393fbc46c92aa
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/30/2021
ms.locfileid: "113084555"
---
# <a name="migrate-mysql-on-premises-to-azure-database-for-mysql-sample-applications"></a>MySQL 온-프레미스에서 Azure Database for MySQL 샘플 애플리케이션으로 마이그레이션

[!INCLUDE[applies-to-mysql-single-flexible-server](../../includes/applies-to-mysql-single-flexible-server.md)]

## <a name="overview"></a>개요

이 문서에서는 엔드투엔드 MySQL 마이그레이션 가이드를 사용하여 샘플 애플리케이션을 배포하고 사용 가능한 서버 매개 변수를 검토하는 방법을 설명합니다.

## <a name="environment-setup"></a>환경 설정

이 마이그레이션 가이드에 대해 만든 [추가 설명서를 다운로드](https://github.com/Azure/azure-mysql/blob/master/MigrationGuide/MySQL%20Migration%20Guide_v1.1%20Appendix%20A.pdf)하고 샘플 [회의 데모 애플리케이션](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide/sample-app)에 대한 가이드의 마이그레이션 단계를 수행하도록 환경을 구성하는 방법을 알아봅니다.

## <a name="azure-resource-manager-arm-templates"></a>ARM(Azure Resource Manager) 템플릿

### <a name="secure"></a>보안

ARM 템플릿은 프라이빗 엔드포인트를 통해 모든 리소스를 배포합니다. ARM 템플릿은 인터넷에서 PaaS 서비스에 대한 액세스를 효과적으로 제거합니다.

[보안 ARM 템플릿](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide/arm-templates/ExampleWithMigration)

### <a name="non-secure"></a>비보안

ARM 템플릿은 인터넷에서 모든 리소스를 사용할 수 있는 표준 배포를 사용하여 리소스를 배포합니다.

[비보안 ARM 템플릿](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide/arm-templates/ExampleWithMigrationSecure)

## <a name="default-server-parameters-mysql-55-and-azure-database-for-mysql"></a>기본 서버 매개 변수 MySQL 5.5 및 Azure Database for MySQL

GitHub 리포지토리에서 [MySQL 5.5 및 Azure Database for MySQL 기본 서버 매개 변수의 전체 목록](https://github.com/Azure/azure-mysql/blob/master/MigrationGuide/MySQL%20Migration%20Guide_v1.1%20Appendix%20C.pdf)을 찾을 수 있습니다.