# Tabbed R-Markdown Reports with jQuery UI

Matt Parker

2015-12-22


Quick Links to Source Files and Rendered Outputs:

 - Simple Report: [source](https://raw.githubusercontent.com/mmparker/tabbed_reporting/master/report.rmd), 
[rendered](http://blog.revolutionanalytics.com/downloads/report.html)    

 - Tabbed Report: [source](https://raw.githubusercontent.com/mmparker/tabbed_reporting/master/report_with_jquery.rmd), 
[rendered](http://blog.revolutionanalytics.com/downloads/report_with_jquery.html)    


One of the great advantages of R's openness is its extensibility. R's abundant
packages are the most conspicuous example of that extensibility, and 
Revolution R Enterprise is a powerful example of how far it can stretch. 

But R is also part of an entire ecosystem of open tools that can be linked 
together. For example, 
[Markdown](https://daringfireball.net/projects/markdown/), 
[Pandoc](http://pandoc.org/), and 
[knitr](http://yihui.name/knitr/) 
combine to make R an incredible tool for dynamic reporting and reproducible 
research. If your chosen output format is HTML, you've linked into yet another 
open ecosystem with countless further extensions.

One of those extensions - and the focus of this post - is 
[jQuery UI](http://jqueryui.com/).
jQuery UI makes a set of JavaScript's most useful moves available to developers
as a robust, easy-to-implement toolkit - ideal for adding a bit of interactivity
to your knitr reports.

## Tabs

For example: it's easy to use 
[jQuery UI's Tabs widget](http://jqueryui.com/tabs/) 
to split a long report across several tabs of a webpage. Tabs are great for 
splitting complex reports up by topic, or for providing different types of 
users with customized views of the results.

To get a sense of what this conversion might look like, here's 
[a simple R-Markdown report without tabs](http://blog.revolutionanalytics.com/downloads/report.html) 
([Rmd source](https://github.com/mmparker/tabbed_reporting/blob/master/report.rmd)):

![report_screenshot.png]

... and the [same report with tabs](http://blog.revolutionanalytics.com/downloads/report_with_jquery.html) 
([source](https://github.com/mmparker/tabbed_reporting/blob/master/report_with_jquery.rmd)):

![tabbed_report_screenshot.png]

Here's how I added tabs to the report.

1) First, I downloaded jQuery UI. Picking the right place to store the library
can be tricky, but as long as the jQuery UI files are accessible to knitr when 
it's building the report, you'll be okay. For this demo report, I just unzipped
the files right next to the .rmd source.

2) Next, I added a few lines to the `<head>` element of the report. Every 
webpage has a `<head>` element. knitr would typically build this for you, but
in this case we need to write it manually to be sure that the jQuery UI scripts
and CSS are linked in the HTML output.

    <head>
      <meta charset="utf-8">
      <title>Reported Active Tuberculosis Cases in the United States, 1993-2013</title>
      <link rel="stylesheet" href="jquery-ui/jquery-ui.min.css">
      <script src="jquery-ui/external/jquery/jquery.js"></script>
      <script src="jquery-ui/jquery-ui.js"></script>
      <script>
      $(function() {
        $( "#tabs" ).tabs();
      });
      </script>
    </head>


3) Next, I created the navigation bar by creating an HTML chunk (`div`) with a
list inside of it (`ul`). Each item in that list (`li`) represents one tab
that I'd like the page to have. Finally, I make each of those list items a link
with a unique tag (`<a href="#nation">`), 
and give the link a title (`Nationally`, `By State`, `Treatment Completion`).

    <div id="tabs">
    <ul>
    <li><a href="#nation">Nationally</a></li>
    <li><a href="#states">By State</a></li>
    <li><a href="#treatment">Treatment Completion</a></li>
    </ul>

Don't worry if you don't understand the HTML syntax here - you can just copy
and edit the code above.

4) Finally, I marked out which sections of R-Markdown I wanted to put on each
tab by surrounding that section with a `div`:

    <div id="nation">
    
    ## Reported Active TB Cases in the United States, 1993-2013
    
    ```{r nation}
    
    tbstats %>% 
        group_by(Year) %>% 
        summarise(n_cases = sum(Count)) %>% 
        ggplot(aes(x = Year, y = n_cases)) +
            geom_line(size = 2) +
            labs(x = "Year Reported",
                 y = "Number of Cases",
                 title = "Reported Active TB Cases in the United States") +
            expand_limits(y = 0)
    
    
    ```
    
    </div>

There are two crucial details here: 
 - the `div` has an `id` that corresponds to one of the tabs I've created
   (`href=#nation` corresponds to `<div id="nation">`)
 - the `div` is closed with a `</div>` tag. Without this, the entire report
   would be included on the first tab.


5) Click the "Knit HTML" button! knitr will convert your R-Markdown into plain
Markdown, and then call Pandoc to complete the conversion into gloriously-tabbed
HTML.

Tabs are very handy for reporting - but the whole HTML/CSS/JavaScript ecosystem
is at your disposal. If you've seen other good reporting tricks in HTML, let us
know in the comments below.
