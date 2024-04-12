```sh
wget https://dumps.wikimedia.org/thwiki/latest/thwiki-latest-pages-articles-multistream.xml.bz2
```

```sh
bzip2 -d thwiki-latest-pages-articles-multistream.xml.bz2
```

```sh
pipx install wikiextractor
```

```sh
wikiextractor --json thwiki-latest-pages-articles-multistream.xml
```
