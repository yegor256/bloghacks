[![jekyll](https://github.com/yegor256/bloghacks/actions/workflows/jekyll.yml/badge.svg)](https://github.com/yegor256/bloghacks/actions/workflows/jekyll.yml)

Check us at [bloghacks.yegor256.com](https://bloghacks.yegor256.com).

The "256 Bloghacks" book is [here](https://www.yegor256.com/256-bloghacks.html).

## How to run

The blog can be deployed in this [Docker image](https://github.com/yegor256/blog-image) (`/code/blog` is where the sources are): 

```bash
$ docker run -it --rm \
  -v "$(readlink -f /code/blog):/b" \
  -p 4000:4000 \
  yegor256/blog-image \
  'cd /b && bundle update && bundle exec jekyll serve --drafts --future --host=0.0.0.0'
```

## License

Copyright (c) 2016-2023 Yegor Bugayenko

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the 'Software'), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so.

THE SOFTWARE IS PROVIDED 'AS IS', WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
