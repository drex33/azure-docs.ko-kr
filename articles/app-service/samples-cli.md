---
title: Azure App Service용 Azure CLI 샘플 | Microsoft Docs
description: 몇 가지 일반적인 App Service 시나리오에 대한 Azure CLI 샘플을 찾습니다. App Service 배포 또는 관리 작업을 자동화하는 방법을 알아봅니다.
tags: azure-service-management
ms.assetid: 53e6a15a-370a-48df-8618-c6737e26acec
ms.topic: sample
ms.date: 09/17/2021
ms.custom: mvc, devx-track-azurecli, seo-azure-cli
keywords: azure cli 샘플, azure cli 예제, azure cli 코드 샘플
ms.openlocfilehash: 622c26488a3a92cfd63e744246014a7e5bdaf71f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128676412"
---
# <a name="cli-samples-for-azure-app-service"></a>Azure App Service에 대한 CLI 샘플

다음 테이블은 Azure CLI를 사용하여 빌드된 bash 셸에 대한 링크를 포함합니다.

| 스크립트 | Description |
|-|-|
|**앱 만들기**||
| [앱 만들기 및 FTP를 사용하여 파일 배포](./scripts/cli-deploy-ftp.md?toc=%2fcli%2fazure%2ftoc.json)| App Service 앱을 만들고 FTP를 사용하여 파일을 배포합니다. |
| [앱 만들기 및 GitHub의 코드 배포](./scripts/cli-deploy-github.md?toc=%2fcli%2fazure%2ftoc.json)| App Service 앱을 만들고 공용 GitHub 리포지토리에서 코드를 배포합니다. |
| [GitHub의 지속적인 배포를 사용하여 앱 만들기](./scripts/cli-continuous-deployment-github.md?toc=%2fcli%2fazure%2ftoc.json)| 사용자가 소유한 GitHub 리포지토리에서 연속 게시를 통해 App Service 앱을 만듭니다. |
| [앱 만들기 및 로컬 Git 리포지토리의 코드 배포](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json) | App Service 앱을 만들고 로컬 Git 리포지토리의 코드 푸시를 구성합니다. |
| [앱 만들기 및 스테이징 환경에 코드 배포](./scripts/cli-deploy-staging-environment.md?toc=%2fcli%2fazure%2ftoc.json) | 코드 변경 내용을 준비하기 위해 배포 슬롯을 사용하여 App Service 앱을 만듭니다. |
| [Docker 컨테이너에서 ASP.NET Core 앱 만들기](./scripts/cli-linux-docker-aspnetcore.md?toc=%2fcli%2fazure%2ftoc.json) | Linux에서 App Service 앱을 만들고 Docker Hub의 Docker 이미지를 로드합니다. |
| [프라이빗 엔드포인트를 사용하여 앱 만들기 및 노출](./scripts/cli-deploy-privateendpoint.md?toc=%2fcli%2fazure%2ftoc.json) | App Service 앱 및 프라이빗 엔드포인트 만들기 |
|**앱 구성**||
| [사용자 지정 도메인을 앱에 매핑](./scripts/cli-configure-custom-domain.md?toc=%2fcli%2fazure%2ftoc.json)| App Service 앱을 만들고 여기에 사용자 지정 도메인 이름을 매핑합니다. |
| [앱에 사용자 지정 TLS/SSL 인증서 바인딩](./scripts/cli-configure-ssl-certificate.md?toc=%2fcli%2fazure%2ftoc.json)| App Service 앱을 만들고 여기에 사용자 지정 도메인 이름의 TLS/SSL 인증서를 바인딩합니다. |
|**앱 크기 조정**||
| [수동으로 앱 크기 조정](./scripts/cli-scale-manual.md?toc=%2fcli%2fazure%2ftoc.json) | App Service 앱을 만들고 2개의 인스턴스로 확장합니다. |
| [전 세계에 고가용성 아키텍처를 가진 앱 확장](./scripts/cli-scale-high-availability.md?toc=%2fcli%2fazure%2ftoc.json) | 두 개의 지역에 두 개의 App Service 앱을 만들고 Azure Traffic Manager를 사용하여 단일 엔드포인트를 통해 사용할 수 있습니다. |
|**앱 보호**||
| [Azure Application Gateway와 통합](./scripts/cli-integrate-app-service-with-application-gateway.md?toc=%2fcli%2fazure%2ftoc.json) | App Service 앱을 만들고 서비스 엔드포인트 및 액세스 제한을 사용하여 Application Gateway와 통합합니다. |
|**리소스에 앱 연결**||
| [SQL Database에 앱 연결](./scripts/cli-connect-to-sql.md?toc=%2fcli%2fazure%2ftoc.json)| App Service 앱 및 Azure SQL Database의 데이터베이스를 만든 다음, 앱 설정에 데이터베이스 연결 문자열을 추가합니다. |
| [스토리지 계정에 앱 연결](./scripts/cli-connect-to-storage.md?toc=%2fcli%2fazure%2ftoc.json)| App Service 앱 및 스토리지 계정을 만든 다음, 앱 설정에 스토리지 연결 문자열을 추가합니다. |
| [Azure Cache for Redis에 앱 연결](./scripts/cli-connect-to-redis.md?toc=%2fcli%2fazure%2ftoc.json) | App Service 앱 및 Azure Cache for Redis를 만든 다음, Redis 연결 세부 정보를 앱 설정에 추가합니다.) |
| [Cosmos DB에 앱 연결](./scripts/cli-connect-to-documentdb.md?toc=%2fcli%2fazure%2ftoc.json) | App Service 앱 및 Cosmos DB를 만든 다음, 앱 설정에 Cosmos DB 연결 세부 정보를 추가합니다. |
|**앱 백업 및 복원**||
| [앱 백업](./scripts/cli-backup-onetime.md?toc=%2fcli%2fazure%2ftoc.json) | App Service 앱을 만들고 이에 대한 일회성 백업을 만듭니다. |
| [앱에 대한 예약된 백업 만들기](./scripts/cli-backup-scheduled.md?toc=%2fcli%2fazure%2ftoc.json) | App Service 앱을 만들고 이에 대한 예약된 백업을 만듭니다. |
| [백업으로 앱 복원](./scripts/cli-backup-restore.md?toc=%2fcli%2fazure%2ftoc.json) | 백업에서 App Service 앱을 복원합니다. |
|**앱 모니터링**||
| [웹 서버 로그로 앱 모니터링](./scripts/cli-monitor.md?toc=%2fcli%2fazure%2ftoc.json) | App Service 앱을 만들고 로깅을 사용하도록 설정하고 로그를 로컬 머신에 다운로드합니다. |
| | |
