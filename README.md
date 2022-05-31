# BoxBoat's Cloud Native Workshop

Repository with labs that attendes can follow at their own pace.

## Running Locally

### Bash
``` shell
docker run --rm \
  --volume="$PWD:/srv/jekyll" \
  -p 4000:4000 \
  jekyll/jekyll \
  jekyll serve
```

## Updating Gems

### Bash
``` shell
export JEKYLL_VERSION=3.8
docker run --rm \
  --volume="$PWD:/srv/jekyll" \
  -it jekyll/jekyll:$JEKYLL_VERSION \
  bundle update
```
