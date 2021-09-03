---
title: Azure AD ECMA 커넥터 호스트 일반 SQL 커넥터 구성
description: 이 문서에서는 Azure AD ECMA 커넥터 호스트 일반 SQL 커넥터를 구성하는 방법을 설명합니다.
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: how-to
ms.workload: identity
ms.date: 06/06/2021
ms.author: billmath
ms.reviewer: arvinh
ms.openlocfilehash: a78ae13af84f3453e3a6119f163d90cd37be16bc
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114437369"
---
# <a name="azure-ad-ecma-connector-host-generic-sql-connector-configuration"></a>Azure AD ECMA 커넥터 호스트 일반 SQL 커넥터 구성

>[!IMPORTANT]
> 온-프레미스 프로비저닝 미리 보기는 현재 초대 전용 미리 보기로 제공됩니다. 기능에 대한 액세스를 요청하려면 [액세스 요청 양식](https://aka.ms/onpremprovisioningpublicpreviewaccess)을 사용하세요. 일반 공급을 준비하면서 향후 몇 개월 동안 더 많은 고객 및 커넥터에 대한 미리 보기를 공개할 예정입니다.

이 문서에서는 Azure AD(Azure Active Directory) ECMA 커넥터 호스트를 사용하여 새 SQL 커넥터를 만드는 방법과 SQL 커넥터를 구성하는 방법을 설명합니다. Azure AD ECMA 커넥터 호스트를 성공적으로 설치한 후 이 작업을 수행해야 합니다.

>[!NOTE] 
> 이 문서에서는 일반 SQL 커넥터의 구성에 대해서만 설명합니다. 일반 SQL 커넥터를 설정하는 방법의 단계별 예제는 [자습서: ECMA 커넥터 호스트 일반 SQL 커넥터](tutorial-ecma-sql-connector.md)를 참조하세요.

 이 흐름은 Azure AD ECMA 커넥터 호스트를 설치하고 구성하는 프로세스를 안내합니다.

 ![설치 흐름을 보여 주는 다이어그램](./media/on-premises-sql-connector-configure/flow-1.png)

설치 및 구성에 대한 자세한 내용은 다음을 참조하세요.
   - [Azure AD ECMA 커넥터 호스트의 필수 구성 요소](on-premises-ecma-prerequisites.md)
   - [Azure AD ECMA 커넥터 호스트 설치](on-premises-ecma-install.md)
   - [Azure AD ECMA 커넥터 호스트 및 프로비저닝 에이전트 구성](on-premises-ecma-configure.md)

선택한 옵션에 따라 일부 마법사 화면을 사용하지 못할 수 있으며 정보가 약간 다를 수도 있습니다. 이 구성의 목적에 맞게 사용자 개체 형식이 사용됩니다. 다음 정보를 사용하여 구성 작업을 수행합니다. 

#### <a name="supported-systems"></a>지원되는 시스템
* Microsoft SQL Server 및 Azure SQL
* IBM DB2 10.x
* IBM DB2 9.x
* Oracle 10 및 11g
* Oracle 12c 및 18c
* MySQL 5.x

## <a name="create-a-generic-sql-connector"></a>일반 SQL 커넥터 만들기

일반 SQL 커넥터를 만들려면 다음을 수행합니다.

 1. 바탕 화면에서 ECMA 커넥터 호스트 바로 가기를 선택합니다.
 1. **새 커넥터** 를 선택합니다.
 
     ![새 커넥터 선택 스크린샷](.\media\on-premises-sql-connector-configure\sql-1.png)

 1. **속성** 페이지에서 상자를 채우고 **다음** 을 선택합니다. 개별 상자에 대한 지침은 이미지 뒤에 나오는 표를 사용합니다.
 
     ![속성 입력 스크린샷](.\media\on-premises-sql-connector-configure\sql-2.png)

     |속성|설명|
     |-----|-----|
     |Name|이 커넥터의 이름입니다.|
     |자동 동기화 타이머(분)|허용되는 최솟값은 120분입니다.|
     |비밀 토큰|123456(토큰은 10~20자의 ASCII 문자 및/또는 숫자로 이루어진 문자열이어야 함)|
     |설명|커넥터에 대한 설명입니다.|
     |확장 DLL|일반 SQL 커넥터의 경우 **Microsoft.IAM.Connector.GenericSql.dll** 을 선택합니다.|
 1. **연결** 페이지에서 상자를 채우고 **다음** 을 선택합니다. 개별 상자에 대한 지침은 이미지 뒤에 나오는 표를 사용합니다.
 
     ![연결 입력 스크린샷](.\media\on-premises-sql-connector-configure\sql-3.png)

     |속성|설명|
     |-----|-----|
     |DSN 파일|SQL Server 인스턴스에 연결하는 데 사용되는 데이터 원본 이름 파일입니다.|
     |사용자 이름|SQL Server 인스턴스에 대한 권한이 있는 개인의 사용자 이름입니다. 독립 실행형 서버의 경우 hostname\sqladminaccount 형식, 도메인 구성원 서버의 경우 domain\sqladminaccount 형식이어야 합니다.|
     |암호|제공한 사용자 이름의 암호입니다.|
     |DN이 앵커입니다.|환경에 필요한 설정이 아닌 경우 **DN이 앵커입니다.** 및 **내보내기 형식:개체 바꾸기** 확인란을 선택하지 않습니다.|
     |내보내기 형식:개체 바꾸기||
 1. **스키마 1** 페이지에서 상자를 채우고 **다음** 을 선택합니다. 개별 상자에 대한 지침은 이미지 뒤에 나오는 표를 사용합니다.
 
     ![스키마 1 페이지를 보여 주는 스크린샷](.\media\on-premises-sql-connector-configure\sql-4.png)

     |속성|설명|
     |-----|-----|
     |개체 형식 검색 방법|커넥터에서 프로비저닝할 개체 형식을 검색하는 데 사용되는 방법입니다.|
     |고정 값 목록/테이블/보기/SP|이 상자에는 **사용자** 가 포함되어야 합니다.|
     |테이블/뷰/SP의 열 이름||
     |저장 프로시저 매개 변수||
     |개체 형식을 검색하는 SQL 쿼리 제공||
 1. **스키마 2** 페이지에서 상자를 채우고 **다음** 을 선택합니다. 개별 상자에 대한 지침은 이미지 뒤에 나오는 표를 사용합니다. 이 스키마 화면은 이전 단계에서 선택한 개체 형식에 따라 약간 다르거나 추가 정보를 포함할 수 있습니다.
 
     ![스키마 2 페이지를 보여 주는 스크린샷](.\media\on-premises-sql-connector-configure\sql-5.png)

     |속성|설명|
     |-----|-----|
     |사용자:특성 검색|이 속성은 **테이블** 로 설정해야 합니다.|
     |사용자:테이블/보기/SP|이 상자에는 **직원** 이 포함되어야 합니다.|
     |사용자:다중값 테이블/뷰 이름||
     |사용자:저장 프로시저 매개 변수||
     |사용자:특성을 검색하는 SQL 쿼리 제공||
 1. **스키마 3** 페이지에서 상자를 채우고 **다음** 을 선택합니다. 개별 상자에 대한 지침은 이미지 뒤에 나오는 표를 사용합니다. 표시되는 특성은 이전 단계에서 제공한 정보에 따라 달라집니다.
 
     ![스키마 3 페이지를 보여 주는 스크린샷](.\media\on-premises-sql-connector-configure\sql-6.png)

     |속성|설명|
     |-----|-----|
     |사용자에 대한 DN 특성 선택||
 1. **스키마 4** 페이지에서 **DataType** 특성과 커넥터의 흐름 방향을 검토합니다. 필요한 경우 조정하고 **다음** 을 선택할 수 있습니다.
 
     ![스키마 4 페이지 스크린샷](.\media\on-premises-sql-connector-configure\sql-7.png)  
 1. **전체** 페이지에서 상자를 채우고, **다음** 을 선택합니다. 개별 상자에 대한 지침은 이미지 뒤에 나오는 표를 사용합니다.
 
     ![전체 페이지를 보여 주는 스크린샷](.\media\on-premises-sql-connector-configure\sql-8.png)

     |속성|설명|
     |-----|-----|
     |워터마크 쿼리||
     |데이터 원본 표준 시간대|데이터 원본이 있는 표준 시간대를 선택합니다.|
     |데이터 원본 날짜 시간 형식|데이터 원본의 형식을 지정합니다.|
     |명명된 매개 변수를 사용하여 저장 프로시저 실행||
     |작업 방법||
     |확장 이름||
     |암호 SP 이름 설정||
     |암호 SP 매개 변수 설정||
 1. **파티션 선택** 페이지에서 올바른 파티션이 선택되었는지 확인하고 **다음** 을 선택합니다.
 
     ![파티션 선택 페이지 스크린샷](.\media\on-premises-sql-connector-configure\sql-9.png)

 1. **실행 프로필** 페이지에서 사용할 실행 프로필을 선택하고 **다음** 을 선택합니다.
 
     ![실행 프로필 페이지를 보여 주는 스크린샷](.\media\on-premises-sql-connector-configure\sql-10.png)

     |속성|설명|
     |-----|-----|
     |내보내기|데이터를 SQL로 내보내는 실행 프로필입니다. 이 실행 프로필은 필수입니다.|
     |전체 가져오기|이전에 지정한 SQL 원본에서 모든 데이터를 가져오는 실행 프로필입니다.|
     |델타 가져오기|마지막 전체 또는 델타 가져오기 이후의 변경 내용만 SQL에서 가져오는 실행 프로필입니다.|
 
 1. **실행 프로필** 페이지에서 상자를 채우고 **다음** 을 선택합니다. 개별 상자에 대한 지침은 이미지 뒤에 나오는 표를 사용합니다.
  
     ![내보내기 정보 입력 스크린샷](.\media\on-premises-sql-connector-configure\sql-11.png)

     |속성|Description|
     |-----|-----|
     |작업 방법||
     |테이블/보기/SP||
     |시작 인덱스 매개 변수 이름||
     |끝 인덱스 매개 변수 이름||
     |저장 프로시저 매개 변수||
 
 1. **개체 형식** 페이지에서 상자를 채우고, **다음** 을 선택합니다. 개별 상자에 대한 지침은 이미지 뒤에 나오는 표를 사용합니다. 
 
     ![개체 형식 페이지를 보여 주는 스크린샷](.\media\on-premises-sql-connector-configure\sql-12.png)

     |속성|Description|
     |-----|-----|
     |대상 개체|구성할 개체입니다.|
     |앵커|개체의 앵커로 사용되는 특성입니다. 이 특성은 대상 시스템에서 고유해야 합니다. Azure AD 프로비전 서비스는 초기 주기 후에 이 특성을 사용하여 ECMA 호스트를 쿼리합니다. 이 앵커 값은 스키마 3의 앵커 값과 같아야 합니다.|
     |쿼리 특성|ECMA 호스트에서 메모리 내 캐시를 쿼리하는 데 사용됩니다. 이 특성은 고유해야 합니다.|
     |DN|대상 개체의 고유 이름에 사용되는 특성입니다. 대부분의 경우 **자동 생성됨** 확인란을 선택해야 합니다. 선택되지 않은 경우 DN 특성이 DN을 CN = anchorValue, Object = objectType 형식으로 저장하는 Azure AD의 특성에 매핑되었는지 확인합니다.|
 
 1. ECMA 호스트는 대상 시스템에서 지원하는 특성을 검색합니다. 해당 특성 중에서 Azure AD에 노출할 특성을 선택할 수 있습니다. 그런 다음, 프로비저닝을 위해 Azure Portal에서 특성을 구성할 수 있습니다. **특성 선택** 페이지의 드롭다운 목록에서 추가할 특성을 선택합니다.
 
     ![특성 선택 페이지를 보여 주는 스크린샷](.\media\on-premises-sql-connector-configure\sql-13.png)

1. **프로비전 해제** 페이지에서 프로비전 해제 정보를 검토하고 필요에 따라 조정합니다. 이전 페이지에서 선택한 특성은 **프로비전 해제** 페이지에서 선택할 수 없습니다. **마침** 을 선택합니다.

     ![프로비전 해제 페이지를 보여 주는 스크린샷](.\media\on-premises-sql-connector-configure\sql-14.png)

## <a name="next-steps"></a>다음 단계

- [앱 프로비저닝](user-provisioning.md)
- [Azure AD ECMA 커넥터 호스트 설치](on-premises-ecma-install.md)
- [Azure AD ECMA 커넥터 호스트 구성](on-premises-ecma-configure.md)
- [자습서: ECMA 커넥터 호스트 일반 SQL 커넥터](tutorial-ecma-sql-connector.md)
