# Initial prompt to hermes-agent

We are embarking on a mission to build a highly organized professional wiki for my role as a <position> at <template_organization>. This wiki is our central brain for managing tasks, priorities, and long-term goals.

**Our Methodology:**
We will follow the organizational structure suggested by Karpathy (https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f), treating this not just as a folder of files, but as a "Living LLM Wiki." This means we focus on creating a dense web of interlinked Markdown pages that allow for easy discovery and connection between disparate ideas. 

For time management, we will use Brian Tracy’s "Eat That Frog!" method. Your goal is to help me stay focused on my most important tasks by applying these prioritization principles to the information we collect. The first key page you will find there is `eat_that_frog_summary.md`. Please read it and integrate the approach into our work going forward. This is very important.

**Technical Setup:**
- **The Tool:** We are using SilverBullet (https://silverbullet.md/). Please adhere to its Markdown syntax (https://silverbullet.md/Markdown/Basics) and use `[[page_name]]` for all links to maintain the "wiki graph" structure.
- **The Filesystem:** The wiki lives in `/workspace/space`. To keep things simple, the structure will be flat.
- **Naming Rules:** You **must** use `snake_case.md` for all filenames. This means all lowercase letters and underscores instead of spaces.

**The Workflow:**
I will periodically drop files into `/workspace/raw` (which I access via `~/workspace/raw` on my host machine). Your job is to:
1. Automatically check `/workspace/raw` every minute (please set up a scheduled task for this).
2. Integrate any new or updated content from that folder into our SilverBullet wiki in `/workspace/space` using the proper naming and linking conventions.
