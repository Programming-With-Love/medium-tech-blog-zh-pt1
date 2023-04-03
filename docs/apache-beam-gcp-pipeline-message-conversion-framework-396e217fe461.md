# Apache Beam GCP 管道消息转换框架

> 原文：<https://medium.com/globant/apache-beam-gcp-pipeline-message-conversion-framework-396e217fe461?source=collection_archive---------1----------------------->

![](img/4a178fbaceb28503a82799de0cc837a2.png)

[BigData](https://en.wikipedia.org/wiki/Big_data) 正在三个方面扩展:数据量、速度和多样性。它支持通过[批处理和](https://www.geeksforgeeks.org/difference-between-batch-processing-and-stream-processing/)流处理数据。一个 [Apache Beam](https://beam.apache.org/) 是一个用于批处理和流用例的开源高级统一编程模型。使用 Apache Beam，可以使用 SDK([Java](https://www.java.com/)、 [Python](https://www.python.org/) 、 [Go](https://go.dev/) 和 [Scala](https://www.scala-lang.org/) )创建大数据处理管道。

Apache Beam 消息框架是一个消息转换、消息处理和消息构建框架。本文的读者应该对 Apache Beam 管道和管道术语有所了解。这个故事对在[数据科学](https://en.wikipedia.org/wiki/Data_science)和大数据处理领域工作的读者很有帮助。

本文旨在提供一个关于[框架](https://www.techtarget.com/whatis/definition/framework)的想法，该框架提供了对消息进行 [Pubsub](https://cloud.google.com/pubsub/docs/overview) 转换、序列化、反序列化和错误处理所需的样板代码。

# **相关使用案例**

我们将关注以下使用案例:

*   序列化和反序列化一条[发布子消息](https://cloud.google.com/pubsub/docs/reference/rest/v1/PubsubMessage)。
*   构建发布消息所需的消息属性。
*   从入站(传入)发布消息构建错误消息。
*   根据错误消息构建消息属性。
*   创建一个 PubsubMessage，用于发布(传出)到一个 [GCP](https://cloud.google.com/) (谷歌云平台)主题。
*   用所需的属性验证发布消息。
*   消息的异常处理程序。

# **框架的关键组成部分**

以下部分显示了消息转换的重要类、字段和方法。

*   `**ObjectMapper**` 字段及其使用 [Jackson JSON 库](https://en.wikipedia.org/wiki/Jackson_(API))进行**序列化**和**反序列化**的配置。

```
public static final ObjectMapper OBJECT_MAPPER;
static {
  OBJECT_MAPPER = new ObjectMapper();
  objectMapperDefaultConfiguration(OBJECT_MAPPER);
  OBJECT_MAPPER
    .setSerializationInclusion(JsonInclude.Include.NON_NUL);
  OBJECT_MAPPER
    .setSerializationInclusion(JsonInclude.Include.NON_EMPTY);
}

public static void objectMapperDefaultConfiguration(
  ObjectMapper objectMapper) {
  objectMapper.configure(
    DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES, false);
  objectMapper.enable(SerializationFeature.INDENT_OUTPUT);
  objectMapper.configure(
    SerializationFeature.WRITE_DATES_AS_TIMESTAMPS, false);
  objectMapper.registerModule(new JavaTimeModule());
}
```

*   用简单的方法创建`**PubsubMessage**` 。

```
public static PubsubMessage createPubSubMessage(byte[] payload,
  Map < String, String > attributes) {
  PubsubMessage pubsubMessage;
  if (Objects.nonNull(payload) && Objects.nonNull(attributes)) {
    pubsubMessage = new PubsubMessage(payload, attributes);
  } else if (Objects.nonNull(payload) &&
    Objects.isNull(attributes)) {
    pubsubMessage = new PubsubMessage(payload, new HashMap < > ());
  } else if (Objects.isNull(payload) &&
    Objects.nonNull(attributes)) {
    pubsubMessage = new PubsubMessage(
      ERROR_EMPTY_PUBSUB_MESSAGE.getBytes(), attributes);
  } else {
    pubsubMessage = new PubsubMessage(
      ERROR_EMPTY_PUBSUB_MESSAGE.getBytes(), new HashMap < > ());
  }
  return pubsubMessage;
}
```

*   纤薄的`PubsubMessage`以一种简单的方式。

```
public static String readablePubSubMessage(
  PubsubMessage pubSubMessage) {
  if (Objects.isNull(pubSubMessage) ||
    Objects.isNull(pubSubMessage.getPayload())) {
    return ERROR_EMPTY_PUBSUB_MESSAGE;
  } else {
    return new String(pubSubMessage.getPayload(),
      StandardCharsets.UTF_8);
  }
}
```

*   以简单的方式读取`PubsubMessage`有效载荷。

```
public static StringBuilder pubSubMessagePayload(
  PubsubMessage pubSubMessage) {
  StringBuilder sb = new StringBuilder(500);
  if (Objects.nonNull(pubSubMessage) &&
    Objects.nonNull(pubSubMessage.getPayload())) {
    sb.append(new String(pubSubMessage.getPayload(),
      StandardCharsets.UTF_8));
  }
  return sb;
}
```

*   使用[构建器模式](https://en.wikipedia.org/wiki/Builder_pattern)构建`ErrorMessage`(消息)的`ErrorMessageBuilder`类。

```
public class ErrorMessage implements Serializable {
  private String details;
  private String cause;
  private String step;
  private String system;
  private String messageSource;
  private String messageVersion;
  private String messageType;
  private String messageFormat;
  private List < RequiredField > fieldErrors;
  public ErrorMessage(ErrorMessageBuilder builder) {
    this.details = builder.details;
    this.cause = builder.cause;
    this.step = builder.step;
    this.system = builder.system;
    this.messageSource = builder.messageSource;
    this.messageVersion = builder.messageVersion;
    this.messageType = builder.messageType;
    this.messageFormat = builder.messageFormat;
    this.fieldErrors = builder.fieldErrors;
  }
  public static class ErrorMessageBuilder {
    private String details;
    private String cause;
    private String step;
    private String system;
    private String messageSource;
    private String messageVersion;
    private String messageType;
    private String messageFormat;
    private List < RequiredField > fieldErrors;
  }
  public ErrorMessageBuilder withDetails(String details) {
    this.details = details;
    return this;
  }
  public ErrorMessageBuilder withCause(String cause) {
    this.cause = cause;
    return this;
  }
  public ErrorMessageBuilder withStep(String step) {
    this.step = step;
    return this;
  }
  public ErrorMessageBuilder withSystem(String system) {
    this.system = system;
    return this;
  }
  public ErrorMessageBuilder withMessageSource(String messageSource) {
    this.messageSource = messageSource;
    return this;
  }
  public ErrorMessageBuilder withMessageVersion(String messageVersion) {
    this.messageVersion = messageVersion;
    return this;
  }
  public ErrorMessageBuilder withMessageType(String messageType) {
    this.messageType = messageType;
    return this;
  }
  public ErrorMessageBuilder withMessageFormat(String messageFormat) {
    this.messageFormat = messageFormat;
    return this;
  }
  public ErrorMessageBuilder withFieldErrors(List < FieldError >
    fieldErrors) {
    this.fieldErrors = fieldErrors;
    return this;
  }
  public ErrorMessage build() {
    return new ErrorMessage(this);
  }
}
}
```

*   消息属性验证器方法。

```
public static boolean validateMessageAttributes(Map < String, String >
  attributes)
```

*   `PubsubMessage`错误处理器。

```
public static PubsubMessage pubSubErrorHandler(
  String payload, Map < String, String > attributes, Exception e)
```

*   使用`PubsubMessage`有效负载和其他属性创建一个`ErrorMessage`。

```
public static Message createErrorMessage(
  String payload, String cause, String type, String version, String
  ...args)
```

*   `PubsubMessage`异常处理程序。

```
public static Message < ? > pubSubExceptionHandler(
  PubsubMessage pubSubMessage, String cause, String type,
  Exception e)
```

*   定义类型为`String`的不同通用消息，可以使用`[MessageFormat](https://docs.oracle.com/javase/7/docs/api/java/text/MessageFormat.html).format()`进行替换。

方法`validateMessageAttributes()`、`pubSubErrorHandler()`、`createErrorMessage()`和`pubSubExceptionHandler()`的实现细节有意留给开发者决定。他们可以扩展功能定义来满足自己的需求。

# 结论

作为读者，我希望通过这篇文章，您可能已经了解了如何使用 Apache Beam 开发一个有助于消息处理和转换的框架。

感谢您的阅读！