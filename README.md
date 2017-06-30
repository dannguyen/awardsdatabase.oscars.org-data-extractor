# Academy Awards Database Scraping

There doesn't appear to be a reliable or comprehensive, normalized source for the Academy Awards. However, there the Academy does have a comprehensive searchable database at:

http://awardsdatabase.oscars.org/search/

Example results when searching for the 2016 Academy Award winners and nominees:

<a href="http://awardsdatabase.oscars.org/search/">
  <img src="assets/images/example-search-results-2016-awards.png" alt="example-search-results-2016-awards.png">
</a>



This repo contains raw HTML, code (soon), and resulting data (hopefully). It turns out winning Oscars -- or at least how and *to whom* awards are awarded -- is more complicated than anticipated.

Here's some wrong assumptions you/I have about Oscar data:

- The Academy keeps the same categories it had since the beginning (there's no longer awards for Assistant Director or Dance Direction)
- The Academy doesn't add categories (Animated Short Film has existed since the 1930s, but Animated Feature Film began in 2001)
- There are 5 categories for the "Big 5" Oscars (there are 6 -- there are 2 kinds of best screenplay) 
- There has always been a "Best Picture" Oscar (it has undergone at least 5 different name changes, including "Outstanding Picture" and "Outstanding Production")
- There's only one person per nomination (Best Picture, among many other awards, can include many people)
- There's only one person per category (back when there were fewer movies, such as 1928, actors could be nominated for several films)
- There's always been the same number of nominees (Best Picture has recently expanded to include 10 nominees instead of the usual 5)
- There's only one winner per category (nope, there can be multiple winners in the event of a tie, such as the 1932 Best Actor category)
- No two award-winners have ever had the same first and last name (there's Will Smith, the best actor nominee, and William Craig Smith, nominated in 1982 for Art Direction)



## Data notes

To get an idea of how the data is modeled and stored on the Academy's database, do a search at: http://awardsdatabase.oscars.org/search/


Unfortunately, the searches are executed via POST (i.e. state-dependent) requests, using ASP as the backend and some serious JavaScript-heavy front-end code, so direct-links are a little unwieldy. A search for Brad Pitt can be found at this URL:

[http://awardsdatabase.oscars.org/Search/GetResults?query=%7B%22NomineeId%22:6188,%22IsHyperlinkQuery%22:true,%22Sort%22:%221-Nominee-Alpha%22,%22Search%22:%22Basic%22%7D](http://awardsdatabase.oscars.org/Search/GetResults?query=%7B%22NomineeId%22:6188,%22IsHyperlinkQuery%22:true,%22Sort%22:%221-Nominee-Alpha%22,%22Search%22:%22Basic%22%7D)

Here's a screenshot of what that page looks like:

<img src="assets/images/bradpitt-search-results.png" alt="assets/images/bradpitt-search-results.png">

- There's **Brad Pitt**, the person who gets nominated, i.e. `nomineeId=6188`
- Pitt was nominated for the 1995 "ACTOR IN A SUPPORTING ROLE" (`categoryExactId=4` and `&awardShowFrom=68`), for the film, `12 Monkeys` (`filmId=3636`).
- Pitt was one of several producers listed as the 2015 Best Picture award-winning nominee for Best Picture (`nominationId=10453`)

So there's probably at least these tables to be made:

- award_shows
- categories 
  - many-to-many relationship with award shows
  - also, categories vary in name and type over the years)
- nominees 
  - many-to-one relationship with nominations
- films 
  - one-to-one relationship with nominations... I think
- nominations 
  - many-to-one relationship with categories+award_shows
  - many-to-many relationship with nominees
  - one-to-many relationship to films...I think


Other notes:

- The search page filters are quite flexible. You can basically do searches for all the data -- e.g. every nomineee for every category for every year -- and the site will deliver. I've included HTML dumps of these search results in this repo for scraping purposes.
- The search result pages are **massive**, number in the hundreds of thousands of raw HTML (this doesn't include CSS or JS).
- Plenty of typos in the data, including "All about eve" and "Good Fellas"
- The reason why this repo contains 3 separate result pages is because we need to capture the unique ids for the nominees, films, and awards, as well as the **nominations** (which can be a combination of the other 3 datatypes)





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



## Wikidata

This looks promising:

```sql
#added before 2016-10
SELECT ?human ?humanLabel ?awardEditionLabel ?awardLabel ?awardWork ?awardWorkLabel ?director ?directorLabel ?time 
WHERE 
{
  {
    SELECT (SAMPLE(?human) AS ?human) ?award ?awardWork (SAMPLE(?director) AS ?director) (SAMPLE(?awardEdition) AS ?awardEdition) (SAMPLE(?time) AS ?time) WHERE {
      ?award wdt:P31 wd:Q19020 .      # All items that are instance of(P31) of Academy awards (Q19020)
      {
        ?human p:P166 ?awardStat .              # Humans with an awarded(P166) statement
        ?awardStat ps:P166 ?award .        # ... that has any of the values of ?award
        ?awardStat pq:P805 ?awardEdition . # Get the award edition (which is "subject of" XXth Academy Awards)
        ?awardStat pq:P1686 ?awardWork . # The work they have been awarded for
        ?human wdt:P31 wd:Q5 .        # Humans
      } UNION {
        ?awardWork wdt:P31 wd:Q11424 . # Films
        ?awardWork p:P166 ?awardStat . # ... with an awarded(P166) statement
        ?awardStat ps:P166 ?award .        # ... that has any of the values of ?award
        ?awardStat pq:P805 ?awardEdition . # Get the award edition (which is "subject of" XXth Academy Awards)
      }
      OPTIONAL {
        ?awardEdition wdt:P585 ?time . # the "point of time" of the Academy Award
        ?awardWork wdt:P57 ?director .
      }
    }
    GROUP BY ?awardWork ?award # We only want every movie once for a category (a 'random' person is selected)
  }

  SERVICE wikibase:label {            # ... include the labels
    bd:serviceParam wikibase:language "en" .
  }
}
ORDER BY DESC(?time)
```


https://query.wikidata.org/#%23added%20before%202016-10%0ASELECT%20%3Fhuman%20%3FhumanLabel%20%3FawardEditionLabel%20%3FawardLabel%20%3FawardWork%20%3FawardWorkLabel%20%3Fdirector%20%3FdirectorLabel%20%3Ftime%20%0AWHERE%20%0A%7B%0A%09%7B%0A%09%09SELECT%20%28SAMPLE%28%3Fhuman%29%20AS%20%3Fhuman%29%20%3Faward%20%3FawardWork%20%28SAMPLE%28%3Fdirector%29%20AS%20%3Fdirector%29%20%28SAMPLE%28%3FawardEdition%29%20AS%20%3FawardEdition%29%20%28SAMPLE%28%3Ftime%29%20AS%20%3Ftime%29%20WHERE%20%7B%0A%09%09%09%3Faward%20wdt%3AP31%20wd%3AQ19020%20.%09%09%09%23%20All%20items%20that%20are%20instance%20of%28P31%29%20of%20Academy%20awards%20%28Q19020%29%0A%09%09%09%7B%0A%09%09%09%09%3Fhuman%20p%3AP166%20%3FawardStat%20.%20%20%20%20%20%20%20%20%20%20%20%20%20%20%23%20Humans%20with%20an%20awarded%28P166%29%20statement%0A%09%09%09%09%3FawardStat%20ps%3AP166%20%3Faward%20.%20%20%20%20%20%09%20%23%20...%20that%20has%20any%20of%20the%20values%20of%20%3Faward%0A%09%09%09%09%3FawardStat%20pq%3AP805%20%3FawardEdition%20.%20%23%20Get%20the%20award%20edition%20%28which%20is%20%22subject%20of%22%20XXth%20Academy%20Awards%29%0A%09%09%09%09%3FawardStat%20pq%3AP1686%20%3FawardWork%20.%20%23%20The%20work%20they%20have%20been%20awarded%20for%0A%09%09%09%09%3Fhuman%20wdt%3AP31%20wd%3AQ5%20.%20%09%09%09%09%23%20Humans%0A%09%09%09%7D%20UNION%20%7B%0A%09%09%09%09%3FawardWork%20wdt%3AP31%20wd%3AQ11424%20.%20%23%20Films%0A%09%09%09%09%3FawardWork%20p%3AP166%20%3FawardStat%20.%20%23%20...%20with%20an%20awarded%28P166%29%20statement%0A%09%09%09%09%3FawardStat%20ps%3AP166%20%3Faward%20.%20%20%20%20%20%09%20%23%20...%20that%20has%20any%20of%20the%20values%20of%20%3Faward%0A%09%09%09%09%3FawardStat%20pq%3AP805%20%3FawardEdition%20.%20%23%20Get%20the%20award%20edition%20%28which%20is%20%22subject%20of%22%20XXth%20Academy%20Awards%29%0A%09%09%09%7D%0A%09%09%09OPTIONAL%20%7B%0A%09%09%09%09%3FawardEdition%20wdt%3AP585%20%3Ftime%20.%20%23%20the%20%22point%20of%20time%22%20of%20the%20Academy%20Award%0A%09%09%09%09%3FawardWork%20wdt%3AP57%20%3Fdirector%20.%0A%09%09%09%7D%0A%09%09%7D%0A%09%09GROUP%20BY%20%3FawardWork%20%3Faward%20%23%20We%20only%20want%20every%20movie%20once%20for%20a%20category%20%28a%20%27random%27%20person%20is%20selected%29%0A%09%7D%0A%0A%09SERVICE%20wikibase%3Alabel%20%7B%20%20%20%20%20%20%20%20%20%20%20%20%23%20...%20include%20the%20labels%0A%09%09bd%3AserviceParam%20wikibase%3Alanguage%20%22en%22%20.%0A%09%7D%0A%7D%0AORDER%20BY%20DESC%28%3Ftime%29

