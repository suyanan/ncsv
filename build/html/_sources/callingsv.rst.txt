ncsv介绍
============

SV的融合类型包括：
    - 重复(Duplication, DUP)
    - 删除(Deletion, DEL)
    - 倒位(Inversion, INV)
    - 跨染色体融合(Inter-Chromosomal Translocation, CTX)
    - 易位(Intra-Chromosomal Translocation, ITX)

ncsv的检出类型包括：
    - DUP/ITX
    - DEL/ITX
    - INV
    - CTX-1（顺式CTX）
    - CTX-2（反式CTX）


ncsv检出原理
============

    ncsv检测是基于SR、SU、DP三种类型特征的数据，经过序列组装及序列重比对来确定实际断点的sv检测算法。

ncsv名词解释
------------

测序以及比对的相关名词
>>>>>>>>>>>

- template(模板链)
    经过各种样本前处理（如酶切/超声打断、PCR等）后，测序仪实际进行测序的数据。
- read
    读段，测序仪产生的测序原始数据经过比对后的数据。
- read_len
    读段长度，在一次测序中通常为固定值，常见值为50,75
- softclip
    若read的部分序列很好的比对到了基因组，但是剩余部分无法完好的比对到该区域（可能产生复杂的Indel），则会将该剩余部分标记为softclip(简称sc)。
- 读段质量值
    指经bwa比对产生的读段的质量值属性。质量值越高，代表该比对的可信度越高，质量值较低的原因通常是该比对位于重复区域或者比对中带有较复杂的插入缺失。
- PE(双端测序)
    PairEnd Sequencing，测序仪从模板链两端分别测序，对同一个 模板链 会产生2个read数据，分别命名为read1和read2。
- isize
    bam的插入片段长度，或称为模板链长度，即read对应的模板链的长度。

sv分析的相关名词
>>>>>>>>>>>>>

- SR
    Split Reads，SV判断的一种支持数据类型，其来源于序列比对（bwa）产生read的softclip部分。
    softclip的产生来源通常有序列插入、融合、比对错误等，所以可以作为sv的一种潜在支持进行分析。
- SU
    Single Unmap，SV判断的一种支持数据类型，其来源于序列比对（bwa）产生的未匹配（unmap）序列。
    unmap序列的产生原因通常是因为该部分带有难以解析的插入、融合序列，所以可以作为sv的一种潜在支持进行分析。
- DP
    Discordant Pair，SV判断的一种支持数据类型，其指的是序列比对中模板链长度异常( isize > isize_max )的读段对。
    DP产生的原因通常是由于序列融合，导致读段两侧分别比对到基因相距较远的两部分。若DP两侧的读段质量值够高，则可以作为融合的最直接证据。
- isize_max
    该样本测序的正常模板链长度最大值，其值为isize排序并剔除极端值(<1%，或>99%)后的 isize均值 + 3倍方差
- read_inner_span
    自创变量，用于度量在给定断点位置可能存在的DP支持读段距离断点的最大距离，其值为 isize_max - 1.5* read_len 。
- 模板链覆盖数
    跨越给定断点的所有正常模板链（不支持任何变异）的计数，相比于正常的bam位点深度，会增加那些位点在配对read中间的模板链。



ncsv使用说明
============

ncsv安装环境
----------

    * 操作系统：Linux
    * 运行软件: python2.7
    * CPU要求: 双核，推荐8核及以上
    * 内存要求： 4G以上

ncsv调用方法
-----------

    NcSV提供一个外部调用脚本wrapper_run_sv.py

