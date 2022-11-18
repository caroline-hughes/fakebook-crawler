Web Crawler for Fakebook.com

Usage:

```$ ./3700crawler <-s server> <-p port> <username> <password>```

where -s and -p allow the user to specify the server and port to crawl, respectively, and username and password will be used to log in to Fakebook.

Our high-level approach:
1. Spent time understanding how to construct raw HTTP request headers and bodies. The website HTTP Made Really Easy (https://www.jmarshall.com/easy/http/) helped a lot.
2. Used the network calls on Chrome's dev tools to see how the cookie data was being passed around during the login process. Spent a few days working on the GET and POST for a successful login. This also requireed reading HTTPParser documentation and understanding how to leverage it to scrape the HTML for what we need.
3. Wrote the logic for handling response status', and implemented the core crawling loop. Included logic for redirection, switching to "Connection: keep-alive", checking for socket disconnection when a send or recv failed, and scraping HTML for flags in h2 tags.
4. Debugged various issues that were blocking us from getting beyond 2-3 flags before our program would error.
5. Switched our requests from HTTP 1.0 to 1.1 and added supporting headers.

Challenges:
- For a while, our crawler would get between 2-3 flags and then error, the stack trace citing an issue with recv. It was difficult to debug this error for a few reasons-- because we were using recursion to crawl, because we had some cascading try/except statements, and because it was unclear whether the problem was with our recv logic, or actually our send logic or socket connection. Ultimately, switching from a recursive strategy to a "while True:" loop fixed the problem, made it easier to debug down the line, and our code could collect all the flags without erroring.

Testing:
- We tested by running the script via our command line. We used print statements to inspect our outgoing requests and to understand the responses. When the response was delayed and the terminal lingered a few seconds before printing, we could tell there was a problem with our recv logic. Ultimately, in our testing we saw our crawler find all flags by searching between 2000-8000 paths in 5-10 minutes.