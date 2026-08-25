# omp Sandbox Kit

A Docker Sandboxes kit for running [omp](https://omp.sh/), the terminal coding
agent with IDE-grade tooling built in. It installs omp in a `shell-docker`
sandbox, enables its tools without nested approval prompts, and uses OpenRouter
as the default model provider.

The OpenRouter key stays in Docker Sandboxes' host credential store. Inside the
sandbox, `OPENROUTER_API_KEY` is only a sentinel; the proxy replaces it in the
`Authorization` header when omp calls `openrouter.ai`.

## Quick start

Store your OpenRouter key once:

```bash
sbx secret set openrouter -t "$OPENROUTER_API_KEY"
```

Allow this GitHub publisher as a kit source, then run omp in the current project:

```bash
sbx settings set kit.allowedSources '["docker.io/","github.com/shelajev/"]'
sbx run --kit git+https://github.com/shelajev/omp-sbx-kit.git omp .
```

The default model is `openrouter/openai/gpt-5.5`. Choose another OpenRouter model
for an individual run by passing an omp model selector after `--`:

```bash
sbx run --kit git+https://github.com/shelajev/omp-sbx-kit.git omp . \
  -- --model openrouter/anthropic/claude-sonnet-4.6
```

## Named sandbox

```bash
sbx create --name omp-current \
  --kit git+https://github.com/shelajev/omp-sbx-kit.git omp .
sbx run omp-current
```

From a local clone, the wrapper creates or reattaches to `omp-current`:

```bash
./run.sh
```

Pass a different name and any remaining `sbx run` arguments as needed:

```bash
./run.sh my-omp-sandbox .
```

## One-shot tasks

The kit maps non-interactive launches to omp's `--print` mode:

```bash
sbx run --task "summarize this repository" \
  --kit git+https://github.com/shelajev/omp-sbx-kit.git omp .
```

Interactive launches retain omp's normal TUI. Both modes add `--auto-approve`;
the Docker Sandbox is intended to be the security boundary.

## Network policy

The kit permits only:

- `registry.npmjs.org` to install the pinned omp package at sandbox creation
- `openrouter.ai` for model discovery, authentication, and inference

Other package registries, Git hosts, web search services, and application APIs
remain blocked unless you add them to `permissions.network.allow` or allow them
with `sbx policy allow network <domain>`.

## Development

Validate the kit locally without creating a sandbox:

```bash
sbx kit validate .
sbx kit inspect .
sbx kit pack . -o /tmp/omp-kit.zip
sbx kit validate /tmp/omp-kit.zip
```

## License

Apache License 2.0. See [LICENSE](LICENSE).
