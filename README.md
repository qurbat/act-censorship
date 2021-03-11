# act-censorship
This repository contains a list of hostnames that are known to be blocked on ACT Fibernet's network.

## Methodology
The censorship technique employed by ACT Fibernet seemingly poisons the DNS `A record` entry for each root domain present on their block list. The poisoned `A` record further appears to consistently point to a single static IP address. This knowledge is useful for enumerating a large set of hostnames in order to infer a proportionately comprehensive list of blocked hostnames.

```
...
tencent.com. 0 IN A 49.205.171.200
qq.com. 0 IN A 49.205.171.200
ucweb.com. 0 IN A 49.205.171.200
...
```

## Data

As a uniform list of suitable root domains was not readily available, several publicly available domain name sets were collated and used as input. The collated dataset was modified to exclude subdomains as well as any duplicate entries. The process used for excluding subdomains was not ideal, as a result of which a number of root domain names were accidentally omitted, too. In total, the collated dataset (which is not released here) includes exactly `22673297` entries.

1. **Top 1 Million** from [http://s3.amazonaws.com/alexa-static/top-1m.csv.zip](Alexa)

The version of the `top-1m.csv` list available as of today does not actually contain `1000000` lines. After support for the list was officially discontinued, the actual number of domain names included in it varies on any given day. At the time this list was pulled it contained roughly 700,000 lines. The test returned a total of `794` blocked hostnames. Due to the popular nature of the domain names included in this list, a lot of these websites might have already been known by the general public to have been blocked.

2. **Top 10 Million** from [https://www.domcop.com/files/top/top10milliondomains.csv.zip](DomCop)

This list was reduced to roughly 6,000,000 lines after subdomains and subsequent duplicate entries were pruned from the list. The test with this dataset returned a total of `1254` blocked hostnames, with a negligible overlap in results from the Alexa set.

3. Domains released by [https://dataset.domainsproject.org](Domains Project)

```
.IN dataset [Pruned to 582582 entries] [190 blocked]
.CO dataset [Pruned to 828814 entries] [171 blocked]
.NET dataset [Pruned to 6326153 entries] [546 blocked]
.ORG dataset [Pruned to 8625008 entries] [359 blocked]
```

### Results

Overall, after removing duplicate lines, `2731` individual hostnames were found to have been blocked.

Further, despite their comparatively smaller size, the lists made available by Alexa and DomCop resulted in more than half of the final compiled list of blocked hostnames. This is most likely due to the 'popularity' of the hostnames included in these lists. The lists made available by Domains Project were however found to be far more useful for uncovering otherwise 'obscure' blocked websites. 

Owing primarily to the gathering process of hostnames, the list of blocked hostnames released here will not be fully representative of all of the hostnames blocked by ACT Fibernet. A list containing contextually gathered hostnames would be ideal and may provide better results.

### Reproducibility

- The provided `blocktest.sh` script can be used to verify these results.

```
./script/blocktest.sh compiled_block_list.txt
```

![blocktest.sh](https://i.imgur.com/YXHP6WT.gif)

The `compiled_block_list.txt` file referenced here is present in the `lists` directory.

- The MassDNS tool can be used to carry out these tests from scratch.

```
./massdns -r lists/act_resolver.txt -s 10000 -t A lists/10m_domain_sorted.txt > output/10m_dns_responses.txt
```

### Notes

The intention behind this project is of introducing some amount of transparency to the otherwise opaque website blocking processes followed by Telecommunications and Internet Service Providers in India. It is hoped that this data is useful to those researching network censorship in India. This project was inspired by the paper [https://arxiv.org/abs/1912.08590]('How India Censors the Web'), authored by Kushagra Singh, Gurshabad Grover, and Varun Bansal.
