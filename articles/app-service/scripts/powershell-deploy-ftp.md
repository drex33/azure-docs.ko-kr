---
title: 'PowerShell: FTP를 사용하여 파일 업로드'
description: Azure PowerShell을 사용하여 App Service의 배포 및 관리를 자동화하는 방법을 알아봅니다. 이 샘플에서는 FTP를 사용하여 앱에 파일을 업로드하는 방법을 보여줍니다.
tags: azure-service-management
ms.assetid: b7d46d6f-44fd-454c-8008-87dab6eefbc1
ms.topic: sample
ms.date: 06/23/2021
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 83ec2fad8d4f3ec6efb1b88e1e765ebcfa0d260d
ms.sourcegitcommit: ca38027e8298c824e624e710e82f7b16f5885951
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2021
ms.locfileid: "112575265"
---
# <a name="upload-files-to-a-web-app-using-ftp"></a>FTP를 사용하여 웹앱에 파일 업로드

이 샘플 스크립트에서는 관련 리소스를 사용하여 App Service에서 웹앱을 만든 다음, FTPS를 사용하여 파일을 배포합니다([System.Net.FtpWebRequest](/dotnet/api/system.net.ftpwebrequest)를 통해).

필요한 경우 [Azure PowerShell 가이드](/powershell/azure/)에 있는 지침을 사용하여 Azure PowerShell을 설치한 다음, `Connect-AzAccount`를 실행하여 Azure에 연결합니다.

## <a name="sample-script"></a>샘플 스크립트

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/deploy-ftp/deploy-ftp.ps1?highlight=1 "Upload files to a web app using FTP")]

## <a name="clean-up-deployment"></a>배포 정리 

스크립트 샘플을 실행한 후에는 다음 명령을 사용하여 리소스 그룹, 웹앱 및 모든 관련된 리소스를 제거할 수 있습니다.

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | App Service 계획을 만듭니다. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | 웹앱을 만듭니다. |
| [Get-AzWebAppPublishingProfile](/powershell/module/az.websites/get-azwebapppublishingprofile) | 웹앱의 게시 프로필을 가져옵니다. |

## <a name="next-steps"></a>다음 단계

Azure PowerShell 모듈에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/)를 참조하세요.

Azure App Service Web Apps에 대한 추가 Azure PowerShell 샘플은 [Azure PowerShell 샘플](../samples-powershell.md)에서 확인할 수 있습니다.
