---
title       : Course Project Shiny Application and Reproducible Pitch
subtitle    : 
author      : Ceci practice
job         : 
framework   : io2012        # {io2012, html5slides, shower, dzslides, ...}
highlighter : highlight.js  # {highlight.js, prettify, highlight}
hitheme     : tomorrow      # 
widgets     : []            # {mathjax, quiz, bootstrap}
mode        : selfcontained # {standalone, draft}
knit        : slidify::knit2slides
---
---

## Slide 1: Introduction

Welcome to the presentation of our Shiny application.

This app is built using **Shiny**, **tidyverse**, **plotly**, and **DT** packages.

---

## Slide 2: Import Data


```r
library(tidyverse)
```

```
## ── Attaching packages ─────────────────────────────────────── tidyverse 1.3.0 ──
```

```
## ✓ ggplot2 3.3.5     ✓ purrr   0.3.4
## ✓ tibble  3.0.1     ✓ dplyr   1.0.0
## ✓ tidyr   1.1.0     ✓ stringr 1.4.0
## ✓ readr   1.3.1     ✓ forcats 0.5.0
```

```
## ── Conflicts ────────────────────────────────────────── tidyverse_conflicts() ──
## x dplyr::filter() masks stats::filter()
## x dplyr::lag()    masks stats::lag()
```

```r
dat <- read_csv(url("https://www.dropbox.com/s/uhfstf6g36ghxwp/cces_sample_coursera.csv?raw=1"))
```

```
## Warning in open.connection(con, "rb"): URL 'https://www.dropbox.com/s/
## uhfstf6g36ghxwp/cces_sample_coursera.csv?raw=1': status was 'SSL connect error'
```

```
## Error in open.connection(con, "rb"): cannot open the connection to 'https://www.dropbox.com/s/uhfstf6g36ghxwp/cces_sample_coursera.csv?raw=1'
```

```r
dat <- dat %>% select(c("pid7", "ideo5", "newsint", "gender", "educ", "CC18_308a", "region"))
```

```
## Error in select(., c("pid7", "ideo5", "newsint", "gender", "educ", "CC18_308a", : object 'dat' not found
```

```r
dat <- drop_na(dat)
```

```
## Error in drop_na(dat): object 'dat' not found
```
---

## Slide 3: UI Overview
The UI is built using navbarPage with multiple tabPanel components:

Page 1: Slider input to filter data based on ideology.
Page 2: Checkbox input to filter data based on gender.
Page 3: Select input to filter data based on region.

---

## Slide 4: Server Overview 

The server logic includes multiple render functions:

renderPlot for ggplot2 plots.
renderPlotly for interactive Plotly plots.
renderDataTable for displaying data tables.

```r
library(shiny)
library(tidyverse)
library(plotly)
```

```
## 
## Attaching package: 'plotly'
```

```
## The following object is masked from 'package:ggplot2':
## 
##     last_plot
```

```
## The following object is masked from 'package:stats':
## 
##     filter
```

```
## The following object is masked from 'package:graphics':
## 
##     layout
```

```r
library(DT)
```

```
## 
## Attaching package: 'DT'
```

```
## The following objects are masked from 'package:shiny':
## 
##     dataTableOutput, renderDataTable
```

```r
ui <- navbarPage(
  title = "My Application",
  tabPanel("Page 1",
           sidebarLayout(
             sidebarPanel(
               sliderInput("my_ideo5",
                           "Select Five Point Ideology (1=Very liberal, 5=Very conservative)",
                           min = 1,
                           max = 5,
                           value = 3)
             ),
             mainPanel(
               tabsetPanel(
                 tabPanel("Tab1", plotOutput("plot1")),
                 tabPanel("Tab2", plotOutput("plot2"))
               )
             )
           )
  ),
  tabPanel("Page 2",
           sidebarLayout(
             sidebarPanel(
               checkboxGroupInput(inputId = "my_gender",
                                  label = "Select Gender",
                                  choices = c("1", "2"),
                                  selected = "1")
             ),
             mainPanel(
               plotlyOutput("plot3")
             )
           )
  ),
  tabPanel("Page 3",
           sidebarLayout(
             sidebarPanel(
               selectInput(inputId = "my_region",
                           label = "Select Region",
                           choices = c("1", "2", "3", "4"),
                           multiple = TRUE)
             ),
             mainPanel(
               dataTableOutput(outputId = "table1")
             )
           )
  )
)

server <- function(input, output) {
  output$plot1 <- renderPlot({
    ggplot(filter(dat, ideo5 == input$my_ideo5), aes(x = pid7)) +
      geom_bar() +
      xlab("7 Point Party ID, 1=Very D, 7=Very R") +
      ylab("Count") +
      scale_x_continuous(limits = c(0, 8)) +
      scale_y_continuous(limits = c(0, 100))
  })
  
  output$plot2 <- renderPlot({
    ggplot(filter(dat, ideo5 == input$my_ideo5), aes(x = CC18_308a)) +
      geom_bar() +
      xlab("Trump Support") +
      ylab("Count") +
      scale_x_continuous(limits = c(0, 5))
  })
  
  output$plot3 <- renderPlotly({
    print(ggplotly(
      ggplot(filter(dat, gender == input$my_gender), aes(x = educ, y = pid7)) +
        geom_jitter() +
        geom_smooth(method = lm)
    ))
  })
  
  output$table1 <- renderDataTable({
    filter(dat, region %in% input$my_region)
  })
}

shinyApp(ui, server)
```

```
## PhantomJS not found. You can install it with webshot::install_phantomjs(). If it is installed, please make sure the phantomjs executable can be found via the PATH variable.
```

```
## 
## Listening on http://127.0.0.1:7478
```

```
## Error in path.expand(path): invalid 'path' argument
```
---

## Slide 5: Shiny App Code
Here is the complete code for the Shiny app:


```r
library(shiny)
library(tidyverse)
library(plotly)
library(DT)

ui <- navbarPage(
  title = "My Application",
  tabPanel("Page 1",
           sidebarLayout(
             sidebarPanel(
               sliderInput("my_ideo5",
                           "Select Five Point Ideology (1=Very liberal, 5=Very conservative)",
                           min = 1,
                           max = 5,
                           value = 3)
             ),
             mainPanel(
               tabsetPanel(
                 tabPanel("Tab1", plotOutput("plot1")),
                 tabPanel("Tab2", plotOutput("plot2"))
               )
             )
           )
  ),
  tabPanel("Page 2",
           sidebarLayout(
             sidebarPanel(
               checkboxGroupInput(inputId = "my_gender",
                                  label = "Select Gender",
                                  choices = c("1", "2"),
                                  selected = "1")
             ),
             mainPanel(
               plotlyOutput("plot3")
             )
           )
  ),
  tabPanel("Page 3",
           sidebarLayout(
             sidebarPanel(
               selectInput(inputId = "my_region",
                           label = "Select Region",
                           choices = c("1", "2", "3", "4"),
                           multiple = TRUE)
             ),
             mainPanel(
               dataTableOutput(outputId = "table1")
             )
           )
  )
)

server <- function(input, output) {
  output$plot1 <- renderPlot({
    ggplot(filter(dat, ideo5 == input$my_ideo5), aes(x = pid7)) +
      geom_bar() +
      xlab("7 Point Party ID, 1=Very D, 7=Very R") +
      ylab("Count") +
      scale_x_continuous(limits = c(0, 8)) +
      scale_y_continuous(limits = c(0, 100))
  })
  
  output$plot2 <- renderPlot({
    ggplot(filter(dat, ideo5 == input$my_ideo5), aes(x = CC18_308a)) +
      geom_bar() +
      xlab("Trump Support") +
      ylab("Count") +
      scale_x_continuous(limits = c(0, 5))
  })
  
  output$plot3 <- renderPlotly({
    print(ggplotly(
      ggplot(filter(dat, gender == input$my_gender), aes(x = educ, y = pid7)) +
        geom_jitter() +
        geom_smooth(method = lm)
    ))
  })
  
  output$table1 <- renderDataTable({
    filter(dat, region %in% input$my_region)
  })
}

shinyApp(ui, server)
```

```
## 
## Listening on http://127.0.0.1:5492
```

```
## Error in path.expand(path): invalid 'path' argument
```
