---
title: Azure NetApp Files 액세스를 위한 NetApp 계정 만들기 | Microsoft Docs
description: 용량 풀을 설정하고 볼륨을 만들 수 있도록 Azure NetApp Files에 액세스하고 NetApp 계정을 만드는 방법을 설명합니다.
services: azure-netapp-files
documentationcenter: ''
author: b-hchen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 10/04/2021
ms.author: b-hchen
ms.openlocfilehash: 8d8b158644c251c8ded7f6dbba2519ec1e29dd25
ms.sourcegitcommit: 9ef0965834870700468c822ddcafc011881fc2d5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133486450"
---
# <a name="create-a-netapp-account"></a>NetApp 계정 만들기
NetApp 계정 만들기를 사용하면 용량 풀을 설정하고 이후에 볼륨을 만들 수 있습니다. Azure NetApp Files 블레이드를 사용하여 새 NetApp 계정을 만듭니다.

## <a name="before-you-begin"></a>시작하기 전에

NetApp 리소스 공급자를 사용 하기 위해 구독을 등록 해야 합니다. [NetApp 리소스 공급자 등록](azure-netapp-files-register.md)을 참조하세요.

## <a name="steps"></a>단계 

1. Azure Portal에 로그인합니다. 
2. 다음 방법 중 하나를 사용하여 Azure NetApp Files 블레이드에 액세스합니다.  
   * Azure Portal 검색 상자에서 **Azure NetApp Files** 를 검색합니다.  
   * 탐색에서 **모든 서비스** 를 클릭한 다음, Azure NetApp Files로 필터링합니다.  

   옆에 있는 별 모양 아이콘을 클릭하여 Azure NetApp Files 블레이드를 "즐겨찾기에 추가"할 수 있습니다. 

3. **+ 추가** 를 클릭하여 새 NetApp 계정을 만듭니다.  
   새 NetApp 계정 창이 나타납니다.  

4. NetApp 계정에 대한 다음과 같은 정보를 제공합니다. 
   * **계정 이름**  
     구독에 대해 고유한 이름을 지정합니다.
   * **구독**  
     기존 구독에서 구독을 선택합니다.
   * **리소스 그룹**   
     기존 리소스 그룹을 사용하거나 새 리소스 그룹을 만듭니다.
   * **위치**  
     계정 및 해당 자식 리소스를 배치하려는 지역을 선택합니다.  

     ![새 NetApp 계정](../media/azure-netapp-files/azure-netapp-files-new-netapp-account.png)


5. **만들기** 를 클릭합니다.     
   이제 사용자가 만든 NetApp 계정이 Azure NetApp Files 블레이드에 나타납니다. 

> [!NOTE] 
> Azure NetApp Files 서비스에 대한 액세스 권한이 부여되지 않은 경우 첫 번째 NetApp 계정을 만들려고 하면 다음 오류가 표시됩니다.  
>
> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"NotFound","message":"{\r\n \"error\": {\r\n \"code\": \"InvalidResourceType\",\r\n \"message\": \"The resource type could not be found in the namespace 'Microsoft.NetApp' for api version '2017-08-15'.\"\r\n }\r\n}"}]}`

## <a name="next-steps"></a>다음 단계  

[용량 풀 만들기](azure-netapp-files-set-up-capacity-pool.md)

