---
title: 에이전트 기반 및 에이전트 없는 종속성 분석 문제 해결
description: Azure Migrate의 종속성 시각화에 대한 도움을 받으세요.
author: Vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: troubleshooting
ms.date: 07/01/2020
ms.openlocfilehash: 671820e4c3ca3a4b8069f6be6d2a884a97da0739
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/07/2021
ms.locfileid: "123535638"
---
# <a name="troubleshoot-dependency-visualization"></a>종속성 시각화 문제 해결

이 문서는 _VMware 서버 에서만 사용할 수_ 있는 에이전트 기반 및 에이전트 없는 종속성 분석과 관련된 문제를 해결하는 데 도움이 됩니다. Azure Migrate에서 지원되는 종속성 시각화 유형에 대해 [자세히 알아봅니다](concepts-dependency-visualization.md).

## <a name="visualize-dependencies-for-1-hour-with-agentless-dependency-analysis"></a>에이전트 없는 종속성 분석을 >1시간 동안 종속성 시각화

에이전트 없는 종속성 분석을 사용하면 최대 30일 동안 맵에서 종속성을 시각화하거나 내보낼 수 있습니다.

## <a name="visualize-dependencies-for-10-servers-with-agentless-dependency-analysis"></a>에이전트 없는 종속성 분석을 통해 >10개 서버에 대한 종속성 시각화

Azure Migrate는 여러 서버의 네트워크 연결을 한 번에 시각화하고 프로세스 및 서버를 기준으로 필터링하는 데 사용할 수 있는 Power BI 템플릿을 제공합니다. 여러 서버에 대한 의존도를 함께 시각화하는 방법에 대해 [자세히 알아봅니다.](how-to-create-group-machine-dependencies-agentless.md#visualize-network-connections-in-power-bi)

## <a name="dependencies-export-csv-shows-unknown-process-with-agentless-dependency-analysis"></a>종속성 내보내기 CSV에 에이전트 없는 종속성 분석이 포함된 "알 수 없는 프로세스"가 표시됨
에이전트 없는 종속성 분석에서 최선의 방식으로 프로세스 이름이 캡처됩니다. 특정 시나리오에서는 원본 및 대상 서버 이름과 대상 포트가 캡처되지만 종속성의 양쪽 끝에서 프로세스 이름을 확인할 수는 없습니다. 이러한 경우 프로세스는 "알 수 없는 프로세스"로 표시됩니다.

## <a name="unable-to-export-dependency-data-in-a-csv-due-to-the-error-403-this-request-is-not-authorized-to-perform-this-operation"></a>"403: 이 요청은 이 작업을 수행할 권한이 없습니다." 오류로 인해 CSV에서 종속성 데이터를 내보낼 수 없습니다.
Azure Migrate 프로젝트에 프라이빗 엔드포인트 연결이 있는 경우 프라이빗 네트워크를 통해 Azure 가상 네트워크에 연결된 클라이언트에서 종속성 데이터 내보내기 요청을 시작해야 합니다. 이 오류를 해결하려면 온-프레미스 네트워크 또는 어플라이언스 서버에서 Azure Portal 열고 다시 내보내기를 시도합니다.

## <a name="common-agentless-dependency-analysis-errors"></a>일반적인 에이전트 없는 종속성 분석 오류

Azure Migrate Azure Migrate 사용하여 에이전트 없는 종속성 분석을 지원합니다. 검색 및 평가. 에이전트 없는 종속성 분석은 현재 VMware에 대해서만 지원됩니다. 에이전트 없는 종속성 분석의 요구 사항에 대해 [자세히 알아봅니다](how-to-create-group-machine-dependencies-agentless.md).

에이전트 없는 종속성 분석 오류 목록은 다음 표에 요약되어 있습니다.

> [!Note]
> 필요한 데이터를 수집하기 위해 에이전트 없는 종속성 분석과 동일한 방법을 따르므로 소프트웨어 인벤토리에서도 동일한 오류가 발생할 수 있습니다.

| **오류** | **원인** | **동작** |
|--|--|--|
| **9000:** 서버의 VMware 도구 상태를 검색할 수 없습니다. | VMware 도구가 서버에 설치되어 있지 않거나 설치된 버전이 손상되었을 수 있습니다. | 버전 10.2.1 이후의 VMware 도구가 서버에 설치되어 실행되고 있는지 확인합니다. |
| **9001:** VMware 도구가 서버에 설치되어 있지 않습니다. | VMware 도구가 서버에 설치되어 있지 않거나 설치된 버전이 손상되었을 수 있습니다. | 버전 10.2.1 이후의 VMware 도구가 서버에 설치되어 실행되고 있는지 확인합니다. |
| **9002:** VMware 도구가 서버에서 실행되고 있지 않습니다. | VMware 도구가 서버에 설치되어 있지 않거나 설치된 버전이 손상되었을 수 있습니다. | 버전 10.2.0 이후의 VMware 도구가 서버에 설치되어 실행되고 있는지 확인합니다. |
| **9003:** 서버에서 실행되는 운영 체제 유형은 지원되지 않습니다. | 서버에서 실행되는 운영 체제는 Windows 또는 Linux가 아닙니다. | Windows 및 Linux OS 유형만 지원됩니다. 서버가 실제 실행 중인 Windows 또는 Linux OS인 경우 vCenter Server에 지정된 운영 체제 유형을 확인합니다. |
| **9004:** 서버가 실행 중 상태가 아닙니다. | 서버가 전원이 꺼진 상태입니다. | 서버가 실행 중인 상태인지 확인합니다. |
| **9005:** 서버에서 실행되는 운영 체제 유형은 지원되지 않습니다. | 서버에서 실행되는 운영 체제는 Windows 또는 Linux가 아닙니다. | Windows 및 Linux OS 유형만 지원됩니다. \<FetchedParameter>운영 체제는 현재 지원되지 않습니다. |
| **9006:** 서버에서 검색 메타데이터 파일을 다운로드하는 데 필요한 URL이 비어 있습니다. | 어플라이언스의 검색 에이전트가 예상대로 작동하지 않기 때문에 이 문제는 일시적일 수 있습니다. | 문제는 24시간 이내에 다음 주기에서 자동으로 해결됩니다. 문제가 지속되면 Microsoft 지원 사례를 제출하세요. |
| **9007:** 메타데이터를 수집하기 위해 스크립트를 실행하는 프로세스가 서버에 없습니다. | 어플라이언스의 검색 에이전트가 예상대로 작동하지 않기 때문에 이 문제는 일시적일 수 있습니다. | 문제는 24시간 이내에 다음 주기에서 자동으로 해결됩니다. 문제가 지속되면 Microsoft 지원 사례를 제출하세요. |
| **9008:** 메타데이터를 수집하기 위해 서버에서 실행 중인 프로세스의 상태를 검색할 수 없습니다. | 이 문제는 내부 오류로 인해 일시적일 수 있습니다. | 문제는 24시간 이내에 다음 주기에서 자동으로 해결됩니다. 문제가 지속되면 Microsoft 지원 사례를 제출하세요. |
| **9009:** Windows UAC(사용자 계정 컨트롤)는 서버에서 검색 작업을 실행하지 못하게 합니다. | Windows UAC 설정은 서버에서 설치된 애플리케이션의 검색을 제한합니다. | 영향을 받는 서버에서 제어판 **사용자 계정 컨트롤** 설정의 수준을 낮춥니다. |
| **9010:** 서버의 전원이 꺼져 있습니다. | 서버가 전원이 꺼진 상태입니다. | 서버가 전원이 켜진 상태인지 확인합니다. |
| **9011:** 검색된 메타데이터를 포함하는 파일을 서버에서 찾을 수 없습니다. | 이 문제는 내부 오류로 인해 일시적일 수 있습니다. | 문제는 24시간 이내에 다음 주기에서 자동으로 해결됩니다. 문제가 지속되면 Microsoft 지원 사례를 제출하세요. |
| **9012:** 서버에서 검색된 메타데이터를 포함하는 파일이 비어 있습니다. | 이 문제는 내부 오류로 인해 일시적일 수 있습니다. | 문제는 24시간 이내에 다음 주기에서 자동으로 해결됩니다. 문제가 지속되면 Microsoft 지원 사례를 제출하세요. |
| **9013:** 매번 서버에 로그인할 때 새 임시 사용자 프로필이 만들어집니다. | 매번 서버에 로그인할 때 새 임시 사용자 프로필이 만들어집니다. | 이 문제를 해결하는 데 도움이 되는 Microsoft 지원 사례를 제출합니다. |
| **9014:** ESXi 호스트에서 발생한 오류로 인해 검색된 메타데이터가 포함된 파일을 검색할 수 없습니다. 오류 코드: %ErrorCode; 세부 정보: %ErrorMessage | ESXi 호스트 \<HostName>에서 오류가 발생했습니다. 오류 코드: %ErrorCode; 세부 정보: %ErrorMessage. | 서버가 실행 중인 ESXi 호스트에서 포트 443이 열려 있는지 확인합니다.<br/><br/> 문제 해결 방법에 관해 [자세히 알아보세요](troubleshoot-dependencies.md#error-9014-httpgetrequesttoretrievefilefailed).|
| **9015:** 서버 검색에 제공된 vCenter Server 사용자 계정에 게스트 작업 권한이 설정되어 있지 않습니다. | 게스트 작업의 필수 권한은 vCenter Server 사용자 계정에서 사용하도록 설정되지 않았습니다. | vCenter Server 사용자 계정에 **Virtual Machines**  >  **게스트 작업이** 서버와 상호 작용하고 필요한 데이터를 끌어올 수 있는 권한이 있는지 확인합니다. <br/><br/> 필요한 권한으로 vCenter Server 계정을 설정하는 방법에 관해 [자세히 알아보세요](tutorial-discover-vmware.md#prepare-vmware). |
| **9016:** 서버의 게스트 작업 에이전트가 오래되어 메타데이터를 검색할 수 없습니다. | VMware 도구가 서버에 설치되지 않았거나 설치된 버전이 최신 버전이 아닙니다. | VMware 도구가 서버에 설치되어 실행되고 최신 상태인지 확인합니다. VMware Tools 버전은 버전 10.2.1 이상이어야 합니다. |
| **9017:** 검색된 메타데이터를 포함하는 파일을 서버에서 찾을 수 없습니다. | 내부 오류로 인해 일시적인 문제일 수 있습니다. | 이 문제를 해결하는 데 도움이 되는 Microsoft 지원 사례를 제출합니다. |
| **9018:** PowerShell이 서버에 설치되어 있지 않습니다. | 서버에서 PowerShell을 찾을 수 없습니다. | 서버에 PowerShell 버전 2.0 이상이 설치되어 있는지 확인합니다. <br/><br/> 문제를 해결하는 방법에 대해 [자세히 알아보세요.](troubleshoot-dependencies.md#error-9018-powershellnotfound)|
| **9019:** 서버에서 게스트 작업 오류로 인해 메타데이터를 검색할 수 없습니다. | 서버에서 VMware 게스트 작업에 실패했습니다. 서버에서 \<FriendlyNameOfCredentials> 자격 증명을 시도할 때 문제가 발생했습니다. | 어플라이언스 서버 자격 증명이 유효하고 자격 증명의 사용자 이름이 UPN(사용자 계정 이름) 형식인지 확인합니다. (가능한 원인에서 Azure Migrate 시도한 자격 증명의 이름을 찾습니다.) |
| **9020:** 서버에서 검색된 메타데이터를 포함하는 데 필요한 파일을 만들 수 없습니다. | 어플라이언스 또는 온-프레미스 그룹 정책에서 제공된 자격 증명에 연결된 역할이 필요한 폴더에서 파일 생성을 제한하고 있습니다. 서버에서 \<FriendlyNameOfCredentials> 자격 증명을 시도할 때 문제가 발생했습니다. | 1. 어플라이언스에 제공된 자격 증명이 서버의 폴더에 대한 파일 권한을 만들었는지 \<folder path/folder name> 확인합니다. <br/>2. 어플라이언스에 제공된 자격 증명에 필요한 권한이 없는 경우 다른 자격 증명 집합을 제공하거나 기존 자격 증명을 편집합니다. (가능한 원인에서 Azure Migrate 시도한 자격 증명의 이름을 찾습니다.) |
| **9021:** 서버의 오른쪽 경로에 검색된 메타데이터를 포함하는 데 필요한 파일을 만들 수 없습니다. | VMware 도구에서 파일을 만들기 위한 잘못된 파일 경로를 보고합니다. | 버전 10.2.0 이후의 VMware 도구가 서버에 설치되어 실행되고 있는지 확인합니다. |
| **9022:** 서버에서 Get-WmiObject cmdlet을 실행하기 위한 액세스가 거부되었습니다. | 어플라이언스 또는 온-프레미스 그룹 정책에서 제공하는 자격 증명에 연결된 역할이 WMI 개체에 대한 액세스를 제한하고 있습니다. 서버에서 \<FriendlyNameOfCredentials> 자격 증명을 시도할 때 문제가 발생했습니다. | 1. 어플라이언스에 제공된 자격 증명이 파일 관리자 권한을 만들었고 WMI를 사용하도록 설정했는지 확인합니다. <br/>2. 어플라이언스에 제공된 자격 증명에 필요한 권한이 없는 경우 다른 자격 증명 집합을 제공하거나 기존 자격 증명을 편집합니다. (가능한 원인에서 Azure Migrate 시도한 자격 증명의 이름을 찾습니다.)<br/><br/> 문제 해결 방법에 관해 [자세히 알아보세요](troubleshoot-dependencies.md#error-9022-getwmiobjectaccessdenied).|
| **9023:**%SystemRoot% 환경 변수 값이 비어 있으므로 PowerShell을 실행할 수 없습니다. | %SystemRoot% 환경 변수의 값이 서버에 대해 비어 있습니다. | 1. 환경 변수가 영향을 받는 서버에서 echo %systemroot% 명령을 실행하여 빈 값을 반환하는지 확인합니다. <br/>2. 문제가 지속되면 Microsoft 지원 사례를 제출합니다. |
| **9024:**%TEMP% 환경 변수 값이 비어 있기 때문에 검색을 수행할 수 없습니다. | 서버에 대한 %TEMP% 환경 변수 값이 비어 있습니다. | 1. 환경 변수가 영향을 받는 서버에서 echo %temp% 명령을 실행하여 빈 값을 반환하는지 확인합니다. <br/>2. 문제가 지속되면 Microsoft 지원 사례를 제출합니다. |
| **9025:** 서버에서 PowerShell이 손상되었으므로 검색을 수행할 수 없습니다. | PowerShell이 서버에서 손상되었습니다. | PowerShell을 다시 설치하고 영향을 받는 서버에서 실행 중인지 확인합니다. |
| **9026:** 서버에서 게스트 작업을 실행할 수 없습니다. | 서버의 현재 상태에서는 게스트 작업을 실행할 수 없습니다. | 1. 영향을 받는 서버가 실행 중인지 확인합니다.<br/>2. 문제가 지속되면 Microsoft 지원 사례를 제출합니다. |
| **9027:** 게스트 작업 에이전트가 서버에서 실행되고 있지 않으므로 메타데이터를 검색할 수 없습니다. | 서버의 게스트 작업 에이전트에 연결할 수 없습니다. | 버전 10.2.0 이후의 VMware 도구가 서버에 설치되어 실행되고 있는지 확인합니다. |
| **9028:** 서버에 스토리지가 부족하여 검색된 메타데이터를 포함하는 데 필요한 파일을 만들 수 없습니다. | 서버 디스크에 충분한 스토리지 공간이 부족합니다. | 영향을 받는 서버의 디스크 스토리지에 충분한 공간을 사용할 수 있는지 확인합니다. |
| **9029:** 어플라이언스에 제공된 자격 증명에 PowerShell을 실행할 수 있는 액세스 권한이 없습니다. | 어플라이언스의 자격 증명에는 PowerShell을 실행할 수 있는 액세스 권한이 없습니다. 서버에서 \<FriendlyNameOfCredentials> 자격 증명을 시도할 때 문제가 발생했습니다. | 1. 어플라이언스 자격 증명이 서버의 PowerShell에 액세스할 수 있는지 확인합니다.<br/>2. 어플라이언스의 자격 증명에 필요한 액세스 권한이 없는 경우 다른 자격 증명 집합을 제공하거나 기존 자격 증명을 편집합니다. (가능한 원인에서 Azure Migrate 시도한 자격 증명의 이름을 찾습니다.) |
| **9030:** 서버가 호스트되는 ESXi 호스트의 연결이 끊어진 상태이므로 검색된 메타데이터를 수집할 수 없습니다. | 서버가 있는 ESXi 호스트의 연결이 끊어진 상태입니다. | 서버를 실행하는 ESXi 호스트가 연결된 상태인지 확인합니다. |
| **9031:** 서버가 호스트되는 ESXi 호스트가 응답하지 않으므로 검색된 메타데이터를 수집할 수 없습니다. | 서버가 있는 ESXi 호스트가 잘못된 상태입니다. | 서버를 실행하는 ESXi 호스트가 실행 및 연결 상태인지 확인합니다. |
| **9032:** 내부 오류로 인해 검색할 수 없습니다. | 내부 오류로 인해 발생하는 문제입니다. | 이 웹 [사이트의](troubleshoot-dependencies.md#error-9032-invalidrequest) 단계에 따라 문제를 해결합니다. 문제가 지속되면 Microsoft 지원 사례를 여세요. |
| **9033:** 서버의 어플라이언스에 제공된 자격 증명의 사용자 이름에 잘못된 문자가 있으므로 검색할 수 없습니다. | 어플라이언스 자격 증명에 사용자 이름에 잘못된 문자가 포함되어 있습니다. 서버에서 \<FriendlyNameOfCredentials> 자격 증명을 시도할 때 문제가 발생했습니다. | 어플라이언스의 자격 증명에 사용자 이름에 잘못된 문자가 없는지 확인합니다. 어플라이언스 구성 관리자로 돌아가서 자격 증명을 편집할 수 있습니다. (가능한 원인에서 Azure Migrate 시도한 자격 증명의 이름을 찾습니다.) |
| **9034:** 서버의 어플라이언스에 제공된 자격 증명의 사용자 이름이 UPN 형식이 아니므로 검색할 수 없습니다. | 어플라이언스의 자격 증명에는 UPN 형식의 사용자 이름이 없습니다. 서버에서 \<FriendlyNameOfCredentials> 자격 증명을 시도할 때 문제가 발생했습니다. | 어플라이언스 자격 증명의 사용자 이름이 UPN 형식인지 확인합니다. 어플라이언스 구성 관리자로 돌아가서 자격 증명을 편집할 수 있습니다. (가능한 원인에서 Azure Migrate 시도한 자격 증명의 이름을 찾습니다.) |
| **9035:** PowerShell 언어 모드가 올바르게 설정되지 않았으므로 검색할 수 없습니다. | PowerShell 언어 모드가 전체 **언어** 로 설정되지 않았습니다. | PowerShell 언어 모드가 전체 **언어** 로 설정되어 있는지 확인합니다. |
| **9036:** 서버의 어플라이언스에 제공된 자격 증명의 사용자 이름이 UPN 형식이 아니므로 검색할 수 없습니다. | 어플라이언스의 자격 증명에는 UPN 형식의 사용자 이름이 없습니다. 서버에서 \<FriendlyNameOfCredentials> 자격 증명을 시도할 때 문제가 발생했습니다. | 어플라이언스 자격 증명의 사용자 이름이 UPN 형식인지 확인합니다. 어플라이언스 구성 관리자로 돌아가서 자격 증명을 편집할 수 있습니다. (가능한 원인에서 Azure Migrate 시도한 자격 증명의 이름을 찾습니다.) |
| **9037:** 서버의 높은 응답 시간으로 인해 메타데이터 컬렉션이 일시적으로 일시 중지되었습니다. | 서버가 응답하는 데 너무 오래 걸립니다. | 문제는 24시간 이내에 다음 주기에서 자동으로 해결됩니다. 문제가 지속되면 Microsoft 지원 사례를 제출하세요. |
| **10000:** 서버에서 실행되는 운영 체제 유형은 지원되지 않습니다. | 서버에서 실행되는 운영 체제는 Windows 또는 Linux가 아닙니다. | Windows 및 Linux OS 유형만 지원됩니다. \<GuestOSName> 운영 체제는 현재 지원되지 않습니다. |
| **10001:** 검색 메타데이터를 수집하는 데 필요한 스크립트가 서버에 없습니다. | 검색을 수행하는 데 필요한 스크립트가 예상 위치에서 삭제되거나 제거되었을 수 있습니다. | 이 문제를 해결하는 데 도움이 되는 Microsoft 지원 사례를 제출합니다. |
| **10002:** 서버에서 검색 작업이 시간 부족했습니다. | 어플라이언스의 검색 에이전트가 예상대로 작동하지 않기 때문에 이 문제는 일시적일 수 있습니다. | 문제는 24시간 이내에 다음 주기에서 자동으로 해결됩니다. 해결되지 않으면 [이 웹 사이트의](troubleshoot-dependencies.md#error-10002-scriptexecutiontimedoutonvm) 단계에 따라 문제를 해결합니다. 문제가 지속되면 Microsoft 지원 사례를 엽니다.|
| **10003:** 검색 작업을 실행하는 프로세스가 오류와 함께 종료되었습니다. | 검색 작업을 실행하는 프로세스가 오류로 인해 갑자기 종료되었습니다.| 문제는 24시간 이내에 다음 주기에서 자동으로 해결됩니다. 문제가 지속되면 Microsoft 지원 사례를 제출하세요. |
| **10004:** 서버 OS 유형에 대한 자격 증명이 어플라이언스에 제공되지 않습니다. | 서버 OS 유형에 대한 자격 증명이 어플라이언스에서 추가되지 않았습니다. | 1. 어플라이언스의 영향을 받는 서버의 OS 유형에 대한 자격 증명을 추가해야 합니다.<br/> 2. 이제 어플라이언스에 여러 서버 자격 증명을 추가할 수 있습니다. |
| **10005:** 서버의 어플라이언스에 제공된 자격 증명이 잘못되었습니다. | 어플라이언스에서 제공 된 자격 증명이 유효 하지 않습니다. 서버에서 \<FriendlyNameOfCredentials> 자격 증명을 시도할 때 문제가 발생했습니다. | 1. 어플라이언스에서 제공 된 자격 증명이 유효 하 고 자격 증명을 사용 하 여 서버에 액세스할 수 있는지 확인 합니다.<br/> 2. 이제 어플라이언스에 여러 서버 자격 증명을 추가할 수 있습니다.<br/> 3. 어플라이언스 구성 관리자로 돌아가서 다른 자격 증명 세트를 제공하거나 기존 자격 증명을 편집합니다. Azure Migrate에서 시도 하는 자격 증명의 이름을 찾을 수 있습니다. <br/><br/> 문제를 해결 하는 방법에 [대해 자세히 알아보세요](troubleshoot-dependencies.md#error-10005-guestcredentialnotvalid) .|
| **10006**: 서버에서 실행 중인 운영 체제 유형이 지원 되지 않습니다. | 서버에서 실행 중인 운영 체제가 Windows 또는 Linux가 아닙니다. | Windows 및 Linux OS 유형만 지원됩니다. \<GuestOSName> 운영 체제는 현재 지원 되지 않습니다. |
| **10007**: 서버에서 검색 된 메타 데이터를 처리할 수 없습니다. | 검색된 메타데이터가 포함된 파일의 내용을 구문 분석하는 동안 오류가 발생했습니다. | 이 문제를 해결 하는 데 도움이 되는 Microsoft 지원 사례를 제출 합니다. |
| **10008**: 서버에서 검색 된 메타 데이터를 포함 하는 데 필요한 파일을 만들 수 없습니다. | 어플라이언스 또는 온-프레미스 그룹 정책에 제공된 자격 증명과 연결된 역할이 필수 폴더에서의 파일 생성을 제한하고 있습니다. 서버에서 다음 자격 증명을 시도할 때 문제가 발생했습니다. \<FriendlyNameOfCredentials>. | 1. 어플라이언스에서 제공 된 자격 증명이 서버의 폴더에 대 한 파일 사용 권한을 만들었는지 확인 합니다. \<folder path/folder name><br/>2. 어플라이언스에 제공 된 자격 증명에 필요한 권한이 없으면 다른 자격 증명 집합을 제공 하거나 기존 자격 증명을 편집 합니다. Azure Migrate에서 시도 하는 자격 증명의 이름을 찾을 수 있습니다. |
| **10009**: 서버의 파일에서 검색 된 메타 데이터를 쓸 수 없습니다. | 어플라이언스 또는 온-프레미스 그룹 정책에 제공된 자격 증명과 연결된 역할이 서버의 파일 쓰기를 제한하고 있습니다. 서버에서 \<FriendlyNameOfCredentials> 자격 증명을 시도할 때 문제가 발생했습니다. | 1. 어플라이언스에 제공 된 자격 증명에 서버의 폴더에 대 한 쓰기 파일 권한이 있는지 확인 합니다. \<folder path/folder name><br/>2. 어플라이언스에 제공 된 자격 증명에 필요한 권한이 없으면 다른 자격 증명 집합을 제공 하거나 기존 자격 증명을 편집 합니다. Azure Migrate에서 시도 하는 자격 증명의 이름을 찾을 수 있습니다. |
| **10010**:% CommandName; 명령을 검색 하지 못했습니다. 일부 메타 데이터를 수집 하는 데 필요한가 서버에 없습니다. | % CommandName; 명령을 포함 하는 패키지 가 서버에 설치 되어 있지 않은 경우 | % CommandName; 명령을 포함 하는 패키지가 있는지 확인 하십시오. 가 서버에 설치 되어 있어야 합니다. |
| **10011**: 어플라이언스에 제공 된 자격 증명을 사용 하 여 대화형 세션을 로그인 하 고 로그 아웃 했습니다. | 대화형 로그인 및 로그 아웃을 사용 하면 사용 중인 계정의 프로필에서 레지스트리 키가 언로드됩니다. 이 조건을 사용하면 나중에 키를 사용할 수 없게 됩니다. | [이 웹 사이트](/sharepoint/troubleshoot/administration/800703fa-illegal-operation-error#resolutionus/sharepoint/troubleshoot/administration/800703fa-illegal-operation-error)에 설명 된 해결 방법을 사용 합니다. |
| **10012**: 서버에 대 한 자격 증명을 어플라이언스에서 제공 하지 않았습니다. | 서버에 대 한 자격 증명이 제공 되지 않았거나, 어플라이언스에서 도메인 이름이 잘못 된 도메인 자격 증명을 제공 했습니다. 이 오류의 원인에 [대해 자세히 알아보세요](troubleshoot-dependencies.md#error-10012-credentialnotprovided) . | 1. 서버에 대 한 자격 증명을 어플라이언스에 제공 하 고 자격 증명을 사용 하 여 서버에 액세스할 수 있는지 확인 합니다. <br/>2. 이제 어플라이언스에 여러 서버 자격 증명을 추가할 수 있습니다. 어플라이언스 구성 관리자로 돌아가서 서버의 자격 증명을 제공합니다.|


## <a name="error-970-dependencymapinsufficientprivilegesexception"></a>오류 970: DependencyMapInsufficientPrivilegesException

### <a name="cause"></a>원인
어플라이언스에 대 한 필요한 권한이 있는 자격 증명을 제공 하지 않은 경우 Linux 서버에 대 한 오류가 일반적으로 표시 됩니다.

### <a name="remediation"></a>수정
다음과 같은 두 가지 옵션이 있습니다.

- 루트 사용자 계정을 제공 했는지 확인 합니다.
- 계정에/bin/netstat 및/bin/ls 파일에 대 한 다음 권한이 있는지 확인 합니다.
   - CAP_DAC_READ_SEARCH
   - CAP_SYS_PTRACE

어플라이언스에 제공 된 사용자 계정에 필요한 권한이 있는지 확인 하려면 다음을 수행 합니다.

1. 오류 메시지에 설명 된 것과 동일한 사용자 계정으로이 오류가 발생 한 서버에 로그인 합니다.
1. Azure Shell에서 다음 명령을 실행 합니다. 에이전트 없는 종속성 분석에 필요한 권한이 없는 경우 오류가 발생 합니다.

   ````
   ps -o pid,cmd | grep -v ]$
   netstat -atnp | awk '{print $4,$5,$7}'
   ````
1. 다음 명령을 실행하여 /bin/netstat 및 /bin/ls 파일에 필요한 권한을 설정합니다.

   ````
   sudo setcap CAP_DAC_READ_SEARCH,CAP_SYS_PTRACE=ep /bin/ls
   sudo setcap CAP_DAC_READ_SEARCH,CAP_SYS_PTRACE=ep /bin/netstat
   ````
1. 이전 명령이 사용자 계정에 필요한 사용 권한을 할당 했는지 여부를 확인할 수 있습니다.

   ````
   getcap /usr/bin/ls
   getcap /usr/bin/netstat
   ````
1. 성공적인 출력을 얻으려면 2단계에서 제공된 명령을 다시 실행합니다.


## <a name="error-9014-httpgetrequesttoretrievefilefailed"></a>오류 9014: HTTPGetRequestToRetrieveFileFailed

### <a name="cause"></a>원인
어플라이언스의 VMware 검색 에이전트가 서버가 호스트 되는 ESXi 호스트를 통해 서버 파일 시스템에서 종속성 데이터를 포함 하는 출력 파일을 다운로드 하려고 하면 문제가 발생 합니다.

### <a name="remediation"></a>재구성
- 어플라이언스에서 ESXi 호스트 _(오류 메시지에 제공 된 이름)_ 443에 대 한 TCP 연결을 테스트 하 여 장치에서 종속성 데이터를 끌어올 ESXi 호스트에서 열어야 하는 데 필요 합니다. 어플라이언스 서버에서 PowerShell을 열고 다음 명령을 실행 합니다.
    ````
    Test -NetConnection -ComputeName <Ip address of the ESXi host> -Port 443
    ````

- 명령에서 성공적인 연결을 반환 하는 경우 **프로젝트**  >  **검색 및 평가** 개요 어플라이언스 관리로 Azure Migrate 이동 하 여  >    >    >  어플라이언스 이름을 선택 하 고 **서비스 새로 고침** 을 선택 합니다.

## <a name="error-9018-powershellnotfound"></a>오류 9018: PowerShellNotFound

### <a name="cause"></a>원인
이 오류는 일반적으로 Windows Server 2008를 실행 하는 서버에 대해 표시 됩니다.

### <a name="remediation"></a>재구성
서버 ($SYSTEMROOT) \System32\WindowsPowershell\v1.0\powershell.exe의이 위치에 필요한 PowerShell 버전 (2.0 이상)을 설치 합니다. Windows Server에서 PowerShell을 설치 하는 방법에 [대해 자세히 알아보세요](/powershell/scripting/windows-powershell/install/installing-windows-powershell) .

필요한 PowerShell 버전을 설치한 후 [이 웹 사이트](troubleshoot-dependencies.md#mitigation-verification-by-using-vmware-powercli)의 단계를 수행 하 여 오류가 해결 되었는지 확인 합니다.

## <a name="error-9022-getwmiobjectaccessdenied"></a>오류 9022: GetWMIObjectAccessDenied

### <a name="remediation"></a>수정
어플라이언스에 제공 된 사용자 계정에 WMI 네임 스페이스 및 네임 스페이스에 대 한 액세스 권한이 있는지 확인 합니다. 액세스를 설정 하려면:

1.  이 오류를 보고 하는 서버로 이동 합니다.
1.  **시작** 메뉴에서 **실행** 을 검색하여 선택합니다. **실행** 대화 상자에서 **열기** 텍스트 상자에 **Wmimgmt.msc** 를 입력 하 고 **enter 키** 를 선택 합니다.
1.  열린 wmimgmt 콘솔의 왼쪽 패널에서 **WMI 컨트롤(로컬)** 을 찾을 수 있습니다. 이를 마우스 오른쪽 단추로 클릭하고 메뉴에서 **속성** 을 선택합니다.
1.  **WMI 컨트롤(로컬) 속성** 대화 상자에서 **보안** 탭을 선택합니다.
1.  보안 탭 **에서** **보안** 을 선택 하 여 **루트에 대 한 보안** 대화 상자를 엽니다.
1.  **고급** 을 선택 하 여 **Root에 대 한 고급 보안 설정** 대화 상자를 엽니다. 
1.  **추가** 를 선택 하 여 **루트 대화 상자에 대 한 사용 권한 항목** 을 엽니다.
1.  **보안 주체 선택** 을 클릭 하 여 **사용자, 컴퓨터, 서비스 계정 또는 그룹 선택** 대화 상자를 엽니다.
1. WMI에 대 한 액세스 권한을 부여 하려는 사용자 이름 또는 그룹을 선택 하 고 **확인** 을 선택 합니다.
1. 실행 권한을 부여 하 고 **적용 대상** 드롭다운 목록에서 **이 네임 스페이스 및 네임 스페이스** 를 선택 합니다.
1. **적용** 을 선택 하 여 설정을 저장 하 고 모든 대화 상자를 닫습니다.

필요한 액세스를 받은 후 [이 웹 사이트](troubleshoot-dependencies.md#mitigation-verification-by-using-vmware-powercli)의 단계에 따라 오류가 해결 되었는지 확인 합니다.

## <a name="error-9032-invalidrequest"></a>오류 9032: InvalidRequest

### <a name="cause"></a>원인
이 문제에는 여러 가지 이유가 있을 수 있습니다. 한 가지 이유는 어플라이언스 구성 관리자에서 제공 된 사용자 이름 (서버 자격 증명)에 잘못 된 XML 문자가 있는 경우입니다. 잘못 된 문자가 있으면 SOAP 요청을 구문 분석 하는 동안 오류가 발생 합니다.

### <a name="remediation"></a>재구성
- 서버 자격 증명의 사용자 이름에 잘못 된 XML 문자가 있는지 확인 하 고 형식으로 되어 있는지 확인 하십시오 username@domain.com . 이 형식은 UPN 형식 이라고 많이.
- 어플라이언스에서 자격 증명을 편집한 후 [이 웹 사이트](troubleshoot-dependencies.md#mitigation-verification-by-using-vmware-powercli)의 단계에 따라 오류가 해결 되었는지 확인 합니다.


## <a name="error-10002-scriptexecutiontimedoutonvm"></a>오류 10002: ScriptExecutionTimedOutOnVm

### <a name="cause"></a>원인
- 이 오류는 서버가 느리거나 응답 하지 않을 때 종속성 데이터를 가져오기 위해 실행 된 스크립트가 시간 초과 될 때 발생 합니다.
- 검색 에이전트가 서버에서이 오류를 발견 한 후에는 서버에서 에이전트 없는 종속성 분석을 시도 하지 않으므로 응답 하지 않는 서버를 오버 로드 하지 않습니다.
- 서버와 관련된 문제를 확인하고 검색 서비스를 다시 시작할 때까지 오류가 계속 표시됩니다.

### <a name="remediation"></a>재구성
1. 이 오류가 발생한 서버에 로그인합니다.
1. PowerShell에서 다음 명령을 실행합니다.
   ````
   Get-WMIObject win32_operatingsystem;
   Get-WindowsFeature  | Where-Object {$_.InstallState -eq 'Installed' -or ($_.InstallState -eq $null -and $_.Installed -eq 'True')};
   Get-WmiObject Win32_Process;
   netstat -ano -p tcp | select -Skip 4;
   ````
1. 명령이 몇 초 안에 결과를 출력하는 경우 **Azure Migrate 프로젝트** 검색  >  **및 평가**  >  **개요**  >  어플라이언스 **관리로** 이동하여  >  **어플라이언스** 이름을 선택하고 **서비스 새로 고침을** 선택하여 검색 서비스를 다시 시작합니다.
1. 출력을 제공하지 않고 명령의 시간이 정해지면 다음을 수행해야 합니다.

   - 서버에서 높은 CPU 또는 메모리를 사용하는 프로세스를 파악합니다.
   - 해당 서버에 더 많은 코어 또는 메모리를 제공하고 명령을 다시 실행합니다.

## <a name="error-10005-guestcredentialnotvalid"></a>오류 10005: GuestCredentialNotValid

### <a name="remediation"></a>수정
- 어플라이언스 구성 관리자에서 자격 증명 유효성 재검사 를 선택하여 자격 _증명(오류에 제공된 이름)의_ 유효성을 확인합니다. 
- 어플라이언스에 제공된 것과 동일한 자격 증명을 사용하여 영향을 받는 서버에 로그인할 수 있는지 확인합니다.
- 관리자 계정 대신 해당 서버에 대해 다른 사용자 계정(서버가 도메인에 가입된 경우 동일한 도메인의 경우)을 사용해 볼 수 있습니다.
- 문제는 글로벌 카탈로그와 도메인 컨트롤러 간 통신이 끊어졌을 때 발생할 수 있습니다. 도메인 컨트롤러에서 새 사용자 계정을 만들고 어플라이언스에서 동일한 계정을 제공하여 이 문제를 확인합니다. 도메인 컨트롤러를 다시 시작해야 할 수도 있습니다.
- 수정 단계를 수행하면 [이 웹 사이트의](troubleshoot-dependencies.md#mitigation-verification-by-using-vmware-powercli)단계에 따라 오류가 해결되었는지 확인합니다.

## <a name="error-10012-credentialnotprovided"></a>오류 10012: CredentialNotProvided

### <a name="cause"></a>원인
이 오류는 어플라이언스 구성 관리자에서 도메인 자격 증명에 잘못된 도메인 이름을 제공한 경우에 발생합니다. 예를 들어 사용자 이름으로 도메인 자격 증명을 제공했지만 도메인 이름을 def.com 제공한 경우 user@abc.com 서버가 def.com 연결되어 있으면 해당 자격 증명이 시도되지 않고 이 오류 메시지가 표시됩니다.

### <a name="remediation"></a>재구성
- 어플라이언스 구성 관리자로 이동하여 서버 자격 증명을 추가하거나 원인에 설명된 대로 기존 자격 증명을 편집합니다.
- 수정 단계를 수행하면 [이 웹 사이트의](troubleshoot-dependencies.md#mitigation-verification-by-using-vmware-powercli)단계에 따라 오류가 해결되었는지 확인합니다.

## <a name="mitigation-verification-by-using-vmware-powercli"></a>VMware PowerCLI를 사용하여 완화 확인

이전 오류에 대한 완화 단계를 사용한 후 어플라이언스 서버에서 몇 가지 PowerCLI 명령을 실행하여 완화가 작동하는지 확인합니다. 명령이 성공하면 문제가 해결됨을 의미합니다. 그렇지 않으면 수정 단계를 다시 확인하고 따릅니다.

1. 다음 명령을 실행하여 어플라이언스 서버에서 PowerCLI를 설정합니다.
   ````
   Install-Module -Name VMware.PowerCLI -AllowClobber
   Set-PowerCLIConfiguration -InvalidCertificateAction Ignore
   ````
1. 명령에서 vCenter 서버 IP 주소를 제공하고 프롬프트에서 자격 증명을 제공하여 어플라이언스에서 vCenter 서버로 커넥트.
   ````
   Connect-VIServer -Server <IPAddress of vCenter Server>
   ````
1. 어플라이언스에 제공된 대로 서버 이름 및 서버 자격 증명을 제공하여 어플라이언스에서 대상 서버로 커넥트.
   ````
   $vm = get-VM <VMName>
   $credential = Get-Credential
   ````
1. 에이전트 없는 종속성 분석의 경우 다음 명령을 실행하여 성공적인 출력을 얻을 수 있는지 확인합니다.

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
1. 완화가 작동하는지 확인한 후 **Azure Migrate 프로젝트** 검색  >  **및 평가**  >  **개요**  >  어플라이언스 **관리로** 이동하여  >  **어플라이언스** 이름을 선택하고 **서비스 새로 고침을** 선택하여 새 검색 주기를 시작합니다.

## <a name="my-log-analytics-workspace-isnt-listed-when-you-try-to-configure-the-workspace-in-azure-migrate-for-agent-based-dependency-analysis"></a>에이전트 기반 종속성 분석을 위해 Azure Migrate 작업 영역을 구성하려고 하면 Log Analytics 작업 영역이 나열되지 않습니다.
Azure Migrate 현재 미국 동부, 동남 아시아 및 서유럽 지역에서 OMS 작업 영역 만들기를 지원합니다. 작업 영역이 다른 지역의 Azure Migrate 외부에서 만들어진 경우 현재 프로젝트와 연결될 수 없습니다.

## <a name="agent-based-dependency-visualization-in-azure-government"></a>Azure Government의 에이전트 기반 종속성 시각화

에이전트 기반 종속성 분석은 Azure Government에서 지원되지 않습니다. _VMware 서버 에서만 사용할 수_ 있는 에이전트 없는 종속성 분석을 사용합니다.

## <a name="agent-based-dependencies-dont-show-after-agent-installation"></a>에이전트를 설치한 후 에이전트 기반 dependencies가 표시되지 않습니다.

온-프레미스 VM에 종속성 시각화 에이전트를 설치한 후 Azure Migrate 일반적으로 포털에 종속성을 표시하는 데 15~30분이 걸립니다. 30분 넘게 대기한 경우 MMA(Microsoft Monitoring Agent)가 Log Analytics 작업 영역에 연결할 수 있는지 확인합니다.

Windows VM의 경우
1. 제어판에서 MMA를 시작합니다.
1. **Microsoft Monitoring Agent 속성**  >  **OMS(Azure Log Analytics)에서** 작업 영역의 **상태가** 녹색인지 확인합니다.
1. 상태가 녹색이 아닌 경우 작업 영역을 제거했다가 MMA에 다시 추가하세요.

    ![MMA 상태를 보여 주는 스크린샷.](./media/troubleshoot-assessment/mma-properties.png)

Linux VM의 경우 MMA 및 종속 에이전트 설치 명령이 정상적으로 실행되었는지 확인합니다. 이 웹 사이트에 대한 자세한 문제 해결 지침을 [참조하세요.](../azure-monitor/vm/service-map.md#post-installation-issues)

## <a name="supported-operating-systems-for-agent-based-dependency-analysis"></a>에이전트 기반 종속성 분석에 대해 지원되는 운영 체제

- **MMS 에이전트:** 지원되는 [Windows](../azure-monitor/agents/agents-overview.md#supported-operating-systems) 및 [Linux](../azure-monitor/agents/agents-overview.md#supported-operating-systems) 운영 체제입니다.
- **종속성 에이전트:** 지원되는 [Windows 및 Linux](../azure-monitor/vm/vminsights-enable-overview.md#supported-operating-systems) 운영 체제입니다.

## <a name="visualize-dependencies-for-1-hour-with-agent-based-dependency-analysis"></a>에이전트 기반 종속성 분석을 통해 >1시간 동안 종속성 시각화

에이전트 기반 종속성 분석을 사용하면 Azure Migrate 지난 달의 특정 날짜로 돌아갈 수 있지만 종속성을 시각화할 수 있는 최대 기간은 1시간입니다. 예를 들어 종속성 맵의 기간 기능을 사용해 어제의 종속성을 확인할 수는 있지만, 1시간 동안만 볼 수 있습니다. Azure Monitor 로그를 사용하여 더 긴 기간 동안 [종속성 데이터를 쿼리할](./how-to-create-group-machine-dependencies.md) 수 있습니다.

## <a name="visualize-dependencies-for-10-servers-with-agent-based-dependency-analysis"></a>에이전트 기반 종속성 분석을 통해 >10개 서버에 대한 종속성 시각화

Azure Migrate에서 에이전트 기반 종속성 분석을 사용하면 최대 10개의 VM이 있는 [그룹에 대한 종속성을 시각화](./how-to-create-a-group.md#refine-a-group-with-dependency-mapping)할 수 있습니다. 대규모 그룹의 경우 VM을 더 작은 그룹으로 분할하여 dependencies를 시각화합니다.

## <a name="servers-show-install-agent-for-agent-based-dependency-analysis"></a>서버에 에이전트 기반 종속성 분석을 위해 "에이전트 설치"가 표시됨

종속성 시각화를 사용하도록 설정된 서버를 Azure로 마이그레이션한 후 서버는 다음 동작으로 인해 **종속성 보기** 대신 **에이전트 설치** 작업을 표시할 수 있습니다.

- Azure로 마이그레이션한 후 온-프레미스 서버는 꺼지고 동일한 VM이 Azure에서 작동됩니다. 이러한 서버는 다른 MAC 주소를 갖습니다.
- 서버는 온-프레미스 IP 주소를 보유했는지 여부에 따라 다른 IP 주소를 가질 수도 있습니다.
- MAC 주소와 IP 주소가 온-프레미스와 다른 경우 Azure Migrate는 온-프레미스 서버를 서비스 맵 종속성 데이터와 연결하지 않습니다. 이 경우, 에이전트를 설치하는 옵션이 표시되기는 하지만, 이 옵션에서는 해당 에이전트를 볼 수 없습니다.
- Azure에 대해 테스트 마이그레이션을 수행한 후 온-프레미스 서버는 예상대로 켜진 상태를 유지합니다. Azure에서 분리된 동등한 서버는 다른 MAC 주소를 획득하고 다른 IP 주소를 획득할 수 있습니다. 이러한 서버에서 나가는 Azure Monitor 로그 트래픽을 차단하지 않는 한 Azure Migrate 온-프레미스 서버를 서비스 맵 종속성 데이터와 연결하지 않습니다. 이 경우, 에이전트를 설치하는 옵션이 표시되기는 하지만, 이 옵션에서는 해당 에이전트를 볼 수 없습니다.

## <a name="capture-network-traffic"></a>네트워크 트래픽 캡처

네트워크 트래픽 로그를 수집하려면

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. F12 키를 선택하여 개발자 도구를 시작합니다. 필요한 경우 **탐색 시 항목 지우기** 설정을 선택 취소합니다.
1. **네트워크** 탭을 선택하고 네트워크 트래픽 캡처를 시작합니다.
   - 크롬에서 **Preserve log** 를 클릭합니다. 자동으로 기록이 시작됩니다. 빨간색 원은 트래픽을 캡처하고 있다는 뜻입니다. 빨간색 원이 나타나지 않으면 시작할 검은색 원을 선택합니다.
   - Microsoft Edge 및 Internet Explorer 기록이 자동으로 시작되어야 합니다. 그렇지 않으면 녹색 재생 단추를 선택합니다.
1. 오류를 재현해 봅니다.
1. 기록하는 동안 오류가 발생한 후 기록을 중지하고 기록된 활동의 복사본을 저장합니다.
   - Chrome에서는 마우스 오른쪽 단추로 클릭하고 **Save as HAR with content(콘텐츠와 함께 HAR로 저장)** 를 선택합니다. 이 작업은 로그를 압축하고 HTTP 보관 파일(har)로 내보냅니다.
   - Microsoft Edge 또는 Internet Explorer에서 **캡처된 트래픽 내보내기** 옵션을 선택합니다. 그러면 로그가 압축되어 내보내집니다.
1. **콘솔** 탭을 선택하여 경고 또는 오류를 확인합니다. 콘솔 로그를 저장하려면:
   - 크롬의 경우 콘솔 로그에서 아무 위치를 마우스 오른쪽 단추로 클릭합니다. **다른 이름으로 저장** 을 선택하여 로그를 내보내고 압축합니다.
   - Microsoft Edge 또는 Internet Explorer에서 오류를 마우스 오른쪽 단추로 클릭하고 **모두 복사** 를 선택합니다.
1. 개발자 도구를 닫습니다.


## <a name="next-steps"></a>다음 단계

평가를 [만들거나](how-to-create-assessment.md) [사용자 지정](how-to-modify-assessment.md)합니다.
