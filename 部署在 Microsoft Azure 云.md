# 部署在 Microsoft Azure 云

这个按部就班的教程描述了如何在 Microsoft Azure 云平台上部署一个小型的 Symfony 网页应用。它将会解释如何设置一个新的 Azure 网页，包括设置正确的 PHP 版本和全球环境变量。文件还展示了您如何可以利用 Git 和 Composer 在云上部署您的 Symfony 应用。

## 建立 Azure 网站

建立一个新的 Microsoft Azure 网站，首先[注册 Azure](https://signup.live.com/signup?uaid=5342819efab64b99bd8238efe0e39bcc&lic=1) 或者用证书注册。一旦您连接到了 [Azure Portal](https://login.microsoftonline.com/common/oauth2/authorize?response_type=code+id_token&redirect_uri=https%3a%2f%2fmanage.windowsazure.com%2f&client_id=00000013-0000-0000-c000-000000000000&resource=https%3a%2f%2fmanagement.core.windows.net%2f&scope=user_impersonation+openid&nonce=097f59b2-ccfc-46d7-8576-3f1e7542234e&domain_hint=&site_id=500879&response_mode=form_post) 的界面，向下滚动到按钮然后选择 **New** 面板。在此面板上，点击 **Web Site** 然后选择 **Custome Create**：

[!image]

### 步骤1：创建 Web Site

这里，系统将提示您填写一些基本信息。

[!image]

对于 URL 来说，进入 URL 您将会用于 Symfony 应用程序，然后在您想要的区域内挑选 **Create new web hosting plan**。默认情况下，在下拉列表中的数据库中选择 *free 20 MB SQL datebase*。在本教程中，Symfony 应用程序将会连接到 MySQL 数据库上。在下拉列表的数据库中选择 ** Create a new MySQL database**。您可以保持 **DefaultConnection** 的字符串名称。最后，确认会话框中的 ** Publish from source control** 来启动 Git 库然后进入下一步。

### 步骤2：新的 MySQL 数据库

在此步骤中，系统将会提示您建立您的 MySQL 数据库存储，有数据库名称和区域。MySQL 数据库存储由 Microsoft 和 ClearDB 联合提供。选择与您上一步为托管计划配置选择的相同的区域。

[!images]

### 步骤3：您的源代码在哪里

现在，第三步，选择 **Local Git repository** 项目，然后点击右箭头来配置您的 Azure 网站证书。

[!images]

### 步骤4：新的用户名与密码

太棒了！您现在在最后一步。创建一个用户名和一个安全密码：这些将成为连接到 FTP 服务器最根本的身份标识，并且也能推动您的应用程序代码到 Git 库。

[!images]

祝贺！您的 Azure 网站现在已建立并正在运行了。您可以通过您在第一步中配置的网站 url 中浏览进行验证。您应该在您的网页浏览器看到以下显示：

[!images]

Microsoft Azure 门户同样为 Azure Website 提供了一个完整的控制面板。

[!images]

您的 Azure Website 已经做好准备！但是要运行一个 Symfony 网址，您需要配置一些其他的事情。

## 为 Symfony 配置 Azure Website

教程的这个部分详细地展示了怎样配置正确的 PHP 版本来运行 Symfony。它同样也展示了您怎样启动一些强制性的 PHP 扩张以及如何适当地为生产环境配置 PHP。

## 配置最新的 PHP 运行时间

尽管 Symfony 只需要 PHP 5.3.9 来运行，但还总是会建议使用最新的 PHP 版本。PHP 5.3 不再由 PHP 核心团队所支持，但是您可以在 Azure 里很容易地升级。

在 Azure 里升级您的 PHP 版本，在控制面板内找到 **Configure** 标记，然后选择您想要的版本。

[!images]

在视窗下方点击 **Save** 按钮来保存您的变化，然后重启网页服务器。

选择一个较新一点的 PHP 版本可以极大地提高运行时性能。PHP 5.5 装载了一个新植入的 PHP 加速器称作 OPCache，替代了 APC。在 Azure Website 上，OPCache 已经被启动并且无需再安装和建立 APC 了。

以下的截屏显示了在 Azure Website 上运行的 [phpinfo](http://php.net/manual/en/function.phpinfo.php) 脚本的输出，来验证 PHP 5.5 是在启动了 OPCache 的情况下运行的。

[!images]

## 对 php.ini 配置设置微调 

Microsoft Azure 允许您覆盖掉 **php.ini** 全球配置设置，通过在项目根目录（**site/wwwroot**）创建一个自定义文件 **.user.ini**。

```
1
2
3
4	; .user.ini
expose_php = Off
memory_limit = 256M
upload_max_filesize = 10M

```

这些设置均不需要覆盖。默认 PHP 配置已经足够好，所以只是一个例子来展示如何通过加载您的自定义文件 **.ini** 轻松地微调 PHP 内部设置。

您也可以在您的 Azure Website 服务器上手动创建此文件，在 **site/wwwroot** 目录下或者用 Git 部署。您可以从 Azure Website Control 面板上获取您的 FTP 服务器证书，在右侧侧边栏的 **Dashboard** 标记下。如果您想使用 Git，仅需要把您的 **.user.ini** 文件放置在您本地存储库的根下，然后在您的 Azure Website 库中启动提交。

本教程有一个部分专门解释如何配置您的 Azure Website Git 存储库以及如何启动部署提交。参见 [Deploying from Git](http://symfony.com/doc/current/cookbook/deployment/azure-website.html#deploying-from-git). 您也可以在官方网页 [PHP MSDN documentation](http://blogs.msdn.com/b/silverlining/archive/2012/07/10/configuring-php-in-windows-azure-websites-with-user-ini-files.aspx) 获取更多关于配置 PHP 内部设置的信息。

## 启动 PHP intl Extension

