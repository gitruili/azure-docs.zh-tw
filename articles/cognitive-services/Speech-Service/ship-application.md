---
title: Azure 認知服務、認知服務語音 SDK API 文件 - 教學課程和 API 參考
description: 了解如何使用認知服務語音 SDK 建立和開發應用程式
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: 65ff0e47cf7a53d519bfd0c50ea4c3ebd09a5766
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/20/2018
ms.locfileid: "42093837"
---
# <a name="shipping-an-application"></a>寄送應用程式

在散發認知服務語音 SDK 時，請觀察[語音 SDK 授權](license.md)及[第三方軟體聲明](third-party-notices.md)。 此外，請檢閱 [Microsoft 隱私權聲明](https://aka.ms/csspeech/privacy)。

視平台而定，有不同的相依性存在以執行您的應用程式。

## <a name="windows"></a>Windows

測試認知語音 SDK 會在 Windows 10 和 Windows Server 2016 上進行測試。

認知服務語音 SDK 需要系統上的[適用於 Visual Studio 2017 的 Microsoft Visual C++ 可轉散發套件](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)。 您可以在此下載最新版 `Microsoft Visual C++ Redistributable for Visual Studio 2017` 的安裝程式：

- [Win32](https://aka.ms/vs/15/release/vc_redist.x86.exe)
- [x64](https://aka.ms/vs/15/release/vc_redist.x64.exe)

如果您的應用程式使用受控程式碼，則目標電腦上需要 `.NET Framework 4.6.1` 或更新版本。

對於麥克風輸入，必須安裝媒體基礎程式庫。 這些程式庫是 Windows 10 和 Windows Server 2016 的一部分。 沒有這些程式庫也可能使用語音 SDK，只要麥克風並未作為音訊輸入裝置即可。

在與您的應用程式相同的目錄中，可以部署必要的語音 SDK 檔案。 如此一來，您的應用程式就可以直接存取程式庫。 確定您選取與您的應用程式相符的正確版本 (Win32/x64)。

| 名稱 | 函式
|:-----|:----|
| `Microsoft.CognitiveServices.Speech.core.dll` | 核心 SDK (原生和受控部署所需)
| `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` | 受控部署所需
| `Microsoft.CognitiveServices.Speech.csharp.dll` | 受控部署所需

## <a name="linux"></a>Linux

對於原生應用程式，您需要提供語音 SDK 程式庫 `libMicrosoft.CognitiveServices.Speech.core.so`。
確定您選取與您的應用程式相符的版本 (x86、x64)。 視 Linux 版本而定，您可能也需要包含下列相依性：

* GNU C 程式庫的共用程式庫 (包括 POSIX 執行緒程式設計程式庫 `libpthreads`)
* OpenSSL 程式庫 (`libssl.so.1.0.0`)
* cURL 程式庫 (`libcurl.so.4`)
* ALSA 應用程式的共用程式庫 (`libasound.so.2`)

例如在 Ubuntu 16.04 上，預設應該已安裝 GNU C 程式庫。 使用下列命令可以安裝最後三項：

```sh
sudo apt-get update
sudo apt-get install libssl1.0.0 libcurl3 libasound2 wget
```

## <a name="next-steps"></a>後續步驟

* [試用認知服務](https://azure.microsoft.com/try/cognitive-services/)
* [了解如何以 C# 辨識語音](quickstart-csharp-dotnet-windows.md) (英文)
