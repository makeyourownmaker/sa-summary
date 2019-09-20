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

Here are some examples of usage with short command line arguments:
```sh
# Print help message
./sa-summary -h

# Analyse contents of spam.log in current directory
./sa-summary -f spam.log

# Analyse contents of multiple spam.log.* files under /var/log directory
./sa-summary -l /var/log -f spam.log.*

# Only display top 5 rules
./sa-summary -f spam.log -t 5

# Use higher positive spamassassin score threshold
./sa-summary -f spam.log -p 3

# Use lower negative spamassassin score threshold
./sa-summary -f spam.log -n -1

# Use lower margin with marginal mail classifications table
./sa-summary -f spam.log -m 2

# Print web friendly output
./sa-summary -f spam.log -w
```
Long command line options are listed in the Details section below.


## Installation

Requires perl 5.8 or later, spamassassin 3.4.2 and procmail.

The following should work in any unix like environment:
```sh
wget https://raw.githubusercontent.com/makeyourownmaker/sa-summary/master/sa-summary
chmod u+x sa-summary
./sa-summary -h
```


## Spamassassin Logfile Configuration

I generate a custom spam log file configured mostly within procmail.

One day I will write an ode to [procmail](https://en.wikipedia.org/wiki/Procmail) ... 
or possibly a diss track for [sieve](http://sieve.info/) :-)

My full spamassassin procmail file is included here 
[spamassassin.procmail](https://raw.githubusercontent.com/makeyourownmaker/sa-summary/master/spamassassin.procmail).

It's called from $HOME/.procmailrc like so:
```
########################################################################
# Spamassassin
# WARNING: Put as few recipes as possible above the spam recipes

INCLUDERC=$HOME/.procmail/spamassassin.procmail

# End of spamassassin recipes
########################################################################
```

If you are uncomfortable with procmail recipes and custom log files then see the
Alternatives section below.

The following fields are included:

| Field                 | Description                                                 |
|-----------------------|-------------------------------------------------------------|
| Date time             | Basic timestamp                                             |
| Stop time             | Time spamassassin finished processing at (secs since epoch) |
| Start time            | Time spamassassin started processing at (secs since epoch)  |
| Mail size             | Size of mail in bytes                                       |
| Message ID            | Email header message ID                                     |
| Language              | 2 character language code(s)                                |
| Classification result | Yes or no (spam or ham)                                     |
| Spamassassin score    | Aggregate score (5 or more is spam)                         |
| Rules                 | Spamassassin rules hit (with rule scores)                   |
| Autolearn             | Was mail used for Bayesian learning                         |
| Bayes score           | Bayes score for mail (0 to 1)                               |

Fields are tab separated.  

Here are a few examples:
```
2019.09.20-12:24:04	1568982244	1568982233	1050	<54ad4096-0989-83dd-988f-ba5c9c84fa4a@bristol.ac.uk>	en	No, score=-3.6 required=5.0 tests=BAYES_00,MAILING_LIST_MULTI,RCVD_IN_DNSWL_LOW,SPF_HELO_PASS,SPF_PASS autolearn=ham 	autolearn_force=no version=3.4.2	0.0000
2019.09.20-12:46:34	1568983594	1568983585	6436	<D9183B21-940E-4884-8D47-64D9AE1C6635@exeter.ac.uk>	en	No, score=-3.6 required=5.0 tests=BAYES_00,LOTS_OF_MONEY,MAILING_LIST_MULTI,RCVD_IN_DNSWL_LOW,SPF_HELO_PASS,SPF_PASS 	autolearn=ham autolearn_force=no version=3.4.2	0.0000
2019.09.20-13:02:14	1568984534	1568984526	1230	<CAPVnE2YCHktS2n9oQMCXgn39yYhWf5Y-gxXLwKw_SV_PQ1fT5A@mail.gmail.com>	en	No, score=-2.7 required=5.0 tests=AWL,BAYES_00,DKIM_ADSP_CUSTOM_MED,FREEMAIL_FORGED_FROMDOMAIN,FREEMAIL_FROM,HEADER_FROM_DIFFERENT_DOMAINS,MAILING_LIST_MULTI,RCVD_IN_DNSWL_LOW,SPF_HELO_PASS,SPF_PASS autolearn=ham autolearn_force=no version=3.4.2	0.0000
2019.09.20-14:31:04	1568989864	1568989855	6042	<DB7PR01MB5080A86AE6EDD7479248F96CB7880@DB7PR01MB5080.eurprd01.prod.exchangelabs.com>	en	No, score=-3.5 required=5.0 tests=AWL,BAYES_00,HEADER_FROM_DIFFERENT_DOMAINS,LOTS_OF_MONEY,MAILING_LIST_MULTI,RCVD_IN_DNSWL_LOW,SPF_HELO_PASS,SPF_PASS autolearn=ham 	autolearn_force=no version=3.4.2	0.0000
2019.09.20-15:15:40	1568992540	1568992531	4676	<74eefd0fdef841d8806b169b6a6505de@uwaterloo.ca>	en	No, score=-3.6 required=5.0 tests=AWL,BAYES_00,HEADER_FROM_DIFFERENT_DOMAINS,LOTS_OF_MONEY,MAILING_LIST_MULTI,RCVD_IN_DNSWL_LOW,SPF_HELO_PASS,SPF_PASS autolearn=ham 	autolearn_force=no version=3.4.2	0.0000
```


## Details

This script is based on [sa-stats.pl by Dallas Engelken](http://www.impsec.org/~jhardin/antispam/sa-stats.pl).
The original script has been extensively modified.

I use it for processing a custom generated spamassassin log file.  It no 
longer works with the default spamassassin log files.

The sa-summary script proved very useful while I was seeing too many
false-positive spamassassin classifications.


### Command line options

The script includes the following options

| Long              | Short | Parameter  | Description                                               | Default value |
|-------------------|-------|------------|-----------------------------------------------------------|---------------|
| --logdir          | -l    | directory  | Directory containing spam logs                            | '.'           |
| --file            | -f    | file/regex | File name or regular expression to look for in the logdir | '^maillog$'   |
| --top             | -t    | integer    | Number of top rules to display                            | 20            |
| --sa_threshold    | -s    | integer    | Spamassassin score threshold                              | 5             |
| --bayes_threshold | -b    | float      | Bayes classifier threshold                                | 0.5           |
| --pos_threshold   | -p    | float      | Positive spamassassin score threshold                     | 2             |
| --neg_threshold   | -n    | float      | Negative spamassassin score threshold                     | -0.1          |
| --margin          | -m    | integer    | Plus/minus margin for marginal classifications            | 4             |
| --web             | -w    | N/A        | Print web friendly output                                 |               |
| --help            | -h    | N/A        | Print help message                                        |               |

If number of top rules to display is set to 0 then the following tables will not be displayed
 * Rules hit:
   * top ham rules
   * top spam rules
   * top positive ham rules
   * top negative spam rules

If margin is set to 0 then the following tables will not be displayed
 * Mail details:
   * marginal mail classifications

If Bayes threshold is set to 0 then the following tables will not be displayed
 * Mail details:
   * ham with high Bayes scores
   * spam with low Bayes scores

If both positive and negative spamassassin score thresholds are set to 0 then
the following tables will not be displayed
 * Mail details:
   * ham with high positive rule scores
   * spam with high positive rule scores

The following options will disable all tables except the first summary tables:
```sh
./sa-summary -f spam-custom.log -p 0 -n 0 -m 0 -b 0 -t 0
```



## Roadmap

 * Improve code
   * Simplify some of the more complex functions
   * Fix worst perlcritic issues
 * Add unit tests
   * Setup travis CI
   * Possibly also setup [kritika.io](http://kritika.io)
 * Improve documentation
   * Describe output reports in the Details section
 * Add functionality
   * Include ability to sort other table columns
   * Improve error messages
     * See [Error messages](https://style.tidyverse.org/error-messages.html) chapter in the R tidyverse style guide


## Contributing

Pull requests are welcome.  For major changes, please open an issue first to 
discuss what you would like to change.


## Alternatives

 * [sa-stats.pl by Dallas Engelken](http://www.impsec.org/~jhardin/antispam/sa-stats.pl).
 * [The Apache Spamassassin sa-stats.pl script](http://svn.apache.org/repos/asf/spamassassin/branches/3.3/tools/sa-stats.pl)
 * [Spamassassin Statistics and log analyzers](https://cwiki.apache.org/confluence/display/SPAMASSASSIN/StatsAndAnalyzers)
 * [Spamassassin topic on github](https://github.com/topics/spamassassin)


## License

Whatever license Dallas Engelken used.

