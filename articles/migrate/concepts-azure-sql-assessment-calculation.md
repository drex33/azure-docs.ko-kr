---
title: Azure Migrate 검색 및 평가 도구의 Azure SQL 평가
description: Azure Migrate 검색 및 평가 도구의 Azure SQL 평가에 대해 알아보기
author: rashi-ms
ms.author: rajosh
ms.topic: conceptual
ms.date: 02/07/2021
ms.openlocfilehash: bba3965dc3f03bb0b42595ba6bdf5d8889b483ee
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130004296"
---
# <a name="assessment-overview-migrate-to-azure-sql"></a>평가 개요(Azure SQL로 마이그레이션)

이 문서에서는 [Azure Migrate: 검색 및 평가 도구](./migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool)를 사용하여 VMware 환경에서 Azure SQL Database 또는 Managed Instance로 온-프레미스 SQL Server 인스턴스를 마이그레이션하기 위한 평가를 대략적으로 설명합니다.

## <a name="whats-an-assessment"></a>평가란?
검색 및 평가 도구를 사용한 평가는 데이터의 특정 시점 스냅샷으로, 온-프레미스 서버를 Azure로 마이그레이션하기 위한 준비 상태를 측정하고 효과를 예측합니다.

## <a name="types-of-assessments"></a>평가 유형

Azure Migrate: 검색 및 평가 도구를 사용하여 만들 수 있는 평가에는 세 가지 유형이 있습니다.

**평가 유형** | **세부 정보**
--- | --- 
**Azure VM** | 온-프레미스 서버를 Azure 가상 머신으로 마이그레이션하기 위한 평가. <br/><br/> 이 평가 형식을 사용하여 Azure VM으로 마이그레이션하기 위한 [VMware](how-to-set-up-appliance-vmware.md) 및 [Hyper-V](how-to-set-up-appliance-hyper-v.md) 환경과 [물리적 서버](how-to-set-up-appliance-physical.md)에서 의 온-프레미스 서버를 평가할 수 있습니다.
**Azure SQL** | 온-프레미스 SQL Server를 VMware 환경에서 Azure SQL Database 또는 Azure SQL Managed Instance로 마이그레이션하기 위한 평가. <br/><br/> SQL 서버가 비 VMware 플랫폼에서 실행되는 경우 [Data Migration Assistant](/sql/dma/dma-assess-sql-data-estate-to-sqldb)사용하여 준비 상태를 평가할 수 있습니다.
**AVS(Azure VMware 솔루션)** | 온-프레미스 서버를 [AVS(Azure VMware 솔루션)](../azure-vmware/introduction.md)로 마이그레이션하기 위한 평가. <br/><br/> 이 평가 유형을 사용하여 AVS(Azure VMware 솔루션)로 마이그레이션하기 위한 온-프레미스 [VMware VM](how-to-set-up-appliance-vmware.md)을 평가할 수 있습니다. [자세히 알아보기](concepts-azure-vmware-solution-assessment-calculation.md)

> [!NOTE]
> 검색 및 평가 도구에서 Azure VM의 수 또는 AVS 평가가 잘못된 경우, 총 평가 수를 클릭하여 모든 평가로 이동하고 Azure VM 또는 AVS 평가를 다시 계산합니다. 그러면 검색 및 평가 도구에 해당 평가 유형에 대한 올바른 개수가 표시됩니다. 

Azure SQL 평가는 다음과 같은 하나의 크기 조정 기준을 제공합니다.

**크기 조정 기준** | **세부 정보** | **Data**
--- | --- | ---
**성능 기반** | 수집된 성능 데이터를 기반으로 권장 사항을 만드는 평가 | Azure SQL 구성은 CPU 사용률, 메모리 사용률, IOPS(데이터 및 로그 파일), IO 작업의 처리량 및 대기 시간을 포함하는 SQL 인스턴스 및 데이터베이스의 성능 데이터를 기반으로 합니다.

## <a name="how-do-i-assess-my-on-premises-sql-servers"></a>온-프레미스 SQL Server는 어떻게 평가하나요?

경량 Azure Migrate 어플라이언스에서 수집한 구성 및 사용률 데이터를 사용하여 온-프레미스 SQL Server 인스턴스를 평가할 수 있습니다. 이 어플라이언스는 온-프레미스 SQL Server 인스턴스 및 데이터베이스를 검색하고 Azure Migrate로 구성 및 성능 데이터를 전송합니다. [자세한 내용](how-to-set-up-appliance-vmware.md)

## <a name="how-do-i-assess-with-the-appliance"></a>어떻게 어플라이언스를 사용하여 평가할까요?
온-프레미스 서버를 발견하기 위해 Azure Migrate 어플라이언스를 배포하는 경우 다음 단계를 수행합니다.
1.  Azure Migrate와 함께 사용할 Azure 및 온-프레미스 환경을 설정합니다.
2.  첫 평가를 위해 Azure Migrate 프로젝트를 만들고 Azure Migrate: 검색 및 평가 도구를 추가합니다.
3.  경량 Azure Migrate 어플라이언스를 배포합니다. 어플라이언스는 지속적으로 온-프레미스 서버를 검색하고 구성 및 성능 데이터를 Azure Migrate에 보냅니다. 어플라이언스를 VM 또는 물리적 서버로 배포합니다. 평가하려는 서버에는 아무것도 설치할 필요가 없습니다.

어플라이언스가 검색을 시작한 후에는 평가하려는 서버를 그룹으로 수집하고 평가 유형이 **Azure SQL** 인 그룹에 대한 평가를 실행할 수 있습니다.

[SQL Server 인스턴스](tutorial-assess-sql.md)를 평가하기 위한 자습서에 따라 이러한 단계를 시험해 보세요.

## <a name="how-does-the-appliance-calculate-performance-data-for-sql-instances-and-databases"></a>어플라이언스는 SQL 인스턴스 및 데이터베이스의 성능 데이터를 어떻게 계산하나요?

어플라이언스는 다음 단계에 따라 컴퓨팅 설정의 성능 데이터를 수집합니다.
1. 어플라이언스는 실시간 샘플 지점을 수집합니다. SQL Server의 경우 샘플 지점은 30초마다 수집됩니다.
2. 어플라이언스는 10분 동안 30초 간격으로 수집된 샘플 데이터 요소를 집계합니다. 데이터 요소를 만들기 위해 어플라이언스는 모든 샘플에서 최고 값을 선택합니다. 각 카운터의 최대, 평균 및 분산을 Azure로 보냅니다.
3. Azure Migrate는 지난 달의 모든 10분 데이터 요소를 저장합니다.
4. 평가를 만들 때 Azure Migrate는 적절한 크기 조정에 사용할 적절한 데이터 요소를 식별합니다. 식별은 성능 기록 및 백분위수 사용률에 대한 백분위수 값을 기준으로 합니다.
    - 예를 들어 성능 기록이 1주이고 백분위수 사용률이 95번째 백분위수인 경우, 평가는 지난 주에 대한 10분 샘플 지점을 정렬합니다. 오름차순으로 정렬하고 적절한 크기 조정의 95번째 백분위수 값을 선택합니다.
    - 95번째 백분위수 값은 99번째 백분위수 값을 선택할 때 포함될 수 있는 모든 이상값을 무시합니다.
    - 해당 기간의 최대 사용량을 선택하고 이상값을 누락하지 않으려면 백분위수 사용률로 99번째 백분위수를 선택해야 합니다.
5. 이 값은 어플라이언스가 수집하는 다음과 같은 메트릭을 위해 유효한 성능 사용률 데이터를 얻고자 쾌적 인자를 곱한 값입니다.
    - CPU 사용률(%)
    - 메모리 사용률(%)
    - 읽기 IO/s 및 쓰기 IO/s(데이터 및 로그 파일)
    - 읽기 MB/s 및 쓰기 MB/s(처리량)
    - IO 작업의 대기 시간

## <a name="what-properties-are-used-to-create-and-customize-an-azure-sql-assessment"></a>Azure SQL 평가를 만들고 사용자 지정하는 데 사용되는 속성은 무엇인가요?

다음은 Azure SQL 평가 속성에 포함된 요소입니다.

**속성** | **세부 정보**
--- | ---
**대상 위치** | 마이그레이션할 Azure 지역입니다. Azure SQL 구성 및 비용 권장 사항은 사용자가 지정한 위치를 기반으로 합니다.
**대상 배포 유형** | 평가를 실행하려는 대상 배포 유형: <br/><br/> Azure Migrate에서 Azure SQL MI 및 Azure SQL DB로 마이그레이션하기 위한 SQL 서버의 준비 상태를 평가하고 가장 적합한 대상 배포 옵션인 대상 계층, Azure SQL 구성 및 월별 추정치를 추천하려면 **권장** 을 선택합니다.<br/><br/>Azure SQL Database로만 마이그레이션하기 위한 SQL 서버를 평가하고 대상 계층, Azure SQL DB 구성, 월별 추정치를 검토하려면 **Azure SQL DB** 를 선택합니다.<br/><br/>Azure SQL Database로만 마이그레이션하기 위한 SQL 서버를 평가하고 대상 계층, Azure SQL MI 구성, 월별 추정치를 검토하려면 **Azure SQL MI** 를 선택합니다.
**예약 용량** | 평가의 비용 예측이 고려할 수 있도록 예약된 용량을 지정합니다.<br/><br/> 예약된 용량 옵션을 선택하면 "할인(%)"을 지정할 수 없습니다.
**크기 조정 기준** | 해당 속성은 Azure SQL 구성의 크기를 적절하게 조정하는 데 사용됩니다. <br/><br/> 기본적으로 **성능 기반** 으로 설정됩니다. 이는 평가가 SQL Server 인스턴스 및 데이터베이스 성능 메트릭을 수집하여 최적의 크기의 Azure SQL Managed Instance 및/또는 Azure SQL Database 계층/구성 권장 사항을 추천한다는 것을 의미합니다.
**성능 기록** | 성능 기록은 성능 데이터를 평가할 때 사용되는 기간을 지정합니다.
**백분위 수 사용률** | 백분위 수 사용률은 적합한 크기 조정에 사용되는 성능 샘플의 백분위수 값을 지정합니다.
**쾌적 인자** | 평가 중에 사용되는 버퍼입니다. 계절별 사용량, 짧은 성능 기록, 향후 사용량 증가 등의 문제를 고려합니다.<br/><br/> 예를 들어 사용률이 20%인 10코어 인스턴스는 일반적으로 2코어 인스턴스라는 결과가 나옵니다. 2\.0의 쾌적 인자를 사용하면 4코어 인스턴스라는 결과가 대신 나옵니다.
**제품/라이선싱 프로그램** | 등록한 [Azure 제품](https://azure.microsoft.com/support/legal/offer-details/)입니다. 현재 종량제 및 종량제 개발/테스트에서만 선택할 수 있습니다. 종량제 제품 외에 예약된 용량과 Azure 하이브리드 혜택을 적용하여 추가 할인을 받을 수 있습니다.
**서비스 계층** | Azure SQL Database 및/또는 Azure SQL Managed Instance로 마이그레이션해야 하는 비즈니스 요구 사항에 가장 적합한 서비스 계층 옵션입니다.<br/><br/>Azure Migrate에서 서버에 가장 적합한 서비스 계층을 추천하려면 **권장** 을 선택합니다. 권장 제품은 범용 또는 중요 비즈니스용 제품입니다. <br/><br/> 예산 기반 워크로드에 맞게 Azure SQL 구성을 디자인하려면 **범용** 을 선택합니다. [자세한 내용](../azure-sql/database/service-tier-general-purpose.md) <br/><br/> 복원력이 높고 장애 조치(failover)에 대한 대기 시간이 낮은 워크로드에 맞게 디자인된 Azure SQL 구성을 원하는 경우 **중요 비즈니스용** 을 선택합니다. [자세한 내용](../azure-sql/database/service-tier-business-critical.md)
**통화** | 계정의 청구 통화입니다.
**할인(%)** | Azure 제품에 적용되는 구독별 할인입니다. 기본 설정은 0%입니다.
**Azure 하이브리드 혜택** | SQL Server 라이선스가 이미 있는지를 지정합니다. <br/><br/> 이러한 작업을 수행하고 SQL Server 구독의 활성 소프트웨어 보증이 적용되는 경우 Azure에 라이선스를 가져올 때 Azure 하이브리드 혜택을 적용할 수 있습니다.

Azure Migrate를 사용하여 평가를 만들기 위해 [모범 사례를 검토](best-practices-assessment.md)합니다.

## <a name="calculate-readiness"></a>준비 상태 계산

> [!NOTE]
이 평가에는 온라인 상태인 데이터베이스만 포함됩니다. 데이터베이스가 다른 상태에 있는 경우 평가는 이러한 데이터베이스에 대한 준비, 크기 조정 및 비용 계산을 무시합니다. 이러한 데이터베이스를 평가하려는 경우에는 데이터베이스의 상태를 변경하고 잠시 후 평가를 다시 계산하세요.

### <a name="azure-sql-readiness"></a>Azure SQL 준비 상태

SQL 인스턴스 및 데이터베이스에 대한 Azure SQL 준비 상태는 Azure SQL Database 및 Azure SQL Managed Instance의 기능 호환성 검사를 기준으로 합니다.
1. Azure SQL 평가는 현재 원본 SQL Server 워크로드(SQL 에이전트 작업, 연결된 서버 등)에서 사용하는 SQL Server 인스턴스 기능 및 사용자 데이터베이스 스키마(테이블, 뷰, 트리거, 저장 프로시저 등)를 고려하여 호환성 문제를 식별합니다.
1. 호환성 문제가 발견되지 않은 경우 준비 상태가 대상 배포 유형(Azure SQL Database 또는 Azure SQL Managed Instance)에 대해 **준비** 로 표시됩니다.
1. 특정 대상 배포 유형으로의 마이그레이션을 차단하지 않는 성능 저하 또는 지원되지 않는 기능과 같은 중요하지 않은 호환성 문제가 있는 경우 준비 상태는 **경고** 세부 정보 및 권장되는 수정 지침을 나타내면서 **준비**(하이퍼링크 및 파란색 정보 아이콘)로 표시됩니다.
1. 특정 대상 배포 유형으로의 마이그레이션을 차단할 수 있는 호환성 문제가 있는 경우 준비 상태는 **이슈** 세부 정보 및 권장되는 수정 지침을 나타내면서 **준비 안 됨** 으로 표시됩니다.
    - SQL 인스턴스에서 특정 대상 배포 유형에 대해 준비되지 않은 데이터베이스가 하나 이상 있는 경우 해당 인스턴스는 해당 배포 유형에 대해 **준비 안 됨** 으로 표시됩니다.
1. 검색이 아직 진행 중이거나 SQL 인스턴스 또는 데이터베이스에 대한 검색 이슈가 있는 경우 평가 기능이 해당 SQL 인스턴스의 준비 상태를 파악할 수 없으므로 준비 상태가 **알 수 없음** 으로 표시됩니다.

### <a name="recommended-deployment-type"></a>권장 배포 유형

Azure SQL 평가 속성에서 **권장** 으로 표시된 대상 배포 유형을 선택하는 경우 Azure Migrate에서는 SQL 인스턴스와 호환되는 Azure SQL 배포 유형을 권장합니다. Microsoft 권장 대상으로 마이그레이션하면 전반적인 마이그레이션 노력이 줄어듭니다. 

#### <a name="recommended-deployment-type-based-on-azure-sql-readiness"></a>Azure SQL 준비 상태에 따른 권장 배포 유형

 **Azure SQL DB 준비 상태** | **Azure SQL MI 준비 상태** | **권장 배포 유형** | **Azure SQL 구성 및 예상 비용을 계산했나요?**
 --- | --- | --- | --- |
 준비 | 준비 | Azure SQL DB 또는 <br/>Azure SQL MI | 예
 준비 | 준비 안 됨 또는<br/> Unknown | Azure SQL DB | 예
 준비 안 됨 또는<br/>Unknown | 준비 | Azure SQL MI | 예
 준비 안 됨 | 준비 안 됨 | 잠재적으로 Azure VM 준비 | No
 준비 안 됨 또는<br/>Unknown | 준비 안 됨 또는<br/>알 수 없음 | 알 수 없음 | 아니요

> [!NOTE]
> 평가 속성에서 **권장** 으로 표시되는 권장 배포 유형을 선택하고 원본 SQL Server가 Azure SQL DB 단일 데이터베이스와 Azure SQL Managed Instance에 모두 적합한 경우 평가 기능은 비용을 최적화하고 크기 및 성능 경계를 벗어나지 않는 특정 옵션을 권장합니다.

#### <a name="potentially-ready-for-azure-vm"></a>잠재적으로 Azure VM 준비

SQL 인스턴스가 Azure SQL Database 및 Azure SQL Managed Instance에 대해 준비되지 않은 경우 권장 배포 유형은 *잠재적으로 Azure VM 준비* 로 표시됩니다.
- 사용자는 Azure Migrate에서 평가 유형을 "Azure VM"으로 사용하는 평가를 만들어 인스턴스가 실행되는 서버에서 Azure VM으로 대신 마이그레이션할 준비가 되었는지 확인하는 것이 좋습니다. 다음 사항에 유의하세요.
    - Azure Migrate의 Azure VM 평가는 현재 리프트 앤 시프트에 초점을 맞춘 후 Azure 가상 머신에서 SQL 인스턴스 및 데이터베이스를 실행하는 특정 성능 메트릭을 고려하지 않습니다. 
    - 서버에서 Azure VM 평가를 실행하는 경우 서버에서 실행되는 모든 인스턴스에 대해 권장되는 크기 및 예상 비용을 계산하고 서버 마이그레이션 도구를 사용하여 Azure VM으로 마이그레이션할 수 있습니다. 마이그레이션하기 전에 Azure Virtual Machines의 SQL Server에 대한 [성능 지침을 검토](../azure-sql/virtual-machines/windows/performance-guidelines-best-practices-checklist.md)합니다.


## <a name="calculate-sizing"></a>크기 계산

### <a name="azure-sql-configuration"></a>Azure SQL 구성

평가 기능은 준비 상태 및 권장 Azure SQL 배포 유형을 확인한 후 온-프레미스 SQL 인스턴스 성능을 충족하거나 초과할 수 있는 특정 서비스 계층 및 Azure SQL 구성(SKU 크기)을 계산합니다.
1. 검색 프로세스 중에 Azure Migrate은 다음을 포함하는 SQL 인스턴스 구성 및 성능을 수집합니다.
    - vCore(할당) 및 CPU 사용률(%)
        - SQL 인스턴스의 CPU 사용률은 SQL Server의 인스턴스에서 사용하는 할당된 CPU의 백분율입니다.
        - 데이터베이스의 CPU 사용률은 SQL 인스턴스의 데이터베이스에서 사용하는 할당된 CPU의 백분율입니다.
    - 메모리(할당) 및 메모리 사용률(%)
    - 읽기 IO/s 및 쓰기 IO/s(데이터 및 로그 파일)
        - 읽기 IO/s 및 쓰기 IO/s는 해당 인스턴스에서 검색된 모든 데이터베이스의 읽기 IO/s와 쓰기 IO/s를 추가하여 계산합니다.
    - 읽기 MB/s 및 쓰기 MB/s(처리량)
    - IO 작업의 대기 시간
    - 총 DB 크기 및 데이터베이스 파일 조직
        - 데이터베이스 크기는 모든 데이터 및 로그 파일을 추가하여 계산합니다.
1. 평가 기능은 모든 구성 및 성능 데이터를 집계하고 다양한 Azure SQL 서비스 계층 및 구성에서 가장 적합한 항목을 찾으려고 시도하며, SQL 인스턴스 성능 요구 사항을 충족하거나 초과하는 구성을 선택하여 비용을 최적화합니다.

### <a name="confidence-ratings"></a>신뢰 등급 
각 Azure SQL 평가는 신뢰 등급과 연결됩니다. 등급의 범위는 1(최저)에서 5(가장 높음) 사이입니다. 신뢰 등급을 통해 Azure Migrate에서 제공하는 크기 권장 사항의 안정성을 예측할 수 있습니다.
- 신뢰 등급이 평가에 할당됩니다. 이 등급은 평가를 컴퓨팅하는 데 필요한 데이터 요소의 가용성을 기반으로 합니다.
- 성능 기반 크기 조정의 경우 평가 기능은 다음을 포함하는 모든 SQL 인스턴스 및 데이터베이스의 성능 데이터를 수집합니다.
    - CPU 사용률(%)
    - 메모리 사용률(%)
    - 읽기 IO/s 및 쓰기 IO/s(데이터 및 로그 파일)
    - 읽기 MB/s 및 쓰기 MB/s(처리량)
    - IO 작업의 대기 시간
    
이러한 사용률을 사용할 수 없는 경우에는 크기 권장 사항이 불안정해질 수 있습니다.
다음 표에서는 사용 가능한 데이터 요소의 백분율에 따른 평가 신뢰 등급을 보여 줍니다.

**데이터 요소 가용성** | **신뢰 등급**
--- | ---
0%-20% | 별 1개
21%-40% | 별 2개
41%-60% | 별 3개
61%-80% | 별 4개
81%-100% | 별 5개

#### <a name="low-confidence-ratings"></a>낮은 신뢰 등급
여기에는 평가의 신뢰 등급이 낮아질 수 있는 이유가 나와 있습니다.
- 평가를 만드는 동안 환경을 프로파일링하지 않았습니다. 예를 들어 성능 기간을 1일로 설정하여 평가를 작성하는 경우 검색 시작 후 1일 이상 기다려야 모든 데이터 요소가 수집됩니다.
- 평가는 일부 또는 모든 서버에 대한 성능 데이터를 평가 기간에 수집할 수 없습니다. 높은 신뢰 등급의 경우 다음을 확인하세요.
    - 평가 기간에 서버의 전원이 켜져 있는지 확인합니다.
    - 443 포트에서 아웃바운드 연결이 허용되는지 확인합니다.
    - Azure Migrate에서 SQL 에이전트의 Azure Migrate 연결 상태가 '연결됨'이면 마지막 하트비트를 확인합니다. 
    - 검색된 SQL 인스턴스 블레이드에서 모든 SQL 인스턴스에 대한 Azure Migrate 연결 상태가 "연결됨"인 경우

    신뢰 등급의 최신 변경 내용을 반영하려면 평가를 '다시 계산'하세요.
- 평가가 계산되는 기간에 일부 데이터베이스 또는 인스턴스가 만들어졌습니다. 예를 들어 마지막 한 달의 성능 기록을 위한 평가를 만들려고 하는데, 일부 데이터베이스 또는 인스턴스가 불과 일주일 전에 만들어졌습니다. 이 경우 새 서버의 성능 데이터는 전체 기간에 사용할 수 없으며 신뢰 등급은 낮습니다.

> [!NOTE]
> Azure SQL 평가가 성능 기반 평가이므로 평가의 신뢰 등급이 별 5개 미만인 경우 어플라이언스가 환경을 프로파일링하도록 하루 이상 기다린 다음, 평가를 다시 계산하는 것이 좋습니다. 그렇지 않으면 성능 기반 크기 조정이 불안정해질 수 있습니다.

## <a name="calculate-monthly-costs"></a>월별 비용 계산
크기 조정 권장 사항이 완료된 후 Azure SQL 평가 기능은 내부 가격 책정 API를 사용하여 권장되는 Azure SQL 구성에 대한 컴퓨팅 및 스토리지 비용을 계산합니다. 모든 인스턴스에 대해 컴퓨팅 및 스토리지 비용을 집계하여 월별 총 컴퓨팅 비용을 계산합니다. 
### <a name="compute-cost"></a>컴퓨팅 비용
- Azure SQL 구성의 컴퓨팅 비용을 계산하기 위해 평가 기능은 다음 속성을 고려합니다.
    - SQL 라이선스에 대한 Azure 하이브리드 혜택
    - 예약 용량
    - Azure 대상 위치
    - 통화
    - 제품/라이선싱 프로그램
    - 할인(%)

### <a name="storage-cost"></a>스토리지 비용
- 스토리지 비용은 데이터 파일만 포함하고 로그 파일은 포함하지 않습니다. 
- Azure SQL 구성에 대한 스토리지 비용을 계산하기 위해 평가 기능은 다음 속성을 고려합니다.
    - Azure 대상 위치
    - 통화
    - 제품/라이선싱 프로그램
    - 할인(%)
- 백업 스토리지 비용은 평가에 포함되지 않습니다.
- **Azure SQL Database**
    - 최소 5GB 스토리지 비용은 비용 추정치에 추가되고 스토리지에 대한 추가 스토리지 비용은 1GB 단위로 추가됩니다. [자세한 내용](https://azure.microsoft.com/pricing/details/sql-database/single/)
- **Azure SQL Managed Instance**
    - 처음 32GB/인스턴스/월 스토리지에 대해 추가되는 스토리지 비용이 없지만, 32GB 단위로 증분되는 스토리지에 대해서는 스토리지 비용이 추가됩니다. [자세한 내용](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/)
        
## <a name="next-steps"></a>다음 단계
- 평가를 만드는 모범 사례를 [검토합니다](best-practices-assessment.md). 
- [Azure SQL 평가](how-to-create-azure-sql-assessment.md)를 실행하는 방법을 알아봅니다.
