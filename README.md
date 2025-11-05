# Black Matter Labs Plugin Marketplace

A curated marketplace of plugins for [Claude Code](https://docs.claude.com/en/docs/claude-code).

## Installation

To add this marketplace to your Claude Code installation:

```bash
/plugin marketplace add Black-Matter-Labs/plugin-marketplace
```

## Available Plugins

Currently, this marketplace is empty. Check back soon for available plugins!

## Contributing Plugins

Want to add your plugin to this marketplace? Here's how:

### Requirements

Your plugin must:
- Be a valid Claude Code plugin with a `plugin.json` file
- Be hosted on GitHub or as a Git repository
- Have clear documentation
- Include proper licensing information

### Submission Process

1. Fork this repository
2. Add your plugin entry to `.claude-plugin/marketplace.json`
3. Submit a pull request with:
   - Plugin name and description
   - Link to the plugin repository
   - Brief explanation of functionality

### Plugin Entry Format

```json
{
  "name": "your-plugin-name",
  "description": "Brief description of what your plugin does",
  "version": "1.0.0",
  "author": {
    "name": "Your Name",
    "email": "your@email.com"
  },
  "license": "MIT",
  "source": {
    "source": "github",
    "repo": "username/repo-name"
  }
}
```

## Marketplace Structure

This marketplace follows the [Claude Code Plugin Marketplace specification](https://docs.claude.com/en/docs/claude-code/plugin-marketplaces).

The marketplace configuration is located at `.claude-plugin/marketplace.json` and contains:
- **name**: Unique marketplace identifier
- **owner**: Marketplace maintainer information
- **plugins**: Array of available plugins

## Support

For questions or issues with:
- **This marketplace**: Open an issue in this repository
- **A specific plugin**: Contact the plugin author directly
- **Claude Code**: Visit the [official documentation](https://docs.claude.com/en/docs/claude-code)

## License

MIT
