This is the specification for the DNU file format.  'DNU' stands for 'Do Not Use.'  It is an alternative to Comma Separated Value (CSV) and similar file formats.

Please note that this project is in the planning stage.

##Description

Whereas CSV files use commas and newlines in order to delimit fields and records, DNU files use the delimiters specified in the ASCII standard.  This means that, instead of using a comma (or tab or |) to separate data within a record, DNU files use ASCII 31 (the Unit Separator character) and, instead of newlines to separate records, DNU files use ASCII 30 (the Record Separator character).

Additionally, the ASCII standard also specifies characters 28 (File Separator) and 29 (Group Separator).  This standard will probably make use of those characters, as well.

##Inspiration

This project was inspired by [this](https://ronaldduncan.wordpress.com/2009/10/31/text-file-formats-ascii-delimited-text-not-csv-or-tab-delimited-text/) excellent bit of fact-trolling.  After reading it, I had the idea of making a file standard which made use of those ASCII characters in order to address the drawbacks of the CSV format.  Imagine if programmers in the 1960s & 1970s had actually paid attention to the ASCII standard instead of making the quick hack called CSV.  That's the point of this project.  Further inspiration comes from commentary on Ronald Duncan's original weblog entry from [Hacker News](https://news.ycombinator.com/item?id=7474600) and [Reddit](http://www.reddit.com/r/programming/comments/21hzgs/text_file_formats_ascii_delimited_text_not_csv_or/).

Here's the basic idea.  Long ago, everyone felt the need to transfer text in a database-like format between user-level programs.  So someone (likely, several someones) came up with the quick & dirty hack of putting this information in a text file where each record was its own line and each field was separated by commas.  Such a simple convention was trivial to encode by the sending application and trivial to decode by the receiving application.



##The Name

Of course, making such a format would put it in competition with CSV, TSV, and so forth.  If it were to become common, then pressure would be brought to bear on the developers of standard tools to handle it.  The maintainers of those tools would be most cross with me for forcing them to make even more special cases in their code.  Therefore, I make this standard only for my own amusement.  That means **do not use this in production**.  To further mark this as an amateur project of the 'scratch an itch' variety, I have given it an extension of DNU, for *Do Not Use*.

to accomodate a file format that's more a haphazard collection of hacks than a real standard.  As [one Hacker News commenter](https://news.ycombinator.com/item?id=13266109) put it, *CSV is less a file format and more a hypothetical construct like "peace on Earth".*

And if you don't like how this Readme looks, never fear.  I will consult [this page](https://github.com/matiassingers/awesome-readme) in order to make a Readme that will help you.