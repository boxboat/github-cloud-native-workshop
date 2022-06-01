# BoxBoat's Cloud Native Workshop

A series of labs that people can try to do things with Azure using Codespaces.

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

## Contribution Guidelines

- Submit a PR with your lab, you can use the instructions above for running the static website locally ☝️
- Ensure that the Codespaces repository that a person will fork is public and it allows forking.
- To configure Codespaces, you will have to configure Dev Containers to the repository that a person will fork.
- After you submit your PR, a peer will attempt the instructions.
