# 用 Python、熊猫和 Plotly 在一本 Jupyter 笔记本上剖析荷兰人的死亡统计数据

> 原文：<https://medium.com/oracledevs/dissecting-dutch-death-statistics-with-python-pandas-and-plotly-in-a-jupyter-notebook-68b089ac75a3?source=collection_archive---------0----------------------->

CBS(荷兰中央统计局)记录了荷兰的许多事情。并将其许多数据集作为开放数据共享，通常采用 JSON、CSV 或 XML 文件的形式。公布的数据中有一组是关于每天出生和死亡人数的。我已经把这个数据集，摄入和争论到一个 Jupyter 笔记本的数据，并进行一些可视化和分析。这篇文章描述了我的一些行动和我的发现，包括尝试使用矩阵配置文件…