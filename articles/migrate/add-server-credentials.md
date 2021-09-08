---
title: 소프트웨어 인벤토리, 종속성, 웹앱, SQL Server 인스턴스 및 데이터베이스를 검색하기 위한 서버 자격 증명 제공
description: 어플라이언스 구성 관리자에서 서버 자격 증명을 제공하는 방법을 알아봅니다.
author: vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/18/2021
ms.openlocfilehash: 2128145e0f2efa7a443c7a0c972117f29de73765
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2021
ms.locfileid: "122965416"
---
# <a name="provide-server-credentials-to-discover-software-inventory-dependencies-web-apps-and-sql-server-instances-and-databases"></a>소프트웨어 인벤토리, 종속성, 웹앱, SQL Server 인스턴스 및 데이터베이스를 검색하기 위한 서버 자격 증명 제공

이 문서에 따라 어플라이언스 구성 관리자에서 소프트웨어 인벤토리(설치된 애플리케이션 검색)를 수행하고, 에이전트 없는 종속성을 분석하며, 웹앱 및 SQL Server 인스턴스와 데이터베이스를 검색하는 여러 서버 자격 증명을 추가하는 방법에 대해 알아봅니다.

[Azure Migrate 어플라이언스](migrate-appliance.md)는 Azure Migrate에서 사용하는 경량 어플라이언스이며 VMware 환경에서 실행되는 온-프레미스 서버를 검색하고 Azure에 서버 구성 및 성능 메타데이터를 전송하기 위한 검색 및 평가를 수행합니다. 또한 어플라이언스를 사용하여 소프트웨어 인벤토리를 수행하고, 에이전트 없는 종속성을 분석하며, 웹앱 및 SQL Server 인스턴스와 데이터베이스를 검색할 수 있습니다.

이러한 기능을 사용하려면 아래 단계에 따라 서버 자격 증명을 제공할 수 있습니다. 어플라이언스는 검색 기능을 수행하기 위해 자격 증명을 서버에 자동으로 매핑하려고 시도합니다.

## <a name="add-credentials-for-servers-running-in-vmware-environment"></a>VMware 환경에서 실행 중인 서버에 대한 자격 증명 추가

### <a name="types-of-server-credentials-supported"></a>지원되는 서버 자격 증명 유형

어플라이언스 구성 관리자에서 도메인, 비도메인(Windows 또는 Linux) 또는 SQL Server 인증 자격 증명 등 여러 서버 자격 증명을 추가할 수 있습니다.

지원되는 서버 자격 증명 유형은 아래 표에 나와 있습니다.

자격 증명 유형 | Description
--- | ---
**도메인 자격 증명** | **자격 증명 추가** 모달 드롭다운의 옵션을 선택하여 **도메인 자격 증명** 을 추가할 수 있습니다. <br/><br/> 도메인 자격 증명을 제공하려면 FQDN 형식(예: prod.corp.contoso.com)으로 제공되어야 하는 **도메인 이름** 을 지정해야 합니다. <br/><br/> 또한 자격 증명, 사용자 이름 및 암호에 대해 익숙한 이름을 지정해야 합니다. <br/><br/> 추가된 도메인 자격 증명은 도메인의 Active Directory에 대해 신뢰성이 있는지 자동으로 검사됩니다. 이는 어플라이언스가 검색된 서버에 대해 도메인 자격 증명을 매핑하려고 할 때 계정 잠금을 방지하기 위한 것입니다. <br/><br/>어플라이언스에서 도메인 컨트롤러를 사용하여 도메인 자격 증명의 유효성을 검사하려면 도메인 이름을 확인할 수 있어야 합니다. 자격 증명을 추가하는 동안 올바른 도메인 이름을 제공했는지 확인하세요. 그러지 않으면 유효성 검사가 실패합니다.<br/><br/> 어플라이언스는 유효성 검사에 실패한 도메인 자격 증명을 매핑하려고 시도하지 않습니다. 소프트웨어 인벤토리를 계속 진행하려면 유효성 검사에 성공한 도메인 자격 증명이 하나 이상 있거나 비도메인 자격 증명이 하나 이상 있어야 합니다.<br/><br/>Windows 서버에 대해 자동으로 매핑된 도메인 자격 증명은 소프트웨어 인벤토리를 수행하는 데 사용되며, 웹앱 및 SQL Server 인스턴스와 데이터베이스를 검색하는 데에도 사용될 수 있습니다 _(SQL Server에서 Windows 인증 모드를 구성한 경우)_ .<br/> SQL Server에서 지원되는 인증 모드의 유형에 대해 [자세히 알아보세요](/dotnet/framework/data/adonet/sql/authentication-in-sql-server) .
**비도메인 자격 증명(Windows/Linux)** | **자격 증명 추가** 모달의 드롭다운에서 필요한 옵션을 선택하여 **Windows(비도메인)** 또는 **Linux(비도메인)** 를 추가할 수 있습니다. <br/><br/> 자격 증명, 사용자 이름 및 암호에 대해 익숙한 이름을 지정해야 합니다.
**SQL Server 인증 자격 증명** | **자격 증명 추가** 모달 드롭다운의 옵션을 선택하여 **SQL Server 인증** 자격 증명을 추가할 수 있습니다. <br/><br/> 자격 증명, 사용자 이름 및 암호에 대해 익숙한 이름을 지정해야 합니다. <br/><br/> SQL Server에서 SQL Server 인증 모드를 구성한 경우, 이 유형의 자격 증명을 추가하여 VMware 환경에서 실행 중인 SQL Server 인스턴스 및 데이터베이스를 검색할 수 있습니다.<br/> SQL Server에서 지원되는 인증 모드의 유형에 대해 [자세히 알아보세요](/dotnet/framework/data/adonet/sql/authentication-in-sql-server) .<br/><br/> SQL Server 인증 자격 증명을 사용하여 SQL Server 인스턴스와 데이터베이스를 검색하기 전에 어플라이언스에서 소프트웨어 인벤토리를 완료하여 서버에 설치된 SQL을 검색할 수 있게 하려면 유효성이 확인된 도메인 자격 증명이나 Windows(비도메인) 자격 증명을 하나 이상 제공해야 합니다.

소프트웨어 인벤토리를 수행하고, 에이전트 없는 종속성을 분석하며, 웹앱 및 SQL Server 인스턴스와 데이터베이스를 검색하기 위해 Windows/Linux 자격 증명에 필요한 권한을 확인합니다.

### <a name="required-permissions"></a>필요한 사용 권한

아래 표에는 해당 기능을 수행하기 위해 어플라이언스에 제공된 서버 자격 증명에 필요한 권한이 있습니다.

기능 | Windows 자격 증명 | Linux 자격 증명
---| ---| ---
**소프트웨어 인벤토리** | 게스트 사용자 계정 | 일반/일반 사용자 계정(비sudo 액세스 권한)
**SQL Server 인스턴스 및 데이터베이스 검색** | Sysadmin 서버 역할의 구성원인 사용자 계정입니다. | _현재 지원되지 않음_
**ASP.NET 웹앱 검색** | 관리 권한이 있는 도메인 또는 비도메인(로컬) 계정 | _현재 지원되지 않음_
**에이전트 없는 종속성 분석** | 관리 권한이 있는 도메인 또는 비도메인(로컬) 계정 | 루트 사용자 계정 또는 <br/> /bin/netstat 및 /bin/ls 파일에 대해 CAP_DAC_READ_SEARCH 및 CAP_SYS_PTRACE 권한이 있는 계정.<br/><br/> 다음 명령을 사용하여 이러한 기능을 설정합니다. <br/><br/> sudo setcap CAP_DAC_READ_SEARCH,CAP_SYS_PTRACE=ep /bin/ls<br/><br/> sudo setcap CAP_DAC_READ_SEARCH,CAP_SYS_PTRACE=ep /bin/netstat

### <a name="recommended-practices-to-provide-credentials"></a>권장하는 자격 증명 제공 방법

- [필수 권한](add-server-credentials.md#required-permissions)으로 전용 도메인 사용자 계정을 만드는 것이 좋습니다. 그러면 소프트웨어 인벤토리를 수행하고, 에이전트 없는 종속성을 분석하며, 웹앱과 원하는 서버에 있는 SQL Server 인스턴스 및 데이터베이스를 검색하도록 범위가 지정됩니다.
- 소프트웨어 인벤토리를 시작하려면 유효성 검사에 성공한 도메인 자격 증명이나 비도메인 자격 증명을 하나 이상 제공하는 것이 좋습니다.
- SQL Server에서 Windows 인증 모드를 구성한 경우, SQL Server 인스턴스와 데이터베이스를 검색하기 위해 도메인 자격 증명을 제공할 수 있습니다.
- SQL Server 인증 모드를 SQL Server에 구성한 경우 SQL Server 인증 자격 증명을 제공할 수 있지만, 어플라이언스에서 먼저 소프트웨어 인벤토리를 완료할 수 있도록 유효성 검사가 완료된 도메인 자격 증명이나 Windows(비도메인) 자격 증명을 하나 이상 제공하는 것이 좋습니다.

## <a name="credentials-handling-on-appliance"></a>어플라이언스의 자격 증명 처리

- 어플라이언스 구성 관리자에서 제공되는 모든 자격 증명은 어플라이언스 서버에 로컬로 저장되고 Azure로 보내지 않습니다.
- 어플라이언스 서버에 저장된 자격 증명은 DPAPI(데이터 보호 API)를 사용하여 암호화됩니다.
- 자격 증명을 추가한 다음에는 어플라이언스에서 자격 증명을 자동으로 매핑하여 개별 서버에서 검색을 수행하려고 합니다.
- 이 어플라이언스는 자격 증명에서 필요한 검색 데이터를 가져올 수 있을 때까지 모든 후속 검색 주기에 대해 서버에서 자동으로 매핑되는 자격 증명을 사용합니다. 자격 증명의 작동이 중지되면 어플라이언스는 추가된 자격 증명 목록에서 다시 매핑하여 서버에서 검색을 계속 시도합니다.
- 추가된 도메인 자격 증명은 도메인의 Active Directory에 대해 신뢰성이 있는지 자동으로 검사됩니다. 이는 어플라이언스가 검색된 서버에 대해 도메인 자격 증명을 매핑하려고 할 때 계정 잠금을 방지하기 위한 것입니다. 어플라이언스는 유효성 검사에 실패한 도메인 자격 증명을 매핑하려고 시도하지 않습니다.
- 이 어플라이언스에서 서버에 대해 도메인 또는 비도메인 자격 증명을 매핑할 수 없는 경우 프로젝트의 서버에 대해 "자격 증명을 사용할 수 없음" 상태가 표시됩니다.

## <a name="next-steps"></a>다음 단계

[VMware 환경에서 실행 중인 서버 검색](tutorial-discover-vmware.md)에 대한 자습서를 검토합니다.