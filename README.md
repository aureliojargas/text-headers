# Text Headers

An idea for simplified e-mail-style headers ([RFC 2822](http://www.ietf.org/rfc/rfc2822.txt)) to add metadata into text files.
> by [Aurelio Jargas](http://aurelio.net/about.html) in 2013-09-06.



## Goal

* Add metadata to text files (Title, Author, Tags, etc.)
* Simple syntax: few rules, no exceptions



## Usage ideas

Reporting utilities, static website generators and conversion tools for text markup (Markdown, Textile, …) could support the Text Headers format and use the metadata in the conversion process.

* Special macros could expand header contents inside the body contents: `%Title%`, `%Author%`, …
* The extracted metadata could be used to customize the generated document or its structure: blog post metadata, target document format, user-defined variables, …
* New features could be added using special headers, avoiding the creation of new markup or commands. For example, a simple search/replace header (`Replace: this that`), including default headers from an external file (`Include: /tmp/defaults.txt`) or even system command execution to set "variables" (`System: TODAY date '+%Y-%m-%d'`).


<!--
## Sample email headers

	Message-ID: <1234869991.499a9ee7f1d5e@foo.generated>
	Date: Tue, 17 Feb 2009 22:26:31 +1100
	Subject: Awesome subject!
	From: sender@example.org
	To: recipient@example.org
	MIME-Version: 1.0
	Content-Type: text/plain; charset=utf-8
	Content-Transfer-Encoding: quoted-printable
-->



## E-mail style file format

	Title: My nice article
	Author: John Doe
	Date: 31 Dec 1999
	
	This is the first paragraph of the contents.
	
	The headers (metadata) and the file contents are
	separated by a blank line (^$).
	
	This is basically the format used by e-mail messages:
	headers + blank line + body.



## Simple syntax

* These are the three required tokens:

		name : contents

* Blanks around tokens are ignored. Parser implementation will look like this:

		name, value = line.split(':', 1)
		name = name.strip()
		value = value.strip()

* Headers are not multiline. Each header line must contain the three tokens. There's no folding and unfolding for multiple lines as in RFC 2822.

* Unlike e-mail headers, there's no line length limit.



## Header name

### Only use US-ASCII letters and numbers: A-Za-z0-9

### Separate words with hyphens (as in e-mail headers)

	Publish-Date: 25 Dec 1999
	Modified-Date: 31 Dec 1999
	Allow-Comments: Yes

### Header name is case insensitive

	Author: John Doe
	author: John Doe
	AUTHOR: John Doe

### Leading and trailing blanks are ignored (trimmed)

	Author: John Doe
	Author   : John Doe
	   Author   : John Doe


### There's no standard names

* Each implementation will choose the list of supported headers.



## Header value

### Free form contents

* The value is always plain text (Unstructured Header Field, in RFC 2822 terms), there's no need to quote or escape.
* The contents are free form, but implementation can force a syntax, if needed.
* The encoding is not enforced, but consider using Unicode as the default.

### Blanks

* Leading and trailing blanks are ignored (trimmed).
* Inner blanks are preserved.

### Multiple values are allowed using multiple instances

	Title: My nice article
	Author: John Doe
	Author: Mary Ann
	Author: K. Lee
	Date: 31 Dec 1999

* No delimiter needed (comma, quotes, …)
* Easy to comment or remove just one single entry




## Set one, set many, unset

### The order matters

* Unlike email headers, in Text Headers the order matters.

### For single-value headers, the last defined will win

		Title: This title will be overwritten
		Title: Another title that will vanish
		Title: This will be the real title
		Author: John Doe
		Date: 31 Dec 1999

### For multiple-value headers, all instances will be saved (array)

	 	Title: My nice article
		Author: John Doe
		Date: 31 Dec 1999
		Tag: computer
		Tag: Red Hat Linux
		Tag: console

### Use an empty header to reset its contents

	 	Title: My nice article
		Author: John Doe
		Date: 31 Dec 1999
		Tag: computer
		Tag: Red Hat Linux
		Tag: console
		Tag:
		Date:

In this example, the Tag and Date headers will be reset to their default value (as if the user has never specified them).

### Single or multiple?

* The implementation will decide which headers are single or multiple valued.
* Single or multiple, all headers should be resettable with an empty value.



## Comments

### Disable header lines with # prefix

	Title: My nice article
	#Author: John Doe
	#Date: 31 Dec 1999

* The prefix `#` for comments is only valid at the line beginning.
* Optional blanks before `#` are allowed.

### Add comments to explain headers

	# Here's the document title
	Title: My nice article
	#
	# Now put the author full name
	Author: John Doe
	#
	# The date this document was created
	Date: 31 Dec 1999
	#
	# But remember, no blank lines here!
	# Any blank line will close the headers
	# and will begin the document body.



## A blank line separates headers from body

* The blank line is always required.
* When there's no headers, the first line must be blank.
* A blank line should be really empty, with no spaces nor tabs (regex: `^$`)



## The body contents can be in any textual format

* Plain text
* Markdown
* HTML
* CSS
* PHP
* ...



## Sample headers for a text file

	Content-type: Markdown
	Encoding:     UTF-8
	Language:     en-US
	Date:         31 Dec 1999
	Version:      1.2

## Sample headers for a blog post

	Title:          My nice article
	Slug:           my-nice-article
	Author:         John Doe
	Publish-Date:   25 Dec 1999
	Modified-Date:  31 Dec 1999
	Category:       Tech
	Tag:            computer
	Tag:            Red Hat Linux
	Tag:            console
	Allow-Comments: Yes
	Template:       post
	CSS:            extra.css

