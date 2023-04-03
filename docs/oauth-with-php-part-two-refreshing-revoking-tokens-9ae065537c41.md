# 使用 PHP 的 OAuth 第二部分:刷新和撤销令牌

> 原文：<https://medium.com/square-corner-blog/oauth-with-php-part-two-refreshing-revoking-tokens-9ae065537c41?source=collection_archive---------1----------------------->

> 注意，我们已经行动了！如果您想继续了解 Square 的最新技术内容，请访问我们的新家[https://developer.squareup.com/blog](https://developer.squareup.com/blog)

这是第 1 部分的后续，第 1 部分讨论了从授权码创建访问令牌。

在我们使用 OAuth 的 PHP 旅程的第一部分结束时，我们有两个脚本:一个`index.php`将开始 OAuth 过程，另一个`callback.php`将接受来自 Square 的结果，并使用提供的授权代码创建一个访问令牌，您的应用程序可以使用该令牌代表商家使用 Square 的 API。

你新制作的访问令牌将在大约一个月内工作良好，但随后你的令牌将到期，你的应用程序将无法使用 Square 的 API 做任何事情，直到商家重新授权该应用程序。这对您的用户来说不太方便，所以幸运的是，有一种方法可以以编程方式刷新您的 OAuth 凭证，而不需要您的最终用户授权您的应用程序。仅当您的权限不会更改您的应用程序权限时，刷新您的令牌才有效。任何时候你想改变你的应用程序可用的范围，你必须重新向商家进行认证。

## 到期日期

当您将授权码换成访问令牌时，您将得到如下 json 响应:

```
{ 
  "access_token": "sq0atp-XXXX",
  "token_type": "bearer",
  "expires_at": "2017-12-22T19:11:30Z",
  "merchant_id": "XXXXXXXXX"
}
```

您将希望跟踪那个`expires_at`时间，以便能够及时刷新令牌。您甚至可以在令牌过期后进行刷新，只要您在过期后 15 天内进行刷新。

## 刷新端点

刷新您的访问令牌的端点与我们的大多数端点略有不同，因为您需要使用您的`client_secret`而不是标题中的`access_token`进行授权。下面是 PHP 使用`file_get_contents`代码的样子:

```
//Refresh the token
$url = "[https://connect.squareup.com/oauth2/clients/$client_id/access-token/renew](https://connect.squareup.com/oauth2/clients/$client_id/access-token/renew)";
$data = array(
    'access_token' => $access_token
 );$options = array(
    'http' => array(
        'header'  => "Content-type: application/json\r\n".  
        "Authorization: **Client $client_secret\r\n",**
        'method'  => 'POST',
        'content' => json_encode($data)
    )
);var_dump($options);
$context  = stream_context_create($options);
$result = file_get_contents($url, false, $context);echo "Result from /oauth2/clients/$client_id/access-token/renew:";
var_dump($result);
```

> 需要注意的重要一点是，如果一切都正确，但是在请求体中提供了一个无效的`access_token`,您将得到一个 404 响应，因为找不到访问令牌。

如果一切顺利，您应该会得到类似于您第一次获得访问令牌时的响应，现在的刷新日期比您刷新的日期晚一个月:

```
{
  "access_token": "sq0atp-XXXXXXX",
  "token_type": "bearer",
  "expires_at": "2017-12-22T19:49:36Z",
  "merchant_id": "XXXXXXXXX"
}
```

这就是刷新您的访问令牌所需做的全部工作！现在，您的应用程序将能够代表其他商家继续发出请求。

## 被撤销端点

撤销访问令牌会删除它对 Square 商户帐户的任何访问权限。这个端点的访问类似于 renew 端点，头中有`client_secret`:

```
//Revoke the token
$url = "[https://connect.squareup.com/oauth2/revoke](https://connect.squareup.com/oauth2/revoke)";
$data = array(
    'access_token' => $access_token,
    'client_id' => $client_id
 );$options = array(
    'http' => array(
        'header'  => "Content-type: application/json\r\n".  
        "Authorization: Client $client_secret\r\n",
        'method'  => 'POST',
        'content' => json_encode($data)
    )
);var_dump($options);
$context  = stream_context_create($options);
$result = file_get_contents($url, false, $context);echo "Result from /oauth2/revoke:";
var_dump($result);
```

如果您的请求成功，那么 API 将返回以下响应:

```
{
  "success": true
}
```

现在，您创建的访问令牌已经失效，不能使用或刷新。您的最终用户需要再次授权您的应用程序，以便您的应用程序能够代表他们使用 Square 的 API。

## 代码

为了简化一切，我将所有代码(包括来自第一部分的授权)包含在一个页面中，该页面将把您重定向到 Square，创建一个访问令牌，更新令牌，然后一次性撤销它。

现在，您应该有了一个完整的 PHP 示例，可以用 OAuth 做任何事情。如果你有任何问题，请在这个帖子上留下评论，当然也可以看看官方的 [Square OAuth 文档](https://docs.connect.squareup.com/api/oauth)。