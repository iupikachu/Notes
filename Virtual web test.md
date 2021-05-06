**Virtual web test**



简介

Web测试很容易在应用程序下频繁中断测试的发展，在实践中导致了大量的维护工作。开发人员通常检查测试通过GUI与应用程序的交互。现有的自动化测试修复技术侧重于代码，完全忽略应用程序的可视方面。我们提议做个测试修复技术是由应用程序的视觉分析通知。我们的方法获取相关的视觉信息通过快速图像处理测试执行和分析它们。







​	Abstract 说明这篇论文的主要贡献、方法特色与主要内容。这篇论文的abstract清晰地说明了问题——Web测试很容易在应用程序下频繁中断测试的发展，在实践中导致了大量的维护工作，导致问题的原因——现有的自动化测试修复技术侧重于代码，完全忽略应用程序的可视方面，解决方案——获取相关的视觉信息通过快速图像处理测试执行和分析。

​	Introduction 的功能是介绍问题的背景和起源，交代前人在这个题目上已经有过的主要贡献，说清楚前人留下来的未解问题，以及在这个背景下这篇论文的想解决的问题和它的重要性。这篇论文的Introduction介绍了现在的通用方法以及产生的问题，并且提出了自己的新的测试修复算法。

​	 第二部分  A WEB TEST BREAKAGE TRAVELOGUE 中用了一个很长的句子都是围绕Water修复算法的一些不足之处。但是前面又有一些描述water算法的描述性语句。再跟三个缺点，不如拆成两个句子，一个描述算法，一个阐述缺点，更加清晰。

While the repair procedure of Water has a straightforward design and can manage a considerable number of cases related to locators or assertions,it has a number of limitations that derive from its DOM-related narrowness: First, considering only on the DOM as a source where to find possible repairs may be insufficient to find candidate fixes.	Second, this can lead to a large number of false positives [11]. Third,the algorithm attempts repairs always at the point in which the test stops, which makes it impossible to handle propagated breakages.		 

