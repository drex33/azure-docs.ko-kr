---
title: Azure 계산 갤러리 (미리 보기)의 VM 응용 프로그램 개요
description: Azure Compute 갤러리에서 VM 응용 프로그램 패키지에 대해 자세히 알아보세요.
author: cynthn
ms.service: virtual-machines
ms.subservice: gallery
ms.topic: conceptual
ms.workload: infrastructure
ms.date: 11/02/2021
ms.author: cynthn
ms.reviewer: amjads
ms.custom: ''
ms.openlocfilehash: b43d2c5f20afa67c40115430903b97d49fef4e08
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131482899"
---
# <a name="vm-applications-overview-preview"></a>VM 응용 프로그램 개요 (미리 보기)

VM 응용 프로그램은 가상 컴퓨터용 응용 프로그램의 관리, 공유 및 글로벌 배포를 간소화 하는 Azure Compute 갤러리 (이전의 공유 이미지 갤러리)의 리소스 유형입니다.

> [!IMPORTANT]
> **Azure 계산 갤러리의 VM 응용 프로그램** 은 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 권장되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.


앱이 사전 설치 된 VM의 이미지를 만들 수 있지만 응용 프로그램을 변경할 때마다 이미지를 업데이트 해야 합니다. VM 이미지에서 응용 프로그램 설치를 분리 하면 모든 코드 줄 변경에 대해 새 이미지를 게시할 필요가 없습니다.

응용 프로그램 패키지는 다른 배포 및 패키징 방법에 대 한 이점을 제공 합니다.

- 패키지 그룹화 및 버전 관리

- VM 응용 프로그램은 인프라와 긴밀 하 게 복제할 수 있으므로 AzCopy 또는 다른 저장소 복사 메커니즘을 사용 하 여 Azure 지역 간에 비트를 복사할 필요가 없습니다.

- Azure 역할 기반 Access Control (RBAC)을 통해 다른 사용자와 공유

- 가상 컴퓨터에 대 한 지원 및 유연 하 고 균일 한 확장 집합 모두 지원

- VM 또는 확장 집합에 NSG (네트워크 보안 그룹) 규칙이 적용 된 경우 인터넷 리포지토리에서 패키지를 다운로드 하는 것이 불가능할 수 있습니다. 저장소 계정을 사용 하 여 패키지를 잠긴 Vm으로 다운로드 하려면 개인 링크를 설정 해야 합니다.
- VM 응용 프로그램은 [Deployifnotexists](/azure/governance/policy/concepts/effects) 정책과 함께 사용할 수 있습니다.


## <a name="what-are-vm-app-packages"></a>VM 앱 패키지 란?

VM 응용 프로그램 패키지는 여러 리소스 종류를 사용 합니다.

| 리소스 | Description|
|----------|------------|
| **Azure compute 갤러리** | 갤러리는 응용 프로그램 패키지를 관리 하 고 공유 하기 위한 리포지토리입니다. 사용자는 갤러리 리소스를 공유할 수 있으며 모든 자식 리소스는 자동으로 공유 됩니다. 갤러리 이름은 구독 마다 고유 해야 합니다. 예를 들어 모든 OS 이미지를 저장 하는 갤러리와 모든 VM 응용 프로그램을 저장 하는 갤러리 하나가 있을 수 있습니다.|
| **VM 응용 프로그램** | VM 응용 프로그램의 정의입니다. 그 아래에 있는 모든 버전에 대 한 공통 메타 데이터를 저장 하는 *논리적* 리소스입니다. 예를 들어 Apache Tomcat에 대 한 응용 프로그램 정의가 있고 그 안에 여러 버전이 있을 수 있습니다. |
| **VM 응용 프로그램 버전** | 배포할 수 있는 리소스입니다. Vm 응용 프로그램 버전을 VM 인프라에 가까운 대상 영역에 전역적으로 복제할 수 있습니다. VM 응용 프로그램 버전을 해당 지역의 VM에 배포 하려면 먼저 해당 지역에 복제 해야 합니다. |


## <a name="limitations"></a>제한 사항

- **지역별 복제본이 3** 개이 하: VM 응용 프로그램 버전을 만들 때 지역 당 최대 복제본 수는 3입니다. 

- **실패** 한 설치 다시 시도: 현재 실패 한 설치를 다시 시도 하는 유일한 방법은 프로필에서 응용 프로그램을 제거한 다음 다시 추가 하는 것입니다.

- **Vm 당 5 개의 응용** 프로그램만: 언제 든 지 vm에 5 개 이하의 응용 프로그램을 배포할 수 있습니다.

- **1gb 응용 프로그램 크기**: 응용 프로그램 버전의 최대 파일 크기는 1gb입니다. 

- **스크립트를** 다시 부팅 해야 하는 것은 아닙니다. 스크립트를 다시 부팅 해야 하는 경우 배포 중에 응용 프로그램을 마지막에 배치 하는 것이 좋습니다. 코드에서 다시 부팅을 처리 하려고 하면 실패할 수 있습니다.

- Vm **에이전트가 필요** 합니다. vm 에이전트가 vm에 존재 하 고 목표 상태를 받을 수 있어야 합니다.

- 동일한 **vm에 있는 여러 버전의 동일한 응용 프로그램**: vm에서 동일한 응용 프로그램의 여러 버전을 사용할 수 없습니다.


## <a name="cost"></a>Cost

VM 응용 프로그램 패키지를 사용 하는 데 추가 요금이 부과 되지 않지만 다음 리소스에 대 한 요금이 청구 됩니다.

- 각 패키지 및 복제본을 저장 하는 데 드는 비용을 Storage 합니다. 
- 이미지 버전을 원본 지역에서 복제된 지역으로 처음 복제할 때 발생하는 네트워크 송신 요금. 후속 복제본은 지역 내에서 처리되므로 추가 비용이 없습니다. 

네트워크 송신에 대한 자세한 내용은 [대역폭 가격 책정](https://azure.microsoft.com/pricing/details/bandwidth/)을 참조하세요.


## <a name="vm-applications"></a>VM 응용 프로그램

Vm 응용 프로그램 리소스는 VM 응용 프로그램에 대해 다음을 정의 합니다.

- VM 응용 프로그램이 저장 되는 Azure Compute 갤러리
- 애플리케이션의 이름입니다.
- Linux 또는 Windows와 같은 지원 되는 OS 유형
- VM 응용 프로그램에 대 한 설명

## <a name="vm-application-versions"></a>VM 응용 프로그램 버전

VM 응용 프로그램 버전은 배포 가능한 리소스입니다. 버전은 다음 속성으로 정의 됩니다.
- 버전 번호
- 저장소 계정의 응용 프로그램 패키지 파일에 대 한 링크
- 응용 프로그램을 설치 하기 위한 설치 문자열
- 앱을 적절 하 게 제거 하는 방법을 표시 하려면 문자열을 제거 합니다.
- VM에 다운로드 될 때 사용할 패키지 파일 이름
- VM에서 앱을 구성 하는 데 사용할 구성 파일 이름입니다.
- VM 응용 프로그램의 구성 파일에 대 한 링크
- VM 응용 프로그램을 최신 버전으로 업데이트 하는 방법에 대 한 업데이트 문자열
- 수명 종료 날짜입니다. 수명 종료 날짜는 정보를 제공 합니다. 계속 해 서 수명 종료 날짜 이후에 VM 응용 프로그램 버전을 배포할 수 있습니다.
- 최신 항목에서 제외. 최신 버전의 응용 프로그램으로 사용 되는 버전을 유지할 수 있습니다. 
- 복제를 위한 대상 영역
- 지역별 복제본 수

## <a name="download-directory"></a>다운로드 디렉터리 
 
응용 프로그램 패키지 및 구성 파일의 다운로드 위치는 다음과 같습니다.
  
- Linux: `/var/lib/waagent/Microsoft.CPlat.Core.VMApplicationManagerLinux/<appname>/<app version> `
- Windows: `C:\Packages\Plugins\Microsoft.CPlat.Core.VMApplicationManagerWindows\1.0.4\Downloads\<appname>\<app version> `


응용 프로그램 패키지와 구성 파일이 현재 디렉터리에 있다고 가정 하 고 설치/업데이트/제거 명령을 작성 해야 합니다.

## <a name="file-naming"></a>파일 이름 지정

미리 보기 중에 응용 프로그램 파일이 VM에 다운로드 될 때 파일 이름은 VM 응용 프로그램을 만들 때 사용 하는 이름과 동일 합니다. 예를 들어 내 VM 응용 프로그램의 이름을 지정 하는 경우 `myApp` `myApp` 저장소 계정에서 사용 되는 파일 이름에 관계 없이 vm에 다운로드 되는 파일의 이름도 지정 됩니다. VM 응용 프로그램에도 구성 파일이 있는 경우 해당 파일은 추가 된의 응용 프로그램 이름입니다 `_config` . `myApp`에 구성 파일이 있으면 이름이로 지정 됩니다 `myApp_config` .

예를 들어 갤러리에서 만들 때 VM 응용 프로그램의 이름을 `myApp` 로 지정할 때 저장소 계정에로 저장 되는 경우 `myApplication.exe` vm에 다운로드 될 때 파일 이름은가 됩니다 `myApp` . 설치 문자열은 VM에서 실행 해야 하는 모든 항목 (예: myApp.exe)으로 이름을 변경 하 여 시작 해야 합니다.

설치, 업데이트 및 제거 명령을 염두에 두어야 합니다. 
 
## <a name="command-interpreter"></a>명령 인터프리터  

기본 명령 인터프리터는 다음과 같습니다.
- Linux: `/bin/sh` 
- Windows: `cmd.exe`

실행 파일을 호출 하 고 명령을 전달 하 여 컴퓨터에 설치 된 경우에는 다른 인터프리터를 사용할 수 있습니다. 예를 들어 명령을 cmd 대신 Windows PowerShell에서 실행 하려면 다음을 전달 하면 됩니다.`powershell.exe -Command '<powershell commmand>'`
  

## <a name="how-updates-are-handled"></a>업데이트를 처리 하는 방법

응용 프로그램 버전을 업데이트 하면 배포 중에 제공한 업데이트 명령이 사용 됩니다. 업데이트 된 버전에 업데이트 명령이 없으면 현재 버전이 제거 되 고 새 버전이 설치 됩니다. 

업데이트 명령은 이전 버전의 VM 응용 프로그램에서 업데이트 될 수 있다고 가정 하 고 작성 해야 합니다.


## <a name="tips-for-creating-vm-applications-on-linux"></a>Linux에서 VM 응용 프로그램을 만들기 위한 팁 

Linux 용 타사 응용 프로그램은 몇 가지 방법으로 패키지할 수 있습니다. 가장 일반적인 몇 가지에 대 한 설치 명령 생성을 처리 하는 방법을 살펴보겠습니다.

### <a name="tar-and-gz-files"></a>tar 및 release.tar.gz 파일 

압축된 보관 파일이며 원하는 위치로 추출할 수 있습니다. 특정 위치에 추출해야 하는 경우 원래 패키지의 설치 지침을 확인합니다. .tar.gz 파일에 소스 코드가 포함된 경우 소스에서 설치하는 방법에 대한 패키지 지침을 참조하세요. 

Linux 컴퓨터에 설치하는 명령을 `golang` 설치하는 예제:

```bash
tar -C /usr/local -xzf go_linux
```

예제 remove 명령:

```bash
rm -rf /usr/local/go
```

### <a name="deb-rpm-and-other-platform-specific-packages"></a>.deb, .rpm 및 기타 플랫폼별 패키지 
플랫폼별 패키지 관리자에 대한 개별 패키지를 다운로드할 수 있지만 일반적으로 일부 의존성만 포함되어 있지는 않습니다. 이러한 파일의 경우 애플리케이션 패키지에 모든 의존성도 포함하거나 시스템 패키지 관리자가 VM에서 사용할 수 있는 리포지토리를 통해 해당 애플리케이션을 다운로드하도록 해야 합니다. 인터넷 액세스가 제한된 VM으로 작업하는 경우 모든 dependencies를 직접 패키지해야 합니다.


의존성을 파악하는 것은 약간 어려울 수 있습니다. 전체 종속성 트리를 표시할 수 있는 타사 도구가 있습니다. 

Ubuntu에서 를 `apt-get install <name> --simulate` 실행하여 명령에 대해 설치할 모든 패키지를 표시할 수 `apt-get install <name>` 있습니다. 그런 다음 해당 출력을 사용하여 모든 .deb 파일을 다운로드하여 애플리케이션 패키지로 사용할 수 있는 보관 파일을 만들 수 있습니다. 이 방법의 단점은 가 VM에 이미 설치된 dependencies를 표시하지 않는다는 것입니다.
 
예를 들어 Ubuntu용 PowerShell을 설치하는 VM 애플리케이션 패키지를 만들려면 `apt-get install powershell --simulate` 새 Ubuntu VM에서 명령을 실행합니다. 줄의 출력을 **확인합니다. 다음 패키지를** 나열하는 다음 새 패키지가 설치됩니다.
- `liblttng-ust-ctl4` 
- `liblttng-ust0` 
- `liburcu6` 
- `powershell`. 

를 사용하여 이러한 파일을 `apt-get download` 다운로드하고 루트 수준의 모든 파일이 있는 tar 보관 파일을 만듭니다. 이 tar 보관 파일은 애플리케이션 패키지 파일이 됩니다. 이 경우 install 명령은 다음과 같습니다.

```bash
tar -xf powershell && dpkg -i ./liblttng-ust-ctl4_2.10.1-1_amd64.deb ./liburcu6_0.10.1-1ubuntu1_amd64.deb ./liblttng-ust0_2.10.1-1_amd64.deb ./powershell_7.1.2-1.ubuntu.18.04_amd64.deb
```

그리고 remove 명령은 다음과 같습니다.

```bash
dpkg -r powershell && apt autoremove
```

`apt autoremove`모든 dependencies를 명시적으로 제거하는 대신 를 사용합니다. 겹치는 의존성으로 다른 애플리케이션을 설치했을 수 있으며, 이 경우 명시적 제거 명령이 실패합니다.


직접 의존 관계를 해결하지 않고 apt/rpm이 리포지토리에 연결할 수 있는 경우 .deb/.rpm 파일을 하나만 사용하여 애플리케이션을 설치하고 apt/rpm이 해당 dependencies를 처리하도록 할 수 있습니다.

설치 명령 예제:

```bash
dpkg -i <appname> || apt --fix-broken install -y
```
 
## <a name="tips-for-creating-vm-applications-on-windows"></a>Windows VM 애플리케이션을 만들기 위한 팁 

Windows 대부분의 타사 애플리케이션은 .exe 또는 .msi 설치 관리자로 사용할 수 있습니다. 일부는 zip 파일 추출 및 실행으로도 사용할 수 있습니다. 각 모범 사례를 살펴보겠습니다.


### <a name="exe-installer"></a>.exe 설치 관리자 

설치 관리자 실행 파일은 일반적으로 UI(사용자 인터페이스)를 시작하고 누군가가 UI를 클릭해야 합니다. 설치 관리자가 자동 모드 매개 변수를 지원하는 경우 설치 문자열에 포함되어야 합니다. 

또한 Cmd.exe 확장명이 .exe 실행 파일이 필요하므로 확장명이 te .exe 파일의 이름을 바꿔야 합니다.  

실행 파일로 제공하는 myApp.exe 대한 VM 애플리케이션 패키지를 만들려면 내 VM 애플리케이션을 'myApp'이라고 하므로 애플리케이션 패키지가 현재 디렉터리에 있다고 가정하여 명령을 작성합니다.

```
"move .\\myApp .\\myApp.exe & myApp.exe /S -config myApp_config" 
```
 
설치 관리자 실행 파일에서 제거 매개 변수를 지원하지 않는 경우 테스트 컴퓨터에서 레지스트리를 조회하여 제거 프로그램이 있는지 확인할 수 있습니다. 

레지스트리에서 제거 문자열은 에 `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall\<installed application name>\UninstallString` 저장되므로 콘텐츠를 remove 명령으로 사용합니다.

```
'\"C:\\Program Files\\myApp\\uninstall\\helper.exe\" /S'
```

### <a name="msi-installer"></a>.msi 설치 관리자 

설치 관리자의 명령줄 실행의 경우 `.msi` 애플리케이션을 설치하거나 제거하는 명령은 를 사용해야 `msiexec` 합니다. 일반적으로 는 `msiexec` 별도의 프로세스로 실행되며 `cmd` 완료되기를 기다리지 않으므로 두 개 이상의 VM 애플리케이션을 설치할 때 문제가 발생할 수 있습니다.  `start`명령을 와 함께 사용하여 `msiexec` 명령이 반환되기 전에 설치가 완료되도록 할 수 있습니다. 예를 들어:

```
start /wait %windir%\\system32\\msiexec.exe /i myapp /quiet /forcerestart /log myapp_install.log
```

예제 remove 명령:

```
start /wait %windir%\\system32\\msiexec.exe /x $appname /quiet /forcerestart /log ${appname}_uninstall.log
```

### <a name="zipped-files"></a>압축된 파일 

.zip 또는 다른 압축된 파일의 경우 애플리케이션 패키지 내용의 압축을 원하는 대상에 압축을 풀면됩니다. 

설치 명령 예제:

```
mkdir C:\\myapp && powershell.exe -Command \"Expand-Archive -Path myapp -DestinationPath C:\\myapp\" 
```

예제 remove 명령:

```
rmdir /S /Q C:\\myapp
```


## <a name="troubleshooting-during-preview"></a>미리 보기 중 문제 해결

미리 보기 중에 VM 애플리케이션 확장은 설치/업데이트/제거 중에 VM 앱이 실패했는지 여부에 관계없이 항상 성공을 반환합니다. VM 애플리케이션 확장은 확장 또는 기본 인프라에 문제가 있는 경우에만 확장 상태를 실패로 보고합니다. 특정 VM 애플리케이션이 VM 인스턴스에 성공적으로 추가되었는지 여부를 확인하려면 VMApplication 확장의 메시지를 확인하세요.

VM 확장의 상태를 확인하는 방법에 대한 자세한 내용은 [Windows 대한 가상 머신 확장 및 기능을](extensions/features-windows.md#view-extension-status)참조하세요.

VM 확장의 상태를 얻으려면 [Get-AzVM을](/powershell/module/az.compute/get-azvm)사용합니다.

```azurepowershell-interactive
Get-AzVM -name <VMSS name> -ResourceGroupName <resource group name> -InstanceView | convertto-json  
```

VMSS 확장의 상태를 얻으려면 [Get-AzVMSS를](/powershell/module/az.compute/get-azvmss)사용합니다.

```azurepowershell-interactive
Get-AzVmss -name <VMSS name> -ResourceGroupName <resource group name> -InstanceView | convertto-json  
```

## <a name="error-messages"></a>오류 메시지

| 메시지 | Description |
|--|--|
| 현재 VM 애플리케이션 버전 {name}은 {date}에서 더 이상 사용되지 않습니다. | 이미 사용되지 않는 VM 애플리케이션 버전을 배포하려고 했습니다. 특정 `latest` 버전을 지정하는 대신 를 사용해 보세요. |
| 현재 VM 애플리케이션 버전 {name}은 OS {OS}를 지원하지만 현재 OSDisk의 OS는 {OS}입니다. | Windows 인스턴스에 Linux 애플리케이션을 배포하거나 그 반대로 배포하려고 했습니다. |
| 최대 VM 애플리케이션 수(max=5, current={count})를 초과했습니다. 더 적은 수의 애플리케이션을 사용하고 요청을 다시 시도합니다. | 현재 VM 또는 VMSS당 5개의 VM 애플리케이션만 지원합니다. |
| 동일한 packageReferenceId를 통해 두 개 이상의 VMApplication이 지정되었습니다. | 동일한 애플리케이션이 두 번 이상 지정되었습니다. |
| 이 이미지에 액세스할 권한이 없는 구독입니다. | 구독이 이 애플리케이션 버전에 액세스할 수 없습니다. |
| 인수에 Storage 계정이 없습니다. | 이 구독에 대한 애플리케이션이 없습니다. |
| 플랫폼 이미지 {image}를 사용할 수 없습니다. 스토리지 프로필의 모든 필드가 올바른지 확인합니다. 스토리지 프로필 정보에 대한 자세한 내용은 을 https://aka.ms/storageprofile 참조하세요. | 응용 프로그램이 없습니다. |
| 갤러리 이미지 {image}는 {region} 지역에서 사용할 수 없습니다. 이미지 소유자에게 문의하여 이 지역에 복제하거나 요청된 지역을 변경하세요. | 갤러리 애플리케이션 버전이 있지만 이 지역에 복제되지 않았습니다. |
| SAS는 원본 uri {uri}에 유효하지 않습니다. | `Forbidden`URL(mediaLink 또는 defaultConfigurationLink)에 대한 정보를 검색하려고 할 때 스토리지에서 오류가 발생했습니다. |
| 원본 uri {uri}에서 참조하는 Blob이 없습니다. | mediaLink 또는 defaultConfigurationLink 속성에 대해 제공된 Blob이 없습니다. |
| 다음 오류로 인해 갤러리 애플리케이션 버전 URL {url}에 액세스할 수 없습니다. 원격 이름을 찾을 수 없습니다. Blob이 존재하고 공개적으로 액세스할 수 있는지 또는 읽기 권한이 있는 SAS URL인지 확인합니다. | 가장 가능성이 높은 경우는 읽기 권한이 있는 SAS URI가 제공되지 않은 것입니다. |
| {오류 설명} 오류로 인해 갤러리 애플리케이션 버전 URL {url}에 액세스할 수 없습니다. Blob이 존재하고 공개적으로 액세스할 수 있는지 또는 읽기 권한이 있는 SAS URL인지 확인합니다. | 제공된 스토리지 Blob에 문제가 있었습니다. 오류 설명은 자세한 정보를 제공합니다. |
| {application}이(가) 삭제되도록 표시되어 있기 때문에 {operationName} 작업은 허용되지 않습니다. 삭제 작업을 다시 시도(하거나 실행 중인 작업이 완료되기를 기다림)할 수 있습니다. | 현재 삭제 중인 애플리케이션을 업데이트하려고 시도합니다. |
| 'galleryApplicationVersion.properties.publishingProfile.replicaCount' 매개 변수의 {value} 값이 범위를 벗어났습니다. 값은 1에서 3 사이여야 합니다(포함). | VMApplication 버전에는 1~3개의 복제본만 허용됩니다. |
| 'galleryApplicationVersion.properties.publishingProfile.manageActions.install' 속성을 변경할 수 없습니다. (또는 업데이트, 삭제) | 기존 VmApplication에서 관리 작업을 변경할 수 없습니다. 새 VmApplication 버전을 만들어야 합니다. |
| 속성 ' galleryApplicationVersion.properties.publishingProfile.settings.packageFileName '을 변경할 수 없습니다. (또는 configFileName) | 패키지 파일 이름 또는 구성 파일 이름과 같은 설정을 변경할 수 없습니다. 새 VmApplication 버전을 만들어야 합니다. |
| 원본 uri {uri}에서 참조 하는 blob이 너무 큽니다. 크기 = {size}. 허용 되는 최대 blob 크기는 ' 1gb '입니다. | MediaLink 또는 defaultConfigurationLink에서 참조 하는 blob의 최대 크기는 현재 1gb입니다. |
| 소스 uri {uri}에서 참조 하는 blob이 비어 있습니다. | 빈 blob이 참조 되었습니다. |
| {type} blob 유형은 {operation} 작업에 지원 되지 않습니다. 페이지 blob과 블록 blob만 지원 됩니다. | VmApplications는 페이지 blob 및 블록 blob만 지원 합니다. |
| SAS가 원본 uri {uri}에 적합 하지 않습니다. | MediaLink 또는 defaultConfigurationLink에 제공 된 SAS uri가 올바른 SAS uri가 아닙니다. |
| 구독에 필요한 기능 {featureName}이 (가) 없기 때문에 대상 지역에서 {region}을 (를) 지정할 수 없습니다. 필요한 기능을 사용 하 여 구독을 등록 하거나 대상 지역 목록에서 해당 지역을 제거 하십시오. | 특정 제한 된 지역에서 VmApplications를 사용 하려면 해당 구독에 대해 기능 플래그가 등록 되어 있어야 합니다. |
| 갤러리 이미지 버전 게시 프로필 지역 {regions}은 (는) 이미지 버전 {location}의 위치를 포함 해야 합니다. | 복제용 지역 목록에는 응용 프로그램 버전이 있는 위치가 포함 되어야 합니다. |
| 대상 게시 영역에서는 중복 된 영역을 사용할 수 없습니다. | 게시 영역에 중복 항목이 없을 수 있습니다. |
| 갤러리 응용 프로그램 버전 리소스는 현재 암호화를 지원 하지 않습니다. | 대상 지역의 암호화 속성은 VM 응용 프로그램에 대해 지원 되지 않습니다. |
| 엔터티 이름이 요청 URL의 이름과 일치 하지 않습니다. | 요청 url에 지정 된 갤러리 응용 프로그램 버전이 요청 본문에 지정 된 것과 일치 하지 않습니다. |
| 갤러리 응용 프로그램 버전 이름이 잘못 되었습니다. 응용 프로그램 버전 이름은 주 (int32)를 따라야 합니다. Minor (int32). Patch (int32) 형식으로, 여기서 int는 0에서 2147483647 (포함) 사이입니다. 예: 1.0.0, 2018.12.1 등 | 갤러리 응용 프로그램 버전은 지정 된 형식을 따라야 합니다. |



## <a name="next-steps"></a>다음 단계

- [VM 응용 프로그램 패키지를 만들고 배포](vm-applications-how-to.md)하는 방법을 알아봅니다.


