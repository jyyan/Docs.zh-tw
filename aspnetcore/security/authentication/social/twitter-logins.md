---
title: "Twitter 外部登入安裝程式"
author: rick-anderson
description: 
keywords: ASP.NET Core
ms.author: riande
manager: wpickett
ms.date: 11/1/2016
ms.topic: article
ms.assetid: E5931607-31C0-4B20-B416-85E3550F5EA8
ms.technology: aspnet
ms.prod: asp.net-core
uid: security/authentication/twitter-logins
ms.openlocfilehash: 800f98285859a54198b76411aea000384de05cd3
ms.sourcegitcommit: 74e22e08e3b08cb576e5184d16f4af5656c13c0c
ms.translationtype: MT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/25/2017
---
# <a name="configuring-twitter-authentication"></a><span data-ttu-id="f1a9f-103">設定 Twitter 驗證</span><span class="sxs-lookup"><span data-stu-id="f1a9f-103">Configuring Twitter authentication</span></span>

<a name=security-authentication-twitter-logins></a>

<span data-ttu-id="f1a9f-104">由[Valeriy Novytskyy](https://github.com/01binary)和[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f1a9f-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="f1a9f-105">本教學課程會示範如何讓使用者[使用 Twitter 帳戶登入](https://dev.twitter.com/web/sign-in/desktop-browser)上使用範例 ASP.NET Core 2.0 專案建立[上一頁](index.md)。</span><span class="sxs-lookup"><span data-stu-id="f1a9f-105">This tutorial shows you how to enable your users to [sign in with their Twitter account](https://dev.twitter.com/web/sign-in/desktop-browser) using a sample ASP.NET Core 2.0 project created on the [previous page](index.md).</span></span>

## <a name="create-the-app-in-twitter"></a><span data-ttu-id="f1a9f-106">在 Twitter 中建立應用程式</span><span class="sxs-lookup"><span data-stu-id="f1a9f-106">Create the app in Twitter</span></span>

* <span data-ttu-id="f1a9f-107">瀏覽至[https://apps.twitter.com/](https://apps.twitter.com/)並登入。</span><span class="sxs-lookup"><span data-stu-id="f1a9f-107">Navigate to [https://apps.twitter.com/](https://apps.twitter.com/) and sign in.</span></span> <span data-ttu-id="f1a9f-108">如果您還沒有 Twitter 帳戶，使用**[立即註冊](https://twitter.com/signup)**建立一個連結。</span><span class="sxs-lookup"><span data-stu-id="f1a9f-108">If you don't already have a Twitter account, use the **[Sign up now](https://twitter.com/signup)** link to create one.</span></span> <span data-ttu-id="f1a9f-109">登入之後,**應用程式管理**頁面會顯示：</span><span class="sxs-lookup"><span data-stu-id="f1a9f-109">After signing in, the **Application Management** page is shown:</span></span>

![Twitter 應用程式管理的 Microsoft Edge 開啟](index/_static/TwitterAppManage.png)

* <span data-ttu-id="f1a9f-111">點選**建立新的應用程式**填妥應用程式和**名稱**，**描述**和公用**網站**（這可能是暫時性直到您的 URI註冊的網域名稱）：</span><span class="sxs-lookup"><span data-stu-id="f1a9f-111">Tap **Create New App** and fill out the application **Name**, **Description** and public **Website** URI (this can be temporary until you register the domain name):</span></span>

![建立應用程式頁面](index/_static/TwitterCreate.png)

* <span data-ttu-id="f1a9f-113">輸入您的開發 URI 與*/signin-twitter*附加到**有效的 OAuth 重新導向 Uri**欄位 (例如： `https://localhost:44320/signin-twitter`)。</span><span class="sxs-lookup"><span data-stu-id="f1a9f-113">Enter your development URI with */signin-twitter* appended into the **Valid OAuth Redirect URIs** field (for example: `https://localhost:44320/signin-twitter`).</span></span> <span data-ttu-id="f1a9f-114">稍後在本教學課程中設定的 Twitter 驗證配置將會自動處理在要求*/signin-twitter*實作 OAuth 流程的路由。</span><span class="sxs-lookup"><span data-stu-id="f1a9f-114">The Twitter authentication scheme configured later in this tutorial will automatically handle requests at */signin-twitter* route to implement the OAuth flow.</span></span>

* <span data-ttu-id="f1a9f-115">填寫表單的其餘部分，並點選**建立應用程式 Twitter**。</span><span class="sxs-lookup"><span data-stu-id="f1a9f-115">Fill out the rest of the form and tap **Create your Twitter application**.</span></span> <span data-ttu-id="f1a9f-116">會顯示新的應用程式詳細資料：</span><span class="sxs-lookup"><span data-stu-id="f1a9f-116">New application details are displayed:</span></span>

![應用程式頁面的詳細資料 索引標籤](index/_static/TwitterAppDetails.png)

* <span data-ttu-id="f1a9f-118">部署站台時必須再次瀏覽**應用程式管理**頁面上，並註冊新的公用 URI。</span><span class="sxs-lookup"><span data-stu-id="f1a9f-118">When deploying the site you'll need to revisit the **Application Management** page and register a new public URI.</span></span>

## <a name="storing-twitter-consumerkey-and-consumersecret"></a><span data-ttu-id="f1a9f-119">儲存 Twitter ConsumerKey 和 ConsumerSecret</span><span class="sxs-lookup"><span data-stu-id="f1a9f-119">Storing Twitter ConsumerKey and ConsumerSecret</span></span>

<span data-ttu-id="f1a9f-120">連結機密設定，例如 Twitter`Consumer Key`和`Consumer Secret`您應用程式組態使用[密碼管理員](../../app-secrets.md)。</span><span class="sxs-lookup"><span data-stu-id="f1a9f-120">Link sensitive settings like Twitter `Consumer Key` and `Consumer Secret` to your application configuration using the [Secret Manager](../../app-secrets.md).</span></span> <span data-ttu-id="f1a9f-121">基於本教學課程的目的，名稱語彙基元`Authentication:Twitter:ConsumerKey`和`Authentication:Twitter:ConsumerSecret`。</span><span class="sxs-lookup"><span data-stu-id="f1a9f-121">For the purposes of this tutorial, name the tokens `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret`.</span></span>

<span data-ttu-id="f1a9f-122">這些語彙基元可以找到上**機碼和存取權杖**之後建立新的 Twitter 應用程式 索引標籤：</span><span class="sxs-lookup"><span data-stu-id="f1a9f-122">These tokens can be found on the **Keys and Access Tokens** tab after creating your new Twitter application:</span></span>

![索引鍵和存取權杖 索引標籤](index/_static/TwitterKeys.png)

## <a name="configure-twitter-authentication"></a><span data-ttu-id="f1a9f-124">設定 Twitter 驗證</span><span class="sxs-lookup"><span data-stu-id="f1a9f-124">Configure Twitter Authentication</span></span>

<span data-ttu-id="f1a9f-125">在本教學課程中使用的專案範本，可確保[Microsoft.AspNetCore.Authentication.Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter)封裝是否已經安裝。</span><span class="sxs-lookup"><span data-stu-id="f1a9f-125">The project template used in this tutorial ensures that [Microsoft.AspNetCore.Authentication.Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter) package is already installed.</span></span>

* <span data-ttu-id="f1a9f-126">若要安裝此套件與 Visual Studio 2017，以滑鼠右鍵按一下專案，然後選取**管理 NuGet 封裝**。</span><span class="sxs-lookup"><span data-stu-id="f1a9f-126">To install this package with Visual Studio 2017, right-click on the project and select **Manage NuGet Packages**.</span></span>
* <span data-ttu-id="f1a9f-127">若要安裝的.NET Core CLI，請在您的專案目錄中執行下列：</span><span class="sxs-lookup"><span data-stu-id="f1a9f-127">To install with .NET Core CLI, execute the following in your project directory:</span></span>

   `dotnet add package Microsoft.AspNetCore.Authentication.Twitter`

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="f1a9f-128">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="f1a9f-128">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="f1a9f-129">Twitter 服務中的新增`ConfigureServices`方法中的*Startup.cs*檔案：</span><span class="sxs-lookup"><span data-stu-id="f1a9f-129">Add the Twitter service in the `ConfigureServices` method in *Startup.cs* file:</span></span>

```csharp
services.AddAuthentication().AddTwitter(twitterOptions =>
{
    twitterOptions.ConsumerKey = Configuration["Authentication:Twitter:ConsumerKey"];
    twitterOptions.ConsumerSecret = Configuration["Authentication:Twitter:ConsumerSecret"];
});
```

<span data-ttu-id="f1a9f-130">`AddAuthentication`方法只能呼叫一次時新增多個驗證提供者。</span><span class="sxs-lookup"><span data-stu-id="f1a9f-130">The `AddAuthentication` method should only be called once when adding multiple authentication providers.</span></span> <span data-ttu-id="f1a9f-131">後續呼叫可能會覆寫任何先前設定的[AuthenticationOptions](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.authenticationoptions)屬性。</span><span class="sxs-lookup"><span data-stu-id="f1a9f-131">Subsequent calls to it have the potential of overriding any previously configured [AuthenticationOptions](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.authenticationoptions) properties.</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="f1a9f-132">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="f1a9f-132">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="f1a9f-133">加入在 Twitter 中的介軟體`Configure`方法中的*Startup.cs*檔案：</span><span class="sxs-lookup"><span data-stu-id="f1a9f-133">Add the Twitter middleware in the `Configure` method in *Startup.cs* file:</span></span>

```csharp
app.UseTwitterAuthentication(new TwitterOptions()
{
    ConsumerKey = Configuration["Authentication:Twitter:ConsumerKey"],
    ConsumerSecret = Configuration["Authentication:Twitter:ConsumerSecret"]
});
```

---

<span data-ttu-id="f1a9f-134">請參閱[TwitterOptions](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.twitteroptions) API 參考，如需有關 Twitter 驗證所支援的組態選項。</span><span class="sxs-lookup"><span data-stu-id="f1a9f-134">See the [TwitterOptions](https://docs.microsoft.com/aspnet/core/api/microsoft.aspnetcore.builder.twitteroptions) API reference for more information on configuration options supported by Twitter authentication.</span></span> <span data-ttu-id="f1a9f-135">這可以用於要求的使用者不同的資訊。</span><span class="sxs-lookup"><span data-stu-id="f1a9f-135">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-twitter"></a><span data-ttu-id="f1a9f-136">使用 Twitter 登入</span><span class="sxs-lookup"><span data-stu-id="f1a9f-136">Sign in with Twitter</span></span>

<span data-ttu-id="f1a9f-137">執行您的應用程式，然後按一下**登入**。</span><span class="sxs-lookup"><span data-stu-id="f1a9f-137">Run your application and click **Log in**.</span></span> <span data-ttu-id="f1a9f-138">使用 Twitter 登入選項會出現：</span><span class="sxs-lookup"><span data-stu-id="f1a9f-138">An option to sign in with Twitter appears:</span></span>

![Web 應用程式： 未驗證的使用者](index/_static/DoneTwitter.png)

<span data-ttu-id="f1a9f-140">按一下**Twitter**重新導向至 Twitter 驗證：</span><span class="sxs-lookup"><span data-stu-id="f1a9f-140">Clicking on **Twitter** redirects to Twitter for authentication:</span></span>

![Twitter 驗證頁面](index/_static/TwitterLogin.png)

<span data-ttu-id="f1a9f-142">輸入您的 Twitter 認證之後, 您將會重新導向回 web 站台，您可以設定您的電子郵件。</span><span class="sxs-lookup"><span data-stu-id="f1a9f-142">After entering your Twitter credentials, you are redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="f1a9f-143">您現在會記錄在使用您的 Twitter 認證：</span><span class="sxs-lookup"><span data-stu-id="f1a9f-143">You are now logged in using your Twitter credentials:</span></span>

![Web 應用程式： 已驗證的使用者](index/_static/Done.png)

## <a name="troubleshooting"></a><span data-ttu-id="f1a9f-145">疑難排解</span><span class="sxs-lookup"><span data-stu-id="f1a9f-145">Troubleshooting</span></span>

* <span data-ttu-id="f1a9f-146">**ASP.NET Core 2.x 僅：**如果身分識別未設定藉由呼叫`services.AddIdentity`中`ConfigureServices`，嘗試驗證將會導致*ArgumentException： 必須提供 'SignInScheme' 選項*。</span><span class="sxs-lookup"><span data-stu-id="f1a9f-146">**ASP.NET Core 2.x only:** If Identity is not configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="f1a9f-147">在本教學課程中使用的專案範本可確保，這已完成。</span><span class="sxs-lookup"><span data-stu-id="f1a9f-147">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="f1a9f-148">如果尚未套用初始移轉建立站台資料庫，您會收到*處理要求時，資料庫作業失敗*錯誤。</span><span class="sxs-lookup"><span data-stu-id="f1a9f-148">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="f1a9f-149">點選**套用移轉**來建立資料庫，並重新整理 以忽略錯誤繼續執行。</span><span class="sxs-lookup"><span data-stu-id="f1a9f-149">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="f1a9f-150">後續步驟</span><span class="sxs-lookup"><span data-stu-id="f1a9f-150">Next steps</span></span>

* <span data-ttu-id="f1a9f-151">這篇文章會示範您可以向 Twitter。</span><span class="sxs-lookup"><span data-stu-id="f1a9f-151">This article showed how you can authenticate with Twitter.</span></span> <span data-ttu-id="f1a9f-152">您可以依照類似的方法，來向其他提供者列在[上一頁](index.md)。</span><span class="sxs-lookup"><span data-stu-id="f1a9f-152">You can follow a similar approach to authenticate with other providers listed on the [previous page](index.md).</span></span>

* <span data-ttu-id="f1a9f-153">一旦您將您的網站發行至 Azure web 應用程式時，您應該重設`ConsumerSecret`Twitter 開發人員入口網站中。</span><span class="sxs-lookup"><span data-stu-id="f1a9f-153">Once you publish your web site to Azure web app, you should reset the `ConsumerSecret` in the Twitter developer portal.</span></span>

* <span data-ttu-id="f1a9f-154">設定`Authentication:Twitter:ConsumerKey`和`Authentication:Twitter:ConsumerSecret`為在 Azure 入口網站應用程式設定。</span><span class="sxs-lookup"><span data-stu-id="f1a9f-154">Set the `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="f1a9f-155">設定系統設定來讀取環境變數中的索引鍵。</span><span class="sxs-lookup"><span data-stu-id="f1a9f-155">The configuration system is set up to read keys from environment variables.</span></span>