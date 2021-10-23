---
title: SAP HANA Backup용 Azure Backup 아키텍처
description: SAP HANA 백업을 위한 Azure Backup 아키텍처에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 09/27/2021
ms.openlocfilehash: df8bb6adfeff88eafba19bcc459f887f075aa75b
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130244581"
---
# <a name="azure-backup-architecture-for-sap-hana-backup"></a>SAP HANA 백업에 대한 Azure Backup 아키텍처

[Azure Backup 서비스를](./backup-overview.md) 사용하면 일관된 방식으로 SAP HANA 데이터베이스의 데이터를 백업할 수 있습니다. 이 문서에서는 Azure Backup 아키텍처 구성 요소 및 프로세스에 대해 설명합니다.

## <a name="how-does-azure-backup-work-with-sap-hana-databases"></a>Azure Backup SAP HANA 데이터베이스에서 어떻게 작동하나요?

Azure Backup Azure VM에서 실행되는 SAP HANA 데이터베이스를 백업하는 스트리밍 백업 솔루션을 제공합니다. 이 백업 제품에는 인프라가 없는 설정이 필요하므로 백업 인프라를 배포하고 관리할 필요가 없습니다.

Azure Backup SAP에서 [인증된 Backint이며](https://www.sap.com/dmc/exp/2013_09_adpd/enEN/#/d/solutions?id=8f3fd455-a2d7-4086-aa28-51d8870acaa5) SAP HANA 네이티브 API를 사용하여 네이티브 백업 지원을 제공합니다. 이 솔루션을 사용하면 Azure VM에서 실행되는 SAP HANA 데이터베이스를 원활하게 백업 및 복원하고 Azure Backup 제공하는 엔터프라이즈 관리 기능을 사용할 수 있습니다.

SAP HANA 위해 Azure Backup 제공하는 추가 값에 대해 [자세히 알아봅니다.](./sap-hana-db-about.md#added-value)

## <a name="where-is-the-data-backed-up"></a>데이터가 백업되는 위치는 어디인가요?

Azure Backup Recovery Services 자격 증명 모음에 백업된 데이터를 저장합니다. 자격 증명 모음은 백업 복사본, 복구 지점 및 백업 정책과 같은 데이터를 저장하는 데 사용되는 Azure의 온라인 스토리지 엔터티입니다.

Recovery Services 자격 증명 모음에 대해 [자세히 알아보세요.](./backup-azure-backup-faq.yml)

## <a name="backup-agents"></a>백업 에이전트

Azure VM에서 실행되는 SAP HANA 데이터베이스를 백업하려면 Azure VM에 플러그 인(SAP HANA Backup 에이전트)을 설치하도록 허용해야 합니다. 이 플러그 인은 HANA Backint와 연결되며 Azure Backup 서비스에서 데이터를 자격 증명 모음으로 이동하는 데 도움이 됩니다. 또한 Azure Backup 복원을 수행할 수 있습니다.

## <a name="backup-types"></a>Backup 유형

SAP HANA 백업 유형에 대해 [알아봅니다.](./backup-architecture.md#sap-hana-backup-types)

## <a name="about-architecture"></a>아키텍처 정보

SAP HANA [데이터베이스에 대한 Azure Backup](./sap-hana-db-about.md#backup-architecture)아키텍처를 참조하세요. 백업 프로세스에 대한 자세한 내용은 다음 프로세스를 참조하세요.

:::image type="content" source="./media/sap-hana-db-about/backup-architecture.png" alt-text="SAP HANA 데이터베이스의 백업 프로세스를 보여 주는 다이어그램":::

1. 백업 프로세스를 시작하려면 [Azure에서 Recovery Services 자격 증명 모음을 만듭니다.](./tutorial-backup-sap-hana-db.md#create-a-recovery-services-vault) 이 자격 증명 모음은 시간에 따라 생성된 백업 및 복구 지점을 저장하는 데 사용됩니다.

1. SAP HANA 서버를 실행하는 Azure VM이 자격 증명 모음에 등록되고 백업할 데이터베이스가 [검색됩니다.](./tutorial-backup-sap-hana-db.md#discover-the-databases) Azure Backup 서비스에서 데이터베이스를 검색할 수 있도록 하려면 HANA 서버에서 이 [사전 등록 스크립트를](https://go.microsoft.com/fwlink/?linkid=2173610) 루트 사용자로 실행해야 합니다. 
   >[!Note]
   >이 인스턴스에서 데이터베이스를 검색하는 동안 HANA 인스턴스가 실행 중인지 확인합니다.

1. 또한 다른 필수 구성 조건이 충족되는지 [확인합니다.](./tutorial-backup-sap-hana-db.md#prerequisites)

   >[!Important]
   >올바른 네트워크 연결을 설정하기 위한 필수 구성 조건이 충족되는지 확인합니다. 백업 제품을 [사용하기 위해 추가 네트워크 구성 요소를 사용하여 SAP HANA 실행되는 Azure VM을 설정하는 방법에](./tutorial-backup-sap-hana-db.md#set-up-network-connectivity)대한 권장 사항을 참조하세요.

1. 사전 등록 [스크립트가](./tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does)수행하는 작업을 참조하세요. 이 스크립트를 실행하지 않고 SAP HANA 데이터베이스에 대한 백업을 구성하려고 하면 _UserErrorHanaScriptNotRun_ 오류가 발생할 수 있습니다.

1. 이제 Azure Backup 서비스는 등록된 SAP HANA 서버에 HANA용 Azure Backup 플러그 인을 설치합니다. 이 플러그 인은 사전 등록 스크립트에서 만든 Backup 사용자를 사용하여 모든 백업 및 복원 작업을 수행합니다.

1. 검색된 데이터베이스에서 [백업을 구성하려면](./tutorial-backup-sap-hana-db.md#configure-backup) 필요한 백업 정책을 선택하고 백업을 사용하도록 설정합니다.

1. SAP HANA 대한 Azure Backup(Backint 인증 솔루션)는 기본 디스크 또는 VM 유형에 따라 달라지지 않습니다. 백업은 SAP HANA 생성된 스트림에 의해 수행됩니다.

## <a name="backup-flow"></a>백업 흐름

1. 예약된 백업은 HANA VM에서 만든 crontab 항목에 의해 관리되는 반면 주문형 백업은 Azure Backup 서비스에 의해 직접 트리거됩니다.

1. SAP HANA 백업 엔진/Backint가 백업 요청을 받으면 저장 지점을 만들고 데이터를 기본 스토리지 볼륨으로 이동하여 백업을 위해 SAP HANA 데이터베이스를 준비합니다.

1. 그런 다음 Backint는 기본 데이터 볼륨(테넌트 데이터베이스에 대한 인덱스 서버 및 XS 엔진 및 SYSTEMDB의 이름 서버)에서 읽기 작업을 실행합니다. Premium SSD 디스크는 백업 스트리밍 작업에 대한 최적의 I/O 처리량을 제공할 수 있습니다. 그러나 M64Is와 함께 캐시되지 않은 디스크를 사용하면 속도가 더 빨라질 수 있습니다.

1. 백업 데이터를 스트리밍하기 위해 Backint는 Azure Backup Recovery Services 자격 증명 모음에 직접 쓰는 최대 3개의 파이프를 만듭니다.

   설치에서 방화벽/NVA를 사용하지 않는 경우 백업 스트림이 Azure 네트워크를 통해 Recovery Services 자격 증명 모음으로 전송됩니다. 또한 SAP HANA 백업 트래픽을 Azure Storage 직접 보낼 수 있도록 Virtual Network [서비스 엔드포인트](../virtual-network/virtual-network-service-endpoints-overview.md) 또는 [프라이빗 엔드포인트를](../private-link/private-endpoint-overview.md) 설정하여 NVA/Azure Firewall 건너뛸 수 있습니다. 또한 방화벽/NVA를 사용하는 경우 Azure Active Directory 및 Recovery Services 자격 증명 모음에 대한 트래픽이 방화벽/NVA를 통과하며 전체 백업 성능에 영향을 미치지 않습니다. 

1. Azure Backup 비 로그 백업의 경우 최대 420MB/초, 로그 백업의 경우 최대 100MB/초의 속도를 달성하려고 시도합니다. 백업 및 복원 처리량 성능에 대해 [자세히 알아보세요.](./tutorial-backup-sap-hana-db.md#understanding-backup-and-restore-throughput-performance)

1. 자세한 로그는 SAP HANA 인스턴스의 _backup.log_ 및 _backint.log_ 파일에 기록됩니다.

1. 백업 스트리밍이 완료되면 카탈로그가 Recovery Services 자격 증명 모음으로 스트리밍됩니다. 백업(전체/차등/증분/로그) 및 이 백업의 카탈로그가 성공적으로 스트리밍되어 Recovery Services 자격 증명 모음에 저장되면 Azure Backup 백업 작업이 성공했다고 간주합니다.

다음 SAP HANA 설정을 참조하고 위에서 언급한 백업 작업의 실행을 참조하세요.

**SAP HANA 설정 시나리오: NVA/Azure Firewall 없이 Azure 네트워크**

:::image type="content" source="./media/azure-backup-architecture-for-sap-hana-backup/azure-network-without-nva-or-azure-firewall.png" alt-text="NVA/Azure Firewall 없는 Azure 네트워크의 경우 SAP HANA 설정을 보여 주는 다이어그램":::

**SAP HANA 설정 시나리오: Azure 네트워크 - UDR + NVA/Azure Firewall**

:::image type="content" source="./media/azure-backup-architecture-for-sap-hana-backup/azure-network-with-udr-and-nva-or-azure-firewall.png" alt-text="UDR + NVA/Azure Firewall 있는 Azure 네트워크인 경우 SAP HANA 설정을 보여 주는 다이어그램":::

>[!Note]
>NVA/Azure Firewall Azure Storage/Recovery Services 자격 증명 모음(데이터 평면)에 스트림 백업을 SAP HANA 때 오버헤드가 추가됩니다. 위의 다이어그램에서 _점 6을_ 참조하세요.

**SAP HANA 설정 시나리오: Azure 네트워크 - UDR + NVA/ Azure Firewall + 프라이빗 엔드포인트 또는 서비스 엔드포인트 사용**

:::image type="content" source="./media/azure-backup-architecture-for-sap-hana-backup/azure-network-with-udr-and-nva-or-azure-firewall-and-private-endpoint-or-service-endpoint.png" alt-text="UDR + NVA / Azure Firewall + 프라이빗 엔드포인트 또는 서비스 엔드포인트가 있는 Azure 네트워크인 경우 SAP HANA 설정을 보여 주는 다이어그램.":::

## <a name="next-steps"></a>다음 단계

[Azure VM에서 SAP HANA 데이터베이스를 백업합니다.](./backup-azure-sap-hana-database.md)
