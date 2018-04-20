# Azure Function Lab
---
## Một số yêu cầu

- Azure subscription, đăng ký với GAB2018 organizer để có Azure Pass, sau đó đăng nhập vào Azure Portal
- Cài đặt VS 2017 ver 15.5 hoặc mới hơn, bao gồm Azure development
- Hoặc Visual Code, bao gồm:
    + [Azure Functions Core Tools](https://docs.microsoft.com/en-us/azure/azure-functions/functions-run-local)
    + [Plug-in Azure Functions for Visual Studio Code](https://github.com/Microsoft/vscode-azurefunctions)
- Cài đặt ngrok

## Lab 1: Tạo Azure function thông qua Azure portal

- Viết function đầu tiên với http trigger "helloAzureFuncHttpTrigger"
- Viết timer function để gọi function "helloAzureFuncHttpTrigger"
```C#
using System;

public static async Task Run(TimerInfo myTimer, TraceWriter log)
{
    var client = new HttpClient();
    var response = await client.GetAsync(url);
    var content = await response.Content.ReadAsAsync<string>();

    log.Info($"C# Timer trigger function executed at: {DateTime.Now} - " + content);
}
```

## Lab 2: Tạo webhook bằng Azure function bằng Visual Studio
- Viết function với http trigger, với webhooktype = "genericJson"
- Tạo 1 alert trong phần "Monitor" cua Azure portal
- Cấu hinh ngrok
```bash
    ./ngrok http 7071 -host-header="localhost:7001"
```
- Chạy function va debug

## Lab 3: Deployment
