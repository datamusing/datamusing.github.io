## Skill Set Extraction 

After some initial exploration of the data and the problem specification at hand, as well as some survey of related literature, the following seems to be a promising plan of attack: 

### Phase I:

Harvesting keywords and associated text segments to generate a curated set of skills. This is a simple heuristic where we look for N-grams which contain trigger words such as _skill_,  _ability_, _perform_, _knowledge_ etc. that are key indicator of skills. For instance, just looking at 2-5 grams that contain the root word __skill__ in them, we were able to surface a lot more skill tags than were in the canonical skill set column. A combination of stemming and lemmatization can further help disambiguate the usage of terms like "able, ability, abled" that usually refer to the same root word and improve the results in this phase. At the end of this step, we will have a reasonably rich set of "skill tags". 

### Phase II:

With the set of skill "tags" generated in Phase I, we would then train a Named Entity Recognition algorithm (using Apache openNLP). The idea is to annotate sentences that contain the  "skill tags" from Phase I, and use the  annotated sentences in training. Roughly speaking, this algorithm learns to detect __entities__, in this case job skills, by association with other words in the sentence,  their relative position, usage, and the parts-of-speech tagging. 

We hope to deliver a first set of results before the Friday meeting. 

Cheers,

Sudeep and Sanghamitra 