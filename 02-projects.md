---
layout: page
title: Projects
permalink: /projects/
tagline: "What am I building"
---

<br/>

## <a href="https://github.com/havanagrawal/clomask"><img src="{{ site.url }}/assets/img/github.png" width="25" height="25" style="padding: 0px 0px 8px 0px"/></a> Clomask - Object Detection, Segmentation and Classification using Mask-RCNN

Clomask is an application of <span class="tooltip">Mask-RCNN<span class="tooltiptext">Mask Region-based Convolutional Neural Networks generate binary image masks for objects in an image</span></span> to perform object detection and <span class="tooltip">semantic segmentation<span class="tooltiptext">associating each pixel of the image with a label</span></span> of retail products such as bottles, candy bags and cereal boxes. We trained it on a synthetically generated dataset, achieving a weighted <span class="tooltip">mAP<span class="tooltiptext"><b>a</b>verage precision is the precision for a single image averaged over multiple intersection-over-union values. <b>m</b>ean <b>a</b>verage <b>p</b>recision (mAP) is for the entire dataset</span></span> of 0.586.

In addition to working on data synthesis, performance tuning and technical writing, I also developed a scalable architecture to serve the model over the web using S3 and SQS.

**Keywords**: computer vision, deep learning  
**Project Timeline**: Sep 2018 - March 2019  
**Technologies**: Python, Mask-RCNN, AWS  
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

---  
<br/>
## FollowApp - A Low-Cost High-Efficacy Vaccination Reminder

Developed as part of a technology innovation program at Morgan Stanley, FollowApp is an end-to-end application that reminds parents and/or guardians of pending vaccinations for their children through automated calls. Their responses to the call are recorded for scheduling future calls. Among other modules, we developed an interface that was agnostic to the <span class="tooltip">IVR<span class="tooltiptext"><b>I</b>nteractive <b>V</b>oice <b>R</b>esponse is an automated telephony system that interacts with callers, gathers information and routes calls to the appropriate recipients.</span></span> service provider, allowing us to switch providers (Twilio, Exotel, imimobile) with minimal development effort. The project won the first prize for technological innovation, and has been covered [in](https://www.thebetterindia.com/121451/this-mumbai-womans-app-is-helping-slum-mothers-get-their-kids-vaccinated-on-time/) [media](https://twitter.com/morganstanley/status/930654095542771713?lang=en) [several](https://www.mid-day.com/articles/mumbai-news-mumbai-techie-creates-app-to-help-mothers-remember-when-to-vaccinate-children/18772196) [times](https://www.thehindu.com/news/cities/mumbai/an-app-that-beeps-vaccination-reminders/article21236265.ece).   

**Keywords**: scalability, system design, social good, api development  
**Project Timeline**: June 2016 - Aug 2017  
**Technologies**: Java, SQL, AngularJS, AWS, IVR  
**Libraries**: springboot, spring mvc, mysql, lombok  
