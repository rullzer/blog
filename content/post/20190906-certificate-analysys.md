---
title: "Certificate lifetime analysis"
date: "2019-09-06"
tags:
  - Certificate lifetime
  - Security
---

Currently a vote is ongoing in the Server Certificate Working Group. The vote is with regards to the [SC22 ballot](https://cabforum.org/pipermail/servercert-wg/2019-August/000894.html) that would limit certificate lifetime to a maximum of 1 year.

Due to the discussion and controversy around SC I decided to dive into some data to see what the actual lifetime of certificates is in practice. Now any selection will be biased to some degree. However taking the Alexa top 1 million crawls seems like a fair selection and should provide insights into the biggest websites out there.

The data source used can be found at: <https://scans.io/data/scotthelme/alexaTop1Million/11-08-2019.zip>

Still not all websites use encrypted communication (shame on them!) but of those 1 million in the dump 471665 do. Filtering out obvious bad/invalid certificates (CA certificates, lifetime of more than 825 days) and we are left with 411241 certificates to analyze.

For full transparency I am in favor of SC22 passing. But I am just curious of the current state of affairs in regards to the ballot.

# Methodology

The data is imported into a local database. And after which all the sites are analyzed. Since we only care about websites with certificates all the other ones are discarded. Of the websites with certificates present only the first certificate in the chain in analyzed.

# Certificate lifetimes

As a first step all the certificates were analyzed with their start and expiration date. This gives the most interesting metrics already. The first thing that pops out is the wide range. The minimum certificate lifetime found is 7 days and the largest is 825 days (the maximum allowed).

After some more number crunching this gives an average lifetime of \~261.6 days. With a standard deviation of 197.9 days. Clearly the spread is quite large when we consider certificate lifetimes. But the good news is that the "average" certificate would not have to be changed with regards to SC22.

Besides the average the mean is also an interesting metric. The center value at 50%. This is 190 days. Again a good sign that a majority of certificates is already doing what SC22 proposes.

## How many certificates are we talking about?

This leads to the next question in the analysis. What percentage of certificates is valid for X days or less. Calculating this yields some interesting results. It turns out the number of certificates with really short lifetimes is small. But we see a huge jump at 90 days. 45.6% of all certificates has a lifetime of 90 days or less. Of course we know this lifetime all to well from Let’s Encrypt. And further analysis shows that this is indeed where the vast majority of those 90 days certificates comes from. That means that a lot of website are already using automation!

The next significant jump comes at 190 days. 54.6% off all certificates have a lifetime of less than that.

Followed by jumps at 356 (71.4%) and 366 (76.0%). Then there is a short climb. But already after 395 days we are at more than 80% of certificates. More than 90% comes after 732 days (\~2 years). And more than 99% of certificates is covered after at 814 days.

# What does this data tell us?

Now that we have all the data I'm going to make some assumptions to simplify things. I feel the 395 day lifetime of certificates is not far enough away from 365 days to warrant a special status. If you can renew your certificate every 395 days you can also do it once a year. My bet is the \~400 days are people that renewed their old certificate before the year was over and the provider of the certificate added the remaining days to their new certificate.

With the assumption above in consideration we can see that already more than 80% would fit within what is proposed in SC22. As the vast majority of certificates is already renewed once a year.

It also tells us that about 10% of the certificates have to be renewed somewhere in the range of 1 to 2 years. Which mean for at least part of the that the change to switch to certificates with a lifetime of at most a year is rather minimal.

# Conclusions

This short analysis shows us that from the Alexa Top 1 Million the websites using a certificate at least 80% would not be affected by the SC22 ballot. This is of course already great news on its own.

Of course the remaining 20% is not insignificant. However I feel it is worth to point out that often discounts are provided for multi-year certificates. Which could be an incentive at the moment to buy them. But there can be many other reasons those 20% chose for longer certificates.

I hope this short analysis provided you with some insights. And I'm curious to hear what your thoughts are!

## Acknowledgements

As the observant reader can already see the scan data was provided by [Scott Helme](https://scotthelme.co.uk/), to be exact from [crawler.ninja](https://crawler.ninja/). Also thanks to Scott for going over my initial analysis and proposing some improvements.
