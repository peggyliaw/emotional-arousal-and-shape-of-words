# emotional-arousal-and-shape-of-words
Are word representations similar between human vs. embedding model?

### Background
Words that sound spiky tend to be perceived more emotionally arousing. This finding suggests an association between shape and emotional arousal for words. Research on this topic primarily used data from human ratings. That is, participants will rate how emotionally arousing (ranging from calm to exciting) a word sounds to them. It's unclear whether the effect of arousal on shape can extent to statistical embedding models. So the question is: Does the association between emotional arousal and shape differ between human vs. statistical embedding models like Qwen3? I also looked at whether this association is different between abstract vs. concrete words. More broadly, results may tell us whether the sound symbolism phenomenon (specifically shape sound symbolism) in human can also be observed in embedding models.

To test this, I used Muraki and Pexman (2025)'s data: https://osf.io/3mywq/files/43skq. Each word has human ratings on arousal (1=calm to 9=excited). Paper: English verbs semantic norms database: Concreteness, embodiment, imageability, valence and arousal ratings for 3,500 verbs. https://doi.org/10.3758/s13428-025-02675-6

I used Qwen3 embedding model by Zhang et al. (2025). Qwen3 Embedding: Advancing Text Embedding and Reranking Through Foundation Models. https://arxiv.org/abs/2506.05176. Spefically, I used the "Qwen3-Embedding-0.6B" model: https://huggingface.co/Qwen/Qwen3-Embedding-0.6B

### Analyses and Results
**(1) Correlations between all variables**
```
Combined Spearman Correlation Coefficient (p-value) Matrix:
                     Arousal.Mean    ArousalScore      ShapeScore   Concreteness.Mean
Arousal.Mean        1.000 (0.000)   0.272 (0.000)  -0.354 (0.000)       0.165 (0.000)
ArousalScore        0.272 (0.000)   1.000 (0.000)  -0.151 (0.000)      -0.076 (0.000)
ShapeScore         -0.354 (0.000)  -0.151 (0.000)   1.000 (0.000)      -0.152 (0.000)
Concreteness.Mean   0.165 (0.000)  -0.076 (0.000)  -0.152 (0.000)       1.000 (0.000) 
```
Human-rated arousal is related to embedding arousal (r = .27, p < .001). It makes sense that more arousing words are spikier (r = -.35, p < .001 for human-rated arousal; r = -.15, p < .001 for embedding arousal).

More arousig words are also more concrete, but only for human-rated arousal (r = .17, p < .001).

Lastly, more concrete words are spikier (r = -.15, p < .001).

**(2) Model1: Arousal (human) and Concreteness (human) predicting ShapeScore (embedding)**
```
                                                   coef    std err          t      P>|t|      [0.025      0.975]
----------------------------------------------------------------------------------------------------------------
Intercept                                       -0.0024      0.001     -3.034      0.002      -0.004      -0.001
Concreteness_Mean_scaled                        -0.0050      0.001     -6.257      0.000      -0.007      -0.003
Arousal_Mean_scaled                             -0.0149      0.001    -17.954      0.000      -0.017      -0.013
Concreteness_Mean_scaled:Arousal_Mean_scaled     0.0007      0.001      0.868      0.385      -0.001       0.002
```
There was a main effect of Arousal (human) on Shape (embedding) (b = -0.02, p < .001). This means that more arousing words are spikier.

There was also a main effect of Concreteness (human) on Shape (embedding) (b = -0.01, p < .001). This means that more concrete words are spikier.

There was no interaction between Concreteness (human) and Arousal (human) (b = 0.00, p = .385). This means that the effects of arousal and concreteness on shape operate independently.

In ganeral, more arousing and more concrete words tend to be spikier. And this pattern holds similarily across levels of concreteness when using human-rated arousal. Human perception of arousal seems to have a consistent influence on shape meanings across levels of concreteness.

**(3) Model2: Arousal (embedding) and Concreteness (human) predicting ShapeScore (embedding)**
```
                                                   coef    std err          t      P>|t|      [0.025      0.975]
----------------------------------------------------------------------------------------------------------------
Intercept                                       -0.0024      0.001     -2.916      0.004      -0.004      -0.001
Concreteness_Mean_scaled                        -0.0080      0.001     -9.664      0.000      -0.010      -0.006
ArousalScore_scaled                             -0.0070      0.001     -8.403      0.000      -0.009      -0.005
Concreteness_Mean_scaled:ArousalScore_scaled    -0.0013      0.001     -1.528      0.127      -0.003       0.000
```
There was a main effect of Arousal (embedding) on Shape (embedding) (b = -0.01, p < .001). This means that more arousing words are spikier.

There was also a main effect of Concreteness (human) on Shape (embedding) (b = -0.01, p < .001). This means that more concrete words are spikier.

There was no interaction between Concreteness (human) and Arousal (embedding) (b = 0.00, p = .127). This means that the effects of arousal and concreteness on shape operate independently.

In general, more arousing and more concrete are spikier. And this pattern holds similarily across levels of concreteness when using embedding arousal scores. Embedding representations of arousal have a consistent influence on shape meanings across levels of concreteness.

**(4) Model3: Arousal (aggregated), Concreteness (human), and Group (human vs. Qwen3) predicting ShapeScore (embedding)**
```
                                                      coef    std err          t      P>|t|      [0.025      0.975]
-------------------------------------------------------------------------------------------------------------------
Intercept                                          -0.0024      0.001     -2.975      0.003      -0.004      -0.001
Group[T.human]                                  -2.052e-05      0.001     -0.018      0.986      -0.002       0.002
Concreteness_Mean_scaled                           -0.0080      0.001     -9.861      0.000      -0.010      -0.006
Concreteness_Mean_scaled:Group[T.human]             0.0030      0.001      2.580      0.010       0.001       0.005
Arousal                                            -0.0070      0.001     -8.575      0.000      -0.009      -0.005
Arousal:Group[T.human]                             -0.0079      0.001     -6.700      0.000      -0.010      -0.006
Concreteness_Mean_scaled:Arousal                   -0.0013      0.001     -1.559      0.119      -0.003       0.000
Concreteness_Mean_scaled:Arousal:Group[T.human]     0.0019      0.001      1.717      0.086      -0.000       0.004
```
There was no effect of Group (b = -0.000, p = 0.986). This makes sense becasue the values for ShapeScore are the same across group.

There was a main effect of concreteness (b = -0.008, p < .001), meaning that concrete words are spikier. Moreover, the effect of concreteness is stronger in Qwen3 (b = 0.003, p = .001).

There was a main effect of arousal (b = -0.007, p < .001), meaning that arousing words are spikier. Moreover, the effect of arousal is stronger in human (b = -0.008, p = .010).

There was no interaction between concreteness and arousal (b = -0.001, p = .119), meaning that the association between arousal and shape doesn't vary across concreteness level.

Lastly, there was no three-way interaction (b = 0.002, p = 0.086), meaning that the interaction effect of concreteness and arousal on shape does not change between human vs. Qwen3.

### Implications
Arousal and concreteness independently predict spikiness because of main effects of both, and the null interaction between arousal and concreteness. The relationship between how arousing a word is and how spiky its shape is perceived to be, do not differ for abstract vs. concrete words. 

Interestingly, the effect of concreteness is stronger in Qwen3, whereas the effect of arousal is stronger in human. This suggests that when making word associations with meanings, human and model may rely on different properties within a word. Specifically, human may rely more on arousal and model may rely more on concreteness. This is interesting as it reflects the fundamental difference between human and model. That is, people are emotional but models seem to lack this affective property.

Combined effects of arousal and concreteness do no differ between human vs. Qwen3, as shown in the null three-way interaction. This may be becasue there was no interaction between arousal and concreteness to begin with. However, note that the three-way interaction is marginally significant (p = .086). This suggests that the interplay of these factors could vary between human cognitive process and the embedding model's internal structure.

So, high arousal words and concrete words are perceived spikier, and thses effects operate independently. Moreover, this pattern stays the same across human and embedding model, suggesting that meaning representations within human may be observable in embedding models.

*Note: ShapeScore is calculated using embedding model for both human and Qwen3. The analyses would be more complete with human-rated shape scores. However, there is no existing database that include shape ratings.*
