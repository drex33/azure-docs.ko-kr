---
title: 포함 파일
description: 포함 파일
services: cosmos-db
ms.custom: include file
ms.openlocfilehash: 3f7a37ce78e49abe1356180818a215488fe82ea3
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122515628"
---
1. 새 브라우저 창에서 [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. 왼쪽 메뉴에서 **+ 리소스 만들기** 를 선택합니다.
   
   :::image type="content" source="./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-0.png" alt-text="Azure Portal에서 리소스를 만드는 스크린샷.":::
   
3. **새로 만들기** 페이지에서 **데이터베이스** > **Azure Cosmos DB** 를 차례로 선택합니다.
   
   :::image type="content" source="./media/cosmos-db-create-dbaccount-mongodb/create-nosql-db-databases-json-tutorial-1.png" alt-text="Azure Portal 데이터베이스 창의 스크린샷.":::
   
4. **API 옵션 선택** 페이지에서 **Azure Cosmos DB API for MongoDB** > **만들기** 를 선택합니다.

   API는 만들 계정의 형식을 결정합니다. 이 빠른 시작에서는 MongoDB에서 사용하는 컬렉션을 만들므로 **Azure Cosmos DB API for MongoDB** 를 선택합니다. 자세한 내용은 [Azure Cosmos DB API for MongoDB 개요](../mongodb-introduction.md)를 참조하세요.

   :::image type="content" source="./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-select-api.png" alt-text="API 선택 옵션 창의 스크린샷.":::

5. **Azure Cosmos DB 계정 만들기** 페이지에서 새 Azure Cosmos DB 계정에 대한 설정을 입력합니다.

   |설정|값|설명 |
   |---|---|---|
   |Subscription|구독 이름|이 Azure Cosmos DB 계정에 사용하려는 Azure 구독을 선택합니다. |
   |리소스 그룹|리소스 그룹 이름|리소스 그룹을 선택하거나 **새로 만들기** 를 선택한 후, 새 리소스 그룹에 고유한 이름을 입력합니다. |
   |계정 이름|고유한 이름을 입력합니다.|내 Azure Cosmos DB 계정을 식별하는 고유한 이름을 입력합니다. 계정 URI는 고유한 계정 이름에 *mongo.cosmos.azure.com* 이 추가됩니다.<br><br>계정 이름은 소문자, 숫자, 하이픈(-) 문자만 사용할 수 있으며 3~44자 길이여야 합니다.|
   |위치|사용자와 가장 가까운 지역|Azure Cosmos DB 계정을 호스트할 지리적 위치를 선택합니다. 데이터에 가장 빨리 액세스할 수 있도록 사용자와 가장 가까운 위치를 사용합니다.|
   |용량 모드|프로비저닝된 처리량 또는 서버리스|**프로비저닝된 처리량** 을 선택하여 [프로비저닝된 처리량](../set-throughput.md) 모드에서 계정을 만듭니다. **서버리스** 를 선택하여 [서버리스](../serverless.md) 모드에서 계정을 만듭니다.<br><br>**참고**: MongoDB API 버전 4 및 3.6만 서버리스 계정에서 지원합니다. 3\.2 버전을 선택하면 계정이 프로비저닝된 처리량 모드로 강제 적용됩니다.|
   |Azure Cosmos DB 체험 계층 할인 적용|**적용** 또는 **적용 안 함**|Azure Cosmos DB 체험 계층을 사용하는 경우 처음에는 1000RU/초 및 25GB의 스토리지가 계정에 무료로 제공됩니다. [체험 계층](https://azure.microsoft.com/pricing/details/cosmos-db/)에 대해 자세히 알아보세요.|
   | 버전 | 필요한 서버 버전 선택 | Azure Cosmos DB API for MongoDB는 서버 버전 4.0, 3.6 및 3.2와 호환됩니다. 계정을 만든 후 [업그레이드하거나 다운그레이드](../mongodb/upgrade-mongodb-version.md)할 수 있습니다. |

   > [!NOTE]
   > Azure 구독당 최대 1개의 체험 계층 Azure Cosmos DB 계정을 사용할 수 있으며 계정을 만들 때 옵트인해야 합니다. 체험 계층 할인을 적용하는 옵션이 표시되지 않으면 구독의 다른 계정에서 이미 체험 계층을 사용하도록 설정되었음을 의미합니다.

   :::image type="content" source="./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-create-new-account.png" alt-text="Azure Cosmos DB에 대한 새 계정 페이지의 스크린샷."::: 

1. **전역 배포** 탭에서 다음 세부 정보를 구성합니다. 이 빠른 시작의 목적을 위해 기본값을 그대로 둘 수 있습니다.

   |설정|값|Description |
   |---|---|---|
   |지리적 중복|사용 안 함|지역에 쌍 영역을 페어링하여 계정에서 글로벌 배포를 사용하거나 사용하지 않도록 설정합니다. 나중에 계정에 더 많은 지역을 추가할 수 있습니다.|
   |다중 지역 쓰기|사용 안 함|다중 영역 쓰기 기능을 사용하면 전 세계의 데이터베이스 및 컨테이너에 대해 프로비저닝된 처리량을 활용할 수 있습니다.|

   > [!NOTE]
   > **용량 모드** 로 **서버리스** 를 선택한 경우 다음 옵션을 사용할 수 없습니다.
   > - 체험 계층 할인 적용
   > - 지리적 중복
   > - 다중 지역 쓰기

1. 필요에 따라 다음 탭에서 추가 세부 정보를 구성할 수 있습니다.

   * **네트워킹** - [가상 네트워크에서 액세스](../how-to-configure-vnet-service-endpoint.md)를 구성합니다.
   * **백업 정책** - [주기적](../configure-periodic-backup-restore.md) 또는 [지속적인](../provision-account-continuous-backup.md) 백업 정책을 구성합니다.
   * **암호화** - 서비스 관리형 키 또는 [고객 관리형 키](../how-to-setup-cmk.md#create-a-new-azure-cosmos-account)를 사용합니다.
   * **태그** - 태그는 동일한 태그를 여러 개의 리소스 및 리소스 그룹에 적용하여 리소스를 범주화하고 통합된 청구를 볼 수 있는 이름/값 쌍입니다.

1. **검토 + 만들기** 를 선택합니다.

4. 계정 생성에는 몇 분 정도가 소요됩니다. 포털에 **축하합니다! Azure Cosmos DB API for MongoDB 계정이 준비되었습니다.** 페이지가 표시될 때까지 기다리세요.

   :::image type="content" source="./media/cosmos-db-create-dbaccount-mongodb/azure-cosmos-db-account-created.png" alt-text="Azure Portal 알림 창의 스크린샷."::: 
