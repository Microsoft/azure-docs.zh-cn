---
title: 使用 Hive 在 Azure HDInsight Hadoop 上执行分析 - Team Data Science Process
description: Team Data Science Process 的示例，演练如何在 Azure HDInsight Hadoop 上使用 Hive 来执行预测分析。
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: f72ea6ed5f0eec076d181ef56c99c4f1308a7741
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "75864156"
---
# <a name="hdinsight-hadoop-data-science-walkthroughs-using-hive-on-azure"></a>在 Azure 上使用 Hive 进行 HDInsight Hadoop 数据科学演练 

这些演练在 HDInsight Hadoop 群集中使用 Hive 执行预测分析。 它们遵循 Team Data Science Process 中所述的步骤。 有关 Team Data Science Process 的概述，请参阅[数据科学过程](overview.md)。 有关 Azure HDInsight 的简介，请参阅 [Azure HDInsight、Hadoop 技术堆栈和 Hadoop 群集简介](../../hdinsight/hadoop/apache-hadoop-introduction.md)。

其他执行 Team Data Science Process 的数据科学演练按所使用的 **平台** 分组。 有关这些示例的明细，请参阅[执行 Team Data Science Process 的演练](walkthroughs.md)。


## <a name="predict-taxi-tips-using-hive-with-hdinsight-hadoop"></a>在 HDInsight Hadoop 中使用 Hive 预测出租车小费

[使用 HDInsight Hadoop 群集](hive-walkthrough.md)演练使用纽约出租车中的数据来预测： 

- 是否支付了小费 
- 小费金额的分布

该方案是在 [Azure HDInsight Hadoop 群集](https://azure.microsoft.com/services/hdinsight/)中使用 Hive 实现的。 本演练介绍如何存储、探索和特征化纽约市出租车行程与车费数据集中的工程数据。 还可以使用 Azure 机器学习来生成和部署模型。

## <a name="predict-advertisement-clicks-using-hive-with-hdinsight-hadoop"></a>在 HDInsight Hadoop 中使用 Hive 来预测广告点击量

[使用 Azure HDInsight Hadoop 群集处理 1-TB 数据集](hive-criteo-walkthrough.md)演练使用公用 [Criteo](https://labs.criteo.com/downloads/download-terabyte-click-logs/) 点击量数据集来预测是否支付了小费以及预期的金额。 该方案的实现方式是在 [Azure HDInsight Hadoop 群集](https://azure.microsoft.com/services/hdinsight/)中使用 Hive 来存储、探索及特征化工程数据与下游样本数据。 它使用 Azure 机器学习来生成、训练和评分一个用于预测用户是否点击了某个广告的二元分类模型。 演练的最后展示了如何将这些模型之一发布为 Web 服务。


## <a name="next-steps"></a>后续步骤

有关构成 Team Data Science Process 的关键组件的讨论，请参阅 [Team Data Science Process 概述](overview.md)。

有关可用于构建数据科学项目的 Team Data Science Process 生命周期的讨论，请参阅 [Team Data Science Process 生命周期](lifecycle.md)。 生命周期概述了执行项目时，其从开始到结束所遵循的步骤。 

