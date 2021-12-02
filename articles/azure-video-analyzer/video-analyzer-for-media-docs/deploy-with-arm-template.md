---
title: ARM 템플릿을 사용 하 여 미디어 용 Azure Video Analyzer 배포
titleSuffix: Azure Video Analyzer for Media (formerly Video Indexer)
description: 이 자습서에서는 ARM (Azure Resource Manager) 템플릿을 사용 하 여 미디어 계정에 대 한 Azure 비디오 분석기를 만듭니다.
ms.topic: tutorial
ms.date: 12/01/2021
ms.author: juliako
ms.openlocfilehash: 388dd7d5d9fed4a69ce02613fafd33add71e8874
ms.sourcegitcommit: 93c7420c00141af83ed3294923b4826dd4dc6ff2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133440620"
---
# <a name="tutorial-deploy-azure-video-analyzer-for-media-with-arm-template"></a>자습서: ARM 템플릿을 사용 하 여 미디어 용 Azure Video Analyzer 배포 

## <a name="overview"></a>개요

이 자습서에서는 ARM (Azure Resource Manager) 템플릿 (미리 보기)을 사용 하 여 미디어 (이전의 Video Indexer) 계정에 대 한 Azure Video Analyzer를 만듭니다.  
리소스는 구독에 배포 되 고, avam. 템플릿 파일에 정의 된 매개 변수를 기반으로 미디어 리소스에 대 한 Azure 비디오 분석기를 만듭니다.

> [!NOTE]
> 이 샘플은 미디어 클래식 계정에 대 한 기존 Azure Video Analyzer를 미디어 계정에 대 한 ARM 기반 비디오 분석기에 연결 하는 데 사용 *되지 않습니다* .
> Azure Video Analyzer for Media API에 대 한 전체 설명서는 [개발자 포털](https://aka.ms/avam-dev-portal) 페이지를 참조 하세요.
> 현재 API 버전은 "2021-10-27-preview"입니다. 새 API 버전에 대 한 업데이트를 가져오는 시점에서이 리포지토리를 확인 합니다.

## <a name="prerequisites"></a>사전 요구 사항

* AMS(Azure Media Services) 계정 [AMS 계정을 만들어](../../media-services/latest/account-create-how-to.md)무료로 만들 수 있습니다.

## <a name="deploy-the-sample"></a>샘플 배포

----

### <a name="option-1-click-the-deploy-to-azure-button-and-fill-in-the-missing-parameters"></a>옵션 1: "Azure에 배포" 단추를 클릭 하 고 누락 된 매개 변수를 채웁니다.

[![Azure에 배포](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fmedia-services-video-indexer%2Fmaster%2FARM-Samples%2FCreate-Account%2Favam.template.json)  

----

### <a name="option-2--deploy-using-powershell-script"></a>옵션 2: PowerShell 스크립트를 사용 하 여 배포

1. [템플릿 파일](https://github.com/Azure-Samples/media-services-video-indexer/blob/master/ARM-Samples/Create-Account/avam.template.json) 파일을 열고 해당 내용을 검사 합니다.
2. 필수 매개 변수를 입력 합니다 (아래 참조).
3. 다음 PowerShell 명령을 실행 합니다.

    * [AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) cmdlet을 사용 하 여 미디어 계정에 대 한 Azure Video Analyzer와 동일한 위치에 새 리소스 그룹을 만듭니다.


    ```powershell
    New-AzResourceGroup -Name myResourceGroup -Location eastus
    ```

    * [AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) cmdlet을 사용 하 여 리소스 그룹에 템플릿을 배포 합니다.

    ```powershell
    New-AzResourceGroupDeployment -ResourceGroupName myResourceGroup -TemplateFile ./avam.template.json
    ```

> [!NOTE]
> Bicep 형식으로 작업 하려면이 리포지토리의 [bicep 파일](https://github.com/Azure-Samples/media-services-video-indexer/blob/master/ARM-Samples/Create-Account/avam.template.bicep) 을 검사 합니다.

## <a name="parameters"></a>매개 변수

### <a name="name"></a>name

* Type: string
* 설명: 미디어 계정에 대 한 새 Azure Video Analyzer의 이름을 지정 합니다.
* 필수: true

### <a name="location"></a>위치

* Type: string
* 설명: 미디어 계정에 대 한 Azure 비디오 분석기를 만들어야 하는 Azure 위치를 지정 합니다.
* 필수: false

> [!NOTE]
> AMS (연결 된 Azure Media Services) 리소스가 존재 하므로 미디어 계정에 대 한 Azure Video Analyzer를 동일한 위치 (지역)에 배포 해야 합니다.

### <a name="mediaserviceaccountresourceid"></a>mediaServiceAccountResourceId

* Type: string
* 설명: AMS (Azure Media Services) 리소스의 리소스 ID입니다.
* 필수: true

### <a name="managedidentityid"></a>managedIdentityId

* Type: string
* 설명: Azure Media Services (AMS) 리소스와 미디어 계정에 대 한 Azure Video Analyzer 간의 액세스 권한을 부여 하는 데 사용 되는 관리 id의 리소스 ID입니다.
* 필수: true

### <a name="tags"></a>tags

* 형식: object
* 설명: 미디어 계정에 대 한 Azure Video Analyzer의 사용자 지정 사용자 태그를 나타내는 개체의 배열입니다.

    필수: false

## <a name="reference-documentation"></a>참조 설명서

미디어 (이전에 Video Indexer) 용 Azure Video Analyzer를 처음 접하는 경우 다음을 참조 하세요.

* [미디어 용 Azure 비디오 분석기 설명서](https://aka.ms/vi-docs)
* [미디어 개발자 포털 용 Azure 비디오 분석기](https://aka.ms/vi-docs)
* 이 자습서를 완료 한 후 [README.md](https://github.com/Azure-Samples/media-services-video-indexer/blob/master/README.md) 에 설명 된 미디어 샘플에 대 한 다른 Azure Video Analyzer를 소개 합니다.

템플릿 배포를 처음 접하는 경우 다음을 참조 하세요.

* [Azure Resource Manager 설명서](../../azure-resource-manager/index.yml)
* [ARM 템플릿을 사용 하 여 리소스 배포](../../azure-resource-manager/templates/deploy-powershell.md)
* [Bicep 및 Azure CLI를 사용 하 여 리소스 배포](../../azure-resource-manager/bicep/deploy-cli.md)

## <a name="next-steps"></a>다음 단계

[미디어 계정에 대 한 기존 클래식 유료 비디오 분석기를 ARM 기반 계정으로 커넥트](connect-classic-account-to-arm.md)
