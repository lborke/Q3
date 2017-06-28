
[<img src="https://github.com/QuantLet/Styleguide-and-FAQ/blob/master/pictures/banner.png" width="888" alt="Visit QuantNet">](http://quantlet.de/)

## [<img src="https://github.com/QuantLet/Styleguide-and-FAQ/blob/master/pictures/qloqo.png" alt="Visit QuantNet">](http://quantlet.de/) **rgithubQ_yaml** [<img src="https://github.com/QuantLet/Styleguide-and-FAQ/blob/master/pictures/QN2.png" width="60" alt="Visit QuantNet 2.0">](http://quantlet.de/)

```yaml

Name of Quantlet : rgithubQ_yaml

Published in : GitHub API based QuantNet Mining infrastructure in R

Description : 'shows the interaction of two R packages rgithubQ and yamldebugger. Two GitHub API
search examples for Quantlet extraction are given and their YAML meta data are parsed. Some YAML
data fields are retrieved by means of the yamldebugger and the results are aggregated as data
frames. Additionally, real time QuantNet@GitHub statistics via the "qnet.stats" function from the
rgithubQ package are generated.'

Keywords : 'rgithubQ, yamldebugger, GitHub API, YAML, software mining, QuantNet, real time,
statistics'

See also : rgithubQ_yaml_TM, yaml_run

Author : Lukas Borke

Submitted : 28.06.2017 by Lukas Borke

Example : 'rgithubQ_qnet_stats.md, rgithubQ_yaml.md, rgithubQ_black_scholes.md,
rgithubQ_Top10authors_black_scholes.md'

```


### R Code:
```r

library(rgithubQ)
library(yamldebugger)

library(stringr)

# MD output
library(knitr)

# setwd(workdir)

# GitHub's user authorization
ctx = interactive.login("XXX", "YYY")


## QNet stats: a function from rgithubQ
( q_s = qnet.stats(spec_editor = c("bykovskaya", "borke")) )

q_s_t = t(as.data.frame(q_s))
colnames(q_s_t) = "total number"
( k_t = kable(q_s_t) )

writeLines(k_t, con = "rgithubQ_qnet_stats.md")


## GitHub search
q_search = 'Quantlet Published Description Keywords Author filename:"metainfo.txt"'

spec_search_term = "yaml user:Quantlet user:lborke user:b2net"
sr = search.code(paste(spec_search_term, q_search), per_page = 20)

spec_search_term = "black scholes user:Quantlet"
sr = search.code(paste(spec_search_term, q_search), per_page = 10)
# results for Top 10 authors
# sr = search.code(paste(spec_search_term, q_search), per_page = 100)

sr$content$total_count
length(sr$content$items)

q_top = yaml.parser.light(sr, print_item = FALSE)
# q_top = yaml.parser.light(sr, print_item = TRUE)

( q_names  = sapply( q_top, function(yaml_meta){ yaml.getQField(yaml_meta, "q")} ) )
( q_author = sapply( q_top, function(yaml_meta){ yaml.getQField(yaml_meta, "a")} ) )
( q_scores = sapply( sr$content$items, function(item){ item$score} ) )
( q_repos  = sapply( sr$content$items, function(item){ item$repository$full_name } ) )

# main output as data.frame
( name_path_scores = data.frame(qlet.name = q_names, qlet.repo.path = q_repos, search.score = round(q_scores, 2)) )

# md output for name_path_scores 
( k_t = kable(name_path_scores) )

writeLines(k_t, con = "rgithubQ_yaml.md")

writeLines(k_t, con = "rgithubQ_black_scholes.md")

# md output: Top 10 authors of "black scholes" Qs

a_splitted = unlist(str_split(q_author, ", "))
( tab_sorted = sort(table(a_splitted), decreasing=T)[1:10] )
a_df = as.data.frame(tab_sorted)
colnames(a_df) = c("author", "Number of Quantlets")
( k_t = kable(a_df) )

writeLines(k_t, con = "rgithubQ_Top10authors_black_scholes.md")


```
