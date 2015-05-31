## Website Performance Optimization portfolio project

Your challenge, if you wish to accept it (and we sure hope you will), is to 
optimize this online portfolio for speed! In particular, optimize the critical 
rendering path and make this page render as quickly as possible by applying the 
techniques you've picked up in the 
[Critical Rendering Path course](https://www.udacity.com/course/ud884).

#### CONTENTS ####
-A- Project Solutions (Part 1 & Part 2)
-B- Project Files
-C- Installation/Use

#### -A- Project Solutions ####
Part 1: Optimize PageSpeed Insights score for index.html

My first step in optimizing this site was to run it through Google PageSpeed
Insights and evaluate the output from that system. To accomplish this, I took
the following steps:

  1.  I first forked and then cloned the original repo to my Mac.
  2.  Once cloned, I created a new directory within the project directory
      called "original" and then copied all of the original files into
      that directory to make before & after comparisons easier.
  3.  Next, I made the necessary updates to git to add the copied files and
      folders to the repo and pushed the updated repo up as an initial commit.
  4.  Once in sync, I then created a new branch in git called gh-pages. This
      will make it easy to access the pages online by using the GH Pages
      capability (codingvirtual.github.io/Udacity-FE-P4-Web-Optimization).
      Then I pushed this branch up to Github and was able to access it
      via the URL above.
  5.  From here, I put that URL into PageSpeed Insights and was able to get
      the results and evaluation from PSI.
      
Once the evaluation completed, the results indicated that there were 
optimizations available to CSS and a couple of image files, and that there
were render-blocking CSS and JavaScript elements to deal with. To resolve
these issues, I took the following steps:

  1.  Conveniently, there was a link towards the bottom of the results
      to a ZIP file that contained optimized versions of those files, so I
      did not have to resolve those issues (resizing images, scaling, etc)
      manually. I copied the modified files into the appropriate folders
      in the project directory (leaving all the files in the "original"
      subdirectory untouched). I would note that resizing the images had
      a significant impact on the score, moving it from 28 into the 70's.
  2.  Next, I moved the analytics-related JS to the bottom of the body
      tag so that they would not have to load prior to rendering. Related to
      this step, I also extracted the JS code out of the "perfmatters.js" file
      and inlined it directly into the head of the index.html file since 
      this code was both critical (had to load before the rest to accurately
      measure times) and since it was very small code. This eliminated a 
      separate retrieve.
  3.  To optimize CSS, I decided to inline the relatively small amount of
      CSS out of the style.css file and directly into the head of the index
      file. There was also one rule that was unused on the page, so I deleted
      it (related to the <pre> and <code> tags). Finally, I added a media
      attribute to the print.css <link> tag so the browser would skip the load
      of that file.
  4.  To optimize the layout step of the pipeline, I added width and height 
      attributes to every <img> tag.
  5.  Finally, to optimize font loading, I went back to Google Fonts and
      selected only the weights and styles of the Open Sans font that was
      being used, and then also only the Latin charset. There were suggestions
      on that page that provided JS code to use in the document body to
      help control load order. I removed the <link> tag from the head and
      instead have the fonts loading via JS from within a script block located
      at the bottom of the body tag.
      
With all of these optimizations, I achieved a score of 95/100 on Mobile and
97/100 on Desktop.

There are embedded comments within the index.html file that indicate the
changes I made. You can compare this file to the index.html file that is
within the 'original' subdirectory in the project for a line-by-line
comparison.

Part 2: Optimize Frames per Second in pizza.html

Optimizing the frame rate involved reducing the number of pizzas involved
(to only 40, spaced out by 318 pixels intead of 256) and several modifications 
to the JS contained within views/js/main.js. The following alterations were 
made:

  1.  Create an array called 'pizzas' to contain the created pizza elements.
      In the interest of "relevance," I located the declaration just above
      the updatePositions() function block.
  2.  Within the function at the very end that attaches an eventListener for
      the DOMContentLoaded event, I added a statement to push the newly
      created element onto the Pizzas array. This completely eliminated the
      need to do a DOM search using document.querySelector.
  3.  Within updatePositions(), several optimizations were made:
      a.  Move the computation of the scroll ratio (document.body.scrollTop /
          1250) outside the loop body. This calculation won't change, so it
          was a waste of CPU to recalculate that value n times (where n =
          number of pizzas). Within the loop body, I maintained the call to
          Math.sin() using the phases. The optimizations from this step were
          significant enough to cause me to not bother with further steps
          around eliminating the calculation of which of the 5 phases the
          pizza was in.
      b.  Iterate over the previously created pizza array to calculate the
          new position for each element. As mentioned earlier, creating
          the array eliminated the DOM query entirely.
          
These optimizations dropped the 10-frame render time to tenths of milliseconds,
easily blowing away the 60fps target rate. Further optimization could be made
to reduce the time to paint all of the pizzas, which turns out to be the next
longest operation according to the timeline. I suspect that working on moving
the pizza animations over to the HTML5 Canvas would yield the greatest gains
in performance, but that's only speculation.


Part 2: Optimize pizza slider size redraw functionality

For the 2nd part of the Part 2 optimization, the goal was to reduce the
time to redraw the menu pizzas (which are different than the background
"scrolling" pizzas" when the user adjusts the size slider near the top of
the page. Target is <5ms to redraw as reported in the console.

Multiple optimizations were made to achieve this target:
    a.  As before, an array was created to hold the pizza elements to allow
        for faster access. This eliminated the need to query the DOM to do
        a resize since all elements would be neatly contained in the array.
        
        To build the array, I had to prepopulate it with the two hard-coded
        pizzas that were in the index.html file and had id's of pizza0 and
        pizza1. Rather than further hard-code this in the JS, I elected
        to use getElementsByClassName('randomPizzaContainer') to find any
        and all hard-coded pizzas and add them to the menuPizzas array.
        
        Further down in the code, there is a function call that randomnly
        generates each pizza on the menu. I added a single line to the
        end of that function that pushes the newly created menu item
        to the menuPizzas array. This also contributed to eliminating the DOM
        query.
    b.  With this array in place, resizing the pizzas became a matter of simply
        iterating over the array and changing the width attribute to the new
        size. 
    c.  To achieve some slight additional optimization, there were serveral
        variable declarations contained inside for loop bodies that were
        really static (meaning that the value of those variables was constant
        during each iteration of the loop, so there was no reason to calculate
        them inside the loop). Rather, I pulled those initializations out of
        the for loops and tried to reduce the body of the loop to code that
        actually needed to run for each pizza.
    d.  Finally, there were a couple of places that I replaced querySelector
        calls with getElementBy... calls to improve performance.


#### -B- Project Files ####
The repo contains two sets of all of the files. At the top level of the repo
you will find the "final" version of the project. Pointing your browser at
codingvirtual.github.io/Udacity-FE-P4-Web-Optimization will render the
index.html file at the top level of the project (this is the optimized version).

The original initial set of files provided are contained in a subdirectory
at the top level named "original." To reach the original index.html file,
point your browser at:
codingvirtual.github.io/Udacity-FE-P4-Web-Optimization/original/index.html

While you will still find style.css and perfmatters.js in the project "final"
files, they are not actually used by index.html as their contents were inlined
into the index.html file itself.

The Timeline-Data directory contains png images of the Dev Tools Timeline
screen shots. Stage1.5 represents the final project. You'll note that the
optimizations from Stage1 to Stage1.5 are minor and that is because I had
made many of the "Stage 2" alterations in Stage 1 intuitively.


#### -C- Installation/Use ####
The easiest way to use this repo is to fork it. This process will give you
both the original files (contained in the 'original' subdirectory) and my
final, optimized files at the top level.

Part 2, FPS Rendering Optimization, can be done on your local machine using
Chrome Dev Tools. However, to get a Pagespeed Score, the files have to be
loaded to a publicly-accessible web server and the URL to that server then
fed into PageSpeed Insights. The original repo can be found here:

https://github.com/udacity/frontend-nanodegree-mobile-portfolio

along with an excellent README file that describes in detail several alternate
ways to make a URL reachable to PageSpeed Insights. I chose to use the 
Github Pages capability described here:

https://pages.github.com/

This was the process I followed for my project and is loosely described
above in Section -A-.
