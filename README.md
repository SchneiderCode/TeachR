
<!-- README.md is generated from README.Rmd. Please edit that file -->

# teachr

<!-- badges: start -->
<!-- badges: end -->

The teachr package extends learnr’s base [RMarkdown
template](https://pkgs.rstudio.com/learnr/articles/formats.html?q=template#custom-formats)
to provide instructors more fine-grained controls for observing and
managing tutorial progression. For clarity, tutorials created with the
teachr rmarkdown template will be referred to as *teachr* tutorials. But
please note that the bulk of the tutorial’s functionality is still
provided by learnr. Please refer to the learnr documentation for more
information on creating interactive R tutorials
<https://rstudio.github.io/learnr/>.

Currently Supported Features:  
- Enable/disable individual sections & topics in real-time  
- Record student submissions for quiz questions and coding exercises

## Installation

You can install the development version of teachr using the following
code snippet:

``` r
# install.packages("remotes")
remotes::install_github("schneidercode/teachr")
```

## Server Requirements

Tutorial controls are managed via HTTP requests. Therefore, in order for
a teachR tutorial to function, you must setup an endpoint that can
respond to both HTTP GET and HTTP POST requests. The format of the
requests are discussed in more detail below for those who want to
implement a custom server. For those less interested in managing a
personal server or database, you can manage teachR tutorials through a
provided Google Sheet (powered with Google App Scripts) or use a Google
Colab (**Still Under Development**).

### Get Requests - Controlling Tutorial Access

teachr enables an instructor to explicitly set which topics and sections
can be accessed through the use of HTTP GET requests, which are included
in the template’s javascript code
([tutorial-format.js](https://github.com/SchneiderCode/TeachR/blob/main/inst/rmarkdown/templates/tutorial/resources/tutorial-format.js)).

#### Tutorial Topics and Accessible Sections

    https://Your.Server.com?type=2

**Description**  
This GET request retrieves a list of the topics in a given tutorial and
the accessible sections within each topic.

**Trigger**  
When a tutorial first loads, it will request a list of the topics and
accessible sections. This information is used for populating the Table
of Contents, if included.

**Parameters**  
- **type=2**: This is a temporary variable that identifies this GET
request’s purpose. A future update will replace the variable with an
endpoint.

**Return**  
- **access**: JSON formatted data containing all of the topic names and
a list of their accessible sections.

``` json
{
    "access": {
        "section-topic-1": [
            "section-name",
            "section-other-name",
        ],
        "section-topic-2": [],
        "section-topic-3": [
            ""
        ],
    }
}
```

**Notes**  
- The `access` object MUST contain a list of all the topics within a
tutorial.  
- If a topic’s initial section is accessible, list it as an empty string
““.  
- If a topic has no accessible sections, set its value to an empty array
\[\].

#### Check Access on an Individual Section or Topic

    https://Your.Server.com?type=1&topic=${topic.id}&section=${section.id}

**Description**  
This GET request checks if the user can access the given topic and/or
section.

**Trigger**  
A student clicks either the “Next Topic” or “Continue” buttons.

**Parameters**  
- **topic**: The topic id, which is of the format
`section-header-2-name`. For example, (## Topic 1) becomes
(section-topic-1).  
- **section**: The section id, which is of the format
`section-header-3-name`. For example, (### Exercise 1) becomes
(section-exercise-1).  
- **type=1**: This is a temporary variable that identifies this GET
request’s purpose. A future update will replace the variable with an
actual endpoint.

**Return**  
- **can_proceed:**

    -   TRUE - The topic/section CAN be accessed.
    -   FALSE - The topic/section CANNOT be accessed.

**Notes**  
- The “Continue” button will only be shown if [progressive
reveal](https://pkgs.rstudio.com/learnr/articles/exercises.html?q=progress#progressive-reveal)
has been set.  
- GET requests are only sent after checking if the exercise can be
[skipped](https://pkgs.rstudio.com/learnr/articles/exercises.html?q=progress#exercise-skipping).

### Post Requests - Recording Student Submissions

**Description**  
Student submissions can be recorded through the use of post requests.
This feature is built with learnr’s [event
handlers](https://pkgs.rstudio.com/learnr/articles/publishing.html#event-handlers),
which are used here to capture and then transmit student submissions.

**Trigger**  
When a student submits an exercise or quiz question for grading.

**Parameters**:  
- **student_id**: Initially set as the student’s username (if run from
RStudio), but this can be manually set within the tutorial.  
- **exercise_id**: The name of the exercise/quiz’s code chunk
(`{r, exercise_name}`).  
- **is_correct**: How the submission was marked - TRUE (correct), FALSE
(incorrect), NA (not graded).  
- **submission**: Either the student’s code or the answer they chose for
the question.  
- **type**: This is a temporary variable which will be replaced with an
endpoint in a future update.

**Notes**  
- By default, only graded coding exercises are transmitted. To transmit
all coding exercises, *uncomment* the additional code provided in the
`else{}` statement.

### Google Sheet Web App

An example Google Sheet has been created which implements all of the
teachR features and can be deployed following these steps:

1.  Make a copy of the Starter [Google
    Sheet](https://docs.google.com/spreadsheets/d/1UJgTcXp5L3g75YGoTylM0GxFjdudELvIamwEuEDrMsU/copy)  
2.  Adding a tutorial’s information to the Google Sheet.  
3.  Deploy the embedded App Script - [Deploying Google App
    Scripts](https://developers.google.com/apps-script/concepts/deployments)
