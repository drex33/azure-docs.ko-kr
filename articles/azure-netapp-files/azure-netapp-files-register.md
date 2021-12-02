---
title: Azure NetApp Files에서 사용할 NetApp 리소스 공급자 등록 Microsoft Docs
description: Azure NetApp Files에 대 한 NetApp 리소스 공급자를 등록 하는 방법을 알아봅니다.
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
ms.openlocfilehash: 61be6da00a682dff5425f0fc7addf7c61ea2fac6
ms.sourcegitcommit: 9ef0965834870700468c822ddcafc011881fc2d5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133484888"
---
# <a name="register-for-netapp-resource-provider"></a>NetApp 리소스 공급자 등록

Azure NetApp Files 서비스를 사용 하려면 NetApp 리소스 공급자를 등록 해야 합니다.

1. Azure Portal의 오른쪽 위 모서리에 있는 Azure Cloud Shell 아이콘을 클릭합니다.

      ![Azure Cloud Shell 아이콘](../media/azure-netapp-files/azure-netapp-files-azure-cloud-shell.png)

2. Azure 계정에 여러 구독이 있는 경우 Azure NetApp Files의 허용된 구독을 선택합니다.
    
    ```azurecli
    az account set --subscription <subscriptionId>
    ```

3. Azure Cloud Shell 콘솔에서 구독이 허용되었는지 확인하려면 다음 명령을 입력합니다.
    
    ```azurecli
    az feature list | grep NetApp
    ```

   명령 출력은 다음과 같이 나타납니다.
   
    ```output
    "id": "/subscriptions/<SubID>/providers/Microsoft.Features/providers/Microsoft.NetApp/features/ANFGA",  
    "name": "Microsoft.NetApp/ANFGA" 
    ```
       
   `<SubID>`는 구독 ID입니다.



4. Azure Cloud Shell 콘솔에서 다음 명령을 입력하여 Azure 리소스 공급자를 등록합니다. 
    
    ```azurecli
    az provider register --namespace Microsoft.NetApp --wait
    ```

   `--wait` 매개 변수는 등록이 완료될 때까지 기다리도록 콘솔에 지시합니다. 등록 프로세스는 완료하는 데 다소 시간이 걸릴 수 있습니다.

5. Azure Cloud Shell 콘솔에서 다음 명령을 입력하여 Azure 리소스 공급자가 등록되었는지 확인합니다. 
    
    ```azurecli
    az provider show --namespace Microsoft.NetApp
    ```

   명령 출력은 다음과 같이 나타납니다.
   
    ```output
    {
     "id": "/subscriptions/<SubID>/providers/Microsoft.NetApp",
     "namespace": "Microsoft.NetApp", 
     "registrationState": "Registered", 
     "resourceTypes": […. 
    ```

   `<SubID>`는 구독 ID입니다.  `state` 매개 변수 값은 `Registered`를 나타냅니다.

6. Azure Portal에서 **구독** 블레이드를 클릭합니다.
7. 구독 블레이드에서 구독 ID를 클릭합니다. 
8. 구독 설정에서 **리소스 공급자** 를 클릭하여 Microsoft.NetApp 공급자가 등록됨 상태를 나타내는지 확인합니다. 

      ![등록된 Microsoft.NetApp](../media/azure-netapp-files/azure-netapp-files-registered-resource-providers.png)


## <a name="next-steps"></a>다음 단계

* [NetApp 계정 만들기](azure-netapp-files-create-netapp-account.md)
* [Azure 지원 요청 만들기](../azure-portal/supportability/how-to-create-azure-support-request.md)
