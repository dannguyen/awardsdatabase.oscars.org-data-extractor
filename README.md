# Academy Awards Database Scraping


All data via: http://awardsdatabase.oscars.org/search/




## Inventory

### Search result pages

These files consist of *nearly-raw HTML* from the search results at [http://awardsdatabase.oscars.org/search/](http://awardsdatabase.oscars.org/search/). I've removed references to JavaScript files and code, and kept only a subset of the original CSS (including a minimized version of Bootstrap) so that the pages have functionally the same style as the original pages. For web scraping, the actual HTML is all that matters, but it's nice to have HTML files that render legibly in the browser:

- Categories - Results are grouped by category, sorted in chronological result.
  - [Sample webpage](//dannguyen.github.io/awardsdatabase.oscars.org-data-extractor/data/raw/awardsdatabase.oscars.org/sample-categories.html)
  - Raw sample HTML: [data/raw/awardsdatabase.oscars.org/sample-categories.html](data/raw/awardsdatabase.oscars.org/sample-categories.html)
  - Raw full HTML: [data/raw/awardsdatabase.oscars.org/categories.html](data/raw/awardsdatabase.oscars.org/categories.html)
- Films - Results are grouped by film, sorted in alphabetical order.
  - [Sample webpage](//dannguyen.github.io/awardsdatabase.oscars.org-data-extractor/data/raw/awardsdatabase.oscars.org/sample-films.html)
  - Raw sample HTML: [data/raw/awardsdatabase.oscars.org/sample-films.html](data/raw/awardsdatabase.oscars.org/sample-films.html)
  - Raw full HTML: [data/raw/awardsdatabase.oscars.org/films.html](data/raw/awardsdatabase.oscars.org/films.html)

- Nominees - Results are grouped by nominated person, sorted in alphabetical order.
  - [Sample webpage](//dannguyen.github.io/awardsdatabase.oscars.org-data-extractor/data/raw/awardsdatabase.oscars.org/sample-nominees.html)
  - Raw sample HTML: [data/raw/awardsdatabase.oscars.org/sample-nominees.html](data/raw/awardsdatabase.oscars.org/sample-nominees.html)
  - Raw full HTML: [data/raw/awardsdatabase.oscars.org/nominees.html](data/raw/awardsdatabase.oscars.org/nominees.html)

