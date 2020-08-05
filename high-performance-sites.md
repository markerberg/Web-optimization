SUMMARY FROM 'HIGH PERFORMANCE WEBSITES' BOOK

Rule 1: Make fewer http requests (major rule to follow)
  * examples of how to do it
    - use image maps: if we use multiple images within a navbar, switching to image maps will speed up our page
    - css sprites: we can combine all our images within a sprite sheet and select them by location rather than making requests
    - inline image with `data:` uri scheme : data:URL scheme grants us immediate data. They're embedded into the page & wont be cached
    - combine script & stylesheets: multiple scripts into one script file. Same for styles. keep js modularized but combine in the build process

Rule 2: Use a Content Delivery Network to bring responses closer to the user
  A users proximity to a server has impact on the response time. It’s necessary to deploy content across multiple, geographically dispersed servers.
  A CDN is a collection of servers distrubited across multiple locations used to deliver content to users based on network proximity. 
  CDNs can also help with backups, caching, absorb traffic spikes. They deliver static content (images, scripts, stylesheets, and Flash)

Rule 3: Use Expires Headers and cache content efficiently
  - Utilize the browsers caching capability by adding an EXPIRES header to scripts, stylesheets & assets. This makes them cacheable.
  The expires header is sent by the server and tells the browser that a file can be used until a certain data. When the browser needs to load in
  an asset and they see that header, it can fetch it from cache. We should use these headers on values that change infrequently!
  - Another way is to use the `Cache-Control` header. The value of `Cache-Control` has a `max-age:n` value in seconds which lets us set 
  how long an asset can be cached before requesting it. The browser will compare this value against the last time we requested the asset
  - We can also specify both headers to increase utilize caching even more. But we need to know if it makes sense to cache.
  - Discover how frequently files are updated. If there barely being updated (like a logo), set the expiration date far ahead and cache so
  it’s a better UX since we won't request the same content every time. Else, we fire a request all the time
  - If a user has cached content and we want them to recieve a recent update...just change the file name and we will load a new fetch. We can also use 
  `Cache-Busting`

Rule 4: Gzip Components
  - We can shrink the HTTP response and send smaller packets to the client. This reduces network response times and page weight. 
  - Clients attach an `Accept-Encoding` header in the request to indicate support for suppression. Gzip is the most popular compression
  - You can Gzip your html, styles and scripts.
  - When we're dealing with a proxy request, its complicated bc we might deal with browsers that dont support Gzip and might cache an uncompressed file.
  We need to use a `Vary` header in the response. This allows us to cache multiple versions of the response (https://www.keycdn.com/support/vary-header)

Rule 5: Put stylesheets on top
  - When we put stylesheets in the HEAD, the page will render progressively (browser will load whatever content it has to, asap). This allows us to 
  load in header/navbar/logo etc. first, letting users see visual content sooner than later...thus improving UX. When we put stylesheets at the bototm, 
  it prohibits progressive rendering in browsers. Browsers block rendering to avoid redrawing elements of the page if their styles change...so browsers 
  will delay showing visible components while it waits for stylesheets that are placed at the bottom.
  - TLDR: progressive rendering is blocked until all stylesheets have been loaded so its better to put all stylesheets in the HEAD so they're downloaded 
  first and rendering isnt blocked

Rule 6: Put scripts at the bottom
  - putting scrips at the bottom enables progressive rendering and enables greater download parallelization. script block parallel loading. With scripts, 
  progressive rendering is blocked from rendering all content below the script. Any files below the script are also blocked from being downloaded until 
  that script is completed. Moving scripts to the bottom means content is rendered progressively.
  - browsers use parallel download where they can download 2 files at a time. This is good for performance, but its disabled when it needs to download a 
  script. Only until a script is finished loading, can the remaining files in the page be downloaded (like images, fonts etc.) 
  - with scripts at the bottom, the page contents aren't blocked from rendering and components are downloaded asap. note: We can also use DEFER attr
  - TLDR: content below script is blocked from rendering & files below script is blocked from downloading, users might see blank screens! Put scripts at bottom.

Rule 7: Avoid CSS expressions
  - these CSS expressions should be rarely used. They are evaluated more frequently than we expect (on page render, resize, scroll, or mouse move); bad performance.

Rule 8: Make Javascript and CSS External
  - inline script/styles might be faster than external sheets becuase we dont need to make multiple http requests for external sheets. But in the real world, external
  sheets are faster.
  - external sheets can be cached by the browser but internal ones cant. Inline JS and CSS are downloaded every time the HTML doc is requested since that data needs 
  to be readily available If external files are cached by the browser the size of the HTML doc is reduced without increasing the amount of HTTP reqs.
  * check page views to determine internal vs external
  - if we have users who visit site once per month, we might want to inline since any external files that are cached might get purged between visits (if they visit one a month).
  - for frequent visitors, its better to have those assets external since they're more views per session and there are frequent requests. 
  * check file reuse
  - if every page uses the same js or css, external files is preferred since we can have those files in cache, as users navigate our page
  -  for low reuse rate, inlining makes more sense
  * post-onload download 
  - in this scenario we can inline js and css for the first page view. For all second page views, leverage external files. We dynamically download external files in the page after its
  completely loaded (by using the onLoad event). This will place external files in cache anticipating they will continue to other pages.
  * using cookies as indicator
  - use cookies to let a server know if a user has a certain file cached. By returning a session-based cookie with the file, our server can decide whether to use 
  inline styling based on the prescence of that cookie. w/out cookie -> we use inline....but w/ cookie -> we us external 
  - example: on first req, the server sees NO cookie and generates a page that uses inline approach. After page is loaded, the server dynamically downloads external files (as well as set a cookie).
  For the next page view, a server will see the cookie and generate a page using external files.

Rule 9: Reduce DNS Lookup
  - We use DNS to map hostnames to IP addressed (like phonebooks map people name to numbers). When we type huffpost.com -> the browser contacts DNS to return its IP address
  - We can have multiple IP addresses associated with a hostname (our website), which provides a high degree of redundancy for our site.
  - DNS does cost 20-120 milliseconds to look up an IP. Browsers cant download anything from this host until the DNS lookup is complete. The response time depends on the DNS resolver, 
  the load of request on it, our proximity to it, and bandwidth speed. DNS lookups are cached tho
  - The number of DNS lookups equals the number of unique hostnames in a web page. This also includes any hostnames that are linked in the webpage (as well as assets).
  - reduce the amount of hostnames to reduce the amount of parallel downloading that takes place.

Rule 10: Minify Javascript
  - we should minify & remove unnecessary characters (comment, whitespace, etc.) from our code -> to reduce its size -> which will improve load times and reduce size of downloaded file
  - we can also use obfuscation, which minifies as well as turns variable names into smaller variable names....but this might also lead to bugs
  - Make sure we are minifying our external AND INTERNAL scripts.
  - we need to optimize our CSS also, in order to make its file size smaller. Find ways to optimize (use '0' not '0px', use shorthand font, use #000 not #000000 etc.)

Rule 11: Avoid Redirects
  - redirects make our pages slower. Some redirects are necessary (like when performing an Authentication checkpoint) but some aren't (like using it to grab some modules)
  - we're able to redirect with the 'document.location' but its better to use standard 3xx HTTP status codes.
  - redirects delays the delivery of the HTML document. Nothing can be rendered until its been delivered. Inserting a redirect between user and html doc delays everything on the page.
  * examples of bad redirects and alternatives
    - missing a trailing slash when it should've been there: if our urls require a trailing slash and users forget to add it, we SHOULDN'T redirect in that case. Instead, use an "Alias"
    directive. OR use the "Mod_rewrite" module.
    - connecting old sites to a new one: instead of this, we can also use the `Alias or Mod_rewrite`. We could change our code, have the old handler call the new one instead. We also use 
    redirects for tracking. Theres alot of alternatives around.

Rule 12: Remove duplicate scripts
 - many sites unintentionally do this (mainly through team mistake or various scripts). duplicated scripts are bad as it is unnecessary http requests and wasted Javascript execution.
 - to avoid this, use a script management module in our template...like a script tag. Make sure scripts are included only once.

Rule 13: Configure, Reconfigure or Remove ETags
  - Entity tags (ETags) lets us know if an asset that our browser has cached matches the one of the origins server. An alternative to this would be to use the "Last-Modified data" header
  which lets us know if we need to fetch a new asset from the server or not. 
  - If we have multiple servers hosting our iste and you use Apache or IIS with default ETag configs- our users might be getting slower pages, our servers having a higher load, we're consuming
  more bandwidth, and proxies arent caching content efficiently.
  - configure ETags to take advantage of their flexible validation capabilities. If we have no need to customize ETags, remove them completely.

Rule 14: Make Ajax cacheable
  - Ajax uses asynchronous data retrieval to prevnent us from repainting/reloading an entire screen when we need data to interacting with the webserver (from the client) and updating only
  necessary components (all while the UI is still responsive). 
