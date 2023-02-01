# 发展中。OpenShift 上的 NET Core 2.0 Web 应用程序

> 原文：<https://developers.redhat.com/blog/2017/11/22/developing-net-core-2-0-web-applications-openshift>

今天我们将使用 JBoss Developer Studio 和 aCute 插件(C#应用程序开发)创建一个. NET Core 2.0 Web 应用程序。我们将把我们的应用程序部署到一个 OpenShift 实例上，并继续修改它，同时几乎即时地查看更改。尽管初始设置非常复杂，但只需要做一次。

You might imagine that the normal workflow for this would be:

1.  做出局部改变。
2.  提交+将它们推送到版本控制。
3.  手动重新触发一个构建，或者确保设置了 web 挂钩，以便 OpenShift 检测新的变化。

在这些情况下，包含我们的构建/运行时和项目源的映像必须在每次变更时重新构建和启动。这不仅需要一段时间，而且在开发环境中，谁愿意提交和推动他们所做的每一个微小的改变，只是为了看看它看起来像什么？幸运的是，有一种更简单的方法可以更好地反映这种“开发”工作流程。

我们将从获取先决条件开始。我们将从[【https://developers.redhat.com/products/devstudio/download/】](https://developers.redhat.com/products/devstudio/download/)和下载 JBDS。NET Core SDK 来自[https://www.microsoft.com/net/download/core](https://www.microsoft.com/net/download/core)。

一旦安装了这些，你还需要从 Eclipse Marketplace，https://market place . Eclipse . org/content/aCute-c-edition-Eclipse-ide-experimental 安装‘aCute’插件。使用软件站点安装东西总是可能的，但是许多人会发现将“安装”按钮从市场拖放到 Eclipse IDE 工作台中更容易。 ![](img/be118116e11f0faddad8f42e6914a1ef.png)

如果我们最终使用一个软件站点来安装内容，我们将希望在 Eclipse IDE(实验)中安装“ACute - C#版本”。一旦完成，重启后，我们将准备创建我们的第一个项目。

 ![](img/983d138cc2acee3b9496dcc0daf8bbbe.png)


We're going to create a very simple .NET Core 2.0 Web Application project that we'll push to our GitHub repository so that it can be referenced when deploying our application on OpenShift.In JBoss Developer Studio, click on File -> New -> Other. Expand the '.Net Core' category from the selection wizard, and select '.NET Core Project'. Now Click 'Next'.![](img/0015a5523c46a90033fa16421b20c523.png)We will be creating the project in our workspace so we'll leave the location option as is. Once the project templates load, select the 'ASP .NET Core Web App [C#]' template and click 'Finish'.**Note:** If there are no templates from which to select, it is likely that the .NET Core 2.0 SDK was not installed correctly.![](img/d37239b3f91bebba40350b4f4d430439.png)Next, we need to push our new project to a publicly accessible Git repository. In our case, we're using GitHub, but any publicly available location (accessible over https) will do.Right-click on the project, and go to 'Team' -> 'Share Project...'. Select 'Git' and click 'Next'. Select the option to 'Use or create repository in parent folder of the project' and then click the 'Create Repository' button. When the process is completed, click 'Finish'.![](img/2f78adf963fb32064cbee630955e60d0.png)Now let's add the project files to version control by right-clicking our project, going to 'Team' -> 'Commit'. Make sure to stage all files but the contents of the 'obj' folder, provide a commit message and then click 'Commit and Push'.![](img/02ce79ceb683aa43c6cb55a1b6d9af3b.png)A new dialog should now appear asking us to specify the destination git repository. We'll use 'git@github.com:rgrunber/dotnet-demo.git' in the URI and that should auto-complete the remaining necessary fields.![](img/c6d53d897ae986d48cddc4babdcdb521.png)Click Next, and once again as we will be using default settings. Finally, click 'Finish'. We have successfully pushed to our newly created repository. Now we can finally go ahead and deploy the application on OpenShift.First, we need to connect to our OpenShift instance. I'm using one from OpenShift Online, and we'll connect to it by clicking on the 'New Connection Wizard...' link in the OpenShift Explorer view.Now we fill in the relevant connection information for our OpenShift instance.![](img/4063e63de306d272102d90bbc8b8a4c7.png)We'll have to create a project if none exist, or if we want to keep this work separated from other existing projects.![](img/fa82293f108b6ba96b285f02c784c045.png)From the OpenShift Explorer view, right-click 'dotnet-demo', and go to New -> Application... in the context menu. This should bring up the New OpenShift Application wizard.**Note:** It may be necessary to import the JSON image stream template for dotnetcore-2.0 images from [https://github.com/redhat-developer/s2i-dotnetcore/blob/master/dotnet_imagestreams.json](https://github.com/redhat-developer/s2i-dotnetcore/blob/master/dotnet_imagestreams.json) if none are available from the Server application source list.Here we'll just click the 'Browse...' button, and select our project. From the Server application source list, we'll select 'dotnet:2.0' and click Next. Make sure that the 'dotnet:2.0' image selected exists in the context of the 'dotnet-demo' project, and not just the default 'openshift' context. This is to ensure we're using the newer image.For the configuration, we'll name our project 'dotnet-demo', using [https://github.com/rgrunber/dotnet-demo.git](https://github.com/rgrunber/dotnet-demo.git) as the git repository url, 'master' for the branch to reference, and '/' as the context directory. We'll also add an environment variable called 'DEV_MODE' and set its value to 'true'.![](img/c46c0719d55cfdd0f0498df6caac2242.png)Click 'Finish', and we should see the necessary image stream, service, build config, deployment config and routes being created. Our application is being built and deployed.![](img/05cbf167d36f724e2522e0f5e638d821.png)The final step we need to perform involves, right clicking on our newly created dotnet-demo service from under our project, in the OpenShift Explorer view, and selecting the 'Server Adapter...' option from the context menu. From here, click 'Finish', confirming that the 'dotnet-demo' service is selected. Now we're finally ready to make changes to our application.First, let's view what our application actually looks like. In the OpenShift Explorer view, right-click the 'dotnet-demo' service from within our project and select Show In -> Browser. Click the 'About' link from the top to see what that page looks like.![](img/71cfa1d57543269265501ea909cf7f02.png)Now let's try modifying this page's contents. Open up 'About.cshtml.cs' from our local copy of the project and change the message string. Now save the changes, then go back to our web browser and simply refresh the about page. It's that easy.

* * *

**[**红帽 OpenShift 容器平台**](https://www.openshift.com/container-platform/) **可供下载。****

***Last updated: September 3, 2019***