---
title: Azure에서 배포된 SSIS 패키지로 파일 열기 및 저장
description: 로컬 파일 시스템을 사용하는 SSIS 패키지를 Azure의 SSIS로 리프트하고 이동하는 경우 온-프레미스 및 Azure에서 파일을 열고 저장하는 방법 알아보기
ms.date: 10/22/2021
ms.topic: conceptual
ms.service: data-factory
ms.subservice: integration-services
author: swinarko
ms.author: sawinark
ms.reviewer: jburchel
ms.openlocfilehash: 846b27b135714c0fb1eed1c660be15634b2e834e
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131849276"
---
# <a name="open-and-save-files-on-premises-and-in-azure-with-ssis-packages-deployed-in-azure"></a>Azure에 배포된 SSIS 패키지로 온-프레미스 및 Azure에서 파일 열기 및 저장

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

이 문서는 로컬 파일 시스템을 사용하는 SSIS 패키지를 Azure의 SSIS로 리프트하고 이동하는 경우 온-프레미스 및 Azure에서 파일을 열고 저장하는 방법을 설명합니다.

## <a name="save-temporary-files"></a>임시 파일 저장

단일 패키지를 실행하는 동안 임시 파일을 저장하고 처리해야 하는 경우 패키지는 Azure SSIS Integration Runtime 노드의 현재 작업 디렉터리(`.`) 또는 임시 폴더(`%TEMP%`)를 사용할 수 있습니다.

## <a name="use-on-premises-file-shares"></a>온-프레미스 파일 공유 사용

**온-프레미스 파일 공유** 를 계속해서 사용하려면 Azure에서 로컬 파일 시스템을 SSIS로 사용하는 패키지를 리프트 앤 시프트할 때 다음 작업을 수행합니다.

1. 로컬 파일 시스템에서 온-프레미스 파일 공유로 파일을 전송합니다.

2. 온-프레미스 파일 공유를 Azure 가상 네트워크에 조인합니다.

3. Azure SSIS IR을 동일한 가상 네트워크에 조인합니다. 자세한 내용은 [Azure-SSIS 통합 런타임을 가상 네트워크에 조인](./join-azure-ssis-integration-runtime-virtual-network.md)을 참조하세요.

4. Windows 인증을 사용하는 액세스 자격 증명을 설정하여 Azure SSIS IR을 동일한 가상 네트워크 내의 온-프레미스 파일 공유에 연결합니다. 자세한 정보는 [Windows 인증으로 데이터 및 파일 공유에 연결](ssis-azure-connect-with-windows-auth.md)을 참조합니다.

5. 패키지의 로컬 파일 경로를 온-프레미스 파일 공유를 가리키는 UNC 경로로 업데이트합니다. 예를 들어 `C:\abc.txt`를 `\\<on-prem-server-name>\<share-name>\abc.txt`로 업데이트합니다.

## <a name="use-azure-file-shares"></a>Azure 파일 공유 사용

**Azure Files** 를 사용하려면 Azure에서 로컬 파일 시스템을 SSIS로 사용하는 패키지를 리프트 앤 시프트할 때 다음 작업을 수행합니다.

1. 로컬 파일 시스템에서 Azure Files로 파일을 전송합니다. 자세한 내용은 [Azure Files](https://azure.microsoft.com/services/storage/files/)를 참조하세요.

2. Windows 인증을 사용하는 액세스 자격 증명을 설정하여 Azure SSIS IR을 Azure Files에 연결합니다. 자세한 정보는 [Windows 인증으로 데이터 및 파일 공유에 연결](ssis-azure-connect-with-windows-auth.md)을 참조합니다.

3. 패키지의 로컬 파일 경로를 Azure Files를 가리키는 UNC 경로로 업데이트합니다. 예를 들어 `C:\abc.txt`를 `\\<storage-account-name>.file.core.windows.net\<share-name>\abc.txt`로 업데이트합니다.

## <a name="next-steps"></a>다음 단계

- 패키지를 배포합니다. 자세한 내용은 [SSMS를 사용하여 Azure에 SSIS 프로젝트 배포](/sql/integration-services/ssis-quickstart-deploy-ssms)를 참조합니다.
- 패키지를 실행합니다. 자세한 내용은 [SSMS를 사용하여 Azure에서 SSIS 패키지 실행](/sql/integration-services/ssis-quickstart-run-ssms)을 참조합니다.
- 패키지를 예약합니다. 자세한 내용은 [Azure에서 SSIS 패키지 예약](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)을 참조하세요.