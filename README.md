# sa-summary

![Lifecycle
](https://img.shields.io/badge/lifecycle-experimental-orange.svg?style=flat)
![Dependencies
](https://img.shields.io/badge/dependencies-none-brightgreen.svg?style=flat)

sa-summary is a perl script for summarising spam log files.  Statistics on 
rules, spamassassin scores, Bayes scores, time spent processing mail and 
file size of mails are provided.  

Summary tables are provided for:
 * Rules hit:
   * top ham rules
   * top spam rules
   * top positive ham rules
   * top negative spam rules
 * Mail details:
   * marginal mail classifications
   * ham with high Bayes scores
   * spam with low Bayes scores
   * ham with high positive rule scores
   * spam with high positive rule scores


## Usage

Analyzing contents of spam.log: 
```sh
./sa-summary -f spam.log
```


## Installation

Requires perl 5.8 or later.

The following should work in any unix like environment:
```sh
wget https://raw.githubusercontent.com/makeyourownmaker/sa-summary/master/sa-summary
chmod u+x sa-summary
./sa-summary -h
```


## Spamassassin Logfile Configuration

I generate a custom spam log file configured mostly within procmail.

One day I will write an ode to procmail ...

My full spamassassin procmail file is included here 
[spamassassin.procmail](https://raw.githubusercontent.com/makeyourownmaker/sa-summary/master/spamassassin.procmail).

It's called from $HOME/.procmailrc like so:
```
########################################################################
# Spamassassin
# WARNING: Put as few recipes as possible above the spam recipes

INCLUDERC=$HOME/.procmail/spamassassin

# End of spamassassin recipes
########################################################################
```

If you are uncomfortable with procmail recipes and custom log files then see the
Alternatives section below.


## Details

This script is based on [sa-stats.pl by Dallas Engelken](http://www.impsec.org/~jhardin/antispam/sa-stats.pl).
The original script has been extensively modified.

I use it for processing a custom generated spamassassin log file.  It no 
longer works with the default spamassassin log files.

The sa-summary script proved very useful while I was seeing too many
false-positive spamassassin classifications.


## Roadmap

 * Improve code
   * Simplify some of the more complex functions
   * Fix worst perlcritic issues
 * Add unit tests
   * Plus travis CI
   * Possibly also setup [kritika.io](http://kritika.io)
 * Improve documentation
   * Include more usage examples
   * Describe output reports in the Details section


## Contributing

Pull requests are welcome.  For major changes, please open an issue first to 
discuss what you would like to change.


## Alternatives

 * [sa-stats.pl by Dallas Engelken](http://www.impsec.org/~jhardin/antispam/sa-stats.pl).
 * [The Apache Spamassassin sa-stats.pl script](http://svn.apache.org/repos/asf/spamassassin/branches/3.3/tools/sa-stats.pl)
 * [Spamassassin Statistics and log analyzers](https://cwiki.apache.org/confluence/display/SPAMASSASSIN/StatsAndAnalyzers)
 * [Spamassassin topic on github](https://github.com/topics/spamassassin)


## License

Whatever license Dallas Engelken used??

