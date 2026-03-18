# Demo Jekyll Static Blog

[![jekyll](https://github.com/yegor256/bloghacks/actions/workflows/jekyll.yml/badge.svg)](https://github.com/yegor256/bloghacks/actions/workflows/jekyll.yml)

Check us at [bloghacks.yegor256.com](https://bloghacks.yegor256.com).

The "256 Bloghacks" book is [here](https://www.yegor256.com/256-bloghacks.html).

## How to run

The blog can be deployed in this
[Docker image](https://github.com/yegor256/blog-image)
(`/code/blog` is where the sources are):

```bash
docker run -it --rm \
  -v "$(readlink -f /code/blog):/b" \
  -p 4000:4000 \
  yegor256/blog-image \
  'cd /b && bundle update && bundle exec jekyll serve --drafts --future --host=0.0.0.0'
```
