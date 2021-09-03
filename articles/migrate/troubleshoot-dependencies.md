---
title: 에이전트 기반 및 에이전트 없는 종속성 분석 문제 해결
description: Azure Migrate의 종속성 시각화에 대한 도움을 받으세요.
author: Vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: troubleshooting
ms.date: 07/01/2020
ms.openlocfilehash: 7e618052a71f73c5559860fa09b495e9be2a14d9
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114290980"
---
# <a name="troubleshoot-dependency-visualization"></a>종속성 시각화 문제 해결

이 문서는 에이전트 기반 및 에이전트 없는 종속성 분석 _(VMware 서버에서만 사용 가능)_ 문제를 해결하는 데 도움이 됩니다. Azure Migrate에서 지원되는 종속성 시각화 유형에 대해 [자세히 알아봅니다](concepts-dependency-visualization.md).


## <a name="visualize-dependencies-for--1-hour-with-agentless-dependency-analysis"></a>에이전트 없는 종속성 분석으로 1시간 이상 종속성 시각화

에이전트 없는 종속성 분석을 사용하면 최대 30일 동안 맵에서 종속성을 시각화하거나 내보낼 수 있습니다.

## <a name="visualized-dependencies-for--10-servers-with-agentless-dependency-analysis"></a>에이전트 없는 종속성 분석을 사용하는 10대 이상의 서버에 대한 시각화된 종속성

Azure Migrate는 여러 서버의 네트워크 연결을 한 번에 시각화하고 프로세스 및 서버를 기준으로 필터링하는 데 사용할 수 있는 Power BI 템플릿을 제공합니다. 여러 서버의 종속성을 함께 시각화하는 방법에 대해 [자세히 알아봅니다](how-to-create-group-machine-dependencies-agentless.md#visualize-network-connections-in-power-bi).

## <a name="dependencies-export-csv-shows-unknown-process-with-agentless-dependency-analysis"></a>종속성 내보내기 CSV에 에이전트 없는 종속성 분석이 포함된 "알 수 없는 프로세스"가 표시됨
에이전트 없는 종속성 분석에서 최선의 방식으로 프로세스 이름이 캡처됩니다. 특정 시나리오에서는 원본 및 대상 서버 이름과 대상 포트가 캡처되지만 종속성의 양쪽 끝에서 프로세스 이름을 확인하는 것은 불가능합니다. 이러한 경우 프로세스는 "_알 수 없는 프로세스_"로 표시됩니다.

## <a name="common-agentless-dependency-analysis-errors"></a>일반적인 에이전트 없는 종속성 분석 오류

Azure Migrate는 Azure Migrate: 검색 및 평가를 사용하여 에이전트 없는 종속성 분석을 지원합니다. 에이전트 없는 종속성 분석은 현재 VMware에서만 지원됩니다. 에이전트 없는 종속성 분석의 요구 사항에 대해 [자세히 알아봅니다](how-to-create-group-machine-dependencies-agentless.md).

에이전트 없는 종속성 분석 오류 목록은 아래 표에 요약되어 있습니다.

> [!Note]
> 필요한 데이터를 수집하기 위해 에이전트 없는 종속성 분석과 동일한 방법론을 따르기 때문에 소프트웨어 인벤토리에서도 동일한 오류가 발생할 수 있습니다.

| **오류** | **원인** | **동작** |
|--|--|--|
| **9000:** 서버의 VMware 도구 상태를 감지할 수 없습니다. | VMware 도구가 서버에 설치되어 있지 않거나 설치된 버전이 손상되었을 수 있습니다. | 버전 10.2.1 이상의 VMware 도구가 서버에 설치되어 실행 중인지 확인합니다. |
| **9001:** VMware 도구가 서버에 설치되지 않았습니다. | VMware 도구가 서버에 설치되어 있지 않거나 설치된 버전이 손상되었을 수 있습니다. | 버전 10.2.1 이상의 VMware 도구가 서버에 설치되어 실행 중인지 확인합니다. |
| **9002:** VMware 도구가 서버에서 실행되고 있지 않습니다. | VMware 도구가 서버에 설치되어 있지 않거나 설치된 버전이 손상되었을 수 있습니다. | 버전 10.2.0 이상의 VMware 도구가 서버에 설치되어 실행 중인지 확인합니다. |
| **9003:** 서버에서 실행 중인 운영 체제 유형은 지원되지 않습니다. | 서버에서 실행 중인 운영체제가 Windows나 Linux가 아닙니다. | Windows 및 Linux OS 유형만 지원됩니다. 서버가 실제 실행 중인 Windows 또는 Linux OS인 경우 vCenter Server에 지정된 운영 체제 유형을 확인합니다. |
| **9004:** 서버가 실행 중인 상태가 아닙니다. | 서버가 전원이 꺼진 상태입니다. | 서버가 실행 중인 상태인지 확인합니다. |
| **9005:** 서버에서 실행 중인 운영 체제 유형은 지원되지 않습니다. | 서버에서 실행 중인 운영체제가 Windows나 Linux가 아닙니다. | Windows 및 Linux OS 유형만 지원됩니다. \<FetchedParameter> 운영 체제는 현재 지원되지 않습니다. |
| **9006:** 서버에서 검색 메타데이터 파일을 다운로드하는 데 필요한 URL이 비어 있습니다. | 어플라이언스의 검색 에이전트가 예상대로 작동하지 않아 생긴 일시적인 문제일 수 있습니다. | 문제는 24시간 이내에 다음 주기에서 자동으로 해결됩니다. 문제가 지속되면 Microsoft 지원 사례를 제출하세요. |
| **9007:** 메타데이터를 수집하기 위해 스크립트를 실행하는 프로세스를 서버에서 찾을 수 없습니다. | 어플라이언스의 검색 에이전트가 예상대로 작동하지 않아 생긴 일시적인 문제일 수 있습니다. | 문제는 24시간 이내에 다음 주기에서 자동으로 해결됩니다. 문제가 지속되면 Microsoft 지원 사례를 제출하세요. |
| **9008:** 메타데이터를 수집하기 위해 서버에서 실행 중인 프로세스의 상태를 검색할 수 없습니다. | 내부 오류로 인한 일시적인 문제일 수 있습니다. | 문제는 24시간 이내에 다음 주기에서 자동으로 해결됩니다. 문제가 지속되면 Microsoft 지원 사례를 제출하세요. |
| **9009:** Window UAC(사용자 계정 컨트롤)가 서버에서 검색 작업을 실행하지 못하게 합니다. | Windows UAC(사용자 계정 컨트롤) 설정이 서버에서 설치된 애플리케이션의 검색을 제한하고 있습니다. | 영향을 받는 서버에서 제어판의 '사용자 계정 컨트롤' 설정 수준을 낮추세요. |
| **9010:** 서버 전원이 꺼져 있습니다. | 서버가 전원이 꺼진 상태입니다. | 서버의 전원이 켜진 상태인지 확인합니다. |
| **9011:** 검색된 메타데이터가 포함된 파일을 서버에서 찾을 수 없습니다. | 내부 오류로 인한 일시적인 문제일 수 있습니다. | 문제는 24시간 이내에 다음 주기에서 자동으로 해결됩니다. 문제가 지속되면 Microsoft 지원 사례를 제출하세요. |
| **9012:** 서버에서 검색된 메타데이터가 포함된 파일이 비어 있습니다. | 내부 오류로 인한 일시적인 문제일 수 있습니다. | 문제는 24시간 이내에 다음 주기에서 자동으로 해결됩니다. 문제가 지속되면 Microsoft 지원 사례를 제출하세요. |
| **9013:** 서버에 로그인할 때마다 새로운 임시 사용자 프로필이 만들어집니다. | 서버에 로그인할 때마다 새로운 임시 사용자 프로필이 만들어집니다. | 이 문제를 해결하는 데 도움이 되도록 Microsoft 지원 사례를 제출합니다. |
| **9014:** ESXi 호스트에서 발생한 오류로 인해 검색된 메타데이터가 포함된 파일을 검색할 수 없습니다.  오류 코드: %ErrorCode; 세부 정보: %ErrorMessage | ESXi 호스트 \<HostName>에서 오류가 발생했습니다.  오류 코드: %ErrorCode; 세부 정보: %ErrorMessage | 서버가 실행 중인 ESXi 호스트에서 포트 443이 열려 있는지 확인합니다.<br/><br/> 문제 해결 방법에 대해 [자세히 알아봅니다](troubleshoot-dependencies.md#error-9014-httpgetrequesttoretrievefilefailed).|
| **9015:** 서버 검색을 위해 제공된 vCenter Server 사용자 계정에 게스트 작업 권한이 사용하도록 설정되어 있지 않습니다. | 게스트 작업의 필수 권한이 vCenter Server 사용자 계정에서 사용하도록 설정되지 않았습니다. | 서버와 상호 작용하고 필요한 데이터를 끌어오기 위해 vCenter Server 사용자 계정에 Virtual Machines > 게스트 작업에 사용할 수 있는 권한이 있는지 확인합니다. <br/><br/> 필요한 권한으로 vCenter Server 계정을 설정하는 방법에 대해 [자세히 알아봅니다](tutorial-discover-vmware.md#prepare-vmware). |
| **9016:** 서버의 게스트 작업 에이전트가 오래되어 메타데이터를 검색할 수 없습니다. | VMware 도구가 서버에 설치되어 있지 않거나 설치된 버전이 최신 버전이 아닙니다. | VMware 도구가 서버에 설치되어 있고 최신 상태로 실행 중인지 확인합니다. VMware Tools 버전은 버전 10.2.1 이상이어야 합니다. |
| **9017:** 검색된 메타데이터가 포함된 파일을 서버에서 찾을 수 없습니다. | 내부 오류로 인한 일시적인 문제일 수 있습니다. | 이 문제를 해결하는 데 도움이 되도록 Microsoft 지원 사례를 제출합니다. |
| **9018:** PowerShell이 서버에 설치되어 있지 않습니다. | 서버에서 PowerShell을 찾을 수 없습니다. | 서버에 PowerShell 버전 2.0 이상이 설치되어 있는지 확인합니다. <br/><br/> 문제 해결 방법에 대해 [자세히 알아봅니다](troubleshoot-dependencies.md#error-9018-powershellnotfound).|
| **9019:** 서버에서 게스트 작업 실패로 인해 메타데이터를 검색할 수 없습니다. | VMware 게스트 작업이 서버에서 실패했습니다. 서버에서 다음 자격 증명을 시도할 때 문제가 발생했습니다. <FriendlyNameOfCredentials>. | 어플라이언스에 제공된 서버 자격 증명이 유효하고 자격 증명에 제공된 사용자 이름이 UPN 형식인지 확인합니다. (가능한 원인에서 Azure Migrate가 시도한 자격 증명의 친숙한 이름 찾기) |
| **9020:** 서버에서 검색된 메타데이터를 포함하는 데 필요한 파일을 만들 수 없습니다. | 어플라이언스 또는 온-프레미스 그룹 정책에 제공된 자격 증명과 연결된 역할이 필수 폴더에서의 파일 생성을 제한하고 있습니다. 서버에서 다음 자격 증명을 시도할 때 문제가 발생했습니다. <FriendlyNameOfCredentials>. | 1. 어플라이언스에 제공된 자격 증명에 서버의 \<folder path/folder name> 폴더에 대한 파일 만들기 권한이 있는지 확인합니다. <br/>2. 어플라이언스에 제공된 자격 증명에 필요한 권한이 없는 경우 다른 자격 증명 집합을 제공하거나 기존 자격 증명을 편집합니다. (가능한 원인에서 Azure Migrate가 시도한 자격 증명의 친숙한 이름 찾기) |
| **9021:** 서버의 올바른 경로에서 검색된 메타데이터를 포함하는 데 필요한 파일을 만들 수 없습니다. | VMware 도구에서 파일을 만들기 위한 잘못된 파일 경로를 보고합니다. | 버전 10.2.0 이상의 VMware 도구가 서버에 설치되어 실행 중인지 확인합니다. |
| **9022:** 서버에서 Get-WmiObject cmdlet을 실행하기 위한 액세스가 거부되었습니다. | 어플라이언스 또는 온-프레미스 그룹 정책에 제공된 자격 증명과 연결된 역할이 WMI 개체에 대한 액세스를 제한하고 있습니다. 서버에서 다음 자격 증명을 시도할 때 문제가 발생했습니다. \<FriendlyNameOfCredentials>. | 1. 어플라이언스에 제공된 자격 증명에 파일 만들기 관리자 권한이 있고 WMI가 사용하도록 설정되어 있는지 확인합니다. <br/> 2. 어플라이언스에 제공된 자격 증명에 필요한 권한이 없는 경우 다른 자격 증명 집합을 제공하거나 기존 자격 증명을 편집합니다. (가능한 원인에서 Azure Migrate가 시도한 자격 증명의 친숙한 이름을 찾습니다).<br/><br/> 문제 해결 방법에 대해 [자세히 알아봅니다](troubleshoot-dependencies.md#error-9022-getwmiobjectaccessdenied).|
| **9023:** %SystemRoot% 환경 변수 값이 비어 있으므로 PowerShell을 실행할 수 없습니다. | 서버에 대한 %SystemRoot% 환경 변수의 값이 비어 있습니다. | 1. 영향을 받는 서버에서 echo %systemroot% 명령을 실행하여 환경 변수가 빈 값을 반환하는지 확인합니다. <br/> 2. 문제가 지속되면 Microsoft 지원 사례를 제출합니다. |
| **9024:** %TEMP% 환경 변수 값이 비어 있으므로 검색을 수행할 수 없습니다. | 서버에 대한 %TEMP% 환경 변수 값이 비어 있습니다. | 1. 영향을 받는 서버에서 echo %temp% 명령을 실행하여 환경 변수가 빈 값을 반환하는지 확인합니다. <br/> 2. 문제가 지속되면 Microsoft 지원 사례를 제출합니다. |
| **9025:** 검색을 수행할 수 없습니다. PowerShell이 서버에서 손상되었습니다. | PowerShell이 서버에서 손상되었습니다. | PowerShell을 다시 설치하고 영향을 받는 서버에서 실행 중인지 확인합니다. |
| **9026:** 서버에서 게스트 작업을 실행할 수 없습니다. | 서버의 현재 상태로 인해 게스트 작업을 실행할 수 없습니다. | 1. 영향을 받는 서버가 가동되어 실행 중인지 확인합니다.<br/> 2. 문제가 지속되면 Microsoft 지원 사례를 제출합니다. |
| **9027:** 게스트 작업 에이전트가 서버에서 실행되고 있지 않으므로 메타데이터를 검색할 수 없습니다. | 서버의 게스트 작업 에이전트에 연결할 수 없습니다. | 버전 10.2.0 이상의 VMware 도구가 서버에 설치되어 실행 중인지 확인합니다. |
| **9028:** 서버의 스토리지가 부족하여 검색된 메타데이터를 포함하는 데 필요한 파일을 만들 수 없습니다. | 서버 디스크에 충분한 스토리지 공간이 없습니다. | 영향을 받는 서버의 디스크 스토리지에 사용 가능한 공간이 충분한지 확인합니다. |
| **9029:** 어플라이언스에 제공된 로그인 정보에 PowerShell을 실행할 수 있는 액세스 권한이 없습니다. | 어플라이언스에 제공된 자격 증명에는 PowerShell을 실행할 수 있는 액세스 권한이 없습니다. 서버에서 다음 자격 증명을 시도할 때 문제가 발생했습니다. \<FriendlyNameOfCredentials>. | 1. 어플라이언스에 제공된 자격 증명이 서버의 PowerShell에 액세스할 수 있는지 확인합니다.<br/> 2. 어플라이언스에 제공된 자격 증명에 필요한 액세스 권한이 없는 경우 다른 자격 증명 집합을 제공하거나 기존 자격 증명을 편집합니다. (가능한 원인에서 Azure Migrate가 시도한 자격 증명의 친숙한 이름 찾기) |
| **9030:** 서버가 호스팅되는 ESXi 호스트가 연결이 끊긴 상태이므로 검색된 메타데이터를 수집할 수 없습니다. | 서버가 상주하는 ESXi 호스트는 연결이 끊긴 상태입니다. | 서버를 실행하는 ESXi 호스트가 연결된 상태인지 확인합니다. |
| **9031:** 서버가 호스팅되는 ESXi 호스트가 응답하지 않아 검색된 메타데이터를 수집할 수 없습니다. | 서버가 있는 ESXi 호스트가 잘못된 상태에 있습니다. | 서버를 실행하는 ESXi 호스트가 실행 및 연결 상태인지 확인합니다. |
| **9032:** 내부 오류가 발생하여 검색할 수 없습니다. | 발생한 문제는 내부 오류로 인한 것입니다. | [여기](troubleshoot-dependencies.md#error-9032-invalidrequest)의 단계에 따라 문제를 해결하세요. 문제가 지속되면 Microsoft 지원 사례를 제출하세요. |
| **9033:** 서버용 어플라이언스에 제공된 자격 증명의 사용자 이름에 잘못된 문자가 있으므로 검색할 수 없습니다. | 어플라이언스에 제공된 자격 증명의 사용자 이름에 잘못된 문자가 있습니다. 서버에서 다음 자격 증명을 시도할 때 문제가 발생했습니다. \<FriendlyNameOfCredentials>. | 어플라이언스에 제공된 자격 증명의 사용자 이름에 잘못된 문자가 없는지 확인합니다. 어플라이언스 구성 관리자로 돌아가서 자격 증명을 편집할 수 있습니다. (가능한 원인에서 Azure Migrate가 시도한 자격 증명의 친숙한 이름을 찾습니다). |
| **9034:** 서버용 어플라이언스에 제공된 자격 증명의 사용자 이름이 UPN 형식이 아니기 때문에 검색할 수 없습니다. | 어플라이언스에 제공된 자격 증명에 UPN 형식의 사용자 이름이 없습니다. 서버에서 다음 자격 증명을 시도할 때 문제가 발생했습니다. \<FriendlyNameOfCredentials>. | 어플라이언스에 제공된 자격 증명의 사용자 이름이 UPN(사용자 계정 이름) 형식인지 확인합니다. 어플라이언스 구성 관리자로 돌아가서 자격 증명을 편집할 수 있습니다. (가능한 원인에서 Azure Migrate가 시도한 자격 증명의 친숙한 이름을 찾습니다). |
| **9035:** 올바르게 설정되지 않은 PowerShell 언어 모드로 검색할 수 없습니다. | PowerShell 언어 모드가 '전체 언어'로 설정되지 않았습니다. | PowerShell 언어 모드가 '전체 언어'로 설정되어 있는지 확인하세요. |
| **9036:** 서버용 어플라이언스에 제공된 자격 증명의 사용자 이름이 UPN 형식이 아니기 때문에 검색할 수 없습니다. | 어플라이언스에 제공된 자격 증명에 UPN 형식의 사용자 이름이 없습니다. 서버에서 다음 자격 증명을 시도할 때 문제가 발생했습니다. \<FriendlyNameOfCredentials>. | 어플라이언스에 제공된 자격 증명의 사용자 이름이 UPN(사용자 계정 이름) 형식인지 확인합니다. 어플라이언스 구성 관리자로 돌아가서 자격 증명을 편집할 수 있습니다. (가능한 원인에서 Azure Migrate가 시도한 자격 증명의 친숙한 이름을 찾습니다). |
| **9037:** 서버의 긴 응답 시간으로 인해 메타데이터 수집이 일시적으로 일시 중지되었습니다. | 서버가 응답하는 데 너무 오래 걸립니다. | 문제는 24시간 이내에 다음 주기에서 자동으로 해결됩니다. 문제가 지속되면 Microsoft 지원 사례를 제출하세요. |
| **10000:** 서버에서 실행 중인 운영 체제 유형은 지원되지 않습니다. | 서버에서 실행 중인 운영체제가 Windows나 Linux가 아닙니다. | Windows 및 Linux OS 유형만 지원됩니다. \<GuestOSName> 운영 체제는 현재 지원되지 않습니다. |
| **10001:** 검색 메타데이터를 수집하는 데 필요한 스크립트를 서버에서 찾을 수 없습니다. | 검색을 수행하는 데 필요한 스크립트가 삭제되었거나 예상 위치에서 제거되었을 수 있습니다. | 이 문제를 해결하는 데 도움이 되도록 Microsoft 지원 사례를 제출합니다. |
| **10002:** 서버에서 검색 작업 시간이 초과되었습니다. | 어플라이언스의 검색 에이전트가 예상대로 작동하지 않아 생긴 일시적인 문제일 수 있습니다. | 문제는 24시간 이내에 다음 주기에서 자동으로 해결됩니다. 문제가 해결되지 않으면 [여기](troubleshoot-dependencies.md#error-10002-scriptexecutiontimedoutonvm)에 있는 단계에 따라 문제를 해결하세요. 문제가 계속되면 Microsoft 지원 사례를 제출하세요.|
| **10003:** 검색 작업을 실행하는 프로세스가 오류와 함께 종료되었습니다. | 검색 작업을 실행하는 프로세스가 오류로 인해 갑자기 종료되었습니다.| 문제는 24시간 이내에 다음 주기에서 자동으로 해결됩니다. 문제가 지속되면 Microsoft 지원 사례를 제출하세요. |
| **10004:** 어플라이언스에서 서버 OS 유형에 대한 자격 증명이 제공되지 않았습니다. | 서버 OS 유형에 대한 자격 증명이 어플라이언스에 추가되지 않았습니다. | 1. 어플라이언스에서 영향을 받는 서버의 OS 유형에 대한 자격 증명을 추가했는지 확인합니다.<br/> 2. 이제 어플라이언스에 여러 서버 자격 증명을 추가할 수 있습니다. |
| **10005:** 서버용 어플라이언스에 제공된 자격 증명이 잘못되었습니다. | 어플라이언스에 제공된 자격 증명이 유효하지 않습니다. 서버에서 다음 자격 증명을 시도할 때 문제가 발생했습니다. \<FriendlyNameOfCredentials>. | 1. 어플라이언스에서 제공된 자격 증명이 유효하고 자격 증명을 사용하여 서버에 액세스할 수 있는지 확인합니다.<br/> 2. 이제 어플라이언스에 여러 서버 자격 증명을 추가할 수 있습니다.<br/> 3. 어플라이언스 구성 관리자로 돌아가서 다른 자격 증명 세트를 제공하거나 기존 자격 증명을 편집합니다. (가능한 원인에서 Azure Migrate가 시도한 자격 증명의 친숙한 이름을 찾습니다). <br/><br/> 문제 해결 방법에 대해 [자세히 알아봅니다](troubleshoot-dependencies.md#error-10005-guestcredentialnotvalid).|
| **10006:** 서버에서 실행 중인 운영 체제 유형은 지원되지 않습니다. | 서버에서 실행 중인 운영체제가 Windows나 Linux가 아닙니다. | Windows 및 Linux OS 유형만 지원됩니다. \<GuestOSName> 운영 체제는 현재 지원되지 않습니다. |
| **10007:** 서버에서 검색된 메타데이터를 처리할 수 없습니다. | 검색된 메타데이터가 포함된 파일의 내용을 구문 분석하는 동안 오류가 발생했습니다. | 이 문제를 해결하는 데 도움이 되도록 Microsoft 지원 사례를 제출합니다. |
| **10008:** 서버에서 검색된 메타데이터를 포함하는 데 필요한 파일을 만들 수 없습니다. | 어플라이언스 또는 온-프레미스 그룹 정책에 제공된 자격 증명과 연결된 역할이 필수 폴더에서의 파일 생성을 제한하고 있습니다. 서버에서 다음 자격 증명을 시도할 때 문제가 발생했습니다. <FriendlyNameOfCredentials>. | 1. 어플라이언스에 제공된 자격 증명에 서버의 \<folder path/folder name> 폴더에 대한 파일 만들기 권한이 있는지 확인합니다.<br/> 2. 어플라이언스에 제공된 자격 증명에 필요한 권한이 없는 경우 다른 자격 증명 집합을 제공하거나 기존 자격 증명을 편집합니다. (가능한 원인에서 Azure Migrate가 시도한 자격 증명의 친숙한 이름 찾기) |
| **10009:** 검색된 메타데이터를 서버의 파일에 쓸 수 없습니다. | 어플라이언스 또는 온-프레미스 그룹 정책에 제공된 자격 증명과 연결된 역할이 서버의 파일 쓰기를 제한하고 있습니다. 서버에서 다음 자격 증명을 시도할 때 문제가 발생했습니다. \<FriendlyNameOfCredentials>. | 1. 어플라이언스에 제공된 자격 증명이 폴더 <폴더 경로/폴더 이름> 서버에서 쓰기 권한을 갖고 있는지 확인합니다.<br/> 2. 어플라이언스에 제공된 자격 증명에 필요한 권한이 없는 경우 다른 자격 증명 집합을 제공하거나 기존 자격 증명을 편집합니다. (가능한 원인에서 Azure Migrate가 시도한 자격 증명의 친숙한 이름 찾기) |
| **10010:** 일부 메타데이터를 수집하는 데 필요한 %CommandName; 명령이 서버에 없으므로 검색할 수 없습니다. | %CommandName; 명령을 포함하는 패키지가 서버에 설치되어 있지 않습니다. | %CommandName; 명령을 포함하는 패키지가 서버에 설치되어 있는지 확인합니다. |
| **10011:** 어플라이언스에 제공된 로그인 정보는 대화형 세션에 대한 로그인 및 로그오프에 사용되었습니다. | 대화형 로그인 및 로그오프는 사용 중인 계정 프로필에서 레지스트리 키를 강제로 언로드합니다. 이 조건은 키를 나중에 사용할 수 없게 만듭니다. | [여기](/sharepoint/troubleshoot/administration/800703fa-illegal-operation-error#resolutionus/sharepoint/troubleshoot/administration/800703fa-illegal-operation-error)에 설명된 해결 방법을 사용합니다. |
| **10012:** 서버용 어플라이언스에 자격 증명이 제공되지 않았습니다. | 서버에 대한 자격 증명이 제공되지 않았거나 어플라이언스에서 잘못된 도메인 이름으로 도메인 자격 증명을 제공했습니다. 이 오류의 원인에 대해 [자세히 알아봅니다](troubleshoot-dependencies.md#error-10012-credentialnotprovided). | 1. 자격 증명이 어플라이언스에 서버에 제공되고 자격 증명을 사용하여 서버에 액세스할 수 있는지 확인합니다. <br/> 2. 이제 서버용 어플라이언스에 여러 자격 증명을 추가할 수 있습니다. 어플라이언스 구성 관리자로 돌아가서 서버에 대한 자격 증명을 제공합니다.|


## <a name="error-970-dependencymapinsufficientprivilegesexception"></a>오류 970: DependencyMapInsufficientPrivilegesException

### <a name="cause"></a>원인
이 오류는 일반적으로 어플라이언스에 필요한 권한이 있는 자격 증명을 제공하지 않은 경우 Linux 서버에 발생합니다.

### <a name="remediation"></a>수정
- 루트 사용자 계정을 제공했는지 확인하거나
- /bin/netstat 및 /bin/ls 파일에 대한 다음 권한이 있는 계정을 제공했는지 확인합니다:
   - CAP_DAC_READ_SEARCH
   - CAP_SYS_PTRACE
- 어플라이언스에 제공된 사용자 계정에 필요한 권한이 있는지 확인하려면 다음 단계를 수행합니다.
1. 오류 메시지에 언급된 것과 동일한 사용자 계정으로 이 오류가 발생한 서버에 로그인합니다.
2. Shell에서 다음 명령을 실행합니다. 에이전트 없는 종속성 분석에 필요한 권한이 없으면 오류가 발생합니다.

   ````
   ps -o pid,cmd | grep -v ]$
   netstat -atnp | awk '{print $4,$5,$7}'
   ````
3. 다음 명령을 실행하여 /bin/netstat 및 /bin/ls 파일에 필요한 권한을 설정합니다.

   ````
   sudo setcap CAP_DAC_READ_SEARCH,CAP_SYS_PTRACE=ep /bin/ls
   sudo setcap CAP_DAC_READ_SEARCH,CAP_SYS_PTRACE=ep /bin/netstat
   ````
4. 위의 명령이 사용자 계정에 필요한 권한을 할당했는지 여부를 확인할 수 있습니다.

   ````
   getcap /usr/bin/ls
   getcap /usr/bin/netstat
   ````
5. 성공적인 출력을 얻으려면 2단계에서 제공된 명령을 다시 실행합니다.


## <a name="error-9014-httpgetrequesttoretrievefilefailed"></a>오류 9014: HTTPGetRequestToRetrieveFileFailed

### <a name="cause"></a>원인
이 문제는 어플라이언스의 VMware 검색 에이전트가 서버가 호스팅되는 ESXi 호스트를 통해 서버 파일 시스템에서 종속성 데이터가 포함된 출력 파일을 다운로드하려고 할 때 발생합니다.

### <a name="remediation"></a>수정
- 어플라이언스 서버에서 PowerShell을 열고 다음 명령을 실행하여 어플라이언스에서 443 포트 (종속성 데이터를 끌어오기 위해서는 ESXi 호스트에 열려 있어야 함)의 ESXi 호스트 _(오류 메시지에 제공된 이름)_ 에 대한 TCP 연결을 테스트할 수 있습니다.
    ````
    Test -NetConnection -ComputeName <Ip address of the ESXi host> -Port 443
    ````
- 명령이 성공적인 연결을 반환하면 Azure Migrate 프로젝트> 검색 및 평가>개요>관리>어플라이언스에서 어플라이언스 이름을 선택한 다음 **서비스 새로 고침** 을 선택합니다.

## <a name="error-9018-powershellnotfound"></a>오류 9018: PowerShellNotFound

### <a name="cause"></a>원인
이 오류는 일반적으로 Windows Server 2008 이하를 실행하는 서버에서 발생합니다.

### <a name="remediation"></a>수정
서버의 ($SYSTEMROOT)\System32\WindowsPowershell\v1.0\powershell.exe 위치에 필요한 PowerShell 버전(2.0 이상)을 설치해야 합니다. Windows Server에 PowerShell을 설치하는 방법에 대해 [자세히 알아봅니다](/powershell/scripting/windows-powershell/install/installing-windows-powershell).

필요한 PowerShell 버전을 설치한 후 [여기](troubleshoot-dependencies.md#mitigation-verification-using-vmware-powercli)에 있는 단계에 따라 오류가 해결되었는지 확인할 수 있습니다.

## <a name="error-9022-getwmiobjectaccessdenied"></a>오류 9022: GetWMIObjectAccessDenied

### <a name="remediation"></a>수정
어플라이언스에 제공된 사용자 계정에 WMI 네임스페이스 및 하위 네임스페이스에 대한 액세스 권한이 있는지 확인합니다. 다음 단계에 따라 액세스 권한을 설정할 수 있습니다.
1.  이 오류를 보고하는 서버로 이동합니다.
2.  검색하고 시작 메뉴에서 ‘실행’을 선택합니다. ‘실행’ 대화 상자에서 ‘열기:’ 텍스트 필드에 wmimgmt.msc를 입력하고 Enter 키를 누릅니다.
3.  열린 wmimgmt 콘솔의 왼쪽 패널에서 “WMI 컨트롤(로컬)”을 찾을 수 있습니다. 이를 마우스 오른쪽 단추로 클릭하고 메뉴에서 ‘속성’을 선택합니다.
4.  ‘WMI 컨트롤(로컬) 속성’ 대화 상자에서 ‘보안’ 탭을 선택합니다.
5.  보안 탭에서 '보안' 단추를 선택하면 ‘ROOT에 대한 보안’ 대화 상자가 열립니다.
7.  '고급' 단추를 선택하여 '루트에 대한 고급 보안 설정' 대화 상자를 엽니다. 
8.  '루트에 대한 권한 항목' 대화 상자를 여는 '추가' 단추를 선택합니다.
9.  ‘주체 선택’을 클릭하여 ‘사용자, 컴퓨터, 서비스 계정 또는 그룹 선택’ 대화 상자를 엽니다.
10. WMI에 대한 액세스 권한을 부여할 사용자 이름 또는 그룹을 선택하고 '확인'을 클릭합니다.
11. 실행 권한을 부여하고 '적용 대상:' 드롭다운에서 "이 네임스페이스 및 하위 네임스페이스"를 선택했는지 확인합니다.
12. '적용' 단추를 선택하여 설정을 저장하고 모든 대화 상자를 닫습니다.

필요한 액세스 권한을 얻은 후 [여기](troubleshoot-dependencies.md#mitigation-verification-using-vmware-powercli)의 단계에 따라 오류가 해결되었는지 확인할 수 있습니다.

## <a name="error-9032-invalidrequest"></a>오류 9032: InvalidRequest

### <a name="cause"></a>원인
이 문제에는 여러 가지 이유가 있을 수 있습니다. 그 이유 중 하나는 어플라이언스 구성 관리자에 제공된 사용자 이름(서버 자격 증명)에 잘못된 XML 문자가 있어 SOAP 요청을 구문 분석할 때 오류가 발생하기 때문입니다.

### <a name="remediation"></a>수정
- 서버 자격 증명의 사용자 이름에 잘못된 XML 문자가 없고 일반적으로 UPN 형식으로 알려진 username@domain.com 형식인지 확인합니다.
- 어플라이언스에서 로그인 정보를 수정한 후 [여기](troubleshoot-dependencies.md#mitigation-verification-using-vmware-powercli)의 단계에 따라 오류가 해결되었는지 확인할 수 있습니다.


## <a name="error-10002-scriptexecutiontimedoutonvm"></a>오류 10002: ScriptExecutionTimedOutOnVm

### <a name="cause"></a>원인
- 이 오류는 서버가 느리거나 응답하지 않고 종속성 데이터를 가져오기 위해 실행된 스크립트가 시간 초과를 시작할 때 발생합니다.
- 검색 에이전트가 서버에서 이 오류를 검색하면 어플라이언스는 응답하지 않는 서버의 과부하를 방지하기 위해 이후에 서버에 대한 에이전트 없는 종속성 분석을 시도하지 않습니다.
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
3. 명령이 몇 초 안에 결과를 출력하면 Azure Migrate 프로젝트> 검색 및 평가>개요>관리>어플라이언스에서 어플라이언스 이름을 선택하고 **서비스 새로 고침** 을 선택하여 검색 서비스를 다시 시작합니다.
4. 명령이 출력 없이 시간 초과되면 
- 어떤 프로세스가 서버에서 높은 CPU 또는 메모리를 사용하는지 파악해야 합니다.
- 해당 서버에 더 많은 코어/메모리를 할당하고 명령을 다시 실행할 수 있습니다.

## <a name="error-10005-guestcredentialnotvalid"></a>오류 10005: GuestCredentialNotValid

### <a name="remediation"></a>수정
- 어플라이언스 구성 관리자에서 '자격 증명 유효성 재검사'를 클릭하여 자격 증명 _(오류에 제공된 친숙한 이름)_ 의 유효성을 확인합니다.
- 어플라이언스에 제공된 동일한 자격 증명을 사용하여 영향을 받는 서버에 로그인할 수 있는지 확인합니다.
- 관리자 계정 대신 해당 서버에 대해 다른 사용자 계정(동일한 도메인에 대해, 서버가 도메인에 가입된 경우)을 사용해 볼 수 있습니다.
- 문제는 글로벌 카탈로그와 도메인 컨트롤러 간 통신이 끊어졌을 때 발생할 수 있습니다. 도메인 컨트롤러에서 새 사용자 계정을 만들고 어플라이언스에서 동일한 계정을 제공하여 이를 확인할 수 있습니다. 도메인 컨트롤러를 다시 시작해야 할 수도 있습니다.
- 수정 단계를 수행한 후 [여기](troubleshoot-dependencies.md#mitigation-verification-using-vmware-powercli)에 있는 단계에 따라 오류가 해결되었는지 확인할 수 있습니다.

## <a name="error-10012-credentialnotprovided"></a>오류 10012: CredentialNotProvided

### <a name="cause"></a>원인
이 오류는 어플라이언스 구성 관리자에서 잘못된 도메인 이름으로 도메인 자격 증명을 제공한 경우 발생합니다. 예를 들어, 사용자 이름이 user@abc.com인 도메인 자격 증명을 제공했지만 도메인 이름을 def.com으로 제공한 경우 서버가 def.com에 연결되어 있으면 해당 자격 증명이 시도되지 않고 이 오류 메시지가 표시됩니다.

### <a name="remediation"></a>수정
- 어플라이언스 구성 관리자로 이동하여 서버 자격 증명을 추가하거나 원인에 설명된 대로 기존 자격 증명을 편집합니다.
- 수정 단계를 수행한 후 [여기](troubleshoot-dependencies.md#mitigation-verification-using-vmware-powercli)에 있는 단계에 따라 오류가 해결되었는지 확인할 수 있습니다.

## <a name="mitigation-verification-using-vmware-powercli"></a>VMware PowerCLI를 사용한 완화 확인

위에 나열된 오류에 대한 완화 단계를 사용한 후 어플라이언스 서버에서 몇 가지 PowerCLI 명령을 실행하여 완화가 작동했는지 확인할 수 있습니다. 명령이 성공하면 문제가 이제 해결되었음을 의미합니다. 그렇지 않으면 수정 단계를 다시 확인하고 따라야 합니다.

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
4. 에이전트 없는 종속성 분석의 경우 다음 명령을 실행하여 성공적인 출력을 얻는지 확인합니다.

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
5. 완화가 작동했는지 확인한 후 Azure Migrate 프로젝트> 검색 및 평가>개요>관리>어플라이언스에서 어플라이언스 이름을 선택하고 **서비스 새로 고침** 을 선택하여 새로운 검색 주기를 시작합니다.


## <a name="my-log-analytics-workspace-is-not-listed-when-trying-to-configure-the-workspace-in-azure-migrate-for-agent-based-dependency-analysis"></a>에이전트 기반 종속성 분석을 위해 Azure Migrate에서 작업 영역을 구성하려고 할 때 내 Log Analytics 작업 영역이 나열되지 않습니다.
Azure Migrate는 현재 미국 동부, 동남 아시아 및 서유럽 지역에서 OMS 작업 영역을 만들 수 있도록 지원합니다. 작업 영역을 다른 지역의 Azure Migrate 외부에서 만든 경우에도 현재 프로젝트에 연결할 수 없습니다.

## <a name="agent-based-dependency-visualization-in-azure-government"></a>Azure Government의 에이전트 기반 종속성 시각화

에이전트 기반 종속성 분석은 Azure Government에서 지원되지 않습니다. 에이전트 없는 종속성 분석 _(VMware 서버에서만 사용 가능)_ 을 사용하세요.

## <a name="agent-based-dependencies-dont-show-after-agent-install"></a>에이전트 설치 후 에이전트 기반 종속성이 표시되지 않음

온-프레미스 VM에서 종속성 시각화 에이전트를 설치하고 나면 Azure Migrate는 일반적으로 포털에서 종속성을 표시하는 데 15-30분이 소요됩니다. 30분 넘게 대기한 경우 MMA(Microsoft Monitoring Agent)가 Log Analytics 작업 영역에 연결할 수 있는지 확인합니다.

Windows VM의 경우
1. 제어판에서 MMA를 시작합니다.
2. **Microsoft Monitoring Agent 속성** > **Azure Log Analytics(OMS)** 에서 작업 영역의 **상태** 가 녹색인지 확인합니다.
3. 상태가 녹색이 아닌 경우 작업 영역을 제거했다가 MMA에 다시 추가하세요.

    ![MMA 상태](./media/troubleshoot-assessment/mma-properties.png)

Linux VM의 경우 MMA 및 종속 에이전트 설치 명령이 정상적으로 실행되었는지 확인합니다. [여기](../azure-monitor/vm/service-map.md#post-installation-issues)에서 문제 해결 가이드를 참조하세요.

## <a name="supported-operating-systems-for-agent-based-dependency-analysis"></a>에이전트 기반 종속성 분석에 대해 지원되는 운영 체제

- **MMS 에이전트**: 지원되는 [Windows](../azure-monitor/agents/agents-overview.md#supported-operating-systems) 및 [Linux](../azure-monitor/agents/agents-overview.md#supported-operating-systems) 운영 체제를 검토합니다.
- **종속성 에이전트**: 지원되는 [Windows 및 Linux](../azure-monitor/vm/vminsights-enable-overview.md#supported-operating-systems) 운영 체제입니다.

## <a name="visualize-dependencies-for--1-hour-with-agent-based-dependency-analysis"></a>에이전트 기반 종속성 분석으로 1시간 이상 종속성 시각화

에이전트 기반 종속성 분석을 사용할 경우 Azure Migrate에서는 지난 달의 특정 날짜로 돌아갈 수 있으며 종속성을 시각화할 수 있는 최대 기간은 1시간입니다. 예를 들어 종속성 맵의 기간 기능을 사용해 어제의 종속성을 확인할 수는 있지만, 1시간 동안만 볼 수 있습니다. 그러나 Azure Monitor 로그를 사용하여 더 긴 기간의 [종속성 데이터를 쿼리](./how-to-create-group-machine-dependencies.md)할 수 있습니다.

## <a name="visualized-dependencies-for--10-servers-with-agent-based-dependency-analysis"></a>에이전트 기반 종속성 분석이 있는 10대 이상의 서버에 대한 시각화된 종속성

Azure Migrate에서 에이전트 기반 종속성 분석을 사용하면 최대 10개의 VM이 있는 [그룹에 대한 종속성을 시각화](./how-to-create-a-group.md#refine-a-group-with-dependency-mapping)할 수 있습니다. 규모가 큰 그룹의 경우 종속성을 시각화하기 위해 VM을 더 작은 그룹으로 분할하는 것이 좋습니다.

## <a name="servers-show-install-agent-for-agent-based-dependency-analysis"></a>서버에 에이전트 기반 종속성 분석을 위해 "에이전트 설치"가 표시됨

종속성 시각화를 사용하도록 설정하고 Azure에 서버를 마이그레이션한 후 서버는 다음과 같은 동작으로 인해 "종속성 보기" 대신 "에이전트 설치" 작업을 표시할 수 있습니다.

- Azure로 마이그레이션한 후 온-프레미스 서버는 꺼지고 동일한 VM이 Azure에서 작동됩니다. 이러한 서버는 다른 MAC 주소를 갖습니다.
- 서버는 온-프레미스 IP 주소를 보유했는지 여부에 따라 다른 IP 주소를 가질 수도 있습니다.
- MAC 주소와 IP 주소가 온-프레미스와 다른 경우 Azure Migrate는 온-프레미스 서버를 서비스 맵 종속성 데이터와 연결하지 않습니다. 이 경우 종속성 표시 옵션 대신 에이전트를 설치하는 옵션이 표시됩니다.
- Azure에 대해 테스트 마이그레이션을 수행한 후 온-프레미스 서버는 예상대로 켜진 상태를 유지합니다. Azure에서 작동되는 동급의 서버는 다른 MAC 주소를 획득하며 다른 IP 주소를 획득할 수도 있습니다. 이러한 서버에서 나가는 Azure Monitor 로그 트래픽을 차단하지 않는 한, Azure Migrate는 온-프레미스 서버를 서비스 맵 종속성 데이터와 연결하지 않으므로 종속성 표시 옵션 대신 에이전트를 설치하는 옵션이 표시됩니다.

## <a name="capture-network-traffic"></a>네트워크 트래픽 캡처

다음과 같이 네트워크 트래픽 로그를 수집합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. F12 키를 눌러 개발자 도구를 시작합니다. 필요한 경우 **탐색에 대한 항목 지우기** 설정을 선택 취소합니다.
3. **네트워크** 탭을 선택하고 네트워크 트래픽 캡처를 시작합니다.
   - 크롬에서 **Preserve log** 를 클릭합니다. 자동으로 기록이 시작됩니다. 빨간색 원은 트래픽을 캡처하고 있다는 뜻입니다. 빨간색 원이 나타나지 않으면 시작할 검은색 원을 선택합니다.
   - Microsoft Edge 및 Internet Explorer에서 기록이 자동으로 시작됩니다. 그렇지 않으면 녹색 재생 단추를 선택합니다.
4. 오류를 재현해 봅니다.
5. 기록하는 동안 오류가 발생하면 기록을 중지하고 기록된 활동의 복사본을 저장합니다.
   - Chrome에서는 마우스 오른쪽 단추로 클릭하고 **Save as HAR with content(콘텐츠와 함께 HAR로 저장)** 를 선택합니다. 그러면 로그가 HTTP 보관(har) 파일로 압축되어 내보내집니다.
   - Microsoft Edge 또는 Internet Explorer에서 **캡처된 트래픽 내보내기** 옵션을 선택합니다. 그러면 로그가 압축되어 내보내집니다.
6. **콘솔** 탭을 선택하여 경고 또는 오류를 확인합니다. 콘솔 로그를 저장하려면:
   - 크롬의 경우 콘솔 로그에서 아무 위치를 마우스 오른쪽 단추로 클릭합니다. **다른 이름으로 저장** 을 선택하여 로그를 내보내고 압축합니다.
   - Microsoft Edge 또는 Internet Explorer에서 오류를 마우스 오른쪽 단추로 클릭하고 **모두 복사** 를 선택합니다.
7. 개발자 도구를 닫습니다.


## <a name="next-steps"></a>다음 단계

평가를 [만들거나](how-to-create-assessment.md) [사용자 지정](how-to-modify-assessment.md)합니다.