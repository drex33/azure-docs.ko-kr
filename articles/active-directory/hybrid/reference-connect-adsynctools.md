---
title: 'Azure AD Connect: ADSyncTools PowerShell 참조 | Microsoft Docs'
description: 이 문서에서는 ADSyncTools.psm1 PowerShell 모듈에 대한 참조 정보를 제공합니다.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.date: 11/30/2020
ms.subservice: hybrid
ms.author: billmath
ms.topic: reference
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 768b037573d1114141ca9722feb6a9ecd510dd5a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528631"
---
# <a name="azure-ad-connect--adsynctools-powershell-reference"></a>Azure AD Connect: ADSyncTools PowerShell 참조
이 설명서에서는 Azure AD Connect에 포함된 ADSyncTools.psm1 PowerShell 모듈에 대해 다음과 같은 참조 정보를 제공합니다.

## <a name="install-the-adsynctools-powershell-module"></a>ADSyncTools PowerShell 모듈 설치
ADSyncTools PowerShell 모듈을 설치하려면 다음을 수행합니다.

1.  관리자 권한으로 Windows PowerShell을 엽니다.
2.  다음을 입력하거나 복사하여 붙여넣습니다. 
    ``` powershell
        [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
        Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force
        Import-module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\Tools\AdSyncTools"
    ```
3.  Enter 키를 누릅니다.
4.  모듈이 설치되었는지 확인하려면 다음을 입력하거나 복사하여 붙여넣음
    ```powershell
    Get-module AdSyncTools
    ```
5.  이제 모듈에 대한 정보가 표시됩니다.


## <a name="clear-adsynctoolsmsdsconsistencyguid"></a>Clear-ADSyncToolsMsDsConsistencyGuid
### SYNOPSIS
Active Directory 개체의 mS-DS-ConsistencyGuid 지우기
### SYNTAX
```
Clear-ADSyncToolsMsDsConsistencyGuid [-Identity] <Object> [<CommonParameters>]
```
### DESCRIPTION
대상 Active Directory 개체의 mS-DS-ConsistencyGuid 값을 지웁니다.
다중 도메인 포리스트에서 Active Directory 개체를 지원합니다.

### EXAMPLES
#### <a name="example-1"></a>예제 1
```
Clear-ADSyncToolsMsDsConsistencyGuid -Identity 'CN=User1,OU=Sync,DC=Contoso,DC=com'
```
#### <a name="example-2"></a>예제 2
```
Clear-ADSyncToolsMsDsConsistencyGuid -Identity 'User1@Contoso.com'
```
#### <a name="example-3"></a>예제 3
```
'User1@Contoso.com' | Clear-ADSyncToolsMsDsConsistencyGuid
```
### PARAMETERS
#### <a name="-identity"></a>-Identity
mS-DS-ConsistencyGuid를 지울 AD의 대상 개체
```yaml
Type: Object
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable. 자세한 내용은 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)를 참조하세요.
## <a name="connect-adsynctoolssqldatabase"></a>Connect-ADSyncToolsSqlDatabase
### SYNOPSIS
테스트 목적으로 SQL 데이터베이스에 연결
### SYNTAX
```
Connect-ADSyncToolsSqlDatabase [-Server] <String> [[-Instance] <String>] [[-Database] <String>]
 [[-Port] <String>] [[-UserName] <String>] [[-Password] <String>] [<CommonParameters>]
```
### DESCRIPTION
SQL 진단 관련 함수 및 유틸리티
### EXAMPLES
#### <a name="example-1"></a>예제 1
```
Connect-ADSyncToolsSqlDatabase -Server 'sqlserver01.contoso.com' -Database 'ADSync'
```
#### <a name="example-2"></a>예제 2
```
Connect-ADSyncToolsSqlDatabase -Server 'sqlserver01.contoso.com' -Instance 'INTANCE01' -Database 'ADSync'
```
### PARAMETERS
#### <a name="-server"></a>-Server
SQL Server 이름
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-instance"></a>-Instance
SQL Server 인스턴스 이름
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: False
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-database"></a>-Database
SQL Server 데이터베이스 이름
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: False
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-port"></a>-Port
SQL Server 포트(예:
49823)
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: False
Position: 4
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-username"></a>-UserName
SQL Server 로그인 사용자 이름
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: False
Position: 5
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-password"></a>-Password
SQL Server 로그인 암호
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: False
Position: 6
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable. 자세한 내용은 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)를 참조하세요.
## <a name="convertfrom-adsynctoolsaaddistinguishedname"></a>ConvertFrom-ADSyncToolsAadDistinguishedName
### SYNOPSIS
Azure AD 커넥터 DistinguishedName을 ImmutableId로 변환
### SYNTAX
```
ConvertFrom-ADSyncToolsAadDistinguishedName [-DistinguishedName] <String> [<CommonParameters>]
```
### DESCRIPTION
CN={514635484D4B376E38307176645973555049486139513D3D}와 같은 Azure AD 커넥터 DistinguishedName을 사용하여 해당 base64 ImmutableID 값(예: QF5HMK7n80qvdYsUPIHa9Q==)으로 변환합니다.
### EXAMPLES
#### <a name="example-1"></a>예제 1
```
ConvertFrom-ADSyncToolsAadDistinguishedName 'CN={514635484D4B376E38307176645973555049486139513D3D}'
```
### PARAMETERS
#### <a name="-distinguishedname"></a>-DistinguishedName
Azure AD 커넥터 공간 DistinguishedName
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable. 자세한 내용은 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)를 참조하세요.
## <a name="convertfrom-adsynctoolsimmutableid"></a>ConvertFrom-ADSyncToolsImmutableID
### SYNOPSIS
Base64 ImmutableId(SourceAnchor)를 GUID 값으로 변환
### SYNTAX
```
ConvertFrom-ADSyncToolsImmutableID [-Value] <String> [<CommonParameters>]
```
### DESCRIPTION
ImmutableID 값을 Base64 문자열에서 변환하여 GUID 값을 반환합니다. Base64 문자열을 GUID로 변환할 수 없는 경우 바이트 배열을 반환합니다.
### EXAMPLES
#### <a name="example-1"></a>예제 1
```
ConvertFrom-ADSyncToolsImmutableID 'iGhmiAEBERG7uxI0VniQqw=='
```
#### <a name="example-2"></a>예제 2
```
'iGhmiAEBERG7uxI0VniQqw==' | ConvertFrom-ADSyncToolsImmutableID
```
### PARAMETERS
#### <a name="-value"></a>-Value
Base64 형식의 ImmutableId
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable. 자세한 내용은 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)를 참조하세요.
## <a name="convertto-adsynctoolsaaddistinguishedname"></a>ConvertTo-ADSyncToolsAadDistinguishedName
### SYNOPSIS
ImmutableId를 Azure AD 커넥터 DistinguishedName으로 변환
### SYNTAX
```
ConvertTo-ADSyncToolsAadDistinguishedName [-ImmutableId] <String> [<CommonParameters>]
```
### DESCRIPTION
QF5HMK7n80qvdYsUPIHa9Q==와 같은 ImmutableId(SourceAnchor)를 사용하여 해당 Azure AD 커넥터 DistinguishedName 값(예: CN={514635484D4B376E38307176645973555049486139513D3D})으로 변환합니다.
### EXAMPLES
#### <a name="example-1"></a>예제 1
```
ConvertTo-ADSyncToolsAadDistinguishedName 'QF5HMK7n80qvdYsUPIHa9Q=='
```
### PARAMETERS
#### <a name="-immutableid"></a>-ImmutableId
ImmutableId(SourceAnchor)
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable. 자세한 내용은 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)를 참조하세요.
## <a name="convertto-adsynctoolscloudanchor"></a>ConvertTo-ADSyncToolsCloudAnchor
### SYNOPSIS
Base64 앵커를 CloudAnchor로 변환
### SYNTAX
```
ConvertTo-ADSyncToolsCloudAnchor [-Anchor] <String> [<CommonParameters>]
```
### DESCRIPTION
VAAAAFUAcwBlAHIAXwBjADcAMgA5ADAAMwBlAGQALQA3ADgAMQA2AC0ANAAxAGMAZAAtADkAMAA2ADYALQBlAGEAYwAzADMAZAAxADcAMQBkADcANwAAAA==와 같은 Base64 앵커를 사용하여 해당 CloudAnchor 값(예: User_abc12345-1234-abcd-9876-ab0123456789)으로 변환합니다.
### EXAMPLES
#### <a name="example-1"></a>예제 1
```
ConvertTo-ADSyncToolsCloudAnchor "VAAAAFUAcwBlAHIAXwBjADcAMgA5ADAAMwBlAGQALQA3ADgAMQA2AC0ANAAxAGMAZAAtADkAMAA2ADYALQBlAGEAYwAzADMAZAAxADcAMQBkADcANwAAAA=="
```
#### <a name="example-2"></a>예제 2
```
"VAAAAFUAcwBlAHIAXwBjADcAMgA5ADAAMwBlAGQALQA3ADgAMQA2AC0ANAAxAGMAZAAtADkAMAA2ADYALQBlAGEAYwAzADMAZAAxADcAMQBkADcANwAAAA==" | ConvertTo-ADSyncToolsCloudAnchor
```
### PARAMETERS
#### <a name="-anchor"></a>-Anchor
Base64 앵커
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable. 자세한 내용은 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)를 참조하세요.
## <a name="convertto-adsynctoolsimmutableid"></a>ConvertTo-ADSyncToolsImmutableID
### SYNOPSIS
GUID(ObjectGUID/ms-Ds-Consistency-Guid)를 Base64 문자열로 변환
### SYNTAX
```
ConvertTo-ADSyncToolsImmutableID [-Value] <Object> [<CommonParameters>]
```
### DESCRIPTION
GUID, GUID 문자열 또는 바이트 배열 형식의 값을 Base64 문자열로 변환합니다.
### EXAMPLES
#### <a name="example-1"></a>예제 1
```
ConvertTo-ADSyncToolsImmutableID '88888888-0101-3333-cccc-1234567890cd'
```
#### <a name="example-2"></a>예제 2
```
'88888888-0101-3333-cccc-1234567890cd' | ConvertTo-ADSyncToolsImmutableID
```
### PARAMETERS
#### <a name="-value"></a>-Value
GUID, GUID 문자열 또는 바이트 배열
```yaml
Type: Object
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable. 자세한 내용은 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)를 참조하세요.
## <a name="export-adsynctoolsaaddisconnectors"></a>Export-ADSyncToolsAadDisconnectors
### SYNOPSIS
Azure AD 디스커넥터 개체 내보내기
### SYNTAX
```
Export-ADSyncToolsAadDisconnectors [[-SyncObjectType] <Object>] [<CommonParameters>]
```
### DESCRIPTION
CSExport 도구를 실행하여 모든 디스커넥터를 XML로 내보낸 다음, 이 XML 출력을 사용하여 UserPrincipalName, Mail, SourceAnchor, DistinguishedName, CsObjectId, ObjectType, ConnectorId, CloudAnchor가 있는 CSV 파일로 변환합니다.
### EXAMPLES
#### <a name="example-1"></a>예제 1
```
Export-ADSyncToolsDisconnectors -SyncObjectType 'PublicFolder'
```
모든 PublicFolder 디스커넥터 개체를 CSV로 내보냅니다.
#### <a name="example-2"></a>예제 2
```
Export-ADSyncToolsDisconnectors
```
모든 디스커넥터 개체를 CSV로 내보냅니다.
### PARAMETERS
#### <a name="-syncobjecttype"></a>-SyncObjectType
출력에 포함할 ObjectType
```yaml
Type: Object
Parameter Sets: (All)
Aliases:
Required: False
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable. 자세한 내용은 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)를 참조하세요.
### INPUTS
지정된 개체 형식의 디스커넥터만 내보내려는 경우 ObjectType 인수를 사용합니다.
### OUTPUTS
다음을 포함하여 디스커넥터 개체가 있는 CSV 파일을 내보냅니다. 

UserPrincipalName, Mail, SourceAnchor, DistinguishedName, CsObjectId, ObjectType, ConnectorId, CloudAnchor

## <a name="export-adsynctoolshybridaadjoinreport"></a>Export-ADSyncToolsHybridAadJoinReport
### SYNOPSIS
Active Directory 컴퓨터 개체에 저장된 인증서, 특히 하이브리드 Azure AD 조인 기능에서 발급된 인증서에 대한 보고서를 생성합니다.
### SYNTAX
#### <a name="singleobject"></a>SingleObject
```
Export-ADSyncToolsHybridAadJoinReport [-DN] <String> [[-Filename] <String>] [<CommonParameters>]
```
#### <a name="multipleobjects"></a>MultipleObjects
```
Export-ADSyncToolsHybridAadJoinReport [-OU] <String> [[-Filename] <String>] [<CommonParameters>]
```
### DESCRIPTION
이 도구는 AD의 컴퓨터 개체 UserCertificate 속성에 있는 모든 인증서를 확인하고, 만료되지 않은 각 인증서에 대해 인증서가 하이브리드 Azure AD 조인 기능에 대해 발급되었는지(즉, 주체 이름이 CN={ObjectGUID}인지) 확인합니다.
버전 1.4 이전에는 Azure AD Connect가 하나 이상의 인증서를 포함하는 모든 컴퓨터를 Azure AD에 동기화했지만 Azure AD Connect 버전 1.4 이상에서는 ADSync 엔진이 하이브리드 Azure AD 조인 인증서를 식별할 수 있으며, 유효한 하이브리드 Azure AD 조인 인증서가 없을 경우 컴퓨터 개체가 Azure AD에 동기화되지 않도록 “cloudfilter”(제외)합니다.
이미 AD에 동기화되었지만 유효한 하이브리드 Azure AD 조인 인증서가 없는 Azure AD 디바이스 개체는 Azure AD Connect가 Azure AD(CloudFiltered=TRUE)에서 삭제합니다.
### EXAMPLES
#### <a name="example-1"></a>예제 1
```
Export-ADSyncToolsHybridAzureADjoinCertificateReport -DN 'CN=Computer1,OU=SYNC,DC=Fabrikam,DC=com'
```
#### <a name="example-2"></a>예제 2
```
Export-ADSyncToolsHybridAzureADjoinCertificateReport -OU 'OU=SYNC,DC=Fabrikam,DC=com' -Filename "MyHybridAzureADjoinReport.csv" -Verbose
```
### PARAMETERS
#### <a name="-dn"></a>-DN
컴퓨터 DistinguishedName
```yaml
Type: String
Parameter Sets: SingleObject
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```
#### <a name="-ou"></a>-OU
AD OrganizationalUnit
```yaml
Type: String
Parameter Sets: MultipleObjects
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```
#### <a name="-filename"></a>-Filename
출력 CSV 파일 이름(선택 사항)
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: False
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable. 자세한 내용은 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)를 참조하세요.
### RELATED LINKS
자세한 정보: [Azure AD Connect 1.4.xx.x 및 디바이스 누락 이해](/troubleshoot/azure/active-directory/reference-connect-device-disappearance)

## <a name="export-adsynctoolsobjects"></a>Export-ADSyncToolsObjects
### SYNOPSIS
Azure AD Connect 개체를 XML 파일로 내보내기
### SYNTAX
#### <a name="objectid"></a>ObjectId
```
Export-ADSyncToolsObjects [-ObjectId] <Object> [-Source] <Object> [-ExportSerialized] [<CommonParameters>]
```
#### <a name="distinguishedname"></a>DistinguishedName
```
Export-ADSyncToolsObjects [-DistinguishedName] <Object> [-ConnectorName] <Object> [-ExportSerialized]
 [<CommonParameters>]
```
### DESCRIPTION
메타버스에서 내부 ADSync 개체를 내보내고, 커넥터 공간에서 연결된 관련 개체를 내보냅니다.
### EXAMPLES
#### <a name="example-1"></a>예제 1
```
Export-ADSyncToolsObjects -ObjectId '9D220D58-0700-E911-80C8-000D3A3614C0' -Source Metaverse
```
#### <a name="example-2"></a>예제 2
```
Export-ADSyncToolsObjects -ObjectId '9e220d58-0700-e911-80c8-000d3a3614c0' -Source ConnectorSpace
```
#### <a name="example-3"></a>예제 3
```
Export-ADSyncToolsObjects -DistinguishedName 'CN=User1,OU=ADSync,DC=Contoso,DC=com' -ConnectorName 'Contoso.com'
```
### PARAMETERS
#### <a name="-objectid"></a>-ObjectId
ObjectId는 해당 커넥터 공간이나 메타버스에서 개체의 고유 식별자입니다.
```yaml
Type: Object
Parameter Sets: ObjectId
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```
#### <a name="-source"></a>-Source
Source는 개체가 상주하는 테이블로, ConnectorSpace 또는 Metaverse일 수 있습니다.
```yaml
Type: Object
Parameter Sets: ObjectId
Aliases:
Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```
#### <a name="-distinguishedname"></a>-DistinguishedName
DistinguishedName은 해당 커넥터 공간에서 개체의 식별자입니다.
```yaml
Type: Object
Parameter Sets: DistinguishedName
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```
#### <a name="-connectorname"></a>-ConnectorName
ConnectorName은 개체가 상주하는 커넥터 공간의 이름입니다.
```yaml
Type: Object
Parameter Sets: DistinguishedName
Aliases:
Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```
#### <a name="-exportserialized"></a>-ExportSerialized
ExportSerialized는 추가 XML 파일을 내보냅니다.
```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:
Required: False
Position: 3
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable. 자세한 내용은 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)를 참조하세요.
## <a name="export-adsynctoolsrunhistory"></a>Export-ADSyncToolsRunHistory
### SYNOPSIS
Azure AD Connect 실행 기록 내보내기
### SYNTAX
```
Export-ADSyncToolsRunHistory [-TargetName] <String> [<CommonParameters>]
```
### DESCRIPTION
Azure AD Connect 실행 프로필과 실행 단계 결과를 각각 CSV 및 XML 형식으로 내보내는 함수입니다.
결과로 생성된 실행 프로필 CSV 파일은 스프레드시트로 가져올 수 있고, 실행 단계 XML 파일은 Import-Clixml을 사용하여 가져올 수 있습니다.
### EXAMPLES
#### <a name="example-1"></a>예제 1
```
Export-ADSyncToolsRunHistory -TargetName MyADSyncHistory
```
### PARAMETERS
#### <a name="-targetname"></a>-TargetName
출력 파일의 이름
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable. 자세한 내용은 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)를 참조하세요.
## <a name="export-adsynctoolssourceanchorreport"></a>Export-ADSyncToolsSourceAnchorReport
### SYNOPSIS
ms-ds-Consistency-Guid 보고서 내보내기
### SYNTAX
```
Export-ADSyncToolsSourceAnchorReport [-AlternativeLoginId] [-UserPrincipalName] <String>
 [-ImmutableIdGUID] <String> [-Output] <String> [<CommonParameters>]
```
### DESCRIPTION
Import-ADSyncToolsSourceAnchor의 가져오기 CSV 파일에 따라 ms-ds-Consistency-Guid 보고서를 생성합니다.
### EXAMPLES
#### <a name="example-1"></a>예제 1
```
Import-Csv .\AllSyncUsers.csv | Export-ADSyncToolsSourceAnchorReport -Output ".\AllSyncUsers-Report"
```
#### <a name="example-2"></a>예제 2
```
Another example of how to use this cmdlet
```
### PARAMETERS
#### <a name="-alternativeloginid"></a>-AlternativeLoginId
대체 로그인 ID(메일)를 사용합니다.
```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:
Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-userprincipalname"></a>-UserPrincipalName
UserPrincipalName
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="-immutableidguid"></a>-ImmutableIdGUID
ImmutableIdGUID입니다.
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="-output"></a>-Output
CSV 및 LOG 파일의 출력 파일 이름입니다.
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable. 자세한 내용은 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)를 참조하세요.
## <a name="get-adsynctoolsaadobject"></a>Get-ADSyncToolsAadObject
### SYNOPSIS
지정된 SyncObjectType의 동기화된 개체 가져오기
### SYNTAX
```
Get-ADSyncToolsAadObject [-SyncObjectType] <Object> [-Credential] <PSCredential> [<CommonParameters>]
```
### DESCRIPTION
지정된 개체 클래스(SyncObjectType)의 동기화된 개체를 Azure AD에서 모두 읽습니다.
### EXAMPLES
#### <a name="example-1"></a>예제 1
```
Get-ADSyncToolsAadObject -SyncObjectType 'publicFolder' -Credential $(Get-Credential)
```
### PARAMETERS
#### <a name="-syncobjecttype"></a>-SyncObjectType
개체 유형
```yaml
Type: Object
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```
#### <a name="-credential"></a>-Credential
Azure AD 전역 관리자 자격 증명
```yaml
Type: PSCredential
Parameter Sets: (All)
Aliases:
Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters

이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable. 자세한 내용은 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)를 참조하세요.

### OUTPUTS

이 cmdlet은 동기화 클라이언트가 동기화하는 “섀도” 속성을 반환합니다. Azure AD의 해당 속성에 저장된 실제 값과는 다를 수도 있습니다. 예를 들어 확인되지 않은 도메인 접미사 ‘user@nonverified.domain’과 동기화된 사용자 UPN은 Azure AD에서 테넌트의 기본 도메인인 ‘user@tenantname.onmicrosoft.com’으로 변환된 UPN 접미사를 갖게 됩니다. 이 경우 Get-ADSyncToolsAadObject는 Azure AD의 실제 값인 ‘user@tenantname.onmicrosoft.com’이 아니라 “섀도” 값인 user@nonverified.domain을 반환합니다.

## <a name="get-adsynctoolsmsdsconsistencyguid"></a>Get-ADSyncToolsMsDsConsistencyGuid
### SYNOPSIS
Active Directory 개체의 ms-ds-ConsistencyGuid 가져오기
### SYNTAX
```
Get-ADSyncToolsMsDsConsistencyGuid [-Identity] <Object> [<CommonParameters>]
```
### DESCRIPTION
대상 Active Directory 개체의 mS-DS-ConsistencyGuid 특성 값을 GUID 형식으로 반환합니다. 다중 도메인 포리스트에서 Active Directory 개체를 지원합니다.
### EXAMPLES
#### <a name="example-1"></a>예제 1
```
Get-ADSyncToolsMsDsConsistencyGuid -Identity 'CN=User1,OU=Sync,DC=Contoso,DC=com'
```
#### <a name="example-2"></a>예제 2
```
Get-ADSyncToolsMsDsConsistencyGuid -Identity 'User1@Contoso.com'
```
#### <a name="example-3"></a>예제 3
```
'User1@Contoso.com' | Get-ADSyncToolsMsDsConsistencyGuid
```
### PARAMETERS
#### <a name="-identity"></a>-Identity
가져올 AD의 대상 개체
```yaml
Type: Object
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable. 자세한 내용은 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)를 참조하세요.
## <a name="get-adsynctoolsrunhistory"></a>Get-ADSyncToolsRunHistory
### SYNOPSIS
Azure AD Connect 실행 기록 가져오기
### SYNTAX
```
Get-ADSyncToolsRunHistory [[-Days] <Int32>] [<CommonParameters>]
```
### DESCRIPTION
Azure AD Connect 실행 기록을 XML 형식으로 반환하는 함수입니다.
### EXAMPLES
#### <a name="example-1"></a>예제 1
```
Get-ADSyncToolsRunHistory
```
#### <a name="example-2"></a>예제 2
```
Get-ADSyncToolsRunHistory -Days 3
```
### PARAMETERS
#### <a name="-days"></a>-Days
기록을 수집할 이전 일수입니다(기본값 = 1).
```yaml
Type: Int32
Parameter Sets: (All)
Aliases:
Required: False
Position: 1
Default value: 1
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable. 자세한 내용은 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)를 참조하세요.
## <a name="get-adsynctoolsrunhistorylegacywmi"></a>Get-ADSyncToolsRunHistoryLegacyWmi
### SYNOPSIS
Azure AD Connect 이전 버전의 Azure AD Connect 실행 기록 가져오기(WMI)
### SYNTAX
```
Get-ADSyncToolsRunHistoryLegacyWmi [[-Days] <Int32>] [<CommonParameters>]
```
### DESCRIPTION
Azure AD Connect 실행 기록을 XML 형식으로 반환하는 함수입니다.
### EXAMPLES
#### <a name="example-1"></a>예제 1
```
Get-ADSyncToolsRunHistory
```
#### <a name="example-2"></a>예제 2
```
Get-ADSyncToolsRunHistory -Days 3
```
### PARAMETERS
#### <a name="-days"></a>-Days
기록을 수집할 이전 일수입니다(기본값 = 1).
```yaml
Type: Int32
Parameter Sets: (All)
Aliases:
Required: False
Position: 1
Default value: 1
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable. 자세한 내용은 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)를 참조하세요.
## <a name="get-adsynctoolssqlbrowserinstances"></a>Get-ADSyncToolsSqlBrowserInstances
### SYNOPSIS
SQL Browser 서비스에서 SQL Server 인스턴스 가져오기
### SYNTAX
```
Get-ADSyncToolsSqlBrowserInstances [[-Server] <String>]
```
### DESCRIPTION
SQL 진단 관련 함수 및 유틸리티
### EXAMPLES
#### <a name="example-1"></a>예제 1
```
Get-ADSyncToolsSqlBrowserInstances -Server 'sqlserver01'
```
### PARAMETERS
#### <a name="-server"></a>-Server
SQL Server 이름
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
## <a name="get-adsynctoolstenantazureenvironment"></a>Get-ADSyncToolsTenantAzureEnvironment
### SYNOPSIS
사용자가 속한 Azure 환경을 가져오는 도우미 함수
### SYNTAX
```
Get-ADSyncToolsTenantAzureEnvironment [-Credential] <PSCredential> [<CommonParameters>]
```
### DESCRIPTION
이 함수는 Azure 환경을 확인하기 위해 Oauth 검색 엔드포인트를 호출하여 CloudInstance와 tenant_region_scope를 가져옵니다.
https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration

### EXAMPLES
#### <a name="example-1"></a>예제 1
```
Get-ADSyncToolsTenantAzureEnvironment -Credential (Get-Credential)
```
### PARAMETERS
#### <a name="-credential"></a>-Credential
사용자의 PowerShell 자격 증명 개체

```yaml
Type: PSCredential
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```

#### <a name="commonparameters"></a>CommonParameters
이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable. 자세한 내용은 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)를 참조하세요.
### INPUTS
사용자의 PowerShell 자격 증명 개체

### OUTPUTS
Azure 환경(문자열)
## <a name="get-adsynctoolstls12"></a>Get-ADSyncToolsTls12
### SYNOPSIS
.NET Framework에 대한 Client\Server TLS 1.2 설정 가져오기
### SYNTAX
```
Get-ADSyncToolsTls12 [<CommonParameters>]
```
### DESCRIPTION
.NET Framework용 TLS 1.2와 관련된 정보를 레지스트리에서 읽습니다.

| 경로                                                         | Name                     |
| ------------------------------------------------------------ | ------------------------ |
| HKLM:\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319 | SystemDefaultTlsVersions |
| HKLM:\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319 | SchUseStrongCrypto       |
| HKLM:\SOFTWARE\Microsoft\.NETFramework\v4.0.30319            | SystemDefaultTlsVersions |
| HKLM:\SOFTWARE\Microsoft\.NETFramework\v4.0.30319            | SchUseStrongCrypto       |
| HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server | 사용                  |
| HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server | DisabledByDefault        |
| HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client | 사용                  |
| HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client | DisabledByDefault        |

### EXAMPLES
#### <a name="example-1"></a>예제 1
```
Get-ADSyncToolsTls12
```
### PARAMETERS
#### <a name="commonparameters"></a>CommonParameters
이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable. 자세한 내용은 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)를 참조하세요.
### RELATED LINKS
자세한 정보: [Azure AD Connect에 대한 TLS 1.2 적용](reference-connect-tls-enforcement.md)

## <a name="import-adsynctoolsobjects"></a>Import-ADSyncToolsObjects
### SYNOPSIS
XML 파일에서 Azure AD Connect 개체 가져오기
### SYNTAX
```
Import-ADSyncToolsObjects [-Path] <String> [<CommonParameters>]
```
### DESCRIPTION
Export-ADSyncToolsObjects를 사용하여 내보낸 XML 파일에서 내부 ADSync 개체를 가져옵니다.
### EXAMPLES
#### <a name="example-1"></a>예제 1
```
Import-ADSyncToolsObjects -Path .\20210224-003104_81275a23-0168-eb11-80de-00155d188c11_MV.xml
```
### PARAMETERS
#### <a name="-path"></a>-Path
가져올 XML 파일의 경로
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable. 자세한 내용은 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)를 참조하세요.
## <a name="import-adsynctoolsrunhistory"></a>Import-ADSyncToolsRunHistory
### SYNOPSIS
Azure AD Connect 실행 기록 가져오기
### SYNTAX
```
Import-ADSyncToolsRunHistory [-Path] <String> [<CommonParameters>]
```
### DESCRIPTION
Export-ADSyncToolsRunHistory를 사용하여 만든 XML에서 Azure AD Connect 실행 단계 결과를 가져오는 함수입니다.
### EXAMPLES
#### <a name="example-1"></a>예제 1
```
Export-ADSyncToolsRunHistory -Path .\RunHistory-RunStep.xml
```
### PARAMETERS
#### <a name="-path"></a>-Path
가져올 XML 파일의 경로
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable. 자세한 내용은 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)를 참조하세요.
## <a name="import-adsynctoolssourceanchor"></a>Import-ADSyncToolsSourceAnchor
### SYNOPSIS
Azure AD에서 ImmutableID 가져오기
### SYNTAX
```
Import-ADSyncToolsSourceAnchor [-Output] <String> [-IncludeSyncUsersFromRecycleBin] [<CommonParameters>]
```
### DESCRIPTION
GUID 형식 요구 사항: MSOnline PowerShell 모듈의 ImmutableID 값이 있는 모든 Azure AD 동기화된 사용자가 포함된 파일을 생성합니다.
### EXAMPLES
#### <a name="example-1"></a>예제 1
```
Import-ADSyncToolsSourceAnchor -OutputFile '.\AllSyncUsers.csv'
```
#### <a name="example-2"></a>예제 2
```
Another example of how to use this cmdlet
```
### PARAMETERS
#### <a name="-output"></a>-Output
출력 CSV 파일입니다.
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-includesyncusersfromrecyclebin"></a>-IncludeSyncUsersFromRecycleBin
Azure AD 휴지통에서 동기화된 사용자를 가져옵니다.
```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:
Required: False
Position: Named
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable. 자세한 내용은 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)를 참조하세요.
## <a name="invoke-adsynctoolssqlquery"></a>Invoke-ADSyncToolsSqlQuery
### SYNOPSIS
테스트 목적으로 데이터베이스에 대해 SQL 쿼리 호출
### SYNTAX
```
Invoke-ADSyncToolsSqlQuery [-SqlConnection] <SqlConnection> [[-Query] <String>] [<CommonParameters>]
```
### DESCRIPTION
SQL 진단 관련 함수 및 유틸리티
### EXAMPLES
#### <a name="example-1"></a>예제 1
```
New-ADSyncToolsSqlConnection -Server SQLserver01.Contoso.com -Port 49823 | Invoke-ADSyncToolsSqlQuery
```
#### <a name="example-2"></a>예제 2
```
$sqlConn = New-ADSyncToolsSqlConnection -Server SQLserver01.Contoso.com -Port 49823
```
Invoke-ADSyncToolsSqlQuery -SqlConnection $sqlConn -Query ‘SELECT *, database_id FROM sys.databases’
### PARAMETERS
#### <a name="-sqlconnection"></a>-SqlConnection
SQL 연결
```yaml
Type: SqlConnection
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="-query"></a>-Query
SQL 쿼리
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: False
Position: 2
Default value: SELECT name, database_id FROM sys.databases
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable. 자세한 내용은 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)를 참조하세요.

## <a name="remove-adsynctoolsaadobject"></a>Remove-ADSyncToolsAadObject
### SYNOPSIS
Azure AD에서 분리된 동기화된 개체 제거
### SYNTAX
#### <a name="csvinput"></a>CsvInput
```
Remove-ADSyncToolsAadObject [-Credential] <PSCredential> [-InputCsvFilename] <Object> [-WhatIf] [-Confirm]
 [<CommonParameters>]
```
#### <a name="objectinput"></a>ObjectInput
```
Remove-ADSyncToolsAadObject [-Credential] <PSCredential> [-SourceAnchor] <Object> [-SyncObjectType] <Object>
 [-WhatIf] [-Confirm] [<CommonParameters>]
```
### DESCRIPTION
SourceAnchor와 ObjecType을 기준으로, 동기화된 개체를 Azure AD에서 10개 개체의 일괄 처리로 삭제합니다. Export-ADSyncToolsAadDisconnectors를 사용하여 CSV 파일을 생성할 수 있습니다.
### EXAMPLES
#### <a name="example-1"></a>예제 1
```
Remove-ADSyncToolsAadObject -InputCsvFilename .\DeleteObjects.csv -Credential (Get-Credential)
```
#### <a name="example-2"></a>예제 2
```
Remove-ADSyncToolsAadObject -SourceAnchor '2epFRNMCPUqhysJL3SWL1A==' -SyncObjectType 'publicFolder' -Credential (Get-Credential)
```
### PARAMETERS
#### <a name="-credential"></a>-Credential
Azure AD 전역 관리자 자격 증명
```yaml
Type: PSCredential
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```
#### <a name="-inputcsvfilename"></a>-InputCsvFilename
CSV 입력 파일 이름
```yaml
Type: Object
Parameter Sets: CsvInput
Aliases:
Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```
#### <a name="-sourceanchor"></a>-SourceAnchor
개체 SourceAnchor
```yaml
Type: Object
Parameter Sets: ObjectInput
Aliases:
Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```
#### <a name="-syncobjecttype"></a>-SyncObjectType
개체 유형
```yaml
Type: Object
Parameter Sets: ObjectInput
Aliases:
Required: True
Position: 3
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```
#### <a name="-whatif"></a>-WhatIf
cmdlet을 실행할 경우 발생하는 일을 표시합니다.
cmdlet은 실행되지 않습니다.
```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: wi
Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-confirm"></a>-Confirm
cmdlet을 실행하기 전에 확인을 요청합니다.
```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: cf
Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable. 자세한 내용은 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)를 참조하세요.
### INPUTS
InputCsvFilename은 최소한 SourceAnchor, SyncObjectType 열 2개가 있는 CSV 파일을 가리켜야 합니다.
### OUTPUTS
ExportDeletions 작업의 결과를 표시합니다. 부인: 휴지통이 있는 사용자 개체 외에 이 함수를 사용하여 삭제된 다른 개체 형식은 복구할 수 없습니다.
## <a name="remove-adsynctoolsexpiredcertificates"></a>Remove-ADSyncToolsExpiredCertificates
### SYNOPSIS
UserCertificate 특성에서 만료된 인증서를 제거하는 스크립트입니다.
### SYNTAX
```
Remove-ADSyncToolsExpiredCertificates [-TargetOU] <String> [[-BackupOnly] <Boolean>] [-ObjectClass] <String>
 [<CommonParameters>]
```
### DESCRIPTION
이 스크립트는 Active Directory 도메인의 대상 조직 구성 단위에서 모든 개체를 가져와서 개체 클래스(User/Computer)를 기준으로 필터링하고, UserCertificate 특성에 있는 만료된 인증서를 모두 삭제합니다.
기본적으로(BackupOnly 모드) 만료된 인증서만 파일에 백업하고, AD에서는 변경하지 않습니다.
-BackupOnly $false를 사용하면 개체의 UserCertificate 특성에 있는 만료된 인증서가 파일에 복사된 후 Active Directory에서 제거됩니다.
각 인증서는 별도의 파일 이름(ObjectClass_ObjectGUID_CertThumprint.cer)으로 백업됩니다. 또한 스크립트는 실제로 수행된 작업(건너뜀/내보냄/삭제됨)을 포함하여 유효하거나 만료된 인증서가 있는 모든 사용자를 표시하는 CSV 형식의 로그 파일을 만듭니다.
### EXAMPLES
#### <a name="example-1"></a>예제 1
```
Check all users in target OU - Expired Certificates will be copied to separated files and no certificates will be removed
```
Remove-ADSyncToolsExpiredCertificates -TargetOU "OU=Users,OU=Corp,DC=Contoso,DC=com" -ObjectClass user
#### <a name="example-2"></a>예제 2
```
Delete Expired Certs from all Computer objects in target OU - Expired Certificates will be copied to files and removed from AD
```
Remove-ADSyncToolsExpiredCertificates -TargetOU "OU=Computers,OU=Corp,DC=Contoso,DC=com" -ObjectClass computer -BackupOnly $false
### PARAMETERS
#### <a name="-targetou"></a>-TargetOU
AD 개체를 조회할 대상 OU입니다.
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-backuponly"></a>-BackupOnly
BackupOnly는 AD에서 어떤 인증서도 삭제하지 않습니다.
```yaml
Type: Boolean
Parameter Sets: (All)
Aliases:
Required: False
Position: 2
Default value: True
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-objectclass"></a>-ObjectClass
개체 클래스 필터입니다.
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 3
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable. 자세한 내용은 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)를 참조하세요.
## <a name="repair-adsynctoolsautoupgradestate"></a>Repair-ADSyncToolsAutoUpgradeState
### SYNOPSIS
Azure AD Connect AutoUpgrade 상태 복구
### SYNTAX
```
Repair-ADSyncToolsAutoUpgradeState
```
### DESCRIPTION
빌드 1.1.524(2017년 5월)에서 도입된 AutoUpgrade와 관련된 문제로, AutoUpgrade를 사용하는 동안 새 버전의 온라인 검사를 사용할 수 없는 문제를 해결합니다.
### EXAMPLES
#### <a name="example-1"></a>예제 1
```
Repair-ADSyncToolsAutoUpgradeState
```
## <a name="resolve-adsynctoolssqlhostaddress"></a>Resolve-ADSyncToolsSqlHostAddress
### SYNOPSIS
SQL 서버 이름 확인
### SYNTAX
```
Resolve-ADSyncToolsSqlHostAddress [-Server] <String> [<CommonParameters>]
```
### DESCRIPTION
SQL 진단 관련 함수 및 유틸리티
### EXAMPLES
#### <a name="example-1"></a>예제 1
```
Resolve-ADSyncToolsSqlHostAddress -Server 'sqlserver01'
```
### PARAMETERS
#### <a name="-server"></a>-Server
SQL Server 이름
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable. 자세한 내용은 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)를 참조하세요.
## <a name="search-adsynctoolsadobject"></a>Search-ADSyncToolsADobject
### SYNOPSIS
Active Directory 포리스트에서 UserPrincipalName, sAMAccountName 또는 DistinguishedName으로 Active Directory 개체 검색
### SYNTAX
```
Search-ADSyncToolsADobject [-Identity] <Object> [<CommonParameters>]
```
### DESCRIPTION
다중 도메인 쿼리를 지원하고 mS-DS-ConsistencyGuid를 비롯한 모든 필수 속성을 반환합니다.
### EXAMPLES
#### <a name="example-1"></a>예제 1
```
Search-ADSyncToolsADobject 'CN=user1,OU=Sync,DC=Contoso,DC=com'
```
#### <a name="example-2"></a>예제 2
```
Search-ADSyncToolsADobject -Identity "user1@Contoso.com"
```
#### <a name="example-3"></a>예제 3
```
Get-ADUser 'CN=user1,OU=Sync,DC=Contoso,DC=com' | Search-ADSyncToolsADobject
```
### PARAMETERS
#### <a name="-identity"></a>-Identity
ConsistencyGuid를 설정할 AD의 대상 사용자입니다.
```yaml
Type: Object
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable. 자세한 내용은 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)를 참조하세요.
## <a name="set-adsynctoolsmsdsconsistencyguid"></a>Set-ADSyncToolsMsDsConsistencyGuid
### SYNOPSIS
Active Directory 개체의 ms-ds-ConsistencyGuid 설정
### SYNTAX
```
Set-ADSyncToolsMsDsConsistencyGuid [-Identity] <Object> [-Value] <Object> [<CommonParameters>]
```
### DESCRIPTION
대상 Active Directory 사용자의 mS-DS-ConsistencyGuid 특성 값을 설정합니다.
다중 도메인 포리스트에서 Active Directory 개체를 지원합니다.
### EXAMPLES
#### <a name="example-1"></a>예제 1
```
Set-ADSyncToolsMsDsConsistencyGuid -Identity 'CN=User1,OU=Sync,DC=Contoso,DC=com' -Value '88666888-0101-1111-bbbb-1234567890ab'
```
#### <a name="example-2"></a>예제 2
```
Set-ADSyncToolsMsDsConsistencyGuid -Identity 'CN=User1,OU=Sync,DC=Contoso,DC=com' -Value 'GGhsjYwBEU+buBsE4sqhtg=='
```
#### <a name="example-3"></a>예제 3
```
Set-ADSyncToolsMsDsConsistencyGuid 'User1@Contoso.com' '8d6c6818-018c-4f11-9bb8-1b04e2caa1b6'
```
#### <a name="example-4"></a>예제 4
```
Set-ADSyncToolsMsDsConsistencyGuid 'User1@Contoso.com' 'GGhsjYwBEU+buBsE4sqhtg=='
```
#### <a name="example-5"></a>예제 5
```
'88666888-0101-1111-bbbb-1234567890ab' | Set-ADSyncToolsMsDsConsistencyGuid -Identity User1
```
#### <a name="example-6"></a>예제 6
```
'GGhsjYwBEU+buBsE4sqhtg==' | Set-ADSyncToolsMsDsConsistencyGuid User1
```
### PARAMETERS
#### <a name="-identity"></a>-Identity
mS-DS-ConsistencyGuid를 설정할 AD의 대상 개체
```yaml
Type: Object
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="-value"></a>-Value
설정할 값(ImmutableId, 바이트 배열, GUID, GUID 문자열 또는 Base64 문자열)
```yaml
Type: Object
Parameter Sets: (All)
Aliases:
Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable. 자세한 내용은 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)를 참조하세요.
## <a name="set-adsynctoolstls12"></a>Set-ADSyncToolsTls12
### SYNOPSIS
.NET Framework에 대한 Client\Server TLS 1.2 설정 지정
### SYNTAX
```
Set-ADSyncToolsTls12 [[-Enabled] <Boolean>] [<CommonParameters>]
```
### DESCRIPTION
.NET Framework용 TLS 1.2를 사용하거나 사용하지 않도록 설정하는 레지스트리 항목을 설정합니다.

| 경로                                                         | Name                     |
| ------------------------------------------------------------ | ------------------------ |
| HKLM:\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319 | SystemDefaultTlsVersions |
| HKLM:\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319 | SchUseStrongCrypto       |
| HKLM:\SOFTWARE\Microsoft\.NETFramework\v4.0.30319            | SystemDefaultTlsVersions |
| HKLM:\SOFTWARE\Microsoft\.NETFramework\v4.0.30319            | SchUseStrongCrypto       |
| HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server | 사용                  |
| HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server | DisabledByDefault        |
| HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client | 사용                  |
| HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client | DisabledByDefault        |

매개 변수 없이 cmdlet을 실행하면 .NET Framework용 TLS 1.2가 사용하도록 설정됩니다.

### EXAMPLES
#### <a name="example-1"></a>예제 1
```
Set-ADSyncToolsTls12
```
#### <a name="example-2"></a>예제 2
```
Set-ADSyncToolsTls12 -Enabled $true
```
### PARAMETERS
#### <a name="-enabled"></a>-Enabled
TLS 1.2 사용
```yaml
Type: Boolean
Parameter Sets: (All)
Aliases:
Required: False
Position: 1
Default value: True
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable. 자세한 내용은 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)를 참조하세요.
### RELATED LINKS
자세한 정보: [Azure AD Connect에 대한 TLS 1.2 적용](reference-connect-tls-enforcement.md)

## <a name="test-adsynctoolssqlnetworkport"></a>Test-ADSyncToolsSqlNetworkPort
### SYNOPSIS
SQL Server 네트워크 포트 테스트
### SYNTAX
```
Test-ADSyncToolsSqlNetworkPort [[-Server] <String>] [[-Port] <String>]
```
### DESCRIPTION
SQL 진단 관련 함수 및 유틸리티
### EXAMPLES
#### <a name="example-1"></a>예제 1
```
Test-ADSyncToolsSqlNetworkPort -Server 'sqlserver01'
```
#### <a name="example-2"></a>예제 2
```
Test-ADSyncToolsSqlNetworkPort -Server 'sqlserver01' -Port 1433
```
### PARAMETERS
#### <a name="-server"></a>-Server
SQL Server 이름
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: False
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-port"></a>-Port
SQL Server 포트
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: False
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
## <a name="trace-adsynctoolsadimport"></a>Trace-ADSyncToolsADImport
### SYNOPSIS
Active Directory 가져오기 단계에서 추적 파일 만들기
### SYNTAX
#### <a name="adconnectorxml"></a>ADConnectorXML
```
Trace-ADSyncToolsADImport [-DC] <String> [-RootDN] <String> [[-Filter] <String>] [[-Credential] <PSCredential>]
 [-SSL] [-ADConnectorXML] <String> [<CommonParameters>]
```
#### <a name="adwatermarkinput"></a>ADwatermarkInput
```
Trace-ADSyncToolsADImport [-DC] <String> [-RootDN] <String> [[-Filter] <String>] [[-Credential] <PSCredential>]
 [-SSL] [-ADwatermark] <String> [<CommonParameters>]
```
### DESCRIPTION
지정된 Active Directory 워터마크 검사점(파티션 쿠키라고도 함)에서 실행된 Active Directory 가져오기의 모든 LDAP 쿼리를 추적합니다. 현재 폴더에 '. \ ADimportTrace_yyyyMMddHHmmss.log' 추적 파일을 만듭니다.
-ADConnectorXML을 사용하려면 Synchronization Service Manager로 이동하여 AD 커넥터를 마우스 오른쪽 단추로 클릭한 다음, “커넥터 내보내기...”를 선택합니다.
### EXAMPLES
#### <a name="example-1"></a>예제 1
```
Trace Active Directory Import for user objects by providing an AD Connector XML file
```
Trace-ADSyncToolsADImport -DC ‘DC1.contoso.com’ -RootDN ‘DC=Contoso,DC=com’ -Filter ‘(&amp;(objectClass=user))’ -ADConnectorXML .\ADConnector.xml
#### <a name="example-2"></a>예제 2
```
Trace Active Directory Import for all objects by providing the Active Directory watermark (cookie) and AD Connector credential
```
$creds = Get-Credential Trace-ADSyncToolsADImport -DC ‘DC1.contoso.com’ -RootDN ‘DC=Contoso,DC=com’ -Credential $creds -ADwatermark “TVNEUwMAAAAXyK9ir1zSAQAAAAAAAAAA(...)”
### PARAMETERS
#### <a name="-dc"></a>-DC
대상 도메인 컨트롤러입니다.
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-rootdn"></a>-RootDN
포리스트 루트 DN입니다.
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 2
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-filter"></a>-Filter
추적할 AD 개체 형식.
모든 개체 형식을 지정하려면 ‘(&(objectClass=*))’를 사용합니다.
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: False
Position: 3
Default value: (&(objectClass=*))
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-credential"></a>-Credential
AD에 대해 LDAP 쿼리를 실행할 자격 증명을 제공합니다.
```yaml
Type: PSCredential
Parameter Sets: (All)
Aliases:
Required: False
Position: 4
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-ssl"></a>-SSL
SSL 연결
```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases:
Required: False
Position: 5
Default value: False
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-adconnectorxml"></a>-ADConnectorXML
AD 커넥터 내보내기 XML 파일 - AD 커넥터를 마우스 오른쪽 단추로 클릭하고 “커넥터 내보내기...”를 선택합니다.
```yaml
Type: String
Parameter Sets: ADConnectorXML
Aliases:
Required: True
Position: 6
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-adwatermark"></a>-ADwatermark
XML 파일 대신 워터마크를 수동으로 입력합니다(예: $ADwatermark = “TVNEUwMAAAAXyK9ir1zSAQAAAAAAAAAA(...)”).
```yaml
Type: String
Parameter Sets: ADwatermarkInput
Aliases:
Required: True
Position: 6
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable. 자세한 내용은 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)를 참조하세요.
## <a name="trace-adsynctoolsldapquery"></a>Trace-ADSyncToolsLdapQuery
### SYNOPSIS
LDAP 쿼리 추적
### SYNTAX
```
Trace-ADSyncToolsLdapQuery [-RootDN] <String> [-Credential] <PSCredential> [[-Server] <String>]
 [[-Port] <Int32>] [-Filter <String>] [<CommonParameters>]
```
### DESCRIPTION
{{ 설명 입력 }}
### EXAMPLES
#### <a name="example-1"></a>예제 1
```
Trace-ADSyncToolsLdapQuery -RootDN "DC=Contoso,DC=com" -Credential $Credential
```
### PARAMETERS
#### <a name="-rootdn"></a>-RootDN
포리스트/도메인 DistinguishedName
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 1
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```
#### <a name="-credential"></a>-Credential
AD 자격 증명
```yaml
Type: PSCredential
Parameter Sets: (All)
Aliases:
Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```
#### <a name="-server"></a>-Server
도메인 컨트롤러 이름(선택 사항)
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: False
Position: 3
Default value: None
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```
#### <a name="-port"></a>-Port
도메인 컨트롤러 포트(기본값: 389)
```yaml
Type: Int32
Parameter Sets: (All)
Aliases:
Required: False
Position: 3
Default value: 389
Accept pipeline input: True (ByPropertyName)
Accept wildcard characters: False
```
#### <a name="-filter"></a>-Filter
LDAP 필터(기본값: objectClass=*)
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: False
Position: Named
Default value: (objectClass=*)
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable. 자세한 내용은 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)를 참조하세요.
## <a name="update-adsynctoolssourceanchor"></a>Update-ADSyncToolsSourceAnchor
### SYNOPSIS
사용자를 새 ConsistencyGuid(ImmutableId)로 업데이트합니다.
### SYNTAX
```
Update-ADSyncToolsSourceAnchor [[-DistinguishedName] <String>] [-ImmutableIdGUID] <String> [-Action] <String>
 [-Output] <String> [-WhatIf] [-Confirm] [<CommonParameters>]
```
### DESCRIPTION
사용자를 ConsistencyGuid 보고서에서 가져온 새 ConsistencyGuid(ImmutableId) 값으로 업데이트합니다. 이 함수는 WhatIf 스위치를 지원합니다. 참고: ConsistencyGuid 보고서는 탭 구분 기호를 사용하여 가져와야 합니다.
### EXAMPLES
#### <a name="example-1"></a>예제 1
```
Import-Csv .\AllSyncUsersTEST-Report.csv -Delimiter "`t"| Update-ADSyncToolsSourceAnchor -Output .\AllSyncUsersTEST-Result2 -WhatIf
```
#### <a name="example-2"></a>예제 2
```
Import-Csv .\AllSyncUsersTEST-Report.csv -Delimiter "`t"| Update-ADSyncToolsSourceAnchor -Output .\AllSyncUsersTEST-Result2
```
### PARAMETERS
#### <a name="-distinguishedname"></a>-DistinguishedName
DistinguishedName
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: False
Position: 1
Default value: False
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="-immutableidguid"></a>-ImmutableIdGUID
ImmutableIdGUID입니다.
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 2
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="-action"></a>-Action
작업
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 3
Default value: None
Accept pipeline input: True (ByPropertyName, ByValue)
Accept wildcard characters: False
```
#### <a name="-output"></a>-Output
LOG 파일에 대한 출력 파일 이름입니다.
```yaml
Type: String
Parameter Sets: (All)
Aliases:
Required: True
Position: 4
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-whatif"></a>-WhatIf
cmdlet을 실행할 경우 발생하는 일을 표시합니다.
cmdlet은 실행되지 않습니다.
```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: wi
Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="-confirm"></a>-Confirm
cmdlet을 실행하기 전에 확인을 요청합니다.
```yaml
Type: SwitchParameter
Parameter Sets: (All)
Aliases: cf
Required: False
Position: Named
Default value: None
Accept pipeline input: False
Accept wildcard characters: False
```
#### <a name="commonparameters"></a>CommonParameters
이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable. 자세한 내용은 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)를 참조하세요.