---
title: Azure Database for MySQL - 유연한 서버의 새로운 기능
description: MySQL Community Edition을 기반으로 하는 Microsoft 클라우드의 관계형 데이터베이스 서비스인 Azure Database for MySQL - 유연한 서버에 대한 최신 업데이트에 관해 알아봅니다.
author: hjtoland3
ms.service: mysql
ms.author: jtoland
ms.custom: mvc, references_regions
ms.topic: conceptual
ms.date: 09/29/2021
ms.openlocfilehash: 4a74fb34eb750924f9d8c08225edd10a5a220084
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129657857"
---
# <a name="whats-new-in-azure-database-for-mysql---flexible-server-preview"></a>Azure Database for MySQL - 유연한 서버의 새로운 기능(미리 보기)

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

[Azure Database for MySQL - 유연한 서버](./overview.md#azure-database-for-mysql---flexible-server-preview)는 단일 서버 배포 모드가 제공하는 것보다 데이터베이스 관리 기능 및 구성 설정에 대해 보다 세부적인 제어와 유연성을 제공하도록 설계된 배포 모드입니다. 이 서비스는 현재 MySQL 커뮤니티 버전 5.7 및 8.0을 지원합니다.

이 문서에서는 2021년 1월부터 Azure Database for MySQL - 유연한 서버의 새로운 릴리스 및 기능을 요약합니다. 목록은 가장 최근의 업데이트가 먼저 있는 역방향 시간순으로 표시됩니다.

## <a name="october-2021"></a>2021년 10월

- **읽기 복제본을 만들 때 선택 항목 가용성 영역**

    읽기 복제본을 만들 때 선택한 가용성 영역 위치를 선택할 수 있습니다. 가용성 영역은 데이터 센터 오류로부터 애플리케이션 및 데이터를 보호하는 고가용성 제품입니다. 가용성 영역은 Azure 지역 내의 고유한 물리적 위치입니다. [자세히 알아보기](../flexible-server/concepts-read-replicas.md).

- **Azure Database for MySQL 읽기 복제본 - 유연한 서버는 버스트 가능한 S SKU에서 더 이상 사용할 수 없습니다.**
    
    버스트 가능 계층 서버에서는 새 읽기 복제본을 만들거나 기존 읽기 복제본을 유지할 수 없습니다. 버스트 가능한 SKU 계층에 대한 좋은 쿼리 및 개발 환경을 제공하기 위해 버스트 가능 가격 책정 계층의 서버에 대한 읽기 복제본을 만들고 유지 관리하는 지원이 중단될 예정입니다. 

    읽기 복제본을 사용하도록 설정된 기존 Azure Database for MySQL 유연한 서버가 있는 경우 범용 또는 메모리 최적화 가격 책정 계층으로 서버를 확장하거나 60일 이내에 읽기 복제본을 삭제해야 합니다. 60일이 지나면 읽기/쓰기 작업에 주 서버를 계속 사용할 수 있지만 복제본 서버를 읽는 복제가 중지됩니다. 새로 만든 서버의 경우 읽기 복제본 옵션은 범용 및 메모리 최적화 가격 책정 계층에만 사용할 수 있습니다.  

 - **모니터링 Azure Database for MySQL - Azure Monitor 통합 문서를 사용하는 유연한 서버**
 
     Azure Database for MySQL - 유연한 서버는 이제 Azure Monitor 통합 문서와 통합됩니다. 통합 문서는 Azure Portal 내에서 데이터를 분석하고 풍부한 시각적 보고서를 생성할 수 있는 유연한 캔버스를 제공합니다. 이 통합을 통해 서버에는 통합 문서에 대한 링크와 서비스를 대규모로 모니터링하는 데 도움이 되는 몇 가지 샘플 템플릿이 있습니다. 이러한 템플릿을 편집하고, 고객 요구 사항에 맞게 사용자 지정하고, 대시보드에 고정하여 Azure 리소스에 대한 집중적이고 구성된 보기를 만들 수 있습니다. [쿼리 성능 Insights](./tutorial-query-performance-insights.md), [감사](./tutorial-configure-audit.md)및 인스턴스 개요 템플릿은 현재 사용할 수 있습니다. [자세히 알아보기](./concepts-workbooks.md).

- **예약 인스턴스를 Azure Database for MySQL 컴퓨팅 리소스에 대한 선불** Azure Database for MySQL - 유연한 서버는 이제 종량제 가격에 비해 컴퓨팅 리소스에 대한 선불로 비용을 절감할 수 있습니다. Azure Database for MySQL 예약 인스턴스를 사용하면 1년 또는 3년 동안 MySQL 서버에서 사전 약정을 통해 컴퓨팅 비용을 대폭 할인할 수 있습니다. Azure Database for MySQL - 유연한 서버가 있는 단일 서버에서 예약을 교환할 수도 있습니다. [자세히 알아보기](../concept-reserved-pricing.md).

## <a name="september-2021"></a>2021년 9월

이 Azure Database for MySQL - 유연한 서버 릴리스에는 다음과 같은 업데이트가 포함되어 있습니다.

- **세 개의 추가 Azure 지역에서 가용성**

   Azure Database for MySQL - 유연한 서버의 공개 미리 보기는 이제 다음 Azure 지역에서 사용할 수 있습니다.

   - 영국 서부
   - 캐나다 동부
   - 일본 서부

- **버그 수정**

   동일한 영역 HA 생성은 다음 지역에서 수정됩니다.

   - 인도 중부
   - 동아시아
   - 한국 중부
   - 남아프리카 북부
   - 스위스 북부

## <a name="august-2021"></a>2021년 8월

이 Azure Database for MySQL - 유연한 서버 릴리스에는 다음과 같은 업데이트가 포함되어 있습니다.

- **동일한 영역의 고가용성을 사용하는 단일 영역 내 고가용성**

  이제 서비스는 고객이 고가용성을 사용하도록 설정할 때 대기 서버에 대한 기본 가용성 영역을 유연하게 선택할 수 있도록 합니다. 이 기능을 사용하면 고객은 기본 서버와 동일한 영역에 대기 서버를 배치하여 기본 서버와 대기 서버 간의 복제 지연 시간을 줄일 수 있습니다. 또한 동일한 Azure 영역 내에 배치되는 경우 애플리케이션 서버와 데이터베이스 서버 간의 대기 시간을 줄일 수 있습니다. [자세히 알아보기](/azure/mysql/flexible-server/concepts-high-availability#same-zone-high-availability).

- **영역 중복 고가용성을 사용하는 대기 영역 선택**

  이제 이 서비스를 사용하여 고객은 대기 서버 영역 위치를 선택할 수 있습니다. 고객은 이 기능을 사용하여 대기 서버를 선택한 영역에 배치할 수 있습니다. 대기 데이터베이스 서버와 대기 애플리케이션을 동일한 영역에 배치하면 대기 시간이 줄어들고 고객이 재해 복구 상황 및 "영역 축소" 시나리오에 더 잘 대비할 수 있습니다. [자세히 알아보기](/azure/mysql/flexible-server/concepts-high-availability#standby-zone-selection).

- **프라이빗 DNS 영역 통합**

  [Azure 프라이빗 DNS](../../dns/private-dns-privatednszone.md)는 가상 네트워크에 대해 안정적이고 안전한 DNS 서비스(서비스 이름을 IP 주소로 변환)를 제공합니다. Azure Private DNS는 사용자 지정 DNS 솔루션을 구성할 필요 없이 가상 네트워크의 도메인 이름을 관리하고 확인합니다. 이렇게 하면 가상 네트워크에서 실행되는 애플리케이션을 로컬 또는 전역적으로 피어된 가상 네트워크에서 실행되는 유연한 서버에 연결할 수 있습니다. 이제 Azure Database for MySQL - 유연한 서버는 Azure 프라이빗 DNS 영역과 통합하도록 하여 현재 VNet 내의 프라이빗 DNS 또는 프라이빗 DNS 영역이 연결된 피어된 VNet을 원활하게 확인할 수 있도록 합니다. 이 통합을 통해 장애 조치(failover) 또는 다른 이벤트 중에 백 엔드 유연한 서버의 IP 주소가 변경되면 통합된 프라이빗 DNS 영역이 자동으로 업데이트되어 서버가 온라인 상태일 때 애플리케이션 연결이 자동으로 다시 시작됩니다. [자세히 알아보기](./concepts-networking-vnet.md).

- **지정된 가상 네트워크의 서버에 대한 지정 시간 복원**

  이제 서비스에 대한 지정 시간 복원 환경을 통해 고객은 네트워킹 설정을 구성할 수 있으므로 사용자가 복원 작업을 수행할 때 프라이빗 및 퍼블릭 네트워킹 옵션 간을 전환할 수 있습니다. 이 기능을 통해 고객은 연결 엔드포인트를 보호하면서 지정된 가상 네트워크에 복원되는 서버를 유연하게 삽입할 수 있습니다. [자세히 알아보기](./how-to-restore-server-portal.md).

- **가용성 영역의 서버에 대한 지정 시간 복원**

  이제 서비스에 대한 지정 시간 복원 환경을 통해 고객은 가용성 영역을 구성할 수 있으며, 동일한 영역에 데이터베이스 서버와 대기 애플리케이션을 공동 배치하면 대기 시간이 줄어들고 고객이 재해 복구 상황 및 "영역 축소" 시나리오에 더 잘 대비할 수 있습니다. [자세히 알아보기](/azure/mysql/flexible-server/concepts-high-availability#standby-zone-selection).

- **프라이빗 미리 보기에서 사용할 수 있는 validate_password 및 caching_sha2_password 플러그 인**

  유연한 서버는 이제 프라이빗 미리 보기에서 validate_password 및 caching_sha2_password 플러그 인을 사용하도록 지원합니다. 으로 이메일을 보내주세요. AskAzureDBforMySQL@service.microsoft.com

- **4개의 추가 Azure 지역의 가용성**

   Azure Database for MySQL - 유연한 서버의 공개 미리 보기는 이제 다음 Azure 지역에서 사용할 수 있습니다.

   - 오스트레일리아 남동부
   - 남아프리카 북부
   - 동아시아(홍콩)
   - 인도 중부

   [자세히 알아보기](overview.md#azure-regions).

- **알려진 문제**

   - ssl_mode VERIFY_IDENTITY에서 SSL을 사용하는 경우 영역 중복 고가용성 서버 장애 조치 직후에 클라이언트는 서버에 연결하지 못합니다. VERIFY_CA에서 ssl_mode를 사용하여 이 문제를 완화할 수 있습니다.
   - 인도 중부, 동아시아, 한국 중부, 남아프리카 공화국 북부, 스위스 북부 지역에서는 동일한 영역의 고가용성 서버를 만들 수 없습니다.
   - 드문 시나리오에서 HA 장애 조치 후 주 서버는 read_only 모드가 됩니다. 서버 매개 변수 블레이드에서 "read_only" 값을 OFF로 업데이트하여 이 문제를 해결합니다.
   - 컴퓨팅+스토리지 블레이드에서 컴퓨팅을 스케일 인한 후에 IOPS가 SKU 기본값으로 다시 설정됩니다. 고객은 컴퓨팅 배포 후 Compute+Storage 블레이드의 IOPS를 원하는 값(이전에 설정)으로 다시 설정하고 그에 따라 IOPS를 다시 설정하여 이 문제를 해결할 수 있습니다.

## <a name="july-2021"></a>2021년 7월

이 Azure Database for MySQL - 유연한 서버 릴리스에는 다음과 같은 업데이트가 포함되어 있습니다.

- **단일 서버에서 유연한 서버로 온라인 마이그레이션**

  이제 고객은 입력 데이터 복제를 사용하여 애플리케이션의 가동 중지 시간을 최소화하면서 Azure Database for MySQL – 단일 서버의 인스턴스를 유연한 서버로 마이그레이션할 수 있습니다. 자세한 단계별 지침은 [가동 중지 시간을 최소화하면서 Azure Database for MySQL – 단일 서버를 유연한 서버로 마이그레이션](../howto-migrate-single-flexible-minimum-downtime.md)을 참조하세요.

- **미국 서부 및 독일 중서부의 가용성**

  Azure Database for MySQL - 유연한 서버의 퍼블릭 미리 보기를 이제 미국 서부 및 독일 중서부 Azure 지역에서 사용할 수 있습니다.

## <a name="june-2021"></a>2021년 6월

이 Azure Database for MySQL - 유연한 서버 릴리스에는 다음과 같은 업데이트가 포함되어 있습니다.

- **더 작은 스토리지 서버에서 성능 향상**

    2021년 6월 21일부터 새로 만든 모든 서버에 대해 허용되는 프로비저닝된 최소 스토리지 크기가 5GB에서 20GB로 늘어납니다. 또한 사용 가능한 무료 IOPS도 100에서 300으로 늘어납니다. 이러한 변경 내용은 다음 표에 요약되어 있습니다.

    | **Current** | **2021년 6월 21일 현재** |
    |:----------|:----------|
    | 허용되는 최소 스토리지 크기: 5GB | 허용되는 최소 스토리지 크기: 20GB |
    | IOPS 사용 가능: 최대(100, 3 * [프로비저닝된 스토리지(GB)]) | IOPS 사용 가능: (300 + 3 * [프로비저닝된 스토리지(GB)]) |

- **12개월 무료 제공**

  2021년 6월 15일부터 [Azure 무료 계정](https://azure.microsoft.com/free/)은 고객에게 매월 750시간의 사용량과 32GB의 스토리지를 포함하는 Azure Database for MySQL – 유연한 서버에 최대 12개월 동안 무료로 액세스할 수 있는 혜택을 제공합니다. 고객은 이 제공 서비스를 활용하여 Azure Database for MySQL – 유연한 서버를 사용하는 애플리케이션을 개발하고 배포할 수 있습니다. [자세히 알아보기](./how-to-deploy-on-azure-free-account.md).

- **스토리지 자동 증가**

  자동 증가 Storage 서버에서 저장소를 모두 실행 하 고 읽기 전용으로 설정 하는 것을 방지 합니다. 스토리지 자동 증가를 사용하도록 설정하면 워크로드에 영향을 주지 않고 스토리지가 자동으로 증가합니다. 2021 년 6 월 21 일부 터 새로 만든 모든 서버는 기본적으로 저장소 자동 증가를 사용 하도록 설정 합니다. [자세히 알아보기](concepts-compute-storage.md#storage-auto-grow).

- **입력 데이터 복제**

    유연한 서버는 이제 [입력 데이터 복제](concepts-data-in-replication.md)를 지원합니다. 이 기능을 사용하여 온-프레미스, 가상 머신, Azure Database for MySQL 단일 서버 또는 Azure 외부의 데이터베이스 서비스에서 실행 중인 MySQL 서버에서 Azure Database for MySQL- 유연한 서버로 데이터를 동기화하고 마이그레이션할 수 있습니다. [입력 데이터 복제 구성 방법](how-to-data-in-replication.md)에 대해 알아봅니다.

- **Azure CLI를 통한 GitHub 작업 지원**

  이제 유연한 서버 CLI를 통해 고객은 워크플로를 자동화하여 GitHub 작업이 있는 업데이트를 배포할 수 있습니다. 이 기능은 MySQL GitHub 작업 워크플로를 통해 데이터베이스 업데이트를 설정하고 배포하는 데 도움이 됩니다. 이러한 CLI 명령은 편리한 개발을 위해 지속적인 배포를 지원하도록 리포지토리를 설정하는 데 유용합니다. [자세히 알아보기](/cli/azure/mysql/flexible-server/deploy?view=azure-cli-latest&preserve-view=true).

- **영역 중복 HA 강제 장애 조치 수정**

  이 릴리스에는 서버 매개 변수 및 추가 IOPS 변경 내용이 장애 조치가 수행되어도 유지되도록 하는 강제 장애 조치와 관련된 알려진 문제의 수정 사항이 포함되어 있습니다.

- **알려진 문제**

   - 프로비저닝된 스토리지가 20GB 미만인 기존 서버에서 컴퓨팅 스케일 업 또는 스케일 다운 작업을 수행하려고 하면 제대로 완료되지 않습니다. 프로비저닝된 스토리지를 20GB로 확장하고 컴퓨팅 스케일링 작업을 다시 시도하여 문제를 해결하세요.

## <a name="may-2021"></a>2021년 5월

이 Azure Database for MySQL - 유연한 서버 릴리스에는 다음과 같은 업데이트가 포함되어 있습니다.

- **확장 지역 가용성(프랑스 중부, 브라질 남부 및 스위스 북부)**

    Azure Database for MySQL - 유연한 서버의 퍼블릭 미리 보기를 이제 프랑스 중부, 브라질 남부 및 스위스 북부 지역에서 사용할 수 있습니다. [자세히 알아보기](overview.md#azure-regions).

- **SSL/TLS 1.2 적용을 사용하지 않도록 설정할 수 있음**

   이 릴리스에서는 SSL 및 최소 TLS 버전의 적용을 사용자 지정할 수 있는 향상된 유연성을 제공합니다. 자세한 내용은 [암호화된 연결을 사용하여 Azure Database for MySQL - 유연한 서버에 연결](how-to-connect-tls-ssl.md)을 참조하세요.

- **영국 남부 및 일본 동부 지역에서 영역 중복 HA를 사용할 수 있음**

   이제 Azure Database for MySQL - 유연한 서버는 영국 남부 및 일본 동부의 두 추가 지역에서 영역 중복 고가용성을 제공합니다. [자세히 알아보기](overview.md#azure-regions).

- **알려진 문제**

   - 영역 중복 HA 지원 서버에는 추가 IOP 변경 내용이 적용되지 않습니다. 고객은 HA를 사용하지 않도록 설정하고 영역 중복 HA를 다시 사용하도록 설정하여 문제를 해결할 수 있습니다.
   - 강제 장애 조치 후에는 대기 가용성 영역이 포털에 정확하게 반영되지 않습니다. (해결 방법 없음)
   - 강제 장애 조치 후에는 서버 매개 변수 변경 내용이 영역 중복 HA 지원 서버에 적용되지 않습니다. (해결 방법 없음)

## <a name="april-2021"></a>2021년 4월

이 Azure Database for MySQL - 유연한 서버 릴리스에는 다음과 같은 업데이트가 포함되어 있습니다.

- **영역 중복 고가용성이 해제된 상태로 대기 서버에 강제로 장애 조치하는 기능**

  고객은 이제 수동으로 강제 장애 조치하여 사용자의 애플리케이션 시나리오에서 기능을 테스트함으로써 중단 상황을 대비할 수 있습니다. [자세히 알아보기](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/forced-failover-for-azure-database-for-mysql-flexible-server/ba-p/2280671).

- **유연한 서버용 PowerShell 모듈이 출시됨**

  개발자는 이제 PowerShell을 사용하여 MySQL 유연한 서버와 종속 리소스를 프로비저닝, 관리, 운영 및 지원할 수 있습니다. [자세히 알아보기](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/introducing-the-mysql-flexible-server-powershell-module/ba-p/2203383).

- **Azure CLI를 사용하여 쿼리 연결, 테스트 및 실행**

  Azure Database for MySQL 유연한 서버는 이제 고객이 Azure CLI에서 "az mysql flexible-server connect" 및 "az mysql flexible-server execute" 명령을 사용하여 서버에 연결하고 쿼리를 실행할 수 있도록 하는 향상된 개발자 환경을 제공합니다. [자세히 알아보기](connect-azure-cli.md#view-all-the-arguments).

- **프라이빗 액세스를 사용하여 가상 네트워크에서 서버를 만들 때 발생하는 프로비저닝 오류에 대한 수정 사항**

  가상 네트워크에서 서버를 만들 때 발생하는 모든 프로비저닝 오류가 수정되었습니다. 이 릴리스에서 사용자는 매번 프라이빗 액세스를 사용하여 유연한 서버를 성공적으로 만들 수 있습니다.  

## <a name="march-2021"></a>2021년 3월

이 Azure Database for MySQL - 유연한 서버 릴리스에는 다음과 같은 업데이트가 포함되어 있습니다.

- **MySQL 8.0.21이 릴리스됨**

  이제 MySQL 8.0.21을 모든 주요 [Azure 지역](overview.md#azure-regions)의 유연한 서버에서 사용할 수 있습니다. 고객은 Azure Portal, Azure CLI 또는 Azure Resource Manager 템플릿을 사용하여 MySQL 8.0.21 릴리스를 프로비저닝할 수 있습니다. [자세히 알아보기](quickstart-create-server-portal.md#create-an-azure-database-for-mysql-flexible-server).

- **서버 생성 동안의 가용성 영역 배치 지원이 릴리스됨**

  이제 고객은 서버를 만들 때 기본 가용성 영역을 지정할 수 있습니다. 이 기능을 통해 고객은 Azure VM, 가상 머신 확장 집합 또는 AKS에 호스트된 애플리케이션과 데이터베이스를 동일한 가용성 영역에 함께 배치하여 데이터베이스 대기 시간을 최소화하고 성능을 향상시킬 수 있습니다. [자세히 알아보기](quickstart-create-server-portal.md#create-an-azure-database-for-mysql-flexible-server).

- **프라이빗 액세스를 사용하여 가상 네트워크에서 유연한 서버를 실행할 때 발생하는 문제에 대한 성능 수정 사항**

  이 릴리스 이전에는 가상 네트워크 구성에서 실행하는 경우 유연한 서버의 성능이 크게 저하되었습니다. 이 릴리스에는 해당 문제에 대한 수정 사항이 포함되어 있으므로 사용자는 가상 네트워크의 유연한 서버에서 향상된 성능을 볼 수 있습니다.

- **알려진 문제**

   - SSL\TLS 1.2은 적용되며 사용하지 않도록 설정할 수 없습니다. (해결 방법 없음)
   - VNet에 프로비저닝된 서버에 대해 프로비저닝 오류가 간헐적으로 발생합니다. 해결 방법은 성공할 때까지 서버 프로비저닝을 다시 시도하는 것입니다.

## <a name="february-2021"></a>2021년 2월

이 Azure Database for MySQL - 유연한 서버 릴리스에는 다음과 같은 업데이트가 포함되어 있습니다.

- **추가 IOPS 기능이 릴리스됨**

  Azure Database for MySQL – 유연한 서버는 프로비저닝된 스토리지와는 별개로 추가 [IOPS](concepts-compute-storage.md#iops)의 프로비저닝을 지원합니다. 고객은 이 기능을 사용하여 워크 로드 요구 사항에 따라 언제든지 IOPS 수를 늘리거나 줄일 수 있습니다.

- **알려진 문제**

  Azure Database for MySQL – 유연한 서버 성능은 프라이빗 액세스 가상 네트워크 격리에 따라 저하됩니다(해결 방법 없음).

## <a name="january-2021"></a>2021년 1월

이 Azure Database for MySQL - 유연한 서버 릴리스에는 다음과 같은 업데이트가 포함되어 있습니다.

- **MySQL - 유연한 서버에 대한 최대 10개의 읽기 복제본**

  이제 유연한 서버는 동일한 지역에 있는 한 Azure Database for MySQL 서버 (' 원본 ')에서 최대 10 개의 Azure Database for MySQL 서버 (' 복제본 ')로의 비동기 데이터 복제를 지원 합니다. 이 기능을 사용하면 읽기 작업이 많은 워크로드가 스케일 아웃되고 사용자의 기본 설정에 따라 복제본 서버 사이에서 다시 부하 분산될 수 있습니다. [자세히 알아보기](concepts-read-replicas.md).

## <a name="contacts"></a>연락처

Azure Database for MySQL 작업에 대한 질문이 있거나 제안 사항이 있으면 다음 연락 지점으로 연락해 보세요.

- Azure 고객 지원팀에 문의하려면 [Azure Portal에서 티켓을 제출](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하세요.
- 계정 관련 문제를 해결하려면 Azure Portal에서 [지원 요청](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)을 제출합니다.
- 사용자 의견을 제공 하거나 새로운 기능을 요청 하려면으로 메일을 보내세요 AskAzureDBforMySQL@service.microsoft.com .

## <a name="next-steps"></a>다음 단계

- [Azure Database for MySQL 가격 책정](https://azure.microsoft.com/pricing/details/mysql/server/)에 대해 자세히 알아봅니다.
- Azure Database for MySQL - 유연한 서버에 대한 [공용 설명서](index.yml)를 찾아보세요.
- [일반적인 마이그레이션 오류 문제 해결](../howto-troubleshoot-common-errors.md)에 대한 세부 정보를 검토합니다.
