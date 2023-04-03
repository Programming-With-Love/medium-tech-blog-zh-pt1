# 声明式服务——从菲利克斯 SCR 到 OSGi

> 原文：<https://medium.com/globant/declarative-services-from-felix-scr-to-osgi-4770509f1965?source=collection_archive---------0----------------------->

![](img/f167db042e0b235cbbdb4008f7f1161d.png)

# **案情**

到目前为止，任何从头开始的 AEM 项目都使用 Apache Felix 的*服务组件运行时* (SCR)注释在后端捆绑包上实现 OSGi 声明式服务。然而，在 AEM 6.5 中，情况不再是这样了，因为最新的 Maven 原型生成的项目在它们的包中使用了标准的 OSGi v6 声明性服务注释。

那么，如果一个客户要求迁移到 AEM 6.5，一个预先存在的为 AEM 6.3 甚至 6.2 编写的项目的 Java 代码库呢？本文将尝试描述这样做的一系列必要步骤。

# **流程**

## **改变 POMs**

我们要做的第一件事是在父模块和核心模块的 POM 中做一些改变。让我们从删除 Maven SCR 插件开始，因为这个插件不会处理 OSGi 版本 6 的注释:

```
<plugin>
  <groupId>org.apache.felix</groupId>
  <artifactId>maven-bundle-plugin</artifactId>
  <version>4.2.1</version>
  <extensions>true</extensions> <configuration>
    <instructions>
      <Embed-Dependency>*;scope=compile|runtime</Embed-Dependency>
      <Embed-Directory>OSGI-INF/lib</Embed-Directory>
      <Embed-Transitive>true</Embed-Transitive>
    </instructions>
  </configuration></plugin>
```

然后我们将把 Maven Bundle 插件版本升级到 4.2.0，它能够处理 OSGi 注释。在核心捆绑包模块 POM 中有一个额外的更改，即在捆绑包插件中添加以下配置:

```
<plugin> <groupId>org.apache.felix</groupId>
  <artifactId>maven-bundle-plugin</artifactId>
  <extensions>true</extensions>
  <executions>
    <execution>
      <id>generate-scr-metadata-for-unittests</id>
      <goals>
        <goal>manifest</goal>
      </goals>
      <phase>process-classes</phase>
      <configuration>
        <exportScr>true</exportScr>
        <instructions>
          <_dsannotations>*</_dsannotations>
          <_metatypeannotations>*</_metatypeannotations>
        </instructions>
      </configuration>
    </execution>
  </executions>
  <configuration>
    <supportIncrementalBuild>true</supportIncrementalBuild>
    <exportScr>true</exportScr>
    <instructions>
      <Embed-Dependency>*;scope=compile </Embed-Dependency>
      <Embed-Transitive>true</Embed-Transitive>
      <Import-Package>
        !com.sun.jdi,
        !com.sun.jdi.connect,
        !com.sun.jdi.event,
        !com.sun.jdi.request,
        !sun.misc,
        !sun.rmi.rmic,
        !sun.tools.javac,
        !com.ibm.security.krb5.internal,
        !sun.security.krb5,
        org.apache.sling.jcr.resource.api;version=”1.0.0",
        *
      </Import-Package>
      <Export-Package>
        my.app.exported.package,
        my.app.other.exported.package
      </Export-Package>
      <Sling-Model-Packages>
        my.app.model
      </Sling-Model-Packages>
      <Bundle-SymbolicName>
        ${project.artifactId}
      </Bundle-SymbolicName>
    </instructions>
  </configuration>
</plugin>
```

改变 POM 的最后一步是用 OSGi v6 替代旧的 Felix SCR 依赖关系:

```
 <dependency>
   <groupId>org.apache.felix</groupId>
   <artifactId>org.apache.felix.scr</artifactId>
   <version>1.6.0</version>
   <scope>provided</scope>
 </dependency>
 <dependency>
   <groupId>org.apache.felix</groupId>
   <artifactId>org.apache.felix.scr.annotations</artifactId>
   <version>1.9.6</version>
   <scope>provided</scope>
 </dependency>
 <dependency>
   <groupId>org.osgi</groupId>
   <artifactId>org.osgi.service.component.annotations</artifactId>
   <version>1.4.0</version>
   <scope>provided</scope>
 </dependency>
 <dependency>
   <groupId>org.osgi</groupId>
   <artifactId>org.osgi.annotation.versioning</artifactId>
   <version>1.1.0</version>
   <scope>provided</scope>
 </dependency>
 <dependency>
   <groupId>org.osgi</groupId>
   <artifactId>org.osgi.annotation.bundle</artifactId>
   <version>1.0.0</version>
   <scope>provided</scope>
 </dependency>
 <dependency>
   <groupId>org.osgi</groupId>
   <artifactId>org.osgi.core</artifactId>
   <version>6.0.0</version>
   <scope>provided</scope>
 </dependency>
 <! — New OSG dependencies for SCR --> 
 <dependency>
   <groupId>org.osgi</groupId>
   <artifactId>org.osgi.service.metatype.annotations</artifactId>
   <version>1.4.0</version>
   <scope>provided</scope>
 </dependency>
 <dependency>
   <groupId>org.osgi</groupId>
   <artifactId>org.osgi.service.component</artifactId>
   <version>1.4.0</version>
   <scope>provided</scope>
 </dependency>
 <dependency>
   <groupId>org.osgi</groupId>
   <artifactId>org.osgi.service.cm</artifactId>
   <version>1.6.0</version>
   <scope>provided</scope>
 </dependency>
 <dependency>
   <groupId>org.osgi</groupId>
   <artifactId>org.osgi.service.event</artifactId>
   <version>1.3.1</version>
   <scope>provided</scope>
 </dependency>
 <dependency>
   <groupId>org.osgi</groupId>
   <artifactId>org.osgi.service.log</artifactId>
   <version>1.4.0</version>
   <scope>provided</scope>
 </dependency>
 <dependency>
   <groupId>org.osgi</groupId>
   <artifactId>org.osgi.resource</artifactId>
   <version>1.0.0</version>
   <scope>provided</scope>
 </dependency>
 <dependency>
   <groupId>org.osgi</groupId>
   <artifactId>org.osgi.framework</artifactId>
   <version>1.9.0</version>
   <scope>provided</scope>
 </dependency>
```

**注意:**Felix SCR 依赖项的删除应该在本文描述的整个过程的最后*有效地完成。*

## **改变注释**

我们的下一步是用新的 OSGi v6 标准注释替换旧的 Apache Felix SCR 注释。在部署我们的后端之前，这种改变必须在整个代码上一次完成，因此建议在您的代码存储库上切出一个单独的分支，并从这个分支切出“阶段”分支，以便您可以在许多开发人员之间并行工作，或者简单地改变您的源代码的减少的子集，以减轻代码审查。

下面是注释替换的一系列不同情况。这些案例将使用代码片段以“前后”的方式进行描述。

**组件定义**

**菲利克斯 SCR:**

```
… 
 import org.apache.felix.scr.annotations.Component;
 import org.apache.felix.scr.annotations.Service;
…
…
@Component(metatype = true,immediate = true,
           label = “MyServiceImpl”, description = “My Service”) @Service(MyService.class)
public class MyServiceImpl implements MyService {
 …
 …
}
```

**OSGi v6:**

```
…
import org.osgi.service.component.annotations.Component; 
import org.osgi.service.component.propertytypes.ServiceDescription;
…

…
@Component(service = MyService.class, immediate = true) @ServiceDescription(“My Service”)
public class MyServiceImpl implements MyService {
 …
 …
}
```

请注意:

1.  在**@组件**注释中，**@服务**注释被替换为服务属性。
2.  属性 ***元类型*** 和 ***标签*** 不见了，而属性 ***描述*** 被一个 **@ServiceDescription** 注释代替

**参考文献**

用于将其他组件注入我们的组件的 **@Reference** 注释是一个简单的替换，您只需替换 imports:

**菲利克斯 SCR:**

```
import org.apache.felix.scr.annotations.Reference;
```

**OSGi v6:**

```
import org.osgi.service.component.annotations.Reference;
```

**激活/去激活/属性**

这里我们既有容易的部分也有困难的部分。最简单的部分是替换**@激活**/**@去激活**注释，这和使用**@引用**注释一样简单(*见上面的*)。

**菲利克斯 SCR:**

```
import org.apache.felix.scr.annotations.Activate;
import org.apache.felix.scr.annotations.Deactivate;
```

**OSGi v6:**

```
import org.osgi.service.component.annotations.Activate;
import org.osgi.service.component.annotations.Deactivate;
```

现在到了困难的部分:如果我们的组件具有可以使用 AEM 的 Web 控制台编辑的配置属性，它们通常会驻留在一个 XML 文件中，如下所示:

```
<?xml version=”1.0" encoding=”UTF-8"?>
<jcr:root xmlns:sling=”http://sling.apache.org/jcr/sling/1.0"
          xmlns:jcr=”http://www.jcp.org/jcr/1.0"
          jcr:primaryType=”sling:OsgiConfig”
  batch.size=”{Integer}0"
/>
```

通常，我们的组件使用 **@Property** 注释定义属性数据，然后在激活方法中读取它们。使用新的 OSGi 注释将需要更多的编码，如我们之前/之后的代码片段所示:

**菲利克斯 SCR:**

```
…
…
@Property(
  label = “Batch size”, intValue = 30,
  description = “Size of the batch to process each time”
)
public static final String BATCH_SIZE = “batch.size”; …private int batchSize
…
…@Activate
public void onActivate(ComponentContext context) {
 …
 this.batchSize =
   PropertiesUtil.toInteger(props.get(BATCH_SIZE), 30));
 …
} 
```

**OSGi v6:**

1)配置类别:

```
… 
import org.osgi.service.metatype.annotations.AttributeDefinition;
import org.osgi.service.metatype.annotations.AttributeType;
import org.osgi.service.metatype.annotations.ObjectClassDefinition;
…
… 
@ObjectClassDefinition(name = “My Service configuration”) 
public @interface MyServiceConfigProperties {
 … @AttributeDefinition(
   name = “Batch Size”,
   description = “Batch size to process at a time”,
   type = AttributeType.INTEGER
 )
 int batch_size() default 30;
 …
}
```

2)组件的类别:

```
…
import org.osgi.service.metatype.annotations.Designate;
…@Component(service = MyService.class, immediate = true) @ServiceDescription(“My Service”)
@Designate(ocd = MyServiceConfigProperties.class)
public class MyServiceImpl implements MyService {
  …
  private int batchSize
  … @Activate
  public void onActivate(MyServiceConfigProperties config) {
    this.batchSize = config.batch_size();
  }
  …
}
```

记下属性的特定名称(带“_”分隔符)；该分隔符被解释为点(“.”)在 XML 文件中。

**小服务程序**

列表中的最后一项:更改 Servlets(如果有的话)。

**菲利克斯 SCR:**

```
… 
import org.apache.felix.scr.annotations.Service;
import org.apache.felix.scr.annotations.sling.SlingServlet;
…
…
@Service(value = Servlet.class)
@SlingServlet(
  description = “My ACME Servlet — ACME-related endpoint”,
  paths = { “/bin/acme/my-servlet” },
  methods = “POST”,
  metatype = true
)
public class MyServlet extends SlingAllMethodsServlet {
 …
 …
}
```

**OSGi v6:**

```
… 
import org.osgi.service.component.annotations.Component;
…
…
@Component(
  service = Servlet.class,
  property = {
    SLING_SERVLET_PATHS + “=/bin/acme/my-servlet”,
    SLING_SERVLET_METHODS + “=” + METHOD_POST
  }
)
@ServiceDescription(“My ACME Servlet — ACME-related endpoint”) public class MyServlet extends SlingAllMethodsServlet {
 …
 …
}
```

这里主要要注意的是， **@SlingServlet** 注释没了； **@Component** 批注通过指定 ***路径*** 、 ***方法*** 、 ***资源类型*** 等暴露的接口和数据来替代其功能。在它的属性数组上。

# **结论**

在本文中，我们尽可能简洁地介绍了迁移 AEM 应用程序包中 Java 代码上使用的注释的过程。

请记住，根据您的应用程序的 BE 逻辑的大小，这可能是一个漫长的过程，并且必须在部署之前立即完成，因为两个注释处理插件(Felix SCR 插件和 Maven Bundle 插件 4.2.0+)不能共存。