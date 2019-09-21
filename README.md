# sa-summary

![Lifecycle
](https://img.shields.io/badge/lifecycle-experimental-orange.svg?style=flat)
![Dependencies
](https://img.shields.io/badge/dependencies-none-brightgreen.svg?style=flat)

sa-summary is a script for summarising spam log files.  Statistics on 
rules, spamassassin scores, Bayes scores, time spent processing mail and 
file size of mails are provided.  

Summary tables are provided for:
 * Totals
   * Score, time, size, rules and Bayes - total, min, max, mean
 * Rules hit:
   * top spam rules
   * top ham rules
   * top negative spam rules
   * top positive ham rules
 * Mail details:
   * marginal mail classifications
   * spam with low Bayes scores
   * ham with high Bayes scores
   * spam with high positive rule scores
   * ham with high positive rule scores


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

Requires perl 5.8 or later, [spamassassin](https://spamassassin.apache.org/) 
3.4.2 and [procmail](https://en.wikipedia.org/wiki/Procmail).

The following should work in any unix like environment:
```sh
wget https://raw.githubusercontent.com/makeyourownmaker/sa-summary/master/sa-summary
chmod u+x sa-summary
./sa-summary -h
```


## Spamassassin Logfile Configuration

I generate a custom spam log file configured mostly within procmail.

One day I will write an ode to [procmail](https://en.wikipedia.org/wiki/Procmail) ... 
and possibly a diss track for [sieve](http://sieve.info/) :-)

My full spamassassin procmail file is included here 
[spamassassin.procmail](https://raw.githubusercontent.com/makeyourownmaker/sa-summary/master/spamassassin.procmail).

It's included in $HOME/.procmailrc like so:
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

The sa-summary tool proved very useful while I was seeing too many
false-positive spamassassin classifications.

### Tables descriptions and examples

The following tables are provided: 

 * Totals
   * Score, time, size, rules and Bayes - total, min, max, mean
```
Email:      351  Autolearn:   181
Spam:       110  Autolearn:    96
Ham:        241  Autolearn:    85

Email: From: Sun May 28 07:50:52 2017  To: Wed Jun  7 08:21:27 2017
Spam:  From: Sun May 28 07:50:52 2017  To: Wed Jun  7 08:01:48 2017
Ham:   From: Sun May 28 08:39:10 2017  To: Wed Jun  7 08:21:27 2017

Email: TotalScore: -761.60  MinScore: -107.30  AvgScore:  -2.17  MaxScore:  81.00
Spam:  TotalScore: 3012.40  MinScore:    5.00  AvgScore:  27.39  MaxScore:  81.00
Ham:   TotalScore: -3774.00  MinScore: -107.30  AvgScore: -15.66  MaxScore:   4.60

Email: TotalTime:    1.44 hours  MinTime:  3.00 sec  AvgTime: 14.73 sec  MaxTime: 953.00 sec
Spam:  TotalTime:    0.27 hours  MinTime:  4.00 sec  AvgTime:  8.77 sec  MaxTime: 22.00 sec
Ham:   TotalTime:    1.17 hours  MinTime:  3.00 sec  AvgTime: 17.44 sec  MaxTime: 953.00 sec

Email: TotalSize: 5810915 bytes  MinSize:     0.00 bytes  AvgSize: 16555.31 bytes  MaxSize: 249624.00 bytes
Spam:  TotalSize: 1531391 bytes  MinSize:    95.00 bytes  AvgSize: 13921.74 bytes  MaxSize: 101431.00 bytes
Ham:   TotalSize: 4279524 bytes  MinSize:     0.00 bytes  AvgSize: 17757.36 bytes  MaxSize: 249624.00 bytes

Email: TotalRules:  4654  UniqueRules:   281  MinRules:   2  AvgRules: 13.26  MaxRules:   38
Spam:  TotalRules:  2101  UniqueRules:   178  MinRules:   5  AvgRules: 19.10  MaxRules:   38
Ham:   TotalRules:  2553  UniqueRules:   103  MinRules:   2  AvgRules: 10.59  MaxRules:   28

Email: TotalBayes: 88.54  MinBayes: 0.00  AvgBayes: 0.25  MaxBayes: 1.00
Spam:  TotalBayes: 82.02  MinBayes: 0.00  AvgBayes: 0.75  MaxBayes: 1.00
Ham:   TotalBayes:  6.52  MinBayes: 0.00  AvgBayes: 0.03  MaxBayes: 0.52
```

 * Rules hit:
   * top spam rules
```
--------------------------------------------------------------------------------
RANK	RULE NAME               	COUNT	 %OFMAIL %OFSPAM  %OFHAM  AVGSCO
--------------------------------------------------------------------------------
   1	HTML_MESSAGE            	  105	 67.24	 95.45	 54.36	  0.00
   2	RDNS_NONE               	   82	 25.07	 74.55	  2.49	  4.50
   3	RAZOR2_CF_RANGE_51_100  	   75	 21.65	 68.18	  0.41	  0.50
   4	RAZOR2_CHECK            	   75	 21.65	 68.18	  0.41	  1.90
   5	RAZOR2_CF_RANGE_E8_51_100	   75	 21.65	 68.18	  0.41	  1.89
--------------------------------------------------------------------------------
```

   * top ham rules
```
--------------------------------------------------------------------------------
RANK	RULE NAME               	COUNT	 %OFMAIL %OFSPAM  %OFHAM  AVGSCO
--------------------------------------------------------------------------------
   1	BAYES_00                	  228	 65.53	  1.82	 94.61	 -1.90
   2	SPF_PASS                	  171	 51.85	 10.00	 70.95	 -0.00
   3	AWL                     	  170	 49.00	  1.82	 70.54	  0.42
   4	RCVD_IN_HOSTKARMA_W     	  163	 46.44	  0.00	 67.63	 -0.10
   5	KHOP_RCVD_TRUST         	  161	 45.87	  0.00	 66.80	 -1.75
--------------------------------------------------------------------------------
```

   * top negative spam rules
```

----------------------------------------------------------------------------------------------
RANK	RULE NAME               	COUNT  %OFMAIL %OFSPAM  %OFHAM  AVGSCO   SCORE  TOTSCO
----------------------------------------------------------------------------------------------
   1	BAYES_20                	    1	  0.28	  0.91	  0.00	 -0.00	-0.0010	-0.0010
   2	BAYES_40                	    2	  0.57	  1.82	  0.00	 -0.00	-0.0010	-0.0020
   3	RP_MATCHES_RCVD         	    4	 12.82	  3.64	 17.01	 -0.00	-0.0010	-0.0040
   4	SPF_HELO_PASS           	    4	  5.98	  3.64	  7.05	 -0.00	-0.0010	-0.0040
   5	SPF_PASS                	   11	 51.85	 10.00	 70.95	 -0.00	-0.0010	-0.0110
----------------------------------------------------------------------------------------------
```

   * top positive ham rules
```
----------------------------------------------------------------------------------------------
RANK	RULE NAME               	COUNT  %OFMAIL %OFSPAM  %OFHAM  AVGSCO   SCORE  TOTSCO
----------------------------------------------------------------------------------------------
   1	DCC_CHECK               	   54	 32.19	 53.64	 22.41	  3.00	3.0000	162.0000
   2	KAM_COUK                	   47	 14.53	  3.64	 19.50	  1.10	1.1000	51.7000
   3	RDNS_NONE               	    6	 25.07	 74.55	  2.49	  4.50	4.5000	27.0000
   4	PYZOR_CHECK             	    7	 10.26	 26.36	  2.90	  2.50	2.5000	17.5000
   5	KAM_NUMSUBJECT          	   33	 11.68	  7.27	 13.69	  0.50	0.5000	16.5000
----------------------------------------------------------------------------------------------
```

 * Mail details:
   * marginal mail classifications
```
--------------------------------------------------------------------------------
MESSAGE_ID
	SCORE	DIFF	RESULT	TIME	SIZE		BAYES	LANG
	RULES
--------------------------------------------------------------------------------
<KtmxD25vLTdcI85l7p-Ywu_jX1_eMx5ptOIi9EDBMPA.lwau0udeD_0Y7xkbGfPr2h6q3wgt62ufQAuLVKAoP-g@erefen.info>
	5.0	0	Yes	8 secs	12456 bytes	0.1545	en
	BAYES_20=-0.001,HTML_MESSAGE=0.001,KAM_INFOUSMEBIZ=0.75,RAZOR2_CF_RANGE_51_100=0.5,RAZOR2_CF_RANGE_E8_51_100=1.886,RAZOR2_CHECK=1.9,T_REMOTE_IMAGE=0.01,URIBL_BLOCKED=0.001

<upKpFbW5DtiS0Yzd_At-AE9I45-maPlttBv2EEP12rQ.LzNKojy1dgaB_4dF1h0fgvDpjiOopZQVJffm01461g8@erefen.info>
	5.0	0	Yes	8 secs	12033 bytes	0.3477	en
	BAYES_40=-0.001,HTML_MESSAGE=0.001,KAM_INFOUSMEBIZ=0.75,RAZOR2_CF_RANGE_51_100=0.5,RAZOR2_CF_RANGE_E8_51_100=1.886,RAZOR2_CHECK=1.9,T_REMOTE_IMAGE=0.01,URIBL_BLOCKED=0.001

<DcIXYhyaoHT88UzfIPCneS7lMWzcpeqF-wN63RctNP4.L6ad7Z-fxGQPZMjR5MpzoyXFpt9O4pOj22L8VCAYYgg@refuti.info>
	4.6	0.4	No	5 secs	8786 bytes	0.5004	en
	BAYES_50=0.8,CRM114_PROB_SPAM=0.5,HTML_MESSAGE=0.001,KAM_INFOUSMEBIZ=0.75,PYZOR_CHECK=2.5,T_REMOTE_IMAGE=0.01,URIBL_BLOCKED=0.001

<20170529235902.3DE8C10195@foo.org>
	4.3	0.7	No	18 secs	10577 bytes	0.5000	en da
	AWL=-4.007,BAYES_50=0.8,DKIM_SIGNED=0.1,DKIM_VALID=-0.1,DKIM_VALID_AU=-0.1,KAM_ASCII_DIVIDERS=0.8,KAM_MXURI=1.5,NO_RELAYS=-0.001,URIBL_ABUSE_SURBL=1.25,URIBL_DBL_SPAM=2.5,URIBL_RHS_DOB=1.514

<0AhDuulGhopstSnhQbMJRmKIWld-9zYVcYcuJPtn6I4.1TlePfoiiUAM3tAMSMsHE5tVF69Y4WmLqC8OmVN3KeY@diunap.info>
	5.8	0.8	Yes	6 secs	17387 bytes	0.4992	en
	BAYES_50=0.8,HTML_FONT_LOW_CONTRAST=0.001,HTML_MESSAGE=0.001,KAM_INFOUSMEBIZ=0.75,RAZOR2_CF_RANGE_51_100=0.5,RAZOR2_CF_RANGE_E8_51_100=1.886,RAZOR2_CHECK=1.9,URIBL_BLOCKED=0.001

--------------------------------------------------------------------------------
```

   * spam with low Bayes scores
```
--------------------------------------------------------------------------------
MESSAGE_ID
	SCORE	DIFF	RESULT	TIME	SIZE		BAYES
--------------------------------------------------------------------------------
<20170602024104.95763.84760.98D42E82@grovesidecapitalmanagement.co.uk>
	25.8	20.8	Yes	14 secs	88880 bytes	0.0000

<178d98100b64db3b6cbbeaf_db1b2e59@underdo.sinet.co>
	12.7	7.7	Yes	5 secs	2500 bytes	0.0000

<CrQuZn1JkWtf_aopZSZzvRJlnonkNuRTUisfkXin25I.GGph59fufibRc_pK26XeG9NISrN0Ghx9V8IEgt0sPBc@rhdfol.info>
	12.1	7.1	Yes	6 secs	11334 bytes	0.0273

<KtmxD25vLTdcI85l7p-Ywu_jX1_eMx5ptOIi9EDBMPA.lwau0udeD_0Y7xkbGfPr2h6q3wgt62ufQAuLVKAoP-g@erefen.info>
	5.0	0	Yes	8 secs	12456 bytes	0.1545

<1784bc9ba7540b3c6cbbeaf_b2453759@bookmaking.netrep.co>
	16.0	11	Yes	7 secs	2825 bytes	0.2142

--------------------------------------------------------------------------------
```

   * ham with high Bayes scores
```
--------------------------------------------------------------------------------
MESSAGE_ID
	SCORE	DIFF	RESULT	TIME	SIZE		BAYES
--------------------------------------------------------------------------------
<20170528235902.2FA64101A6@bar.org>
	3.5	1.5	No	9 secs	8192 bytes	0.5003

<DcIXYhyaoHT88UzfIPCneS7lMWzcpeqF-wN63RctNP4.L6ad7Z-fxGQPZMjR5MpzoyXFpt9O4pOj22L8VCAYYgg@refuti.info>
	4.6	0.4	No	5 secs	8786 bytes	0.5004

<20170604235901.C133810D63@baz.org>
	0.9	4.1	No	11 secs	9700 bytes	0.5021

<145CE01219A8AEEBA31FEDE65751145C@WRG3UW5>
	-85.3	90.3	No	6 secs	1590 bytes	0.5193

--------------------------------------------------------------------------------
```

   * spam with high positive rule scores
```
--------------------------------------------------------------------------------
MESSAGE_ID
	SCORE	DIFF	POS	NEG	TIME	SIZE		BAYES
	RULES
--------------------------------------------------------------------------------
<592F197D.8471.C7602F@qux.corge.org>
	12.8	7.8	29.135	-16.368	11 secs	1923 bytes	0.5000
	AWL=-13.068,BAYES_50=0.8,CK_HELO_DYNAMIC_SPLIT_IP=0.001,DATE_IN_PAST_03_06=1.592,HELO_DYNAMIC_IPADDR2=3.607,HTML_MESSAGE=0.001,KHOP_DNSBL_ADJ=-3.3,KHOP_SC_CIDR8=0.1,RCVD_IN_BRBL_LASTEXT=3,RCVD_IN_HOSTKARMA_BL=1.7,RCVD_IN_MSPIKE_BL=0.01,RCVD_IN_MSPIKE_L5=0.001,RCVD_IN_PSBL=2.7,RCVD_IN_SBL_CSS=3.335,RCVD_IN_SEMBLACK=0.5,RCVD_IN_XBL=3,RDNS_NONE=4.5,SPF_SOFTFAIL=0.665,TO_EQ_FM_DIRECT_MX=0.622,TVD_RCVD_IP=0.001,URI_WP_HACKED=3

<458633w2sukg$0672b6v2$1824g7h0@qfn>
	39.8	34.8	43.588	-3.8	9 secs	12030 bytes	0.9974
	BAYES_99=4,CRM114_PROB_GOOD=-0.5,HK_RANDOM_ENVFROM=0.001,HK_RANDOM_FROM=0.001,HTML_IMAGE_RATIO_04=0.556,HTML_MESSAGE=0.001,INVALID_DATE=1.096,KHOP_DNSBL_ADJ=-3.3,KHOP_URIBL_BUMP=2.5,MIME_HTML_ONLY=0.723,RAZOR2_CF_RANGE_51_100=0.5,RAZOR2_CF_RANGE_E8_51_100=1.886,RAZOR2_CHECK=1.9,RCVD_IN_BL_SPAMCOP_NET=1.347,RCVD_IN_BRBL_LASTEXT=3,RCVD_IN_HOSTKARMA_BL=1.7,RCVD_IN_MSPIKE_BL=0.01,RCVD_IN_MSPIKE_L5=0.001,RCVD_IN_NIX_SPAM=0.25,RCVD_IN_PSBL=2.7,RCVD_IN_RP_RNBL=1.31,RCVD_IN_SBL_CSS=3.335,RCVD_IN_SEMBLACK=0.5,RCVD_IN_SORBS_SPAM=0.5,RCVD_IN_SORBS_WEB=1.5,RCVD_IN_XBL=3,RCVD_VIA_APNIC=0.001,RDNS_NONE=4.5,SPF_HELO_SOFTFAIL=0.732,SPF_SOFTFAIL=0.665,URIBL_ABUSE_SURBL=1.25,URIBL_DBL_SPAM=2.5,URIBL_SBL=1.623

<kly6_fbu1-5000402557-5550386327-40-86226.6784400517@rfl8>
	33.6	28.6	37.422	-3.8	5 secs	12007 bytes	1.0000
	BAYES_99=4,BAYES_999=4,CRM114_PROB_GOOD=-0.5,DIGEST_MULTIPLE=0.293,HTML_IMAGE_RATIO_04=0.556,HTML_MESSAGE=0.001,INVALID_DATE=1.096,KHOP_DNSBL_ADJ=-3.3,MIME_HTML_ONLY=0.723,PYZOR_CHECK=2.5,RAZOR2_CF_RANGE_51_100=0.5,RAZOR2_CF_RANGE_E8_51_100=1.886,RAZOR2_CHECK=1.9,RCVD_IN_BL_SPAMCOP_NET=1.347,RCVD_IN_BRBL_LASTEXT=3,RCVD_IN_HOSTKARMA_BL=1.7,RCVD_IN_MSPIKE_BL=0.01,RCVD_IN_MSPIKE_L5=0.001,RCVD_IN_NIX_SPAM=0.25,RCVD_IN_PSBL=2.7,RCVD_IN_RP_RNBL=1.31,RCVD_IN_SEMBLACK=0.5,RCVD_IN_SORBS_SPAM=0.5,RCVD_IN_SORBS_WEB=1.5,RCVD_VIA_APNIC=0.001,RDNS_NONE=4.5,SPF_HELO_SOFTFAIL=0.732,SPF_SOFTFAIL=0.665,URIBL_ABUSE_SURBL=1.25,URIBL_BLOCKED=0.001

<033545z0beu5$zb1r5k25$7065x8v2@LO260628153036>
	41.7	36.7	44.983	-3.3	13 secs	235 bytes	0.9996
	BAYES_99=4,BAYES_999=4,DCC_CHECK=3,DIGEST_MULTIPLE=0.293,HTML_MESSAGE=0.001,HTML_MIME_NO_HTML_TAG=0.377,INVALID_DATE=1.096,KAM_LAZY_DOMAIN_SECURITY=1,KHOP_DNSBL_ADJ=-3.3,MIME_HTML_ONLY=0.723,RAZOR2_CF_RANGE_51_100=0.5,RAZOR2_CF_RANGE_E8_51_100=1.886,RAZOR2_CHECK=1.9,RCVD_IN_BL_SPAMCOP_NET=1.347,RCVD_IN_BRBL_LASTEXT=3,RCVD_IN_HOSTKARMA_BL=1.7,RCVD_IN_MSPIKE_BL=2,RCVD_IN_MSPIKE_L5=0.8,RCVD_IN_PSBL=2.7,RCVD_IN_RP_RNBL=1.31,RCVD_IN_XBL=3,RCVD_VIA_APNIC=0.001,RDNS_NONE=4.5,TO_MALFORMED=2.099,URIBL_ABUSE_SURBL=1.25,URIBL_DBL_PHISH=2.5

<26b470f7vt4j$sz3l56t7$fo4332v8@FRCI08>
	31.3	26.3	34.635	-3.3	9 secs	330 bytes	0.9691
	BAYES_95=3,DCC_CHECK=3,DIGEST_MULTIPLE=0.293,HTML_MESSAGE=0.001,HTML_MIME_NO_HTML_TAG=0.377,INVALID_DATE=1.096,KAM_LAZY_DOMAIN_SECURITY=1,KHOP_DNSBL_ADJ=-3.3,MIME_HTML_ONLY=0.723,RAZOR2_CF_RANGE_51_100=0.5,RAZOR2_CF_RANGE_E8_51_100=1.886,RAZOR2_CHECK=1.9,RCVD_IN_BL_SPAMCOP_NET=1.347,RCVD_IN_BRBL_LASTEXT=3,RCVD_IN_HOSTKARMA_BL=1.7,RCVD_IN_MSPIKE_BL=2,RCVD_IN_MSPIKE_L5=0.8,RCVD_IN_NIX_SPAM=0.25,RCVD_IN_PSBL=2.7,RCVD_IN_RP_RNBL=1.31,RCVD_IN_SEMBLACK=0.5,RCVD_IN_SORBS_WEB=1.5,RCVD_VIA_APNIC=0.001,RDNS_NONE=4.5,URIBL_ABUSE_SURBL=1.25,URIBL_BLOCKED=0.001

--------------------------------------------------------------------------------
```

   * ham with high positive rule scores
```
--------------------------------------------------------------------------------
MESSAGE_ID
	SCORE	DIFF	POS	NEG	TIME	SIZE		BAYES
	RULES
--------------------------------------------------------------------------------
<DB5PR05MB13035B73AEE1E607674860EFEECB0@DB5PR05MB1303.eurprd05.prod.outlook.com>
	-6.7	11.7	2.007	-8.751	10 secs	4259 bytes	0.0000
	BAYES_00=-1.9,CRM114_GOOD=-3,KAM_NUMSUBJECT=0.5,KHOP_RCVD_TRUST=-1.75,RCVD_IN_HOSTKARMA_W=-0.1,RCVD_IN_HOSTKARMA_WL=-2,SPF_PASS=-0.001,SUBJ_ALL_CAPS=1.506,URIBL_BLOCKED=0.001

<0100015c542d318d-e408f5ff-df97-41b2-9219-021ac6f5e88e-000000@email.amazonses.com>
	-3.9	8.9	2.015	-5.9521	5 secs	3480 bytes	0.0000
	AWL=-0.000,BAYES_00=-1.9,DKIM_SIGNED=0.1,DKIM_VALID=-0.1,DKIM_VALID_AU=-0.1,HTML_IMAGE_ONLY_28=1.404,HTML_MESSAGE=0.001,KAM_NUMSUBJECT=0.5,KHOP_RCVD_TRUST=-1.75,RCVD_IN_DNSWL_NONE=-0.0001,RCVD_IN_HOSTKARMA_W=-0.1,RCVD_IN_HOSTKARMA_WL=-2,RP_MATCHES_RCVD=-0.001,SPF_PASS=-0.001,T_REMOTE_IMAGE=0.01

<E1dH22r-000wOE-kb@ppsw-40.csi.cam.ac.uk>
	-2.9	7.9	2.022	-4.903	8 secs	41189 bytes	0.0000
	AWL=1.286,BAYES_00=-1.9,CRM114_GOOD=-3,HTML_MESSAGE=0.001,LOTS_OF_MONEY=0.001,MIME_HTML_ONLY=0.723,RP_MATCHES_RCVD=-0.001,SPF_HELO_PASS=-0.001,SPF_PASS=-0.001,T_REMOTE_IMAGE=0.01,URIBL_BLOCKED=0.001

<B81F3D646AC0484CA7B9EED7B20F0DA10131DFB873@TS-EXMB-P1.CXG.CO.UK>
	-6.7	11.7	2.087	-8.751	8 secs	12019 bytes	0.0000
	AWL=2.084,BAYES_00=-1.9,CRM114_GOOD=-3,HTML_MESSAGE=0.001,KHOP_RCVD_TRUST=-1.75,MIME_QP_LONG_LINE=0.001,RCVD_IN_HOSTKARMA_W=-0.1,RCVD_IN_HOSTKARMA_WL=-2,SPF_PASS=-0.001,URIBL_BLOCKED=0.001

<10BB0CP1706071115320000001732@oswald1.endsleigh.co.uk>
	-1.9	6.9	2.101	-4	6 secs	1770 bytes	0.0000
	BAYES_00=-1.9,KAM_COUK=1.1,KAM_NUMSUBJECT=0.5,KHOP_RCVD_UNTRUST=0.5,RCVD_IN_HOSTKARMA_W=-0.1,RCVD_IN_HOSTKARMA_WL=-2,URIBL_BLOCKED=0.001

--------------------------------------------------------------------------------
```


### Command line options

sa-summary includes the following options:

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
| --web             | -w    | N/A        | Print web friendly output                                 | N/A           |
| --help            | -h    | N/A        | Print help message                                        | N/A           |

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

The following options will disable all tables except the initial summary tables:
```sh
./sa-summary -f spam-custom.log -p 0 -n 0 -m 0 -b 0 -t 0
```


## Roadmap

 * Improve code
   * Simplify some of the more complex functions
   * Fix worst perlcritic issues
   * Improve error messages
     * See [Error messages](https://style.tidyverse.org/error-messages.html) chapter in the R tidyverse style guide
 * Add unit tests
   * Setup travis CI
   * Possibly also setup [kritika.io](http://kritika.io)
 * Improve documentation
   * Add some of the README sections to the script in the form of [pod](https://perldoc.perl.org/perlpod.html) markup
 * Add functionality
   * Include ability to sort more table columns


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

