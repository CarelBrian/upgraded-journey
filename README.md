
![Logo](https://github.com/LeMeteore/upgraded-journey/blob/master/img/Logo.png)

#### **Introduction**
Whether you are a developer, data scientist or marketer, being able to develop web scrapers is a hugely valuable skill to have. When it comes to web scraping, Python Scrapy is the godfather of web scraping frameworks. Scrapy has a huge amount of functionality but it is a very structured web scraping framework and has a Scrapy way of doing things. So to get the most out of Scrapy, you need to understand the main building blocks within Scrapy and how they work together.




#### **Presentation of Scrapy**
Scrapy (/ˈskreɪpaɪ/) is an application framework for crawling web sites and extracting structured data which can be used for a wide range of useful applications, like data mining, information processing or historical archival. Even though Scrapy was originally designed for [web scraping](https://en.wikipedia.org/wiki/Web_scraping), it can also be used to extract data using APIs (such as [Amazon Associates Web Services](https://affiliate-program.amazon.com/gp/advertising/api/detail/main.html)) or as a general purpose web crawler.

###### **Why Scrapy:**

Scrapy provides a lot of powerful features for making scraping easy and efficient, such as:
- Built-in support for [selecting and extracting](https://docs.scrapy.org/en/latest/topics/selectors.html#topics-selectors) data from HTML/XML sources using extended CSS selectors and XPath expressions, with helper methods to extract using regular expressions.
- An [interactive shell console](https://docs.scrapy.org/en/latest/topics/shell.html#topics-shell) (IPython aware) for trying out the CSS and XPath expressions to scrape data, very useful when writing or debugging your spiders.
- Built-in support for [generating feed exports](https://docs.scrapy.org/en/latest/topics/feed-exports.html#topics-feed-exports) in multiple formats (JSON, CSV, XML) and storing them in multiple backends (FTP, S3, local filesystem)
- Robust encoding support and auto-detection, for dealing with foreign, non-standard and broken encoding declarations.
- [Strong extensibility support](https://docs.scrapy.org/en/latest/index.html#extending-scrapy), allowing you to plug in your own functionality using [signals](https://docs.scrapy.org/en/latest/topics/signals.html#topics-signals) and a well-defined API (middlewares, [extensions](https://docs.scrapy.org/en/latest/topics/extensions.html#topics-extensions), and [pipelines](https://docs.scrapy.org/en/latest/topics/item-pipeline.html#topics-item-pipeline)).

###### **When Should You Use Scrapy?**
It can come down to personal perference and your existing tech stack but Scrapy can be used for virtually every web scraping use case.

However, there are some situations where it really shines and others where using another web scraping stack is just as good or even better.

**a Large Scale Web Scraping**

Where Scrapy really shines is if you want to build scalable web scrapers that power your production data feeds. This is the use case Scrapy was built for and is the king at.

Out of the box, with Scrapy you have a highly scalable web scraping stack that is trusted by thousands of developers every day in production applications.

**b Want To Learn A Powerful Web Scraping Framework**

If you wanted to learn a web scraping technology to expand your skillset and enhance your CV then Scrapy is the best option. Companies all over the world use Scrapy in their production applications so if you can call yourself an experienced Scrapy developer you will never be short of work.

Learning Scrapy is also a great side skill for any data scientist, as you can quickly develop scrapers to power your data pipelines without the need of building your own web scraping stack from scratch.

**c New To Web Scraping & Have Small Project**

Generally speaking, the learning curve associated with Python Scrapy is a bit steeper than other approaches. So if you are new to web scraping and just have a small once off project like scraping a couple pages, then using the Python Requests/BeautifulSoup stack is a good option.

It is quick and easy to get setup, and you don't need to worry about learning about Scrapy Items, Pipelines, Feed Exporters, etc.

Just create the simplest possible scraper to get the job done.

However, if you want to get into web scraping, need to build a production system or want to learn a powerful web scraping framework then learning Scrapy is well worth the extra hour or two to learn it.

**d Scraping Javascript Heavy Websites**

Although, Scrapy can't render Javascript out of the box, it can be easily configured to do so by using one of the many headless browser extensions for Scrapy.

However, if you would like to focus solely on making a bot that heavily interacts with a page, logging in and navigating around then it might be better for you if you used the Puppeteer or Playwright in their native language of Javascript.




###### **Installation**
There are different ways to install Scrapy. The simple option when using [Anaconda](https://docs.anaconda.com/anaconda/) or [Miniconda](https://docs.conda.io/projects/conda/en/latest/user-guide/install/index.html) is: to install the package from the [conda-forge](https://conda-forge.org/) channel, which has up-to-date packages for Linux, Windows and macOS. We can also install Scrapy by using pip. 


#### **1. An overview of Scrapy**
In this section, we are going to scrape quotes.toscrape.com, a website that lists quotes from famous authors. We'll walk through five tasks.
- Creating a new Scrapy project

- Writing a spider to crawl a site and extract data

- Exporting the scraped data using the command line

- Changing spider to recursively follow links

- Using spider arguments

###### **Creating a project**
First step, we have to set up a new Scrapy project. We'll have to enter a directory where you’d like to store your code and run:

```
scrapy startproject tutorial
```

This will create a `tutorial` directory with the following contents:

```
tutorial/
    scrapy.cfg            # deploy configuration file

    tutorial/             # project's Python module, you'll import your code from here
        __init__.py

        items.py          # project items definition file

        middlewares.py    # project middlewares file

        pipelines.py      # project pipelines file

        settings.py       # project settings file

        spiders/          # a directory where you'll later put your spiders
            __init__.py
```

###### **Writing a spider to crawl a site and extract data**
Spiders are classes that you define and that Scrapy uses to scrape information from a website (or a group of websites). They must subclass Spider and define the initial requests to make, optionally how to follow links in the pages, and how to parse the downloaded page content to extract data.

This is the code for our first Spider. Save it in a file named `quotes_spider.py` under the `tutorial/spiders` directory in your project:
```
from pathlib import Path

import scrapy


class QuotesSpider(scrapy.Spider):
    name = "quotes"

    def start_requests(self):
        urls = [
            'https://quotes.toscrape.com/page/1/',
            'https://quotes.toscrape.com/page/2/',
        ]
        for url in urls:
            yield scrapy.Request(url=url, callback=self.parse)

    def parse(self, response):
        page = response.url.split("/")[-2]
        filename = f'quotes-{page}.html'
        Path(filename).write_bytes(response.body)
        self.log(f'Saved file {filename}')
```
Our Spider subclasses `scrapy.Spider` and defines some attributes and methods:

`name`: identifies the Spider. It must be unique within a project, that is, you can’t set the same name for different Spiders.

`start_requests()`: must return an iterable of Requests (you can return a list of requests or write a generator function) which the Spider will begin to crawl from. Subsequent requests will be generated successively from these initial requests.

`parse()`: a method that will be called to handle the response downloaded for each of the requests made. The response parameter is an instance of `TextResponse` that holds the page content and has further helpful methods to handle it.

The `parse()` method usually parses the response, extracting the scraped data as dicts and also finding new URLs to follow and creating new requests (`Request`) from them.


###### **How to run our spider**
You have to go to the project’s top level directory and run:

```
scrapy crawl quotes
```

You will get an output similar to this:
```
... (omitted for brevity)
2016-12-16 21:24:05 [scrapy.core.engine] INFO: Spider opened
2016-12-16 21:24:05 [scrapy.extensions.logstats] INFO: Crawled 0 pages (at 0 pages/min), scraped 0 items (at 0 items/min)
2016-12-16 21:24:05 [scrapy.extensions.telnet] DEBUG: Telnet console listening on 127.0.0.1:6023
2016-12-16 21:24:05 [scrapy.core.engine] DEBUG: Crawled (404) <GET https://quotes.toscrape.com/robots.txt> (referer: None)
2016-12-16 21:24:05 [scrapy.core.engine] DEBUG: Crawled (200) <GET https://quotes.toscrape.com/page/1/> (referer: None)
2016-12-16 21:24:05 [scrapy.core.engine] DEBUG: Crawled (200) <GET https://quotes.toscrape.com/page/2/> (referer: None)
2016-12-16 21:24:05 [quotes] DEBUG: Saved file quotes-1.html
2016-12-16 21:24:05 [quotes] DEBUG: Saved file quotes-2.html
2016-12-16 21:24:05 [scrapy.core.engine] INFO: Closing spider (finished)
...
```
Now, check the files in the current directory. You should notice that two new files have been created: quotes-1.html and quotes-2.html, with the content for the respective URLs, as our `parse` method instructs.

###### **A shortcut to the start_requests method**
Instead of implementing a `start_requests()` method that generates `scrapy.Request` objects from URLs, you can just define a `start_urls` class attribute with a list of URLs. This list will then be used by the default implementation of `start_requests()` to create the initial requests for your spider:

```
from pathlib import Path

import scrapy


class QuotesSpider(scrapy.Spider):
    name = "quotes"
    start_urls = [
        'https://quotes.toscrape.com/page/1/',
        'https://quotes.toscrape.com/page/2/',
    ]

    def parse(self, response):
        page = response.url.split("/")[-2]
        filename = f'quotes-{page}.html'
        Path(filename).write_bytes(response.body)
```

The `parse()` method will be called to handle each of the requests for those URLs, even though we haven’t explicitly told Scrapy to do so. This happens because `parse()` is Scrapy’s default callback method, which is called for requests without an explicitly assigned callback.

###### **Extracting data**
The best way to learn how to extract data with Scrapy is trying selectors using the [Scrapy shell](https://docs.scrapy.org/en/latest/topics/shell.html#topics-shell). 

Run:
```
scrapy shell 'https://quotes.toscrape.com/page/1/'
```
On Windows, use double quotes instead:
```
scrapy shell "https://quotes.toscrape.com/page/1/"
```
You will see something like:
```
[ ... Scrapy log here ... ]
2016-09-19 12:09:27 [scrapy.core.engine] DEBUG: Crawled (200) <GET https://quotes.toscrape.com/page/1/> (referer: None)
[s] Available Scrapy objects:
[s]   scrapy     scrapy module (contains scrapy.Request, scrapy.Selector, etc)
[s]   crawler    <scrapy.crawler.Crawler object at 0x7fa91d888c90>
[s]   item       {}
[s]   request    <GET https://quotes.toscrape.com/page/1/>
[s]   response   <200 https://quotes.toscrape.com/page/1/>
[s]   settings   <scrapy.settings.Settings object at 0x7fa91d888c10>
[s]   spider     <DefaultSpider 'default' at 0x7fa91c8af990>
[s] Useful shortcuts:
[s]   shelp()           Shell help (print this help)
[s]   fetch(req_or_url) Fetch request (or URL) and update local objects
[s]   view(response)    View response in a browser
```
Using the shell, you can try selecting elements using [CSS](https://www.w3.org/TR/selectors) with the response object:

```
>>> response.css('title')
[<Selector xpath='descendant-or-self::title' data='<title>Quotes to Scrape</title>'>]
```

The result of running `response.css('title')` is a list-like object called `SelectorList`, which represents a list of Selector objects that wrap around XML/HTML elements and allow you to run further queries to fine-grain the selection or extract the data.

To extract the text from the title above, you can do:

```
>>> response.css('title::text').getall()
['Quotes to Scrape']
```
There are two things to note here: one is that we’ve added `::text` to the CSS query, to mean we want to select only the text elements directly inside `<title>` element. If we don’t specify `::text`, we’d get the full title element, including its tags:

```
>>> response.css('title').getall()
['<title>Quotes to Scrape</title>']
```

The other thing is that the result of calling `.getall()` is a list: it is possible that a selector returns more than one result, so we extract them all. When you know you just want the first result, as in this case, you can do:

```
>>> response.css('title::text').get()
'Quotes to Scrape'
```

As an alternative, you could’ve written:
```
>>> response.css('title::text')[0].get()
'Quotes to Scrape'
```

Accessing an index on a `SelectorList` instance will raise an IndexError exception if there are no results:
```
>>> response.css('noelement')[0].get()
Traceback (most recent call last):
...
IndexError: list index out of range
```

You might want to use `.get()` directly on the `SelectorList` instance instead, which returns `None` if there are no results:

```
>>> response.css("noelement").get()
```

There’s a lesson here: for most scraping code, you want it to be resilient to errors due to things not being found on a page, so that even if some parts fail to be scraped, you can at least get some data.

Besides the `getall()` and `get()` methods, you can also use the re() method to extract using [regular expressions](https://docs.python.org/3/library/re.html):

```
>>> response.css('title::text').re(r'Quotes.*')
['Quotes to Scrape']
>>> response.css('title::text').re(r'Q\w+')
['Quotes']
>>> response.css('title::text').re(r'(\w+) to (\w+)')
['Quotes', 'Scrape']
```
In order to find the proper CSS selectors to use, you might find useful opening the response page from the shell in your web browser using `view(response)`. You can use your browser’s developer tools to inspect the HTML and come up with a selector (see [Using your browser’s Developer Tools for scraping](https://docs.scrapy.org/en/latest/topics/developer-tools.html#topics-developer-tools)).

[Selector Gadget](https://selectorgadget.com/) is also a nice tool to quickly find CSS selector for visually selected elements, which works in many browsers.


###### **XPath: a brief intro**
Besides [CSS](https://www.w3.org/TR/selectors), Scrapy selectors also support using [XPath](https://www.w3.org/TR/xpath/all/) expressions:

```
>>> response.xpath('//title')
[<Selector xpath='//title' data='<title>Quotes to Scrape</title>'>]
>>> response.xpath('//title/text()').get()
'Quotes to Scrape'
```
XPath expressions are very powerful, and are the foundation of Scrapy Selectors. In fact, CSS selectors are converted to XPath under-the-hood. You can see that if you read closely the text representation of the selector objects in the shell.

We won’t cover much of XPath here, but you can read more about [using XPath with Scrapy Selectors here](https://docs.scrapy.org/en/latest/topics/selectors.html#topics-selectors). To learn more about XPath, we recommend [this tutorial to learn XPath through examples](http://zvon.org/comp/r/tut-XPath_1.html), and [this tutorial to learn “how to think in XPath”](http://zvon.org/comp/r/tut-XPath_1.html).

###### **Extracting quotes and authors**
Now that you know a bit about selection and extraction, let’s complete our spider by writing the code to extract the quotes from the web page.

Each quote in [https://quotes.toscrape.com](https://quotes.toscrape.com/) is represented by HTML elements that look like this:

```
<div class="quote">
    <span class="text">“The world as we have created it is a process of our
    thinking. It cannot be changed without changing our thinking.”</span>
    <span>
        by <small class="author">Albert Einstein</small>
        <a href="/author/Albert-Einstein">(about)</a>
    </span>
    <div class="tags">
        Tags:
        <a class="tag" href="/tag/change/page/1/">change</a>
        <a class="tag" href="/tag/deep-thoughts/page/1/">deep-thoughts</a>
        <a class="tag" href="/tag/thinking/page/1/">thinking</a>
        <a class="tag" href="/tag/world/page/1/">world</a>
    </div>
</div>
```
Let’s open up scrapy shell and play a bit to find out how to extract the data we want:

```
scrapy shell 'https://quotes.toscrape.com'
```
We get a list of selectors for the quote HTML elements with:
```
>>> response.css("div.quote")
[<Selector xpath="descendant-or-self::div[@class and contains(concat(' ', normalize-space(@class), ' '), ' quote ')]" data='<div class="quote" itemscope itemtype...'>,
 <Selector xpath="descendant-or-self::div[@class and contains(concat(' ', normalize-space(@class), ' '), ' quote ')]" data='<div class="quote" itemscope itemtype...'>,
 ...]
```
Each of the selectors returned by the query above allows us to run further queries over their sub-elements. Let’s assign the first selector to a variable, so that we can run our CSS selectors directly on a particular quote:
```
>>> quote = response.css("div.quote")[0]
```

Now, let’s extract `text`, `author` and the `tags` from that `quote` using the quote object we just created:
```
>>> text = quote.css("span.text::text").get()
>>> text
'“The world as we have created it is a process of our thinking. It cannot be changed without changing our thinking.”'
>>> author = quote.css("small.author::text").get()
>>> author
'Albert Einstein'
```
Given that the tags are a list of strings, we can use the `.getall()` method to get all of them:
```
>>> tags = quote.css("div.tags a.tag::text").getall()
>>> tags
['change', 'deep-thoughts', 'thinking', 'world']
```
Having figured out how to extract each bit, we can now iterate over all the quotes elements and put them together into a Python dictionary:
```
>>> for quote in response.css("div.quote"):
...    text = quote.css("span.text::text").get()
...    author = quote.css("small.author::text").get()
...    tags = quote.css("div.tags a.tag::text").getall()
...    print(dict(text=text, author=author, tags=tags))
{'text': '“The world as we have created it is a process of our thinking. It cannot be changed without changing our thinking.”', 'author': 'Albert Einstein', 'tags': ['change', 'deep-thoughts', 'thinking', 'world']}
{'text': '“It is our choices, Harry, that show what we truly are, far more than our abilities.”', 'author': 'J.K. Rowling', 'tags': ['abilities', 'choices']}
...
```

###### **Extracting data in our spider**
Let’s get back to our spider. Until now, it doesn’t extract any data in particular, just saves the whole HTML page to a local file. Let’s integrate the extraction logic above into our spider.

A Scrapy spider typically generates many dictionaries containing the data extracted from the page. To do that, we use the `yield` Python keyword in the callback, as you can see below:
```
import scrapy


class QuotesSpider(scrapy.Spider):
    name = "quotes"
    start_urls = [
        'https://quotes.toscrape.com/page/1/',
        'https://quotes.toscrape.com/page/2/',
    ]

    def parse(self, response):
        for quote in response.css('div.quote'):
            yield {
                'text': quote.css('span.text::text').get(),
                'author': quote.css('small.author::text').get(),
                'tags': quote.css('div.tags a.tag::text').getall(),
            }
```

If you run this spider, it will output the extracted data with the log:
```
2016-09-19 18:57:19 [scrapy.core.scraper] DEBUG: Scraped from <200 https://quotes.toscrape.com/page/1/>
{'tags': ['life', 'love'], 'author': 'André Gide', 'text': '“It is better to be hated for what you are than to be loved for what you are not.”'}
2016-09-19 18:57:19 [scrapy.core.scraper] DEBUG: Scraped from <200 https://quotes.toscrape.com/page/1/>
{'tags': ['edison', 'failure', 'inspirational', 'paraphrased'], 'author': 'Thomas A. Edison', 'text': "“I have not failed. I've just found 10,000 ways that won't work.”"}
```

##### **Storing the scraped data**
The simplest way to store the scraped data is by using [Feed exports](https://docs.scrapy.org/en/latest/topics/feed-exports.html#topics-feed-exports), with the following command:
```
scrapy crawl quotes -O quotes.json
```
That will generate a `quotes.json` file containing all scraped items, serialized in [JSON](https://en.wikipedia.org/wiki/JSON).

The `-O` command-line switch overwrites any existing file; use `-o` instead to append new content to any existing file. However, appending to a JSON file makes the file contents invalid JSON. When appending to a file, consider using a different serialization format, such as [JSON Lines](http://jsonlines.org/):
```
scrapy crawl quotes -o quotes.jsonl
```
The JSON Lines format is useful because it’s stream-like, you can easily append new records to it. It doesn’t have the same problem of JSON when you run twice.

In small projects (like the one in this tutorial), that should be enough. However, if you want to perform more complex things with the scraped items, you can write an Item Pipeline. A placeholder file for [Item Pipelines](https://docs.scrapy.org/en/latest/topics/item-pipeline.html#topics-item-pipeline) has been set up for you when the project is created, in `tutorial/pipelines.py`. Though you don’t need to implement any item pipelines if you just want to store the scraped items.

##### **Following links**
Let’s say, instead of just scraping the stuff from the first two pages from https://quotes.toscrape.com, you want quotes from all the pages in the website.

Now that you know how to extract data from pages, let’s see how to follow links from them.

First thing is to extract the link to the page we want to follow. Examining our page, we can see there is a link to the next page with the following markup:
```
<ul class="pager">
    <li class="next">
        <a href="/page/2/">Next <span aria-hidden="true">&rarr;</span></a>
    </li>
</ul>
```
We can try extracting it in the shell:
```
>>> response.css('li.next a').get()
'<a href="/page/2/">Next <span aria-hidden="true">→</span></a>'
```
This gets the anchor element, but we want the attribute `href`. For that, Scrapy supports a CSS extension that lets you select the attribute contents, like this:
```
>>> response.css('li.next a::attr(href)').get()
'/page/2/'
```

There is also an `attrib` property available (see [Selecting element attributes](https://docs.scrapy.org/en/latest/topics/selectors.html#selecting-attributes) for more):
```
>>> response.css('li.next a').attrib['href']
'/page/2/'
```
Let’s see now our spider modified to recursively follow the link to the next page, extracting data from it:
```
import scrapy


class QuotesSpider(scrapy.Spider):
    name = "quotes"
    start_urls = [
        'https://quotes.toscrape.com/page/1/',
    ]

    def parse(self, response):
        for quote in response.css('div.quote'):
            yield {
                'text': quote.css('span.text::text').get(),
                'author': quote.css('small.author::text').get(),
                'tags': quote.css('div.tags a.tag::text').getall(),
            }

        next_page = response.css('li.next a::attr(href)').get()
        if next_page is not None:
            next_page = response.urljoin(next_page)
            yield scrapy.Request(next_page, callback=self.parse)
```

Now, after extracting the data, the `parse()` method looks for the link to the next page, builds a full absolute URL using the `urljoin()` method (since the links can be relative) and yields a new request to the next page, registering itself as callback to handle the data extraction for the next page and to keep the crawling going through all the pages.

In our example, it creates a sort of loop, following all the links to the next page until it doesn’t find one – handy for crawling blogs, forums and other sites with pagination.

###### **A shortcut for creating Requests**
As a shortcut for creating Request objects you can use `response.follow`:
```
import scrapy


class QuotesSpider(scrapy.Spider):
    name = "quotes"
    start_urls = [
        'https://quotes.toscrape.com/page/1/',
    ]

    def parse(self, response):
        for quote in response.css('div.quote'):
            yield {
                'text': quote.css('span.text::text').get(),
                'author': quote.css('span small::text').get(),
                'tags': quote.css('div.tags a.tag::text').getall(),
            }

        next_page = response.css('li.next a::attr(href)').get()
        if next_page is not None:
            yield response.follow(next_page, callback=self.parse)
```

Unlike scrapy.Request, `response.follow` supports relative URLs directly - no need to call urljoin. Note that `response.follow` just returns a Request instance; you still have to yield this Request.

You can also pass a selector to `response.follow` instead of a string; this selector should extract necessary attributes:
```
for href in response.css('ul.pager a::attr(href)'):
    yield response.follow(href, callback=self.parse)
```

For `<a>` elements there is a shortcut: `response.follow` uses their href attribute automatically. So the code can be shortened further:
```
for a in response.css('ul.pager a'):
    yield response.follow(a, callback=self.parse)
```

To create multiple requests from an iterable, you can use response.follow_all instead:
```
anchors = response.css('ul.pager a')
yield from response.follow_all(anchors, callback=self.parse)
```

or, shortening it further:
```
yield from response.follow_all(css='ul.pager a', callback=self.parse)
```

###### **More examples and patterns**
Here is another spider that illustrates callbacks and following links, this time for scraping author information:
```
import scrapy


class AuthorSpider(scrapy.Spider):
    name = 'author'

    start_urls = ['https://quotes.toscrape.com/']

    def parse(self, response):
        author_page_links = response.css('.author + a')
        yield from response.follow_all(author_page_links, self.parse_author)

        pagination_links = response.css('li.next a')
        yield from response.follow_all(pagination_links, self.parse)

    def parse_author(self, response):
        def extract_with_css(query):
            return response.css(query).get(default='').strip()

        yield {
            'name': extract_with_css('h3.author-title::text'),
            'birthdate': extract_with_css('.author-born-date::text'),
            'bio': extract_with_css('.author-description::text'),
        }
```
This spider will start from the main page, it will follow all the links to the authors pages calling the `parse_author` callback for each of them, and also the pagination links with the `parse` callback as we saw before.

Here we’re passing callbacks to `response.follow_all` as positional arguments to make the code shorter; it also works for `Request`.

The `parse_author` callback defines a helper function to extract and cleanup the data from a CSS query and yields the Python dict with the author data.


##### **Using spider arguments**
You can provide command line arguments to your spiders by using the `-a` option when running them:
```
scrapy crawl quotes -O quotes-humor.json -a tag=humor
```
These arguments are passed to the Spider’s `__init__` method and become spider attributes by default.

In this example, the value provided for the `tag` argument will be available via `self.tag`. You can use this to make your spider fetch only quotes with a specific tag, building the URL based on the argument:
```
import scrapy


class QuotesSpider(scrapy.Spider):
    name = "quotes"

    def start_requests(self):
        url = 'https://quotes.toscrape.com/'
        tag = getattr(self, 'tag', None)
        if tag is not None:
            url = url + 'tag/' + tag
        yield scrapy.Request(url, self.parse)

    def parse(self, response):
        for quote in response.css('div.quote'):
            yield {
                'text': quote.css('span.text::text').get(),
                'author': quote.css('small.author::text').get(),
            }

        next_page = response.css('li.next a::attr(href)').get()
        if next_page is not None:
            yield response.follow(next_page, self.parse)
```
If you pass the `tag=humor` argument to this spider, you’ll notice that it will only visit URLs from the `humor` tag, such as `https://quotes.toscrape.com/tag/humor`.

You can [learn more about handling spider arguments here](https://docs.scrapy.org/en/latest/topics/spiders.html#spiderargs).


##### **Next steps**
This section covered only the basics of Scrapy, but there’s a lot of other features not mentioned here. Check the [What else?](https://docs.scrapy.org/en/latest/intro/overview.html#topics-whatelse) section in [Scrapy at a glance](https://docs.scrapy.org/en/latest/intro/overview.html#intro-overview) chapter for a quick overview of the most important ones.

You can continue from the sections below: Basic concepts to know more about the command-line tool, spiders, selectors, the built-in services, the solving specific problems, the extending Scrapy and other things the tutorial hasn’t covered like modeling the scraped data.

Before that, we have another example project for you below.

#### **2. Example project: QuoteBot**
This is a Scrapy project to scrape quotes from famous people from http://quotes.toscrape.com ([github repo](https://github.com/scrapinghub/spidyquotes)).

###### **Extracted data**
This project extracts quotes, combined with the respective author names and tags. The extracted data looks like this sample:
```
{
    'author': 'Douglas Adams',
    'text': '“I may not have gone where I intended to go, but I think I ...”',
    'tags': ['life', 'navigation']
}
```
###### **Spiders**
This project contains two spiders and you can list them using the `list` command:
```
$ scrapy list
toscrape-css
toscrape-xpath
```
Both spiders extract the same data from the same website, but `toscrape-css` employs CSS selectors, while `toscrape-xpath` employs XPath expressions.


###### **Running the spiders**
You can run a spider using the `scrapy crawl` command, such as:
```
$ scrapy crawl toscrape-css
```
If you want to save the scraped data to a file, you can pass the `-o` option:
```
$ scrapy crawl toscrape-css -o quotes.json
```

#### **3. Example project: Scraping an E-Commerce Site**
As a test site, you will scrape ShopClues for 4G-Smartphones

Let’s first generate a basic spider:
```
scrapy genspider shopclues www.shopclues.com/mobiles-featured-store-4g-smartphone.html
```
This is what the ShopClues web page looks like:
![ShopClues web page](https://av-eks-blogoptimized.s3.amazonaws.com/182.png)



The following information needs to be extracted from the page:

- Product Name
- Product price
- Product discount
- Product image

**Extracting image URLs of the product**
On careful inspection, it can be seen that the attribute “data-img” of the <img> tag can be used to extract image URLs:
```
response.css("img::attr(data-img)").extract()
```
![alt text](https://av-eks-blogoptimized.s3.amazonaws.com/192.png)

**Extracting product name from <img> tags**
Notice that the “title” attribute of the <img> tag contains the product’s full name:
![alt text](https://av-eks-blogoptimized.s3.amazonaws.com/202.png)
```
response.css("img::attr(title)").extract()
```
Similarly, selectors for price(“.p_price”) and discount(“.prd_discount”).

**How to download product images?**
Scrapy provides reusable image pipelines for downloading files attached to a particular item (for example, when you scrape products and also want to download their images locally).

In order to use the images pipeline to download images, it needs to be enabled in the settings.py file. Add the following lines to the file:
```
ITEM_PIPELINES = {
  'scrapy.pipelines.images.ImagesPipeline': 1
}
IMAGES_STORE = 'tmp/images/'
```

you are basically telling scrapy to use the ‘Images Pipeline,’ and the location for the images should be in the folder ‘tmp/images/.’ The final spider would now be:
```
import scrapy

class ShopcluesSpider(scrapy.Spider):
   #name of spider
   name = 'shopclues'

   #list of allowed domains
   allowed_domains = ['www.shopclues.com/mobiles-featured-store-4g-smartphone.html']
   #starting url
   start_urls = ['http://www.shopclues.com/mobiles-featured-store-4g-smartphone.html/']
   #location of csv file
   custom_settings = {
       'FEED_URI' : 'tmp/shopclues.csv'
   }

   def parse(self, response):
       #Extract product information
       titles = response.css('img::attr(title)').extract()
       images = response.css('img::attr(data-img)').extract()
       prices = response.css('.p_price::text').extract()
       discounts = response.css('.prd_discount::text').extract()

       for item in zip(titles,prices,images,discounts):
           scraped_info = {
               'title' : item[0],
               'price' : item[1],
               'image_urls' : [item[2])], #Set's the url for scrapy to download images
               'discount' : item[3]
           }

           yield scraped_info
```
Here are a few things to note:

custom_settings: This is used to set the settings of an individual spider. Remember that settings.py is for the whole project, so here you tell scrapy that the output of this spider should be stored in a CSV  file “shopclues.csv” that is to be stored in the “tmp” folder.
scraped_info[“image_urls”]: This is the field that scrapy checks for the image’s link. If you set this field with a list of URLs, scrapy will automatically download and store those images for you.
On running the spider, the output can be read from “tmp/shopclues.csv”:
![alt text](https://av-eks-blogoptimized.s3.amazonaws.com/211.png)

You also get the images downloaded. Check the folder “tmp/images/full,” and you will see the images:
![alt text](https://av-eks-blogoptimized.s3.amazonaws.com/222.png)

Also, notice that scrapy automatically adds the download path of the image on your system in the csv:
![alt text](https://av-eks-blogoptimized.s3.amazonaws.com/232.png)
There you have your own little e-commerce aggregator.


#### **4. Example project: Scraping Techcrunch: Creating Your Own RSS Feed Reader**
![alt text](https://av-eks-blogoptimized.s3.amazonaws.com/Screen-Shot-2017-07-24-at-12.png)

One of Scrapy’s features is its ability to handle XML data with ease, and in this part, you are going to extract data from Techcrunch’s RSS feed.

Create a basic spider:
```
Scrapy genspider techcrunch techcrunch.com/feed/
```
Let’s have a look at the XML; the marked portion is data of interest:
![alt text](https://av-eks-blogoptimized.s3.amazonaws.com/242.png)
Here are some observations from the page:

- Each article is present between `<item>``</item>` tags, and there are 20 such items(articles).
- The title of the post is in `<title></title`> tags.
- The link to the article can be found in `<link>` tags.
<pubDate> contains the date of publishing.
- The author’s name is enclosed between funny-looking <dc:creator> tags.

**Overview of XPath and XML**
XPath is a syntax that is used to define XML documents. It can be used to traverse through an XML document. Note that XPath follows a hierarchy.

**Extracting the title of the post**

Let’s extract the title of the first post. Similar to `response.css(..)`, the function `response.xpath(..)` in scrapy deals with XPath. The following code should do it:
```
response.xpath("//item/title").extract_first()
```
Output:
```
u'<title xmlns:content="http://purl.org/rss/1.0/modules/content/" xmlns:wfw="http://wellformedweb.org/CommentAPI/" xmlns:dc
="http://purl.org/dc/elements/1.1/" xmlns:atom="http://www.w3.org/2005/Atom" xmlns:sy="http://purl.org/rss/1.0/modules/syndication/"
xmlns:slash="http://purl.org/rss/1.0/modules/slash/" xmlns:georss="http://www.georss.org/georss" xmlns:geo="http://www.w3.org/2003/
01/geo/wgs84_pos#" xmlns:media="http://search.yahoo.com/mrss/">Why the future of deep learning depends on finding good data</title>'
```
Wow! That’s a lot of content, but only the text content of the title is of interest. Let’s filter it out:
```
response.xpath("//item/title/text()").extract_first()
```
Output:
```
u'Why the future of deep learning depends on finding good data'
```
This is much better. Notice that text() here is equivalent of ::text from CSS selectors. Also, look at the XPath //item/title/text(); here, you are basically saying to find the element “item” and extract the “text” content of its sub-element “title”.

Similarly, the Xpaths for the link, pubDate as:

Link – //item/link/text()
Date of publishing – //item/pubDate/text()

**Extracting author name: dealing with namespaces in XML**

Notice the `<creator>` tags:
![alt text](https://av-eks-blogoptimized.s3.amazonaws.com/252.png)
The tag itself has some text “dc:” because of which it can’t be extracted using XPath, and the author name itself is crowded with “![CDATA..” irrelevant text. These are just XML namespaces, and you don’t want to have anything to do with them, so we’ll ask scrapy to remove the namespace:
```
response.selector.remove_namespaces()
```
Now when you try extracting the author name, it will work:
```
response.xpath("//item/creator/text()").extract_first()
```
Output: u’Ophir Tanz,Cambron Carter’

The complete spider for TechCrunch would be:
```
import scrapy

class TechcrunchSpider(scrapy.Spider):
    #name of the spider
    name = 'techcrunch'

    #list of allowed domains
    allowed_domains = ['techcrunch.com/feed/']

    #starting url for scraping
    start_urls = ['http://techcrunch.com/feed/']

    #setting the location of the output csv file
    custom_settings = {
        'FEED_URI' : 'tmp/techcrunch.csv'
    }

    def parse(self, response):
        #Remove XML namespaces
        response.selector.remove_namespaces()

        #Extract article information
        titles = response.xpath('//item/title/text()').extract()
        authors = response.xpath('//item/creator/text()').extract()
        dates = response.xpath('//item/pubDate/text()').extract()
        links = response.xpath('//item/link/text()').extract()

        for item in zip(titles,authors,dates,links):
            scraped_info = {
                'title' : item[0],
                'author' : item[1],
                'publish_date' : item[2],
                'link' : item[3]
            }

            yield scraped_info
```
Let’s run the spider:
```
scrapy crawl techcrunch
```

![alt text](https://av-eks-blogoptimized.s3.amazonaws.com/261.png)
And there you have your own RSS reader!



#### **5. To know more about Scrapy**
This section shows the links to have more informations about the basic concepts, built-in services, solving specific problems and extending Scrapy.

###### **Basic concepts**
Here, we present some basic concepts of Scrapy following by a brief description. We can clic on the link of each concept for more details.

| Concept                                                                     | Description                                                                                                                                 |
|-----------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------|
| [Command line tool](https://docs.scrapy.org/en/latest/topics/commands.html) | Learn about the command-line tool used to manage your Scrapy project.                                                                       |
| [Spiders](https://docs.scrapy.org/en/latest/topics/spiders.html)            | Write the rules to crawl your websites.                                                                                                     |
| [Selectors](https://docs.scrapy.org/en/latest/topics/selectors.html)            | Extract the data from web pages using XPath.                                                                                                |
| [Scrapy shell](https://docs.scrapy.org/en/latest/topics/shell.html)            | Test your extraction code in an interactive environment.                                                                                    |
| [Items](https://docs.scrapy.org/en/latest/topics/items.html)            | Define the data you want to scrape.                                                                                                         |
| [Item Loaders](https://docs.scrapy.org/en/latest/topics/loaders.html)            | Populate your items with the extracted data.                                                                                                |
| [Item Pipeline](https://docs.scrapy.org/en/latest/topics/item-pipeline.html)            | Post-process and store your scraped data.                                                                                                   |
| [Feed exports](https://docs.scrapy.org/en/latest/topics/feed-exports.html)            | Output your scraped data using different formats and storages.                                                                              |
| [Requests and Responses](https://docs.scrapy.org/en/latest/topics/request-response.html)            | Understand the classes used to represent HTTP requests and responses.                                                                       |
| [Link Extractors](https://docs.scrapy.org/en/latest/topics/link-extractors.html)            | Convenient classes to extract links to follow from pages.                                                                                   |
| [Settings](https://docs.scrapy.org/en/latest/topics/settings.html)            | Learn how to configure Scrapy and see all [available settings](https://docs.scrapy.org/en/latest/topics/settings.html#topics-settings-ref). |
| [Exceptions](https://docs.scrapy.org/en/latest/topics/exceptions.html)            | See all available exceptions and their meaning. |



###### **Built-in services**
Here are some built-in services of Scrapy.

| Built-in                                                                    | Description                                                                                                                                 |
|-----------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------|
| [Logging](https://docs.scrapy.org/en/latest/topics/logging.html) | Learn how to use Python’s builtin logging on Scrapy.                                                                       |
| [Stats Collection](https://docs.scrapy.org/en/latest/topics/stats.html)            | Collect statistics about your scraping crawler.                                                                                                     |
| [Sending e-mail](https://docs.scrapy.org/en/latest/topics/email.html)            | Send email notifications when certain events occur.                                                                                                     |
| [STelnet Console](https://docs.scrapy.org/en/latest/topics/telnetconsole.html)            | Inspect a running crawler using a built-in Python console.                                                                                                     |


###### **Solving specific problems**
Some way to solve specific problems we can encounter.

| Solving way                                                                  | Description                                                                                |
|------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------|
| [Frequently Asked Questions](https://docs.scrapy.org/en/latest/faq.html)     | Get answers to most frequently asked questions.                                            |
| [Debugging Spiders](https://docs.scrapy.org/en/latest/topics/debug.html)     | Learn how to debug common problems of your Scrapy spider.                                  |
| [Spiders Contracts](https://docs.scrapy.org/en/latest/topics/contracts.html) | Learn how to use contracts for testing your spiders.                                       |
| [Common Practices](https://docs.scrapy.org/en/latest/topics/practices.html)  | Get familiar with some Scrapy common practices.                                            |
| [Broad Crawls](https://docs.scrapy.org/en/latest/topics/broad-crawls.html)   | Tune Scrapy for crawling a lot domains in parallel.                                        |
| [Using your browser’s Developer Tools for scraping](https://docs.scrapy.org/en/latest/topics/developer-tools.html)   | Learn how to scrape with your browser’s developer tools.                                   |
| [Selecting dynamically-loaded content](https://docs.scrapy.org/en/latest/topics/dynamic-content.html)   | Read webpage data that is loaded dynamically.                                              |
| [Debugging memory leaks](https://docs.scrapy.org/en/latest/topics/leaks.html)   | Learn how to find and get rid of memory leaks in your crawler.                             |
| [Downloading and processing files and images](https://docs.scrapy.org/en/latest/topics/media-pipeline.html)   | Download files and/or images associated with your scraped items.                           |
| [Deploying Spiders](https://docs.scrapy.org/en/latest/topics/deploy.html)   | Deploying your Scrapy spiders and run them in a remote server.                             |
| [AutoThrottle extension](https://docs.scrapy.org/en/latest/topics/autothrottle.html)   | Adjust crawl rate dynamically based on load.                                               |
| [Benchmarking](https://docs.scrapy.org/en/latest/topics/benchmarking.html)   | Check how Scrapy performs on your hardware.                                                |
| [Jobs: pausing and resuming crawls](https://docs.scrapy.org/en/latest/topics/jobs.html)   | Learn how to pause and resume crawls for large spiders.                                    |
| [Coroutines](https://docs.scrapy.org/en/latest/topics/coroutines.html)   | Use the [coroutine syntax](https://docs.python.org/3/reference/compound_stmts.html#async). |
| [asyncio](https://docs.scrapy.org/en/latest/topics/asyncio.html)   | Use `asyncio` and `asyncio`-powered libraries. |



###### **Extending Scrapy**
For more information, we bring in the table below informations about extending Scrapy.

| Extending                                                                | Description                                       |
|--------------------------------------------------------------------------|---------------------------------------------------|
| [Architecture overview](https://docs.scrapy.org/en/latest/topics/architecture.html) | Understand the Scrapy architecture.               |
| [Downloader Middleware](https://docs.scrapy.org/en/latest/topics/downloader-middleware.html) | Customize how pages get requested and downloaded. |
| [Spider Middleware](https://docs.scrapy.org/en/latest/topics/spider-middleware.html) | Customize the input and output of your spiders.   |
| [Extensions](https://docs.scrapy.org/en/latest/topics/extensions.html) | Extend Scrapy with your custom functionality.     |
| [Signals](https://docs.scrapy.org/en/latest/topics/signals.html) | See all available signals and how to work with them.     |
| [Scheduler](https://docs.scrapy.org/en/latest/topics/scheduler.html) | Understand the scheduler component.     |
| [Item Exporters](https://docs.scrapy.org/en/latest/topics/exporters.html) | Quickly export your scraped items to a file (XML, CSV, etc).     |
| [Components](https://docs.scrapy.org/en/latest/topics/components.html) | Learn the common API and some good practices when building custom Scrapy components.     |
| [Core API](https://docs.scrapy.org/en/latest/topics/api.html) | Use it on extensions and middlewares to extend Scrapy functionality.     |


#### **Conclusion**
We have just scratched the surface of Scrapy’s potential as a web scraping tool. It is highly recommended to improve your skills by browsing the links below to go further and contribute to the development of Scrapy.

| All the rest                                                                                 | Description                                       |
|----------------------------------------------------------------------------------------------|---------------------------------------------------|
| [Release notes](https://docs.scrapy.org/en/latest/news.html)          | See what has changed in recent Scrapy versions.               |
| [Contributing to Scrapy](https://docs.scrapy.org/en/latest/contributing.html) | Learn how to contribute to the Scrapy project. |
| [Versioning and API stability](https://docs.scrapy.org/en/latest/versioning.html) | Understand Scrapy versioning and API stability. |


#### **Ressources**

[Scrapy official site](https://scrapy.org/)

[ScrapyOps](https://scrapeops.io/python-scrapy-playbook/scrapy-web-scraping-intro/)

[Web Scraping in Python using Scrapy (with multiple examples)](https://www.analyticsvidhya.com/blog/2017/07/web-scraping-in-python-using-scrapy/)







