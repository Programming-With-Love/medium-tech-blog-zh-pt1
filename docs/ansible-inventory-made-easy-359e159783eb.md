# 易变库存变得简单

> 原文：<https://medium.com/walmartglobaltech/ansible-inventory-made-easy-359e159783eb?source=collection_archive---------2----------------------->

![](img/4e631c051d6f3c6076899c3251150958.png)

Image by [Hugo Hercer](https://pixabay.com/users/loginueve_ilustra-12954610/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=5193383) from [Pixabay](https://pixabay.com/?utm_source=link-attribution&utm_medium=referral&utm_campaign=image&utm_content=5193383)

在 Ansible 中，没有库存，你几乎不能执行任何操作。即使在控制主机(本地)上执行的临时操作也需要清单，即使该清单仅包含一台主机(本地)。清单是 Ansible 架构结构中非常基本的部分。当执行 ansible 或 ansible-playbook 时，它必须能够引用库存。清单是存在于…中的文件或目录