# 以下为论文翻译

> [原文链接:https://ieeexplore.ieee.org/document/8455965](https://ieeexplore.ieee.org/document/8455965)
> *2018 17th IEEE International Conference On Trust, Security And Privacy In Computing And Communications/ 12th IEEE International Conference On Big Data Science And Engineering*

> *Samir G. Sayed*
> *Mohamed Shawkey*

### 摘要
可移植文档格式(PDF)是一种被广泛接受的文档格式。然而，它已经成为了对恶意软件开发人员和漏洞研究人员最具吸引力的攻击目标之一。恶意PDF文件可用于针对个人、政府和金融部门的高级持久威胁(apt)。现有的工具，如入侵检测系统(IDSs)和杀毒软件包，在减轻这类攻击方面效率低下。这是因为这些技术需要定期更新新的恶意PDF文件，这些文件每天都在增加。本文提出了一种基于数据挖掘技术的恶意PDF文件检测算法。该算法由特征选择阶段和分类阶段组成。利用特征选择阶段，从PDF文件中提取最优特征个数，以较小的计算开销实现较高的检出率和较低的错检率。实验结果表明，该算法可以达到99.77%的检出率，99.84%的准确率，0.05%的错检率。

##### 关键词：
*恶意PDF检测，恶意软件检测，数据挖掘，引力搜索算法(GSA)。*

### 第一部分 介绍

自1993年Adobe Systems公司发明可移植文档格式(PDF)[^1]以来，它已成为广泛接受的格式。这是因为PDF文档完全独立于操作系统、硬件和软件。尽管所有这些特性都简化了对此类文档的处理，但它们也使PDF成为恶意软件开发人员利用的最吸引人的引擎之一。因此，PDF攻击的数量在过去的几年里大大增加了[^2][^3][^4][^5]。一旦系统被利用，这些特性可以用于一类称为高级持续威胁(Advanced Persistent Threats, APTs)的目标是对政府机构、金融部门和个人进行[^6]间谍活动的有针对性的攻击。它们也可能用于非目标攻击，如蠕虫和僵尸网络。攻击者利用PDF文件中的漏洞，在PDF文档中注入其他恶意代码，如JavaScript、可移植可执行程序(PEs)、HTML、图像或其他恶意PDF文件。PDF漏洞和攻击用[^7]解释。

[^1]: Adobe Systems Incorporated, “PDF Reference, Fifth ed.: Adobe Portable Document Format version 1.6,” Adobe Systems Incorporated,2004.
[^2]: K. Selvaraj and N. F. Gutierrez, “The rise of pdf malware,” in Symantec Technical Report, 2010.
[^3]: J. Drake, “Exploiting memory corruption vulnerabilities in the java runtime,” Black Hat Abu Dhabi 2011, pp. 3–20, 2011.
[^4]: H. W. Daiping Liu and A. Stavrou, “Detecting Malicious Javascript in PDF through Document Instrumentation,” 44th Annual IEEE/IFIP International Conference on Dependable Systems and Networks, pp. 100 – 111, 2014.
[^5]: M. Li, Y. Liu, M. Yu, G. Li, Y. Wang, and C. Liu, “FEPDF: A Robust Feature Extractor for Malicious PDF Detection,” in 2017 IEEE Trustcom/BigDataSE/ICESS, Aug 2017, pp. 218–224.
[^6]: N. A. S. Mirza, H. Abbas, F. A. Khan, and J. A. Muhtadi, “Anticipating Advanced Persistent Threat (APT) countermeasures using collaborative security mechanisms,” International Symposium on Biometrics and Security Technologies (ISBAST), pp. 129–132, 2014.
[^7]: D. Stevens, “Malicious PDF Documents Explained,” IEEE Security & Privacy, vol. 9, no. 1, pp. 80–82, Jan.-Feb. 2011.


在[^8]中，给出了一个针对会议论文呼叫的恶意PDF文件攻击实例。PDF文件中注入的恶意软件安装了一个后门，以访问目标用户的网络。假定受害者是军事当局或如`[9]`[^9]所述的国防承包商。`[10]`[^10]中给出了另一个示例，用于通过附带恶意PDF文件的垃圾邮件进行非目标攻击。在社会工程技术的帮助下，邮件接收者被说服打开所附的恶意PDF文件。

[^8]: F. Schmitt, J. Gassen, and E. Gerhards-Padilla, “PDF SCRUTINIZER: Detecting JavaScript-based Attacks in PDF Documents,” in Tenth An-nual International Conference on Privacy, Security and Trust, 2012, pp. 104–111.

[^9]: The H security, “Targeted attacks on arms manufacturers continue,” 2011. [Online]. Available: http://www.h-online.com/security/news/item/Targeted-attacks-on-arms-manufacturers-continue-1283425.html

[^10]: P. O. Baccas, “Who ordered spam? New trick in PDF malware uncovered,” 2011. [Online]. Available: http://nakedsecurity.sophos.com/ 2011/04/18/orders-spam-new-trick-in-pdf-malware/


从上面的例子可以得出结论，使用PDF文档对个人、公司和政府来说是一把双刃剑。为了获得使用PDF文档的优点和最小的缺点，我们引入了一些研究工作来检测和/或防止恶意PDF文档。现有的入侵检测系统(IDSs)和杀毒包等工具都是基于启发式和签名的[^11]技术。然而，这些技术是低效的，因为它们需要定期更新新的恶意PDF文件的签名，而这些恶意PDF文件每天都在增加。因此，迫切需要其他技术来检测恶意PDF。近年来，PDF文件的动态分析、静态分析和混合分析已经成为基于签名和启发式算法的替代方法。

[^11]: N. Nissim, A. Cohen, R. Moskovitch, A. Shabtai, M. Edry, O. Bar-Ad, and Y. Elovici, “ALPD: Active Learning Framework for Enhancing the Detection of Malicious PDF Files,” in IEEE Joint Intelligence and Security Informatics Conference, 2014, pp. 91–98.


在动态分析中，PDF文件是在隔离的环境(虚拟操作系统)中打开的。可以通过监视PDF文件与其系统的交互来提取PDF行为:网络、注册表(适用于Windows OS)、文件系统和其他OS组件。根据从虚拟系统收集的信息，对文件进行恶意或良性分类。基于动态分析的系统有CWSanbox[^12]、MalOffice[^13]、JSAND[^14]、MDScan[^15]和ShellOS[^16]。一般来说，动态分析技术可以实现较高的检出率和较低的错检率。这些优点是以仪器仪表、时间消耗和计算开销为代价的。随后，这种基于动态分析的方法只局限于恶意软件研究系统。由于这些限制，出现了静态分析和混合分析来分类PDF文件。静态分析是通过从良性和恶意文件中提取一些特征来实现的。这些特性可用于分类给定的PDF文件是恶意的还是良性的。通过静态分析PDF文件的内容或结构，开展了[^17]、[^18]、[^19]、[^20]、[^21]、[^22]、[^23]这些检测恶意PDF的最新工作。

[^12]: C. Willems, T. Holz, and F. Freiling, “CWSandbox: Towardsautomated dynamic binary analysis,” IEEE Security and Privacy, vol. 5, no. 2, pp. 32–39, 2007.

[^13]:	M. Engleberth, C. Willems, and T. Holz., “Detecting malicious docu-ments with combined static and dynamic analysis,” in Technical report, Virus Bulletin, 2009.

[^14]:	M. Cova, C. Kruegel, and G. Vigna, “Detection and analysis of drive-by-download attacks and malicious JavaScript code,” in In International Conference on World Wide Web (WWW), 2010, pp. 281–290.

[^15]:	Z. Tzermias, G. Sykiotakis, M. Polychronakis, and E. P. Markatos, “Combining static and dynamic analysis for the detection of malicious documents,” in Proceedings of European Workshop on System Security (EUROSEC), 2011.

[^16]:	K. Z. Snow, S. Krishnan, F. Monrose, and N. Provos, “ShellOS: Enabling fast detection and forensic analysis of code injection attacks,” in USENIX Security Symposium, 2011.

[^17]:	M. Z. Shaﬁq, S. A. Khayam, and M. Farooq, “Embedded malware detection using markov n-grams,” in Proceedings of International conference on Detection of Intrusions and Malware, and Vulnerability Assessment (DIMVA), 2008.

[^18]:	P. Laskov and N. Srndic, “Static detection of malicious javascript-bearing pdf documents,” in Proceedings of Annual Computer Security Applications Conference (ACSAC), 2011.

[^19]:	C. Smutz and A. Stavrou, “Malicious pdf detection using metadata and structural features,” in Proceedings of 28th Annual Computer Security Applications Conference (ACSAC), 2012.

[^20]:	D. Maiorca, G. Giacinto, and I. Corona, “A Pattern Recognition System for Malicious PDF Files Detection,” in Proceedings of 8th International Conference, MLDM 2012, Berlin, Germany, July 2012, pp. 510–524.

[^21]: N. Srndic and P. Laskov, “Detection of Malicious PDF Files Based on Hierarchical Document Structure,” in 20th Annual Network & Distributed System Security Symposium, 2013.
[^22]:	D. Maiorca, D. Ariu, I. Corona, and G. Giacinto, “A Structural and Content-Based Approach for a Precise and Robust Detection of Mali-cious PDF Files,” in Proceedings of the 1st International Conference on Information Systems Security and Privacy (ICISSP), Angers, France, 2015.

[^23]: A. Cohen, N. Nissim, L. Rokach, and Y. Elovici, “SFEM: Structural Feature Extraction Methodology for the Detection of Malicious Ofﬁce Documents Using Machine Learning Methods,” in Expert Systems with Applications, vol. 63, 2016, pp. 324–343.


在文献中，针对PDF文件中只检测嵌入的恶意Javascript代码，已经开展了[^24]、[^25]、[^26]、[^27]、[^28]、[^29]、[^30] 的研究。这些系统不如基于文件结构和内容的系统有效。由于这些系统无法检测到其他基于Flash [^20]. 的漏洞。然而，基于PDF文件内容和/或结构的系统容易受到有意攻击 [^31], [^32].

[^24]: P. Likarish, E. Jung, and I. Jo, “Obfuscated malicious javascript detec-tion using classiﬁcation techniques,” in 4th International Conference on Malicious and Unwanted Software (MALWARE), 2009, pp. 47–54.

[^25]:	K. Rieck, T. Krueger, and A. Dewald, “Cujo: efﬁcient detection and prevention of drive-by-download attacks,” in Proceedings of the 26th Annual Computer Security Applications Conference, 2010.

[^26]:	C. Curtsinger, B. Livshits, B. Zorn, and C. Seifert, “Zozzle: Fast and precise in-browser javascript malware detection,” in Proceedings of the 20th USENIX Conference on Security, Berkeley, CA, USA: USENIX Association, 2011.

[^27]: D. Canali, M. Cova, G. Vigna, and C. Kruegel, “Prophiler: a fast ﬁlter for the large-scale detection of malicious web pages,” in Proceedings of the 20th International Conference on World Wide Web, 2011.

[^28]:	T. Krueger and K. Rieck, “Intelligent defense against malicious javascript code,” Praxis der Informationsverarbeitung und Kommunika-tion, vol. 35, no. 1, pp. 54–60, 2012.

[^29]: G. Schwenk, A. Bikadorov, T. Krueger, and K. Rieck, “Autonomous learning for detection of javascript attacks: Vision or reality?” in Proceedings of the 5th ACM Workshop on Security and Artiﬁcial Intelligence, New York, NY, USA: ACM, 2012, pp. 93–104.

[^30]: D. Cosovan, R. Benchea, and D. Gavrilut, “A Practical Guide for Detecting JavaScript-based Malware using Hidden Markov Models and Linear Classiﬁers,” in 16th International Symposium on Symbolic and Numeric Algorithms for Scientiﬁc Computing, 2014, pp. 236–243.

[^31]: D. Maiorca, I. Corona, and G. Giacinto, “Looking at the Bag is Not Enough to Find the Bomb: An Evasion of Structural Methods for Malicious PDF Files Detection,” in Proceedings of the 8th ACM SIGSAC Symposium on Information, Computer and Communications Security, 2013, pp. 119–130.

[^32]:  N. Srndic and P. Laskov, “Practical evasion of a learning-based clas-siﬁer: A case study,” in Proceedings of the 2014 IEEE Symposium on Security and Privacy, SP’14, Washington, DC, USA, 2014, pp. 197–211.


本文提出了一种基于数据挖掘技术niques的恶意PDF文件检测算法。该算法结合了特征选择算法和数据挖掘技术。该算法由特征选择阶段和分类阶段组成。利用特征选择阶段，从PDF文件中提取最优特征个数，以较小的计算开销实现较高的检出率和较低的错检率。因此，该算法能够达到较高的检测率和准确率，且错检率较低。

本文的其余部分组织如下。第二节提供PDF文件的结构。第三部分给出了该算法的系统模型。第四节给出了该算法，第五节给出并讨论了该算法的实验结果。第六节给出了结论和未来的工作。

### 第二部分 PDF文件的结构

如`图1`所示的PDF文件由四个组件组成，它们是文件头、对象、交叉引用(xref)和文件尾。文件头提供有关PDF语言版本的信息。文件头包含在PDF文件的开头。如果缺少文件头，PDF呈现程序将忽略该文件。头后面跟着由一个或多个对象组成的文件主体。有几种类型的对象，如字符串、数字、字典、布尔值和流。对象具有包括字体、图形、页面和嵌入代码(如JavaScript和Acrobat表单)在内的信息。字典对象包含更简单的对象类型，如名称、数组和数字。流用于存储大量嵌入式数据，如图像、多媒体、字体和JavaScript。良性PDF文件中的流通常包含文本、字体和图片。然而，恶意PDF文件中的流通常包含JavaScript。攻击者使用压缩流隐藏恶意JavaScript代码，否则这些恶意代码将是纯文本，并且很容易看到。交叉引用(xref)表包含PDF文件中所有对象的列表和这些对象开头的偏移量。PDF文件的末尾提供了关于交叉引用表的位置偏移量和对象数量的信息。

![图1 PDF文件结构](https://upload-images.jianshu.io/upload_images/3229480-c076f7810bd14d74.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

本文从一个给定的PDF文件中提取了一组特征。这组特性用于识别PDF文件是良性的还是恶意的。使用自定义工具从培训数据中使用的PDF文件中提取特性。所有这些特性都是静态特性，可以通过解析给定的PDF文件来提取，而不需要访问PDF文件本身。通过选择最优特征个数，该算法可以在较小的计算开销下实现较高的检测率和较低的错检率。有15个特征表示文件中特定关键字的频率。一些关键字在恶意文档中比在良性文档中使用得更多。例如，在本文使用的数据集中，大约79%的恶意文档中发现了JavaScript关键字，而只有4%的良性文档中发现了JavaScript关键字。数据描述特性提供关于文档内容的信息。本研究使用了三个数据描述特征。第一个和第二个特征分别表示流对象内部和外部的熵。第三个特性计算文件最后一个结束(EOF)关键字之后的字节数。在PDF文件中可以通过多种方式进行混淆。例如，文件的头通常需要10个字节。这个头文件应该在文件的前1024字节中找到。在良性PDF文件中，头通常是文件中的第一项。一些恶意文件倾向于隐藏这个头，并将它从PDF文件的开头移开，以欺骗反病毒扫描器。这里使用两个特性来指示PDF文件中的混淆。第一个特性指示标头是否混淆。第二个特性计算模糊关键词的数量。

### 第三部分 系统模型

所提出的算法如`图2`所示。该系统由培训阶段和测试阶段两部分组成。在<font coler = red>图2(a)</font>所示的培训阶段，使用一组恶意和良性的PDF文件作为培训数据。自定义特征提取工具所获得的特征被用于特征选择算法和数据挖掘技术。本文采用改进的二值引力搜索算法(IBGSA)[^33]来选择分类精度最高、错检率最低的特征。通过分类器计算了IBGSA算法的适应度函数。适应度函数计算为真正值、真负值、假正值和假负值的函数。在本文中，我们使用了两个分类器来获得最佳结果。这些分类器是随机森林[^34]和决策树[^35]。从训练阶段获得的最佳特征集用于测试阶段，如<font coler = red>图2(b)</font>所示。在特征提取算法的输入处使用待测PDF文件，只提取训练阶段选择的特征。然后，分类器使用这些特性来判断PDF是恶意的还是良性的。

[^33]:	E. Rashedi and H. Nezamabadi-pour, “Feature subset selection using improved binary gravitational search algorithm,” Journal of Intelligent & Fuzzy Systems, vol. 26, pp. 1211–1221, 2014.

[^34]:	L. Breiman, “Random Forests,” Machine Learning, vol. 45, no. 1, pp. 5–32, 2001.

[^35]:	J. R. Quinlan, “Simplifying decision trees,” International Journal of Man-Machine Studies, vol. 27, no. 3, pp. 221–234, 1987.


### 第四部分 我们提出的恶意PDF文件检测算法

如第三节所述，该算法由训练和测试两个阶段组成。训练阶段由特征提取、特征选择和分类器组成。测试阶段包括特征提取和分类器。这些组件的详细信息如下所示。

![图2(a)](https://upload-images.jianshu.io/upload_images/3229480-794b236527b278d9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![图2(b)](https://upload-images.jianshu.io/upload_images/3229480-2cdabb295377a598.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##### A、特征提取

特征选择的目标是找到产生最佳分类结果的特征子集。它还删除了不协调和不相关的特性。特征选择对于n个离散二进制空间来说是一个挑战，每个维度都可以是0或1来判断是否使用该特征。需要注意的是，所有长度为n的可能向量的轨迹都是不实用的，因此可以使用启发式搜索算法来获得为所考虑问题提供最佳解决方案的向量。

本文采用改进的二元引力研究算法(IBGSA)[^33]作为特征选择算法。IBGSA是一种二进制空间的启发式搜索算法。将IBGSA算法与随机森林[^34]和决策树[^35]分类器一起使用，以训练从PDF文件中提取的所有可能的特征子集。该算法的目标是选择分类精度最高、错检率最低的特征子集。然后，这些选择的功能被用来分类PDF文件是恶意的还是良性的。下一节解释IBGSA算法及其作为特征选择算法的应用。正如在[^33]中所解释的，IBGSA的目标是减轻导致BGSA算法性能下降的停滞效应。停滞是解决方案落在局部最小值的情况。

##### B、改进的二进制引力搜索算法（IBGSA）

本算法采用IBGSA算法[^33]作为特征选择算法，如`图2(a)`所示。IBGSA是BGSA的改进版本，BGSA是原始算法引力研究算法(GSA)[^36]的二进制版本。GSA是一种针对实值搜索空间的优化算法。然而，BGSA是针对二进制值的搜索空间开发的。首先简要介绍了BGSA算法，以便更好地理解IBGSA算法。

[^36]:	E. Rashedi, H. Nezamabadi-pour, and S. Saryazdi, “GSA: A Gravita-tional Search Algorithm,” Information Sciences, vol. 179, no. 13, pp. 2232–2248, 2009.

[^37]:	D. Halliday, R. Resnick, and J. Walker, Fundamentalsof Physics. John Wiley and Sons, 1993.

[^38]:	E. Rashedi, H. Nezamabadi-pour, and S. Saryazdi, “BGSA: binary gravitational search algorithm,” Natural Computing, vol. 9, no. 3, pp. 727–745, 2009.



原始算法GSA是一种基于牛顿引力和运动定律的启发式算法。万有引力定律指出，每一个粒子的质量都以一种叫做“万有引力”的力吸引着每一个粒子[^37]。GSA试剂被认为是粒子，其性能由其质量来衡量。所有的粒子都用引力互相吸引。这个力使质量较轻的粒子向质量较重的粒子移动。最大质量代表问题的最优解。提出了BGSA[^38]算法来解决二值问题。在BGSA算法中，每个agent (particle)都有四个参数:位置、被动引力质量、主动引力质量和惯性质量。惯性质量和引力质量由适应度函数计算，而质量位置表示问题的解。在算法的连续迭代过程中，对引力和惯性质量进行了概率调整。BGSA算法的数学解释如下:假设存在一个含有N个agent(粒子)的系统，其中第i个agent的位置为:

$$ X_i=(x_i^1,x_i^2,...,x_i^d,...,x_i^n),\quad \forall i = 1,2,3,...,n $$ (1)

其中n为空间维数，$x_i^d$ 为第i个agent在第d维数中的位置。每个位置 $x_i^d$ 只取二进制值(1或0)，第i个agent在t时刻的质量更新如下:

$$ M_i(t)=\frac{m_i(t)}{\sum_{k=1}^Nm_k(t)},\quad\forall i=1,2,…,N $$ (2)

  其中

$$ m_k(t)=\frac{fit_k(t)-worst(t)}{best(t)-worst(t)} $$ (3)

其中 $fit_k(t)$ 是 agent k 在 t 时刻的适应度值，对于最大化问题，分别给出了$best(t)$和$worst(t)$。

$$ bset(t)=\max \limits_{k\in\{1,2,..,N\}}fit_k(t) $$ (4)

$$ worst(t)=\min \limits_{k\in\{1,2,..,N\}}fit_k(t) $$ (5)

第 d 维第 i 个 agent 在第 t 时刻作用的总力 F 计算方法为:

$$ F_i^d(t)=\sum\limits_{j \in kbset,j \neq i} \gamma_j\ G(t) \frac{M_i(t)×M_j(t)}{R_{ij}(t)+\epsilon} (x_j^d(t)-x_i^d(t)) $$ (6)

$R_{ij} (t)$为第i个agent到第j个agent在t时刻的汉明距离：

$$ R_{ij}(t)=\sum\limits_{d=1}^n\ |\ x_j^d(t)-x_i^d(t)\ | $$ (7)

$G(t)$的值是重力常数G0的初值与时间t的函数:

$$ G(t)=G_0(1-\frac t T)$$(8) 

$a$的值是加速度:

$$ a_i^d(t)=\frac{F_i^d(t)}{M_{ii}(t)}$$(9)

为了利用 BGSA 算法求解优化问题，在每次迭代t中更新每个agent的位置和速度。agent的速度限制为$| v(t) |< 6$。
则下一刻的速度是：

$$ v_i^d(t+1)=\gamma_i^d×v_i^d(t)+a_i^d(t)$$(10)

第i个agent的新位置$x(t +1)$如`式(11)`所示。$γ$是一个在0和1之间选取的随机值：

$$ x_i^d(t+1)=
\begin{cases}
    \overline{x_i^d(t)} & \gamma < f(v_i^d(t+1)) \\
    x_i^d(t) & otherwise
\end{cases}
$$(11)

其中，
$$ f(v_i^d(t+1))=|\tanh(v_i^d(t+1))|$$(12)

##### BGSA 的停滞现象

停滞是一种agent降到局部最小值的情况。当某一刻的速度为零时就会发生这种情况。IBGSA的目标便是为了减轻停滞效应。

#### IBGSA 中的改进

1. 改变`公式(12)`中的函数，
2. 把`公式(7)`中的汉明距离 $R_{ij}(t)$ 除以空间维n来将其归一化，
3. 采用精英主义策略，当新agent的适应度值高于前一个agent时，更新agent的位置，否则agent将停留在原来的位置。式(12)中的函数修改如下：

$$ f(_i^d(t+1))=A+(1-A)×|\tanh(v_i^d(t+1))|$$(13)

这里面的 $A$ 由`公式(14)`得出：

$$ A=k_1(1-e^\frac{F_C}{K_2})$$(14)

agent 的新位置就成了：

$$ X_i(t+1)=
  \begin{cases}
    X_i(t+1) & fit(X_i(t+1))\geq fit(X_i(t))\\
    X^i(t) & otherwise
  \end{cases}
$$(15)


##### C、我们提出的PDF特征选择算法

[^39]:	A. Pagnin, S. A. Schellini, A. Spadotto, R. C. Guido, M. Ponti,
G.	Chiachia, and A. X. Falcao, “Feature selection through gravitational search algorithm,” in IEEE International Conference on Acoustics, Speech and Signal Processing (ICASSP), 2011, pp. 2052–2055.


在[^33]和[^39]已有研究的基础上，本文利用IBGSA作为特征选择算法。在`图(2(a))`所示的训练阶段，将分类器的检测精度作为IBGSA算法的适应度函数。本文的适应度函数计算方法为:

$$ fit_i(t)=\frac{T_P+T_N}{T_P+T_N+F_P+F_N} $$ (16)

$T_P$是真阳性代表恶意PDF文件被分类为恶意文件的数量,$T_N$是真正的正数代表良性的文件被分类为良性的数量，错检,$F_P$,良性的文件被分类为恶意的数量,$F_N$代表假阴性的数量代表恶意文件被分类为良性的文件的数量。这些值是在目标分类器完成分类过程后计算出来的。IBGSA的目标是最大化`公式(16)`中给出的适应度函数。该适应度函数表示了该算法的总体精度(ACC)。该函数的最佳值为100%，即$T_P$为恶意文件总数，$T_N$为良性文件总数，$F_N$ =0, $F_P$ =0。基于分类器后计算的适应度函数，IBGSA再次更新agent的位置，使适应度函数最大化，以此类推，直到完成IBGSA算法的迭代次数。采用三个指标检测率(DR)、错检率(FPR)和总准确率(ACC)来衡量算法的性能，如`公式(17)`所示。这些指标的数学计算方法如下:

$$
 DR = \frac{T_P}{T_P+F_N} \\
 FRP = \frac{F_P}{F_P+P_N} \\
 ACC = \frac{T_P+T_N}{F_P+F_N+T_P+T_N}
$$ (17)

`算法1`给出了采用IBGSA算法作为特征选择算法的伪代码。提出的特征选择算法将数据集分为训练子集$T_s$和评价子集$E_s$两部分。为了评估候选解的适应度，通过只选择该解所选择的特征，将$T_s$和$E_s$分别创建另外两个子集$T$和$E$。$T$用于训练分类器，$E$用于评价分类器。分类器的结果用于计算IBGSA算法的适应度函数。然后，IBGSA将适应度最高的候选解决方案返回给特征选择算法。解是选取了最优的特征集，得到如`公式(16)`所示的最优适应度值。测试阶段使用所选择的特征和经过训练的分类器，如`图(2(b))`所示。从测试的PDF文件中提取所需的特性，然后应用于分类器的输入，以确定PDF文件的恶意或良性。

| 算法1提出了恶意PDF文件检测算法 |
| :---|
|输入：训练数据集$Ts$,评估数据集,迭代次数$T$,维度(特性)$n$,最初的引力常数$G_0$,其他参数 $N$, $k_1$, $k_2$, $F_C$, $kbest$ 和 $ε$|
|输出：在$E_s$上达到最大适应度函数(精度)的特征子集$f$|
|1: $\ x_{i,j}\leftarrow Random\left \{0,1  \right \},v_{i,j}\leftarrow 0,\ \forall i=0,1,..,N\ \&\ j=1,2..,n $|
|2: $\  A \leftarrow k_1(1-e^\frac{F_C}{k_2})$|
|3: $ 不搞了，插入图片吧…… $|
| ![公式1](https://upload-images.jianshu.io/upload_images/3229480-de428c02f11c1d24.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
 |


### 第五部分 实验结果

本节给出了该算法的实验结果。收集了大量恶意和良性PDF文件的数据集。为了使训练集达到均衡，训练数据集中恶意和良性PDF文件的数量是相同的。培训共使用了22000个恶意和良性的PDF文件，数据集中没有重复。恶意PDF文件已从 EG-CERT[^40]) 和 VirusTotal[^41] 的蜜网项目中收集。而良性的PDF文件则来自三个不同的来源。大多数良性的文件都是从EG-CERT和VirusTotal中随机收集的。其余的文件是从埃及和英国的一些大学获得的。从大学中随机抽取各种PDF文件，包括学术论文、报告、考试、考试等。为了进行可靠的评估，所有收集的PDF文件都使用EG-CERT工具和VirusTotal被验证为正确的恶意或良性。数据集被划分为训练集和测试集。训练阶段使用训练集来获得最有效的特征。测试集用于在不可见的PDF文件上测量所提议的系统的性能。训练集的选择过程是基于十次交叉验证的。在众多分类技术中，由于随机森林[^34]和决策树[^35]分类器的准确性和效率[^42]，本研究选取了这两种分类器。在Intel Pentium Core 2四核计算机、2.8GHz处理器和4G Byte RAM上进行了分析。

[^40]:	egcert. [Online]. Available: http://www.egcert.eg/
[^41]:	VirusTotal. [Online]. Available: https://www.virustotal.com/
[^42]: A. A. Awad, S. G. Sayed, and S. A. Salem, “A network-based frame-work for rat-bots detection,” in 2017 8th IEEE Annual Information Tech-nology, Electronics and Mobile Communication Conference (IEMCON), Oct 2017, pp. 128–133.



在10次交叉验证的基础上，将训练集划分为10个子集。为了选择最好的特性，每个特性的频率在10次迭代中计算。研究发现，随机森林和决策树的特征数最小值分别为10、8。而随机森林的最大特征数为16，决策树的最大特征数为13。表I显示了该算法对两个分类器所选特征个数的性能。结果表明，与决策树分类器相比，随机森林分类器具有更好的分类性能。如`表1`所示，使用随机森林分类器获得最佳结果的特征数为15个。

![表1 随机森林与决策树对比](https://upload-images.jianshu.io/upload_images/3229480-9f862701f571d1a7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

为了更好地了解所提算法的性能，我们使用具有最佳特征集的随机森林分类器将所提算法与一些著名的杀毒软件包进行比较。`表2`显示了该算法相对于某些杀毒软件包的性能。该系统在检测率(DR)和总体精度(ACC)方面优于所有给定的杀毒软件包。与Avast和卡巴斯基等杀毒软件相比，该系统的错检率(0.05%)有较小的下降。但是，与需要不断更新签名数据库的杀毒包相比，该算法基于数据挖掘技术，具有专门检测恶意PDF文件的优点。因此，该算法在检测高级持续威胁(APTs)方面具有较好的性能。此外，所提出的算法是灵活的，无论是与杀毒软件包集成或一个独立的工具。该算法也可用于任何类型的PDF文件。

![表2 我们提出的系统与杀毒软件包对比](https://upload-images.jianshu.io/upload_images/3229480-6dd2ce125cfb6655.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 第六部分 总结

提出了一种基于数据挖掘技术的恶意PDF文件检测策略。实验表明，该算法由鲁棒特征选择阶段和一组分层技术组成。特征选择阶段选择的最优特征被分类阶段用来检查给定的PDF文件是恶意的还是良性的。实验结果表明，该算法能够达到99.77%的检出率，99.84%的准确率，小于0.05%的错检率。与CalamAV、TrendMicro、MacAfee和赛门铁克等杀毒软件相比，该算法具有更好的性能。该算法基于数据挖掘技术，与杀毒包相比，该算法还具有检测从未见过的恶意PDF文件的优点。因此，该算法能够较好地检测高级持续威胁(APTs)。此外，提出的算法是灵活的，无论是与防毒包集成或作为一个独立的工具。此外，该算法也可用于任何类型的PDF文件。

在今后的工作中，可以用动态分析技术对该算法进行扩充。这将进一步提高检出率、准确率和错检率。此外，该算法还将测试规避技术和模仿攻击，其中恶意PDF文件模仿良性PDF文件的结构。

### 致谢

作者要感谢埃及国家电信管理当局(NTRA)支持这项研究作为国家项目的一部分。作者还要感谢埃及计算机应急准备小组(EG-CERT)和VirusTotal提供了本研究中使用的恶意PDF文件的数据库。
