# LlamaGen AI Skills

Official agent skills for creating and managing AI comics with LlamaGen.

## Install

```bash
npx skills add LlamaGenAI/skills --yes
```

The installer adds the `llamagen-comics` skill. On first use, the skill installs `@llamagen/cli` when needed and guides browser-based authentication without copying browser cookies into the terminal.

## First-phase capabilities

- Browser login with `llamagen auth login`
- Authentication status and local logout
- Comic creation and generation lookup
- Continue-writing and panel regeneration
- Comic API usage checks
- Independent authentication-site and API-domain configuration for isolated tests

## Manual setup

```bash
npm install --global @llamagen/cli
llamagen auth login
```

## Links

- CLI source: https://github.com/LlamaGenAI/cli
- Skills source: https://github.com/LlamaGenAI/skills
- Documentation: https://llamagen.ai/mcp

## License

MIT
