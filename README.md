
<!-- README.md is generated from README.Rmd. Please edit that file -->

# teachr

<!-- badges: start -->
<!-- badges: end -->

The teachr package provides an rmarkdown template that extends learnr
tutorials. An interactive tutorial built with teachr provide instructors
more fine-grained controls for observing and managing student
progression. The features currently supported are as follows: - Control
student access to individual sections & topics - Record student
submissions for quiz questions and coding exercises

For clarity, tutorials created from the included rmarkdown template will
be referred to as *teachr* tutorials. But please note that the bulk of
the tutorial’s functionality is still provided by learnr. Please refer
to the learnr documentation for more information on creating interactive
R tutorials <https://rstudio.github.io/learnr/>. Todo: 1. Readme 2.
Explain Google Script & Create copy link 3. update name to use teachr
(lower case) 4. Create Github Repo 5. Share and review with colleagues

## Installation

You can install the development version of teachr like so:

``` r
# install.packages("remotes")
remotes::install_github("schneidercode/teachr")
```

## Enabling teachr

A teachr tutorial enables an instructor to monitor and control student
progress through the use of a secondary server. Just about any server
can be setup for teachr. The only requirement is in the formating of get
and post requests.

### Get Requests - Controlling Student Progression

teachr enables an instructor to explicitly set which topics and sections
can be accessed through the use of http get requests, which are included
in the template’s javascript
code(**teachr/inst/man/rmarkdown/templates/tutorial/resources/tutorial-format.js**).
An http get request will be triggered when a student clicks “Next Topic”
or “Continue”. The parameters of the get request are as follows: -
**topic**: The topic id, which is of the format section-header-2-name.
For example, (## Topic 1) becomes (section-topic-1). - **section**: The
section id, which is of the format section-header-3-name. For example,
(### Exercise 1) becomes (section-exercise-1).  
- **type=1**: This is a preset parameter (1) that is intended for future
extensions of teachr, where additional get requests may be supported.

**Please note**, the “Continue” button will only be shown if
[https://pkgs.rstudio.com/learnr/articles/exercises.html?q=progress#progressive-reveal](progressive%20reveal)
has been set. Also, get requests are sent after checking if the exercise
can be
[https://pkgs.rstudio.com/learnr/articles/exercises.html?q=progress#exercise-skipping](skipped).

This feature was designed primarily for active lectures and lab
activities, where an instructor may want to limit how far ahead students
can progress. Additionally, this feature could be helpful for hiding
tutorial sections, removing the need to manually edit & re-deploy a
tutorial.

### Post Requests - Recording Student Submissions

Student submissions can be recorded through the use of post requests.
These requests are triggered when a student submits an exercise or quiz
question for grading. This features is built off of learnr’s built in
[https://pkgs.rstudio.com/learnr/articles/publishing.html#event-handlers](event%20handlers),
which are used here to capture and then transmit student submissions. By
default, only graded coding exercises are transmitted. To transmit all
coding exercises, uncomment the additional code provided in the else{}
statement. The parameters provided by teachr are as follows: -
**student_id**: Initially set as the student’s login (if run from
RStudio), but this can be manually set within the tutorial. -
**exercise_id**: The name of the exercise/quiz’s code chunk (\`\`\`{r,
exercise_name}) - **is_correct**: How the submission was marked - TRUE
(correct), FALSE (incorrect), NA (not graded) - **submission**: Either
the student’s code or the answer they chose for the question

### Google Sheet Server

For convience, a Google Sheet has been created which includes Google
Scripts for handling a teachr’s get/post requests. Follow these steps
for deploying a Google Sheet Server: 1. Make a copy of the
[](Google%20Sheet) 2. Prepare the Google Sheet based on your finished
tutorial 3. Deploy the connected Google Script
