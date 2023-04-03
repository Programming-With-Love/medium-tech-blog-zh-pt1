# OCI 函数作为资源主体调用 OCI 服务的示例

> 原文：<https://medium.com/oracledevs/example-of-oci-function-as-resource-principal-enabled-to-invoke-oci-services-3116fc8f6c50?source=collection_archive---------2----------------------->

资源主体是一种 OCI 资源，通过其动态组的成员身份和通过策略授予动态组的权限，可以访问 OCI 资源和服务。资源主体的例子有函数和 API 网关。

由资源主体启用的功能可以例如调用对象存储服务 API 或保险库密码的 API 或另一个功能。为此，它不需要使用人类用户的私钥。私钥在运行时可用…