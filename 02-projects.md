---
layout: page
title: Projects
permalink: /projects/
tagline: "What am I building"

---
<br/>
## <a class="githubIcon" href="https://github.com/havanagrawal/GoodreadsScraper"><img src="{{ site.url }}/assets/img/github.png" width="25" height="25" style="padding: 0px 0px 8px 0px"/></a> Mining Book and Author Data From Goodreads

Data are scraped using a site-wide crawler on Goodreads, extracting details from `/book/show` and `/author/show` type pages. This enables the collection of a large, rich, high-quality dataset which can be used for quantitative analyses, visualization and augmenting other datasets. The crawlers are focused, enabling the collection of very specific datasets, such as all books from a <span class="tooltip">Listopia<span class="tooltiptext">Listopia is a collection of lists of books, each one being ordered by members votes. Each member who votes on a list can order their individual votes, which are then used to generate the order of the main list.</span></span> list.

**Keywords**: web crawling, data mining, asynchronous programming
**Project Timeline**: Sep 2017 - Current
**Technologies**: Python
**Libraries**: [click](https://click.palletsprojects.com/), [rich](https://rich.readthedocs.io/en/stable/introduction.html), [scrapy](https://docs.scrapy.org/en/latest/index.html), selenium

---
<br/>
## <a class="githubIcon" href="https://github.com/havanagrawal/wikidata-toolkit"><img src="{{ site.url }}/assets/img/github.png" width="25" height="25" style="padding: 0px 0px 8px 0px"/></a> Wikidata Toolkit - Bots to Fix Data Inconsistencies on Wikidata

[Wikidata](https://www.wikidata.org) is a free and open knowledge base that can be read and edited by both humans and machines, and acts as central storage for the structured data of its Wikimedia sister projects including Wikipedia, Wikivoyage, Wiktionary, Wikisource, and others. The Wikidata toolkit provides bots and scripts that work on television series and episodes to improve data quality. Here are some [stats](https://xtools.wmflabs.org/ec/www.wikidata.org/TheFireBenderBot) for the bot.

A core part of the implementation is a simple rule engine, where arbitrary constraints on the data can be verified and enforced.

**Keywords**: open source, rule engine <br/>
**Project Timeline**: July 2019 - December 2020 <br/>
**Technologies**: Python <br/>
**Libraries**: [click](https://click.palletsprojects.com/), [pywikibot](https://doc.wikimedia.org/pywikibot/master/) <br/>

---
<br/>
## <a class="githubIcon" href="https://github.com/havanagrawal/clomask"><img src="{{ site.url }}/assets/img/github.png" width="25" height="25" style="padding: 0px 0px 8px 0px"/></a> Clomask - Object Detection, Segmentation and Classification using Mask-RCNN

Clomask is an application of <span class="tooltip">Mask-RCNN<span class="tooltiptext">Mask Region-based Convolutional Neural Networks generate binary image masks for objects in an image</span></span> to perform object detection and <span class="tooltip">semantic segmentation<span class="tooltiptext">associating each pixel of the image with a label</span></span> of retail products such as bottles, candy bags and cereal boxes. We trained it on a synthetically generated dataset, achieving a weighted <span class="tooltip">mAP<span class="tooltiptext"><b>a</b>verage <b>p</b>recision is the precision for a single image averaged over multiple intersection-over-union values. <b>m</b>ean <b>a</b>verage <b>p</b>recision (mAP) is for the entire dataset</span></span> of 0.586.

In addition to working on data synthesis, performance tuning and technical writing, I also developed a scalable architecture to serve the model over the web using S3 and SQS.

**Keywords**: computer vision, deep learning <br/>
**Project Timeline**: Sep 2018 - March 2019 <br/>
**Technologies**: Python, Mask-RCNN, AWS <br/>
**Libraries**: numpy, opencv <br/>

---
<br/>
## Social Network Analysis of Dynamic Character Graphs in 18/19th Century Novels

We generate character graphs from literature using <span class="tooltip">named entity recognition<span class="tooltiptext">NER: locate and classify named entities into categories such as person names, organizations and locations</span></span> to extract characters (nodes), and co-occurrence to infer links (edges) between these characters. We also maintain the points in time (in the novel) where characters interact, resulting in dynamic (temporal) graphs that can reveal interesting patterns in narrative structure and storylines.

**Keywords**: computational linguistics, natural language processing, social network analysis, graph theory <br/>
**Project Timeline**: March 2018 - March 2019 <br/>
**Technologies**: Python, Gephi <br/>
**Libraries**: flair, igraph, networkx, stanford-nlp, nltk, selenium, scrapy, d3 <br/>

---
<br/>
## <a class="githubIcon" href="https://github.com/havanagrawal/data-512-final-project"><img src="{{ site.url }}/assets/img/github.png" width="25" height="25" style="padding: 0px 0px 8px 0px"/></a> Analyzing 19th Century Literature w.r.t. The Bechdel Test

We infer conversations between characters in literature using named entity recognition to extract characters, and an ad-hoc algorithm to infer interactions. These data are then used to perform a qualitative analysis of 19th century literature w.r.t. the Bechdel test, a popular measure of women representation in fiction and media.

**Keywords**: computational linguistics, natural language processing, human centered data science, reproducibility <br/>
**Project Timeline**: Nov 2018 - Dec 2018 <br/>
**Technologies**: Python <br/>
**Libraries**: stanford-nlp, nltk, scrapy, matplotlib <br/>

---
<br/>
## <a class="githubIcon" href="https://github.com/Morgan-Stanley/followapp-core"><img src="{{ site.url }}/assets/img/github.png" width="25" height="25" style="padding: 0px 0px 8px 0px"/></a> FollowApp - A Low-Cost High-Efficacy Vaccination Reminder

Developed as part of a technology innovation program at Morgan Stanley, FollowApp is an end-to-end application that reminds parents and/or guardians of pending vaccinations for their children through automated calls. Their responses to the call are recorded for scheduling future calls. Among other modules, we developed an interface that was agnostic to the <span class="tooltip">IVR<span class="tooltiptext"><b>I</b>nteractive <b>V</b>oice <b>R</b>esponse is an automated telephony system that interacts with callers, gathers information and routes calls to the appropriate recipients.</span></span> service provider, allowing us to switch providers (Twilio, Exotel, imimobile) with minimal development effort. The project won the first prize for technological innovation, and has been covered [in](https://www.thebetterindia.com/121451/this-mumbai-womans-app-is-helping-slum-mothers-get-their-kids-vaccinated-on-time/) [media](https://twitter.com/morganstanley/status/930654095542771713?lang=en) [several](https://www.mid-day.com/articles/mumbai-news-mumbai-techie-creates-app-to-help-mothers-remember-when-to-vaccinate-children/18772196) [times](https://www.thehindu.com/news/cities/mumbai/an-app-that-beeps-vaccination-reminders/article21236265.ece).

**Keywords**: scalability, system design, social good, api development <br/>
**Project Timeline**: June 2016 - Aug 2017 <br/>
**Technologies**: Java, SQL, AngularJS, AWS, IVR <br/>
**Libraries**: springboot, spring mvc, mysql, lombok <br/>
