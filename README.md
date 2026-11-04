# 📈 Assessing Learning with Pre- and Posttests in R
> A guide to evaluating learning gains with a pretest-posttest design and paired-sample t-tests, using data from a youth mentoring program. 🎓
![R](https://img.shields.io/badge/language-R-276DC3?logo=r&logoColor=white) ![Analysis](https://img.shields.io/badge/analysis-paired%20t--test-blue) ![Sample](https://img.shields.io/badge/n-8-orange) ![Cost](https://img.shields.io/badge/cost-free-brightgreen)
# Introduction 
Educational research often uses pre- and posttest design to measure how well a new curriculum meets specific learning goals. By testing participants before and after instruction, researchers can determine how much knowledge was gained and evaluate both the efficacy of curriculum and instructional practices. This design is considered one of the best ways to assess the overall impact and quality of a curriculum.

This guide measures the impact of four digital citizenship lessons within Campus Connections, a 12-week mentoring program that paired youth (ages 12–14) with undergraduate student mentors. The digital citizenship lessons included: (1) digital wellness, (2) digital accessibility, (3) securing devices, and (4) digital footprint. In addition to knowledge-based surveys, students also completed the Developmental Assets Profile (DAP) regarding their academic, social, emotional, and psychological wellness. The DAP was completed before the start of programming and again at the end.  

This tutorial serves as an introduction to performing t-tests to compare two groups in R. You will need the `readr`, `psych`, and `ggplot2` packages.

# 1. Evaluating Learning Changes with Paired-sample t-tests 
In order to determine whether participants (n = 8) made significant learning gains, this study used a t-test to determine whether differences between the pre- and posttests were significant. Specifically, when using the paired-sample t-test the researcher calculates the mean percentages of responses on the pretest and posttest. A significant difference implies effective curriculum, while a less-than-significant difference indicates that the participants did not experience a significant change in learning, which may be attributed to a variety of factors. 

```
library(readr) # find, import, and read data
library(psych) # descriptive statistics
library(ggplot2) # plotting
library(ggrepel) # repel overlapping plot labels

DigitalCitizenship <- read_csv("DigitalCitizenship.csv")
View(DigitalCitizenship)

min(DigitalCitizenship$preDAP)
max(DigitalCitizenship$preDAP)
mean(DigitalCitizenship$preDAP)
describe(DigitalCitizenship$preDAP) 

min(DigitalCitizenship$postDAP)
max(DigitalCitizenship$postDAP)
mean(DigitalCitizenship$postDAP)
describe(DigitalCitizenship$postDAP)

df_long <- data.frame(
  ID = rep(DigitalCitizenship$ID, 2),
  value = c(DigitalCitizenship$preDAP, DigitalCitizenship$postDAP),
  time = rep(c("PreDAP", "PostDAP"), each = nrow(DigitalCitizenship))
)
df_long$time <- factor(df_long$time, levels = c("PreDAP", "PostDAP"))
ggplot(df_long, aes(x = time, y = value, fill = time)) +
  geom_dotplot(binaxis = 'y', stackdir = 'center', dotsize = 1.2) + 
  geom_text_repel(aes(label = ID), 
            max.overlaps = Inf, 
            box.padding = 0.5, 
            point.padding = 0.5, 
            size = 3 
            )+
  labs(
    title = "PreDAP vs PostDAP Scores",
    x = NULL,
    y = "DAP Score"
  ) +
  scale_fill_manual(values = c("#E60023", "#45A3FE")) +
  theme_minimal()  
```
In this dot plot, we can see that the postDAP scores show less variation than preDAP scores. While most participants improved, participants **Y000084** and **Y000043** saw decreases. Notably, a high-scoring outlier in the pre-DAP data (**Y000059**) disappeared in the post-test. ![A dot plot showing pre and postDAP scores](https://github.com/user-attachments/assets/75c63f27-3e0d-489a-b935-cea256356c23)


Why did participant Y000059 have such a high preDAP score that is not reflected in their postDAP score? Is there a problem with the scale or the survey procedure? Is there response bias? Were there specific circumstances that impacted these participants as they took the surveys? **With small datasets, outliers are more obvious and significant and make a greater difference in how we analyze and interpret the data.**


