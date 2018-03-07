This is the specification for the DNU file format.  'DNU' stands for 'Do Not Use.'  It is an alternative to Comma Separated Value (CSV) and similar file formats.

Whereas CSV files use commas and newlines in order to delimit fields and records, DNU files use the delimiters specified in the ASCII standard.  This means that, instead of using a comma (or tab or |) to separate data within a record, DNU files use ASCII 31 (the Unit Separator character) and, instead of newlines to separate records, DNU files use ASCII 30 (the Record Separator character) to separate records.

Additionally, the ASCII standard also specifies characters 28 (File Separator) and 29 (Group Separator).  This standard will probably make use of those characters, as well.

This project was inspired by the following web page:
https://ronaldduncan.wordpress.com/2009/10/31/text-file-formats-ascii-delimited-text-not-csv-or-tab-delimited-text/