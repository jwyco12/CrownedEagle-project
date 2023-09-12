---
title: "Crowned Eagles Nesting in the Urban Setting"
author: "Jacob Wyco"
date: "`r Sys.Date()`"
output:
  html_document:
    toc: TRUE
    self_contained: TRUE
    df_print: paged
editor_options: 
  markdown: 
    wrap: sentence
---

[Data analysis of Crowned Eagles breeding in Urban Environments.]{style="color:purple"}

Crowned Eagles are apex avian predators that are able to breed in an urban environment.
This dataset was collected from South Africa and investigates how much urbanisation affects Crowned Eagle breeding.

![Figure 1.Crowned Eagle (*Stephanoaetus coronatus*)](Images/Crowned_Eagle.jpg)

## Libraries and Data Set Up

```{r setup, include=FALSE, echo=FALSE}
library(leaflet)
library(hexbin)
library(tidyverse)  
library(knitr)
library(dplyr)
```

[N/A values were removed from dataset.]{style="color:purple"}

```{r Eagle cleanup, echo=FALSE}
chick <- read.csv("Eagle.csv")
chick <- filter(chick, t.1.attempt. + t.1.success. != "NA")
View(chick)
chick$Year <- as.factor(chick$Year)
```

*Basic plot*

Plot depicting relationship between mass and urbanization.
This is with the basic dataset.

```{r Plot, echo=FALSE}
plot(Mass ~ Urbanisation, data = chick)
model_1 <- lm(Mass ~ Urbanisation, data = chick)
abline(model_1)
```

## Modeling and Plotting

*Linear Modeling*

ANOVA analyses of Mass \~ Urbanisation and Mass \~ P8tot, or the length of primary feather 8 in mm.
This is investigating whether there is any significant differences between mass and urbanisation/P8tot.


```{r Model Analysis}
model_2 <- lm(Mass ~ Urbanisation, data = chick)
anova(model_2)
```

```{r Model Analysis 2}
model_3 <- lm(Mass ~ P8tot, data = chick)
anova(model_3)
```

[T-test of eagle chick mass and sex.]

```{r T-test analysis}
t.test(Mass ~ Sex, data = chick)
```
*Kables*

These are tables depicting both the regular dataset and the means and standard deviation of chick mass.

```{r Eagle Site Kable, echo =FALSE}
kable(chick[1:20, ], caption = 'Table 1. A knitr kable displaying the first 15 sites in the data set.')
```

```{r Eagle Summary, echo = FALSE}
summary1 <- summarise(group_by(chick, Year),
          n=n(), 
          mean=mean(Mass),
          sd=sd(Mass))  
kable(summary1, caption = 'Table 2. A summary kable displaying the mean, and standard deviation Crowned Eagle nestling mass by year.', digits = c(0, 0, 1, 1))
```

*Figures*

Bar plot of eagle chick mean mass over time.

```{r ggplot 1, echo=FALSE}
ggplot(summary1, aes(x = Year, y = mean, 
                     color = Year, fill = Year)) +
  geom_bar(stat = "identity", position = "dodge") +
  geom_errorbar(aes(ymin = mean - sd,
                    ymax = mean + sd), width =0.4, position = position_dodge(width = 0.9)) +
  theme_bw() 
ggsave("Figure_output/Year_Mass.png")
```

[Boxplot of chick mean mass over time.]

```{r gg plot 2, echo=FALSE}
ggplot(chick, aes(x = Year, y = Mass,
                                color = Year)) +
  geom_boxplot() +
  scale_color_manual(values = c("purple", "red",
                                "blue", "green", "brown",
                                "black")) +
  theme_bw()
ggsave("Figure_output/Mass_box.png")
```

Lineplot showing relationship between mass and urbanisation over time.

```{r ggplot 3, echo=FALSE}

ggplot(chick, aes(x = Urbanisation, y = Mass,
                                  color = Year)) +
  geom_point() +
  geom_smooth(method = 'lm') +
  theme_bw()
ggsave("Figure_output/Mass_Urban.png")
```

[Static image from the web.]{style="color:red"}

![Figure 2. Nest of the Crowned Eagle (*Stephanoaetus coronatus*)](Images/Eagle_Nest.jpg)

## Mapping and Video

Map of KwaZulu-Natal, location of the study.
This is a region in South Africa were Crowned Eagles are found in urban settings.


```{r Map}
leaflet() %>%
  setView(lat = -28.652399, lng = 30.524356, zoom = 8) %>% #lat-long of the place of interest
  addTiles() %>%
  addMarkers(lat = -28.652399, lng = 30.524356, popup = "KwaZulu-Natal") 
```

[Youtube video on urban Crowned Eagles.]{style="color:red"}

<iframe width="610" height="348" src="https://www.youtube.com/embed/RWpwiwzMm2E" data-external="1">

</iframe>

[I'm a link to the data source](https://datadryad.org/stash/dataset/doi:10.5061/dryad.kd51c5b30)
