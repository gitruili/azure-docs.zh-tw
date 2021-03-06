---
title: 使用 Azure CLI 在適用於 MySQL 的 Azure 資料庫中存取伺服器記錄
description: 本文說明如何使用 Azure CLI 命令列公用程式，在適用於 MySQL 的 Azure 資料庫中存取伺服器記錄。
services: mysql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.devlang: azure-cli
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 57b72ded77484dc1c8ca4c62811b62e171365db4
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/02/2018
ms.locfileid: "39423452"
---
# <a name="configure-and-access-server-logs-by-using-azure-cli"></a>使用 Azure CLI 設定和存取伺服器記錄
您可以使用 Azure CLI (Azure 的命令列公用程式) 來下載適用於 MySQL 的 Azure 資料庫的伺服器記錄檔。

## <a name="prerequisites"></a>必要條件
若要逐步執行本作法指南，您需要︰
- [適用於 MySQL 的 Azure 資料庫伺服器](quickstart-create-mysql-server-database-using-azure-cli.md)
- [Azure CLI 2.0](/cli/azure/install-azure-cli) 或在瀏覽器中的 Azure Cloud Shell

## <a name="configure-logging-for-azure-database-for-mysql"></a>設定適用於 MySQL 的 Azure 資料庫的記錄
您可以採取下列步驟，設定伺服器以存取 MySQL 慢速查詢記錄檔：
1. 藉由將 **slow\_query\_log** 參數設為 ON 來開啟記錄功能。
2. 調整其他參數，例如 **long\_query\_time** 和 **log\_slow\_admin\_statements**。

若要了解如何透過 Azure CLI 設定這些參數的值，請參閱[如何設定伺服器參數](howto-configure-server-parameters-using-cli.md)。 

例如，下列 CLI 命令會開啟慢速查詢記錄檔、將長時間查詢的時間設定為 10 秒，並且會關閉慢速管理陳述式的記錄。 最後，它會列出設定選項供您檢閱。
```azurecli-interactive
az mysql server configuration set --name slow_query_log --resource-group myresourcegroup --server mydemoserver --value ON
az mysql server configuration set --name long_query_time --resource-group myresourcegroup --server mydemoserver --value 10
az mysql server configuration set --name log_slow_admin_statements --resource-group myresourcegroup --server mydemoserver --value OFF
az mysql server configuration list --resource-group myresourcegroup --server mydemoserver
```

## <a name="list-logs-for-azure-database-for-mysql-server"></a>列出適用於 MySQL 的 Azure 資料庫伺服器之記錄
若要列出伺服器的可用記錄檔，請執行 [az mysql server-logs list](/cli/azure/mysql/server-logs#az-mysql-server-logs-list) 命令。

您可以針對資源群組 **myresourcegroup** 下的伺服器 **mydemoserver.mysql.database.azure.com** 列出記錄檔。 然後將記錄檔的清單導向名為 **log\_files\_list.txt** 的文字檔。
```azurecli-interactive
az mysql server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-from-the-server"></a>從伺服器下載記錄檔
使用 [az mysql server-logs download](/cli/azure/mysql/server-logs#az-mysql-server-logs-download) 命令，即可下載您伺服器適用的個別記錄檔。 

使用下列範例，針對資源群組 **myresourcegroup** 下的伺服器 **mydemoserver.mysql.database.azure.com**，將特定的記錄檔下載至您的本機環境。
```azurecli-interactive
az mysql server-logs download --name 20170414-mydemoserver-mysql.log --resource-group myresourcegroup --server mydemoserver
```

## <a name="next-steps"></a>後續步驟
- 深入了解[適用於 MySQL 的 Azure 資料庫中的伺服器記錄檔](concepts-server-logs.md)。
