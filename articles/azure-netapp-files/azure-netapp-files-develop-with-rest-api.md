---
title: REST API를 사용한 Azure NetApp Files용 개발 | Microsoft Docs
description: Azure NetApp Files 서비스용 REST API는 NetApp 계정, 용량 풀, 볼륨 및 스냅샷과 같은 리소스에 대한 HTTP 작업을 정의합니다.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 06/29/2021
ms.author: b-juche
ms.openlocfilehash: 7158f24d4168995c1fc196f01a15f333b735d4a4
ms.sourcegitcommit: b00a2d931b0d6f1d4ea5d4127f74fc831fb0bca9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2021
ms.locfileid: "132868436"
---
# <a name="develop-for-azure-netapp-files-with-rest-api"></a>REST API를 사용한 Azure NetApp Files용 개발 

Azure NetApp Files 서비스용 REST API는 NetApp 계정, 용량 풀, 볼륨 및 스냅샷과 같은 리소스에 대한 HTTP 작업을 정의합니다. 이 문서는 Azure NetApp Files REST API 사용을 시작하는 데 도움이 됩니다.

## <a name="azure-netapp-files-rest-api-specification"></a>Azure NetApp Files REST API 사양

Azure NetApp Files용 REST API 사양은 [GitHub](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager)를 통해 게시됩니다.

`https://github.com/Azure/azure-rest-api-specs/tree/master/specification/netapp/resource-manager`

## <a name="considerations"></a>고려 사항

* API 제한을 초과하는 경우 HTTP 응답 코드는 **429** 입니다.  예를 들면 다음과 같습니다.

   `"Microsoft.Azure.ResourceProvider.Common.Exceptions.ResourceProviderException: Error getting Pool. Rate limit exceeded for this endpoint - try again later ---> CloudVolumes.Service.Client.Client.ApiException: Error calling V2DescribePool: {\"code\":429,\"message\":\"Rate limit exceeded for this endpoint - try again later\"}`
   
   이 응답 코드는 제한 또는 임시 상태에서 발생할 수 있습니다. 자세한 내용은 [Azure Resource Manager HTTP 429 응답 코드](../azure-resource-manager/management/request-limits-and-throttling.md#error-code)를 참조하세요.

## <a name="access-the-azure-netapp-files-rest-api"></a>Azure NetApp Files REST API 액세스  

1. 아직 설치하지 않은 경우 [Azure CLI를 설치](/cli/azure/install-azure-cli)합니다.
2. Azure AD(Azure Active Directory)에 서비스 주체를 만듭니다.
   1. [충분한 권한](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)이 있는지 확인합니다.

   2. Azure CLI에서 다음 명령을 입력합니다. 
    
        ```azurecli
        az ad sp create-for-rbac --name $YOURSPNAMEGOESHERE --role Contributor
        ```

      명령 출력은 다음 예제와 유사합니다.  

        ```output
        { 
            "appId": "appIDgoeshere", 
            "displayName": "APPNAME", 
            "name": "http://APPNAME", 
            "password": "supersecretpassword", 
            "tenant": "tenantIDgoeshere" 
        } 
        ```

      명령 출력을 보관합니다.  `appId`, `password` 및 `tenant` 값이 필요합니다. 

3. OAuth 액세스 토큰을 요청합니다.

    이 문서의 예제에서는 cURL을 사용합니다. [Postman](https://www.getpostman.com/), [Insomnia](https://insomnia.rest/) 및 [Paw](https://paw.cloud/)와 같은 다양한 API 도구를 사용할 수도 있습니다.  

    다음 예제의 변수를 위 2단계의 명령 출력으로 바꿉니다. 
    
    ```azurecli
    curl -X POST -d 'grant_type=client_credentials&client_id=[APP_ID]&client_secret=[PASSWORD]&resource=https%3A%2F%2Fmanagement.azure.com%2F' https://login.microsoftonline.com/[TENANT_ID]/oauth2/token
    ```

    출력에서 다음 예제와 유사한 액세스 토큰을 제공합니다.

    `eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSIsImtpZCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSJ9`

    표시된 토큰은 3600초간 유효합니다. 그 후에는 새 토큰을 요청해야 합니다. 
    텍스트 편집기에 토큰을 저장합니다.  이 토큰은 다음 단계에서 필요합니다.

4. 테스트 호출을 보내고 REST API에 대한 액세스 권한을 확인할 토큰을 포함합니다.

    ```azurecli
    curl -X GET -H "Authorization: Bearer [TOKEN]" -H "Content-Type: application/json" https://management.azure.com/subscriptions/[SUBSCRIPTION_ID]/providers/Microsoft.Web/sites?api-version=2019-11-01
    ```

## <a name="examples-using-the-api"></a>API 사용 예제  

이 문서에서는 요청 기준선으로 다음 URL을 사용합니다. 이 URL은 Azure NetApp Files 네임스페이스의 루트를 가리킵니다. 

`https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts?api-version=2019-11-01`

다음 예제의 `SUBIDGOESHERE` 및 `RESOURCEGROUPGOESHERE` 값을 사용자 고유의 값으로 바꾸어야 합니다. 

### <a name="get-request-examples"></a>GET 요청 예제

다음 예제와 같이 GET 요청을 사용하여 구독에서 Azure NetApp Files의 개체를 쿼리합니다. 

```azurecli
#get NetApp accounts 
curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts?api-version=2019-11-01
```

```azurecli
#get capacity pools for NetApp account 
curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools?api-version=2019-11-01
```

```azurecli
#get volumes in NetApp account & capacity pool 
curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes?api-version=2019-11-01
```

```azurecli
#get snapshots for a volume 
curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes/VOLUMEGOESHERE/snapshots?api-version=2019-11-01
```

### <a name="put-request-examples"></a>PUT 요청 예제

다음 예제와 같이 PUT 요청을 사용하여 Azure NetApp Files에 새 개체를 만듭니다. PUT 요청의 본문에는 변경 내용에 대한 JSON 형식의 데이터가 포함될 수 있습니다. curl 명령에 텍스트로 포함되거나 파일로 참조되어야 합니다. 본문을 파일로 참조하려면 json 예제를 파일에 저장하고 curl 명령에 `-d @<filename>`을(를) 추가합니다.

```azurecli
#create a NetApp account  
curl -d @<filename> -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE?api-version=2019-11-01
```

```azurecli
#create a capacity pool  
curl -d @<filename> -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE?api-version=2019-11-01
```

```azurecli
#create a volume  
curl -d @<filename> -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes/MYNEWVOLUME?api-version=2019-11-01
```

```azurecli
 #create a volume snapshot  
curl -d @<filename> -X PUT -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/NETAPPACCOUNTGOESHERE/capacityPools/CAPACITYPOOLGOESHERE/volumes/MYNEWVOLUME/Snapshots/SNAPNAME?api-version=2019-11-01
```

### <a name="json-examples"></a>JSON 예

다음 예제에서는 NetApp 계정을 만드는 방법을 보여 줍니다.

```json
{ 
    "name": "MYNETAPPACCOUNT", 
    "type": "Microsoft.NetApp/netAppAccounts", 
    "location": "westus2", 
    "properties": { 
        "name": "MYNETAPPACCOUNT" 
    }
} 
```

다음 예제에서는 용량 풀을 만드는 방법을 보여 줍니다. 

```json
{
    "name": "MYNETAPPACCOUNT/POOLNAME",
    "type": "Microsoft.NetApp/netAppAccounts/capacityPools",
    "location": "westus2",
    "properties": {
        "name": "POOLNAME",
        "size": "4398046511104",
        "serviceLevel": "Premium"
    }
}
```

다음 예제에서는 새 볼륨을 만드는 방법을 보여 줍니다. 볼륨의 기본 프로토콜은 NFSV3입니다. 

```json
{
    "name": "MYNEWVOLUME",
    "type": "Microsoft.NetApp/netAppAccounts/capacityPools/volumes",
    "location": "westus2",
    "properties": {
        "serviceLevel": "Premium",
        "usageThreshold": "322122547200",
        "creationToken": "MY-FILEPATH",
        "snapshotId": "",
        "subnetId": "/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.Network/virtualNetworks/VNETGOESHERE/subnets/MYDELEGATEDSUBNET.sn"
         }
}
```

다음 예제에서는 볼륨의 스냅샷을 만드는 방법을 보여 줍니다. 

```json
{
    "name": "apitest2/apiPool01/apiVol01/snap02",
    "type": "Microsoft.NetApp/netAppAccounts/capacityPools/Volumes/Snapshots",
    "location": "westus2",
    "properties": {
         "name": "snap02",
        "fileSystemId": "0168704a-bbec-da81-2c29-503825fe7420"
    }
}
```

> [!NOTE] 
> 스냅샷을 만들려면 `fileSystemId`를 지정해야 합니다.  볼륨에 대한 GET 요청을 사용하여 `fileSystemId` 값을 얻을 수 있습니다. 

## <a name="next-steps"></a>다음 단계

[Azure NetApp Files REST API 참조 문서 참조](/rest/api/netapp/)
