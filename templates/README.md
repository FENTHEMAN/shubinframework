# templates/

Files an adopting team copies into their own repositories.

The Shubin Framework is documentation, not a CLI or a scaffolder. There is
no `init` command and no template registry. When the framework prescribes
a concrete file — an Issue Template, an `AGENTS.md` for a specific
directory — the canonical version of that file lives here, and a team
copies it into the right place in their own repo.

The current contents are:

- `.github/ISSUE_TEMPLATE/` — GitHub Issue templates (Feature, Bug, plus
  `config.yml` and a copy guide). Belongs in each *code* repo, not in the
  markdown repo. See `framework.md` ch. 4.1 for what the Feature template
  enforces and why.
- `knowledge/AGENTS.md` — instructions for the AI agent that maintains
  `knowledge/wiki/`. Belongs in the markdown repo of teams that adopt the
  optional knowledge module. See `knowledge.md` for the broader picture.

Templates are intentionally not auto-installed. Copying is a deliberate
act: the team should read the file, understand it, and adapt it to their
naming and conventions before committing.
