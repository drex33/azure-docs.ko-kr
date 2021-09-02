---
title: SQL Server 버전의 현재 위치 변경
description: Azure에서 SQL Server 가상 머신의 버전을 변경하는 방법을 알아봅니다.
services: virtual-machines-windows
documentationcenter: na
author: ramakoni1
manager: ramakoni1
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/08/2020
ms.author: RamaKoni
ms.reviewer: sqlblt, daleche
ms.custom: seo-lt-2019
ms.openlocfilehash: 8428f9ce69bc58a206eb5c60b3602ffbb17f7c19
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123225678"
---
# <a name="in-place-change-of-sql-server-version-on-azure-vm"></a>Azure VM에서 SQL Server 버전의 현재 위치 변경

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

이 문서에서는 Microsoft Azure의 Windows VM(가상 머신)에서 Microsoft SQL Server의 버전을 변경하는 방법을 설명합니다.

## <a name="prerequisites"></a>사전 요구 사항

SQL Server의 현재 위치 업그레이드를 수행하려면 다음 조건이 적용됩니다.

- 원하는 SQL Server 버전의 설치 미디어가 필요합니다. [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default)가 있는 고객은 [볼륨 라이선싱 센터](https://www.microsoft.com/Licensing/servicecenter/default.aspx)에서 설치 미디어를 구할 수 있습니다. Software Assurance가 없는 고객은 최신 버전의 SQL Server가 포함된 Azure Marketplace SQL Server VM 이미지의 설치 미디어를 사용할 수 있습니다(일반적으로 C:\SQLServerFull에 있음).
- 에디션 업그레이드는 [업그레이드 경로 지원](/sql/database-engine/install-windows/supported-version-and-edition-upgrades-version-15)을 따라야 합니다.

## <a name="planning-for-version-change"></a>버전 변경 계획

버전을 변경하기 전에 다음 항목을 검토하는 것이 좋습니다.

1. 업그레이드하려는 버전의 새로운 기능을 확인합니다.

   - [SQL 2019](/sql/sql-server/what-s-new-in-sql-server-ver15)의 새로운 기능
   - [SQL 2017](/sql/sql-server/what-s-new-in-sql-server-2017)의 새로운 기능
   - [SQL 2016](/sql/sql-server/what-s-new-in-sql-server-2016)의 새로운 기능


1. 데이터베이스 호환성 모드를 사용하여 업그레이드 영향을 최소화할 수 있도록 변경할 버전에 대한 [호환성 인증](/sql/database-engine/install-windows/compatibility-certification)을 확인하는 것이 좋습니다.
1. 다음 문서를 검토하면 버전을 제대로 변경할 수 있습니다.

   - [비디오: SQL Server 현대화 | Pam Lahoud 및 Pedro Lopes | 20살이 된 PASS](https://www.youtube.com/watch?v=5RPkuQHcxxs&feature=youtu.be)
   - [AB 테스트용 데이터베이스 실험 도우미](/sql/dea/database-experimentation-assistant-overview)
   - [쿼리 튜닝 도우미를 사용하여 데이터베이스 업그레이드](/sql/relational-databases/performance/upgrade-dbcompat-using-qta)
   - [데이터베이스 호환성 수준 변경 및 쿼리 저장소 사용](/sql/database-engine/install-windows/change-the-database-compatibility-mode-and-use-the-query-store)

## <a name="upgrade-sql-version"></a>SQL 버전 업그레이드

> [!WARNING]
> SQL Server 버전을 업그레이드하면 Analysis Services 및 R Services와 같은 관련 서비스와 함께 SQL Server용 서비스가 다시 시작됩니다.

SQL Server 버전을 업그레이드하려면 SQL Server [업그레이드 경로를 지원](/sql/database-engine/install-windows/supported-version-and-edition-upgrades-version-15)하는 최신 버전의 SQL Server 설치 미디어를 구한 후 다음 단계를 수행합니다.

1. 프로세스를 시작하기 전에 시스템(tempdb 제외) 및 사용자 데이터베이스를 포함하여 데이터베이스를 백업합니다. Azure Backup 서비스를 사용하여 애플리케이션 일치 VM 수준 백업을 만들 수도 있습니다.
1. SQL Server 설치 미디어에서 Setup.exe를 시작합니다.
1. 설치 마법사가 SQL Server 설치 센터를 시작합니다. 기존 SQL Server 인스턴스를 업그레이드하려면 탐색 창에서 **설치** 를 선택한 다음, **이전 버전의 SQL Server에서 업그레이드** 를 선택합니다.

   :::image type="content" source="./media/change-sql-server-version/upgrade.png" alt-text="SQL Server 버전을 업그레이드하기 위한 선택 항목":::

1. **제품 키** 페이지에서 SQL Server 무료 에디션으로 업그레이드하는지 또는 프로덕션 버전 제품의 PID 키가 있는지 여부를 나타내는 옵션을 선택합니다. 자세한 내용은 [SQL Server 2019(15.x)의 에디션 및 지원되는 기능](/sql/sql-server/editions-and-components-of-sql-server-version-15)과 [지원되는 버전 및 에디션 업그레이드(SQL Server 2016)](/sql/database-engine/install-windows/supported-version-and-edition-upgrades)를 참조하세요.
1. **업그레이드 준비** 페이지가 표시될 때까지 **다음** 을 선택한 다음, **업그레이드** 를 선택합니다. 변경 내용이 적용되는 동안 설치 창이 몇 분 동안 응답하지 않을 수 있습니다. **완료** 페이지에서 업그레이드가 완료되었음을 확인합니다. 단계별 업그레이드 절차는 [전체 절차](/sql/database-engine/install-windows/upgrade-sql-server-using-the-installation-wizard-setup#procedure)를 참조하세요.

   :::image type="content" source="./media/change-sql-server-version/complete-page.png" alt-text="완료 페이지":::

SQL Server 버전과 에디션을 함께 변경한 경우 에디션도 업데이트하고 **포털에서 버전 및 에디션 확인** 섹션을 참조하여 SQL VM 인스턴스를 변경합니다.

   :::image type="content" source="./media/change-sql-server-version/change-portal.png" alt-text="버전 메타데이터 변경":::

## <a name="downgrade-the-version-of-sql-server"></a>SQL Server 버전 다운그레이드

SQL Server 버전을 다운그레이드하려면 SQL Server를 완전히 제거하고 원하는 버전으로 다시 설치해야 합니다. 최신 버전의 이전 데이터베이스를 새로 설치된 이전 버전으로 복원할 수 없기 때문에 SQL Server를 새로 설치하는 것과 유사합니다. 데이터베이스를 처음부터 다시 만들어야 합니다. 업그레이드 중에 SQL Server 에디션도 변경한 경우 Azure Portal에서 SQL Server VM의 **에디션** 속성을 새 에디션 값으로 변경합니다. 이렇게 하면 이 VM과 연결된 메타데이터 및 청구가 업데이트됩니다.

> [!WARNING]
> SQL Server 현재 위치 다운그레이드는 지원되지 않습니다.

다음 단계를 수행하여 SQL Server 버전을 다운그레이드할 수 있습니다.

1. [최신 버전에서만 사용할 수 있는](https://social.technet.microsoft.com/wiki/contents/articles/24222.find-enterprise-only-features-in-your-database.aspx) 기능을 사용하고 있도록 합니다.
1. 시스템(tempdb 제외) 및 사용자 데이터베이스를 포함하여 모든 데이터베이스를 백업합니다.
1. 필요한 서버 수준 개체(예: 서버 트리거, 역할, 로그인, 연결된 서버, 작업, 자격 증명, 인증서)를 모두 내보냅니다.
1. 이전 버전에서 사용자 데이터베이스를 다시 만드는 스크립트가 없는 경우 모든 개체를 스크립팅하고 BCP.exe, SSIS 또는 DACPAC를 사용하여 모든 데이터를 내보내야 합니다.

   대상 버전, 종속 개체, 고급 옵션과 같은 항목을 스크립팅할 때는 올바른 옵션을 선택해야 합니다.

   :::image type="content" source="./media/change-sql-server-version/scripting-options.png" alt-text="스크립팅 옵션":::

1. SQL Server와 모든 연결된 서비스를 완전히 제거합니다.
1. VM을 다시 시작합니다.
1. 원하는 프로그램 버전을 위한 미디어를 사용하여 SQL Server를 설치합니다.
1. 최신 서비스 팩과 누적 업데이트를 설치합니다.
1. 3단계에서 내보낸 필요한 서버 수준 개체를 모두 가져옵니다.
1. 4단계에서 만든 스크립트 또는 파일을 사용하여 필요한 사용자 데이터베이스를 처음부터 모두 다시 만듭니다.

## <a name="verify-the-version-and-edition-in-the-portal"></a>포털에서 버전 및 에디션 확인

SQL Server 버전을 변경한 후에는 Azure Portal을 사용하여 SQL Server 버전을 볼 수 있도록 [SQL IaaS 에이전트 확장](sql-agent-extension-manually-register-single-vm.md)에 SQL Server VM을 다시 등록합니다. 그러면 나열된 버전 번호에 새로 업그레이드된 SQL Server 설치 버전 및 에디션이 반영됩니다.

:::image type="content" source="./media/change-sql-server-version/verify-portal.png" alt-text="버전 확인":::

> [!NOTE]
> SQL IaaS 에이전트 확장에 이미 등록한 경우 VM에 설치된 SQL Server 버전 및 에디션을 올바르게 검색하도록 [RP에서 등록 취소](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension)한 다음, [SQL VM 리소스 등록](sql-agent-extension-manually-register-single-vm.md#full-mode)을 다시 수행합니다. 이렇게 하면 이 VM과 연결된 메타데이터 및 청구 정보가 업데이트됩니다.

## <a name="remarks"></a>설명

- 업그레이드가 완료되면 백업/통계 업데이트/인덱스 다시 빌드/일관성 확인을 시작하는 것이 좋습니다. 개별 데이터베이스 호환성 수준을 확인하여 원하는 수준을 반영할 수도 있습니다.
- VM의 SQL Server가 업데이트되면 Azure Portal에서 SQL Server의 **에디션** 속성이 청구를 위해 설치된 에디션 번호와 일치하는지 확인합니다.
- [에디션 변경](change-sql-server-edition.md#change-edition-in-portal) 기능은 SQL IaaS 에이전트 확장의 기능입니다. Azure Portal을 통해 Azure Marketplace 이미지를 배포하면 SQL Server VM이 자동으로 확장에 등록됩니다. 그러나 SQL Server를 직접 설치하는 고객은 수동으로 [SQL Server VM을 등록](sql-agent-extension-manually-register-single-vm.md)해야 합니다.
- SQL Server VM 리소스를 삭제하면 이미지의 하드 코드된 에디션 설정이 복원됩니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조하세요.

- [Windows VM에서 SQL Server 개요](sql-server-on-azure-vm-iaas-what-is-overview.md)
- [Windows VM의 SQL Server FAQ](frequently-asked-questions-faq.yml)
- [Windows VM의 SQL Server 가격 책정 가이드](pricing-guidance.md)
- [Windows VM의 SQL Server 릴리스 정보](doc-changes-updates-release-notes.md)