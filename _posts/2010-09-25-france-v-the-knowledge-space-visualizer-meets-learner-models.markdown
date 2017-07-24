---
author: christeplovs
comments: true
date: 2010-09-25 12:54:07+00:00
layout: post
link: https://christeplovs.wordpress.com/2010/09/25/france-v-the-knowledge-space-visualizer-meets-learner-models/
slug: france-v-the-knowledge-space-visualizer-meets-learner-models
title: 'France V: The Knowledge Space Visualizer meets Learner Models'
wordpress_id: 180
categories:
- Work
tags:
- france
- ksv
---

I have been working a lot on the Knowledge Space Visualizer to allow us to investigate the relationships between social and latent semantic networks.  A key piece in the transition from the old KSV to the new KSV has been the enhancement of author-level analyses.  Earlier versions of the KSV tended to focus on the note as the unit of analysis, which was fraught with difficulties.  I have expanded the functionality of the KSV in several important ways.  First, it no longer relies on Knowledge Forum for data input and storage.  Instead, GraphML representations of the data are used to ensure portability.   This is a major leap forward, as this has been a limitation of the KSV that has been identified in the literature (see, for example, the proceedings of the most recent JOCAIR conference).  Second, the KSV can now create latent semantic representations of participants in the discourse environment.  For the case that we're studying right now, that discourse environment is WebCT (now BlackBoard).  In this iteration of the research, the user model is simply the latent semantic vector space representation of all the user's contributions to the environment.  The mathematics of Latent Semantic Analysis make this possible:  it's a matter of scaling the vector representations of each piece of writing appropriately and then just adding the vectors together.

This, in turn, allows me to calculate the relatedness not only between documents, but between the participants based on the documents that they have contributed.  An easy future enhancement to this approach will be to also incorporate the documents that an author has read, in addition to the documents that they have contributed.  So that means that I can represent the _**Latent Semantic Network**_ between participants.

It has always been possible to represent the Social Network between participants, although Christophe and I have had good discussions about the relevance of certain measures.   For example, he has eloquently made the case the common network measures such as betweenness centrality are virtually meaningless when access to information is not controlled by individuals.  In the case of forums (rather than email) it is certainly the case that most individuals have free access to postings (i.e. they are not dependent on other members to send them individual postings).  The nature of the social network and its relationship to the intensity of communication was the focus of his 2003 paper.

We are now poised to make meaningful comparisons between the social network, based on who has read whose postings, and the latent semantic network.  We can use a variety of techniques to make these comparisons.  For example, we can outright compare the networks that are formed using the two techniques.  Alternatively, we can examine the network that is formed using one set of relations and overlay the network structure from the other set of relations.  For example, the Latent Semantic Network, looks like the following for one subset of forums from the data set (LSA-based links are shown as red lines between authors who are represented as purple triangles):
[![](http://christopherteplovs.files.wordpress.com/2010/09/aqlsna.png?w=245)](http://christopherteplovs.files.wordpress.com/2010/09/aqlsna.png)

The Social Network, based on who has read whose postings, can be overlaid (black links indicate more than 50 postings read by both parties) and we can look for interesting cases where the two networks do or do not coincide:
[![](http://christopherteplovs.files.wordpress.com/2010/09/aqlsnasnaoverlaid.png?w=245)](http://christopherteplovs.files.wordpress.com/2010/09/aqlsnasnaoverlaid.png)

We are going to spend some time thinking about what all this means, with the hopes of generating some publications as a result.  What is clear, though, is that we seem to be on to a relatively new type of learner modelling:  we are creating a representation of the learner that is the sum of what they have contributed to the discourse space.  It will be interesting to see how this fits with other conceptions about learner models, and even more interesting to see how agency over this representation could be turned over to the user.  How does this relate to Open Learner Models?
