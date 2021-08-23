---
title: Azure Data Factory에서 자격 증명 암호화
description: 자체 호스팅 통합 런타임을 사용하는 컴퓨터에서 온-프레미스 데이터 저장소의 자격 증명을 암호화하고 저장하는 방법을 알아봅니다.
author: lrtoyou1223
ms.service: data-factory
ms.subservice: integration-runtime
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: lle
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: f0cd1fed706ae22c02c6a96e9588d6cd65177e36
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566971"
---
# <a name="encrypt-credentials-for-on-premises-data-stores-in-azure-data-factory"></a>Azure Data Factory에서 온-프레미스 데이터 저장소에 대한 자격 증명 암호화

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

자체 호스팅 통합 런타임을 사용하는 컴퓨터에서 온-프레미스 데이터 저장소(중요한 정보가 있는 연결된 서비스)의 자격 증명을 암호화하고 저장할 수 있습니다. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

자격 증명이 있는 JSON 정의 파일을 <br/>[**New-AzDataFactoryV2LinkedServiceEncryptedCredential**](/powershell/module/az.datafactory/New-AzDataFactoryV2LinkedServiceEncryptedCredential) cmdlet에 전달하여 암호화된 자격 증명이 있는 출력 JSON 정의 파일을 생성합니다. 그런 다음 업데이트된 JSON 정의를 사용하여 연결된 서비스를 만듭니다.

## <a name="author-sql-server-linked-service"></a>SQL Server 연결된 서비스 작성
다음 콘텐츠로 원하는 폴더에 **SqlServerLinkedService.json** 이라는 JSON 파일을 만듭니다.  

파일을 저장하기 전에 `<servername>`, `<databasename>`, `<username>` 및 `<password>`를 SQL Server 값으로 바꿉니다. 그리고 `<integration runtime name>`을 통합 런타임의 이름으로 바꿉니다. 

```json
{
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": "Server=<servername>;Database=<databasename>;User ID=<username>;Password=<password>;Timeout=60"
        },
        "connectVia": {
            "type": "integrationRuntimeReference",
            "referenceName": "<integration runtime name>"
        },
        "name": "SqlServerLinkedService"
    }
}
```

## <a name="encrypt-credentials"></a>자격 증명 암호화
온-프레미스 자체 호스팅 통합 런타임에서 JSON 페이로드의 중요한 데이터를 암호화하기 위해 **New-AzDataFactoryV2LinkedServiceEncryptedCredential** 을 실행하고 JSON 페이로드를 전달합니다. 이 cmdlet을 사용하면 자격 증명이 DPAPI를 사용하여 암호화되고 자체 호스팅 통합 런타임 노드에 로컬로 저장됩니다. 자격 증명에 암호화된 참조를 포함하는 출력 페이로드는 다른 JSON 파일(이 경우 ‘encryptedLinkedService.json’)로 리디렉션될 수 있습니다.

```powershell
New-AzDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "SqlServerLinkedService" -DefinitionFile ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
```

## <a name="use-the-json-with-encrypted-credentials"></a>암호화된 자격 증명이 있는 JSON 사용
이제 암호화된 자격 증명이 포함된 이전 명령의 출력 JSON 파일을 사용하여 **SqlServerLinkedService** 를 설정합니다.

```powershell
Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -DefinitionFile ".\encryptedSqlServerLinkedService.json" 
```

## <a name="next-steps"></a>다음 단계
데이터 이동에 대한 보안 고려 사항에 대한 정보는 [ 데이터 이동 보안 고려 사항 ](data-movement-security-considerations.md)을 참조하십시오.

