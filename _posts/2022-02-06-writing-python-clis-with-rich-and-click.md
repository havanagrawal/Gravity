---
layout: post
title:  "Writing Python CLIs with Rich and Click (Part 1)"
date:   2022-02-05 17:43:00 -0700
category: ["pinned"]
---

**This is a two-part post. This is part 1, and part 2 will be posted soon!**

Recently, someone reached out to me over email to appreciate one of my scraping projects, [GoodreadsScraper](https://github.com/havanagrawal/GoodreadsScraper). They said they found it useful and used it to build a dashboard driven by the data from the "My Books" section. Emails like these make me truly ecstatic, to know that someone finds value from your software.

However, GoodreadsScraper is now over 4 years old, with only small patches being committed infrequently. I decided to give it a lookover to see if any pieces needed improvements. When I visited my own README page, one of the things that irked me the most was this snippet of example usage:

```bash
scrapy crawl \
  --logfile=scrapy.log \
  -a start_page_no=1 \
  -a end_page_no=25 \
  -a list_name="1.Best_Books_Ever" \
  -s OUTPUT_FILE_SUFFIX="best_001_025" \
  list
```

Do you see any problems? Here is what truly bothered me:

1.  **Inconsistent**: Every argument "looks" different. Long-form (`--logfile`), short-form (`-a`, `-s`), key-value pairs (`start_page_no=1`), arguments (`list`) are mixed together. Even naming formats are inconsistent, switching between no underscores (`logfile`), lower snake case (`start_page_no`) and upper snake case (`OUTPUT_FILE_SUFFIX`).
1.  **Not Discoverable**: Apart from reading the README (which, despite being fittingly named, is rarely perused by hurried users), there is no way to discover how to run the tool. If one of the arguments was to change, the command above would fail with no actionable error message. (`scrapy crawl --help` will print out only scrapy specific options and arguments, it has no knowledge of how this specific scraper is set up)
1.  **Exposes Implementation**: While not evident at first, the `-a` options are actually [arguments to `ListSpider`](https://github.com/havanagrawal/GoodreadsScraper/blob/0090f37c354fc3a02b7611a34915205f409c47f6/GoodreadsScraper/spiders/list_spider.py#L18). Further, the last command-line argument to scrapy is `list`, which is [the name of ListSpider](https://github.com/havanagrawal/GoodreadsScraper/blob/0090f37c354fc3a02b7611a34915205f409c47f6/GoodreadsScraper/spiders/list_spider.py#L14). If any of these were to change, the command line would once again fail with no actionable error message.
1.  **Blocks the CLI**: When this command is run, the only feedback of progress is a wall of text that is printed to the console. If the user specified a `logfile`, then the CLI simply appears to hang, with the wall of text redirected to `scrapy.log`. Will it end in 5 minutes? 8 days? Lifeetime of the observable universe? Keep guessing.

I had been aware of these problems earlier, and my "patch" was to wrap the common usage into a shabbily, hastily thrown-together Bash script, which had the following usage:

```bash
./run_scraper.sh "1.Best_Books_Ever" 1 50 best_books
```

The invocation looked infinitesimally better, but was very obviously a tiny bandaid over a colossal UX crack.

This was enough to shove me into a weekend of thinking up and implementing a better user experience. Fortunately, I had used `click` for another project ([Wikidata Toolkit](https://github.com/havanagrawal/wikidata-toolkit)). I had also heard about `rich` as the new trending library for writing polished command-line interfaces from PythonBytes. I'd been itching to use both of these, and finally found a fantastic opportunity to give them both a go.

## Using Click to implement a conversational, discoverable CLI

Click is a Python package for building beautiful and standard command-line interfaces. While it has a programmatic API as well, the most common way to use it is with their decorators.

The best part about Click is that you write regular functions and then decorate them with `@click.command()` to make it a CLI command and `@click.option`/`@click.argument` to annotate the method parameters as CLI args.

The way I started is by working backwards from the command I would want to run. While the original invocation was structurally inaesthetic, it wasn't too bad functionally. I decided that the following invocation would be reasonable:

```bash
python3 crawl.py list \
  --list_name="1.Best_Books_Ever" \
  --start_page=1 \
  --end_page=50 \
  --output_file_suffix="best_001_050"
```

_Side Note_: There are broadly two CLI styles. One is where the CLI is essentially a single function and does one single thing. Prime examples are `rm`, `ls`, `mv` and other built-in Unix commands. The other is a CLI that also has **sub-commands**; think `git add`, `git commit`. Since this scraper exposes crawling different types of items, and each invocation behaves differently, I decided to go with the latter style.

### Step 1: Enabling `python3 crawl.py`

The first step was to set up the commands and sub-commands. This was relatively easy:

```python
@click.group()
def crawl():
    ...
```

This makes `crawl` a top level command group. I can now add sub-commands by using `@crawl` as a decorator.

```python
@crawl.command()
def list():
    ...

@crawl.command()
def author():
    ...
```

This enables running the tool as `python3 crawl.py list` or `python3 crawl.py author`

## Step 2: Implementing the sub-commands

Next was simply a matter of implementing the `list` and `author` functions that take the appropriate arguments:

```python
@crawl.command()
def list(list_name: str, start_page: int, end_page: int,
         output_file_suffix):
    ...
```

and then decorating them with the appropriate Click options

```python
@crawl.command()
@click.option("--list_name", required=True, help="Goodreads Listopia list name.", type=str)
@click.option("--start_page", help="Start page number", type=int, default=1, show_default=True)
@click.option("--end_page", required=True, help="End page number", type=int)
@click.option("--output_file_suffix", help="The suffix for the output file [default: list name]", type=str)
def list(list_name: str, start_page: int, end_page: int, output_file_suffix: str):
    ...
```

Voila. You can now run the script as we earlier outlined!

## Bonus: Pretty Help Messages

One of the things Click pretty much gives you for free is excellent help messages. An incorrect invocation of the tool will automatically print the help message, and then quit with a non-zero exit code. For example, this is what `python3 crawl.py --help` prints:

```
Usage: crawl.py [OPTIONS] COMMAND [ARGS]...

Options:
  --log_file TEXT  Log file for scrapy logs  [default: scrapy.log]
  --help           Show this message and exit.

Commands:
  author  Crawl all authors on Goodreads.
  list    Crawl a Goodreads Listopia List.
```

and this is what `python3 crawl.py list --help` prints:

```
Usage: crawl.py list [OPTIONS]

  Crawl a Goodreads Listopia List.

  Crawl all pages between start_page and end_page (inclusive) of a Goodreads
  Listopia List.

  By default, two files will be created:
    1.   book_{output_file_suffix}.jl, for books from the given list
    2.   author_{output_file_suffix}.jl, for authors of the above books from the given list

Options:
  --list_name TEXT           Goodreads Listopia list name. [required]
  --start_page INTEGER       Start page number  [default: 1]
  --end_page INTEGER         End page number  [required]
  --output_file_suffix TEXT  The suffix for the output file. [default: list name]
  --help                     Show this message and exit.
```

It is pretty extensive in describing what arguments are required, what types are expected, and what the defaults are.

## Learnings

-  While using Click addressed three out of four disadvantages, the last one (appearing to hang) is still not addressed. In the next post, we'll see how we can leverage Rich to provide useful (and pretty!) feedback about the progress of the scraper.
-  [https://clig.dev/](https://clig.dev/) has some excellent notes on writing modern CLI tools. In particular, I was inspired by their sections on discoverability, naming and modelling CLIs as a conversation.
-  [PythonBytes](https://pythonbytes.fm/) is a fantastic Python podcast. It has helped me keep up to date with the latest and greatest in Python without investing more than 40 minutes a week. All credit for discovering Click and Rich goes to them.