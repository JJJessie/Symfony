# 部署在 Heroku 云

这本按部就班的教程描述了如何部署一个 Symfony 网页应用程序在 Heroku 云平台上。其内容基于在 Heroku 上出版的[原创文章](https://devcenter.heroku.com/articles/getting-started-with-symfony2)。

## 设置

创建一个新的 Heroku 网站，首先用 [Heroku 注册](https://signup.heroku.com/dc)或者用您自己的证书注册。然后在您的本地计算机上下载并安装 [Heroku Toolbelt](https://devcenter.heroku.com/articles/getting-started-with-php#introduction)。

您也可以查看[在 Heroku 上开始使用 PHP](https://devcenter.heroku.com/articles/getting-started-with-php#introduction) 的指导来获取更多的对于在 Heroku 上使用 PHP 应用程序的细节的熟悉度。

## 准备您的应用程序

在 Heroku 上部署一个 Symfony 应用程序不需要其代码的任何变化，但是需要对其配置的一些轻微调整。

默认情况下，Symfony 应用程序会登录进入您应用程序的 **app/log/** 目录。这不是很理想化因为 Heroku 使用的是[短暂的文件系统](https://devcenter.heroku.com/articles/dynos#ephemeral-filesystem)。在 Heroku 中，最好处理登录的方法是使用 [Logplex](https://devcenter.heroku.com/articles/logplex)。并且发送登录数据到 Logplex 最好的方式是通过编写 **STDERR** 或者 **STDOUT**。幸运地是，Symfony 使用的是超级好的 Monolog 库来登录。因此，一个新的日志目的就是仅仅改变一个配置文件。

打开 **app/config/config_prod.yml** 文件，把 **monolog/handlers/nested**  片段（若还未存在就创建一个）并改变 **path** 的值，从 **"%kernel.logs_dir%/%kernel.environment%.log"** 到 **"php://stderr"**：

```
# app/config/config_prod.yml
monolog:
    # ...
    handlers:
        # ...
        nested:
            # ...
            path: "php://stderr"
```

一旦应用程序被部署，运行 **heroku logs –tail** 使 Heroku 中的日志流在您的终端保持开启状态。

## 在 Heroku 中创建一个新的应用程序

创建一个您可以启动的新的 Heroku 应用程序，使用 CLI **create** 命令：

```
$ heroku create

Creating mighty-hamlet-1981 in organization heroku... done, stack is cedar
http://mighty-hamlet-1981.herokuapp.com/ | git@heroku.com:mighty-hamlet-1981.git
Git remote heroku added
```

现在您已经准备好部署应用程序，如同在下一个部分解释的那样。

## 在 Heroku 上部署您的应用程序

在您的首次部署前，您仅需要再做三件事，解释如下：

1.[创建一个 Procfile]()

2.[设置环境 prod]()

3.[启动 Heroku 的代码]()
