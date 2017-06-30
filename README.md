# Academy Awards Database Scraping


All data via: http://awardsdatabase.oscars.org/search/




## Inventory

### Search result pages

These files consist of *nearly-raw HTML* from the search results at [http://awardsdatabase.oscars.org/search/](http://awardsdatabase.oscars.org/search/). I've removed references to JavaScript files and code, and kept only a subset of the original CSS (including a minimized version of Bootstrap) so that the pages have functionally the same style as the original pages. For web scraping, the actual HTML is all that matters, but it's nice to have HTML files that render legibly in the browser:

- [data/raw/awardsdatabase.oscars.org/categories.html](data/raw/awardsdatabase.oscars.org/categories.html)
- [data/raw/awardsdatabase.oscars.org/films.html](data/raw/awardsdatabase.oscars.org/films.html)
- [data/raw/awardsdatabase.oscars.org/nominees.html](data/raw/awardsdatabase.oscars.org/nominees.html)
