sql-guess-bad-urls
==================

Use SQL to guess mistyped or truncated URL requests

My coffee website INeedCoffee.com went live way back in April 1999. And although the search engines do a good job indexing the site, what I found when I studied the log files was that the site was receiving about 1,000 404 requests every week to invalid pages. The pages weren’t moved, the links to the site had errors. Often these links resided on websites and forums that weren’t being updated.

My goal was to connect the reader with the page they believed the link was taking them to. So instead of returning a simple 404 Page Not Found “Tough Luck” screen, I wanted to connect the reader to the resource they requested. When I looked at the requests, my human eyes could usually figure out what page they had incorrectly linked to. Many of the broken link requests fell into three patterns.

Truncated. Often the link had a missing trailing slash or a few letters.
Extra Characters. The other common element was that the link was correct, but somehow extra letters got attached to the URL.
Mistyped Characters. Sometimes when a URL is hand copied, a zero becomes an “O” or a one becomes an “l”.
Here is an example of a valid link to INeedCoffee and 3 examples of how things could go wrong. http://www.ineedcoffee.com/07/aeropress/

Truncated: http://www.ineedcoffee.com/07/aeropres (trailing “s” and slash is missing from request)
Extra Characters: http://www.ineedcoffee.com/07/aeropress/ target= (when the url href doesn’t get closed properly, the link sent includes the characters after the link until the quote is closed)
Mistyped Characters: http://www.ineedcoffee.com/07/aer0press/ (in this example, an “o” is typed as a zero)
One Stored Procedure and One User Defined Function

The first thing I noticed was that it was rare for the garbage characters to appear between the base domain name (www.ineedcoffee.com) and the article URL (/07/aeropress/). My idea was to compile a full list of valid URLs on my site and then perform a character by character comparison. For every character that matches, I would score a point. The URL with the most points would likely be the correct URL.

The Stored Procedure

In the stored procedure, I create a temp table loaded with valid URLs. I don’t include the home page. For INeedCoffee this means a link to each article, each section and every contributor page.

prcBadLinkSuggest

The User Defined Function

In the stored procedure prcBadLinkSuggest, the UDF udfCompareURLs is being called in the ORDER BY clause. The errorURL and a valid URL are passed. The UDF returns a score based upon character matching. The valid URL with the highest score is returned from the stored procedure. Notice that the SELECT is returning just one row and the ORDER BY is sorting on the Score in descending order (high to low).

udfCompareURLs

The User Defined Function takes the length of the ErrorURL and then constructs a loop where it does a character by character comparison building a score of matches.

Last Words

I tested this code using actual 404 data and it was extremely accurate in guessing the correct web page. It isn’t bullet-proof as it would return a low score if the garbage appears early in the URL string, but that case is extremely rare.
