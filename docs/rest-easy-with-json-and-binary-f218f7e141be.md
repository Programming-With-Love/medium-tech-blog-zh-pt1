# Json 和二进制代码让您高枕无忧

> 原文：<https://medium.com/walmartglobaltech/rest-easy-with-json-and-binary-f218f7e141be?source=collection_archive---------0----------------------->

![](img/4a5811761a3c490088fc6c2e9fb3d792.png)

Photo credit: [Pexels](https://pixabay.com/en/tiger-lazy-sleeping-white-animal-1285229/)

在基于 SOA 的微服务模型中，大多数 POST & PUT 服务契约通过 HTTP(S)使用 JSON 或 Xml 有效负载。但是，如果您处理图像或 PII 或 HIPAA 数据，可能需要将二进制数据与 JSON 一起发送。

这篇博客解释了实现这一目标所需的方法和设置。我们假设您熟悉 Java-JAX-RS RESTful 服务设置。

有两个选项可以实现这一点:

1.  **对二进制数据使用 Base64 编码**,以便将其转换为文本——然后可以与*应用程序/json* 内容类型一起发送，作为 json 有效负载的另一个属性。这是一个可以接受的解决方案，但另一方面是它的*一点也不高效，尤其是当你处理低带宽和间歇性网络的时候。这是因为编码的图像数据将比原始数据增加大约 30%的有效载荷大小。*
2.  使用 [**多部分表单数据**](https://www.ietf.org/rfc/rfc1867.txt) 作为请求的内容类型，其中各个部分在*多部分*中，内容类型为*应用程序/json* 和*应用程序/八位字节流。*根据 [RFC](https://www.ietf.org/rfc/rfc2388.txt) ，一个应用多部分可以定义自己的内容类型。多部分表单数据本质上是一个由**附件**组成的列表，其中每个附件通常是一个文本或文件。

对于我们的用例，我们使用**选项 2** 来优化我们正在发送的图像尺寸的性能。我们概述了编写和测试这样一个 RESTful 服务的几个重要步骤。(这个解决方案是基于 Spring，JAX-RS 开发框架和 cURL 来测试的)。

JAX-RS 支持开箱即用的多部分表单数据提供者。*提供者* 是序列化&反序列化输入/输出流的实体，以便您的 Java 服务接口可以直接与 POJOS 一起工作。Jax-Rs 拥有几乎所有标准内容类型的提供者——JSON、文本、二进制等。提供者根据各种内容类型进行注册，以便在读取*内容类型*时，选择合适的提供者。为了处理多部分表单数据，您需要在附件的每个部分指定**内容类型**。

**为您的 Java 服务编写 REST 接口**

```
@Path("/employees/{id}/employeeDetails")
@POST
Response submitEmployeeDetails(
@PathParam(value="id") UUID empId,
@Multipart(value="depId", type="application/json"; required=false) UUID deptId,
@Multipart(value="details", type="application/json") EmployeeDetails employeeDetails,
@Multipart(value="photoId",type="application/octet-stream", required=false) InputStream imageInputStream) throws ServiceException;
```

MultipartProvider 必须在 CXF 总线中注册为提供者

```
<bean id="multiPart" class="**org.apache.cxf.jaxrs.provider.MultipartProvider**"/><jaxrs:providers>
 **<ref bean="multipartProvider"/>**
</jaxrs:providers>
```

> 这是相当不明显但重要的一步。即使在运行时调用服务时选择了提供者，上下文初始化也不会发生，并且各个附件的反序列化会导致异常。您可能最终要调试几个小时来找出问题所在。

**测试服务**

如果您使用 PostMan 或 Advanced REST 客户端，测试这一点可能是一个挑战，因为这些客户端只允许“文本”或“文件”作为附件。 **cURL** 解决了这个问题，它允许我们使用“ **type** 属性在表单数据的每个部分显式设置内容类型。

```
curl -X POST -H "Accept: application/json" -F "depId=**\"596ac151-53ea-460c-bd5f-2e5665307971\"**;**type=application/json**" -F "details=**{\"firstName\":\"John\",\"lastName\":\"Doe\"};type=application/json**" -F "photoId=**@/testdir/john_doe.png;type=image**" "http://yourhost.domain.com:8080/app/employees/ddbe8f98-75d4-4764-8f48-39e4a68ca187/employeeDetails"
```

**就是这样，伙计们！**

您已经准备好使用一个在单个有效负载中同时接受 JSON 和 Binary 的服务了。