# 设置 Google 凭据

我们为您走到这一步并为您的 DeFi 应用程序设置前端感到非常自豪。在本课中，我们将设置 Google 凭据。

## 设置 Google 凭据

按照以下步骤设置 Google Cloud 帐户并获取您要在前端应用程序中使用的客户端 ID。

1. 前往 Google Cloud 仪表板并使用您的 Google 帐户登录。登录后您将看到如下页面。

   ![google-setup-1.png](https://github.com/0xmetaschool/Learning-Projects/blob/ba2ce8dea0997931621928704f03f1a8483ecc0d/Build%20the%20Token%20Dex%20DApp/5.%20Work%20With%20the%20Frontend/assets/google-setup-1.png?raw=true)

2. 在侧边栏，您可以看到“API 和服务”字段。转到它并单击“凭据”。

   ![google-setup-2.png](https://github.com/0xmetaschool/Learning-Projects/blob/ba2ce8dea0997931621928704f03f1a8483ecc0d/Build%20the%20Token%20Dex%20DApp/5.%20Work%20With%20the%20Frontend/assets/google-setup-2.png?raw=true)

你会看到这样的页面：

![google-setup-3.png](https://github.com/0xmetaschool/Learning-Projects/blob/ba2ce8dea0997931621928704f03f1a8483ecc0d/Build%20the%20Token%20Dex%20DApp/5.%20Work%20With%20the%20Frontend/assets/google-setup-3.png?raw=true)

1. 现在，单击“创建项目”，在字段中填写以下数据，然后单击“创建”按钮。
   1. 项目名称：Sui App登录
   2. 位置： 保留此字段不变

以下是我们如何填写这些字段。

![google-setup-4.png](https://github.com/0xmetaschool/Learning-Projects/blob/ba2ce8dea0997931621928704f03f1a8483ecc0d/Build%20the%20Token%20Dex%20DApp/5.%20Work%20With%20the%20Frontend/assets/google-setup-4.png?raw=true)

创建项目后，您将看到这样的页面。

![google-setup-5.png](https://github.com/0xmetaschool/Learning-Projects/blob/ba2ce8dea0997931621928704f03f1a8483ecc0d/Build%20the%20Token%20Dex%20DApp/5.%20Work%20With%20the%20Frontend/assets/google-setup-5.png?raw=true)

1. 要创建凭据，请单击“+ CREATE CREDENTIALS”按钮，然后选择“OAuth client ID”。

   ![google-setup-6.png](https://github.com/0xmetaschool/Learning-Projects/blob/ba2ce8dea0997931621928704f03f1a8483ecc0d/Build%20the%20Token%20Dex%20DApp/5.%20Work%20With%20the%20Frontend/assets/google-setup-6.png?raw=true)

   1. 接下来，如果您看到如下页面，请单击“配置同意屏幕”按钮。

      ![google-setup-7.png](https://github.com/0xmetaschool/Learning-Projects/blob/ba2ce8dea0997931621928704f03f1a8483ecc0d/Build%20the%20Token%20Dex%20DApp/5.%20Work%20With%20the%20Frontend/assets/google-setup-7.png?raw=true)

   2. 选择“外部”选项，以便任何用户都可以使用您的应用程序，然后单击“创建”按钮。

      ![google-setup-8.png](https://github.com/0xmetaschool/Learning-Projects/blob/ba2ce8dea0997931621928704f03f1a8483ecc0d/Build%20the%20Token%20Dex%20DApp/5.%20Work%20With%20the%20Frontend/assets/google-setup-8.png?raw=true)

   单击“CREATE”按钮后，您将看到如下页面。

   ![google-setup-9.png](https://github.com/0xmetaschool/Learning-Projects/blob/ba2ce8dea0997931621928704f03f1a8483ecc0d/Build%20the%20Token%20Dex%20DApp/5.%20Work%20With%20the%20Frontend/assets/google-setup-9.png?raw=true)

   1. 您现在需要填写应用程序信息。填写以下字段，跳过任何其他字段，填写信息后单击“保存并继续”按钮。

      1. 应用名称：Sui应用登录
      2. 用户支持电子邮件：在这里，您可以填写您的电子邮件ID
      3. 授权域名：点击“+添加域名”按钮并输入以下链接 - `sui.io`
      4. 开发者联系信息：在此字段中填写您的电子邮件 ID

   2. 现在，您将看到如下所示的页面。移至页面末尾，单击“保存并继续”按钮，然后继续，无需在此处进行任何更改。

      ![google-setup-10.png](https://github.com/0xmetaschool/Learning-Projects/blob/ba2ce8dea0997931621928704f03f1a8483ecc0d/Build%20the%20Token%20Dex%20DApp/5.%20Work%20With%20the%20Frontend/assets/google-setup-10.png?raw=true)

   3. 接下来，您将看到以下页面。再次单击“保存并继续”按钮，然后继续。

      ![google-setup-11.png](https://github.com/0xmetaschool/Learning-Projects/blob/ba2ce8dea0997931621928704f03f1a8483ecc0d/Build%20the%20Token%20Dex%20DApp/5.%20Work%20With%20the%20Frontend/assets/google-setup-11.png?raw=true)

      1. 最后，您将看到以下页面。移至页面末尾并单击“返回仪表板”按钮。

         ![google-setup-12.png](https://github.com/0xmetaschool/Learning-Projects/blob/ba2ce8dea0997931621928704f03f1a8483ecc0d/Build%20the%20Token%20Dex%20DApp/5.%20Work%20With%20the%20Frontend/assets/google-setup-12.png?raw=true)

      2. 将出现以下页面。单击“凭据”页面返回到凭据页面。

         ![google-setup-13.png](https://github.com/0xmetaschool/Learning-Projects/blob/ba2ce8dea0997931621928704f03f1a8483ecc0d/Build%20the%20Token%20Dex%20DApp/5.%20Work%20With%20the%20Frontend/assets/google-setup-13.png?raw=true)

      3. 现在，再次选择“+ CREATE CREDENTIALS”，然后选择“OAuth client ID”。

         ![google-setup-15.png](https://github.com/0xmetaschool/Learning-Projects/blob/ba2ce8dea0997931621928704f03f1a8483ecc0d/Build%20the%20Token%20Dex%20DApp/5.%20Work%20With%20the%20Frontend/assets/google-setup-15.png?raw=true)

      4. 从“应用程序类型”滚动条中选择“Web 应用程序”。

         ![google-setup-16.png](https://github.com/0xmetaschool/Learning-Projects/blob/ba2ce8dea0997931621928704f03f1a8483ecc0d/Build%20the%20Token%20Dex%20DApp/5.%20Work%20With%20the%20Frontend/assets/google-setup-16.png?raw=true)

         1. 填写应用程序的“名称”、“授权重定向 URI”以及 `http://localhost:3000/` 和 `http://127.0.0.1:3000/` URI，然后单击“创建”按钮。

            ![google-setup-17.png](https://github.com/0xmetaschool/Learning-Projects/blob/ba2ce8dea0997931621928704f03f1a8483ecc0d/Build%20the%20Token%20Dex%20DApp/5.%20Work%20With%20the%20Frontend/assets/google-setup-17.png?raw=true)

      5. 创建客户端 ID 后，将出现以下页面。复制客户端 ID。我们将此客户端 ID 粘贴到前端的 `.env` 文件中。

      ![google-setup-18.png](https://github.com/0xmetaschool/Learning-Projects/blob/ba2ce8dea0997931621928704f03f1a8483ecc0d/Build%20the%20Token%20Dex%20DApp/5.%20Work%20With%20the%20Frontend/assets/google-setup-18.png?raw=true)

      或者您可以从此处复制客户端 ID。

      ![google-setup-19.png](https://github.com/0xmetaschool/Learning-Projects/blob/ba2ce8dea0997931621928704f03f1a8483ecc0d/Build%20the%20Token%20Dex%20DApp/5.%20Work%20With%20the%20Frontend/assets/google-setup-19.png?raw=true)

2. 最后，我们需要在 Google Cloud 上公开我们的应用程序。这样任何人，包括您，都可以不受任何限制地登录。为此，请从侧边栏转到“OAuth 同意屏幕”，然后单击“发布应用程序”按钮。

![publish-app.png](https://github.com/0xmetaschool/Learning-Projects/blob/ba2ce8dea0997931621928704f03f1a8483ecc0d/Build%20the%20Token%20Dex%20DApp/5.%20Work%20With%20the%20Frontend/assets/publish-app.png?raw=true)

## 将客户端 ID 粘贴到 .env 文件

返回前端并找到 `.env` 文件。如果不存在，请在根目录中创建该文件。将以下行粘贴到 `.env` 文件中，并将 `YOUR_GOOGLE_CLIENT_ID` 替换为您从 Google 云复制的客户端 ID。

```ini
NEXT_PUBLIC_CLIENT_ID_GOOGLE=YOUR_GOOGLE_CLIENT_ID
```

## 小结

设置 Google Cloud 客户端 ID 方面做得非常出色。在下一课中，我们将设置 ZK 登录并运行我们的前端



## quiz

与我们分享您的 Google 客户端 ID。