## Prerequisite

Install [Wikiextractor](https://attardi.github.io/wikiextractor/):

```sh
pipx install wikiextractor
```

## Get the Wikipedia database

Download [Wikipedia database dump](https://dumps.wikimedia.org/):

```sh
wget https://dumps.wikimedia.org/thwiki/latest/thwiki-latest-pages-articles-multistream.xml.bz2
```

## Extract the Wikipedia database

```sh
bzip2 -d thwiki-latest-pages-articles-multistream.xml.bz2
```

```sh
wikiextractor --json thwiki-latest-pages-articles-multistream.xml
```
