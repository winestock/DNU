This is the specification for the DNU file format.  'DNU' stands for 'Do Not Use.'  It is an alternative to Comma Separated Value (CSV) and similar file formats.

Please note that this project is in the planning stage.

## Description

Whereas CSV files use commas and newlines in order to delimit fields and records, DNU files use the delimiters specified in the ASCII standard.  This means that, instead of using a comma (or tab or |) to separate data within a record, DNU files use ASCII 31 (the Unit Separator character).  Instead of newlines to separate records, DNU files use ASCII 30 (the Record Separator character).

Additionally, the ASCII standard also specifies characters 28 (File Separator) and 29 (Group Separator).  This standard will probably make use of those characters, as well.  On the off-chance that any of these characters actually need to be escaped, then ASCII character 16 (Data Link Escape) will be reserved.

## Inspiration

This project was inspired by [this](https://ronaldduncan.wordpress.com/2009/10/31/text-file-formats-ascii-delimited-text-not-csv-or-tab-delimited-text/) excellent bit of fact-trolling.  After reading it, I had the idea of making a file standard which made use of those ASCII characters in order to address the drawbacks of the CSV format.  Further inspiration comes from commentary on Ronald Duncan's original weblog entry from [Hacker News](https://news.ycombinator.com/item?id=7474600) and [Reddit](http://www.reddit.com/r/programming/comments/21hzgs/text_file_formats_ascii_delimited_text_not_csv_or/).

Here's the basic idea.  Long ago, everyone felt the need to transfer text in a database-like format between user-level programs.  So someone (likely, several someones) came up with the quick & dirty hack of putting this information in a text file where each record was its own line and each field was separated by commas.  Such a simple convention was trivial to encode by the sending application and trivial to decode by the receiving application.

Unfortunately, commas and newline characters may appear in legitimate textual data, so they must be escaped if they appear in a field.  The character used to escape them must itself be escaped if it appears in a field literally.  Furthermore, different locales treat commas differently than in the United States.  Then there's the fact that line breaks have never been standardised.  Unix uses ASCII 10 (LF for Line Feed) as a newline character while MS-Windows adds the Carriage Return character to make the ASCII sequence '13 10' (CR LF).

[RFC 4180](https://tools.ietf.org/html/rfc4180) attempted to retroactively bring some order to the chaos, but it was released in late 2005, too late to turn a quick & dirty hack into a truly standard file format.  As [one Hacker News commenter](https://news.ycombinator.com/item?id=13266109) put it, *CSV is less a file format and more a hypothetical construct like "peace on Earth".*

## The Goal

Imagine if programmers in the 1960s & 1970s had actually paid attention to the ASCII standard instead of making the quick hack called CSV.  That's the point of this project.  I don't expect this file format to sweep all before it and settle the issues with CSV files once and for all.  I'm doing this to teach myself how to make a real file format, however simple.

In addition to the file format, I will also make some simple command line tools to manipulate files in that format.  They will perform functions similar to the Unix find, sort, wc, and other tools.

## The Name

Of course, making such a format would put it in competition with CSV, TSV, and so forth.  If it were to become common, then pressure would be brought to bear on the developers of standard tools to handle it.  The maintainers of those tools would be most cross with me for forcing them to make even more special cases in their code.  Therefore, I make this standard only for my own amusement.  That means **do not use this in production**.  To further mark this as an amateur project of the 'scratch an itch' variety, I have given it an extension of DNU, for *Do Not Use*.

At one with this intention, I deliberately make this file format gratuitously incompatible with other character-separated file formats—both comma- and tab-separated value files.  In particular, I will take pains to make this format unreadable by Microsoft Excel.

And if you don't like how this Readme looks, never fear.  I will consult [this page](https://github.com/matiassingers/awesome-readme) in order to make a Readme that will help you.

## Foundational Issues

Now it's time to be a bit more precise and rigorous.  All of the characters discussed in this document are part of both the ASCII and Unicode standards.  That means that they are either text or are meant to facilitate the display or transmission of text.  The purpose of text is primarily to be read by humans and only secondarily to be processed by computer.

Text is stored as strings.  Therefore, DNU files may not contain non-string data.  Since text is meant for people to read, then DNU files are meant to store data from or transfer data between user-level programs.  The Cold War-era ARPAnet is no more; that means that user data is guilty until proven innocent.  Therefore, applications which use this format are at liberty—and may even be required—to throw away user data which does not conform to this standard.

Consider some of the comments on [this](https://www.reddit.com/r/programming/comments/21hzgs/text_file_formats_ascii_delimited_text_not_csv_or/cgdc42g/) Reddit thread.  Several people reach Slashdot-levels of obtuseness.  Keeping the fundamentals in mind allows one to see through the objections made against using native ASCII characters in tabular data files.

**Objection:**  “Just because _you_ consider these characters (i.e., ASCII 29-31) to be invalid in text data doesn't mean that you won't find them there.  Therefore, they will still have to be escaped, reintroducing all of the complexities of comma-separated values.”

**Answer:**  The whole point to having control codes in the ASCII standard is that they facilitate the display or transfer of text without themselves being text.  Nobody says “My name is J.Q. Doe _(the ASCII DEL is silent)_.”  Nobody has a home address whose street name is interspersed with form feed characters.  In such cases, the program dealing with such (possibly maliciously) corrupted data is required to strip out the offending control codes.

**Objection:**  “So that would make this a data storage format that features silent data loss.  That means that I could feed it, say, a hundred bytes but I would have no guarantee of getting those same hundred bytes back.  I don't _think_ so.”

**Answer:**  That objection may mean something in the realm of system software, but if you're sticking a CSV file in the very implementation of a device driver, or the runtime system of a programming language, or the guts of a file system, then you deserve whatever you get.

Don't forget that the purpose of any character-separated data format is to transfer text between _user-level_ applications.  Inevitably, some user data will be corrupt, nonsensical, or even [maliciously-formed](https://www.contextis.com/blog/comma-separated-vulnerabilities).  In other words, user data may contain illegal characters.  Robust applications must take such situations into account.  In fact, even system software must take such situations into account, as well (e.g., buffer overflow attacks).

**Objection:**  “What if I want to make a delimited list of delimited lists?”  _(Note: Even the person who raised this objection admitted that this was an esoteric use case.)_

**Answer:**  _Any_ character-delimited text file is ill-suited to such a use case, [including CSV files](https://datahub.io/docs/data-packages/csv#what-is-bad-about-csv).  As the folks at DataHub point out, any CSV file “Works best for tabular data—not good for data with nesting or where structure is not especially tabular.”  The [Wikipedia entry](https://en.wikipedia.org/wiki/Comma-separated_values#General_functionality) for CSV files agrees: “CSV cannot naturally represent hierarchical or object-oriented database or other data.”

**Objection:**  “Try this: Make a table of the ASCII character set using these delimiters.  Use one column for the ASCII numeric code, another column for a description of that character, and the last column for the literal character, itself.  You'll run into the escaping problem once you reach the columns that list these delimiters.”  _(Note: This is closely related to the previous objection.)_

**Answer:**  In other words, this table would _describe_ the ASCII standard _in_ the ASCII standard to a program which already _uses_ the ASCII standard.  Apparently, this is a pressing problem which happens all the time in The Year of Our Lord Two Thousand and Whatevs.

This objection, the previous objection, and so many other objections against proper ASCII-delimited text can be, not just answered, but obviated by noting the typical use cases of any character-delimited file format.

**Objection:**  “There are no dedicated keys for these delimiters on any keyboard and there is no way to easily type these delimiters using a text editor.”

**Answer:**  Granted, most editors cannot do so, but several do.  More to the point, the problems of quoting delimiters is evidence that text editors were always the wrong tool for the job.  As much as some of us nerds look down on it, the spreadsheet application was always the proper way to deal with tabular data—even if they were invented fifteen years too late.  One of the aims of this project is to make appropriate tools for editing and viewing files in the DNU format.

## Links

Possibly-Related Software:

- [Christian Genco](http://christian.gen.co/) wrote an [implementation](https://gist.github.com/christiangenco/73a7cfdb03e381bff2e9) of ASCII Delimited Text in Ruby.
- [Tad](https://www.tadviewer.com/) is a desktop viewer for tabular data.  [(Hacker News Discussion)](https://news.ycombinator.com/item?id=14448911)
- [Recfiles](https://www.gnu.org/software/recutils/) are plain text, human-readable databases.  [(Hacker News Discussion)](https://news.ycombinator.com/item?id=9320808)  [(More Hacker News Discussion)](https://news.ycombinator.com/item?id=15302035)
- [sortcsv](https://github.com/johnweldon/sortcsv) sorts CSV files by named columns.  The [discussion](https://news.ycombinator.com/item?id=16079443) on Hacker News has pointers to similar software.
- [jsonexport](https://www.npmjs.com/package/jsonexport) converts JSON files to CSV.  [(Hacker News Discussion)](https://news.ycombinator.com/item?id=14516477)
- [tably](https://github.com/narimiran/tably) converts CSV to LaTeX tables.  [(Hacker News Discussion)](https://news.ycombinator.com/item?id=15432628)
- [Miller](http://johnkerl.org/miller/doc/index.html) combines the functions of awk, sed, cut, join, and sort for name-indexed data such as CSV or tabular JSON.  [(Hacker News Discussion)](https://news.ycombinator.com/item?id=10066742)
- [Andrew Gallant's Guide to Parsing CSV Files with Rust](https://blog.burntsushi.net/csv/)  This is aimed at beginning Rust programmers.  [(Hacker News Discussion)](https://news.ycombinator.com/item?id=14527349)
- Speaking of Andrew Gallant, here's his [xsv](https://github.com/BurntSushi/xsv) toolkit.  [(Hacker News Discussion)](https://news.ycombinator.com/item?id=9088805)  [(More Hacker News Discussion)](https://news.ycombinator.com/item?id=17943076)
- [Here](https://coderexample.com/reading-csv-file-using-javascript/) is an example of reading CSV files in JavaScript using [Papa Parse](http://papaparse.com/).  [(Hacker News Discussion of Papa Parse)](https://news.ycombinator.com/item?id=8042051)
- [csvkit](http://csvkit.readthedocs.io/en/1.0.3/) is a suite of command-line tools for converting to and working with CSV, the king of tabular file formats.
- [CTX](http://www.creativyst.com/Doc/Std/ctx/ctx.htm) “is a more precisely defined and functional alternative to CSV.”
- [Fielded Text](http://www.fieldedtext.org/) is another fix for CSV files.
- [Semantic CSV](https://github.com/metasoarous/semantic-csv) is a Clojure library which deals with the semantics of CSV files whereas other tools tend to deal with the syntax of CSV files.
- Dan Brown's [csvquote](https://github.com/dbro/csvquote) uses “harmless non-printing characters” to make parsing CSV files easier for Unix utilities.
- [Here](https://github.com/knrz/CSV.js) is Kash Nouroozi's RFC 4180-compliant JavaScript CSV parser.
- [csvfaker](https://github.com/pereorga/csvfaker) is a Python program that generates CSV files with fake data.  It is part of D. Bohdan's [list](https://github.com/dbohdan/structured-text-tools) of structured text tools.  [(Hacker News Discussion)](https://news.ycombinator.com/item?id=11649142)
- [q](http://harelba.github.io/q/) is a command line tool that allows direct execution of SQL-like queries on CSVs/TSVs (and any other tabular text files).  Discussion on [Hacker News](https://news.ycombinator.com/item?id=18453133) and on [Lobsters](https://lobste.rs/s/dfc5oe/run_sql_directly_on_csv_files_with_q).
- [txt-sushi](http://keithsheppard.name/txt-sushi/) is a collection of command line utilities for processing comma-separated and tab-delimited files.
- [Tom's Obvious, Minimal Language](https://github.com/toml-lang/toml) is a minimal configuration file format which is similar to JSON.  [(Hacker News Discussion)](https://news.ycombinator.com/item?id=17513770)
- [Jsonnet](https://jsonnet.org/) is similar to TOML.  [(Hacker News Discussion)](https://news.ycombinator.com/item?id=17363871)
- [Dhall](https://github.com/dhall-lang/dhall-lang) is a non-Turing-complete configuration language.  [(Hacker News Discussion)](https://news.ycombinator.com/item?id=15185015)
- [Comma Separated (Named) Values, Version: 1.1](https://csv11.github.io/) bills itself as “CSV Evolved (for Humans)—Easy-to-Write, Easy-to-Read.”  The [discussion on Hacker News](https://news.ycombinator.com/item?id=18190607) has some good links.
- [JSON Text Sequences](https://tools.ietf.org/html/rfc7464) combines ASCII delimiters with JSON in order to make parsing very large JSON files tractable.  I learned of it from [this](https://twitter.com/technomancy/status/907656943069736960) Twitter thread.
- [TextQL](https://github.com/dinedal/textql) allows one to execute SQL queries against CSV or TSV files.
- The structure which the [UXY](https://github.com/sustrik/uxy) project adds to Unix tools is similar in spirit to this project.  [(Hacker News Discussion)](https://news.ycombinator.com/item?id=19954195)
- eBay has written [an entire suite](https://ebay.github.io/tsv-utils/) of TSV tools in the D programming language.  [(Hacker News Discussion)](https://news.ycombinator.com/item?id=20848581)
- [cq](https://github.com/jolmg/cq) is another program that allows one to query CSV files using SQL.  [(Hacker News Discussion)](https://news.ycombinator.com/item?id=21028021)
- [RAWGraphs](https://rawgraphs.io/) is “the missing link between spreadsheets and data visualization.”  It handles both CSV and TSV files.  [(Hacker News Discussion)](https://news.ycombinator.com/item?id=21695561)
- [qsv](https://github.com/jqnatividad/qsv) is written in Rust and specializes in analyzing CSV files. [(Hacker News Discussion)](https://news.ycombinator.com/item?id=38733617)
- [csvlens](https://github.com/YS-L/csvlens) is written in Rust.  It's like less, but for CSV files.  [(Hacker News Discussion)](https://news.ycombinator.com/item?id=38889820)

Problems with CSV Files:

- [Falsehoods Programmers Believe About CSVs](https://donatstudios.com/Falsehoods-Programmers-Believe-About-CSVs) [(Hacker News Discussion)](https://news.ycombinator.com/item?id=13265881)  [(More Hacker News Discussion)](https://news.ycombinator.com/item?id=16809963)
- [Comma Separated Vulnerabilities](https://www.contextis.com/resources/blog/comma-separated-vulnerabilities/) [(Hacker News Discussion)](https://news.ycombinator.com/item?id=14489794)
- [Dangers of CSV Injection](http://georgemauer.net/2017/10/07/csv-injection.html) [(Hacker News Discussion)](https://news.ycombinator.com/item?id=15438894)
- [Design and Implementation of CSV/Excel Upload for SaaS](http://www.kalzumeus.com/2015/01/28/design-and-implementation-of-csvexcel-upload-for-saas/) [(Hacker News Discussion)](https://news.ycombinator.com/item?id=8960280)  Key Quote: “It is _hellaciously_ difficult to do CSV import well in the general case.”
- [DataHub's overview](https://datahub.io/docs/data-packages/csv#what-is-bad-about-csv) of what is wrong with CSV.
- [CSV Is Not A Standard](https://chriswarrick.com/blog/2017/04/07/csv-is-not-a-standard/) Chris Warrick points out that RFC 4180, itself, denies that it is a standard.

Other Links:

- [Ask Hacker News: If your job involves continually importing CSVs, what industry is it?](https://news.ycombinator.com/item?id=13275834)
- Here's are lists of online [CSV](https://catonmat.net/online-csv-tools) and [TSV](https://catonmat.net/online-tsv-tools) utilities from [Cat on Mat](https://catonmat.net/).
- [CSV Challenge](https://news.ycombinator.com/item?id=9438109) (NB, original is a dead link)
- [So You Want To Write Your Own CSV Code?](http://thomasburette.com/blog/2014/05/25/so-you-want-to-write-your-own-CSV-code/)  [(Hacker News Discussion)](https://news.ycombinator.com/item?id=7796268)  [(Proggit Discussion)](http://www.reddit.com/r/programming/comments/26g24y/so_you_want_to_write_your_own_csv_code/)
- The [CSVW](http://w3c.github.io/csvw/) standard was a W3C effort to address the limitations of current CSV usage and to shore up the ambiguities of RFC 4180.  They completed their work in March 2016, but nothing seems to have come of their work.  I will use their work as a reference.
