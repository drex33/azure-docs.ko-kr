---
title: 'Azure AD Connect: ADSync PowerShell 참조 | Microsoft Docs'
description: 이 문서에서는 ADSync.psm1 PowerShell 모듈에 대한 참조 정보를 제공합니다.
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
ms.openlocfilehash: 19d3b2dc72e31cef290bd5253e7b173d624488af
ms.sourcegitcommit: 92dd25772f209d7d3f34582ccb8985e1a099fe62
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/15/2021
ms.locfileid: "114228742"
---
# <a name="azure-ad-connect--adsync-powershell-reference"></a>Azure AD Connect: ADSync PowerShell 참조
이 설명서에서는 Azure AD Connect에 포함된 ADSync.psm1 PowerShell 모듈에 대해 다음과 같은 참조 정보를 제공합니다.


## <a name="add-adsyncaddsconnectoraccount"></a>Add-ADSyncADDSConnectorAccount

 ### <a name="synopsis"></a>개요
 이 cmdlet은 서비스 계정의 암호를 재설정하고 Azure AD와 동기화 엔진에서 암호를 업데이트합니다.

 ### <a name="syntax"></a>SYNTAX
  #### <a name="byidentifier"></a>byIdentifier
   ```
     Add-ADSyncADDSConnectorAccount [-Identifier] <Guid> [-EACredential <PSCredential>] [<CommonParameters>]
   ```

 #### <a name="byname"></a>byName
   ```
     Add-ADSyncADDSConnectorAccount [-Name] <String> [-EACredential <PSCredential>] [<CommonParameters>]
   ```

 ### <a name="description"></a>설명
 이 cmdlet은 서비스 계정의 암호를 재설정하고 Azure AD와 동기화 엔진에서 암호를 업데이트합니다.

 ### <a name="examples"></a>예제

 #### <a name="example-1"></a>예 1
   ```powershell
     PS C:\> Add-ADSyncADDSConnectorAccount -Name contoso.com -EACredential $EAcredentials
   ```

 contoso.com에 연결된 서비스 계정의 암호를 재설정합니다.

 ### <a name="parameters"></a>PARAMETERS

   #### <a name="-eacredential"></a>-EACredential
   Active Directory의 엔터프라이즈 관리자 계정에 대한 자격 증명입니다.

   ```yaml
     Type: PSCredential
     Parameter Sets: (All)
     Aliases:

     Required: False
     Position: Named
     Default value: None
     Accept pipeline input: False
     Accept wildcard characters: False
   ```

  #### <a name="-identifier"></a>-Identifier
  서비스 계정의 암호를 재설정해야 하는 커넥터의 식별자입니다.

   ```yaml
     Type: Guid
     Parameter Sets: byIdentifier
     Aliases:

     Required: True
     Position: 0
     Default value: None
     Accept pipeline input: True (ByValue)
     Accept wildcard characters: False
   ```

  #### <a name="-name"></a>-Name
  연결선의 이름입니다.

   ```yaml
     Type: String
     Parameter Sets: byName
     Aliases:
 
     Required: True
     Position: 1
     Default value: None
     Accept pipeline input: True (ByValue)
     Accept wildcard characters: False
   ```

  #### <a name="commonparameters"></a>CommonParameters
  이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable. 자세한 내용은 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)를 참조하세요.

 ### <a name="inputs"></a>입력

  #### <a name="systemguid"></a>System.Guid

  #### <a name="systemstring"></a>System.String

 ### <a name="outputs"></a>출력

  #### <a name="systemobject"></a>System.Object



## <a name="disable-adsyncexportdeletionthreshold"></a>Disable-ADSyncExportDeletionThreshold

 ### <a name="synopsis"></a>개요
 내보내기 단계에서 삭제 임계값에 대한 기능을 사용하지 않도록 설정합니다.

 ### <a name="syntax"></a>SYNTAX
   
   ```
     Disable-ADSyncExportDeletionThreshold [[-AADCredential] <PSCredential>] [-WhatIf] [-Confirm]
     [<CommonParameters>]
   ```

 ### <a name="description"></a>설명
 내보내기 단계에서 삭제 임계값에 대한 기능을 사용하지 않도록 설정합니다.

 ### <a name="examples"></a>예제

 #### <a name="example-1"></a>예 1
   ```powershell
     PS C:\> Disable-ADSyncExportDeletionThreshold -AADCredential $aadCreds
   ```

 제공된 AAD 자격 증명을 사용하여 내보내기 삭제 임계값 기능을 사용하지 않도록 설정합니다.

 ### <a name="parameters"></a>PARAMETERS

  #### <a name="-aadcredential"></a>-AADCredential
  AAD 자격 증명입니다.

   ```yaml
     Type: PSCredential
     Parameter Sets: (All)
     Aliases:

     Required: False
     Position: 1
     Default value: None
     Accept pipeline input: True (ByPropertyName)
     Accept wildcard characters: False
  ```

  #### <a name="-confirm"></a>-Confirm
  확인 메시지를 표시하기 위한 매개 변수 스위치입니다.

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

 #### <a name="commonparameters"></a>CommonParameters
 이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable. 자세한 내용은 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)를 참조하세요.

 ### <a name="inputs"></a>입력

  #### <a name="systemmanagementautomationpscredential"></a>System.Management.Automation.PSCredential

 ### <a name="outputs"></a>출력

  #### <a name="systemobject"></a>System.Object

## <a name="enable-adsyncexportdeletionthreshold"></a>Enable-ADSyncExportDeletionThreshold를 실행하여 임계값을 사용하도록 설정합니다.

 ### <a name="synopsis"></a>개요
 내보내기 삭제 임계값 기능을 사용하도록 설정하고 임계값을 설정합니다.

 ### <a name="syntax"></a>SYNTAX

 ```
 Enable-ADSyncExportDeletionThreshold [-DeletionThreshold] <UInt32> [[-AADCredential] <PSCredential>] [-WhatIf]
 [-Confirm] [<CommonParameters>]
 ```

 ### <a name="description"></a>설명
 내보내기 삭제 임계값 기능을 사용하도록 설정하고 임계값을 설정합니다.

 ### <a name="examples"></a>예제

 #### <a name="example-1"></a>예 1
 ```powershell
 PS C:\> Enable-ADSyncExportDeletionThreshold -DeletionThreshold 777 -AADCredential $aadCreds
 ```

 내보내기 삭제 임계값 기능을 사용하도록 설정하고 삭제 임계값을 777로 설정합니다.

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-aadcredential"></a>-AADCredential
 AAD 자격 증명입니다.

 ```yaml
 Type: PSCredential
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: 2
 Default value: None
 Accept pipeline input: True (ByPropertyName)
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

 #### <a name="-deletionthreshold"></a>-DeletionThreshold
 삭제 임계값입니다.

 ```yaml
 Type: UInt32
 Parameter Sets: (All)
 Aliases:

 Required: True
 Position: 1
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

 #### <a name="commonparameters"></a>CommonParameters
 이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable. 자세한 내용은 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)를 참조하세요.

 ### <a name="inputs"></a>입력

 #### <a name="systemuint32"></a>System.UInt32
 
 #### <a name="sytemmanagementautomationpscredential"></a>Sytem.Management.Automation.PSCredential

 ### <a name="outputs"></a>출력

 #### <a name="systemobject"></a>System.Object


## <a name="get-adsyncautoupgrade"></a>Get-ADSyncAutoUpgrade

 ### <a name="synopsis"></a>개요
 설치 시 AutoUpgrade의 상태를 가져옵니다.

 ### <a name="syntax"></a>SYNTAX

 ```
 Get-ADSyncAutoUpgrade [-Detail] [<CommonParameters>]
 ``` 

 ### <a name="description"></a>설명
 설치 시 AutoUpgrade의 상태를 가져옵니다.

 ### <a name="examples"></a>예제

 #### <a name="example-1"></a>예 1
 ```powershell
 PS C:\> Get-ADSyncAutoUpgrade -Detail
 ```

 설치의 AutoUpgrade 상태를 반환하고 AutoUpgrade가 일시 중단된 경우 일시 중단 이유를 표시합니다.

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-detail"></a>-Detail
 AutoUpgrade 상태가 일시 중단된 경우 이 매개 변수를 사용하면 일시 중단 이유가 표시됩니다.

 ```yaml
 Type: SwitchParameter
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: 1
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>CommonParameters
 이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable. 자세한 내용은 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)를 참조하세요.

 ### <a name="inputs"></a>입력

 #### <a name="none"></a>None

 ### <a name="outputs"></a>출력

 #### <a name="systemobject"></a>System.Object

## <a name="get-adsynccsobject"></a>Get-ADSyncCSObject

 ### <a name="synopsis"></a>개요
 지정된 커넥터 공간 개체를 가져옵니다.

 ### <a name="syntax"></a>SYNTAX
 
 #### <a name="searchbyidentifier"></a>SearchByIdentifier
 ```
 Get-ADSyncCSObject [-Identifier] <Guid> [<CommonParameters>]
 ```
 
 #### <a name="searchbyconnectoridentifierdistinguishedname"></a>SearchByConnectorIdentifierDistinguishedName
 ```
 Get-ADSyncCSObject [-ConnectorIdentifier] <Guid> [-DistinguishedName] <String> [-SkipDNValidation] [-Transient]
 [<CommonParameters>]
 ```

 #### <a name="searchbyconnectoridentifier"></a>SearchByConnectorIdentifier
 ```
 Get-ADSyncCSObject [-ConnectorIdentifier] <Guid> [-Transient] [-StartIndex <Int32>] [-MaxResultCount <Int32>]
 [<CommonParameters>]
 ```

 #### <a name="searchbyconnectornamedistinguishedname"></a>SearchByConnectorNameDistinguishedName
 ```
 Get-ADSyncCSObject [-ConnectorName] <String> [-DistinguishedName] <String> [-SkipDNValidation] [-Transient]
 [<CommonParameters>]
 ```

 #### <a name="searchbyconnectorname"></a>SearchByConnectorName
 ```
 Get-ADSyncCSObject [-ConnectorName] <String> [-Transient] [-StartIndex <Int32>] [-MaxResultCount <Int32>]
 [<CommonParameters>]
 ```

 ### <a name="description"></a>설명
 지정된 커넥터 공간 개체를 가져옵니다.

 ### <a name="examples"></a>예제

 #### <a name="example-1"></a>예 1
 ```powershell
 PS C:\> Get-ADSyncCSObject -ConnectorName "contoso.com" -DistinguishedName "CN=fabrikam,CN=Users,DC=contoso,DC=com"
 ```

 contoso.com 도메인의 사용자 fabrikam에 대한 CS 개체를 가져옵니다.

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-connectoridentifier"></a>-ConnectorIdentifier
 커넥터의 식별자입니다.

 ```yaml
 Type: Guid
 Parameter Sets: SearchByConnectorIdentifierDistinguishedName, SearchByConnectorIdentifier 
 Aliases:

 Required: True
 Position: 0
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-connectorname"></a>-ConnectorName
 커넥터의 이름입니다.

 ```yaml
 Type: String
 Parameter Sets: SearchByConnectorNameDistinguishedName, SearchByConnectorName
 Aliases:

 Required: True
 Position: 0
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-distinguishedname"></a>-DistinguishedName
 커넥터 공간 개체의 고유 이름입니다.

 ```yaml
 Type: String
 Parameter Sets: SearchByConnectorIdentifierDistinguishedName, SearchByConnectorNameDistinguishedName
 Aliases:

 Required: True
 Position: 1
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-identifier"></a>-Identifier
 커넥터 공간 개체의 식별자입니다.

 ```yaml
 Type: Guid
 Parameter Sets: SearchByIdentifier
 Aliases:

 Required: True
 Position: 0
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-maxresultcount"></a>-MaxResultCount
 결과 집합의 최대 개수입니다.

 ```yaml
 Type: Int32
 Parameter Sets: SearchByConnectorIdentifier, SearchByConnectorName
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-skipdnvalidation"></a>-SkipDNValidation
 DN 유효성 검사를 건너뛰는 매개 변수 스위치입니다.

 ```yaml
 Type: SwitchParameter
 Parameter Sets: SearchByConnectorIdentifierDistinguishedName, SearchByConnectorNameDistinguishedName
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-startindex"></a>-StartIndex
 개수를 반환할 시작 인덱스입니다.

 ```yaml
 Type: Int32
 Parameter Sets: SearchByConnectorIdentifier, SearchByConnectorName
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-transient"></a>-Transient
 임시 CS 개체를 가져오는 매개 변수 스위치입니다.

 ```yaml
 Type: SwitchParameter
 Parameter Sets: SearchByConnectorIdentifierDistinguishedName, SearchByConnectorIdentifier, SearchByConnectorNameDistinguishedName, SearchByConnectorName
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>CommonParameters
 이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable. 자세한 내용은 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)를 참조하세요.

 ### <a name="inputs"></a>입력

 #### <a name="none"></a>None

 ### <a name="outputs"></a>출력

 #### <a name="systemobject"></a>System.Object

## <a name="get-adsynccsobjectlog"></a>Get-ADSyncCSObjectLog

 ### <a name="synopsis"></a>개요
 커넥터 공간 개체 로그 항목을 가져옵니다.

 ### <a name="syntax"></a>SYNTAX

 ```
 Get-ADSyncCSObjectLog [-Identifier] <Guid> [-Count] <UInt32> [<CommonParameters>]
 ```
 
 ### <a name="description"></a>설명
 커넥터 공간 개체 로그 항목을 가져옵니다.

 ### <a name="examples"></a>예제

 #### <a name="example-1"></a>예 1
 ```powershell
 PS C:\> Get-ADSyncCSObjectLog -Identifier "00000000-0000-0000-0000-000000000000" -Count 1
 ```

 지정된 식별자를 가진 하나의 개체를 반환합니다.

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-count"></a>-Count
 검색할 커넥터 공간 개체 로그 항목의 예상 최대 수입니다.

 ```yaml
 Type: UInt32
 Parameter Sets: (All)
 Aliases:

 Required: True
 Position: 1
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-identifier"></a>-Identifier
 커넥터 공간 개체 식별자입니다.

 ```yaml
 Type: Guid
 Parameter Sets: (All)
 Aliases:

 Required: True
 Position: 0
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>CommonParameters
 이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable. 자세한 내용은 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)를 참조하세요.

 ### <a name="inputs"></a>입력

 #### <a name="none"></a>None

 ### <a name="outputs"></a>출력

 #### <a name="systemobject"></a>System.Object

## <a name="get-adsyncdatabaseconfiguration"></a>Get-ADSyncDatabaseConfiguration

 ### <a name="synopsis"></a>개요
 ADSync 데이터베이스의 구성을 가져옵니다.
 
 ### <a name="syntax"></a>SYNTAX

 ```
 Get-ADSyncDatabaseConfiguration [<CommonParameters>]
 ```
 
 ### <a name="description"></a>설명
 ADSync 데이터베이스의 구성을 가져옵니다.

 ### <a name="examples"></a>예제

 #### <a name="example-1"></a>예 1
 ```powershell
 PS C:\> Get-ADSyncDatabaseConfiguration
 ```

 ADSync 데이터베이스의 구성을 가져옵니다.

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="commonparameters"></a>CommonParameters
 이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable. 자세한 내용은 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)를 참조하세요.

 ### <a name="inputs"></a>입력

 #### <a name="none"></a>None

 ### <a name="outputs"></a>출력

 #### <a name="systemobject"></a>System.Object


## <a name="get-adsyncexportdeletionthreshold"></a>Get-ADSyncExportDeletionThreshold

 ### <a name="synopsis"></a>개요
 AAD에서 내보내기 삭제 임계값을 가져옵니다.

 ### <a name="syntax"></a>SYNTAX

 ```
 Get-ADSyncExportDeletionThreshold [[-AADCredential] <PSCredential>] [-WhatIf] [-Confirm] [<CommonParameters>]
 ```

 ### <a name="description"></a>설명
 AAD에서 내보내기 삭제 임계값을 가져옵니다.

 ### <a name="examples"></a>예제

 #### <a name="example-1"></a>예 1
 ```powershell
 PS C:\> Get-ADSyncExportDeletionThreshold -AADCredential $aadCreds
 ```

 지정된 AAD 자격 증명을 사용하여 AAD에서 내보내기 삭제 임계값을 가져옵니다.

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-aadcredential"></a>-AADCredential
 AAD 자격 증명입니다.

 ```yaml
 Type: PSCredential
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: 1
 Default value: None
 Accept pipeline input: True (ByPropertyName)
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

 #### <a name="commonparameters"></a>CommonParameters
 이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable. 자세한 내용은 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)를 참조하세요.

 ### <a name="inputs"></a>입력

 #### <a name="systemmanagementautomationpscredential"></a>System.Management.Automation.PSCredential

 ### <a name="outputs"></a>출력

 #### <a name="systemobject"></a>System.Object


## <a name="get-adsyncmvobject"></a>Get-ADSyncMVObject

 ### <a name="synopsis"></a>개요
 메타버스 개체를 가져옵니다.

 ### <a name="syntax"></a>SYNTAX

 ```
 Get-ADSyncMVObject -Identifier <Guid> [<CommonParameters>]
 ```

 ### <a name="description"></a>설명
 메타버스 개체를 가져옵니다.

 ### <a name="examples"></a>예제

 #### <a name="example-1"></a>예 1
 ```powershell
 PS C:\> Get-ADSyncMVObject -Identifier "00000000-0000-0000-0000-000000000000"
 ```

 지정된 식별자를 가진 메타버스 개체를 가져옵니다.

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-identifier"></a>-Identifier
 메타버스 개체의 식별자입니다.

 ```yaml
 Type: Guid
 Parameter Sets: (All)
 Aliases:

 Required: True
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>CommonParameters
 이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable. 자세한 내용은 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)를 참조하세요.

 ### <a name="inputs"></a>입력

 #### <a name="none"></a>None

 ### <a name="outputs"></a>출력

 #### <a name="systemobject"></a>System.Object

## <a name="get-adsyncrunprofileresult"></a>Get-ADSyncRunProfileResult

 ### <a name="synopsis"></a>개요
 클라이언트의 입력을 처리하고 실행 프로필 결과를 검색합니다.

 ### <a name="syntax"></a>SYNTAX

 ```
 Get-ADSyncRunProfileResult [-RunHistoryId <Guid>] [-ConnectorId <Guid>] [-RunProfileId <Guid>]
 [-RunNumber <Int32>] [-NumberRequested <Int32>] [-RunStepDetails] [-StepNumber <Int32>] [-WhatIf] [-Confirm]
 [<CommonParameters>]
 ```

 ### <a name="description"></a>설명
 클라이언트의 입력을 처리하고 실행 프로필 결과를 검색합니다.

 ### <a name="examples"></a>예제

 #### <a name="example-1"></a>예 1
 ```powershell
 PS C:\> Get-ADSyncRunProfileResult -ConnectorId "00000000-0000-0000-0000-000000000000"
 ```

 지정된 커넥터에 대한 모든 동기화 실행 프로필 결과를 검색합니다.

 ### <a name="parameters"></a>PARAMETERS

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

 #### <a name="-connectorid"></a>-ConnectorId
 커넥터 식별자입니다.

 ```yaml
 Type: Guid
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-numberrequested"></a>-NumberRequested
 최대 반환 수입니다.

 ```yaml
 Type: Int32
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-runhistoryid"></a>-RunHistoryId
 특정 실행의 식별자입니다.

 ```yaml
 Type: Guid
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-runnumber"></a>-RunNumber
 특정 실행의 실행 번호입니다.

 ```yaml
 Type: Int32
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-runprofileid"></a>-RunProfileId
 특정 실행의 실행 프로필 식별자입니다.

 ```yaml
 Type: Guid
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-runstepdetails"></a>-RunStepDetails
 실행 단계 세부 정보에 대한 매개 변수 스위치입니다.

 ```yaml
 Type: SwitchParameter
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-stepnumber"></a>-StepNumber
 단계 번호를 기준으로 필터링합니다.

 ```yaml
 Type: Int32
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
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

 #### <a name="commonparameters"></a>CommonParameters
 이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable. 자세한 내용은 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)를 참조하세요.

 ### <a name="inputs"></a>입력

 #### <a name="none"></a>None

 ### <a name="outputs"></a>출력

 #### <a name="systemobject"></a>System.Object


## <a name="get-adsyncrunstepresult"></a>Get-ADSyncRunStepResult

 ### <a name="synopsis"></a>개요
 AD Sync 실행 단계 결과를 가져옵니다.

 ### <a name="syntax"></a>SYNTAX

 ```
 Get-ADSyncRunStepResult [-RunHistoryId <Guid>] [-StepHistoryId <Guid>] [-First] [-StepNumber <Int32>] [-WhatIf]
 [-Confirm] [<CommonParameters>]
 ```

 ### <a name="description"></a>설명
 AD Sync 실행 단계 결과를 가져옵니다.

 ### <a name="examples"></a>예제

 #### <a name="example-1"></a>예 1
 ```powershell
 PS C:\> Get-ADSyncRunStepResult -RunHistoryId "00000000-0000-0000-0000-000000000000"
 ```

 지정된 실행의 AD Sync 실행 단계 결과를 가져옵니다.

 ### <a name="parameters"></a>PARAMETERS

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

 #### <a name="-first"></a>-First
 첫 번째 개체만 가져오는 매개 변수 스위치입니다.

 ```yaml
 Type: SwitchParameter
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-runhistoryid"></a>-RunHistoryId
 특정 실행의 ID입니다.

 ```yaml
 Type: Guid
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-stephistoryid"></a>-StepHistoryId
 특정 실행 단계의 ID입니다.

 ```yaml
 Type: Guid
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-stepnumber"></a>-StepNumber
 단계 번호입니다.

 ```yaml
 Type: Int32
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
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

 #### <a name="commonparameters"></a>CommonParameters
 이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable. 자세한 내용은 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)를 참조하세요.

 ### <a name="inputs"></a>입력

 #### <a name="none"></a>None

 ### <a name="outputs"></a>출력

 #### <a name="systemobject"></a>System.Object



## <a name="get-adsyncscheduler"></a>Get-ADSyncScheduler

 ### <a name="synopsis"></a>개요
 동기화 스케줄러에 대한 현재 동기화 주기 설정을 가져옵니다.

 ### <a name="syntax"></a>SYNTAX

 ```
 Get-ADSyncScheduler [<CommonParameters>]
 ```

 ### <a name="description"></a>설명
 동기화 스케줄러에 대한 현재 동기화 주기 설정을 가져옵니다.

 ### <a name="examples"></a>예제

 #### <a name="example-1"></a>예 1
 ```powershell
 PS C:\> Get-ADSyncScheduler
 ```

 동기화 스케줄러에 대한 현재 동기화 주기 설정을 가져옵니다.

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="commonparameters"></a>CommonParameters
 이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable. 자세한 내용은 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)를 참조하세요.

 ### <a name="inputs"></a>입력

 #### <a name="none"></a>None

 ### <a name="outputs"></a>출력

 #### <a name="systemobject"></a>System.Object

## <a name="get-adsyncschedulerconnectoroverride"></a>Get-ADSyncSchedulerConnectorOverride

 ### <a name="synopsis"></a>개요
 지정된 커넥터에 대한 AD Sync 스케줄러 재정의 값을 가져옵니다.

 ### <a name="syntax"></a>SYNTAX

 ```
 Get-ADSyncSchedulerConnectorOverride [-ConnectorIdentifier <Guid>] [-ConnectorName <String>]
 [<CommonParameters>]
 ```

 ### <a name="description"></a>설명
 지정된 커넥터에 대한 AD Sync 스케줄러 재정의 값을 가져옵니다.

 ### <a name="examples"></a>예제

 #### <a name="example-1"></a>예 1 
 ```powershell
 PS C:\> Get-ADSyncSchedulerConnectorOverride -ConnectorName "contoso.com"
 ```

 'contoso.com' 커넥터에 대한 AD Sync 스케줄러 재정의 값을 가져옵니다.

 #### <a name="example-2"></a>예 2
 ```powershell
 PS C:\> Get-ADSyncSchedulerConnectorOverride
 ```

 모든 AD Sync 스케줄러 재정의 값을 가져옵니다.

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-connectoridentifier"></a>-ConnectorIdentifier
 커넥터 식별자입니다.

 ```yaml
 Type: Guid
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-connectorname"></a>-ConnectorName
 커넥터 이름입니다.

 ```yaml
 Type: String
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>CommonParameters
 이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable. 자세한 내용은 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)를 참조하세요.

 ### <a name="inputs"></a>입력

 #### <a name="none"></a>None

 ### <a name="outputs"></a>출력

 #### <a name="systemobject"></a>System.Object


## <a name="invoke-adsynccsobjectpasswordhashsync"></a>Invoke-ADSyncCSObjectPasswordHashSync

 ### <a name="synopsis"></a>개요
 지정된 AD 커넥터 공간 개체에 대한 암호 해시를 동기화합니다.

 ### <a name="syntax"></a>SYNTAX

 #### <a name="searchbydistinguishedname"></a>SearchByDistinguishedName
 ```
 Invoke-ADSyncCSObjectPasswordHashSync [-ConnectorName] <String> [-DistinguishedName] <String>
 [<CommonParameters>]
 ```

 #### <a name="searchbyidentifier"></a>SearchByIdentifier
 ```
 Invoke-ADSyncCSObjectPasswordHashSync [-Identifier] <Guid> [<CommonParameters>]
 ```

 #### <a name="csobject"></a>CSObject
 ```
 Invoke-ADSyncCSObjectPasswordHashSync [-CsObject] <CsObject> [<CommonParameters>]
 ```

 ### <a name="description"></a>설명
 지정된 AD 커넥터 공간 개체에 대한 암호 해시를 동기화합니다.

 ### <a name="examples"></a>예제

 #### <a name="example-1"></a>예 1
 ```powershell
 PS C:\> Invoke-ADSyncCSObjectPasswordHashSync -ConnectorName "contoso.com" -DistinguishedName "CN=fabrikam,CN=Users,DN=contoso,DN=com"
 ```

 지정된 개체에 대한 암호 해시를 동기화합니다.

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-connectorname"></a>-ConnectorName
 커넥터의 이름입니다.

 ```yaml
 Type: String
 Parameter Sets: SearchByDistinguishedName
 Aliases:

 Required: True
 Position: 0
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-csobject"></a>-CsObject
 커넥터 공간 개체입니다.

 ```yaml
 Type: CsObject
 Parameter Sets: CSObject
 Aliases:

 Required: True
 Position: 0
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-distinguishedname"></a>-DistinguishedName
 커넥터 공간 개체의 고유 이름입니다.

 ```yaml
 Type: String
 Parameter Sets: SearchByDistinguishedName
 Aliases:

 Required: True
 Position: 1
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-identifier"></a>-Identifier
 커넥터 공간 개체의 식별자입니다.

 ```yaml
 Type: Guid
 Parameter Sets: SearchByIdentifier
 Aliases:

 Required: True
 Position: 0
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>CommonParameters
 이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable. 자세한 내용은 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)를 참조하세요.

 ### <a name="inputs"></a>입력

 #### <a name="none"></a>None

 ### <a name="outputs"></a>출력

 #### <a name="systemobject"></a>System.Object


## <a name="invoke-adsyncrunprofile"></a>Invoke-ADSyncRunProfile

 ### <a name="synopsis"></a>개요
 특정 실행 프로필을 호출합니다.

 ### <a name="syntax"></a>SYNTAX

 #### <a name="connectorname"></a>ConnectorName
 ```
 Invoke-ADSyncRunProfile -ConnectorName <String> -RunProfileName <String> [-Resume] [<CommonParameters>]
 ```

 #### <a name="connectoridentifier"></a>ConnectorIdentifier
 ```
 Invoke-ADSyncRunProfile -ConnectorIdentifier <Guid> -RunProfileName <String> [-Resume] [<CommonParameters>]
 ```

 ### <a name="description"></a>설명
 특정 실행 프로필을 호출합니다.

 ### <a name="examples"></a>예제

 #### <a name="example-1"></a>예 1
 ```powershell
 PS C:\> Invoke-ADSyncRunProfile -ConnectorName "contoso.com" -RunProfileName Export
 ```

 'contoso.com' 커넥터에서 내보내기를 호출합니다.

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-connectoridentifier"></a>-ConnectorIdentifier
 커넥터의 식별자입니다.

 ```yaml
 Type: Guid
 Parameter Sets: ConnectorIdentifier
 Aliases:

 Required: True
 Position: Named
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-connectorname"></a>-ConnectorName
 커넥터의 이름입니다.

 ```yaml
 Type: String
 Parameter Sets: ConnectorName
 Aliases:

 Required: True
 Position: Named
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-resume"></a>-Resume
 이전에 중단되었거나 반쯤 완료된 RunProfile을 재개하는 매개 변수 스위치입니다.

 ```yaml
 Type: SwitchParameter
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-runprofilename"></a>-RunProfileName
 선택한 커넥터에서 호출할 실행 프로필의 이름입니다.

 ```yaml
 Type: String
 Parameter Sets: (All)
 Aliases:

 Required: True
 Position: Named
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>CommonParameters
 이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable. 자세한 내용은 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)를 참조하세요.

 ### <a name="inputs"></a>입력

 #### <a name="systemstring"></a>System.String

 #### <a name="systemguid"></a>System.Guid

 ### <a name="outputs"></a>출력

 #### <a name="systemobject"></a>System.Object


## <a name="remove-adsyncaadserviceaccount"></a>Remove-ADSyncAADServiceAccount

 ### <a name="synopsis"></a>개요
 AAD 테넌트(지정된 자격 증명과 연결됨)의 기존 AAD 서비스 계정을 삭제합니다.

 ### <a name="syntax"></a>SYNTAX

 #### <a name="serviceaccount"></a>ServiceAccount
 ```
 Remove-ADSyncAADServiceAccount [-AADCredential] <PSCredential> [-Name] <String> [-WhatIf] [-Confirm]
 [<CommonParameters>]
 ```

 #### <a name="serviceprincipal"></a>ServicePrincipal
 ```
 Remove-ADSyncAADServiceAccount [-ServicePrincipal] [-WhatIf] [-Confirm] [<CommonParameters>]
 ```

 ### <a name="description"></a>설명
 AAD 테넌트(지정된 자격 증명과 연결됨)의 기존 AAD 서비스 계정을 삭제합니다.

 ### <a name="examples"></a>예제

 #### <a name="example-1"></a>예 1
 ```powershell
 PS C:\> Remove-ADSyncAADServiceAccount -AADCredential $aadcreds -Name contoso.com
 ```

 contoso.com의 모든 기존 AAD 서비스 계정을 삭제합니다.

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-aadcredential"></a>-AADCredential
 AAD 자격 증명입니다.

 ```yaml
 Type: PSCredential
 Parameter Sets: ServiceAccount
 Aliases:

 Required: True
 Position: 1
 Default value: None
 Accept pipeline input: True (ByPropertyName)
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

 #### <a name="-name"></a>-Name
 계정의 이름입니다.

 ```yaml
 Type: String
 Parameter Sets: ServiceAccount
 Aliases:

 Required: True
 Position: 2
 Default value: None
 Accept pipeline input: True (ByPropertyName)
 Accept wildcard characters: False
 ```

 #### <a name="-serviceprincipal"></a>-ServicePrincipal
 계정의 서비스 주체입니다.

 ```yaml
 Type: SwitchParameter
 Parameter Sets: ServicePrincipal
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

 #### <a name="commonparameters"></a>CommonParameters
 이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable. 자세한 내용은 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)를 참조하세요.

 ### <a name="inputs"></a>입력

 #### <a name="systemmanagementautomationpscredential"></a>System.Management.Automation.PSCredential

 #### <a name="systemstring"></a>System.String

 #### <a name="systemmanagementautomationswitchparameter"></a>System.Management.Automation.SwitchParameter

 ### <a name="outputs"></a>출력

 #### <a name="systemobject"></a>System.Object


## <a name="set-adsyncautoupgrade"></a>Set-ADSyncAutoUpgrade

 ### <a name="synopsis"></a>개요
 설치의 AutoUpgrade 상태를 사용 및 사용 안 함으로 변경합니다.

 ### <a name="syntax"></a>SYNTAX

 ```
 Set-ADSyncAutoUpgrade [-AutoUpgradeState] <AutoUpgradeConfigurationState> [[-SuspensionReason] <String>]
 [<CommonParameters>]
 ```

 ### <a name="description"></a>설명
 설치 시 AutoUpgrade 상태를 설정합니다. 이 cmdlet은 AutoUpgrade 상태를 사용 및 사용 안 함으로 변경하는 데만 사용해야 합니다. 시스템에서만 일시 중단 상태를 설정합니다.

 ### <a name="examples"></a>예제

 #### <a name="example-1"></a>예 1
 ```powershell
 PS C:\> Set-ADSyncAutoUpgrade -AutoUpgradeState Enabled
 ```

 AutoUpgrade 상태를 사용으로 설정합니다.

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-autoupgradestate"></a>-AutoUpgradeState
 AtuoUpgrade 상태입니다. 허용되는 값은 일시 중단됨, 사용, 사용 안 함입니다.

 ```yaml
 Type: AutoUpgradeConfigurationState
 Parameter Sets: (All)
 Aliases:
 Accepted values: Suspended, Enabled, Disabled

 Required: True
 Position: 0
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-suspensionreason"></a>-SuspensionReason
 일시 중단 이유입니다. 시스템에서만 AutoUpgrade 상태를 일시 중단으로 설정해야 합니다.

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

 #### <a name="commonparameters"></a>CommonParameters
 이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable. 자세한 내용은 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)를 참조하세요.

 ### <a name="inputs"></a>입력

 #### <a name="none"></a>None

 ### <a name="outputs"></a>출력

 #### <a name="systemobject"></a>System.Object


## <a name="set-adsyncscheduler"></a>Set-ADSyncScheduler

 ### <a name="synopsis"></a>개요
 동기화 스케줄러에 대한 현재 동기화 주기 설정을 지정합니다.

 ### <a name="syntax"></a>SYNTAX

 ```
 Set-ADSyncScheduler [[-CustomizedSyncCycleInterval] <TimeSpan>] [[-SyncCycleEnabled] <Boolean>]
 [[-NextSyncCyclePolicyType] <SynchronizationPolicyType>] [[-PurgeRunHistoryInterval] <TimeSpan>]
 [[-MaintenanceEnabled] <Boolean>] [[-SchedulerSuspended] <Boolean>] [-Force] [<CommonParameters>]
 ```

 ### <a name="description"></a>설명
 동기화 스케줄러에 대한 현재 동기화 주기 설정을 지정합니다.

 ### <a name="examples"></a>예제

 #### <a name="example-1"></a>예 1
 ```powershell
 PS C:\> Set-ADSyncScheduler -SyncCycleEnabled $true
 ```

 SyncCycleEnabled에 대한 현재 동기화 주기 설정을 True로 설정합니다.
 
 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-customizedsynccycleinterval"></a>-CustomizedSyncCycleInterval
 설정하려는 사용자 지정 동기화 간격의 시간 범위 값을 지정하세요.
가장 낮은 허용 설정에서 실행하려면 이 매개 변수를 Null로 설정하세요.

 ```yaml
 Type: TimeSpan
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: 0
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-force"></a>-Force
 값을 강제로 설정하기 위한 매개 변수 스위치입니다.

 ```yaml
 Type: SwitchParameter
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: 6
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-maintenanceenabled"></a>-MaintenanceEnabled
 MaintenanceEnabled 설정을 위한 매개 변수입니다.

 ```yaml
 Type: Boolean
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: 4
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-nextsynccyclepolicytype"></a>-NextSyncCyclePolicyType
 NextSyncCyclePolicyType 설정을 위한 매개 변수입니다. 허용되는 값은 지정되지 않음, 델타, 초기입니다.

 ```yaml
 Type: SynchronizationPolicyType
 Parameter Sets: (All)
 Aliases:
 Accepted values: Unspecified, Delta, Initial

 Required: False
 Position: 2
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-purgerunhistoryinterval"></a>-PurgeRunHistoryInterval
 PurgeRunHistoryInterval 설정을 위한 매개 변수입니다.

 ```yaml
 Type: TimeSpan
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: 3
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-schedulersuspended"></a>-SchedulerSuspended
 SchedulerSuspended 설정을 위한 매개 변수입니다.

 ```yaml
 Type: Boolean
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: 5
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-synccycleenabled"></a>-SyncCycleEnabled
 SyncCycleEnabled 설정을 위한 매개 변수입니다.

 ```yaml
 Type: Boolean
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: 1
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>CommonParameters
 이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable. 자세한 내용은 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)를 참조하세요.

 ### <a name="inputs"></a>입력

 #### <a name="systemnullable1systemtimespan-mscorlib-version4000-cultureneutral-publickeytokenb77a5c561934e089"></a>System.Nullable`1[[System.TimeSpan, mscorlib, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089]]

 #### <a name="systemnullable1systemboolean-mscorlib-version4000-cultureneutral-publickeytokenb77a5c561934e089"></a>System.Nullable`1[[System.Boolean, mscorlib, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089]]

 #### <a name="systemnullable1microsoftidentitymanagementpowershellobjectmodelsynchronizationpolicytype-microsoftidentitymanagementpowershellobjectmodel-version1400-cultureneutral-publickeytoken31bf3856ad364e35"></a>System.Nullable`1[[Microsoft.IdentityManagement.PowerShell.ObjectModel.SynchronizationPolicyType, Microsoft.IdentityManagement.PowerShell.ObjectModel, Version=1.4.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35]]

 #### <a name="systemmanagementautomationswitchparameter"></a>System.Management.Automation.SwitchParameter

 ### <a name="outputs"></a>출력

 #### <a name="systemobject"></a>System.Object


## <a name="set-adsyncschedulerconnectoroverride"></a>Set-ADSyncSchedulerConnectorOverride

 ### <a name="synopsis"></a>개요
 동기화 스케줄러에 대한 현재 동기화 주기 설정을 지정합니다.

 ### <a name="syntax"></a>SYNTAX

 #### <a name="connectoridentifier"></a>ConnectorIdentifier
 ```
 Set-ADSyncSchedulerConnectorOverride -ConnectorIdentifier <Guid> [-FullImportRequired <Boolean>]
 [-FullSyncRequired <Boolean>] [<CommonParameters>]
 ```

 #### <a name="connectorname"></a>ConnectorName
 ```
 Set-ADSyncSchedulerConnectorOverride -ConnectorName <String> [-FullImportRequired <Boolean>]
 [-FullSyncRequired <Boolean>] [<CommonParameters>]
 ```

 ### <a name="description"></a>설명
 동기화 스케줄러에 대한 현재 동기화 주기 설정을 지정합니다.

 ### <a name="examples"></a>예제

 #### <a name="example-1"></a>예 1
 ```powershell
 PS C:\> Set-ADSyncSchedulerConnectorOverride -Connectorname "contoso.com" -FullImportRequired $true
 -FullSyncRequired $false
 ```

 전체 가져오기가 필요하고 전체 동기화가 필요하지 않도록 'contoso.com' 커넥터에 대한 동기화 주기 설정을 지정합니다.

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-connectoridentifier"></a>-ConnectorIdentifier
 커넥터 식별자입니다.

 ```yaml
 Type: Guid
 Parameter Sets: ConnectorIdentifier
 Aliases:

 Required: True
 Position: Named
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-connectorname"></a>-ConnectorName
 커넥터 이름입니다.

 ```yaml
 Type: String
 Parameter Sets: ConnectorName
 Aliases:

 Required: True
 Position: Named
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-fullimportrequired"></a>-FullImportRequired
 다음 주기에서 전체 가져오기가 필요하도록 하려면 true로 설정합니다.

 ```yaml
 Type: Boolean
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-fullsyncrequired"></a>-FullSyncRequired
 다음 주기에서 전체 동기화가 필요하도록 하려면 true로 설정합니다.

 ```yaml
 Type: Boolean
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>CommonParameters
 이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable. 자세한 내용은 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)를 참조하세요.

 ### <a name="inputs"></a>입력

 #### <a name="systemguid"></a>System.Guid

 #### <a name="systemstring"></a>System.String

 #### <a name="systemnullable1systemboolean-mscorlib-version4000-cultureneutral-publickeytokenb77a5c561934e089"></a>System.Nullable`1[[System.Boolean, mscorlib, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089]]

 ### <a name="outputs"></a>출력

 #### <a name="systemobject"></a>System.Object


## <a name="start-adsyncpurgerunhistory"></a>Start-ADSyncPurgeRunHistory

 ### <a name="synopsis"></a>개요
 지정된 시간 범위보다 오래된 실행 기록을 제거하는 cmdlet입니다.

 ### <a name="syntax"></a>SYNTAX

 #### <a name="online"></a>온라인
 ```
 Start-ADSyncPurgeRunHistory [[-PurgeRunHistoryInterval]  <TimeSpan>] [<CommonParameters>]
 ```

 #### <a name="offline"></a>오프라인
 ```
 Start-ADSyncPurgeRunHistory [-Offline] [<CommonParameters>]
 ```

 ### <a name="description"></a>설명
 지정된 시간 범위보다 오래된 실행 기록을 제거하는 cmdlet입니다.

 ### <a name="examples"></a>예제

 #### <a name="example-1"></a>예 1
 ```powershell
 PS C:\> Start-ADSyncPurgeRunHistory -PurgeRunHistoryInterval (New-Timespan -Hours 5)
 ```

 5시간이 지난 모든 실행 기록을 제거합니다.

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-offline"></a>-Offline
 서비스가 오프라인인 동안 데이터베이스에서 모든 실행 기록을 제거합니다.

 ```yaml
 Type: SwitchParameter
 Parameter Sets: offline
 Aliases:

 Required: True
 Position: 0
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-purgerunhistoryinterval"></a>-PurgeRunHistoryInterval
 보존할 기록의 간격입니다.

 ```yaml
 Type: TimeSpan
 Parameter Sets: online
 Aliases:

 Required: False
 Position: 0
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>CommonParameters
 이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable. 자세한 내용은 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)를 참조하세요.

 ### <a name="inputs"></a>입력

 #### <a name="systemtimespan"></a>System.TimeSpan

 ### <a name="outputs"></a>출력

 #### <a name="systemobject"></a>System.Object

## <a name="start-adsyncsynccycle"></a>Start-ADSyncSyncCycle

 ### <a name="synopsis"></a>개요
 동기화 주기를 트리거합니다.

 ### <a name="syntax"></a>SYNTAX

 ```
 Start-ADSyncSyncCycle [[-PolicyType] <SynchronizationPolicyType>] [[-InteractiveMode] <Boolean>]
 [<CommonParameters>]
 ```

 ### <a name="description"></a>설명
 동기화 주기를 트리거합니다.

 ### <a name="examples"></a>예제

 #### <a name="example-1"></a>예 1
 ```powershell
 PS C:\> Start-ADSyncSyncCycle -PolicyType Initial
 ```

 초기 정책 유형으로 동기화 주기를 트리거합니다.

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-interactivemode"></a>-InteractiveMode
 대화형(명령줄) 모드와 스크립트/코드 모드(다른 코드에서 호출)를 구분합니다.

 ```yaml
 Type: Boolean
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: 2
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-policytype"></a>-PolicyType
 실행할 정책 유형입니다. 허용되는 값은 지정되지 않음, 델타, 초기입니다.

 ```yaml
 Type: SynchronizationPolicyType
 Parameter Sets: (All)
 Aliases:
 Accepted values: Unspecified, Delta, Initial

 Required: False
 Position: 1
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>CommonParameters
 이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable. 자세한 내용은 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)를 참조하세요.

 ### <a name="inputs"></a>입력

 #### <a name="systemnullable1microsoftidentitymanagementpowershellobjectmodelsynchronizationpolicytype-microsoftidentitymanagementpowershellobjectmodel-version1400-cultureneutral-publickeytoken31bf3856ad364e35"></a>System.Nullable`1[[Microsoft.IdentityManagement.PowerShell.ObjectModel.SynchronizationPolicyType, Microsoft.IdentityManagement.PowerShell.ObjectModel, Version=1.4.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35]]

 #### <a name="systemboolean"></a>System.Boolean

 ### <a name="outputs"></a>출력

 #### <a name="systemobject"></a>System.Object


## <a name="stop-adsyncrunprofile"></a>Stop-ADSyncRunProfile

 ### <a name="synopsis"></a>개요
 모든 또는 지정된 사용 중인 커넥터를 찾아서 중지합니다.

 ### <a name="syntax"></a>SYNTAX

 ```
 Stop-ADSyncRunProfile [[-ConnectorName] <String>] [<CommonParameters>]
 ```

 ### <a name="description"></a>설명
 모든 또는 지정된 사용 중인 커넥터를 찾아서 중지합니다.

 ### <a name="examples"></a>예제

 #### <a name="example-1"></a>예 1
 ```powershell
 PS C:\> Stop-ADSyncRunProfile -ConnectorName "contoso.com"
 ```

 'contoso.com'에서 실행 중인 모든 동기화를 중지합니다.

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-connectorname"></a>-ConnectorName
 커넥터의 이름입니다.
지정하지 않으면 사용 중인 모든 커넥터가 중지됩니다.

 ```yaml
 Type: String
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: 0
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>CommonParameters
 이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable. 자세한 내용은 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)를 참조하세요.

 ### <a name="inputs"></a>입력

 #### <a name="none"></a>None

 ### <a name="outputs"></a>출력

 #### <a name="systemobject"></a>System.Object


## <a name="stop-adsyncsynccycle"></a>Stop-ADSyncSyncCycle

 ### <a name="synopsis"></a>개요
 현재 실행 중인 동기화 주기를 중지하도록 서버에 신호를 보냅니다.

 ### <a name="syntax"></a>SYNTAX

 ```
 Stop-ADSyncSyncCycle [<CommonParameters>]
 ```

 ### <a name="description"></a>설명
 현재 실행 중인 동기화 주기를 중지하도록 서버에 신호를 보냅니다.

 ### <a name="examples"></a>예제

 #### <a name="example-1"></a>예 1
 ```powershell
 PS C:\> Stop-ADSyncSyncCycle
 ```

 현재 실행 중인 동기화 주기를 중지하도록 서버에 신호를 보냅니다.

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="commonparameters"></a>CommonParameters
 이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable. 자세한 내용은 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)를 참조하세요.

 ### <a name="inputs"></a>입력

 #### <a name="none"></a>None

 ### <a name="outputs"></a>출력

 #### <a name="systemobject"></a>System.Object


## <a name="sync-adsynccsobject"></a>Sync-ADSyncCSObject

 ### <a name="synopsis"></a>개요
 커넥터 공간 개체에서 동기화 미리 보기를 실행합니다.

 ### <a name="syntax"></a>SYNTAX

 #### <a name="connectorname_objectdn"></a>ConnectorName_ObjectDN
 ```
 Sync-ADSyncCSObject -ConnectorName <String> -DistinguishedName <String> [-Commit] [<CommonParameters>]
 ```

 #### <a name="connectoridentifier_objectdn"></a>ConnectorIdentifier_ObjectDN
 ```
 Sync-ADSyncCSObject -ConnectorIdentifier <Guid> -DistinguishedName <String> [-Commit] [<CommonParameters>]
 ```

 #### <a name="objectidentifier"></a>ObjectIdentifier
 ```
 Sync-ADSyncCSObject -Identifier <Guid> [-Commit] [<CommonParameters>]
 ```

 ### <a name="description"></a>설명
 커넥터 공간 개체에서 동기화 미리 보기를 실행합니다.

 ### <a name="examples"></a>예제

 #### <a name="example-1"></a>예 1
 ```powershell
 PS C:\> Sync-ADSyncCSObject -ConnectorName "contoso.com" -DistinguishedName "CN=fabrikam,CN=Users,DC=contoso,DC=com"
 ```

 지정된 개체에 대한 동기화 미리 보기를 반환합니다.

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-commit"></a>-Commit
 커밋을 위한 매개 변수 스위치입니다.

 ```yaml
 Type: SwitchParameter
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-connectoridentifier"></a>-ConnectorIdentifier
 커넥터의 식별자입니다.

 ```yaml
 Type: Guid
 Parameter Sets: ConnectorIdentifier_ObjectDN
 Aliases:

 Required: True
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-connectorname"></a>-ConnectorName
 커넥터의 이름입니다.

 ```yaml
 Type: String
 Parameter Sets: ConnectorName_ObjectDN
 Aliases:

 Required: True
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-distinguishedname"></a>-DistinguishedName
 커넥터 공간 개체의 고유 이름입니다.

 ```yaml
 Type: String
 Parameter Sets: ConnectorName_ObjectDN, ConnectorIdentifier_ObjectDN
 Aliases:

 Required: True
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-identifier"></a>-Identifier
 커넥터 공간 개체의 식별자입니다.

 ```yaml
 Type: Guid
 Parameter Sets: ObjectIdentifier
 Aliases:

 Required: True
 Position: Named
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>CommonParameters
 이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable. 자세한 내용은 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)를 참조하세요.

 ### <a name="inputs"></a>입력

 #### <a name="none"></a>None

 ### <a name="outputs"></a>출력

 #### <a name="systemobject"></a>System.Object


## <a name="test-adsyncazureserviceconnectivity"></a>Test-AdSyncAzureServiceConnectivity

 ### <a name="synopsis"></a>개요
 Azure AD에 대한 연결 문제를 조사하고 식별합니다.

 ### <a name="syntax"></a>SYNTAX

 #### <a name="byenvironment"></a>ByEnvironment
 ```
 Test-AdSyncAzureServiceConnectivity [-AzureEnvironment] <Identifier> [[-Service] <AzureService>] [-CurrentUser]
 [<CommonParameters>]
 ```

 #### <a name="bytenantname"></a>ByTenantName
 ```
 Test-AdSyncAzureServiceConnectivity [-Domain] <String> [[-Service] <AzureService>] [-CurrentUser]
 [<CommonParameters>]
 ```

 ### <a name="description"></a>설명
 Azure AD에 대한 연결 문제를 조사하고 식별합니다.

 ### <a name="examples"></a>예제

 #### <a name="example-1"></a>예 1
 ```powershell
 PS C:\> Test-AdSyncAzureServiceConnectivity -AzureEnvironment Worldwide -Service SecurityTokenService -CurrentUser
 ```

 연결 문제가 없으면 "True"를 반환합니다.

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-azureenvironment"></a>-AzureEnvironment
 테스트할 Azure 환경입니다. 허용되는 값: Worldwide, China, UsGov, Germany, AzureUSGovernmentCloud, AzureUSGovernmentCloud2, AzureUSGovernmentCloud3, PreProduction, OneBox, Default.

 ```yaml
 Type: Identifier
 Parameter Sets: ByEnvironment
 Aliases:
 Accepted values: Worldwide, China, UsGov, Germany, AzureUSGovernmentCloud, AzureUSGovernmentCloud2, AzureUSGovernmentCloud3, PreProduction, OneBox, Default

 Required: True
 Position: 0
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-currentuser"></a>-CurrentUser
 cmdlet을 실행하는 사용자입니다.

 ```yaml
 Type: SwitchParameter
 Parameter Sets: (All)
 Aliases:

 Required: False
 Position: 3
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-domain"></a>-Domain
 연결을 테스트 중인 도메인입니다.

 ```yaml
 Type: String
 Parameter Sets: ByTenantName
 Aliases:

 Required: True
 Position: 1
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-service"></a>-Service
 연결을 테스트 중인 서비스입니다.

 ```yaml
 Type: AzureService
 Parameter Sets: (All)
 Aliases:
 Accepted values: SecurityTokenService, AdminWebService

 Required: False
 Position: 2
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="commonparameters"></a>CommonParameters
 이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable. 자세한 내용은 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)를 참조하세요.

 ### <a name="inputs"></a>입력

 #### <a name="microsoftonlinedeploymentclientframeworkmicrosoftonlineinstanceidentifier"></a>Microsoft.Online.Deployment.Client.Framework.MicrosoftOnlineInstance+Identifier

 #### <a name="systemstring"></a>System.String

 #### <a name="systemnullable1microsoftonlinedeploymentclientframeworkazureservice-microsoftonlinedeploymentclientframework-version1600-cultureneutral-publickeytoken31bf3856ad364e35"></a>System.Nullable`1[[Microsoft.Online.Deployment.Client.Framework.AzureService, Microsoft.Online.Deployment.Client.Framework, Version=1.6.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35]]

 #### <a name="systemmanagementautomationswitchparameter"></a>System.Management.Automation.SwitchParameter

 ### <a name="outputs"></a>출력

 #### <a name="systemobject"></a>System.Object


## <a name="test-adsyncuserhaspermissions"></a>Test-AdSyncUserHasPermissions

 ### <a name="synopsis"></a>개요
 ADMA 사용자에게 필요한 권한이 있는지 확인하는 cmdlet입니다.

 ### <a name="syntax"></a>SYNTAX

 ```
 Test-AdSyncUserHasPermissions [-ForestFqdn] <String> [-AdConnectorId] <Guid>
 [-AdConnectorCredential] <PSCredential> [-BaseDn] <String> [-PropertyType] <String> [-PropertyValue] <String>
 [-WhatIf] [-Confirm] [<CommonParameters>]
 ```

 ### <a name="description"></a>설명
 ADMA 사용자에게 필요한 권한이 있는지 확인하는 cmdlet입니다.

 ### <a name="examples"></a>예제

 #### <a name="example-1"></a>예 1
 ```powershell
 PS C:\> Test-AdSyncUserHasPermissions -ForestFqdn "contoso.com" -AdConnectorId "00000000-0000-0000-000000000000"
 -AdConnectorCredential $connectorAcctCreds -BaseDn "CN=fabrikam,CN=Users,DC=contoso,DC=com" -PropertyType "Allowed-Attributes" -PropertyValue "name"
 ```

 ADMA 사용자에게 'fabrikam' 사용자의 'name' 속성에 액세스할 수 있는 권한이 있는지 확인합니다.

 ### <a name="parameters"></a>PARAMETERS

 #### <a name="-adconnectorcredential"></a>-AdConnectorCredential
 AD 커넥터 계정 자격 증명입니다.

 ```yaml
 Type: PSCredential
 Parameter Sets: (All)
 Aliases:

 Required: True
 Position: 2
 Default value: None
 Accept pipeline input: False
 Accept wildcard characters: False
 ```

 #### <a name="-adconnectorid"></a>-AdConnectorId
 AD 커넥터 ID입니다.

 ```yaml
 Type: Guid
 Parameter Sets: (All)
 Aliases:

 Required: True
 Position: 1
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-basedn"></a>-BaseDn
 확인할 개체의 기본 DN입니다.

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

 #### <a name="-forestfqdn"></a>-ForestFqdn
 포리스트의 이름입니다.

 ```yaml
 Type: String
 Parameter Sets: (All)
 Aliases:

 Required: True
 Position: 0
 Default value: None
 Accept pipeline input: True (ByValue)
 Accept wildcard characters: False
 ```

 #### <a name="-propertytype"></a>-PropertyType
 찾고 있는 권한 유형입니다. 허용되는 값: Allowed-Attributes, Allowed-Attributes-Effective, Allowed-Child-Classes, Allowed-Child-Classes-Effective.

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

 #### <a name="-propertyvalue"></a>-PropertyValue
 PropertyType 특성에서 찾고 있는 값입니다.

 ```yaml
 Type: String
 Parameter Sets: (All)
 Aliases:

 Required: True
 Position: 5
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

 #### <a name="commonparameters"></a>CommonParameters
 이 cmdlet 일반 매개 변수를 지원합니다. -Debug, -ErrorAction, -ErrorVariable, -InformationAction, -InformationVariable, -OutVariable, -OutBuffer, -PipelineVariable, -Verbose, -WarningAction 및 -WarningVariable. 자세한 내용은 [about_CommonParameters](/powershell/module/microsoft.powershell.core/about/about_commonparameters)를 참조하세요.

 ### <a name="inputs"></a>입력

 #### <a name="systemstring"></a>System.String

 #### <a name="systemguid"></a>System.Guid

 ### <a name="outputs"></a>출력

 #### <a name="systemobject"></a>System.Object

## <a name="next-steps"></a>다음 단계

- [하이브리드 ID란?](./whatis-hybrid-identity.md)
- [Azure AD Connect 및 Connect Health란?](whatis-azure-ad-connect.md)