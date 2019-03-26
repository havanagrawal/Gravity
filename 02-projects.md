---
layout: page
title: Projects
permalink: /projects/
tagline: "What am I building"
---

<br/>

## <a href="https://github.com/havanagrawal/clomask"><img src="{{ site.url }}/assets/img/github.png" width="25" height="25" style="padding: 0px 0px 8px 0px"/></a> Clomask - Object Detection, Segmentation and Classification using Mask-RCNN

Clomask is an application of <span class="tooltip">Mask-RCNN<span class="tooltiptext">Mask Region-based Convolutional Neural Networks generate binary image masks for objects in an image</span></span> to perform object detection and <span class="tooltip">semantic segmentation<span class="tooltiptext">associating each pixel of the image with a label</span></span> of retail products such as bottles, candy bags and cereal boxes. We trained it on a synthetically generated dataset, achieving a weighted <span class="tooltip">mAP<span class="tooltiptext">average precision (AP) is the precision for a single image averaged over multiple IoU values. mean average precision (mAP) is for the entire dataset</span></span> of 0.586.

**Keywords**: computer vision, deep learning  
**Project Timeline**: Sep 2018 - March 2019  
**Technologies**: Python, Mask-RCNN  
**Libraries**: numpy, opencv  

---  
<br/>
## Social Network Analysis of Dynamic Character Graphs in 18/19th Century Novels

We generate character graphs from literature using <span class="tooltip">named entity recognition<span class="tooltiptext">NER: locate and classify named entities into categories such as person names, organizations and locations</span></span> to extract characters (nodes), and co-occurrence to infer links (edges) between these characters. We also maintain the points in time (in the novel) where characters interact, resulting in dynamic (temporal) graphs that can reveal interesting patterns in narrative structure and storylines.

**Keywords**: computational linguistics, natural language processing, social network analysis, graph theory  
**Project Timeline**: March 2018 - Current  
**Technologies**: Python, Gephi  
**Libraries**: flair, igraph, networkx, stanford-nlp, nltk, selenium, scrapy, d3  

---  
<br/>
## <a href="https://github.com/havanagrawal/data-512-final-project"><img src="{{ site.url }}/assets/img/github.png" width="25" height="25" style="padding: 0px 0px 8px 0px"/></a> Analyzing 19th Century Literature w.r.t. The Bechdel Test

We infer conversations between characters in literature using named entity recognition to extract characters, and an ad-hoc algorithm to infer interactions. These data are then used to perform a qualitative analysis of 19th century literature w.r.t. the Bechdel test, a popular measure of women representation in fiction and media.

**Keywords**: computational linguistics, natural language processing, human centered data science, reproducibility  
**Project Timeline**: Nov 2018 - Dec 2018  
**Technologies**: Python  
**Libraries**: stanford-nlp, nltk, scrapy, matplotlib  
