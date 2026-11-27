# cobweb-scan

A scan of IndieWeb software projects to check for maintenance signals.

## Purpose

The goal of this project is to collect a list of IndieWeb related projects,
Using a combination of the git repo metadata and published info, I hope to get a snapshot of how well projects are maintained, what projects need adoption, how active different communities are, and where the IndieWeb can get better.
In the age of AI, it would be good to help the community focus resources on key trusted projects and find ways to support project maintainers.

The tooling for measuring these signals still needs to be fleshed out, but to start I'm looking to collect the git repo location and where any releases are published.
I can't guarantee all tooling will be supported (e.g. I'm starting with GitHub as the only git host and a few package managers) but I will try to analyze other systems if there is demand.

A future idea would be to incorporate this list of projects into a IndieWeb community changelog, automatically highlighting important updates in the IndieWeb developer community.

## Add your project

Open a PR to add your project to the analysis.
Primarily focused on IndieWeb community projects such as shared libraries and web services.
Personal website repos used only by one person and archived software projects are discouraged.
The list of projects will be ingested into the scan process.

### Format

Add your project with the following format:

```json
[
  {
    "name": "Example Project",
    "url": "https://example.com",
    "repository": "https://github.com/example/example",
    "description": "Optional description of the project. Also will try to pull from teh repository.",
    "categories": ["web service", "self-hosted"],
    "packages": {
      "go": ["example-package"],
      "npm": ["example-package", "@example/example"],
      "php": ["example-package"],
      "python": ["example-package"],
      "ruby": ["example-gem"],
      "rust": ["example-crate"],
      "wordpress": ["example-plugin"]
    }
  }
]
```

Ideally the file will be sorted alphabetically by the repo URL.

```bash
jq 'sort_by(.repo)' indieweb-projects.json > temp.json
mv temp.json indieweb-projects.json
```

### Suggested Categories

You can include multiple categories that pertain to the project to help with identifying the project purpose.
Some categories (e.g. documentation) may be excluded to focus the results on projects with the most impact.
For published packages, don't add a category for the ecosystem (e.g. "python" or "wordpress") as the package will imply the ecosystem.

- library: For packages to be installed in other software projects (e.g. npm/python packages)
- web service: A web application or service (e.g. bridgy, webmention.io)
- self-hosted
- app: Locally installed applications like desktop/mobile micropub clients, and browser extension
- starter: A repo for getting people started with a tool such as a website builder
- documentation: A documentation repo or website
- microformats: Tool focused on microformats
- micropub: Tool focused on micropub website publishing
- webmention: Tool focused on webmention sending or receiving
- webmention: Tool focused on IndieAuth authentication

### Exporting a GitHub org

The following GitHub CLI command can be helpful for exporting a whole GitHub org's project list.

```bash
gh repo list <ORG_NAME> --visibility public --limit 500 \
  --json name,description,url,homepageUrl \
  --jq '[.[] | {url: .homepageUrl, repo: .url, description: .description, name: .name}]' \
  | jq \
  > temp-file.json
```

### Exporting NPM Search Results

```bash
npm search --json <search_term> \
  | jq '[.[] | {name: .name, url: .links.homepage, repo: .links.repository, description: .description, packages: { npm: [.name]} }]' \
  > temp-file.json
```

NPM often has git repository URLs pointing to the actual git+ URL. With a regex search of `git\+(.*)\.git` you can replace with `$1`.

## Health Signals

Here are some of the signals I'd like to capture:

- Clear description signals
    - [] Repo description
    - [] Project URL
    - [] Project README.md
    - [] Primary software language
- Activity signals
    - [] Recent commits
    - [] Recent lines changed
    - [] Latest release date
    - [] Number of git tags
- Project health signals
    - [] Open issues
    - [] Open PRs
    - [] Number of dependencies
- Security signals
    - [] SECURITY.md file
    - [] Dependency vulnerability scan results
- Contributions
    - [] How many package publisher
    - [] How many repo maintainers
    - [] How many git contributors
    - [] CONTRIBUTING.md
- Reach
    - [] How many installs
    - [] How many reviews
- Qualitative signals
    - [] Readme clarity
    - [] Has unit tests
    - [] "Stable" release versioning

See also: https://indieweb.org/2025/SD/opensource
