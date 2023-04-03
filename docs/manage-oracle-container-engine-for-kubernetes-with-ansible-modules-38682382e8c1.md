# 使用 Ansible 模块管理用于 Kubernetes 的 Oracle 容器引擎

> 原文：<https://medium.com/oracledevs/manage-oracle-container-engine-for-kubernetes-with-ansible-modules-38682382e8c1?source=collection_archive---------2----------------------->

![](img/40c08dfc9a761722b3bb283d63acfbaf.png)

*本帖由*[*Rohit Chaware*](https://medium.com/u/4defe20cc06d?source=post_page-----38682382e8c1--------------------------------)*和*[*siva Kumar Thyagarajan*](https://medium.com/u/2ae078946a2?source=post_page-----38682382e8c1--------------------------------)*联合撰写。*

去年，我们宣布了适用于 Oracle 云基础设施的 Ansible 模块。这些 Ansible 模块使您能够自动调配和配置 Oracle 云基础架构资源。

许多开发人员和部署人员正在采用 Kubernetes 来可靠地部署、编排、扩展和管理云中的分布式应用程序。然而，在云提供商的基础设施(计算、网络和存储)上设置和维护 Kubernetes 集群既复杂又耗时，并且需要对 Kubernetes 集群操作和云提供商的基础设施层有深入的了解。

Oracle Cloud infra structure[Container Engine for Kubernetes](https://blogs.oracle.com/cloud-infrastructure/kubernetes-a-cloud-and-data-center-operating-system)是一项完全托管、可扩展且高度可用的服务，您可以使用它将容器化的应用程序部署到云中。该服务使您能够快速创建、管理和使用利用底层 Oracle 云基础架构计算、网络和存储服务的 Kubernetes 集群，而无需自行安装和维护复杂的支持 Kubernetes 基础架构。开发人员可以专注于构建容器原生微服务和无服务器应用，而无需担心 Kubernetes 集群的部署和管理。

Oracle Cloud infra structure Console、 [Terraform provider](https://www.terraform.io/docs/providers/oci/) 和各种 SDK 为 Kubernetes(有时缩写为 OKE)的容器引擎提供支持。但是，如果您使用 Ansible 进行基础设施自动化和配置管理，我们的 [Ansible 模块](http://github.com/oracle/oci-ansible-modules)包括特定于 Kubernetes 容器引擎的模块，以帮助您自动创建 Kubernetes 集群、节点池和其他相关构件。这篇文章描述了如何使用 Kubernetes 和 Ansible 的容器引擎来创建和管理一个 Kubernetes 集群。

此处描述的步骤显示了如何执行以下任务:

1.  为 Kubernetes 集群的容器引擎创建所有必要的先决资源(VCN、子网和相关资源)。
2.  使用 Kubernetes 的容器引擎创建一个 Kubernetes 集群。
3.  创建节点池。
4.  为集群下载 kubeconfig 文件。
5.  在集群上部署一个示例应用程序。

**注意:**这篇文章只展示了大剧本中的相关片段。要获得可以用来为 Kubernetes 集群创建和管理容器引擎的完整行动手册，请参见 [deploy_app_on_k8s_cluster 示例](https://github.com/oracle/oci-ansible-modules/tree/master/samples/container_engine/deploy_app_on_k8s_cluster)。该示例的自述文件提供了如何针对您的 Oracle 云基础架构租赁重现本行动手册中的步骤的说明。

# 步骤 1:创建先决资源

要为 Kubernetes 创建一个带有容器引擎的 Kubernetes 集群，您需要一个虚拟云网络(VCN)以及五个子网、一个互联网网关、一个路由表和两个安全列表，详见文档中的[。你可以参考](https://docs.cloud.oracle.com/iaas/Content/ContEng/Concepts/contengnetworkconfig.htm)[这个剧本](https://github.com/oracle/oci-ansible-modules/blob/master/samples/container_engine/deploy_app_on_k8s_cluster/setup.yaml)来设置 Kubernetes 的容器引擎的先决条件。本行动手册采用 Oracle 云基础架构网络和身份与访问管理(IAM)服务的可转换模块来创建各种构件。

# 步骤 2:创建一个 Kubernetes 集群

使用您在上一步中创建的 VCN 和子网，您可以通过使用 [oci_cluster](https://oracle-cloud-infrastructure-ansible-modules.readthedocs.io/en/latest/modules/oci_cluster_module.html) 模块创建一个新的 Kubernetes 集群。通过使用[OCI _ cluster _ options _ facts](https://oracle-cloud-infrastructure-ansible-modules.readthedocs.io/en/latest/modules/oci_cluster_options_facts_module.html)模块，您可以动态地检索 Kubernetes 版本以用于集群中的主节点和工作节点(参见[示例](https://github.com/oracle/oci-ansible-modules/blob/master/samples/container_engine/deploy_app_on_k8s_cluster/sample.yaml))。

```
- name: Create a Kubernetes cluster with OKE
  oci_cluster:
    compartment_id: "{{ cluster_compartment }}"
    name: "{{ cluster_name }}"
    vcn_id: "{{ vcn_id }}"
    kubernetes_version: "{{ k8s_version }}"
    options:
      service_lb_subnet_ids:
        - "{{ lb_subnet1_id }}"
        - "{{ lb_subnet2_id }}"
  register: result
- set_fact:
    cluster_id: "{{ result.cluster.id }}"
```

# 步骤 3:创建节点池

现在，您可以使用 [oci_node_pool](https://oracle-cloud-infrastructure-ansible-modules.readthedocs.io/en/latest/modules/oci_node_pool_module.html) 模块创建一个节点池，在三个工作节点子网中各有一个节点(每个子网位于不同的可用性域中，以确保高可用性)。您还可以使用[OCI _ node _ pool _ options _ facts](https://oracle-cloud-infrastructure-ansible-modules.readthedocs.io/en/latest/modules/oci_node_pool_options_facts_module.html)模块来选择节点的图像和形状(参见[示例](https://github.com/oracle/oci-ansible-modules/blob/master/samples/container_engine/deploy_app_on_k8s_cluster/sample.yaml))。

```
- name: Create a node pool
  oci_node_pool:
    cluster_id: "{{ cluster_id }}"
    compartment_id: "{{ cluster_compartment }}"
    name: "{{ node_pool_name }}"
    kubernetes_version: "{{ k8s_version }}"
    node_image_name: "{{ node_image_name }}"
    node_shape: "{{ node_shape }}"
    quantity_per_subnet: 1
    subnet_ids:
      - "{{ ad1_subnet_id }}"
      - "{{ ad2_subnet_id }}"
      - "{{ ad3_subnet_id }}"
```

# 步骤 4:为集群下载 kubeconfig 文件

您可以使用 [oci_kubeconfig](https://oracle-cloud-infrastructure-ansible-modules.readthedocs.io/en/latest/modules/oci_kubeconfig_module.html) 模块下载集群的 kubeconfig 文件。指定集群的 OCID，并将文件路径指定为 dest 的值，以指示 kubeconfig 文件的写入位置。

```
- name: Download kubeconfig
  oci_kubeconfig:
    cluster_id: "{{ cluster_id }}"
    dest: "{{ kubeconfig_path }}"
    force: true
```

# 步骤 5:在集群上部署一个示例应用程序

您现在可以通过使用 [k8s_raw](https://docs.ansible.com/ansible/2.5/modules/k8s_raw_module.html) 模块来部署一个示例应用程序。要断言成功部署，请使用同一个模块检索部署细节。

```
- name: Create a deployment and a service on the created OKE cluster
  k8s_raw:
    kubeconfig: "{{ kubeconfig_path }}"
    state: present
    src: "{{ deployment_yaml_path }}"
  register: result- name: Get the deployment to assert successful deployment
  k8s_raw:
    kubeconfig: "{{ kubeconfig_path }}"
    namespace: default
    kind: Deployment
    name: "{{ deployment_name }}"
  register: deployment
```

# 结论

在这篇博客文章中，我们展示了如何使用 Oracle Cloud infra structure ansi ble modules 为 Kubernetes 提供一个 Kubernetes 集群以及容器引擎中的一个节点池，并将一个示例应用程序部署到新的 Kubernetes 集群。有关这些模块的更多信息，请观看此视频:

要使用我们的 Ansible 模块尝试更多示例和解决方案，请浏览 GitHub 上的 [oci-ansible-modules](https://github.com/oracle/oci-ansible-modules) 项目中的 [samples](https://github.com/oracle/oci-ansible-modules/tree/master/samples) 目录。有关这些可用模块的文档以及有关可用动态清单脚本的详细信息，请参见[入门](https://docs.cloud.oracle.com/iaas/Content/API/SDKDocs/ansiblegetstarted.htm)和[可用模块文档](https://oracle-cloud-infrastructure-ansible-modules.readthedocs.io/en/latest/modules/list_of_cloud_modules.html)。

如果您需要帮助，请使用以下渠道:

*   [oci-ansible-modules GitHub 问题页面](https://github.com/oracle/oci-ansible-modules/issues)
*   [栈溢出](https://stackoverflow.com/)，在你的帖子中使用[Oracle-cloud-infra structure](https://stackoverflow.com/questions/tagged/oracle-cloud-infrastructure)和 [oci-ansible-modules](https://stackoverflow.com/questions/tagged/oci-ansible-modules) 标签
*   Oracle 云论坛的[开发者工具](https://community.oracle.com/community/groundbreakers/oracle-cloud/cloud-infrastructure/content?filterID=contentstatus%5Bpublished%5D~category%5Bdeveloper-tools%5D)部分
*   [我的甲骨文支持](https://community.oracle.com/community/groundbreakers/oracle-cloud/cloud-infrastructure/content)

快乐自动化使用 Ansible！