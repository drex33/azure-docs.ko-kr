---
title: Cloud Services(추가 지원)에서 원격 데스크톱 적용
description: Cloud Services(추가 지원)에 대한 원격 데스크톱 사용
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 02e5a30da1efe8c3cd669babddff305296077f20
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123424189"
---
# <a name="apply-the-remote-desktop-extension-to-azure-cloud-services-extended-support"></a>Azure Cloud Services(추가 지원)에 원격 데스크톱 확장 적용

Azure Portal은 원격 데스크톱 확장을 사용하여 애플리케이션이 배포된 후에도 원격 데스크톱을 사용하도록 설정합니다. Cloud Service에 대한 원격 데스크톱 설정을 통해 원격 데스크톱을 사용하도록 설정하고, 로컬 관리자 계정을 업데이트하고, 인증에 사용되는 인증서를 선택하고, 해당 인증서의 만료 날짜를 설정할 수 있습니다. 

## <a name="apply-remote-desktop--extension"></a>원격 데스크톱 확장 적용
1. 원격 데스크톱을 사용하도록 설정하려는 Cloud Service로 이동하고 왼쪽 탐색 창에서 **"원격 데스크톱"** 을 선택합니다.

    :::image type="content" source="media/remote-desktop-1.png" alt-text="Azure Portal에서 원격 데스크톱 옵션을 선택하는 것을 보여 주는 이미지":::

2. **추가** 를 선택합니다.
3. 원격 데스크톱을 사용하도록 설정할 역할을 선택합니다.
4. 사용자 이름, 암호, 만료 및 인증서에 대한 필수 필드를 입력합니다(필수 아님).
> [참고] 원격 데스크톱의 암호는 8-123자 사이이고 다음과 같은 암호 복잡성 요구 사항 중 3개 이상을 만족해야 합니다. 1) 대문자 포함 2) 소문자 포함 3) 숫자 포함 4) 특수 문자 포함 5) 제어 문자 사용 불가

   :::image type="content" source="media/remote-desktop-2.png" alt-text="원격 데스크톱에 연결하는 데 필요한 정보를 입력하는 것을 보여 주는 이미지":::

5. 작업을 마쳤으면 **저장** 을 선택합니다. 역할 인스턴스가 연결을 수신할 준비가 되기까지 몇 분 정도 걸립니다.

## <a name="connect-to-role-instances-with-remote-desktop-enabled"></a>원격 데스크톱이 사용하도록 설정된 역할 인스턴스에 연결
역할에 대해 원격 데스크톱이 사용 가능하게 설정되면 Azure Portal에서 직접 연결을 시작할 수 있습니다.

1. **역할 및 인스턴스** 를 클릭하여 인스턴스 설정을 엽니다.

    :::image type="content" source="media/remote-desktop-3.png" alt-text="구성 블레이드에서 역할 및 인스턴스 옵션을 선택하는 것을 보여 주는 이미지":::

2. 원격 데스크톱이 구성된 역할 인스턴스를 선택합니다.
3. **연결** 을 클릭하여 원격 데스크톱 연결 파일을 다운로드합니다.

    :::image type="content" source="media/remote-desktop-4.png" alt-text="Azure Portal에서 작업자 역할 인스턴스를 선택하는 것을 보여 주는 이미지":::
    
4. 역할 인스턴스에 연결할 파일을 엽니다.

## <a name="update-remote-desktop-extension-using-powershell"></a>PowerShell을 사용하여 원격 데스크톱 확장 업데이트
아래 단계에 따라 RDP 확장을 사용하여 클라우드 서비스를 최신 모듈로 업데이트합니다.

1.  Az.CloudService 모듈을 [최신 버전으로](https://www.powershellgallery.com/packages/Az.CloudService/0.5.0) 업데이트

```powershell
Update-Module -Name Az.CloudService 
```
 
2.  클라우드 서비스에 대한 기존 RDP 확장 제거 

```powershell
$resourceGroupName='<Resource Group Name>'  
$cloudServiceName='<Cloud Service Name>' 
 
# Get existing cloud service  
$cloudService = Get-AzCloudService -ResourceGroup $resourceGroupName -CloudServiceName $cloudServiceName  
 
# Remove existing RDP Extension from cloud service object  
$cloudService.ExtensionProfile.Extension = $cloudService.ExtensionProfile.Extension | Where-Object { $_.Type-ne "RDP" }  
 ```
 
3.  최신 모듈을 사용하여 클라우드 서비스에 새 RDP 확장 추가

```powershell
# Create new RDP extension object  
$credential = Get-Credential  
$expiration='<Expiration Date>'  
$rdpExtension = New-AzCloudServiceRemoteDesktopExtensionObject -Name "RDPExtension" -Credential $credential -Expiration $expiration -TypeHandlerVersion "1.2.1"  
 
# Add RDP extension to existing cloud service extension object  
$cloudService.ExtensionProfile.Extension = $cloudService.ExtensionProfile.Extension + $rdpExtension  
 
# Update cloud service  
$cloudService | Update-AzCloudService  
```

## <a name="next-steps"></a>다음 단계 
- Cloud Services(추가 지원)에 대한 [배포 필수 구성 요소](deploy-prerequisite.md)를 검토합니다.
- Cloud Services(추가 지원)에 대한 [질문과 대답](faq.yml)을 검토합니다.
- [Azure Portal](deploy-portal.md), [PowerShell](deploy-powershell.md), [템플릿](deploy-template.md) 또는 [Visual Studio](deploy-visual-studio.md)를 사용하여 Cloud Service(추가 지원)를 배포합니다.
