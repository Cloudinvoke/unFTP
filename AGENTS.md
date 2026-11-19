# Project basics

- unFTP is a Rust FTP/FTPS server designed for cloud deployments, built on libunftp and Tokio.
- This repository contains the server application, including configuration and integrations for storage, authentication, monitoring, logging, and notifications.
- GitHub project: https://github.com/Cloudinvoke/unFTP
- Maintained under the CloudInvoke project by Hannes de Jager and Rob klein Gunnewiek (https://github.com/robklg).
- The source for libunftp sits here: https://github.com/Cloudinvoke/libunftp
- Many extensions used by unFTP can be found on crates.io by searching for `unftp-`: https://crates.io/search?q=unftp-
- libunftp provides the underlying FTP/FTPS server library; this application configures and integrates it.
- Application code lives in `src/`
- The project tries to adhere loosly to a domain centric architecture layout like the Clean and Onion Architectures.
- Github Actions are used to build tests and run images

## Examples and release checks

- Build all workspace examples with `cargo build --examples --workspace --all-features` before
  submitting changes or preparing a release. `make pr-prep` includes this check.
- Run each example whose prerequisites are available and verify the behavior it demonstrates.
  For `slog-redis`'s `log_to_redis` example, start a local Redis instance and check that the
  example writes and reads a log entry.
- Before releasing the server, start unFTP with a local test configuration and verify FTP login,
  directory listing, and a download through a client.
- Record the result for each example. If an external service, credential, or platform capability
  is unavailable, name the example and missing prerequisite in the release handoff. A successful
  build alone does not establish that an example works at runtime.

## Packaging

- We offer binaries that are hosted on Github
- We have a couple of docker images. There are *.Dockerfile.template files for these in the root. They build and publish via Github actions
- Images are published to Docker Hub as `cloudinvoke/unftp` and to GHCR as `ghcr.io/cloudinvoke/unftp`.

## Website

- Public website and user documentation: https://unftp.rs
- Doctave (https://github.com/Doctave/doctave) is currently used to build the website
- Website documentation lives in `docs/` and is done in markdown.
- Netlify is used for hosting and it is set up to deploy the "netlify" branch.
- A Makefile target called "site" will compile the docs and add it to the netlify branch

## Commit messages

- No conventional commits please
- Separate subject from body with a blank line
- Limit the subject line to roughly 50 characters
- Capitalize the subject line
- Do not end the subject line with a period
- Use the imperative mood in the subject line
- Wrap the body at 72 characters
- Use the body to explain what and why and context vs. how
- Write thoughtful commit messages like David Tolnay
- Write for the next person reading git log: A good commit title should let you scan history quickly without needing to run git show for every minor bump
- Don't add attribution to AI engines

## Pull requests

- Don't add attribution to AI engines

## Changelogs and release notes

- Publish each unFTP server release to crates.io with `make publish` after
  publishing the GitHub release. First run the same `cargo publish` command
  with `--dry-run`, then verify the new version is
  available on crates.io. Do not treat the GitHub release as complete until
  the crates.io publication has been checked.
- End each release handoff with a ready-to-post Telegram channel announcement.
  Summarize the main user-facing changes and link the GitHub release and
  crates.io package. Draft the text for the maintainer; do not post it unless
  explicitly asked.
- For every unFTP release, compare the libunftp versions resolved in
  `Cargo.lock` for the previous unFTP release and the release being prepared.
  Review the upstream changes across that version range and include significant
  features, improvements, bug fixes, security fixes, and compatibility notes
  relevant to unFTP users in an "Improvements from libunftp" section of the
  GitHub release notes. Exclude cleanup, routine dependency bumps, and library
  APIs or features that unFTP does not expose. Do not include changes already
  shipped in the previous unFTP release or introduced after the bundled version.
- Keep this upstream libunftp detail out of `CHANGELOG.md`; it belongs only in
  the GitHub release notes. `CHANGELOG.md` may still record unFTP's libunftp
  dependency upgrade itself.
- Look up associated merged GitHub pull requests when updating changelogs
  or release notes, and link entries to them where available, following
  the existing `[#240](https://github.com/Cloudinvoke/unFTP/pull/240)`
  style.
- Verify each association using GitHub metadata or commit history. Do not
  invent pull request numbers.

## Upgrading the Rust version

Ask if the Rust version should be upgraded before a release.

Do not upgrade the user's local compiler; remind them to run
`rustup update stable` if they are behind.

Keep these pins in lockstep:

- `.github/workflows/rust.yml` (`env.RUST_VERSION`)
- `Makefile` (`RUST_VERSION`). This is substituted into
  `*.Dockerfile.template` when generating the gitignored root
  `*.Dockerfile` files.
- `packaging/docker/*.Dockerfile.ci` (`FROM rust:<version>-slim`),
  which GitHub Actions uses to publish images.

Also note the new version under Unreleased in `CHANGELOG.md`.

`docs/server/installation.md` documents a *minimum* Rust version
for building from source, not the CI/Docker toolchain. Only bump
that if the project actually requires a newer MSRV.
