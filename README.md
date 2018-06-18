# a2

More reliable than `grep -ir /etc/apache2`

If you're running Apache you'll often find you need to find the config for a
vhost given a serveralias, or the logfile of the site served out of a particular
directory. This aims to be a faster and more reliable way of doing that than 
grepping:

    avi@donkey:$ a2 avi.co
    servername   avi.co
    documentroot /home/avi/public_html
    errorlog     /home/avi/logs/error_log
    customlog    /home/avi/logs/access_log combined
    serveralias  avi.co, www.avi.co, aviswebsite.co.uk, www.aviswebsite.co.uk

If the argument begins with a forward-slash, it is assumed to be a path, and so 
the log, docroot and config file paths are checked instead:

    avi@donkey:~$ a2 /home/avi/logs/pics_error_log 
    servername   pics.avi.co
    documentroot /home/avi/pics_html
    errorlog     /home/avi/logs/pics_error_log
    serveralias  pics.avi.co

and, if you know what you want, you can have it just give the path either to 
the document root or apache's config file (for all matching vhosts):

    vim `a2 avi.co -f`

    cd `a2 avi.co -d`

## Included Files

I had a look through the various Apache config-file parsing moduls on the CPAN 
and each was lacking one of an ability to deal with included config files, or an
ability to deal with a  series of virtualhosts with identical opening tags (all
on the same socket); they are for using Apache-like configs, not actually parsing
Apache's config. 

`parse_apache_config()` is therefore small, inelegant and single-use. It has its
limitations (not least that it can't deal with an `Include` inside a vhosts) but
it's *generally* good enough at the moment. If this is useful but needs to be 
able to more properly parse the configs it may end up making use of one of those,
but for now, as a single-script, I think it's fine :)
