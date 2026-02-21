# Ultra Agent Skill

This is a "[skill](https://agentskills.io)" for LLM chat services.
It helps the user generate queries for [Ultra](https://overpass-ultra.us) ([docs](https://overpass-ultra.us/docs/)), a platform for making maps with OpenStreetMap data.

## Examples

> Write an Ultra query that shows post boxes.

> Write an Ultra query which uses Postpass to show highways, rendered as glowing blue lines.

> I am mapping curbs in OpenStreetMap and I need to visualize what still needs to be mapped. Specifically, I want to see if nodes between sidewalk and crossing ways have been tagged with `kerb=*`. Write an Ultra query to help with this.

## Installation

The following instructions are for [Claude](https://claude.ai).
Anthropic's documentation for installing custom skills is [here](https://support.claude.com/en/articles/12512198-how-to-create-custom-skills).

This process may be different for your LLM chat service of choice.

1. Download this repository as a ZIP archive.
2. Rename the ZIP archive to `ultra-query.zip`.
3. On the Claude website, navigate to [Settings > Capabilities](https://claude.ai/settings/capabilities).
4. In the Skills section, click the Add button to upload the ZIP archive.
5. The skill is now available to use in new chats.

## Contributing

Any issues or pull requests are welcome.

## License

This work is in the public domain.
Please see [LICENSE.md](LICENSE.md) for more information.
