---
layout: distill
title: Who's Reviewing for Natural Language Processing Conferences?
description: an analysis using public data
date: 2024-05-14

authors:
  - name: Orion Weller
    url: "https://orionweller.com"
    affiliations:
      name: Johns Hopkins University

bibliography: 2018-12-22-distill.bib

toc:
  - name: Introduction
  - name: Data
  - name: ⚠️ Caveats
  - name: Analysis
    subsections:
    - name: Overall Results
    - name: Most Egregious Non-Reviewers
    - name: Who is Reviewing the Most?
    - name: People who have published and reviewed
      subsections:
      - name: How much should researchers (and senior ones) review?
      - name: Statistics of Published and Reviewing Authors

    - name: Total Statistics
    - name: H-Index vs Reviewing?
  - name: Conclusion
  - name: Acknowledgments
  - name: BibTex

# Below is an example of injecting additional post-specific styles.
# If you use this post as a template, delete this _styles block.
_styles: >
  .fake-img {
    background: #bbb;
    border: 1px solid rgba(0, 0, 0, 0.1);
    box-shadow: 0 0px 4px rgba(0, 0, 0, 0.1);
    margin-bottom: 12px;
  }
  .fake-img p {
    font-family: monospace;
    color: white;
    text-align: left;
    margin: 12px 0;
    text-align: center;
    font-size: 16px;
  }

---



## Introduction
We all complain about reviewers -- and if you go by Twitter complaints -- ML-adjacent conference reviewing is getting worse. When reviews are released, Twitter is full of (often justified) complaints and screenshots of the terrible things reviewers are saying.

There are a number of problems in reviewing (poor quality reviews, GPT-generated reviews, reviewers who don't reply to rebuttals, etc.) but a consistent theme has been that the number of papers submitted to conferences is outstripping the number of qualified reviewers/ACs/SACs. And this makes intuitive sense: as the field grows in popularity and attracts a larger audience it will take time for newcomers to be qualified to review.

This blog post focuses on the question "who is reviewing?" Although I can't answer this question for all ML venues, I can answer it for the Natural Language Processing (NLP) community which reliably publishes a list of reviewers in their proceedings. Through this, I can gather fine-grained information for *how many times* individuals are reviewing compared to *how many papers* they publish.

## Data
I scraped all PDF proceedings from *CL conferences from the [ACL Anthology](https://aclanthology.org/) website. Each proceedings contains a list of reviewers who reviewed at the conference. Note: this is **not the number of papers** they reviewed, **only whether they reviewed/AC'd/SAC'd at all for a *CL conference**. 

<figure>
    {% include figure.html path="assets/img/proceedings.png" class="img-fluid rounded z-depth-1" %}
    <figcaption>An example of the reviewer list in EACL 2023's proceedings PDF</figcaption>
</figure>

I then matched these names to the ACL Anthology using their name matching code ([`resolve_name`](https://github.com/acl-org/acl-anthology/blob/master/bin/auto_authors.py)). Finally, I gathered the list of all papers that they published in the ACL Anthology, again using the ACL Anthology code.

However, data from 15 years ago in NLP conferences is not quite as interesting, as the explosion of growth in our field happened about 5 years ago. **Thus, I discard all reviewing and publication data before 2018 to focus on the last 5 years (2018-2023).** The final analysis is thus done for the period of 2018 - June 2023 (including ACL 2023). I only considered these venues and their Findings papers: AACL, EACL, NAACL, ACL, and EMNLP (excluding workshops).

I also manually annotated some of the data to provide deeper insight that could not be obtained automatically. I personally annotated individual's h-index, their region (Asia, North America, Europe, etc.),<d-footnote>There was only one person I randomly sampled who was from Australia so I added them to the "Asia" category even though that isn't correct. There were no persons sampled from South America or Africa. This is likely due to a lack of representation in these areas overall.</d-footnote> their pronouns as listed on their websites / invited talks,<d-footnote>Pronouns corresponds somewhat to their preferred gender. Unfortunately, I could not directly ask all persons that I annotated information for due to time. Note that I did not find any non-binary pronouns in websites and/or talk introductions.</d-footnote> their affiliation, whether they were in Academia/Industry/Government, and their seniority categorized in academic standards: student, < 7 years post-PhD is junior, > 7 years is senior. 


### ⚠️ Caveats
⚠️⚠️⚠️
- This data **only includes reviewing and publications at *CL venues**. It is possible (and likely) that people are also reviewing for ML/Speech/Vision conferences. However, I think it reasonable to expect everyone who publishes at *CL venues to also provide service for the NLP community so I don't think this provides an excuse not to review.
- There are many types of academic service (PCs, ACL board, etc.). It is non-trivial to decide a weighting for these, thus I simply count them equally -- but clearly the nature of the service is quite different. However, since the number of people who fill these extremely high workload positions is small it has little impact on the overall analysis (and none of them were annotated in the analysis section).
- The data only contains the number of **times** someone has reviewed. Thus, someone could have taken on a lighter load and have been counted equally high. However, the number of papers reviewed is not public and overall, I think this is a relatively minor issue.
- This data also doesn't capture some forms of service, such as people who sub-review.
- This data **does** capture SAC/AC/etc and I will refer to them as "reviewers" even though their job description is different. Thus when I talked about "reviewing" remember that means reviewing/AC'ing/SAC'ing etc.

### Data Quality
I manually selected only the PDF pages that contained reviewer information from the proceedings.

I then OCR'd the data using [`papermage`](https://github.com/allenai/papermage), chunked it using newlines and/or number of tokens (250 max) with an average of between 10-30 characters, and then asked `GPT-3.5-Turbo` to extract the names from the noisier OCR text. I removed the names at the end which were often cut off due to line and page breaks (e.g. only the first or last name). 

For an example of how this worked see the example below. It removed affiliations and the OCR'd page number (`xi`) and merged names that were split from the end of the page to the beginning of the newline:

<figure>
    {% include figure.html path="assets/img/example_extract.jpg" class="img-fluid rounded z-depth-1" %}
    <figcaption>An example of what the automated extraction did to the reviewer list</figcaption>
</figure>

This process is potentially noisy, so I examined 50 different `GPT-3.5-turbo` based extractions. In 49 cases it was correct -- however, in one case the last name was hyphenated when it should have not been. However, none of the manually annotated names that I annotated had hyphens so this would only impact the overall statistics section.

The other source of noise is the ACL Anthology name matching. I checked 10 names and they all seemed correct, but it is possible there are other errors I missed -- if your name doesn't match up on the ACL Anthology please contact them! 

**Overall**, I found the data extraction process to be fairly high quality. Although there may be errors that I didn't find, in aggregate the data seems sound.

## Analysis
Rather than release the full data and call people out by name, I decided to present the aggregate results. In each section I will break down specific groups of reviewers.

### Overview Results
For the period of 2018 - June 2023 (including ACL 2023).

Overview:
- There were 13,724 total reviewers who reviewed one time or more
- There were 59,278 authors who had one or more papers

Some surprising statistics:
- 2,085 reviewers published more than 5 papers at main venues but did not review (or AC/SAC/etc.) at all in the last 5 years
- 135 reviewers published zero papers but still reviewed (yay, thank you!)
- Prominent persons who did not review in the last five years include *CL conference keynote speakers, best paper award winners (many different such people), and other "leading" researchers whom you the reader have certainly heard of.

⚠️ Reminder: when I say "review" that includes AC/SAC'ing!

I was quite shocked to see such a large number of people who haven't reviewed: the community has qualified reviewers -- they just aren't reviewing over the course of five years!

I was even more shocked to see the number of senior people who aren't doing any community service for *CL venues. This was probably my biggest reason for not naming-and-shaming -- my career would be seriously hurt if these people decided not to hire me! 

### Most Egregious Non-Reviewers
Drilling in on those who are publishing > 5 papers but not reviewing/AC/SAC'ing, who are they?

I manually annotated demographics for the top 25 offenders (ranging from 30 to 100+ published *CL papers in the last 5 years):

| Category    | Type       | Count |
|:-------------:|------------|:-------:|
|       Region      | Europe <br> Asia <br> North America  | 4 <br> 12 <br> 9     |
| Pronouns    | He/Him    <br> She/Her  | 22 <br> 3  |
| Seniority   | Student <br> Junior <br> Senior <br>     | 1 <br> 2 <br> 22    |
| Affiliation | Government <br> Industry <br> Academia   | 2 <br> 7 <br> 16     |
|    H-Index          | Average <br> Min <br> Max  | 54.3 <br> 8 <br> 160+ |


We can see that these non-reviewer publishers are typically from Asia or North America, use male pronouns, are almost always senior researchers, and are generally from academia. They have an average h-index of 54, indicating that they are well-cited. They are from well-respected affiliations including the top academic and industrial groups.

### Who is Reviewing the Most?
We can also examine the opposite -- who are the kind souls who are reviewing despite not publishing in the past five years? I annotated the demographics for those who review the most with no papers, ranging from 10 to 15 reviews over the past 5 years:


| Category    | Type       | Count |
|:-------------:|------------|:-------:|
|       Region      | Europe <br> Asia <br> North America  | 8 <br> 1 <br> 16     |
| Pronouns    | He/Him    <br> She/Her  | 19 <br> 6  |
| Seniority   | Student <br> Junior <br> Senior <br>     | 0 <br> 6 <br> 19    |
| Affiliation | Government <br> Industry <br> Academia   | 1 <br> 15 <br> 9     |
|    H-Index          | Average <br> Min <br> Max  | 13.4 <br> 2 <br> 27 |

We see that those who are reviewing without publishing papers are typically from industry, from North America, and in the junior to senior researcher stage. This makes intuitive sense as there is less incentive to publish in industry, but many have ties to conferences and continue to serve. Their affiliations are generally from smaller startups as well as several from Google and Apple. We are grateful for their service to the community! 


### People who have published and reviewed

We've now covered the edge cases, where people either hadn't published or hadn't reviewed in the past five years. But what about those that have reviewed and published? 

#### How much should researchers (and senior ones) review?
There is no magic number of times that one should review per publication -- however, I think we could probably agree that it would be unkind to publish large numbers of papers and only review once. The percentiles of Papers Per Review is as follows:

| Percentile    | 0.1 | 0.2 | 0.3 | 0.4 | 0.5 | 0.6 | 0.7 | 0.8 | 0.9 | 0.95 | 0.99 |
|-------------:|:------------:|:------------:|:------------:|:------------:|:------------:|:------------:|:------------:|:------------:|:------------:|:------------:|:------------:|
| Papers Per Review |  0.5  |  1.0  |  1.0  |  1.5  | 2.0  |  2.5  |  3.0  |  4.0  |  6.3  | 9.0  |  17.7  |

A relatively service-friendly ratio might be something like 5 publications to one time reviewing/AC/SAC'ing (given that reviewers are typically asked to review five papers per conference and that it is the 85th percentile). 

On one hand, reviewing/AC/SAC'ing once per every 5 papers becomes difficult for very senior researchers, who publish lots of papers! On the other hand, our community needs senior reviewers/ACs/SACs along with the less experienced reviewers (especially for the ACs and SACs), so researchers who publish many papers without reviewing as much leaves the community bereft of their wisdom and forces more and more junior people to take on these roles.


#### Statistics of Published and Reviewing Authors


What are the demographics of those who have the worst *Papers Per Reviewing Time* ratio? 

| Category    | Type       | Count |
|:-------------:|------------|:-------:|
|       Region      | Europe <br> Asia <br> North America  | 8 <br> 6 <br> 11     |
| Pronouns    | He/Him    <br> She/Her  | 25 <br> 0  |
| Seniority   | Student <br> Junior <br> Senior <br>     | 0 <br> 1 <br> 24    |
| Affiliation | Government <br> Industry <br> Academia   | 1 <br> 8 <br> 16     |
|    H-Index          | Average <br> Min <br> Max  | 66.8 <br> 12 <br> 200+ |
|    Papers Per Reviewing Time          | Average <br> Min <br> Max  | 42.4 <br> 26 <br> 84 |

We can see that those who have reviewed but don't do it much (e.g. a high Papers Per Reviewing Time) are typically from North America, use male pronouns (notably no female pronouns), are almost always senior researchers, and generally from academia. They have an astounding average h-index of 66, indicating that they are extremely well cited.

I can tell you that these people include some of the "hottest" names in the field, ranging from prominent universities to major industry groups. 

What about people from normal ranges? I sampled people who have reviewed between 3 to 10 times and annotated until I got tired (38 total):

| Category    | Type       | Count |
|:-------------:|------------|:-------:|
|       Region      | Europe <br> Asia <br> North America  | 16 <br> 6 <br> 16     |
| Pronouns    | He/Him    <br> She/Her  | 31 <br> 7  |
| Seniority   | Student <br> Junior <br> Senior <br>     | 9 <br> 23 <br> 6    |
| Affiliation | Government <br> Industry <br> Academia   | 0 <br> 11 <br> 27     |
|    H-Index          | Average <br> Min <br> Max  | 12.7 <br> 5 <br> 32 |
|    Papers Per Reviewing Time          | Average <br> Min <br> Max  | 1.7 <br> 0.25 <br> 5.75 |

We note that this shows a much more balanced set of reviewers: mostly junior researchers (post-PhD, < 7 years), a few students, a few faculty, and mostly from academia. Those with female pronouns are still underrepresented but are more balanced than in the previous sections. Overall, reviewers come from a broad mix of university and industry affiliations, with no main pattern.


## Total Statistics
I took all the annotated results and computed the following amount of reviews that each demographic does, on average:


| Pronouns       | Average Reviews |
|------------|-------:|
|    He/Him          | 4.8  |
|    She/Her          | 6.7  |

A pretty large and striking difference between those with male vs female pronouns!

| Affiliation Region       | Average Reviews |
|------------|-------:|
|    Asia          | 3.0  |
|    Europe          | 5.3  |
|    North America          | 5.7  |

North American affiliates review the most on average, followed by Europe affiliates, and then Asian affiliates last. ⚠️ Reminder, these are based on regional affiliation and not ethnicity.

| Seniority       | Average Reviews |
|------------|-------:|
|    Student          | 5.8  |
|    Junior (<7 years post-PhD)         | 6.7  |
|    Senior (> 7 years post-PhD)         | 4.2  |

We see that those post-PhD but less than 7 years after graduation review/AC/SAC the most. Senior researchers review/AC/SAC the least.

| Academia vs Industry       | Average Reviews |
|------------|-------:|
|    Government          | 3.3  |
|    Industry          | 6.5 |
|    Academia          | 4.2  |

This debunks a lot of the common criticism I hear about reviewing, such as "people in industry aren't reviewing" (actually no, they review more than academics on average).  Even when separating these by Seniority we see that the trend holds:

| Academia vs Industry by Seniority       | Average Reviews |
|------------|-------:|
|    Senior in Government         | 3.3  |
|    Junior in Industry          | 6.5 |
|    Senior in Industry          | 6.5 |
|    Student in Academia          | 5.8  |
|    Junior in Academia          | 6.7  |
|    Senior in Academia          | 2.6  |


I think a good argument can be made that not enough students are reviewing -- but clearly we can see that students are reviewing a proportionally equivalent amount when they are invited to review.


## H-Index vs Reviewing?
The above prompted me to plot the h-index against the number of times a person reviews.  We can see a pretty strong negative correlation.  This is to be somewhat expected, but the scale of it is quite intense - if no senior people are reviewing/AC'ing/SAC'ing, we lose all their experience! Of all the people I annotated, no one with an h-index above 55 reviewed/AC'd/SAC'd more than 2 times over five years.

<figure>
    {% include figure.html path="assets/img/h_index.jpg" class="img-fluid rounded z-depth-1" %}
    <figcaption>Comparing the number of times someone has reviewed in the last five years to their H-Index</figcaption>
</figure>

## Conclusion
Thanks for reading this far and hopefully you enjoyed the data! If people want the data to conduct reasonable experiments with them feel free to get in touch, but as of right now I think it will remain private.

I'm not going to end with any suggestions, other than to say that I hope the community takes some steps to ensure reviewing is successful; as of right now, I don't feel like I get any benefit from peer review. And this sentiment building through the community only puts peer review in a downward spiral. So let's come together to counter that :)


### FAQ
> Will you release the data publicly?

Currently not - I don't want prominent members of the community to dislike me and deny me opportunities for jobs! Perhaps post-PhD (feel free to send me job offers ;)).

> Can you tell me what my (or my friends') Papers Per Reviewing Time number is?

Sure, send me an email to get yours (not your friends). But since you've done your own reviewing, feel free to compute your Papers Per Reviewing Time using those numbers!

## Acknowledgments
I'd like to thank Luca Soldaini and Kyle Lo who helped inspire this project and provided useful feedback. I'd also like to thank Marc Marone, Nathaniel Weir, Aleem Khan, and Michael Saxon for their advice on data processing and proofreading. 

## BibTeX
If you found this blogpost useful and would like to cite it, you can cite it as:
```
@misc{weller2024reviewing,
      title={Who's Reviewing for Natural Language Processing Conferences?}, 
      author={Orion Weller},
      year={2024},
      url={https://orionweller.github.io/blog/2024/reviewing/}
}
```

