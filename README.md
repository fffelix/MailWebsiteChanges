# MailWebsiteChanges

Python script to keep track of website changes; sends email notifications on updates and/or also provides an RSS feed

To specify which parts of a website should be monitored, <b>XPath selectors</b> (e.g. "//h1"), <b>CSS selectors</b> (e.g. "h1"), <b>and regular expressions can be used</b> (just choose the tools you like!).

MailWebsiteChanges is related to <a href="http://code.google.com/p/pagemon-chrome-ext/">PageMonitor</a> for Chrome and <a href="https://addons.mozilla.org/de/firefox/addon/alertbox/">AlertBox</a> / <a href="https://addons.mozilla.org/de/firefox/addon/check4change/">Check4Change</a> for Firefox. However, instead of living in your web browser, you can run it independently from command line / bash and install it as a simple cron job running on your linux server.


<i>This is Open Source -- so please contribute eagerly! ;-)</i>


## Configuration
Configuration can be done by creating a <code>config.py</code> file (just place this file in the program folder):
Some examples:

### Website definitions
<pre>
<code>
sites = [

         {'name': 'example-css',
          'parsers': [uri(uri='https://github.com/mtill', contenttype='html'),
                      css(contentcss='div')
                     ]
         },

         {'name': 'example-404',
          'ignoredErrors': ['404'],
          'parsers': [uri(uri='https://github.com/404', contenttype='html'),
                      css(contentcss='div')
                     ]
         },

         {'name': 'example-xpath',
          'parsers': [uri(uri='https://example-webpage.com/test', contenttype='html'),
                      xpath(contentxpath='//div[contains(concat(\' \', normalize-space(@class), \' \'), \' package-version-header \')]')
                     ]
         },

         {'name': 'my-script',
          'parsers': [command(command='/home/user/script.sh', contenttype='text'),
                      regex(contentregex='^.*$')
                     ]
         }

]
</code>
</pre>

 * parameters:

   * <b>name</b>  
     name of the entry, used as an identifier when sending email notifications
   * <b>receiver</b> (optional)  
     Overrides global receiver specification.
   * <b>ignoredErrors</b> (optional)  
     Suppress warnings for specified error codes.

 * parameters for the URL receiver:

   * <b>uri</b>  
     URI of the website
   * <b>contenttype</b> (optional; default: 'html')  
     content type, e.g., 'xml'/'html'/'text'.
   * <b>enc</b> (optional; default: 'utf-8')  
     Character encoding of the website, e.g., 'utf-8' or 'iso-8859-1'.
   * <b>userAgent</b> (optional)  
     Defines the user agent string, e.g.,  
     'userAgent': 'Mozilla/5.0 (X11; Fedora; Linux x86_64; rv:49.0) Gecko/20100101 Firefox/49.0'
   * <b>accept</b> (optional)  
     Defines the accept string, e.g.,  
     'accept': 'text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8'

 * parameters for the Command receiver

   * <b>command</b>  
     the command
   * <b>contenttype</b> (optional; default: 'text')  
     content type, e.g., 'xml'/'html'/'text'.
   * <b>enc</b> (optional; default: 'utf-8')  
     Character encoding of the website, e.g., 'utf-8' or 'iso-8859-1'.

 * parameters for the XPath parser:

   * <b>contentxpath</b>  
     XPath expression for the content sections to extract
   * <b>titlexpath</b> (optional)  
     XPath expression for the title sections to extract

 * parameters for the CSS parser:

   * <b>contentcss</b>  
     CSS expression for the content sections to extract
   * <b>titlecss</b> (optional)  
     CSS expression for the title sections to extract

 * parameters for the RegEx parser:

   * <b>contentregex</b>  
     Regular expression for content parsing
   * <b>titleregex</b> (optional)  
     Regular expression for title parsing

 * We collect some XPath/CSS snippets at this place: <a href="https://github.com/Debianguru/MailWebsiteChanges/wiki/snippets">Snippet collection</a> - please feel free to add your own definitions!

 * The <b>--dry-run="shortname"</b> option might be useful in order to validate and fine-tune a definition.

 * If you would like to keep the data stored in a different place than the working directory, you can include something like this:
  <pre>
   <code>
  os.chdir('/path/to/data/directory')
   </code>
  </pre>

### Mail settings
<pre>
<code>
enableMailNotifications = True   #enable/disable notification messages; if set to False, only send error messages
maxMailsPerSession = -1   #max. number of mails to send per session; ignored when set to -1
subjectPostfix = 'A website has been updated!'

sender = 'me@mymail.com'
smtphost = 'mysmtpprovider.com'
useTLS = True
smtpport = 587
smtpusername = sender
smtppwd = 'mypassword'
receiver = 'me2@mymail.com'   # set to '' to also disable notifications in case of errors (not recommended)
</code>
</pre>


### RSS Feeds
If you prefer to use the RSS feature, you just have to specify the path of the feed file which should be generated by the script (e.g., rssfile = 'feed.xml') and then point your webserver to that file. You can also invoke the mwcfeedserver.py script which implements a very basic webserver.

<pre>
 <code>
enableRSSFeed = True   #enable/disable RSS feed

rssfile = 'feed.xml'
maxFeeds = 100
 </code>
</pre>


### Program execution
To setup a job that periodically runs the script, simply attach something like this to your /etc/crontab:
<pre>
 <code>
0 8-22/2    * * *   root	/usr/bin/python3 /usr/bin/mwc
 </code>
</pre>
This will run the script every two hours between 8am and 10pm.

If you prefer invoking the script with an alternate configuration files, simply pass the name of the configuration file as an argument, e.g., for <code>my_alternate_config.py</code>, use <code>mwc --config=my_alternate_config</code>.


## Requirements
Requires Python 3, <a href="http://lxml.de/">lxml</a>, and <a href="http://pythonhosted.org/cssselect/">cssselect</a>.
For <b>Ubuntu 12.04</b>, type:

  * sudo apt-get install python3 python3-dev python3-setuptools libxml2 libxslt1.1 libxml2-dev libxslt1-dev python-libxml2 python-libxslt1
  * sudo easy\_install3 pip
  * sudo pip-3.2 install lxml cssselect

For <b>Ubuntu 14.04</b>, type:

  * sudo apt-get install python3-lxml python3-pip
  * sudo pip3 install cssselect

