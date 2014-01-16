---
title       : Shiny
subtitle    : Data Products
author      : Brian Caffo, Jeff Leek, Roger Peng
job         : Johns Hopkins Bloomberg School of Public Health
logo        : bloomberg_shield.png
framework   : io2012        # {io2012, html5slides, shower, dzslides, ...}
highlighter : highlight.js  # {highlight.js, prettify, highlight}
hitheme     : tomorrow      # 
url:
#    lib: ../../libraries
    assets: ../../assets
widgets     : [mathjax]            # {mathjax, quiz, bootstrap}
mode        : selfcontained # {standalone, draft}
---

## What is Shiny?

- Shiny is a platform for creating interactive R programs embeded into a web page.
- Suppose that you create a prediction algorith, with shiny you can *very easily* create web input form that calls R and thus your prediction algorithm and displays the results.
- Using Shiny, the time to create simple, yet powerful, web-based interactive data products in R is minimized.
  - However, it lacks the flexibility of full featured (and more complex) solutions.
- Shiny is made by the fine folks at R Studio.

---
## Some mild prerequisites 
- Shiny doesn't really require it, but as with all web programming, a little knowledge of html, css and js is very helpful
  - html gives a web page structure and sectioning as well as markup instructions
  - css gives the style
  - js for interactivity
- There are too many tutorials online to count for getting basic proficiency in these topics to count. 
- Shiny uses [bootstrap](http://getbootstrap.com/) (no relation to the statistics bootstrap) style, which (to me) seems to look nice and renders well on mobile platforms

---
## What else is out there?
- Full solutions requiring real knowledge of web client/server programming
- [OpenCPU](https://public.opencpu.org/) by Jerome Ooms, a really neat project providing an API to call 

---
## Context
- You created a novel prediction algorithm to predict risk for developing
  diabetes.
    - You're hoping patients and caregivers will be able to enter their data and, if needed, take preventative measures.
- You want to create a web site so that users can input the relevant predictors and obtain their prediction.
- Your prediction algorithm (ok, so you're not going to be saving the world with this one)
  - [link for a real prediction score](http://www.ncbi.nlm.nih.gov/pubmed/12610029)

```r
diabetesRisk <- function(glucose) glucose/200
```



---
## Getting started
- Make sure you have the latest release of R installed
- If on windows, make sure that you have Rtools installed
- `install.packages("shiny")`
- `libray(shiny)`
- Great tutorial at 
[http://rstudio.github.io/shiny/tutorial/](http://rstudio.github.io/shiny/tutorial/)
- Basically, this lecture is walking through that tutorial offering some of our insights
- Note, some of the proposed interactive plotting uses of Shiny could be handled by the very simple `manipulate` function [rstudio manipulate](http://www.rstudio.com/ide/docs/advanced/manipulate)
- Also, `rCharts` is will be covered in a different lecture.

---
## A Shiny project
- A shiny project is a directory containing at least two parts
  - One named ui.R (for user interface) controls how it looks.
  - One named server.R that controls what it does.

---
## ui.R
```
library(shiny)
shinyUI(pageWithSidebar(
  headerPanel("Data science FTW!"),
  sidebarPanel(
    h3('Sidebar text')
  ),
  mainPanel(
      h3('Main Panel text')
  )
))
```

---
## server.r
```
library(shiny)
shinyServer(
  function(input, output) {
  }
)
```

---
## To run it
- In R, change to the directories with these files and type `runApp()`
- or put the path to the directory as an argument
- It should open an browser window with the app running

---
![simplestApp](fig/simplestApp.png "First Shiny app")

---
## R functions for HTML markup
`ui.R`
```
shinyUI(pageWithSidebar(
  headerPanel("Illustrating markup"),
  sidebarPanel(
      h1('Sidebar panel'),
      h1('H1 text'),
      h2('H2 Text'),
      h3('H3 Text'),
      h4('H4 Text')
      
  ),
  mainPanel(
      h3('Main Panel text'),
      code('some code'),
      p('some ordinary text')
  )
))

```

---
![markup](fig/markup.png 'Marup in ui.R')


---
## Illustrating inputs ui.R
```
shinyUI(pageWithSidebar(
  headerPanel("Illustrating inputs"),
  sidebarPanel(
    numericInput('id1', 'Numeric input, labeled id1', 0, min = 0, max = 10, step = 1),
    checkboxGroupInput("id2", "Checkbox",
                   c("Value 1" = "1",
                     "Value 2" = "2",
                     "Value 3" = "3")),
    dateInput("date", "Date:")  
  ),
  mainPanel(
      
  )
))
```

---
![inputs](fig/inputs.png "Showing inputs")

---
## Part of ui.R
```
  mainPanel(
        h3('Illustrating outputs'),
        h4('You entered'),
        verbatimTextOutput("oid1"),
        h4('You entered'),
        verbatimTextOutput("oid2"),
        h4('You entered'),
        verbatimTextOutput("odate")
  )
```

---
## server.R
```
shinyServer(
  function(input, output) {
    output$oid1 <- renderPrint({input$id1})
    output$oid2 <- renderPrint({input$id2})
    output$odate <- renderPrint({input$date})
  }
)
```

---
![outputs](fig/inputsOutputs.png "Showing outputs")

---
## Let's build our prediction function

---
## 
```
shinyUI(
  pageWithSidebar(
    # Application title
    headerPanel("Diabetes prediction"),
  
    sidebarPanel(
      numericInput('glucose', 'Glucose mg/dl', 90, min = 50, max = 200, step = 5),
      submitButton('Submit')
    ),
    mainPanel(
        h3('Results of prediction'),
        h4('You entered'),
        verbatimTextOutput("inputValue"),
        h4('Which resulted in a prediction of '),
        verbatimTextOutput("prediction")
    )
  )
)
```

---
## server.R
```
diabetesRisk <- function(glucose) glucose / 200

shinyServer(
  function(input, output) {
    output$inputValue <- renderPrint({input$glucose})
    output$prediction <- renderPrint({diabetesRisk(input$glucose)})
  }
)
```

---
## The result
![prediction model](fig/predictionApp.png "prediction app")