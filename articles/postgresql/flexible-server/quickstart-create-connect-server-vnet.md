---
title: Azure Portal에서 프라이빗 액세스 권한으로 Azure Database for PostgreSQL 유연한 서버에 연결
description: 이 문서에서는 프라이빗 액세스 또는 Azure Portal을 사용하는 가상 네트워크를 통해 Azure Database for PostgreSQL 유연한 서버를 만들고 연결하는 방법을 보여줍니다.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.custom: mvc, mode-other
ms.topic: quickstart
ms.date: 07/29/2021
ms.openlocfilehash: b4a9c51a86ed6ce352108cee002a464f8ec853ff
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133066155"
---
# <a name="connect-azure-database-for-postgresql-flexible-server-with-the-private-access-connectivity-method"></a>프라이빗 액세스 연결 방법을 사용하여 Azure Database for PostgreSQL 유연한 서버 연결

Azure Database for PostgreSQL 유연한 서버는 클라우드에서 고가용성 PostgreSQL 서버를 실행, 관리 및 크기 조정하는 데 사용할 수 있는 관리되는 서비스입니다. 이 빠른 시작에서는 Azure Portal을 사용하여 가상 네트워크에서 유연한 서버를 만드는 방법을 보여 줍니다.

> [!IMPORTANT]
> Azure Database for PostgreSQL 유연한 서버는 현재 공개 미리 보기로 제공됩니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure 포털](https://portal.azure.com/)로 이동합니다. 자격 증명을 입력하여 포털에 로그인합니다. 기본 보기는 서비스 대시보드입니다.

## <a name="create-an-azure-database-for-postgresql-flexible-server"></a>Azure Database for PostgreSQL 유연한 서버 만들기

정의된 [컴퓨팅 및 스토리지 리소스](./concepts-compute-storage.md) 세트를 사용하여 유연한 서버를 만듭니다. 서버는 [Azure 리소스 그룹](../../azure-resource-manager/management/overview.md) 내에 만듭니다.

다음 단계를 완료하여 유연한 서버를 만듭니다.

1. Azure Portal에서 **Azure Database for PostgreSQL 서버** 를 검색하여 선택합니다.

   :::image type="content" source="./media/quickstart-create-connect-server-vnet/search-flexible-server-in-portal.png" alt-text="Azure Database for PostgreSQL 서버에 대한 검색을 보여주는 스크린샷입니다." lightbox="./media/quickstart-create-connect-server-vnet/search-flexible-server-in-portal.png":::

2. **추가** 를 선택합니다.

3. **Azure Database for PostgreSQL 배포 옵션 선택** 페이지에서 배포 옵션으로 **유연한 서버** 를 선택합니다.

    :::image type="content" source="./media/quickstart-create-connect-server-vnet/deployment-option.png" alt-text="유연한 서버 옵션을 보여 주는 스크린샷" lightbox="./media/quickstart-create-connect-server-vnet/deployment-option.png":::

4. **기본** 탭에서 **구독**, **리소스 그룹**, **지역** 및 **서버 이름** 을 입력합니다.  기본값을 사용하면 2개 vCore, 8GiB RAM 및 28GiB 스토리지를 사용하는 범용 가격 책정 계층이 포함된 PostgreSQL 서버 버전 12가 프로비전됩니다. 백업 보존은 **7** 일입니다. **개발** 워크로드를 사용하여 저비용 가격 책정 계층을 기본값으로 지정할 수 있습니다.

    :::image type="content" source="./media/quickstart-create-connect-server-vnet/postgres-create-basics.png" alt-text="postgres 유연한 서버 페이지의 기본 탭을 보여주는 스크린샷입니다." lightbox="./media/quickstart-create-connect-server-vnet/postgres-create-basics.png":::

5. **기본** 탭에서 고유한 **관리자 사용자 이름** 및 **관리자 암호** 를 입력합니다.

    :::image type="content" source="./media/quickstart-create-connect-server-vnet/db-administrator-account.png" alt-text="관리자 사용자 정보 페이지를 보여주는 스크린샷입니다." lightbox="./media/quickstart-create-connect-server-vnet/db-administrator-account.png":::

6.  **네트워킹** 탭으로 이동하고 **프라이빗 액세스** 를 선택합니다. 서버가 만들어지면 연결 방법을 변경할 수 없습니다. **가상 네트워크 만들기** 를 선택하여 새 가상 네트워크 **vnetenvironment1** 을 만듭니다. 가상 네트워크 이름 및 서브넷 정보를 제공한 후 **확인** 을 선택합니다.

    :::image type="content" source="./media/quickstart-create-connect-server-vnet/create-new-vnet-for-postgres-server.png" alt-text="새 VNET이 있는 네트워킹 탭을 보여 주는 스크린샷" lightbox="./media/quickstart-create-connect-server-vnet/create-new-vnet-for-postgres-server.png":::

7. **검토 + 만들기** 를 선택하여 유연한 서버 구성을 검토합니다.

8. **만들기** 를 선택하여 서버를 프로비전합니다. 프로비저닝에는 몇 분 정도 걸릴 수 있습니다.

9. 배포가 완료되고 성공할 때까지 기다립니다.

   :::image type="content" source="./media/quickstart-create-connect-server-vnet/deployment-success.png" alt-text="새 VNET이 있는 네트워킹 설정을 보여 주는 스크린샷" lightbox="./media/quickstart-create-connect-server-vnet/deployment-success.png":::

9.  **리소스로 이동** 을 선택하여 서버의 **개요** 페이지가 열리는 것을 확인합니다.

## <a name="create-an-azure-linux-virtual-machine"></a>Azure Linux 가상 머신 만들기

서버가 가상 네트워크에 있기 때문에 서버와 동일한 가상 네트워크에 있는 다른 Azure 서비스에서만 서버에 연결할 수 있습니다. 서버를 연결하고 관리하기 위해 Linux 가상 머신을 만들어 보겠습니다. 가상 머신은 **동일한 지역** 및 **동일한 구독** 에 만들어야 합니다. Linux 가상 머신을 SSH 터널로 사용하여 데이터베이스 서버를 관리할 수 있습니다. 

1. 서버가 만들어진 리소스 그룹으로 이동합니다. **추가** 를 선택합니다.
2. **Ubuntu Server 18.04 LTS** 를 선택합니다.
3. **기본** 탭의 **프로젝트 세부 정보** 아래에서 올바른 구독이 선택되어 있는지 확인한 다음, 리소스 그룹 **새로 만들기** 를 선택합니다. 이름으로 *myResourceGroup* 을 입력합니다.

   :::image type="content" source="../../virtual-machines/linux/media/quick-create-portal/project-details.png" alt-text="가상 머신에 대한 Azure 구독 및 리소스 그룹을 선택하는 위치를 보여 주는 프로젝트 세부 정보 섹션의 스크린샷" lightbox="../../virtual-machines/linux/media/quick-create-portal/project-details.png"::: 

2. **인스턴스 세부 정보** 에서 **가상 머신 이름** 에 *myVM* 을 입력하고 데이터베이스 서버와 동일한 **지역** 을 선택합니다.

   :::image type="content" source="../../virtual-machines/linux/media/quick-create-portal/instance-details.png" alt-text="가상 머신의 이름을 입력하고 해당 지역, 이미지, 크기를 선택하는 인스턴스 세부 정보 섹션의 스크린샷" lightbox="../../virtual-machines/linux/media/quick-create-portal/instance-details.png":::

3. **관리자 계정** 에서 **SSH 공개 키** 를 선택합니다.

4. **사용자 이름** 에서 *azureuser* 를 입력합니다.

5. **SSH 공개 키 원본** 의 경우 기본값인 **새 키 쌍 생성** 을 그대로 둔 다음, **키 쌍 이름** 에 *myKey* 를 입력합니다.

   :::image type="content" source="../../virtual-machines/linux/media/quick-create-portal/administrator-account.png" alt-text="인증 유형을 선택하고 관리자 자격 증명을 입력하는 관리자 계정 섹션의 스크린샷" lightbox="../../virtual-machines/linux/media/quick-create-portal/administrator-account.png":::

6. **인바운드 포트 규칙** > **공용 인바운드 포트** 에서 **선택된 포트 허용을** 선택한 다음, 드롭다운에서 **SSH(22)** 및 **HTTP(80)** 를 선택합니다.

   :::image type="content" source="../../virtual-machines/linux/media/quick-create-portal/inbound-port-rules.png" alt-text="인바운드 연결이 허용되는 포트를 선택하는 인바운드 포트 규칙 섹션의 스크린샷" lightbox="../../virtual-machines/linux/media/quick-create-portal/inbound-port-rules.png":::

7. **네트워킹** 페이지를 선택하여 가상 네트워크를 구성합니다. 가상 네트워크의 경우 데이터베이스 서버에 대해 만든 **vnetenvironment1** 을 선택합니다.

   :::image type="content" source="./media/quickstart-create-connect-server-vnet/vm-vnet-configuration.png" alt-text="데이터베이스 서버의 기존 가상 네트워크가 선택된 스크린샷입니다." lightbox="./media/quickstart-create-connect-server-vnet/vm-vnet-configuration.png":::

8. 서버에 대한 새 서브넷을 만들려면 **서브넷 구성 관리** 를 선택합니다.

    :::image type="content" source="./media/quickstart-create-connect-server-vnet/vm-manage-subnet-integration.png" alt-text="서브넷 관리 스크린샷입니다." lightbox="./media/quickstart-create-connect-server-vnet/vm-manage-subnet-integration.png":::

9. 가상 머신에 대한 새 서브넷을 추가합니다.

    :::image type="content" source="./media/quickstart-create-connect-server-vnet/vm-add-new-subnet.png" alt-text="가상 머신에 대한 새 서브넷을 추가하는 스크린샷입니다." lightbox="./media/quickstart-create-connect-server-vnet/vm-add-new-subnet.png"::: 

10. 서브넷을 성공적으로 만든 후 페이지를 닫습니다.

    :::image type="content" source="./media/quickstart-create-connect-server-vnet/subnet-create-success.png" alt-text="가상 머신에 대해 새 서브넷을 성공적으로 추가한 스크린샷입니다." lightbox="./media/quickstart-create-connect-server-vnet/subnet-create-success.png":::

11. **검토 + 만들기** 를 선택합니다.
12. **만들기** 를 선택합니다. **새 키 쌍 생성** 창이 열리면 **프라이빗 키 다운로드 및 리소스 만들기** 를 선택합니다. 키 파일은 **myKey.pem** 으로 다운로드됩니다.

    >[!IMPORTANT]
    > `.pem` 파일이 다운로드된 위치를 확인합니다. 이 경로는 다음 단계에서 필요합니다.

13. 배포가 완료되었으면 **리소스로 이동** 을 선택하여 가상 머신 **개요** 페이지를 확인합니다.

14. 공용 IP 주소를 선택하고 이를 클립보드에 복사합니다.

    :::image type="content" source="../../virtual-machines/linux/media/quick-create-portal/ip-address.png" alt-text="가상 머신의 IP 주소를 복사하는 방법을 보여 주는 스크린샷" lightbox="../../virtual-machines/linux/media/quick-create-portal/ip-address.png":::

## <a name="install-postgresql-client-tools"></a>PostgreSQL 클라이언트 도구 설치

Bash 또는 PowerShell을 사용하여 VM과의 SSH 연결을 만듭니다. 프롬프트에서 가상 머신에 대한 SSH 연결을 엽니다. IP 주소를 VM의 IP 주소로 바꾸고, `.pem`에 대한 경로를 키 파일이 다운로드된 경로로 바꿉니다.

```console
ssh -i .\Downloads\myKey1.pem azureuser@10.111.12.123
```

> [!TIP]
> 만든 SSH 키는 다음에 Azure에서 VM을 만들 때 사용될 수 있습니다. 다음에 VM을 만들 때 **SSH 공개 키 원본** 에 대해 **Azure에 저장된 키 사용** 을 선택하면 됩니다. 컴퓨터에 프라이빗 키가 이미 있으므로 아무것도 다운로드하지 않아도 됩니다.

서버에 연결할 수 있으려면 postgresql-client 도구를 설치해야 합니다.

```bash
sudo apt-getupdate
sudo apt-get install postgresql-client
```

데이터베이스에 대한 연결은 SSL을 사용하여 적용되므로 퍼블릭 SSL 인증서를 다운로드해야 합니다.

```bash
wget --no-check-certificate https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem
```

## <a name="connect-to-the-server-from-azure-linux-virtual-machine"></a>Azure Linux 가상 머신에서 서버에 연결
**mysql.exe** 클라이언트 도구가 설치되었으므로 이제 로컬 환경에서 서버에 연결할 수 있습니다.

```bash
psql --host=mydemoserver-pg.postgres.database.azure.com --port=5432 --username=myadmin --dbname=postgres --set=sslmode=require --set=sslrootcert=DigiCertGlobalRootCA.crt.pem
```

## <a name="clean-up-resources"></a>리소스 정리
이제 Azure Database for PostgreSQL 유연한 서버를 리소스 그룹에 만들었습니다. 이러한 리소스가 나중에 필요하지 않을 경우에는 리소스 그룹을 삭제하거나 PostgreSQL 서버를 삭제하여 리소스를 삭제할 수 있습니다. 리소스 그룹을 삭제하려면 다음 단계를 완료합니다.

1. Azure Portal에서 **리소스 그룹** 을 검색하고 선택합니다.
1. 리소스 그룹 목록에서 삭제하려는 리소스 그룹의 이름을 선택합니다.
1. 리소스 그룹에 대한 **개요** 페이지에서 **리소스 그룹 삭제** 를 선택합니다.
1. 확인 대화 상자에서 리소스 그룹의 이름을 입력한 다음, **삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Python 앱으로 연결](connect-python.md)
