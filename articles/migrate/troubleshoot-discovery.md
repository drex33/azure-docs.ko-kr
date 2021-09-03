---
title: 지속적인 서버 검색, 소프트웨어 인벤토리, SQL 검색 문제 해결
description: 서버 검색, 소프트웨어 인벤토리, SQL, 웹앱 검색에 관한 도움말 보기
author: Vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: troubleshooting
ms.date: 07/01/2020
ms.openlocfilehash: 124ca1ad5f8af9ab2dd08382588c8933db8a09eb
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122530655"
---
# <a name="troubleshoot-the-ongoing-server-discovery-software-inventory-sql-and-web-app-discovery"></a>지속적인 서버 검색, 소프트웨어 인벤토리, SQL, 웹앱 검색에 관한 문제 해결

이 문서는 지속적인 서버 검색, 소프트웨어 인벤토리, SQL Server 인스턴스 및 데이터베이스 검색과 관련된 문제를 해결하는 데 도움이 됩니다.

## <a name="discovered-servers-not-showing-in-portal"></a>포털에 표시되지 않는 검색된 서버

**오류**

포털에 아직 서버가 표시되지 않거나 검색 상태가 **검색 진행 중** 이면 이 오류가 표시됩니다.
 
**수정**

포털에 서버가 표시되지 않으면 vCenter Server에서 실행 중인 서버를 검색하는 데 약 15분, 호스트에서 실행 중인 서버를 검색하는 어플라이언스에 추가된 Hyper-V 호스트마다 2분, 물리적 어플라이언스에 추가된 각 서버를 검색하는 데 1분이 소요되므로 몇 분 정도 기다립니다.

그래도 상태가 변경되지 않으면 다음을 수행하세요.

- **서버** 탭에서 **새로 고침** 을 선택하여 Azure Migrate: 검색 및 평가와 Azure Migrate: 서버 마이그레이션에서 검색된 서버 수를 표시합니다.

위의 단계가 작동하지 않고 VMware 서버를 검색하는 경우 다음을 수행하세요.

1. 지정한 vCenter 계정에 하나 이상의 서버에 대한 액세스되는 권한이 올바르게 설정되어 있는지 확인합니다.
1. vCenter VM 폴더 수준에서 vCenter 계정에 액세스 권한이 부여된 경우 VMware를 확인합니다. Azure Migrate가 VMware에서 서버를 검색할 수 없습니다. 범위 검색에 관해 [자세히 알아보세요](set-discovery-scope.md).

## <a name="server-data-not-updating-in-portal"></a>포털에서 서버 데이터가 업데이트되지 않음

**오류**

검색된 서버가 포털에 표시되지 않거나 서버 데이터가 오래된 경우 이 오류가 표시됩니다. 

**수정**

검색된 서버 구성 데이터의 변경 내용이 포털에 나타나는 데 최대 30분이 걸리고 소프트웨어 인벤토리 데이터의 변경 내용이 나타나는 데 몇 시간이 걸리므로 몇 분 정도 기다립니다. 이 시간 이후에도 데이터가 없으면 새로 고치고 다음과 같이 수행해 보세요.

1. **Windows, Linux, SQL Servers**, **Azure Migrate: 검색 및 평가** 에서 **개요** 를 선택합니다.
1. **관리** 에서 **어플라이언스** 를 선택합니다.
1. **서비스 새로 고침** 을 선택합니다.
새로고침 작업이 완료될 때까지 기다립니다. 이제 최신 정보가 표시됩니다.

## <a name="deleted-servers-appear-in-portal"></a>삭제된 서버는 포털에 표시됩니다.

**오류**

삭제된 서버가 포털에 계속 나타나면 이 오류가 표시됩니다.

**수정**

데이터가 계속 표시되면 30분 동안 기다렸다가 다음과 같이 합니다.

**Windows, Linux, SQL Servers**, **Azure Migrate: 검색 및 평가** 에서 **개요** 를 선택합니다.
1. **관리** 에서 **어플라이언스** 를 선택합니다.
1. **서비스 새로 고침** 을 선택합니다.
새로고침 작업이 완료될 때까지 기다립니다. 이제 최신 정보가 표시됩니다.

## <a name="i-imported-a-csv-but-i-see-discovery-is-in-progress"></a>CSV를 가져왔지만 "검색 진행 중"이 표시됩니다.

유효성 검사 실패로 인해 CSV 업로드가 실패한 경우에 이 상태가 발생할 수 있습니다. 

**수정**

CSV를 다시 가져옵니다. 이전 업로드의 오류 보고서를 다운로드하고 파일의 수정 지침에 따라 오류를 해결할 수 있습니다. **서버 검색** 페이지의 **가져오기 세부 정보** 섹션에서 오류 보고서를 다운로드합니다.

## <a name="do-not-see-software-inventory-details-even-after-updating-guest-credentials"></a>게스트 자격 증명을 업데이트한 후에도 소프트웨어 인벤토리 세부 정보를 보지 않습니다.

**수정**

소프트웨어 인벤토리 검색은 24시간마다 한 번씩 실행됩니다. 이 작업은 검색된 서버 수에 따라 몇 분 정도 걸릴 수 있습니다. 세부 정보를 즉시 보려면 다음과 같이 새로 고침합니다.

1. **Windows, Linux, SQL Servers**, **Azure Migrate: 검색 및 평가** 에서 **개요** 를 선택합니다.
1. **관리** 에서 **어플라이언스** 를 선택합니다.
1. **서비스 새로 고침** 을 선택합니다.
새로고침 작업이 완료될 때까지 기다립니다. 이제 최신 정보가 표시됩니다.

## <a name="unable-to-export-software-inventory"></a>소프트웨어 인벤토리를 내보낼 수 없음

**오류**

기여자 권한이 없으면 이 오류가 표시됩니다.

**수정**

포털에서 인벤토리를 다운로드하는 사용자에게 구독에 대한 참가자 권한이 있는지 확인합니다.

## <a name="common-software-inventory-errors"></a>일반적인 소프트웨어 인벤토리 오류

Azure Migrate은 Azure Migrate: 검색 및 평가를 사용하여 소프트웨어 인벤토리를 지원합니다. 소프트웨어 인벤토리는 현재 VMware에만 지원됩니다. 소프트웨어 인벤토리의 요구사항에 관해 [자세히 알아보세요](how-to-discover-applications.md).

소프트웨어 인벤토리 오류 목록은 아래 표에 요약되어 있습니다.

> [!Note]
> 필요한 데이터를 수집하기 위해 소프트웨어 인벤토리와 동일한 방법론을 따르기 때문에 에이전트 없는 종속성 분석에서도 동일한 오류가 발생할 수 있습니다.

| **오류** | **원인** | **동작** |
|--|--|--|
| **9000:** 서버의 VMware 도구 상태를 감지할 수 없습니다. | VMware 도구가 서버에 설치되어 있지 않거나 설치된 버전이 손상되었을 수 있습니다. | 버전 10.2.1 이상의 VMware 도구가 서버에 설치되어 실행 중인지 확인합니다. |
| **9001:** VMware 도구가 서버에 설치되지 않았습니다. | VMware 도구가 서버에 설치되어 있지 않거나 설치된 버전이 손상되었을 수 있습니다. | 버전 10.2.1 이상의 VMware 도구가 서버에 설치되어 실행 중인지 확인합니다. |
| **9002:** VMware 도구가 서버에서 실행되고 있지 않습니다. | VMware 도구가 서버에 설치되어 있지 않거나 설치된 버전이 손상되었을 수 있습니다. | 버전 10.2.0 이상의 VMware 도구가 서버에 설치되어 실행 중인지 확인합니다. |
| **9003:** 서버에서 실행 중인 운영 체제 유형은 지원되지 않습니다. | 서버에서 실행 중인 운영체제가 Windows나 Linux가 아닙니다. | Windows 및 Linux OS 유형만 지원됩니다. 서버가 실제 실행 중인 Windows 또는 Linux OS인 경우 vCenter Server에 지정된 운영 체제 유형을 확인합니다. |
| **9004:** 서버가 실행 중인 상태가 아닙니다. | 서버의 전원이 꺼져 있습니다. | 서버가 실행 중인 상태인지 확인합니다. |
| **9005:** 서버에서 실행 중인 운영 체제 유형은 지원되지 않습니다. | 서버에서 실행 중인 운영체제가 Windows나 Linux가 아닙니다. | Windows 및 Linux OS 유형만 지원됩니다. \<FetchedParameter> 운영 체제는 현재 지원되지 않습니다. |
| **9006:** 서버에서 검색 메타데이터 파일을 다운로드하는 데 필요한 URL이 비어 있습니다. | 어플라이언스의 검색 에이전트가 예상대로 작동하지 않아 생긴 일시적인 문제일 수 있습니다. | 문제는 24시간 이내에 다음 주기에서 자동으로 해결됩니다. 문제가 지속되면 Microsoft 지원 사례를 제출하세요. |
| **9007:** 메타데이터를 수집하기 위해 스크립트를 실행하는 프로세스를 서버에서 찾을 수 없습니다. | 어플라이언스의 검색 에이전트가 예상대로 작동하지 않아 생긴 일시적인 문제일 수 있습니다. | 문제는 24시간 이내에 다음 주기에서 자동으로 해결됩니다. 문제가 지속되면 Microsoft 지원 사례를 제출하세요. |
| **9008:** 메타데이터를 수집하기 위해 서버에서 실행 중인 프로세스의 상태를 검색할 수 없습니다. | 내부 오류로 인한 일시적인 문제일 수 있습니다. | 문제는 24시간 이내에 다음 주기에서 자동으로 해결됩니다. 문제가 지속되면 Microsoft 지원 사례를 제출하세요. |
| **9009:** Window UAC(사용자 계정 컨트롤)가 서버에서 검색 작업을 실행하지 못하게 합니다. | Windows UAC(사용자 계정 컨트롤) 설정이 서버에서 설치된 애플리케이션의 검색을 제한하고 있습니다. | 영향을 받는 서버에서 제어판의 ‘사용자 계정 컨트롤’ 설정 수준을 낮추세요. |
| **9010:** 서버 전원이 꺼져 있습니다. | 서버의 전원이 꺼져 있습니다. | 서버의 전원이 켜진 상태인지 확인합니다. |
| **9011:** 발견된 메타데이터가 포함된 파일을 서버에서 찾을 수 없습니다. | 내부 오류로 인한 일시적인 문제일 수 있습니다. | 문제는 24시간 이내에 다음 주기에서 자동으로 해결됩니다. 문제가 지속되면 Microsoft 지원 사례를 제출하세요. |
| **9012:** 서버에서 발견된 메타데이터가 포함된 파일이 비어 있습니다. | 내부 오류로 인한 일시적인 문제일 수 있습니다. | 문제는 24시간 이내에 다음 주기에서 자동으로 해결됩니다. 문제가 지속되면 Microsoft 지원 사례를 제출하세요. |
| **9013:** 서버에 로그인할 때마다 새로운 임시 사용자 프로필이 만들어집니다. | 서버에 로그인할 때마다 새로운 임시 사용자 프로필이 만들어집니다. | 이 문제를 해결하는 데 도움이 되도록 Microsoft 지원 사례를 제출합니다. |
| **9014:** ESXi 호스트에서 발생한 오류로 인해 발견된 메타데이터가 포함된 파일을 발견할 수 없습니다.  오류 코드: %ErrorCode; 세부 정보: %ErrorMessage | ESXi 호스트 \<HostName>에서 오류가 발생했습니다.  오류 코드: %ErrorCode; 세부 정보: %ErrorMessage | 서버가 실행 중인 ESXi 호스트에서 포트 443이 열려 있는지 확인합니다.<br/><br/> 문제 해결 방법에 관해 [자세히 알아보세요](troubleshoot-discovery.md#error-9014-httpgetrequesttoretrievefilefailed).|
| **9015:** 서버 검색을 위해 제공된 vCenter Server 사용자 계정에 게스트 작업 권한이 사용하도록 설정되어 있지 않습니다. | 게스트 작업의 필수 권한이 vCenter Server 사용자 계정에서 사용하도록 설정되지 않았습니다. | 서버와 상호 작용하고 필요한 데이터를 끌어오기 위해 vCenter Server 사용자 계정에 Virtual Machines > 게스트 작업에 사용할 수 있는 권한이 있는지 확인합니다. <br/><br/> 필요한 권한으로 vCenter Server 계정을 설정하는 방법에 관해 [자세히 알아보세요](tutorial-discover-vmware.md#prepare-vmware). |
| **9016:** 서버의 게스트 작업 에이전트가 오래되어 메타데이터를 발견할 수 없습니다. | VMware 도구가 서버에 설치되어 있지 않거나 설치된 버전이 최신 버전이 아닙니다. | VMware 도구가 서버에 설치되어 있고 최신 상태로 실행 중인지 확인합니다. VMware Tools 버전은 버전 10.2.1 이상이어야 합니다. |
| **9017:** 발견된 메타데이터가 포함된 파일을 서버에서 찾을 수 없습니다. | 내부 오류로 인한 일시적인 문제일 수 있습니다. | 이 문제를 해결하는 데 도움이 되도록 Microsoft 지원 사례를 제출합니다. |
| **9018:** PowerShell이 서버에 설치되어 있지 않습니다. | 서버에서 PowerShell을 찾을 수 없습니다. | 서버에 PowerShell 버전 2.0 이상이 설치되어 있는지 확인합니다. <br/><br/> 문제 해결 방법에 관해 [자세히 알아보세요](troubleshoot-discovery.md#error-9018-powershellnotfound).|
| **9019:** 서버에서 게스트 작업 실패로 인해 메타데이터를 발견할 수 없습니다. | 서버에서 VMware 게스트 작업에 실패했습니다. 서버에서 ```<FriendlyNameOfCredentials>.``` 자격 증명을 시도할 때 문제가 발생했습니다. | 어플라이언스에 제공된 서버 자격 증명이 유효하고 자격 증명에 제공된 사용자 이름이 UPN 형식인지 확인합니다. (가능한 원인에서 Azure Migrate가 시도한 자격 증명의 친숙한 이름 찾기) |
| **9020:** 서버에서 발견된 메타데이터를 포함하는 데 필요한 파일을 만들 수 없습니다. | 어플라이언스 또는 온-프레미스 그룹 정책에 제공된 자격 증명과 연결된 역할이 필수 폴더에서의 파일 생성을 제한하고 있습니다. 서버에서 ```<FriendlyNameOfCredentials>.``` 자격 증명을 시도할 때 문제가 발생했습니다. | 1. 어플라이언스에 제공된 자격 증명에 서버의 \<folder path/folder name> 폴더에 대한 파일 만들기 권한이 있는지 확인합니다. <br/>2. 어플라이언스에 제공된 자격 증명에 필요한 권한이 없는 경우 다른 자격 증명 세트를 제공하거나 기존 자격 증명을 편집합니다. (가능한 원인에서 Azure Migrate가 시도한 자격 증명의 친숙한 이름 찾기) |
| **9021:** 서버의 올바른 경로에서 발견된 메타데이터를 포함하는 데 필요한 파일을 만들 수 없습니다. | VMware 도구에서 파일을 만들기 위한 잘못된 파일 경로를 보고합니다. | 버전 10.2.0 이상의 VMware 도구가 서버에 설치되어 실행 중인지 확인합니다. |
| **9022:** 서버에서 Get-WmiObject cmdlet을 실행하기 위한 액세스가 거부되었습니다. | 어플라이언스 또는 온-프레미스 그룹 정책에 제공된 자격 증명과 연결된 역할이 WMI 개체에 대한 액세스를 제한하고 있습니다. 서버에서 \<FriendlyNameOfCredentials> 자격 증명을 시도할 때 문제가 발생했습니다. | 1. 어플라이언스에 제공된 자격 증명에 파일 만들기 관리자 권한이 있고 WMI가 사용하도록 설정되어 있는지 확인합니다. <br/> 2. 어플라이언스에 제공된 자격 증명에 필요한 권한이 없는 경우 다른 자격 증명 세트를 제공하거나 기존 자격 증명을 편집합니다. (가능한 원인에서 Azure Migrate가 시도한 자격 증명의 친숙한 이름을 찾습니다).<br/><br/> 문제 해결 방법에 관해 [자세히 알아보세요](troubleshoot-discovery.md#error-9022-getwmiobjectaccessdenied).|
| **9023:** %SystemRoot% 환경 변수 값이 비어 있으므로 PowerShell을 실행할 수 없습니다. | 서버에 대한 %SystemRoot% 환경 변수의 값이 비어 있습니다. | 1. 영향을 받는 서버에서 echo %systemroot% 명령을 실행하여 환경 변수가 빈 값을 반환하는지 확인합니다. <br/> 2. 문제가 지속되면 Microsoft 지원 사례를 제출합니다. |
| **9024:** %TEMP% 환경 변수 값이 비어 있으므로 검색을 수행할 수 없습니다. | 서버에 대한 %TEMP% 환경 변수 값이 비어 있습니다. | 1. 영향을 받는 서버에서 echo %temp% 명령을 실행하여 환경 변수가 빈 값을 반환하는지 확인합니다. <br/> 2. 문제가 지속되면 Microsoft 지원 사례를 제출합니다. |
| **9025:** 검색을 수행할 수 없습니다. PowerShell이 서버에서 손상되었습니다. | PowerShell이 서버에서 손상되었습니다. | PowerShell을 다시 설치하고 영향을 받는 서버에서 실행 중인지 확인합니다. |
| **9026:** 서버에서 게스트 작업을 실행할 수 없습니다. | 서버의 현재 상태로 인해 게스트 작업을 실행할 수 없습니다. | 1. 영향을 받는 서버가 가동되어 실행 중인지 확인합니다.<br/> 2. 문제가 지속되면 Microsoft 지원 사례를 제출합니다. |
| **9027:** 게스트 작업 에이전트가 서버에서 실행되고 있지 않으므로 메타데이터를 발견할 수 없습니다. | 서버의 게스트 작업 에이전트에 연결할 수 없습니다. | 버전 10.2.0 이상의 VMware 도구가 서버에 설치되어 실행 중인지 확인합니다. |
| **9028:** 서버의 저장 공간이 부족하여 발견된 메타데이터를 포함하는 데 필요한 파일을 만들 수 없습니다. | 서버 디스크에 충분한 스토리지 공간이 없습니다. | 영향을 받는 서버의 디스크 스토리지에 사용 가능한 공간이 충분한지 확인합니다. |
| **9029:** 어플라이언스에 제공된 로그인 정보에 PowerShell을 실행할 수 있는 액세스 권한이 없습니다. | 어플라이언스에 제공된 자격 증명에는 PowerShell을 실행할 수 있는 액세스 권한이 없습니다. 서버에서 \<FriendlyNameOfCredentials> 자격 증명을 시도할 때 문제가 발생했습니다. | 1. 어플라이언스에 제공된 자격 증명이 서버의 PowerShell에 액세스할 수 있는지 확인합니다.<br/> 2. 어플라이언스에 제공된 자격 증명에 필요한 액세스 권한이 없는 경우 다른 자격 증명 세트를 제공하거나 기존 자격 증명을 편집합니다. (가능한 원인에서 Azure Migrate가 시도한 자격 증명의 친숙한 이름 찾기) |
| **9030:** 서버가 호스팅되는 ESXi 호스트가 연결이 끊긴 상태이므로 발견된 메타데이터를 수집할 수 없습니다. | 서버가 상주하는 ESXi 호스트는 연결이 끊긴 상태입니다. | 서버를 실행하는 ESXi 호스트가 연결된 상태인지 확인합니다. |
| **9031:** 서버가 호스팅되는 ESXi 호스트가 응답하지 않아 발견된 메타데이터를 수집할 수 없습니다. | 서버가 있는 ESXi 호스트가 잘못된 상태에 있습니다. | 서버를 실행하는 ESXi 호스트가 실행 및 연결 상태인지 확인합니다. |
| **9032:** 내부 오류가 발생하여 검색할 수 없습니다. | 발생한 문제는 내부 오류로 인한 것입니다. | [여기](troubleshoot-discovery.md#error-9032-invalidrequest)의 단계에 따라 문제를 해결하세요. 문제가 지속되면 Microsoft 지원 사례를 여세요. |
| **9033:** 서버용 어플라이언스에 제공된 자격 증명의 사용자 이름에 잘못된 문자가 있으므로 검색할 수 없습니다. | 어플라이언스에 제공된 자격 증명의 사용자 이름에 잘못된 문자가 있습니다. 서버에서 \<FriendlyNameOfCredentials> 자격 증명을 시도할 때 문제가 발생했습니다. | 어플라이언스에 제공된 자격 증명의 사용자 이름에 잘못된 문자가 없는지 확인합니다. 어플라이언스 구성 관리자로 돌아가서 자격 증명을 편집할 수 있습니다. (가능한 원인에서 Azure Migrate가 시도한 자격 증명의 친숙한 이름을 찾습니다). |
| **9034:** 서버용 어플라이언스에 제공된 자격 증명의 사용자 이름이 UPN 형식이 아니기 때문에 검색할 수 없습니다. | 어플라이언스에 제공된 자격 증명에 UPN 형식의 사용자 이름이 없습니다. 서버에서 \<FriendlyNameOfCredentials> 자격 증명을 시도할 때 문제가 발생했습니다. | 어플라이언스에 제공된 자격 증명의 사용자 이름이 UPN(사용자 계정 이름) 형식인지 확인합니다. 어플라이언스 구성 관리자로 돌아가서 자격 증명을 편집할 수 있습니다. (가능한 원인에서 Azure Migrate가 시도한 자격 증명의 친숙한 이름을 찾습니다). |
| **9035:** 올바르게 설정되지 않은 PowerShell 언어 모드로 검색할 수 없습니다. | PowerShell 언어 모드가 ‘전체 언어’로 설정되지 않았습니다. | PowerShell 언어 모드가 '전체 언어'로 설정되어 있는지 확인하세요. |
| **9036:** 서버용 어플라이언스에 제공된 자격 증명의 사용자 이름이 UPN 형식이 아니기 때문에 검색할 수 없습니다. | 어플라이언스에 제공된 자격 증명에 UPN 형식의 사용자 이름이 없습니다. 서버에서 \<FriendlyNameOfCredentials> 자격 증명을 시도할 때 문제가 발생했습니다. | 어플라이언스에 제공된 자격 증명의 사용자 이름이 UPN(사용자 계정 이름) 형식인지 확인합니다. 어플라이언스 구성 관리자로 돌아가서 자격 증명을 편집할 수 있습니다. (가능한 원인에서 Azure Migrate가 시도한 자격 증명의 친숙한 이름을 찾습니다). |
| **9037:** 서버의 긴 응답 시간으로 인해 메타데이터 수집이 일시적으로 일시 중지되었습니다. | 서버가 응답하는 데 너무 오래 걸립니다. | 문제는 24시간 이내에 다음 주기에서 자동으로 해결됩니다. 문제가 지속되면 Microsoft 지원 사례를 제출하세요. |
| **10000:** 서버에서 실행 중인 운영 체제 유형은 지원되지 않습니다. | 서버에서 실행 중인 운영체제가 Windows나 Linux가 아닙니다. | Windows 및 Linux OS 유형만 지원됩니다. \<GuestOSName> 운영 체제는 현재 지원되지 않습니다. |
| **10001:** 검색 메타데이터를 수집하는 데 필요한 스크립트를 서버에서 찾을 수 없습니다. | 검색을 수행하는 데 필요한 스크립트가 삭제되었거나 예상 위치에서 제거되었을 수 있습니다. | 이 문제를 해결하는 데 도움이 되도록 Microsoft 지원 사례를 제출합니다. |
| **10002:** 서버에서 검색 작업 시간이 초과하였습니다. | 어플라이언스의 검색 에이전트가 예상대로 작동하지 않아 생긴 일시적인 문제일 수 있습니다. | 문제는 24시간 이내에 다음 주기에서 자동으로 해결됩니다. 문제가 해결되지 않으면 [여기](troubleshoot-discovery.md#error-10002-scriptexecutiontimedoutonvm)에 있는 단계에 따라 문제를 해결하세요. 문제가 계속되면 Microsoft 지원 사례를 제출하세요.|
| **10003:** 검색 작업을 실행하는 프로세스가 오류와 함께 종료되었습니다. | 검색 작업을 실행하는 프로세스가 오류로 인해 갑자기 종료되었습니다.| 문제는 24시간 이내에 다음 주기에서 자동으로 해결됩니다. 문제가 지속되면 Microsoft 지원 사례를 제출하세요. |
| **10004:** 어플라이언스에서 서버 OS 유형에 대한 자격 증명이 제공되지 않았습니다. | 서버 OS 유형에 대한 자격 증명이 어플라이언스에 추가되지 않았습니다. | 1. 어플라이언스에서 영향을 받는 서버의 OS 유형에 대한 자격 증명을 추가했는지 확인합니다.<br/> 2. 이제 어플라이언스에 여러 서버 자격 증명을 추가할 수 있습니다. |
| **10005:** 서버용 어플라이언스에 제공된 자격 증명이 잘못되었습니다. | 어플라이언스에 제공된 자격 증명이 유효하지 않습니다. 서버에서 ```\<FriendlyNameOfCredentials>.``` 자격 증명을 시도할 때 문제가 발생했습니다. | 1. 어플라이언스에서 제공된 자격 증명이 유효하고 자격 증명을 사용하여 서버에 액세스할 수 있는지 확인합니다.<br/> 2. 이제 어플라이언스에 여러 서버 자격 증명을 추가할 수 있습니다.<br/> 3. 어플라이언스 구성 관리자로 돌아가서 다른 자격 증명 세트를 제공하거나 기존 자격 증명을 편집합니다. (가능한 원인에서 Azure Migrate가 시도한 자격 증명의 친숙한 이름을 찾습니다). <br/><br/> 문제 해결 방법에 관해 [자세히 알아보세요](troubleshoot-discovery.md#error-10005-guestcredentialnotvalid).|
| **10006:** 서버에서 실행 중인 운영 체제 유형은 지원되지 않습니다. | 서버에서 실행 중인 운영체제가 Windows나 Linux가 아닙니다. | Windows 및 Linux OS 유형만 지원됩니다. \<GuestOSName> 운영 체제는 현재 지원되지 않습니다. |
| **10007:** 서버에서 검색된 메타데이터를 처리할 수 없습니다. | 검색된 메타데이터가 포함된 파일의 내용을 구문 분석하는 동안 오류가 발생했습니다. | 이 문제를 해결하는 데 도움이 되도록 Microsoft 지원 사례를 제출합니다. |
| **10008:** 서버에서 검색된 메타데이터를 포함하는 데 필요한 파일을 만들 수 없습니다. | 어플라이언스 또는 온-프레미스 그룹 정책에 제공된 자격 증명과 연결된 역할이 필수 폴더에서의 파일 생성을 제한하고 있습니다. 서버에서 ```<FriendlyNameOfCredentials>.``` 자격 증명을 시도할 때 문제가 발생했습니다. | 1. 어플라이언스에 제공된 자격 증명에 서버의 \<folder path/folder name> 폴더에 대한 파일 만들기 권한이 있는지 확인합니다.<br/> 2. 어플라이언스에 제공된 자격 증명에 필요한 권한이 없는 경우 다른 자격 증명 세트를 제공하거나 기존 자격 증명을 편집합니다. (가능한 원인에서 Azure Migrate가 시도한 자격 증명의 친숙한 이름 찾기) |
| **10009:** 검색된 메타데이터를 서버의 파일에 쓸 수 없습니다. | 어플라이언스 또는 온-프레미스 그룹 정책에 제공된 자격 증명과 연결된 역할이 서버의 파일 쓰기를 제한하고 있습니다. 서버에서 \<FriendlyNameOfCredentials> 자격 증명을 시도할 때 문제가 발생했습니다. | 1. 어플라이언스에 제공된 자격 증명이 폴더 <폴더 경로/폴더 이름> 서버에서 쓰기 권한을 갖고 있는지 확인합니다.<br/> 2. 어플라이언스에 제공된 자격 증명에 필요한 권한이 없는 경우 다른 자격 증명 세트를 제공하거나 기존 자격 증명을 편집합니다. (가능한 원인에서 Azure Migrate가 시도한 자격 증명의 친숙한 이름 찾기) |
| **10010:** 일부 메타데이터를 수집하는 데 필요한 %CommandName; 명령이 서버에서 누락되었으므로 검색할 수 없습니다. | %CommandName; 명령을 포함하는 패키지가 서버에 설치되어 있지 않습니다. | %CommandName; 명령을 포함하는 패키지가 서버에 설치되어 있는지 확인합니다. |
| **10011:** 어플라이언스에 제공된 로그인 정보는 대화형 세션에 대한 로그인 및 로그오프에 사용되었습니다. | 대화형 로그인 및 로그오프를 수행하면 사용되는 계정 프로필에서 레지스트리 키를 언로드합니다. 이 조건을 사용하면 나중에 키를 사용할 수 없게 됩니다. | [여기](/sharepoint/troubleshoot/administration/800703fa-illegal-operation-error#resolutionus/sharepoint/troubleshoot/administration/800703fa-illegal-operation-error)에 설명된 해결 방법을 사용하세요. |
| **10012:** 서버용 어플라이언스에 자격 증명이 제공되지 않았습니다. | 서버에 대한 자격 증명이 제공되지 않았거나 어플라이언스에서 잘못된 도메인 이름으로 도메인 자격 증명을 제공했습니다. 이 오류의 원인에 관해 [자세히 알아보세요](troubleshoot-discovery.md#error-10012-credentialnotprovided). | 1. 자격 증명이 어플라이언스에 서버에 제공되고 자격 증명을 사용하여 서버에 액세스할 수 있는지 확인합니다. <br/> 2. 이제 어플라이언스에 여러 서버 자격 증명을 추가할 수 있습니다. 어플라이언스 구성 관리자로 돌아가서 서버의 자격 증명을 제공합니다.|

## <a name="error-9014-httpgetrequesttoretrievefilefailed"></a>오류 9014: HTTPGetRequestToRetrieveFileFailed

### <a name="cause"></a>원인
이 문제는 어플라이언스의 VMware 검색 에이전트가 서버가 호스팅되는 ESXi 호스트를 통해 서버 파일 시스템에서 종속성 데이터가 포함된 출력 파일을 다운로드하려고 할 때 발생합니다.

### <a name="remediation"></a>수정
- 어플라이언스 서버에서 PowerShell을 열고 다음 명령을 실행하여 어플라이언스에서 443 포트(종속성 데이터를 끌어오기 위해서는 ESXi 호스트에 열려 있어야 함)의 ESXi 호스트 _(오류 메시지에 제공된 이름)_ 에 대한 TCP 연결을 테스트할 수 있습니다.
    ````
    Test -NetConnection -ComputeName <Ip address of the ESXi host> -Port 443
    ````
- 명령이 성공적인 연결을 반환하면 Azure Migrate 프로젝트> 검색 및 평가>개요>관리>어플라이언스에서 어플라이언스 이름을 선택한 다음 **서비스 새로 고침** 을 선택합니다.

## <a name="error-9018-powershellnotfound"></a>오류 9018: PowerShellNotFound

### <a name="cause"></a>원인
이 오류는 일반적으로 Windows Server 2008 이하를 실행하는 서버에서 발생합니다.

### <a name="remediation"></a>수정
서버의 ($SYSTEMROOT)\System32\WindowsPowershell\v1.0\powershell.exe 위치에 필요한 PowerShell 버전(2.0 이상)을 설치해야 합니다. Windows Server에 PowerShell을 설치하는 방법에 관해 [자세히 알아보세요](/powershell/scripting/windows-powershell/install/installing-windows-powershell).

필요한 PowerShell 버전을 설치한 후 [여기](troubleshoot-discovery.md#mitigation-verification-using-vmware-powercli)에 있는 단계에 따라 오류가 해결되었는지 확인할 수 있습니다.

## <a name="error-9022-getwmiobjectaccessdenied"></a>오류 9022: GetWMIObjectAccessDenied

### <a name="remediation"></a>수정
어플라이언스에 제공된 사용자 계정에 WMI 네임스페이스 및 하위 네임스페이스에 대한 액세스 권한이 있는지 확인합니다. 다음 단계에 따라 액세스 권한을 설정할 수 있습니다.
1.  이 오류를 보고하는 서버로 이동합니다.
1. 검색하고 시작 메뉴에서 **실행** 을 선택합니다. **실행** 대화 상자의 **열기:** 텍스트 필드에 *wmimgmt.msc* 를 입력하고 Enter를 누릅니다.
1. 열린 wmimgmt 콘솔의 왼쪽 패널에서 **WMI Control(로컬)** 을 찾을 수 있습니다. 그것을 마우스 오른쪽 단추로 클릭하고 메뉴에서 **속성** 을 선택합니다.
1. WMI 컨트롤(로컬) 속성 대화 상자에서 **보안** 탭을 선택합니다.
1. **보안** 단추를 선택하면 **루트 보안** 대화 상자가 표시됩니다.
1. **고급** 단추를 선택하여 **루트의 고급 보안 설정** 대화 상자를 엽니다. 
1. **추가** 단추를 선택하여 **루트의 권한 항목** 대화 상자를 엽니다.
1. **주체 선택** 을 클릭하여 **사용자, 컴퓨터, 서비스 계정 또는 그룹 선택** 대화 상자를 엽니다.
1. WMI에 대한 액세스 권한을 부여할 사용자 이름 또는 그룹을 선택하고 확인을 클릭합니다.
1. 실행 권한을 부여하고 **적용 대상:** 드롭다운에서 *이 네임스페이스 및 하위 네임스페이스* 를 선택했는지 확인합니다.
1. **적용** 단추를 선택하여 설정을 저장하고 모든 대화 상자를 닫습니다.

필요한 액세스 권한을 얻은 후 [여기](troubleshoot-discovery.md#mitigation-verification-using-vmware-powercli)의 단계에 따라 오류가 해결되었는지 확인할 수 있습니다.

## <a name="error-9032-invalidrequest"></a>오류 9032: InvalidRequest

### <a name="cause"></a>원인
이 문제에는 여러 가지 이유가 있을 수 있습니다. 그 이유 중 하나는 어플라이언스 구성 관리자에 제공된 사용자 이름(서버 자격 증명)에 잘못된 XML 문자가 있어 SOAP 요청을 구문 분석할 때 오류가 발생하기 때문입니다.

### <a name="remediation"></a>수정
- 서버 자격 증명의 사용자 이름에 잘못된 XML 문자가 없고 일반적으로 UPN 형식으로 알려진 username@domain.com 형식인지 확인합니다.
- 어플라이언스에서 로그인 정보를 수정한 후 [여기](troubleshoot-discovery.md#mitigation-verification-using-vmware-powercli)의 단계에 따라 오류가 해결되었는지 확인할 수 있습니다.


## <a name="error-10002-scriptexecutiontimedoutonvm"></a>오류 10002: ScriptExecutionTimedOutOnVm

### <a name="cause"></a>원인
- 이 오류는 서버가 느리거나 응답하지 않고 종속성 데이터를 가져오기 위해 실행된 스크립트가 시간 초과를 시작할 때 발생합니다.
- 검색 에이전트가 서버에서 이 오류를 발견하면 어플라이언스는 응답하지 않는 서버의 과부하를 방지하기 위해 이후에 서버에 대한 에이전트 없는 종속성 분석을 시도하지 않습니다.
- 따라서 서버 문제를 확인하고 검색 서비스를 다시 시작할 때까지 오류가 계속 표시됩니다.

### <a name="remediation"></a>수정
1. 이 오류가 발생한 서버에 로그인합니다.
1. PowerShell에서 다음 명령을 실행합니다.
   ````
   Get-WMIObject win32_operatingsystem;
   Get-WindowsFeature  | Where-Object {$_.InstallState -eq 'Installed' -or ($_.InstallState -eq $null -and $_.Installed -eq 'True')};
   Get-WmiObject Win32_Process;
   netstat -ano -p tcp | select -Skip 4;
   ````
1. 명령이 몇 초 안에 결과를 출력하면 Azure Migrate 프로젝트, 검색 및 평가, 개요, 관리, 어플라이언스에서 어플라이언스 이름을 선택하고 **서비스 새로 고침** 을 선택하여 검색 서비스를 다시 시작합니다.
1. 명령이 출력 없이 시간 초과하면 
- 어떤 프로세스가 서버에서 높은 CPU 또는 메모리를 사용하는지 파악해야 합니다.
- 해당 서버에 더 많은 코어/메모리를 할당하고 명령을 다시 실행할 수 있습니다.

## <a name="error-10005-guestcredentialnotvalid"></a>오류 10005: GuestCredentialNotValid

### <a name="remediation"></a>수정
- 어플라이언스 구성 관리자에서 ‘자격 증명 재검증’을 클릭하여 자격 증명 _(오류에 제공된 친숙한 이름)_ 의 유효성을 확인합니다.
- 어플라이언스에 제공된 동일한 자격 증명을 사용하여 영향을 받는 서버에 로그인할 수 있는지 확인합니다.
- 관리자 계정 대신 해당 서버에 대해 다른 사용자 계정(동일한 도메인에 대해, 서버가 도메인에 가입된 경우를 대비)을 사용해 볼 수 있습니다.
- 문제는 글로벌 카탈로그와 도메인 컨트롤러 간 통신이 끊어졌을 때 발생할 수 있습니다. 도메인 컨트롤러에서 새 사용자 계정을 만들고 어플라이언스에서 동일한 계정을 제공하여 이를 확인할 수 있습니다. 도메인 컨트롤러를 다시 시작해야 할 수도 있습니다.
- 수정 단계를 수행한 후 [여기](troubleshoot-discovery.md#mitigation-verification-using-vmware-powercli)에 있는 단계에 따라 오류가 해결되었는지 확인할 수 있습니다.

## <a name="error-10012-credentialnotprovided"></a>오류 10012: CredentialNotProvided

### <a name="cause"></a>원인
이 오류는 어플라이언스 구성 관리자에서 잘못된 도메인 이름으로 도메인 자격 증명을 제공한 경우 발생합니다. 예를 들어, 사용자 이름이 user@abc.com인 도메인 자격 증명을 제공했지만 도메인 이름을 def.com으로 제공한 경우 서버가 def.com에 연결되어 있으면 해당 자격 증명이 시도되지 않고 이 오류 메시지가 표시됩니다.

### <a name="remediation"></a>수정
- 어플라이언스 구성 관리자로 이동하여 서버 자격 증명을 추가하거나 원인에 설명된 대로 기존 자격 증명을 편집합니다.
- 수정 단계를 수행한 후 [여기](troubleshoot-discovery.md#mitigation-verification-using-vmware-powercli)에 있는 단계에 따라 오류가 해결되었는지 확인할 수 있습니다.

## <a name="mitigation-verification-using-vmware-powercli"></a>VMware PowerCLI를 사용한 완화 확인
위에 나열된 오류에 대한 완화 단계를 사용한 후 어플라이언스 서버에서 몇 가지 PowerCLI 명령을 실행하여 완화가 작동했는지 확인할 수 있습니다. 명령이 성공하면 문제가 이제 해결되었음을 의미합니다. 실패하면 수정 단계를 다시 확인하고 따라야 합니다.

1. 다음 명령을 실행하여 어플라이언스 서버에서 PowerCLI를 설정합니다.
   ````
   Install-Module -Name VMware.PowerCLI -AllowClobber
   Set-PowerCLIConfiguration -InvalidCertificateAction Ignore
   ````
2. 명령에 vCenter Server IP 주소를 제공하고 프롬프트에 자격 증명을 제공하여 어플라이언스에서 vCenter Server에 연결합니다.
   ````
   Connect-VIServer -Server <IPAddress of vCenter Server>
   ````
3. 서버 이름 및 서버 자격 증명(어플라이언스에 제공된 대로)을 제공하여 어플라이언스에서 대상 서버에 연결합니다.
   ````
   $vm = get-VM <VMName>
   $credential = Get-Credential
   ````
4. 소프트웨어 인벤토리의 경우 다음 명령을 실행하여 성공적인 출력이 표시되는지 확인합니다.

  - Windows 서버의 경우:

      ```` 
        Invoke-VMScript -VM $vm -ScriptText "powershell.exe 'Get-WMIObject win32_operatingsystem'" -GuestCredential $credential

        Invoke-VMScript -VM $vm -ScriptText "powershell.exe Get-WindowsFeature" -GuestCredential $credential
      ```` 
   - Linux 서버:
      ````
      Invoke-VMScript -VM $vm -ScriptText "ls" -GuestCredential $credential
      ````
5. 에이전트 없는 종속성 분석의 경우 다음 명령을 실행하여 성공적인 출력을 얻는지 확인합니다.

  - Windows 서버의 경우:

      ```` 
      Invoke-VMScript -VM $vm -ScriptText "powershell.exe 'Get-WmiObject Win32_Process'" -GuestCredential $credential 
  
      Invoke-VMScript -VM $vm -ScriptText "powershell.exe 'netstat -ano -p tcp'" -GuestCredential $credential 
      ```` 
   - Linux 서버:
      ````
      Invoke-VMScript -VM $vm -ScriptText "ps -o pid,cmd | grep -v ]$" -GuestCredential $credential

      Invoke-VMScript -VM $vm -ScriptText "netstat -atnp | awk '{print $4,$5,$7}'" -GuestCredential $credential
      ````
6. 완화가 작동했는지 확인한 후 Azure Migrate 프로젝트> 검색 및 평가>개요>관리>어플라이언스에서 어플라이언스 이름을 선택하고 **서비스 새로 고침** 을 선택하여 새로운 검색 주기를 시작합니다.

## <a name="discovered-sql-server-instances-and-databases-not-in-portal"></a>포털에 없는 검색된 SQL Server 인스턴스와 데이터베이스

어플라이언스에서 검색을 시작한 후 포털에서 인벤토리 데이터를 표시하기 시작하는 데 최대 24시간이 걸릴 수 있습니다.

어플라이언스 구성 관리자에서 Windows 인증 또는 SQL Server 인증 자격 증명을 제공하지 않은 경우, 어플라이언스가 해당 자격 증명을 사용하여 각 SQL Server 인스턴스에 연결할 수 있도록 자격 증명을 추가합니다.

일단 연결되면 어플라이언스는 SQL Server 인스턴스 및 데이터베이스의 구성 및 성능 데이터를 수집합니다. SQL Server 구성 데이터는 24시간마다 업데이트되고, 성능 데이터는 30초마다 캡처됩니다. 따라서 SQL Server 인스턴스의 속성과 데이터베이스 상태, 호환성 수준 등의 데이터베이스에 대한 변경이 포털에서 업데이트되는 데 최대 24 시간이 걸릴 수 있습니다.

## <a name="sql-server-instance-is-showing-up-in-not-connected-state-on-portal"></a>SQL Server 인스턴스가 포털에서 "연결되지 않음" 상태로 표시됩니다.

SQL Server 인스턴스 및 데이터베이스를 검색하는 동안 발생한 문제를 보려면 프로젝트의 **검색된 서버** 페이지의 연결 상태 열에서 “연결되지 않음” 상태를 클릭하세요.

완전히 검색되지 않았거나 연결되지 않은 상태에 있는 SQL 인스턴스를 포함하는 서버 맨 위에 평가를 작성하면 준비 상태가 "알 수 없음"으로 표시될 수 있습니다.

## <a name="common-sql-server-instances-and-database-discovery-errors"></a>일반적인 SQL Server 인스턴스 및 데이터베이스 검색 오류

Azure Migrate를 사용하여 온-프레미스 컴퓨터에서 실행되는 SQL Server 인스턴스 및 데이터베이스의 검색을 지원합니다.: 검색 및 평가 SQL 검색은 현재 VMware에만 지원됩니다. [검색](tutorial-discover-vmware.md) 지침서를 참조하세요.

일반적인 SQL 검색 오류는 표에 요약되어 있습니다.

| **오류** | **원인** | **동작** | **가이드**
|--|--|--|--|
|30000: 이 SQL Server와 연결된 자격 증명이 작동하지 않습니다.|수동으로 연결된 자격 증명이 잘못되었거나 자동 연결된 자격 증명이 더 이상 SQL 서버에 액세스할 수 없습니다.|어플라이언스에서 SQL Server 자격 증명을 추가하거나 다음 SQL 검색 사이클이나 강제로 새로 고침이 수행될 때까지 기다립니다.| - |
|30001: 어플라이언스에서 SQL Server에 연결할 수 없습니다.|1. 어플라이언스는 SQL Server에 대한 네트워크 시야를 갖지 않습니다.<br/>2. SQL Server와 어플라이언스 간의 방화벽 차단 연결.|1. 어플라이언스에서 SQL Server 연결할 수 있는지 확인합니다.<br/>2. 어플라이언스에서 SQL Server로 들어오는 연결을 허용합니다.| - | 
|30003: 인증서를 신뢰할 수 없습니다.|SQL Server를 실행하는 컴퓨터에 신뢰할 수 있는 인증서가 설치되어 있지 않습니다.|서버에 신뢰할 수 있는 인증서를 설정하세요. [자세한 정보](/troubleshoot/sql/connect/error-message-when-you-connect)| [보기](/troubleshoot/sql/connect/error-message-when-you-connect) |
|30004: 권한 부족.|이 오류는 SQL Server 인스턴스를 검사하는 데 필요한 권한이 부족한 경우에 발생할 수 있습니다. |SQL Server 인스턴스 및 데이터베이스 검색을 위해 어플라이언스에서 제공한 자격 증명/계정에 sysadmin 역할을 부여합니다. [자세한 정보](/sql/t-sql/statements/grant-server-permissions-transact-sql)| [보기](/sql/t-sql/statements/grant-server-permissions-transact-sql) |
|30005: master 데이터베이스의 기본 데이터베이스 문제로 인해 SQL Server 로그인 시 연결하지 못했습니다.|데이터베이스 자체가 잘못되었거나 로그인에 데이터베이스에 대한 CONNECT 권한이 부족합니다.|ALTER LOGIN을 사용하여 기본 데이터베이스를 master database로 설정합니다.<br/>SQL Server 인스턴스 및 데이터베이스 검색을 위해 어플라이언스에서 제공한 자격 증명/계정에 sysadmin 역할을 부여합니다. [자세한 정보](/sql/relational-databases/errors-events/mssqlserver-4064-database-engine-error)| [보기](/sql/relational-databases/errors-events/mssqlserver-4064-database-engine-error) |
|30006: SQL Server 로그인을 Windows 인증에 사용할 수 없습니다.|1. 로그인은 SQL Server 로그인일 수 있지만 서버는 Windows 인증만 허용합니다.<br/>2. SQL Server 인증을 사용하여 연결하려고 하지만 사용된 로그인이 SQL Server에 없습니다.<br/>3. 로그인에서 Windows 인증을 사용할 수 있지만 로그인은 인식할 수 없는 Windows 보안 주체입니다. 인식할 수 없는 Windows 보안 주체란 로그인을 Windows에서 확인할 수 없음을 의미합니다. 이는 Windows 로그인이 트러스트되지 않은 도메인에서 온 것이기 때문일 수 있습니다.|SQL Server 인증을 사용하여 연결을 시도하는 경우 SQL Server이 혼합 인증 모드로 구성되어 있고, SQL Server 로그인이 있는지 확인합니다.<br/>Windows 인증을 사용하여 연결하려고 하는 경우 올바른 도메인에 제대로 로그인되어 있는지 확인합니다. [자세한 정보](/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error)| [보기](/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error) |
|30007: 암호 만료됨|계정의 암호가 만료되었습니다.|SQL Server 로그인 암호가 만료되었을 수 있으므로 암호를 재설정하거나 암호 만료 날짜를 연장하세요. [자세한 정보](/sql/relational-databases/native-client/features/changing-passwords-programmatically)| [보기](/sql/relational-databases/native-client/features/changing-passwords-programmatically) |
|30008: 암호를 변경해야 합니다.|계정의 암호를 변경해야 합니다.|SQL Server 검색을 위해 제공된 자격 증명의 암호를 변경합니다. [자세한 정보](/previous-versions/sql/sql-server-2008-r2/cc645934(v=sql.105))| [보기](/previous-versions/sql/sql-server-2008-r2/cc645934(v=sql.105)) |
|30009: 내부 오류가 발생했습니다.|SQL Server 인스턴스 및 데이터베이스를 검색하는 중 내부 오류가 발생했습니다. |문제가 계속되면 Microsoft 지원에 문의하세요.| - |
|30010: 데이터베이스를 찾을 수 없음|선택한 서버 인스턴스에서 데이터베이스를 찾을 수 없습니다.|SQL 데이터베이스 검색을 위해 어플라이언스에서 제공한 자격 증명/계정에 sysadmin 역할을 부여합니다.| - |
|30011: SQL 인스턴스 또는 데이터베이스를 평가하는 중 내부 오류가 발생했습니다.|평가를 수행하는 중 내부 오류가 발생했습니다.|문제가 계속되면 Microsoft 지원에 문의하세요.| - |
|30012: SQL 연결에 실패했습니다.|1. 서버의 방화벽에서 연결을 거부했습니다.<br/>2. SQL Browser 서비스(sqlbrowser)가 시작되지 않았습니다.<br/>3. 아마도 서버가 시작되지 않았기 때문에 SQL 서버가 클라이언트 요청에 응답하지 않았습니다.<br/>4. SQL Server 클라이언트가 서버에 연결할 수 없습니다. 서버가 원격 연결을 허용하도록 구성되어 있지 않기 때문에 이 오류가 발생했을 수 있습니다.<br/>5. SQL Server 클라이언트가 서버에 연결할 수 없습니다. 클라이언트가 서버 이름을 확인할 수 없거나 서버 이름이 잘못되었기 때문에 이 오류가 발생할 수 있습니다.<br/>6. TCP, 즉 명명된 파이프 프로토콜이 활성화되어 있지 않습니다.<br/>7. 지정된 SQL Server 인스턴스 이름이 잘못되었습니다.|[이것](https://go.microsoft.com/fwlink/?linkid=2153317) 연결 문제를 해결하기 위한 대화형 사용자 가이드를 사용하세요. 서비스에서 업데이트하는 데이터에 대한 가이드를 따라 24시간 동안 기다리세요. 문제가 여전히 지속되면 Microsoft 지원 서비스에 문의하세요.| [보기](https://go.microsoft.com/fwlink/?linkid=2153317) |
|30013: SQL Server 인스턴스에 연결을 수립하는 중 오류가 발생했습니다.|1. 어플라이언스에서 SQL Server의 이름을 확인할 수 없습니다.<br/>2. SQL Server가 원격 연결을 허용하지 않습니다.|어플라이언스에서 SQL server를 ping할 수 있는 경우 24시간 동안 기다린 후 이 문제가 자동으로 해결되는지 확인하세요. 그렇지 않은 경우 Microsoft 지원에 문의하세요. [자세한 정보](/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error)| [보기](/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error) |
|30014: 사용자 이름 또는 암호가 잘못되었습니다.| 이 오류는 잘못된 암호나 사용자 이름과 관련된 인증 오류로 인해 발생할 수 있습니다.|유효한 사용자 이름 및 암호를 사용하는 자격 증명을 제공하세요. [자세한 정보](/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error)| [보기](/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error) |
|30015: SQL 인스턴스를 검색하는 중 내부 오류가 발생했습니다.|SQL 인스턴스를 검색하는 중 내부 오류가 발생했습니다.|문제가 계속되면 Microsoft 지원에 문의하세요.| - |
|30016: 시간 제한으로 인해 '%Instance;' 인스턴스에 연결하지 못했습니다.| 이 오류는 서버의 방화벽에서 연결을 거부하는 경우에 발생할 수 있습니다.|SQL Server의 방화벽이 연결을 허용하도록 구성되어 있는지 확인합니다. 문제가 지속되면 Microsoft 지원에 문의하세요. [자세한 정보](/sql/relational-databases/errors-events/mssqlserver-neg2-database-engine-error)| [보기](/sql/relational-databases/errors-events/mssqlserver-neg2-database-engine-error) |
|30017: 내부 오류 발생.|처리되지 않은 예외가 발생했습니다.|문제가 계속되면 Microsoft 지원에 문의하세요.| - |
|30018: 내부 오류 발생.|Temp DB 크기, 파일 크기 등의 SQL 인스턴스 데이터를 수집하는 중 내부 오류가 발생했습니다.|24시간을 기다렸다가 문제가 계속되면 Microsoft 지원에 문의하세요.| - |
|30019: 내부 오류가 발생.|데이터베이스 또는 인스턴스의 메모리 사용률 등의 성능 메트릭을 수집하는 중 내부 오류가 발생했습니다.|24시간을 기다렸다가 문제가 계속되면 Microsoft 지원에 문의하세요.| - |

## <a name="common-web-apps-discovery-errors"></a>일반적인 웹앱 검색 오류

Azure Migrate에서는 Azure Migrate 검색 및 평가를 사용하여 온-프레미스 머신에서 실행되는 ASP.NET 웹앱의 검색을 지원합니다. 웹앱 검색은 현재 VMware에만 지원됩니다. [검색](tutorial-discover-vmware.md) 지침서를 참조하세요.

일반적인 웹앱 검색 오류는 표에 요약되어 있습니다.

| **오류** | **원인** | **동작** |
|--|--|--|
| **40001:** IIS 관리 콘솔 기능을 사용할 수 없습니다. | IIS 웹앱 검색에서는 IIS의 로컬 버전에 포함된 관리 API를 사용하여 IIS 구성을 읽습니다. 이 API는 IIS의 IIS 관리 콘솔 기능을 사용하도록 설정한 경우에 사용할 수 있습니다. 이 기능을 사용할 수 없거나 OS가 IIS 웹앱 검색을 지원하는 버전이 아닙니다.| IIS 관리 콘솔 기능(관리 도구의 일부)을 포함하는 웹 서버(IIS) 역할이 사용으로 설정되고 서버 OS가 Windows Server 2008 R2 이상 버전인지 확인합니다.|
| **40002:** 어플라이언스에서 서버에 연결할 수 없습니다. | 잘못된 로그인 자격 증명 또는 사용할 수 없는 머신 때문에 서버에 연결하지 못했습니다. | 서버용으로 제공된 로그인 자격 증명이 올바르고 서버가 온라인 상태이며 WS-Management PowerShell 원격 연결을 수락하는지 확인합니다. |
| **40003:** 잘못된 자격 증명 때문에 서버에 연결할 수 없습니다. | 잘못된 로그인 자격 증명 때문에 서버에 연결하지 못했습니다. | 서버용으로 제공된 로그인 자격 증명이 올바른지와 WS-Management PowerShell 원격 기능을 사용할 수 있는지 확인합니다. |
| **40004:** IIS 구성에 액세스할 수 없습니다. | 권한이 없거나 부족합니다. | 서버에 제공된 사용자 자격 증명이 관리자 수준 자격 증명이며 사용자가 IIS 디렉터리(%windir%\System32\inetsrv)와 IIS 서버 구성 디렉터리(%windir%\System32\inetsrv\config)의 파일에 액세스할 권한이 있는지 확인합니다. |
| **40005:** IIS 검색을 완료할 수 없습니다. | VM에서 검색을 완료하지 못했습니다. 서버의 구성에 액세스하는 데 문제가 있기 때문일 수 있습니다. 오류: ‘%detailedMessage;’. | 서버에 제공된 사용자 자격 증명이 관리자 수준 자격 증명이며 사용자가 IIS 디렉터리(%windir%\System32\inetsrv)와 IIS 서버 구성 디렉터리(%windir%\System32\inetsrv\config)의 파일에 액세스할 권한이 있는지 확인합니다. 그런 다음 오류 세부 정보를 보유한 상태로 고객 지원팀에 문의하세요. |
| **40006:** 분류되지 않은 예외입니다. | 새 오류 시나리오. | 오류 세부 정보와 로그를 보유한 상태로 Microsoft 지원에 문의하세요. 로그는 C:\ProgramData\Microsoft Azure\Logs 경로의 어플라이언스 서버에 있습니다. |
| **40007:** 웹 서버의 웹앱을 찾을 수 없습니다. | 웹 서버에는 호스팅된 애플리케이션이 없습니다. | 웹 서버 구성을 확인합니다. |

## <a name="next-steps"></a>다음 단계

[VMware](how-to-set-up-appliance-vmware.md), [Hyper-V](how-to-set-up-appliance-hyper-v.md) 또는 [물리적 서버](how-to-set-up-appliance-physical.md)에 대해 어플라이언스를 설정합니다.