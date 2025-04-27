# **Error in get_anno_data(ontology) : ontology not supported yet...**

```R
do_result <- enrichDO(gene = gene,
                ont = "DO",  
                pvalueCutoff = 0.5,
                qvalueCutoff = 0.5,
                readable = T) 
```

上面代码写着参数 ont 应该是 "HDO", "HPO" 或 "MPO" 中的一个。所以 "DO" (你使用的) 确实是不支持的。

HDO = 人类疾病本体 , HPO = 人类表型本体, MPO = 小鼠表型本体。

HDO = Human Disease Ontology, HPO = Human Phenotype Ontology , MPO = Mouse Phenotype Ontology  

使用人类示例数据集（因此使用人类疾病本体 [HDO]）：

```r
do_result <- enrichDO(gene = gene,
                ont = "HDO",  
                pvalueCutoff = 0.5,
                qvalueCutoff = 0.5,
                readable = T) 
```

那这样修改后就不会出现问题了！

